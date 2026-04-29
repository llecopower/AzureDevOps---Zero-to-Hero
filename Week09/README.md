# Week9 Azure DevOps Self-hosted agents on Virtual Machine Scale Sets 🏗

## Microsoft-hosted vs. self-hosted agents

<img width="991" alt="image" src="https://github.com/piyushsachdeva/AzureDevOps-Zero-to-Hero/assets/40286378/f95a9cf1-dc9f-465f-8f12-ea5b0b1f6287">

## Use case of self-hosted agents



<img width="937" height="448" alt="293014600-d6ffbd95-00db-4a07-b7d6-f5c0c1a56619" src="https://github.com/user-attachments/assets/40973779-faad-4a68-b2b3-942068008faa" />


### Ways to setup self-hosted agents: 
- Azure Virtual Machine
- Azure Virtual Machine Scale Sets (VMSS)
- Containers etc.
  
### What is a Virtual machine scale set
- A group of one or more identical Virtual machines that can be scaled out and scaled in based on the demand/schedule and manual actions.
- This ensures high availability and fault tolerance so that if one VM crashes, another gets provisioned using the template.
- This is similar to AWS AutoScaling Groups or GCP Managed Instance Groups.
  

