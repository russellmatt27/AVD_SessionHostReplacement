# Welcome to Azure Virtual Desktop (AVD) Session Host Replacer

## Overview

This tool automates the deployment and replacement of session hosts in an Azure Virtual Desktop host pool.

The best practice for AVD recommends replacing the session hosts instead of maintaining them,
the AVD Session Host Replacer helps you manage the task of replacing old session hosts with new ones automatically.

# Getting started

| Deployment Type           | Link                                                                                                                                                                                                                                                                                                                                                                                                                       |
| :------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Azure Portal UI           | [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#blade/Microsoft_Azure_CreateUIDef/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAVDSessionHostReplacer%2Fv0.2.9-beta.16%2Fdeploy%2Farm%2FDeployAVDSessionHostReplacer.json/uiFormDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAVDSessionHostReplacer%2Fv0.2.9-beta.16%2Fdeploy%2Fportal-ui%2Fportal-ui.json)  [![Deploy to Azure Gov](https://aka.ms/deploytoazuregovbutton)](https://portal.azure.us/#blade/Microsoft_Azure_CreateUIDef/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAVDSessionHostReplacer%2Fv0.2.9-beta.16%2Fdeploy%2Farm%2FDeployAVDSessionHostReplacer.json/uiFormDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAVDSessionHostReplacer%2Fv0.2.9-beta.16%2Fdeploy%2Fportal-ui%2Fportal-ui.json)  [![Deploy to Azure China](https://aka.ms/deploytoazurechinabutton)](https://portal.azure.cn/#blade/Microsoft_Azure_CreateUIDef/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAVDSessionHostReplacer%2Fv0.2.9-beta.16%2Fdeploy%2Farm%2FDeployAVDSessionHostReplacer.json/uiFormDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAVDSessionHostReplacer%2Fv0.2.9-beta.16%2Fdeploy%2Fportal-ui%2Fportal-ui.json) |
| Command line (Bicep/ARM)  | [![Powershell/Azure CLI](./docs/icons/powershell.png)](./docs/CodeDeploy.md)

# Pre-requisites
The Session Host Replacer requires permissions to manage resources in Azure and, if the session hosts are Entra joined, permissions in Entra. The recommended approach is to create a User Managed Identity, assign the necessary permissions to it, and use it for all instances of the Session Host Replacer.

If you do not select a User Managed Identity, the deployment will create a System Managed Identity and assign permissions to it, however, some extra permissions maybe needed that should be assigned manually after deployment. This is not recommended if you have more than one instance of the Session Host Replacer.

Detailed instructions on what permissions are needed and how to assign them are [here](docs/Permissions.md).

The Session Host Replacer requires permissions to manage resources in Azure and, if the session hosts are Entra joined, permissions in Entra. The recommended approach is to create a User Managed Identity, assign the necessary permissions to it, and use it for all instances of the Session Host Replacer.

If you do not select a User Managed Identity, the deployment will create a System Managed Identity and assign permissions to it. However, some additional permissions may need to be assigned manually after deployment. This is not recommended if you have more than one instance of the Session Host Replacer.

Detailed instructions on the required permissions and how to assign them are available [here](docs/Permissions.md).

## How it works?

There are two criteria for replacing a session host,
1. **Image Version:** Is there a new image version available? If so, we create a new session host with the new image version. This can be from Marketplace or  Gallery Image Definition.
2. **Session Host VM Age:** If the session host is older than a certain age, default is 45 days, we create a new session host and drain the old one.

The core of an AVD Session Host Replacer is an Azure Function App built using PowerShell, the function is triggered every hour to check each session host against the above criteria.

To deploy new session hosts, the function uses an ARM Template that is stored as a Template Spec at deployment time.

When deleting an old session host, the function will check if it has existing sessions and,

1. Place the session host drain mode.
2. Send a notification to all sessions.
3. Add a tag to the session host with a timestamp
4. Delete the session host once there are no sessions or the grace period has passed.
    - Delete VM
    - Remove from Host Pool
    - (If Entra Joined) Delete device from Entra ID


## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft
trademarks or logos is subject to and must follow
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.
