# GitOps with Argo CD on AKS: NGINX Deployment Demo

This repository demonstrates GitOps practices using Argo CD to automate Kubernetes deployments for a basic NGINX application on Azure Kubernetes Service (AKS) across `dev` and `prod` environments. Changes to the Git repository trigger automatic synchronization with the cluster, ensuring declarative and auditable workflows.

## Prerequisites

- An AKS cluster deployed on Azure.
- Azure Container Registry (ACR) configured and accessible from your AKS cluster.
- Argo CD installed and running in AKS cluster. (Setup as part of the project [terraform-aks](https://github.com/karishma-battina/terraform-aks))
 

## Project Overview

This project uses Argo CD to automate the deployment of an NGINX application to AKS, with:

- **Environment Isolation:** Separate `dev` and `prod` configurations using Kustomize overlays.
- **Git as Source of Truth:** All Kubernetes manifests and application configurations are version-controlled in this repository.
- **Automated Synchronization:** Argo CD automatically detects changes in the Git repository and synchronizes them with the AKS cluster, ensuring the desired state is always reflected.
- **Kustomization:** We leverage Kustomize for managing environment-specific configurations, making it easy to customize deployments for `dev` and `prod`.
- **Ingress:**  Ingress resources are configured to route traffic to the correct environment based on the DNS (`example.com/dev` and `example.com/prod`).

### Key Features

- Resource limits and replicas are configured differently for `dev` and `prod`.
- Argo CD auto-syncs changes from Git to the cluster.

## GitOps Workflow

```mermaid
graph LR;
A[Git Repository Changes (dev/prod)] --> B[Argo CD Detects Drift];
B --> C[Automatic Cluster Sync];
C --> D[Updated NGINX Deployment];
```

## Directory Structure

- `app/base/` - Contains the base configuration for the NGINX application.
- `app/overlays/dev/` - Customizations specific to the development environment.
- `app/overlays/prod/` - Customizations for the production environment.
- `app/src/index.html` - Sample content for the NGINX web server.
- `Dockerfile` - Defines the container image build process.
- `argocd-dev.yml` / `argocd-prod.yml` - Argo CD manifests for environment-specific deployments.



## Kustomization
| Feature       | Development           | Production        |
| -----------------|:------------------:| -----------------:|
| Replicas         | 1                  | 3                 |
| Resource Requests| Low (256Mi/100m)   | High (512Mi/500m) |
| Image Tag        | v1.0               | v1.2              |
| Labels           | env: dev           | env: prod         |
| Name Prefix      | dev-               | prod-             |
| Ingress host     | example.com/dev    | example.com/prod  |

---
## Steps
1. **Build the Docker Image**: The `Dockerfile` to create an NGINX container image and push it to Azure Container Registry (ACR).  
2. **Deploy with Kubernetes Manifests**: The `nginx-app.yml` deployment pulls the image from ACR and runs it on AKS.  
3. **Automated Sync with Argo CD**: Any changes made to the dev and prod manifests in the Git repository are automatically detected and applied to the cluster by Argo CD.

![ArgoCD UI](https://github.com/karishma-battina/gitops-argocd/blob/main/argocd-ui.png?raw=true)