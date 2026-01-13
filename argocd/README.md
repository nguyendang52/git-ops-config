# ArgoCD Application Setup

## Prerequisites
- ArgoCD installed in your Kubernetes cluster
- kubectl configured to access your cluster

## Apply Applications

### Using kubectl:

```bash
# Apply dev environment
kubectl apply -f argocd/application-dev.yaml

# Apply prod environment
kubectl apply -f argocd/application-prod.yaml
```

### Using ArgoCD CLI:

```bash
# Dev environment
argocd app create gitops-demo-app-dev \
  --repo https://github.com/nguyendang52/git-ops-config.git \
  --path k8s/overlays/dev \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace gitops-demo-dev \
  --sync-policy automated \
  --auto-prune \
  --self-heal

# Prod environment
argocd app create gitops-demo-app-prod \
  --repo https://github.com/nguyendang52/git-ops-config.git \
  --path k8s/overlays/prod \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace gitops-demo-prod \
  --sync-policy automated \
  --auto-prune \
  --self-heal
```

## Configuration Details

### Key Settings:
- **repoURL**: Update with your actual Git repository URL
- **targetRevision**: Branch name (main/master) or specific commit/tag
- **path**: Path to kustomization overlay (dev or prod)
- **namespace**: Target namespace in Kubernetes

### Sync Policy:
- **automated**: Automatically syncs when Git changes are detected
- **prune**: Removes resources deleted from Git
- **selfHeal**: Reverts manual changes to match Git state
- **CreateNamespace**: Automatically creates the target namespace

## Verify Applications

```bash
# List all applications
argocd app list

# Get application details
argocd app get gitops-demo-app-dev

# Watch sync status
argocd app sync gitops-demo-app-dev --watch
```

## Access ArgoCD UI

```bash
# Port forward to access UI
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Then access: https://localhost:8080
