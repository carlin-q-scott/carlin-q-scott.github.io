# Azure DevOps Pipelines Review

This is a CI/CD SaaS product that has gone through many rebrandings:  
1. Team Foundation Server (TFS) - Self-Hosted VSTS
1. Visual Studio Team Services (VSTS)
1. Azure Pipelines Server - Self-Hosted Azure DevOps
1. **[Azure DevOps](https://devops.azure.com)**
1. Azure Dev? - https://devops.azure.com redirects to https://dev.azure.com, so this might be the next re-branding.

I've used many CI/CD SaaS products so I will be mostly focusing on what's special good and special bad about Azure DevOps.

## Deployments

[Azure DevOps Deployments](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/deployment-jobs?view=azure-devops), like [GitLab Environments](https://docs.gitlab.com/ee/ci/environments/), use one or more defined environments to manage releases.

In GitLab, an environment is fully defined in your pipeline definition (.gitlab-ci.yml), whereas in Azure DevOps, it is managed through the Web UI or API. This leads to better separation of concerns and more robust safe-guards against accidental deployments, at the cost of discouraging Infrastructure as Code (IaC). It also strongly pushes you towards a single cloud solution; Azure.

To GitLab's credit, it also provides the ability to set up security gates around an environment once it has been defined by a pipeline definition. But only if you pay for the Ultimate subscription.