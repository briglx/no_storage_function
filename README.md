# Azure Function without a Storage Account Demo

This project demonstrates how to create an Azure Function without a Storage Account using several Azure technologies:

- Azure Resource Manager tempalates
- Azure Functions

## Background
Azure functions can not be deployed to a storage account without Public network access

Organizations that implement strict controls on storage accounts by limiting public network access make it impossible to create a function app.

- Azure Blob Storage: Maintain bindings state and function keys. Also used by task hubs in Durable Functions.
- Azure Files: File share used to store and run your function app code in a Consumption Plan and Premium Plan. Azure Files is set up by default, but you can create an app without Azure Files under certain conditions.
- Azure Queue Storage: Used by task hubs in Durable Functions and for failure and retry handling by specific Azure Functions triggers.
- Azure Table Storage: Used by task hubs in Durable Functions.


## Possible Solutions

- Arc-enabled Kubernetes cluster doesn't require a storage account
- Create app without Azure Files
	- Use arm template and don't include the WEBSITE_CONTENTAZUREFILECONNECTIONSTRING and WEBSITE_CONTENTSHARE application settings
	- scaling could be limited when running without Azure Files on Consumption and Premium plans

## Recomendations

- Avoid host ID collisions on shared storage accounts
- Optimize storage performance
    - Use a separate storage account for each function app
- Blob storage lifecycle management considerations
    - exclude containers used by Functions, which are prefixed with azure-webjobs or scm.
	
# Setup

The setup will deploy the function app

```bash
# Global
export RG_NAME=no_storage_func
export RG_REGION=westus

# Create Resource Group
az group create --name $RG_NAME --location $RG_REGION

# Deploy app with ARM template
az deployment group create --name "create_function_app" --resource-group $RG_NAME --template-file "template.json" --parameters @parameters.json

```

# References
- Storage considerations for function apps https://docs.microsoft.com/en-us/azure/azure-functions/storage-considerations?tabs=azure-cli#storage-account-requirements
- Override the host ID https://docs.microsoft.com/en-us/azure/azure-functions/functions-app-settings#azurefunctionswebhost__hostid
