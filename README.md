# Pass Genius Kubernetes Manifests Project
This repository contains Kubernetes manifest files for deploying the Pass Genius web services on a Kubernetes cluster. Follow the steps below to set up your Kubernetes environment, configure the manifests, and deploy your application.



## Table of Contents
- [Prerequisites](#prerequisites)
- [Setup](#setup)
    - [1. Install Minikube](#1-start-minikube)
    - [2. Create Secrets](#2-create-secrets)
    - [3. Setup Kong](#3-setup-kong) 
    - [4. Apply Kubernetes Manifests](#4-apply-kubernetes-manifests)
    - [5. Access the Application](#5-access-the-application)
    - [6. Setup ArgoCD](#6-setup-argocd) (Optional for better CI/CD development)


## Prerequisites

Before you begin, ensure you have the following installed on your local machine:

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [Helm](https://helm.sh/docs/intro/install/) 
- [Minikube](https://minikube.sigs.k8s.io/docs/start/) 
- [Docker](https://www.docker.com/products/docker-desktop/) 
- [kustomize](https://github.com/kubernetes-sigs/kustomize/releases/)


## Setup

### 1. Start Minikube
```bash
  minikube start 
```
### 2. Create Secrets
The app relies on certain environmental variables, which need to be rendered from the kubernetes secrets.  
- JWT SECRETS  
```bash
  kubectl create secret generic jwt-secret --from-literal=key=<yout-jwt-key> --from-literal=secret=<your-jwt-secret>  --from-literal=algorithm=<jwt-algorithm>
```
- Email Secret
```bash
 kubectl create secret generic email-secret --from-literal=app-password=<your-app-password>
```
### 3. Setup Kong
Set up Kong as the API gateway using Helm:
```bash
 helm repo add kong https://charts.konghq.com
 helm repo update
 helm install kong/kong --generate-name --set ingressController.installCRDs=false --set proxy.type=LoadBalancer --set ingressController.enabled=true
```

```bash
 helm install kong/kong --generate-name \
  --set ingressController.installCRDs=false \
  --set proxy.type=LoadBalancer \
  --set ingressController.enabled=true \
  --set admin.enabled=true \
  --set admin.http.enabled=true \
  --set admin.http.servicePort=8001 \
  --set admin.http.type=LoadBalancer
```
### 4. Apply Kubernetes Manifests
To deploy the app, navigate to the `overlays/dev` directory and run the following command: 
```bash
 kustomize build --enable-alpha-plugins | kubectl apply -f -
```

### 5. Access the Application
To access the application using the custom domain passgenius.local, you need to add an entry to your local hosts file. This will map the domain to your local machine. Follow these steps:
1. **Open the Hosts File**:
    - Navigate to the `hosts` file located at:
      ```
      C:\Windows\System32\drivers\etc\hosts
      ```
    - You will need administrative privileges to edit this file. You can use a text editor like Notepad with "Run as administrator" to open it.
   
2. **Add the Domain Mapping**:
    - At the end of the file, add the following line:
      ```plaintext
      127.0.0.1 passgenius.local
      ```
    - This line maps the custom domain `passgenius.local` to the loopback address (your local machine).

### 6. Setup ArgoCD
```bash
 kubectl create namespace argocd
 kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
 kubectl port-forward svc/argocd-server -n argocd 9000:443
```  
Access the AroCD UI [hier](http://localhost:9000). From there, create an app and provide access to the GitHub URL of this repository.







