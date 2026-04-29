# Week11 Azure DevOps CICD Pipeline on Azure Kubernetes Services 🛳️ 🐳

## Kubernetes Architecture
<img width="820" height="493" alt="01" src="https://github.com/user-attachments/assets/87e095c1-eb3c-42c1-bb25-7157f31311fc" />

## Kubernetes components

### Kube APiServer


<img width="746" height="472" alt="02" src="https://github.com/user-attachments/assets/62d390cc-3a04-4d79-b29a-928e6db1ac98" />


### Kube Scheduler



<img width="745" height="447" alt="03" src="https://github.com/user-attachments/assets/4a657362-26cd-4515-a5b8-f1326e20e288" />


### Kube Controller Manager



<img width="853" height="480" alt="04" src="https://github.com/user-attachments/assets/c15eef40-d8fe-4c59-8815-a6d438d2632a" />


### ETCD



<img width="817" height="483" alt="05" src="https://github.com/user-attachments/assets/eef86a27-987d-420d-894a-dc6a06e8f057" />



## Pre-requisites

### Infrastructure provisioning

**Using the below script, you can provision infra for this demo**

The following resources will be provisioned:

* A Resource Group
* An AKS Kubernetes Cluster
* An Image container registry
* A SQL Server
* A SQL Database

``` bash

#!/bin/bash
REGION="westus"
RGP="day11-demo-rg"
CLUSTER_NAME="day11-demo-cluster"
ACR_NAME="day11demoacr"
SQLSERVER="day11-demo-sqlserver"
DB="mhcdb"


 #Create Resource group
 az group create --name $RGP --location $REGION

#Deploy AKS
az aks create --resource-group $RGP --name $CLUSTER_NAME --enable-addons monitoring --generate-ssh-keys --location $REGION

#Deploy ACR
az acr create --resource-group $RGP --name $ACR_NAME --sku Standard --location $REGION

#Authenticate with ACR to AKS
az aks update -n $CLUSTER_NAME -g $RGP --attach-acr $ACR_NAME

#Create SQL Server and DB
az sql server create -l $REGION -g $RGP -n $SQLSERVER -u sqladmin -p P2ssw0rd1234

az sql db create -g $RGP -s $SQLSERVER -n $DB --service-objective S0

```

## Change the Firewall settings of the SQL server


<img width="2840" height="1416" alt="06" src="https://github.com/user-attachments/assets/e2548df5-f5d7-4d58-8f5d-758f3c6e9aeb" />


## Setup Azure DevOps Project

### Pre-requisites

Make sure the below Azure DevOps extensions are installed and enabled in your organization
- Replace Token
- Kubernetes extension

Once the infra is ready, go to dev.azure.com --> Project --> repos 
and import the below git repo, which has the source code and pipeline code

https://github.com/llecopower/HealthClinic.git

### Build and Release Pipeline

- You can create your pipeline by following along the video or editing the existing pipeline. The below details need to be updated in the pipeline:
   - Azure Service connection
   - Token pattern
   - Pipeline variables
   - The Kubectl version should be the latest in the release pipeline
   - Secrets should be updated in the deployment step
   - ACR details in the pipeline should be updated
 
#### Steps in the pipeline

<img width="805" alt="image" src="https://github.com/piyushsachdeva/AzureDevOps-Zero-to-Hero/assets/40286378/64907bef-acbf-41f1-b3de-63b46681db3d">

 [Image source](https://azuredevopslabs.com/labs/vstsextend/kubernetes/)

## Destroy the resources at the end of the demo

```
#!/bin/bash


# Set environment variables
REGION="westus"
RGP="day11-demo-rg"
CLUSTER_NAME="week11-demo-cluster"
ACR_NAME="day11demoacr"
SQLSERVER="day11-demo-sqlserver"
DB="mhcdb"

# Function to handle errors
handle_error() {
    echo "Error: $1"
    exit 1
}

# Function to check if the resource exists
resource_exists() {
    az resource show --ids $1 &> /dev/null
}

# Delete Azure Kubernetes Service (AKS)
if resource_exists $(az aks show --resource-group $RGP --name $CLUSTER_NAME --query id --output tsv); then
    az aks delete --resource-group $RGP --name $CLUSTER_NAME || handle_error "Failed to delete AKS."
else
    echo "AKS not found. Skipping deletion."
fi

# Delete Azure Container Registry (ACR)
if resource_exists $(az acr show --name $ACR_NAME --resource-group $RGP --query id --output tsv); then
    az acr delete --name $ACR_NAME --resource-group $RGP || handle_error "Failed to delete ACR."
else
    echo "ACR not found. Skipping deletion."
fi

# Delete SQL Database
if resource_exists $(az sql db show --resource-group $RGP --server $SQLSERVER --name $DB --query id --output tsv); then
    az sql db delete --resource-group $RGP --server $SQLSERVER --name $DB || handle_error "Failed to delete SQL Database."
else
    echo "SQL Database not found. Skipping deletion."
fi

# Delete SQL Server
if resource_exists $(az sql server show --resource-group $RGP --name $SQLSERVER --query id --output tsv); then
    az sql server delete --resource-group $RGP --name $SQLSERVER || handle_error "Failed to delete SQL Server."
else
    echo "SQL Server not found. Skipping deletion."
fi

# Delete Resource Group
if resource_exists $(az group show --name $RGP --query id --output tsv); then
    az group delete --name $RGP || handle_error "Failed to delete Resource Group."
else
    echo "Resource Group not found. Skipping deletion."
fi

echo "Resources successfully deleted."

```

