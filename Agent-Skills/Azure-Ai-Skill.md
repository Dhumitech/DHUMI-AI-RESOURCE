---
name: azure-container-apps-deploy
description: >
  Step-by-step deployment of a containerized web application (separate frontend + backend services)
  to Azure Container Apps using local Docker builds and Azure CLI. Use this skill whenever a user
  wants to deploy a project to Azure, has a Dockerfile, mentions Azure Container Apps, Azure Container
  Registry, ACA, or asks how to get their containerized app running on Azure. Also trigger when the
  user mentions student Azure subscriptions, ACR image push, managed identity for ACR, or internal
  service-to-service routing in Azure. Covers fresh deploys, redeployments, secret rotation, and
  region/subscription constraints common on Azure for Students accounts.
---

# Azure Container Apps Deployment Skill

Deploy a containerized project (frontend + backend) to Azure Container Apps using local Docker
builds and Azure CLI. Designed to work reliably on restricted subscriptions (e.g. Azure for Students)
where `az acr build` / ACR Tasks may be blocked.

---

## Phase 0 — Gather Context Before Starting

Before running any commands, collect answers to these questions. Extract from conversation history
first; ask only for what is missing.

| Question | Why it matters |
|---|---|
| Fresh deploy or redeploy? | Determines whether to create or reuse resources |
| Azure CLI logged in? | Required for all az commands |
| Docker Desktop running? | Required for local image builds |
| Which API keys does the project need? | Backend secrets — must be real or test values |
| Does the project have separate frontend/backend Dockerfiles? | Affects how many images to build |
| What port does each service listen on? | Sets `--target-port` |
| Does the frontend proxy `/api/*` to the backend? | Determines internal vs. external ingress setup |
| Any known subscription constraints? | e.g. ACR Tasks blocked, region policy |

**Key subscription constraint to check early:**
On Azure for Students and some restricted subscriptions, `az acr build` is blocked.
Always use **local Docker build + `docker push`** as the default path — never `az acr build`.

---

## Phase 1 — Verify Prerequisites

```powershell
az account show --output table     # confirm correct subscription is active
docker version                     # confirm Docker daemon is running
node -v                            # optional, confirm Node if needed for builds
```

If the wrong subscription is active:
```powershell
az account list --output table
az account set --subscription "<name or id>"
```

---

## Phase 2 — Set Variables

Fill in this block. All subsequent commands reference these variables.

```powershell
# ── Azure Resource Names ──────────────────────────────────────
$RESOURCE_GROUP  = "rg-<project>-prod-<region>"         # e.g. rg-myapp-prod-centralindia
$LOCATION        = "<region>"                            # e.g. centralindia (see region note below)
$ACR_NAME        = "<project>$(Get-Random -Min 10000 -Max 99999)"  # must be globally unique, lowercase+numbers only
$ACA_ENV         = "<project>-aca-env"
$FRONTEND_APP    = "<project>-frontend"
$BACKEND_APP     = "<project>-api"
$IDENTITY_NAME   = "<project>-acr-pull"
$IMAGE_TAG       = Get-Date -Format "yyyyMMdd-HHmmss"

# ── Build-time variables (baked into frontend image) ──────────
$VITE_SITE_KEY   = "<your-frontend-public-key>"          # e.g. Turnstile/Captcha site key

# ── Runtime secrets (backend) ─────────────────────────────────
$API_KEY_1       = "<primary-api-key>"                   # e.g. Groq, OpenAI, etc.
$API_KEY_2       = "<secondary-api-key>"                 # set to "unused" if not needed
$BACKEND_SECRET  = "<backend-secret>"                    # e.g. Turnstile secret, JWT secret

# ── Confirm ───────────────────────────────────────────────────
Write-Host "ACR: $ACR_NAME  |  Tag: $IMAGE_TAG  |  Region: $LOCATION" -ForegroundColor Cyan
```

### Region Notes
- Prefer a region confirmed to work on your subscription.
- On Azure for Students: `eastus` is often policy-blocked; `centralindia` works reliably.
- Use **one region** for everything: resource group, ACR, ACA environment.

### ACR Naming Rules
- Lowercase letters and numbers only — no dashes, no spaces.
- Must be globally unique across all of Azure.
- Tip: append a random suffix, e.g. `myapp82341`.

---

## Phase 3 — Register Providers (one-time per subscription)

```powershell
az provider register --namespace Microsoft.ContainerRegistry
az provider register --namespace Microsoft.ManagedIdentity
az provider register --namespace Microsoft.App
az provider register --namespace Microsoft.OperationalInsights
```

Wait ~60 seconds, then verify all four are `Registered`:

```powershell
foreach ($ns in @(
  "Microsoft.ContainerRegistry",
  "Microsoft.ManagedIdentity",
  "Microsoft.App",
  "Microsoft.OperationalInsights"
)) {
  $state = az provider show --namespace $ns --query registrationState -o tsv
  Write-Host "$ns : $state"
}
```

