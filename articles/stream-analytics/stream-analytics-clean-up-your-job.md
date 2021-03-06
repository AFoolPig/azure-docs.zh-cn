---
title: 清理 Azure 流分析作业
description: 本文演示用于删除 Azure 流分析作业的不同方法。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426490"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>停止或删除 Azure 流分析作业

可以通过 Azure 门户、Azure PowerShell、Azure SDK for .Net 或 REST API 轻松停止或删除 azure 流分析作业。 删除流分析作业后，无法将其恢复。

>[!NOTE] 
>当停止流分析作业时，数据只会保留在输入和输出存储中，例如事件中心或 Azure SQL 数据库。 如果需要从 Azure 中删除数据，请务必遵循流分析作业的输入和输出资源删除过程进行操作。

## <a name="stop-a-job-in-azure-portal"></a>停止 Azure 门户中的作业

停止作业时，资源是取消预配的，并且停止处理事件。 与此作业相关的费用也将停止。 但是，你的所有配置都将保留，你可以在以后重新启动作业 

1. 登录 [Azure 门户](https://portal.azure.com)。 

2. 找到正在运行的流分析作业并选择它。

3. 在流分析作业页上，选择“停止”以停止作业。 

   ![停止 Azure 流分析作业](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>删除 Azure 门户中的作业

>[!WARNING] 
>删除流分析作业后，无法将其恢复。

1. 登录到 Azure 门户。 

2. 找到现有流分析作业并选择它。

3. 在流分析作业页上，选择“删除”以删除作业。 

   ![删除 Azure 流分析作业](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>使用 PowerShell 停止或删除作业

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要使用 PowerShell 停止作业，请使用[AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) cmdlet。 若要使用 PowerShell 删除作业，请使用[AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) cmdlet。

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>使用 Azure SDK for .NET 停止或删除作业

若要使用 Azure SDK for .NET 停止作业，请使用 [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) 方法。 若要使用 Azure SDK for .NET 删除作业，请使用 [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) 方法。

## <a name="stop-or-delete-a-job-using-rest-api"></a>使用 REST API 停止或删除作业

若要使用 REST API 停止作业，请参阅[停止](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop)方法。 若要使用 REST API 删除作业，请参阅[删除](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete)方法。
