## How It Works

1. **GitLab CI/CD Pipeline**:
   - The pipeline builds a Docker image for the Node.js app.
   - It tests the application using `npm test`.

2. **ArgoCD**:
   - ArgoCD monitors the `gitops` folder in the repository.
   - When changes are pushed to the `main` branch, ArgoCD syncs the Kubernetes manifests to the cluster.

3. **Kubernetes**:
   - The application is deployed as a Kubernetes Deployment with 3 replicas.
   - A LoadBalancer Service exposes the application to the outside world.

---

## Prerequisites

- A Kubernetes cluster with ArgoCD installed.
- A GitLab repository for the application.


## Setup Instructions

### 1. Install ArgoCD
Create the `argocd` namespace:

   kubectl create namespace argocd


### Install ArgoCD on Kubernetes cluster

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

##### Access ArgoCD UI  ############
Port-forward the ArgoCD server:

kubectl port-forward svc/argocd-server -n argocd 8080:443

**Open https://localhost:8080 in your browser.**
Login with the default username admin and password (retrieve it using):

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

**Create the ArgoCD Application**
Apply the application.yaml manifest to ArgoCD:

kubectl apply -f gitops/application.yaml -n argocd

**Push Changes to GitLab**
Push changes to the main branch to trigger the GitLab pipeline and ArgoCD sync.

**GitOps Configuration**
All GitOps configurations are located in the gitops folder. ArgoCD continuously monitors this folder and ensures that the Kubernetes cluster matches the desired state.
