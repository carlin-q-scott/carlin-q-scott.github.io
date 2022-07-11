---
description: A dotnet DevOp's review of Microsoft Azure DevOps Pipelines CI/CD product.
tags: [ web development, administration, infrastructure, devops ]
---
# Azure DevOps Pipelines Review

This is a CI/CD SaaS product that has gone through several rebrandings:

| Self Hosted                  | Cloud SaaS                                   |
| ---------------------------- | -------------------------------------------- |
| Team Foundation Server (TFS) | Visual Studio Team Services (VSTS)           |
|                              | Azure Pipelines                              |
| Azure DevOps Server          | **[Azure DevOps](https://devops.azure.com)** |
|                              | Azure Dev?                                   |

> https://devops.azure.com redirects to https://dev.azure.com, so this might be the next re-branding.

I've used many CI/CD SaaS products so I will be mostly focusing on what's special good and special bad about Azure DevOps (ADO).

## Jobs and Stages

This is a pain point for using ADO and their support staff don't seem to care.

If you're using approvals and permissions to protect your environments, and **you should**, then you're going to have to deal with some odd choices made by Microsoft.

Idealy, I would only use 2-3 jobs in my pipeline:
1. Build
1. Test
1. Deploy

In practice, I've combined build and test, because they need to run linearly and are both pre-reqs for deployment. If jobs were lighter weight in ADO then I would have left them as separate jobs and used dependencies.

What's heavy about jobs?
* You have to publish and download artifacts between jobs using a publish and then a download task
    * The defaults for these tasks are mysterious:  
      * Some docs say the default artifact name is drop, but it's actually the name of your Job. And if your Job is inside a Stage, then it's the {Stage Name}.{Job Name}. Maybe if you're using the default job and stage, then the name of the artifact is drop?
      * Artifact names are important because when you download artifacts, they are downloaded to "Pipeline.Workspace/{artifact name}/" by default.
      * Most other build systems simply preserve the paths of artifacts between jobs. So if I saved the bin or output directory as a build artifact, then it's downloaded to the exact same location for my test and deployment jobs. In ADO, we can explicitly set which artifact to download and where to download it to. So this same standard functionality can be achieved with some repetitive configuration.
    * Only one artifact can be published per publish task; this can be a directory but if you want to filter files, then you'll have to commit a special toml file to set the filter criteria for that directory.
* Job dependencies only manage execution order and don't impact artifact sharing

## Deployments

[ADO Deployments](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/deployment-jobs?view=azure-devops), like [GitLab Environments](https://docs.gitlab.com/ee/ci/environments/), use one or more defined environments to manage releases.

In GitLab, an environment is fully defined in your pipeline definition (.gitlab-ci.yml), whereas in ADO, it is managed through the Web UI or API. This leads to better separation of concerns and more robust safe-guards against accidental deployments, at the cost of discouraging Infrastructure as Code (IaC). It also strongly pushes you towards a single cloud solution; Azure.

To GitLab's credit, it also provides the ability to set up security gates around an environment once it has been defined by a pipeline definition. But only if you pay for the Ultimate subscription.

### Conditions and Approvals

You'll need a pipeline stage for that.

In order to not fail an entire pipeline because you cannot deploy it, you must set up a deployment Stage with a Stage level condition that is coupled to your Environment security settings. For instance, if only certain protected branches are allowed to deploy to an environment, then the deployment stage must not try to run when it's not on those branches. This is because ADO processes the environment security settings before it processes job level conditions, and it doesn't infer that if you can't deploy, then maybe just don't try to deploy?