---
title: 使用 PowerShell 或模板启用用于 VM 的 Azure Monitor （经典）
description: 本文介绍如何使用 Azure PowerShell 或 Azure 资源管理器模板为一个或多个 Azure 虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 4fc5afe3bbb4b2ccf2329432347b23fe9a69c5ea
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977679"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>使用 Azure PowerShell 或资源管理器模板启用用于 VM 的 Azure Monitor （预览版）

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文介绍如何通过使用 Azure PowerShell 或 Azure 资源管理器模板为 Azure 虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor （预览版）。 在此过程结束时，你将成功开始监视所有虚拟机，并了解是否有任何性能或可用性问题。

## <a name="set-up-a-log-analytics-workspace"></a>设置 Log Analytics 工作区

如果没有 Log Analytics 工作区，则需要创建一个。 在继续执行配置步骤之前，请查看[先决条件](vminsights-enable-overview.md#log-analytics)部分中建议的方法。 然后，可以使用 Azure 资源管理器模板方法完成用于 VM 的 Azure Monitor 的部署。

### <a name="enable-performance-counters"></a>启用性能计数器

如果解决方案引用的 Log Analytics 工作区尚未配置为收集解决方案所需的性能计数器，则需要启用性能计数器。 可以通过以下两种方式之一执行此操作：
* 手动方式，如 [Log Analytics 中的 Windows 和 Linux 性能数据源](../../azure-monitor/platform/data-sources-performance-counters.md)所述
* 下载并运行[Azure PowerShell 库](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)中提供的 PowerShell 脚本

### <a name="install-the-servicemap-solution"></a>安装 ServiceMap 解决方案

此方法包含一个 JSON 模板，其中指定了用于在 Log Analytics 工作区中启用解决方案组件的配置。

如果不知道如何使用模板部署资源，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

若要使用 Azure CLI，首先需要在本地安装并使用 CLI。 必须运行 Azure CLI 2.0.27 版或更高版本。 若要确定版本，请运行 `az --version`。 若要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. 使用 *installsolutionsforvminsights.json* 文件名将此文件保存到某个本地文件夹中。

1. 捕获 *WorkspaceName*、*ResourceGroupName* 和 *WorkspaceLocation* 的值。 *WorkspaceName* 的值为 Log Analytics 工作区的名称。 *WorkspaceLocation* 的值是在其中定义工作区的区域。

1. 已做好部署此模板的准备。

    * 请在包含模板的文件夹中使用以下 PowerShell 命令：

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        配置更改可能需要几分钟才能完成。 完成后，将显示一条类似于以下内容的消息，其中包括结果：

        ```powershell
        provisioningState       : Succeeded
        ```

    * 使用 Azure CLI 运行以下命令：

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        配置更改可能需要几分钟才能完成。 完成后，将显示一条类似于以下内容的消息，其中包括结果：

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板启用

我们创建了示例 Azure 资源管理器模板，用于载入虚拟机和虚拟机规模集。 这些模板包括可用于对现有资源启用监视并创建启用了监视的新资源的方案。

>[!NOTE]
>模板需要部署在与要在其上导入的资源相同的资源组中。

如果不知道如何使用模板部署资源，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

若要使用 Azure CLI，首先需要在本地安装并使用 CLI。 必须运行 Azure CLI 2.0.27 版或更高版本。 若要确定版本，请运行 `az --version`。 若要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="download-templates"></a>下载模板

Azure 资源管理器模板在存档文件（.zip）中提供，你可以从 GitHub 存储库[下载](https://aka.ms/VmInsightsARMTemplates)该文件。 文件的内容包括表示每个部署方案的文件夹，以及模板和参数文件。 在运行之前，请修改参数文件并指定所需的值。 请勿修改模板文件，除非你需要对其进行自定义以满足你的特定要求。 修改参数文件后，可以使用本文后面所述的以下方法对其进行部署。

下载文件包含用于不同方案的以下模板：

- 如果虚拟机已存在， **ExistingVmOnboarding**模板会启用用于 VM 的 Azure Monitor。
- **NewVmOnboarding**模板创建虚拟机，并启用用于 VM 的 Azure Monitor 来监视它。
- 如果虚拟机规模集已存在， **ExistingVmssOnboarding**模板会启用用于 VM 的 Azure Monitor。
- **NewVmssOnboarding**模板创建虚拟机规模集，并使用于 VM 的 Azure Monitor 可以对其进行监视。
- **ConfigureWorkspace**模板通过启用 Linux 和 Windows 操作系统性能计数器的解决方案和集合来配置 Log Analytics 工作区以支持用于 VM 的 Azure Monitor。

>[!NOTE]
>如果虚拟机规模集已存在并且升级策略设置为**手动**，则在运行**ExistingVmssOnboarding** Azure 资源管理器模板之后，默认情况下不会为实例启用用于 VM 的 Azure Monitor。 必须手动升级实例。

### <a name="deploy-by-using-azure-powershell"></a>使用 Azure PowerShell 进行部署

以下步骤通过使用 Azure PowerShell 来启用监视。

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
配置更改可能需要几分钟才能完成。 完成后，将显示一条类似于以下内容的消息，其中包括结果：

```powershell
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>使用 Azure CLI 部署

以下步骤通过使用 Azure CLI 启用监视。

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

输出如下所示：

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>使用 PowerShell 启用

若要为多个 Vm 或虚拟机规模集启用用于 VM 的 Azure Monitor，请使用 PowerShell 脚本[Install-VMInsights](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)。 Azure PowerShell 库中提供了该功能。 此脚本循环访问：

- 订阅中的每个虚拟机和虚拟机规模集。
- *ResourceGroup*指定的作用域内资源组。
- 按*名称*指定的单个 VM 或虚拟机规模集。

对于每个 VM 或虚拟机规模集，该脚本将验证是否已安装 VM 扩展。 如果安装了 VM 扩展，该脚本会尝试重新安装它。 如果未安装 VM 扩展，该脚本将安装 Log Analytics 和依赖关系代理 VM 扩展。

验证正在使用的 Azure PowerShell 模块 Az 版本1.0.0 或更高版本是否启用了 `Enable-AzureRM` 兼容性别名。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

若要获取脚本的参数详细信息和示例用法的列表，请运行 `Get-Help`。

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

以下示例演示如何在文件夹中使用 PowerShell 命令来启用用于 VM 的 Azure Monitor，并了解预期的输出：

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>后续步骤

为虚拟机启用监视后，可以使用用于 VM 的 Azure Monitor 分析此信息。

- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。

- 若要确定虚拟机的性能瓶颈和总体利用率，请参阅[查看 AZURE Vm 性能](vminsights-performance.md)。
