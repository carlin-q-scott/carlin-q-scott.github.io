---
description: How to integration test an ASP.NET Core application using DI.
tags: [ dotnet, asp.net, dotnet core, integration testing, testing, NUnit, xUnit, C# ]
---
# Azure Pipelines Private NuGet Feed

## Consuming Packages in Visual Studio

Visual Studio does not work well with the [Microsoft Artifacts Credential Provider for Azure Artifact Feeds](https://github.com/microsoft/artifacts-credprovider) due to [it hiding the OAuth flow](https://github.com/microsoft/artifacts-credprovider/issues/292). 

To work around this issue, you can create a Personal Access Token (PAT) in Azure DevOps and set it as your feed password:

```powershell
dotnet nuget update source "{feed name}" -s ""https://pkgs.dev.azure.com/{organization}/_packaging/{feed name}/nuget/v3/index.json" -u {email} -p {PAT} --valid-authentication-types basic
```

I think this works way better than the MS recommended approach because I don't have to authenticate multiple times for each nuget execution path (VS, dotnet, .NET Framework), and I can avoid re-authenticating for up to a year, rather than every month or so.
