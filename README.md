# GitOps Configuration Management  
### Module 3: Advanced Configuration Management in ArgoCD

This project provides a comprehensive implementation of **advanced configuration management** using **ArgoCD**, focusing on Helm, Kustomize, secret management, and resource/sync policy customization in a GitOps workflow.  
It is designed to demonstrate real-world GitOps best practices used in modern Kubernetes environments.

---

## 📚 Table of Contents
- [Overview](#overview)
- [Learning Objectives](#learning-objectives)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Lesson 3.1: Managing Configurations with Helm & Kustomize](#lesson-31-managing-configurations-with-helm--kustomize)
- [Lesson 3.2: Secret Management & Best Practices](#lesson-32-secret-management--best-practices)
- [Lesson 3.3: Resource Management & Sync Policies](#lesson-33-resource-management--sync-policies)
- [References](#references)

---

## 📌 Overview

This module demonstrates how to:
- Manage application configurations using **Helm** and **Kustomize** in ArgoCD.
- Securely manage secrets using Kubernetes Secrets and external secret managers.
- Customize ArgoCD resource management and sync behavior for enterprise-level deployments.

---

## 🎯 Learning Objectives
By completing this module, you will be able to:

✔ Integrate **Helm charts** and **Kustomize overlays** with ArgoCD  
✔ Implement **secure secret management** (Vault, AWS Secrets Manager, Kubernetes Secrets)  
✔ Customize **resource behaviors**, **health checks**, and **sync policies** in ArgoCD  
✔ Apply GitOps principles across environments (dev, staging, prod)

---

## 🛠 Prerequisites
- Kubernetes cluster (kind, Minikube, EKS, GKE, AKS, etc.)
- ArgoCD installed and configured
- Git repository access
- kubectl + ArgoCD CLI installed
- Basic knowledge of Helm and Kustomize

---

## 📁 Project Structure (Example)

my-app/
├── helm/
│ ├── Chart.yaml
│ ├── values.yaml
│ └── templates/
│ ├── deployment.yaml
│ ├── service.yaml
│ └── ingress.yaml
└── kustomize/
├── base/
│ ├── kustomization.yaml
│ ├── deployment.yaml
│ └── service.yaml
└── overlays/
├── dev/
│ ├── kustomization.yaml
│ └── patch.yaml
└── prod/
├── kustomization.yaml
└── patch.yaml

yaml
Copy code

---

# 📘 Lesson 3.1: Managing Configurations with Helm & Kustomize

## **1. Helm Integration**
Steps:
- Create or use an existing Helm chart.
- Push the chart to your Git repository.
- Create an ArgoCD Application referencing the chart.
- Configure values using ArgoCD UI or CLI.

Example ArgoCD Application:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp-helm
spec:
  source:
    repoURL: https://github.com/user/repo.git
    path: helm/my-app
    helm:
      valueFiles:
        - values.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: myapp
2. Kustomize Integration
Steps:

Create base and overlay directories.

Define overlays for dev, staging, prod.

Create an ArgoCD Application pointing to an overlay.

Example:

yaml
Copy code
spec:
  source:
    path: kustomize/overlays/dev
    repoURL: https://github.com/user/repo.git
    kustomize:
      namePrefix: dev-
📘 Lesson 3.2: Secret Management & Best Practices
1. Kubernetes Secrets
Understand base64 encoding (not encryption).

Create secrets manually:

bash
Copy code
kubectl create secret generic my-secret \
  --from-literal=password=mypassword
Reference in deployments using env or volumeMounts.

2. External Secret Managers
🔐 HashiCorp Vault
Store secrets securely

Configure Vault policies

Use the ArgoCD Vault Plugin to inject secrets during render time

🔐 AWS Secrets Manager
Store secrets in AWS

Grant access using IAM roles

Integrate using operators or custom tooling

Best Practices
Least privilege IAM policies

Enable secret rotation

Use audit logs from external secret managers

📘 Lesson 3.3: Resource Management & Sync Policies
1. Resource Ignore Policies
Used to ignore non-critical differences such as annotations.

Example:

yaml
Copy code
spec:
  ignoreDifferences:
    - group: networking.k8s.io
      kind: Ingress
      jsonPointers:
        - /metadata/annotations
2. Custom Resource Health Checks
yaml
Copy code
resource.customizations: |
  custom.io/MyResource:
    health.lua: |
      hs = {}
      if obj.status ~= nil then
        if obj.status.condition == "Healthy" then
          hs.status = "Healthy"
        else
          hs.status = "Degraded"
        end
      end
      return hs
3. Sync Policies
Automated Sync:
yaml
Copy code
spec:
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
Manual Sync:
Useful for production approvals.

📚 References
ArgoCD Helm Integration Docs

ArgoCD Kustomize Docs

Kubernetes Secret Documentation

HashiCorp Vault Plugin

AWS Secret Manager