Do not proceed until all four show `Registered`.

---

## Phase 4 — Create Azure Resources

Run one at a time; confirm each succeeds before continuing.

```powershell
# 1. Resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# 2. Container registry
az acr create --name $ACR_NAME --resource-group $RESOURCE_GROUP --location $LOCATION --sku Basic --admin-enabled false

# 3. Container Apps environment (~2 min)
az containerapp env create --name $ACA_ENV --resource-group $RESOURCE_GROUP --location $LOCATION

# 4. Managed identity
az identity create --name $IDENTITY_NAME --resource-group $RESOURCE_GROUP --location $LOCATION
```

---

## Phase 5 — Wire Identity to ACR

```powershell
$IDENTITY_ID           = az identity show --name $IDENTITY_NAME --resource-group $RESOURCE_GROUP --query id -o tsv
$IDENTITY_PRINCIPAL_ID = az identity show --name $IDENTITY_NAME --resource-group $RESOURCE_GROUP --query principalId -o tsv
$ACR_ID                = az acr show --name $ACR_NAME --resource-group $RESOURCE_GROUP --query id -o tsv

# Check if role already assigned (avoids duplicate assignment error)
$existing = az role assignment list --assignee-object-id $IDENTITY_PRINCIPAL_ID --scope $ACR_ID --role AcrPull -o tsv
if ($existing) {
  Write-Host "AcrPull already assigned ✓" -ForegroundColor Green
} else {
  az role assignment create `
    --assignee-object-id $IDENTITY_PRINCIPAL_ID `
    --assignee-principal-type ServicePrincipal `
    --role AcrPull `
    --scope $ACR_ID
  Write-Host "AcrPull granted ✓" -ForegroundColor Green
}
```

> Wait a few seconds after a new role assignment before deploying — propagation can take a moment.

---

## Phase 6 — Build and Push Images

```powershell
# Log in to the private registry
az acr login --name $ACR_NAME

# ── Frontend image ────────────────────────────────────────────
# Adjust --build-arg names to match the project's actual Vite/build-time variables
docker build -f Dockerfile `
  --build-arg "VITE_SITE_KEY=$VITE_SITE_KEY" `
  --build-arg "VITE_API_BASE=" `
  -t "$ACR_NAME.azurecr.io/<project>/frontend:$IMAGE_TAG" `
  -t "$ACR_NAME.azurecr.io/<project>/frontend:latest" `
  .

docker push "$ACR_NAME.azurecr.io/<project>/frontend:$IMAGE_TAG"
docker push "$ACR_NAME.azurecr.io/<project>/frontend:latest"

# ── Backend image ─────────────────────────────────────────────
# Adjust path to Dockerfile and build context as needed
docker build -f backend/<service>/Dockerfile `
  -t "$ACR_NAME.azurecr.io/<project>/backend:$IMAGE_TAG" `
  -t "$ACR_NAME.azurecr.io/<project>/backend:latest" `
  backend/<service>

docker push "$ACR_NAME.azurecr.io/<project>/backend:$IMAGE_TAG"
docker push "$ACR_NAME.azurecr.io/<project>/backend:latest"
```

**Build-time vs. runtime variables — critical distinction:**

| Type | When set | How set | Example |
|---|---|---|---|
| Build-time | During `docker build` | `--build-arg` | Public site key, `VITE_*` vars |
| Runtime | On the Container App | `--env-vars` / `--secrets` | API keys, ports, backend URLs |

Never set `VITE_*` variables as Container App env vars — they have no effect at runtime.

---

## Phase 7 — Deploy Backend Container App

```powershell
az containerapp create `
  --name $BACKEND_APP `
  --resource-group $RESOURCE_GROUP `
  --environment $ACA_ENV `
  --image "$ACR_NAME.azurecr.io/<project>/backend:$IMAGE_TAG" `
  --ingress internal `
  --target-port 8000 `
  --user-assigned $IDENTITY_ID `
  --registry-identity $IDENTITY_ID `
  --registry-server "$ACR_NAME.azurecr.io" `
  --cpu 1.0 `
  --memory 2.0Gi `
  --min-replicas 1 `
  --max-replicas 2 `
  --secrets "api-key-1=$API_KEY_1" "api-key-2=$API_KEY_2" "backend-secret=$BACKEND_SECRET" `
  --env-vars PORT=8000 ENVIRONMENT=production `
    "API_KEY_1=secretref:api-key-1" `
    "API_KEY_2=secretref:api-key-2" `
    "BACKEND_SECRET=secretref:backend-secret"
```

**Ingress must be `internal`** — the backend should never be publicly exposed if the frontend proxies for it.

---

## Phase 8 — Deploy Frontend Container App

