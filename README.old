# GitOps ArgoCD Project

This project demonstrates GitOps practices using ArgoCD for Kubernetes deployments.

## Project Structure
- Step 1: ✅ Kubernetes cluster setup with Minikube
- Step 2: ArgoCD installation (Coming next)

## Prerequisites
- Minikube v1.37.0
- Kubernetes v1.28.3
- kubectl

## Cluster Info
- Cluster: minikube
- Nodes: 1 (control-plane)
- Status: Running
## Step 2: ArgoCD Installation ✅

### Components Installed:
- ArgoCD Application Controller
- ArgoCD ApplicationSet Controller
- ArgoCD Dex Server (SSO)
- ArgoCD Notifications Controller
- ArgoCD Redis
- ArgoCD Repo Server
- ArgoCD Server (UI/API)

### Access ArgoCD UI:
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Then navigate to: https://localhost:8080

### Get Admin Password:
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

