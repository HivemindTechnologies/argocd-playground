# Argo CD Playground

# What is Argo CD?

Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes.

![argo-cd-schema](readme_contents/argo-cd-schema.png)

# Features

- Declarative tool for Continuous Delivery of the Kubernetes applications
- Control plane that extends GitOps for Infrastructure deployments
- Live State vs Desired Target State
- Scalability, Multi-tenancy, Security, Extensibility
- Extension of Kubernetes cluster

# Demo Environment

1. Install Argo CD via Helm Chart
2. Connect the Git Repo (https://github.com/HivemindTechnologies/argocd-playground)
3. Deploy the Application via Argo CD

![demo-lab-schema](readme_contents/demo-lab-schema.png)

# Install Argo CD via Helm Chart

1. Create Argo CD namespace:

```yaml
kubectl create namespace argocd
```

1. Install Argo CD with Helm Chart inside the Namespace created at the point above:

```bash
helm repo add argo https://argoproj.github.io/argo-helm /
helm install argocd argo/argo-cd -n argocd
```

# Connect the Git Repo

1. Open the Argo CD UI by port-forwarding the service:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
2. Open the Argo CD UI in the browser: https://localhost:8080
3. Login with the default credentials by using the password stored in the secret argocd-initial-admin-secret:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

4. Click on **Settings** → **Repositories**
5. Click on **Connect Repo**
6. Fill the form with all the Git repo information and click on **Connect**

# Deploy the Application via Argo CD

1. Create the the Argo CD application manifest file called f.i. cool-app.yaml:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: cool-app
  namespace: argocd
spec:
  project: default
  source:
    path: cool-app
    repoURL: https://github.com/HivemindTechnologies/argocd-playground.git
    targetRevision: main
  destination:
    namespace: cool-app
    server: https://kubernetes.default.svc
  syncPolicy:
    automated:
      selfHeal: true
      prune: true
    syncOptions:
      - CreateNamespace=true
```

2. Deploy the application manifest in Kubernetes namespace called argocd:

```yaml
kubectl apply -f cool-app.yaml -n argocd
```