```powershell
az containerapp create `
  --name $FRONTEND_APP `
  --resource-group $RESOURCE_GROUP `
  --environment $ACA_ENV `
  --image "$ACR_NAME.azurecr.io/<project>/frontend:$IMAGE_TAG" `
  --ingress external `
  --target-port 8080 `
  --user-assigned $IDENTITY_ID `
  --registry-identity $IDENTITY_ID `
  --registry-server "$ACR_NAME.azurecr.io" `
  --cpu 0.5 `
  --memory 1.0Gi `
  --min-replicas 1 `
  --max-replicas 2 `
  --env-vars PORT=8080 `
    "BACKEND_URL=http://$BACKEND_APP" `
    "SERVER_API_BASE=http://$BACKEND_APP"
```

The frontend reaches the backend via `http://<backend-app-name>` — the short internal hostname
works within the same Container Apps environment without needing the full FQDN.

---

## Phase 9 — Verify

```powershell
# Get the public URL
$FRONTEND_URL = az containerapp show `
  --name $FRONTEND_APP `
  --resource-group $RESOURCE_GROUP `
  --query properties.configuration.ingress.fqdn -o tsv

Write-Host "Live at: https://$FRONTEND_URL" -ForegroundColor Green

# Health check through frontend proxy
Start-Sleep -Seconds 15
Invoke-WebRequest -Uri "https://$FRONTEND_URL/api/v1/health" -UseBasicParsing |
  Select-Object -ExpandProperty Content
```

A `warming` or `initializing` response on first run is normal — the backend may need 1–3 minutes
to fully start (especially if it loads embeddings or a model on startup). Retry until healthy.

---

## Common Problems and Fixes

### ACR image pull fails
```powershell
# Verify role assignment is present
az role assignment list --assignee-object-id $IDENTITY_PRINCIPAL_ID --scope $ACR_ID --role AcrPull -o table

# Verify registry config on the Container App
az containerapp show --name $BACKEND_APP --resource-group $RESOURCE_GROUP --query properties.configuration.registries
```

### `/api` returns 502 after frontend loads
```powershell
# Verify frontend env vars point to the correct backend name
az containerapp show --name $FRONTEND_APP --resource-group $RESOURCE_GROUP --query "properties.template.containers[0].env"

# Verify backend ingress is internal (not external)
az containerapp show --name $BACKEND_APP --resource-group $RESOURCE_GROUP --query properties.configuration.ingress
```

### Backend stays warming / unhealthy
```powershell
az containerapp logs show --name $BACKEND_APP --resource-group $RESOURCE_GROUP --follow
```

### Provider not registered error
```powershell
az provider register --namespace <namespace>
# Wait, then re-check: az provider show --namespace <namespace> --query registrationState -o tsv
```

### Resource group region conflict
You cannot reuse a resource group name in a different region.
Create a new resource group name, or delete and recreate in the intended region.

---

## Redeploy and Update Operations

### Update an image (frontend or backend)
```powershell
# Rebuild, push, then point the Container App at the new tag
docker build ... -t "$ACR_NAME.azurecr.io/<project>/<service>:$NEW_TAG" ...
docker push "$ACR_NAME.azurecr.io/<project>/<service>:$NEW_TAG"
az containerapp update --name $APP --resource-group $RESOURCE_GROUP --image "$ACR_NAME.azurecr.io/<project>/<service>:$NEW_TAG"
```

### Update backend secrets only
```powershell
az containerapp secret set --name $BACKEND_APP --resource-group $RESOURCE_GROUP `
  --secrets "backend-secret=$NEW_SECRET" "api-key-1=$NEW_KEY"
```

### Update frontend runtime env vars
```powershell
az containerapp update --name $FRONTEND_APP --resource-group $RESOURCE_GROUP `
  --set-env-vars "BACKEND_URL=http://$BACKEND_APP" "SERVER_API_BASE=http://$BACKEND_APP"
```

### Update a build-time variable (e.g. public site key changed)
Build-time variables cannot be changed without a full image rebuild:
```powershell
docker build -f Dockerfile --build-arg "VITE_SITE_KEY=$NEW_KEY" ... -t ...:$NEW_TAG .
docker push ...:$NEW_TAG
az containerapp update --name $FRONTEND_APP --resource-group $RESOURCE_GROUP --image ...:$NEW_TAG
```

---

## Teardown

```powershell
# Remove everything in the resource group
az group delete --name $RESOURCE_GROUP --yes --no-wait

# Remove only the Container Apps, keep ACR and environment
az containerapp delete --name $FRONTEND_APP --resource-group $RESOURCE_GROUP --yes
az containerapp delete --name $BACKEND_APP  --resource-group $RESOURCE_GROUP --yes
```

---

## Recommended Resource Sizing

| Service | CPU | Memory | Min replicas | Max replicas |
|---|---|---|---|---|
| Backend (API/ML) | 1.0 | 2.0Gi | 1 | 2 |
| Frontend (proxy server) | 0.5 | 1.0Gi | 1 | 2 |

Adjust up if the backend loads large models or embedding indexes at startup.
