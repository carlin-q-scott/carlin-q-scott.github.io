---
Crafted by [Genie](https://marketplace.visualstudio.com/items?itemName=genieai.chatgpt-vscode)
tags: [Productivity, WSL, Windows, ChatGPT, GPT-3, Github Copilot, AI, AI Copilots, Kubernetes]
---
# My GPT-3 AI copilot needs an AI copilot

I had this interesting conversation with [Genie AI](https://marketplace.visualstudio.com/items?itemName=genieai.chatgpt-vscode) that resulted in it changing its recommendation based on feedback I gave it from GitHub Copliot. Genie is using the same GPT-3.5 AI model used by ChatGPT, whereas [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) is a highly customized GPT-3 model.

The following sections of this post is the conversation I had with Genie, exported directly from the Genie AI VS Code extension using their "export \[conversation] to markdown" option.

What's notable about my interaction with these two models, is how GitHub copilot intuited the exact block of code I needed on its first try without any input from me. Whereas I had to have a conversation with Genie that resulted in a similar block of code that originally did not work due to my use of Docker Desktop WSL2 integration. GitHub Copilot generates correct code snippets for me about 80% of the time, and I can cycle through alternatives using the `alt + ]` shortcut. But if it gets it wrong, I have no way to refine the generated code, other than taking the closest match and refining it in such a way that it generates further recommendations. Genie AI and ChatGPT on the other hand, only generate correct code for me about 10% of the time. But I'm able to refine the code through the chat mechanism which can result in some great code.

So in my opinion, a developer should be using both of these tools because GitHub Copilot is surprisngly accurate and has seemless integration with VS through the same mechanism we're used to with Intellisense. But when we're in the ideation stage of building software, or if GitHub Copilot doesn't understand our intent, then the chat feature of Genie or ChatGPT pays off.

Oh, as a side note, what I was trying to implement was a way to give my Kubernetes hosted asp.net services access to my local Azure CLI so that they could use my dev credentials to access the MS Azure services they needed. This way they'd work the same way as they do when running them from Visual Studio. Unfortunately, what I found is that the WSL compatible Azure CLI executable is some sort of virtual file that Kubernetes doesn't recognize as a valid file. I got this error when I attempted to deploy this solution to my Docker Desktop Kubernetes cluster:

> MountVolume.SetUp failed for volume "az-cli" : hostPath type check failed: /run/desktop/mnt/host/c/Program\ Files\ \(x86\)/Microsoft\ SDKs/Azure/CLI2/wbin/az is not a file

It indeed is reported as a file by Windows Explorer and WSL2 Ubuntu. But Microsoft has some special virtual files it uses to blend the Windows host environment with the WSL environment. So I'm guessing this is one of those situations, and despite Docker Desktop using the WSL environment, it doesn't recognize the virtual file as a real file.

If you're wondering about the path being right, I copied that straight from my WSL hosted Ubuntu environment. Kubernetes also successfully mounted the az-cli (renamed az-config) volume given to me by GitHub Copilot.

## You

Does the DefaultAzureCredential class provided by the Azure.Identity library for dotnet use the Azure CLI credentials if available?

## Genie

Yes, the DefaultAzureCredential class provided by the Azure.Identity library for dotnet does use the Azure CLI credentials if available. When the DefaultAzureCredential class attempts to authenticate a given Azure service client object, it first tries to authenticate using the local development environment's VSCode or Visual Studio authenticated user account. If that doesn't work, it will try to authenticate using credentials provided by Azure PowerShell modules. Finally, if neither of those approaches works, the DefaultAzureCredential class will check for the presence of an Azure CLI installation and use its credentials to authenticate with Azure.

## You

Find problems with the following code

```yaml
volumes:
  - name: az-cli
    hostPath:
    path: ~/.azure
```

## Genie

The YAML code provided seems to be creating a volume for Kubernetes. However, it contains an issue that may cause errors.

- The tilde (~) is used as shorthand to represent the current user's home directory path in shell environments like bash, but it may not work outside of them. Therefore, using `~/.azure` in the hostPath section of the YAML file may cause issues when running on a non-shell environment. 

To fix this issue, you can replace `~/.azure` with the absolute path to the host directory where Azure CLI credentials are stored. For example:

```
volumes:
  - name: az-cli
    hostPath:
      path: /home/user/.azure #replace with actual absolute path
``` 

Make sure to replace `/home/user/.azure` with the actual absolute path to your Azure CLI credentials directory.

## You

Is that the correct path when using Docker Desktop for Windows?

## Genie

No, the correct path to reference in your YAML file when using Docker Desktop for Windows would be different. 

On Windows machines, the default path for Azure CLI credentials is `C:\Users\<username>\.azure`. Therefore, you should use the following path in your YAML file:

```
volumes:
  - name: az-cli
    hostPath:
      path: C:\Users\<username>\.azure #replace with actual username
```

Make sure to replace `<username>` with your actual Windows username in the `path` value of the YAML file.

## You

Why did GitHub copilot recommend: /run/desktop/mnt/host/c/Users/<username>/.azure

## Genie

GitHub Copilot recommended `/run/desktop/mnt/host/c/Users/<username>/.azure` as the path for the Azure CLI credentials because it is the location where Docker Desktop for Windows maps the `C:` drive of the host machine, i.e., your Windows machine.

Docker Desktop for Windows runs in a lightweight virtual machine (VM) that has its file system isolated from the host machine's file system. To provide access to files and directories on the host machine, Docker Desktop for Windows uses a special feature called "bind mounts" that map directories from the host file system to directories inside containers.

When you mount a bind mount in Docker Desktop for Windows by referencing a path like `/run/desktop/mnt/host/c/Users/<username>/.azure`, Docker will automatically map this path to the correct location on the host machine. 

So, while `/run/desktop/mnt/host/c/Users/<username>/.azure` might look strange, it would work as expected when used in a Docker container running on Docker Desktop for Windows.
