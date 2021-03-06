---
title: 模板概述
description: 介绍使用 Azure 资源管理器部署资源的模板的好处。
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: a4b0dff4b351098095de0b98ede21d9af8a7eef9
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689705"
---
# <a name="azure-resource-manager-templates-overview"></a>Azure 资源管理器模板概述

随着迁移到云，许多团队都采用了 agile 开发方法。 这些团队很快就会循环。 他们需要将他们的解决方案反复部署到云，并且知道其基础结构处于可靠状态。 随着基础结构成为迭代过程的一部分，操作和开发之间的划分已消失。 团队需要通过一个统一的过程来管理基础结构和应用程序代码。

为了解决这些难题，你可以自动执行部署，并使用基础结构作为代码的做法。 在代码中，可以定义需要部署的基础结构。 基础结构代码将成为项目的一部分。 与应用程序代码一样，你可以将基础结构代码存储在源存储库中并将其版本。 你的团队中的任何人都可以运行代码并部署类似的环境。

若要实现 Azure 解决方案的基础结构代码，请使用 Azure 资源管理器模板。 该模板是一个 JavaScript 对象表示法（JSON）文件，用于定义项目的基础结构和配置。 该模板使用声明性语法，使你可以声明要部署的内容，而不需要编写一系列编程命令来进行创建。 在模板中，指定要部署的资源以及这些资源的属性。

## <a name="why-choose-resource-manager-templates"></a>为什么选择资源管理器模板？

如果要尝试决定使用资源管理器模板和其他某个基础结构作为代码服务，请考虑使用模板的以下优点：

* **声明性语法**：资源管理器模板允许以声明方式创建和部署整个 Azure 基础结构。 例如，你不仅可以部署虚拟机，还可以部署网络基础结构、存储系统和可能需要的任何其他资源。

* 可**重复的结果**：在整个开发生命周期内重复部署你的基础结构，并确保以一致的方式部署你的资源。 模板是幂等的，这意味着你可以多次部署同一模板并获取相同状态的相同资源类型。 你可以开发一个表示所需状态的模板，而不是开发多个单独的模板来表示更新。

* **业务流程**：不必担心排序操作的复杂性。 资源管理器协调相互依赖的资源的部署，以便按正确的顺序创建这些资源。 如果可能，资源管理器会以并行方式部署资源，以便部署比串行部署更快。 通过一个命令而不是通过多个命令命令来部署模板。

   ![模板部署比较](./media/overview/template-processing.png)

* **内置验证**：只有通过验证后才会部署模板。 资源管理器在开始部署之前检查模板，以确保部署成功。 你的部署不太可能在半完成状态中停止。

* **模块化文件**：可以将模板分解为更小的可重复使用的组件，并在部署时将它们链接在一起。 还可以在另一个模板内嵌套一个模板。

* **创建任何 azure 资源**：可以立即在模板中使用新的 azure 服务和功能。 一旦资源提供程序引入了新资源，就可以通过模板部署这些资源。 使用新服务之前，无需等待工具或模块进行更新。

* **跟踪的部署**：在 Azure 门户中，你可以查看部署历史记录并获取有关模板部署的信息。 您可以查看部署的模板、传入的参数值以及任何输出值。 其他基础结构，因为代码服务不是通过门户进行跟踪。

   ![部署历史记录](./media/overview/deployment-history.png)

* **策略即代码**： [Azure 策略](../../governance/policy/overview.md)是一种作为代码框架的策略，用于自动化管理。 如果你使用的是 Azure 策略，则在通过模板部署时，将在不符合的资源上执行策略更正。

* **部署蓝图**：你可以利用 Microsoft 提供的[蓝图](../../governance/blueprints/overview.md)来满足法规和合规性标准。 这些蓝图包括用于各种体系结构的预建模板。

* **CI/CD 集成**：可以将模板集成到持续集成和持续部署（CI/CD）工具中，这可以自动执行发布管道，实现快速可靠的应用程序和基础结构更新。 通过使用 Azure DevOps 和资源管理器模板任务，你可以使用 Azure Pipelines 持续生成和部署 Azure 资源管理器模板项目。 若要了解详细信息，请参阅[VS project with 管道](add-template-to-azure-pipelines.md)和[与 Azure Pipelines 持续集成](template-tutorial-use-azure-pipelines.md)。

* 可**导出代码**：可以通过导出资源组的当前状态或查看用于特定部署的模板，获取现有资源组的模板。 查看[导出的模板](export-template-portal.md)是了解模板语法的有用方法。

* **创作工具**：可以[Visual Studio Code](use-vs-code-to-create-template.md)和模板工具扩展创建模板。 你将获得 intellisense、语法突出显示、行内帮助以及许多其他语言功能。 除了 Visual Studio code 以外，还可以使用[Visual studio](create-visual-studio-deployment-project.md)。

## <a name="template-file"></a>模板文件

在模板中，可以编写扩展 JSON 功能的[模板表达式](template-expressions.md)。 这些表达式使用由资源管理器提供的[函数](template-functions.md)。

模板包含以下各节：

* [参数](template-parameters.md)-在部署过程中提供一些值，这些值允许将同一模板用于不同的环境。

* [变量](template-variables.md)-定义在模板中重复使用的值。 它们可从参数值构造。

* [用户定义的函数](template-user-defined-functions.md)-创建简化模板的自定义函数。

* [资源](template-syntax.md#resources)-指定要部署的资源。

* [输出](template-outputs.md)-从已部署的资源返回值。

## <a name="template-deployment-process"></a>模板部署进程

部署模板时，资源管理器会将模板转换为 REST API 操作。 例如，当资源管理器收到具有以下资源定义的模板：

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

它将定义转换为以下 REST API 操作，后者将发送到 Microsoft.Storage 资源提供程序：

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>模板设计

模板和资源组的定义方式完全取决于用户及其所需的解决方案管理方式。 例如，可以通过单个模板将三层应用程序部署到单个资源组。

![三层模板](./media/overview/3-tier-template.png)

但无需在单个模板中定义整个基础结构。 通常，合理的做法是将部署要求划分成一组有针对性的模板。 可以轻松地将这些模板重复用于不同的解决方案。 若要部署特定的解决方案，请创建链接所有所需模板的主模板。 下图显示了如何通过包含三个嵌套模板的父模板部署三层解决方案。

![嵌套层模板](./media/overview/nested-tiers-template.png)

要各层具有单独的生命周期，可将三个层部署到单独的资源组。 请注意，仍可将这些资源链接到其他资源组中的资源。

![层模板](./media/overview/tier-templates.png)

有关嵌套模板的信息，请参阅[将链接的模板用于 Azure 资源管理器](linked-templates.md)。

## <a name="next-steps"></a>后续步骤

* 有关指导你完成创建模板的过程的分步教程，请参阅[教程：创建和部署第一个 Azure 资源管理器模板](template-tutorial-create-first-template.md)。
* 有关模板文件中的属性的信息，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
* 若要了解有关导出模板的信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](quickstart-create-templates-use-the-portal.md)。
