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
---
```

2. Kustomize Integration
Steps:

Create base and overlay directories.

Define overlays for dev, staging, prod.

Create an ArgoCD Application pointing to an overlay.

Example:

```yaml
Copy code
spec:
  source:
    path: kustomize/overlays/dev
    repoURL: https://github.com/user/repo.git
    kustomize:
      namePrefix: dev-
---
```

### Lesson 3.2: Secret Management & Best Practices
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

Use audit logs from external secret 

### Lesson 3.3: Resource Management & Sync 


1. Resource Ignore 

Used to ignore non-critical differences such 
 annotations.

Example:

```yaml
Copy code
spec:
  ignoreDifferences:
    - group: networking.k8s.io
      kind: Ingress
      jsonPointers:
        - /metadata/annotations
---
```

2. Custom Resource Health Checks

```yaml
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
---
```


3. Sync Policies

Automated Sync:

```yaml
Copy code
spec:
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
Manual Sync:
---

```

### STEPS IN PROJECT

1. Project Directory for project

![proj dir](image.png)

2. Minikube start and checking the status of the kubectl

![minikube start](image-1.png)


3. Minikube status and kubectl get nodes

![kubectl get nodes](image-2.png)

4. kubectl create namespace and get pods

![kubectl create namespace](image-3.png)

5. kubectl port-forward to access argocd dashboard

![kubectl port-forward](image-4.png)

6. ArgoCD dashboard

![argocd dashboard](image-5.png)

7. Helm create app my-helm-app

![helm create my-helm-app](image-6.png)

8. my-helm-app values.yaml file

![values.yaml](image-7.png)

9. Values.yaml file dir

![values.yaml dir](image-8.png)

10. git repoitory initialization, add, and commit

![git init, add, and commit](image-9.png)

11. Git push origin main

![git push](image-10.png)

12. Helm app mannual creation

![create helm app](image-11.png)

13. Helm app configuration

![configuration](image-12.png)

14. Helm values.yaml and templates

![values.yaml and templates](image-13.png)

15. Helm Values.yaml Update

![values.yaml update](image-14.png)

16. Git commit, and push

![git commit & push](image-15.png)

17. helm app created

![helm app](image-16.png)

18. Synchronization

![synchronize](image-17.png)

19. Synchronised and healthy

![synchronized](image-18.png)

20. app detail shows app health, sync ok etc

![app details](image-19.png)

21. Base deployment.yaml file

![base deployment.yaml](image-20.png)

22. Base deployment.yaml file directory

![DIR](image-21.png)

23. Base service. yaml file

![service.yaml](image-22.png)

24. Base Kustomization.yaml file

![kustomization.yaml](image-23.png)

25. Yaml directories

![yaml dir](image-24.png)

26. kustomization.yaml file for overlays dev.

![kustomization.yaml](image-25.png)

27. kustomization.yaml file for overlays prod.

![kustomization.yaml](image-26.png)

28. Kustomization file directories

![dir](image-27.png)

29. Kustomize build my kustomize app overlays prod

![kustomize build](image-28.png)

30. kustomize build my kustomize app overlays dev

![kustomize build](image-29.png)

31. creating a patch.yaml file for overlays dev

![patch.yaml](image-30.png)

32. kustomization.yaml file overlays dev updated

![updated kustomization.yaml](image-31.png)

33.  creating a patch.yaml file for overlays prod

![patch.yaml](image-32.png)

34. production kustomization.yaml file updated

![prod kustomization.yaml](image-33.png)

35. kubectl get deployments & kubectl get service

![kubectl get deployment & service](image-34.png)

36. error kustomize app could not access repository because there is no access token in the repository.

![error](image-35.png)

37. access token created in the github repository for kustomize app to have access.

![access token](image-36.png)

38. dev-kustomize-app created

![app created](image-37.png)

39. App created and synchronised and healthy

![app created](image-38.png)

40. app Healthy and other configuration details

![healthy](image-39.png)

41. ArgoCD app get dev-kustomize-app

![Argocd app get](image-40.png)


42. Argocd app create prod-kustomize-app

![created](image-41.png)

43. Argocd app get prod-kustomize-app

![argocd get](image-42.png)

44. kubectl get deployments and services

![kubectl get deployment](image-43.png)

45. kubectl log deployment prod-kustomize-demo

![log](image-44.png)

46. prod-kustomize-app created

![app created](image-45.png)

47. App details healthy, synced, sync ok

![app details](image-46.png)

48. kubectl create secret generic app secret

![secret](image-47.png)

49. base deployment.yaml file updated with secret

![updated](image-48.png)

50. kubectl apply -k and kubectl exec -it

![kubectl apply-k](image-49.png)

51. Overlays production patch.yaml updated with secret

![patch.yaml](image-50.png)

52. ArgoCD synchronize policy dev app for automated sync, pruning, and selfheal.

![sync policy ](image-51.png)

53.  ArgoCD synchronize policy prod app for automated sync, pruning, and selfheal.

![prod sync policy](image-52.png)

54. helm repo add hashicorp for installation 

![add hashicorp](image-53.png)

55. helm repo update for vault installation

![installation](image-54.png)

56. kubectl describe vault

![kubectl describe](image-56.png)

57. ArgoCD installation 

![argocd installation](image-55.png)

58. vault 

![vault](image-57.png)

59. vault secrets

![vault secret](image-58.png)

60. vault server

![vault server](image-59.png)

61. my-helm-app upgrade

![upgrade](image-60.png)

62. vault kv get secrets

![vault kv get secrets](image-61.png)

63. export vault addr and status

![export vault addr](image-62.png)

64. kubectl rollout restart deployment

![kubectl rollout](image-63.png)

65. kubectl get pods & logs



Useful for production approvals.


📚 References


ArgoCD Helm Integration Docs

ArgoCD Kustomize Docs

Kubernetes Secret Documentation

HashiCorp Vault Plugin

AWS Secret Manager
