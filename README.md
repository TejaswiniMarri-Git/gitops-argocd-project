# GitOps with ArgoCD - End-to-End DevOps Project

A complete GitOps implementation using ArgoCD for automated Kubernetes deployments across multiple environments.

## 🎯 Project Overview

This project demonstrates a production-ready GitOps workflow where:
- Git is the single source of truth for infrastructure and applications
- ArgoCD automatically syncs changes from Git to Kubernetes
- Multi-environment deployments (dev/prod) with different configurations
- Automated deployments, self-healing, and rollback capabilities

## 🏗️ Architecture
```
GitHub Repository (Source of Truth)
        ↓
    ArgoCD (Sync Engine)
        ↓
    Kubernetes Cluster
    ├── Dev Namespace (1 replica)
    └── Prod Namespace (3 replicas)
```

## 📁 Project Structure
```
gitops-argocd-project/
├── apps/
│   └── sample-app/              # Application source code
│       ├── server.js
│       ├── package.json
│       ├── Dockerfile
│       └── .dockerignore
├── k8s-manifests/
│   └── sample-app/
│       ├── base/                # Base Kubernetes manifests
│       │   ├── deployment.yaml
│       │   ├── service.yaml
│       │   └── kustomization.yaml
│       └── overlays/
│           ├── dev/             # Dev-specific configs (1 replica)
│           │   └── kustomization.yaml
│           └── prod/            # Prod-specific configs (3 replicas)
│               └── kustomization.yaml
└── argocd-apps/                 # ArgoCD Application definitions
    ├── sample-app-dev.yaml
    └── sample-app-prod.yaml
```

## 🚀 Technologies Used

- **Kubernetes**: Container orchestration (Minikube v1.37.0)
- **ArgoCD**: GitOps continuous delivery tool
- **Docker**: Containerization platform
- **Kustomize**: Kubernetes native configuration management
- **Node.js**: Sample application runtime
- **Git/GitHub**: Version control and source of truth

## 📋 Prerequisites

- Minikube installed
- kubectl installed
- Docker installed
- Docker Hub account
- Git installed

## 🛠️ Setup Instructions

### 1. Start Minikube Cluster
```bash
minikube start --driver=docker --kubernetes-version=v1.28.3
```

### 2. Install ArgoCD
```bash
# Create namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for pods to be ready
kubectl wait --for=condition=ready pod -l app.kubernetes.io/name=argocd-server -n argocd --timeout=300s
```

### 3. Access ArgoCD UI
```bash
# Port forward
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

Access at: https://localhost:8080
- Username: `admin`
- Password: (from command above)

### 4. Create Namespaces
```bash
kubectl create namespace dev
kubectl create namespace prod
```

### 5. Deploy Applications
```bash
# Deploy dev environment
kubectl apply -f argocd-apps/sample-app-dev.yaml

# Deploy prod environment
kubectl apply -f argocd-apps/sample-app-prod.yaml
```

### 6. Verify Deployments
```bash
# Check ArgoCD applications
kubectl get applications -n argocd

# Check dev pods (1 replica)
kubectl get pods -n dev

# Check prod pods (3 replicas)
kubectl get pods -n prod
```

## 🔄 GitOps Workflow

1. **Make Code Changes**: Update application code or Kubernetes manifests
2. **Commit & Push**: Push changes to GitHub repository
3. **Auto-Sync**: ArgoCD detects changes within 3 minutes
4. **Deploy**: ArgoCD automatically applies changes to cluster
5. **Self-Heal**: ArgoCD ensures cluster state matches Git state

### Example: Updating Application Version
```bash
# 1. Update application code
cd apps/sample-app
# Make changes to server.js

# 2. Build new Docker image
docker build -t teja2409/sample-app:v1.2.0 .
docker push teja2409/sample-app:v1.2.0

# 3. Update manifest
cd ../../k8s-manifests/sample-app/base
# Update image tag in deployment.yaml

# 4. Commit and push
git add .
git commit -m "Update to v1.2.0"
git push origin main

# 5. ArgoCD auto-syncs within 3 minutes!
```

## 🧪 Testing the Application

### Dev Environment
```bash
kubectl port-forward -n dev svc/dev-sample-app 3000:80
curl http://localhost:3000
```

Expected Response:
```json
{
  "message": "Hello from GitOps v1.1 - Auto-deployed!",
  "version": "1.1.0",
  "environment": "dev"
}
```

### Prod Environment
```bash
kubectl port-forward -n prod svc/prod-sample-app 3001:80
curl http://localhost:3001
```

Expected Response:
```json
{
  "message": "Hello from GitOps v1.1 - Auto-deployed!",
  "version": "1.1.0",
  "environment": "production"
}
```

## 📊 Environment Differences

| Feature | Dev | Prod |
|---------|-----|------|
| Replicas | 1 | 3 |
| Environment Variable | "dev" | "production" |
| Resource Requests | 128Mi / 100m | 128Mi / 100m |
| Resource Limits | 256Mi / 200m | 256Mi / 200m |
| Auto-sync | ✅ Enabled | ✅ Enabled |
| Self-heal | ✅ Enabled | ✅ Enabled |

## 🔍 Monitoring & Troubleshooting

### View Application Status
```bash
# ArgoCD UI
https://localhost:8080

# Command line
kubectl get applications -n argocd
kubectl describe application sample-app-dev -n argocd
```

### View Application Logs
```bash
# Dev logs
kubectl logs -n dev -l app=sample-app --tail=50 -f

# Prod logs
kubectl logs -n prod -l app=sample-app --tail=50 -f
```

### Manual Sync (if needed)
```bash
# Via kubectl
kubectl patch application sample-app-dev -n argocd --type merge -p '{"operation":{"sync":{}}}'

# Via UI: Click "SYNC" button
```

## 🎓 Key Learnings & DevOps Concepts

1. **GitOps Principles**: Git as single source of truth
2. **Declarative Infrastructure**: Kubernetes manifests define desired state
3. **Automated Deployments**: No manual kubectl apply needed
4. **Multi-Environment Management**: Same code, different configs
5. **Self-Healing**: ArgoCD automatically fixes drift
6. **Audit Trail**: All changes tracked in Git history
7. **Rollback Capability**: Easy rollback via Git revert

## 🚀 Future Enhancements

- [ ] Add Prometheus & Grafana for monitoring
- [ ] Implement blue-green or canary deployments
- [ ] Add Helm charts for package management
- [ ] Integrate with CI/CD pipeline (GitHub Actions)
- [ ] Add security scanning (Trivy, Snyk)
- [ ] Implement secrets management (Sealed Secrets/Vault)
- [ ] Add ingress controller for external access
- [ ] Multi-cluster deployment

## 📚 Resources

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Kustomize Documentation](https://kustomize.io/)
- [GitOps Principles](https://www.gitops.tech/)

## 👤 Author

**Your Name**
- GitHub: [@TejaswiniMarri-Git](https://github.com/TejaswiniMarri-Git)
- Docker Hub: [teja2409](https://hub.docker.com/u/teja2409)

## 📝 License

This project is open source and available for learning purposes.

---

**⭐ If this project helped you, please give it a star!**