## Instalacion de Cluster AKS-Virtual Nodes por Azure Portal
- **Basics**
  - **Subscription:** Free Trial or Pay-as-you-go
  - **Resource Group:** Creat New: aks-rg2
  - **Kubernetes Cluster Name:** aksdemo2
  - **Region:** (US) Central US
  - **Kubernetes Version:** Select what ever is latest stable version
  - **Node Size:** Standard DS2 v2 (Default one)
  - **Node Count:** 1
- **Node Pools**
  - **Virtual Nodes:** Enabled
  - leave to defaults
- **Authentication**
  - Authentication method: 	System-assigned managed identity
  - Rest all leave to defaults
- **Networking**
  - **Network Configuration:** Advanced
  - **Network Policy:** Azure
  - Rest all leave to defaults
- **Integrations**
  - Azure Container Registry: None
  - leave to defaults
- **Tags**
  - leave to defaults
- **Review + Create**
  - Click on **Create**

## Step-03: Verify Nodes & ACI
```
# Configure Command Line Credentials
az aks get-credentials --name aksdemo2 --resource-group aks-rg2

# Verify Nodes
kubectl get nodes 
kubectl get nodes -o wide

# Verify aci-connector-linux
kubectl get pods -n kube-system

# Verify logs of ACI Connector Linux
kubectl logs -f $(kubectl get po -n kube-system | egrep -o 'aci-connector-linux-[A-Za-z0-9-]+') -n kube-system
```
- We should see `virtual-node-aci-linux` node also listed for `kubectl get nodes` output
- **Sample Output**
```
Kalyans-MacBook-Pro:azure-aks-kubernetes-masterclass kdaida$ kubectl get nodes
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-87689508-vmss000000   Ready    agent   24m   v1.17.11
virtual-node-aci-linux              Ready    agent   21m   v1.14.3-vk-azure-aci-v1.2.1.1
Kalyans-MacBook-Pro:azure-aks-kubernetes-masterclass kdaida$
```


---
title: Azure AKS Virtual Nodes Mixed Mode Deployments
description: Deploy Applications in mixed mode to Virtual Nodes and AKS Nodepools
---

# Azure AKS Virtual Nodes Mixed Mode Deployments

## Step-01: Introduction
- We are going to deploy MySQL on regular AKS nodepools (default system nodepool)
- We are going to deploy **User Management Web Application** on Azure Virtual Nodes
- All this we are going to do using NodeSelectors concept in Kubernetes

[![Image](https://stacksimplify.com/course-images/azure-kubernetes-service-virtual-nodes-mixed-mode-deployments.png "Azure AKS Kubernetes - Masterclass")](https://stacksimplify.com/course-images/azure-kubernetes-service-virtual-nodes-mixed-mode-deployments.png)

## Step-02: Review Kubernetes Manifests
### MySQL Deployment 
- **File Name:** 04-mysql-deployment.yml
- No changes in it, MySQL pod will get scheduled on default AKS nodepool

### User Management Web Application Deployment
- **File Name:** 06-UserMgmtWebApp-Deployment.yml
- User Management web app pod will schedule on Azure Virtual Node
```yaml
# To schedule pods on Azure Virtual Nodes            
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule    
```

## Step-03: Deploy App & Test
```
# Deploy
kubectl apply -f kube-manifests/

# Verify Pods
kubectl get pods

# Verify Pods scheduled on which Nodes
kubectl get pods -o wide

# List Kubernetes Nodes
kubectl get nodes 
kubectl get nodes -o wide

# List Node Pools
az aks nodepool list --cluster-name aksdemo2 --resource-group aks-rg2 --output table

# Access Application
kubectl get svc
http://<Public-IP-from-Get-Service-Output>
Username: admin101
Password: password101
```


## Step-04: Clean-Up Apps
```
# Delete App
kubectl delete -f kube-manifests/

# Delete this new cluster created for Virtual Nodes (if you want to)
az aks delete --name aksdemo2 --resource-group aks-rg2
```