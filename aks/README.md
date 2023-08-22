
# Create AKS Cluster using Azure Portal

## Step-01: Introduction
- Create Azure AKS Cluster
- Connect to Azure AKS Cluster using Azure Cloud Shell
- Install Azure CLI and Connect to Azure AKS Cluster using Azure CLI on local desktop
- Deploy Sample Application on AKS Cluster and test


## Step-02: Create AKS Cluster
- Create Kubernetes Cluster
- **Basics**
  - **Subscription:** StackSimplify-Paid-Subscription
  - **Resource Group:** Creat New: aks-rg1
  - **Cluster preset configuration:** Standard
  - **Kubernetes Cluster Name:** aksdemo1
  - **Region:** (US) Central US
  - **Availability zones:** Zones 1, 2, 3
  - **AKS Pricing Tier:** Free
  - **Kubernetes Version:** Select what ever is latest stable version
  - **Automatic upgrade:** Enabled with patch (recommended)
  - **Node Size:** Standard DS2 v2 (Default one)
  - **Scale method:** Autoscale
  - **Node Count range:** 1 to 5
- **Node Pools**
  - leave to defaults
- **Access**
  - **Authentication and Authorization:** 	Local accounts with Kubernetes RBAC
  - Rest all leave to defaults
- **Networking**
  - **Network Configuration:** Azure CNI
  - Review all the auto-populated details 
    - Virtual Network
    - Cluster Subnet
    - Kubernetes Service address range
    - Kubernetes DNS Service IP Address
    - DNS Name prefix
  - **Traffic routing:** leave to defaults
  - **Security:** Leave to defaults
- **Integrations**
  - **Azure Container Registry:** None
  - All leave to defaults
- **Advanced**
  -  All leave to defaults
- **Tags**
  - leave to defaults
- **Review + Create**
  - Click on **Create**


## Step-03: Cloud Shell - Configure kubectl to connect to AKS Cluster
- Go to https://shell.azure.com
```t
# Template
az aks get-credentials --resource-group <Resource-Group-Name> --name <Cluster-Name>

# Replace Resource Group & Cluster Name
az aks get-credentials --resource-group aks-rg1 --name aksdemo1

# List Kubernetes Worker Nodes
kubectl get nodes 
kubectl get nodes -o wide
```

## Step-04: Explore Cluster Control Plane and Workload inside that
```t
# List Namespaces
kubectl get namespaces
kubectl get ns

# List Pods from all namespaces
kubectl get pods --all-namespaces

# List all k8s objects from Cluster Control plane
kubectl get all --all-namespaces
```

## Step-06: Local Desktop - Install Azure CLI and Azure AKS CLI
```t
# Install Azure CLI (MAC & Windows)
brew update && brew install azure-cli
choco install azure-cli

# Install kubectl
choco install kubernetes-cli

# Install Azure AKS Cli
az aks install-cli

# Login to Azure
az login

# Configure Cluster Creds (kube config)
az aks get-credentials --resource-group aks-rg1 --name aksdemo1

# List AKS Nodes
kubectl get nodes 
kubectl get nodes -o wide
```
- **Reference Documentation Links**
- https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest
- https://docs.microsoft.com/en-us/cli/azure/aks?view=azure-cli-latest

## Step-07: Deploy Sample Application and Test
- Don't worry about what is present in these two files for now. 
- By the time we complete **Kubernetes Fundamentals** sections, you will be an expert in writing Kubernetes manifest in YAML.
- For now just focus on result. 
```t
# Deploy Application
kubectl apply -f <folder_manifest>

# Verify Pods
kubectl get pods

# Verify Deployment
kubectl get deployment

# Verify Service (Make a note of external ip)
kubectl get service

# Access Application
http://<External-IP-from-get-service-output>
```

## Step-07: Clean-Up
```t
# Delete Applications
kubectl delete -f <folder_manifest>
```



