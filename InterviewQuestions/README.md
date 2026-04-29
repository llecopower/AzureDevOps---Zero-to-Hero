
# Azure DevOps Interview Questions and Solutions

## Explain a typical structure of an Azure DevOps Pipeline

![image](https://github.com/piyushsachdeva/AzureDevOps-Zero-to-Hero/assets/40286378/ca03a698-fd46-4cc3-90ee-a9db5d945d0d)

- A trigger tells a Pipeline to run. It could be CI or Scheduled, manual(if not specified), or after another build finishes.
- A pipeline is made up of one or more stages. A pipeline can deploy to one or more environments.
- A stage organizes jobs in a pipeline, and each stage can have one or more jobs.
- Each job runs on one agent, such as Ubuntu, Windows, macOS, etc. A job can also be agentless.
- Each agent runs a job that contains one or more steps.
- A step can be a task or script and is the smallest building block of a pipeline.
- A task is a pre-packaged script that acts, such as invoking a REST API or publishing a build artifact.
- An artifact is a collection of files or packages published by a run.

## Which Deployment Strategy are you using in your organization? Explain the CICD flow:

<img width="980" height="555" alt="319430515-5892b678-3cee-45a9-89ed-a3ff6e468e8d" src="https://github.com/user-attachments/assets/08e55126-1ae2-4fc9-bf64-4e3d0988a711" />

### End-to-End CICD Pipeline using Azure DevOps Build and Release Pipeline

<img width="978" height="483" alt="319430556-17a5a6dc-5707-4239-9ba8-b914fd11b137" src="https://github.com/user-attachments/assets/a499cc38-80e8-4a41-a737-ca2b55903c4f" />

## What are some other deployment strategies?
- Recreate, Canary, Ring based, Rolling Update, A/B Deloyment, Feature Flag etc

## Which Build repository have you used with Azure DevOps? Explain the CICD flow:

<img width="1917" height="1020" alt="319430865-d9340a48-8c68-4b69-856a-2be1fb34c766" src="https://github.com/user-attachments/assets/f85c0e9d-d765-4ad3-b42e-ecf6ae7356d2" />

## What are views in an Artifacts feed?
Isolated placeholder in a repository can be categorized into Local, pre-release, Release, etc. Once your Artifacts are deployed to one environment, you promote them to another view so that they can be deployed in another environment, with that view being the trigger.
Could you explain with the help of an example?

## How would you use IaaC tools such as ARM Template or Terraform to automate Infra provisioning?

<img width="1582" height="995" alt="319431360-d8d9b95b-6b6f-4eb3-a0fc-aa01ac6a6d85" src="https://github.com/user-attachments/assets/83543c4e-5ab3-40ee-9801-a208667e0048" />


## What is the difference between Microsoft-hosted agents and self-hosted agents?

<img width="1982" height="900" alt="319431524-ed27ff4a-4786-48c9-a4df-f70bff2f7c7a" src="https://github.com/user-attachments/assets/ddf30b60-ece6-4e3e-ae05-f4c24aed1b67" />

## If Microsoft has already provided you with hosted agents, why would you set up self-hosted agents?
Any other limitations of Microsoft-hosted agents?

<img width="1874" height="896" alt="319431691-9c9b4780-5750-46a2-986e-e705bcee6ef6" src="https://github.com/user-attachments/assets/8253694e-8cb4-4b37-abcd-39ae8caf324a" />

## I am building a Docker image that is taking a long time and is huge. How can I reduce the image size and speed up the process?

Multistage Docker Build

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
## How can you ensure the security and privacy of secrets used in your pipeline to prevent them from being exposed?
- Azure Key Vault and access via a variable group
- Runtime variable, tokenize your files and replace token step inside the pipeline
- Third-party secret management service such as Hashicorp Vault

## What is the most difficult issue you have faced while working with Azure DevOps?
Pick up the issue you have faced while doing hands-on on Azure DevOps if you have followed this series or any other resource.

**Structure your answer in STAR format**

S: Situation

T: Task

A: Action

R: Result

## How would you implement CICD for a dockerized or microservice-based application with multiple services?
- Check out Week 10 for ACI and Week 11 for Kubernetes

