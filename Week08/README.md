# Week 8 - Azure DevOps with Terraform 🏗

## How Terraform works

<img width="1731" height="890" alt="01" src="https://github.com/user-attachments/assets/4a4904a5-eb14-4387-bd81-837fb3bcc6e2" />


## Initialize the Terraform repo

<img width="1409" height="869" alt="02" src="https://github.com/user-attachments/assets/5b09f2eb-258f-43ce-8dbe-eaa87f9cb43d" />

## Run Terraform plan

<img width="1839" height="908" alt="03" src="https://github.com/user-attachments/assets/4208ba37-7f8c-4685-b1f9-a32faa1b5bf4" />


## Run Terraform apply

<img width="1801" height="960" alt="04" src="https://github.com/user-attachments/assets/c0a88e5b-5576-4e26-b9ff-170a866c85a4" />


### Import the below repository into Azure DevOps for Terraform configuration

https://github.com/llecopower/Terraform-Azure.git


## Let's implement this using Azure DevOps

<img width="1774" height="906" alt="05" src="https://github.com/user-attachments/assets/33041124-4f08-4479-a2ca-b2ce8c14dcd0" />


### You can use the below Azure cli commands to set the terraform remote backend, or you can do it via the portal

``` shell
#!/bin/bash
## The Storage account name must be unique, and the values below should match your backend.tf
RESOURCE_GROUP_NAME=demo-resources
STORAGE_ACCOUNT_NAME=techtutorialswithpiyush
CONTAINER_NAME=prod-tfstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location canadacentral

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME
```

## Azure DevOps CICD Pipeline

<img width="1582" height="995" alt="06" src="https://github.com/user-attachments/assets/1b10daf9-570a-44cf-b86f-1ae56c5b5e7e" />


## YAML Code for Build pipeline - Configuration Management (CM)

``` YAML
trigger: 
- main

stages:
- stage: Build
  jobs:
  - job: Build
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: TerraformTaskV4@4
      displayName: Tf init
      inputs:
        provider: 'azurerm'
        command: 'init'
        backendServiceArm: '${SERVICECONNECTION}'
        backendAzureRmResourceGroupName: 'demo-resources'
        backendAzureRmStorageAccountName: 'techtutorialswithpiyush'
        backendAzureRmContainerName: 'prod-tfstate'
        backendAzureRmKey: 'prod.terraform.tfstate'
    - task: TerraformTaskV4@4
      displayName: Tf validate
      inputs:
        provider: 'azurerm'
        command: 'validate'
    - task: TerraformTaskV4@4
      displayName: Tf fmt
      inputs:
        provider: 'azurerm'
        command: 'custom'
        customCommand: 'fmt'
        outputTo: 'console'
        environmentServiceNameAzureRM: '${SERVICECONNECTION}'
      
    - task: TerraformTaskV4@4
      displayName: Tf plan
      inputs:
        provider: 'azurerm'
        command: 'plan'
        commandOptions: '-out $(Build.SourcesDirectory)/tfplanfile'
        environmentServiceNameAzureRM: '${SERVICECONNECTION}'
      
    - task: ArchiveFiles@2
      displayName: Archive files
      inputs:
        rootFolderOrFile: '$(Build.SourcesDirectory)/'
        includeRootFolder: false
        archiveType: 'zip'
        archiveFile: '$(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip'
        replaceExistingArchive: true
    - task: PublishBuildArtifacts@1
      inputs:
        PathtoPublish: '$(Build.ArtifactStagingDirectory)'
        ArtifactName: '$(Build.BuildId)-build'
        publishLocation: 'Container'
```

## Release pipeline

![image](https://github.com/piyushsachdeva/AzureDevOps-Zero-to-Hero/assets/40286378/a492d66d-ed3e-468c-b68e-7d06891a8e92)


![image](https://github.com/piyushsachdeva/AzureDevOps-Zero-to-Hero/assets/40286378/7a0c53ea-0b7c-4098-b264-c66bb778fddf)

### Deployment stage

![image](https://github.com/piyushsachdeva/AzureDevOps-Zero-to-Hero/assets/40286378/66fe7d5d-b665-496a-b43b-e85a88f7271d)

### Destroy stage

![image](https://github.com/piyushsachdeva/AzureDevOps-Zero-to-Hero/assets/40286378/5d17e417-8a7d-49a6-8c9d-b120e236fde8)



>Note: Sharing some frequently faced issues here:

### If you are facing error while terraform init/plan or apply as below:

```bash
[0m[0mThe directory /home/vsts/work/r1/a/'/home/vsts/work/r1/a/' contains no 2024-09-23T09:38:10.6153480Z [31m│[0m [0mTerraform configuration files. 2024-09-23T09:38:10.6153661Z [31m╵[0m[0m 2024-09-23T09:38:10.6153815Z [0m[0m 2024-09-23T09:38:10.6156157Z
```

#### Solution: Check the below
Issue could be due to the fact that you have extracted the files in a different folder and running terraform commands from a different folder. Check the below examples:

- You published the build artifacts to the directory `'/home/vsts/work/1/a'` which is `$(Build.ArtifactStagingDirectory)` and you are running terraform apply from this directory `/home/vsts/work/r1/a` which is `$(System.DefaultWorkingDirectory)`. Make sure you are running terraform commands from the same directory in which you have pubished the artifacts. Also, please check the directory where Extract Files step is running.
- When you are extracting files in `$(System.DefaultWorkingDirectory)` it is extracting `$(System.DefaultWorkingDirectory)/buildid-build/buildid.zip` , suppose your build id is 41 so your folder will become `workingdirectory/41-build/41/` when you are doing init and apply , you are doing it in working directory, but ideally it should be in `workingdirectory/41-build/41` folder 


