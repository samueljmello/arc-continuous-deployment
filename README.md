# ARC
This repository contains workflows for idempotantly creating & updating Actions Runner Controller (ARC) scale sets for GitHub self-hosted runners. This uses the open source ARC for GitHub and Azure Kubernetes Service. Additionally, Azure Container Registry can be used to provide a custom container image for runners.

## Work In Progress
Future updates planned:

- Hashing & caching of scale-set value files to determine if configuration has changed and requires a `helm upgrade` or not (to prevent tear-down & rebuild every workflow execution)

## Prerequisites

### Infrastructure
- Azure Kubernetes Cluster (AKS)
- Azure Container Registry (ACR) and 
- Azure Service Principal connection information
- GitHub App installed on organizations, or a PAT with appropriate access (see [documentation](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/authenticating-to-the-github-api#deploying-using-personal-access-token-classic-authentication))
   - GitHub Apps are preferred over PAT because of the higher API rate limit and lack of tie-back to user identities, but can't be used with enterprise level runners.

### Secrets On Repository
|secret key|description|
|---|---|
|APP_ID|GitHub App ID|
|APP_INSTALL_ID|GitHub App Installation ID (different for each org)|
|APP_PRIVATE_KEY|The private key file (.pem) created for the app|
|AZURE_CLIENT_ID|Azure Service Principal Client ID|
|AZURE_CLIENT_SECRET|Azure Service Principal Client Secret|
|AZURE_SUBSCRIPTION_ID|Azure Service Principal Subscription ID|
|AZURE_TENANT_ID|Azure Service Principal Tenant ID|
|CUSTOM_PAT|A custom GitHub Personal Access Token to use when NOT using the GitHub App (required for enterprise level runners)|
|REGISTRY_LOGIN_SERVER|ACR registry host name|

## Installation Steps
1. Complete [Prerequisites](#prerequisites)
1. Move all GitHub Actions YAML files in `./workflows` to `./.github/workflows` 
  (I have them here so that my repo doesn't attempt to run them)
1. Run `controller.yaml` to make sure the ARC controller is present
2. Modify `./.github/workflows/scale-sets.yaml` to fit your needs, and be sure to add the corresponding values config YAML file overrides to the `./scale-sets/` directory (two included).
1. If using a custom image in ACR, make sure to update & run your `./Dockerfile` and `/customer-runner-image.yaml` workflow accordingly. Additionally, any values override files that use the image will need the corrected values (see `./scale-sets/enterprise.yaml` for example).

## Supportive Documentation
- [Quickstart for ARC](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/quickstart-for-actions-runner-controller)
- [Creating A Docker Image For ARC runners](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/about-actions-runner-controller#creating-your-own-runner-image)
- [Authenticating with ARC](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/authenticating-to-the-github-api#deploying-using-personal-access-token-classic-authentication)
- [ARC Values Overrides](https://github.com/actions/actions-runner-controller/blob/master/charts/gha-runner-scale-set/values.yaml)
- [Managing Scale Sets Documentation for ARC](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/deploying-runner-scale-sets-with-actions-runner-controller#using-a-private-container-registry)
