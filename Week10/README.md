# Week 10 - Azure DevOps CICD Pipeline on Azure Container Instances 🐳

## Understanding Virtual machine V/s Containers.


<img width="813" height="499" alt="01" src="https://github.com/user-attachments/assets/1c4dd8e7-a444-4043-aee8-9ec038919d27" />


## Challenges with the non-containerized applications


<img width="726" height="331" alt="02" src="https://github.com/user-attachments/assets/5a124118-344c-4ccb-9d50-53988ca8b0b2" />

## Docker Architecture


<img width="676" height="394" alt="03" src="https://github.com/user-attachments/assets/8c5b18df-aa60-43bd-861a-244193de9062" />


## Containerize a sample To-Do list web app written in React JS.

**GitHub repo for the App:**
https://github.com/llecopower/todoapp.git




**DockerFile**

```
FROM node:18-alpine AS installer
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
FROM nginx:latest AS deployer
COPY --from=installer /app/build /usr/share/nginx/html
```

## Benefits of a multi-stage docker file

The Dockerfile uses multi-stage builds with two stages ("installer" and "deployer").
The "installer" stage is focused on building the application and installing dependencies.
The final image (in the "deployer" stage) only includes the necessary artifacts from the build, reducing the overall size of the Docker image.

**Efficient Use of Layers:**
Each instruction in a Dockerfile creates a layer in the image.
By organizing the build process into stages, layers containing development dependencies are isolated in the "installer" stage, and these layers are discarded in the final image.
This results in a more efficient use of layers, making image builds and updates faster.

**Security:**
The "installer" stage contains the build tools and dependencies needed for the application but discards them afterward.
The "deployer" stage only includes the built artifacts, reducing the attack surface by excluding unnecessary tools and files.
This separation enhances the security of the final production image.

**Build Isolation and Clarity:**
The Dockerfile separates the build process (in the "installer" stage) from the deployment process (in the "deployer" stage).
This separation improves the clarity of the Dockerfile, making it easier to understand and maintain.

**Faster Builds:**
Since the "deployer" stage only copies the necessary files from the "installer" stage, the build process is more efficient and faster.
This is particularly beneficial in CI/CD pipelines where quick image builds are crucial.

**Optimized for Production:**
The final image is based on the lightweight Nginx image, which is optimized for serving web applications.
It includes only the built artifacts necessary for running the application, making it suitable for production environments.

## What are Azure container instances(ACI)


<img width="904" height="498" alt="04" src="https://github.com/user-attachments/assets/c070085e-086d-4be6-881b-36e8e72ec34e" />


## Azure DevOps CICD Pipeline to deploy to ACI

**Pipeline Code**:

``` YAML
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
- main

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: 'XXXX'
  imageRepository: 'todoapp'
  containerRegistry: 'NAME.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/Dockerfile'
  tag: '$(Build.BuildId)'

  # Agent VM image name
  vmImageName: 'ubuntu-latest'

stages:
- stage: Build
  displayName: Build and push stage
  jobs:
  - job: Build
    displayName: Build
    pool:
      vmImage: $(vmImageName)
    steps:
    - task: AzureCLI@2
      inputs:
        azureSubscription: 'XXXX'
        scriptType: 'bash'
        scriptLocation: 'inlineScript'
        inlineScript: 'az acr login --name=$(containerRegistry)'
    - task: Docker@2
      displayName: Build and push an image to container registry
      inputs:
        command: buildAndPush
        repository: $(imageRepository)
        dockerfile: $(dockerfilePath)
        containerRegistry: $(dockerRegistryServiceConnection)
        tags: |
          $(tag)
    - task: AzureCLI@2
      inputs:
        azureSubscription: 'XXXX'
        scriptType: 'bash'
        scriptLocation: 'inlineScript'
        inlineScript: |
          az container create \
          --name Week10app \
          --resource-group Week10-demo \
          --image $(containerRegistry)/$(imageRepository):$(tag) \
          --registry-login-server $(containerRegistry) \
          --registry-username Week10demo  \
          --registry-password XXXX \
          --dns-name-label aci-demo-piyush101
```
