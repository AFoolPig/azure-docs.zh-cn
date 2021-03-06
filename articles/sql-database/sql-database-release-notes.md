---
title: 发行说明
description: 了解 Azure SQL 数据库服务和 Azure SQL 数据库文档中的新功能和改进
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: bdea9af04008ef68578ff8c136760f2493a2ae35
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279219"
---
# <a name="sql-database-release-notes"></a>SQL 数据库发行说明

本文列出了当前以公共预览版提供的 SQL 数据库功能。 有关 SQL 数据库的更新和改进，请参阅[Sql 数据库服务更新](https://azure.microsoft.com/updates/?product=sql-database)。 有关对其他 Azure 服务的更新和改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="features-in-public-preview"></a>处于公共预览版的功能

### <a name="single-databasetabsingle-database"></a>[单个数据库](#tab/single-database)

| 功能 | 详细信息 |
| ---| --- |
| 新的 Fsv2 系列和 M 系列硬件代| 有关信息，请参阅[硬件代](sql-database-service-tiers-vcore.md#hardware-generations)。|
| [Azure 专用链接](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| 专用链接简化了网络架构，并通过将数据保留在 Azure 网络中来消除向 Internet 暴露数据的风险，从而保护 Azure 中终结点间的连接。 专用链接让你能够在 Azure 中创建和呈现自己的服务。 |
| 通过单一数据库和弹性池加速数据库恢复 | 有关信息，请参阅[加速数据库恢复](sql-database-accelerated-database-recovery.md)。|
|估计非重复计数|有关信息，请参阅[估计非重复计数](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)。|
|行存储上的批处理模式（在兼容性级别 150 下）|有关信息，请参阅[行存储上的批处理模式](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)。|
| 数据发现和分类  |有关信息，请参阅 [Azure SQL 数据库和 SQL 数据仓库数据发现和分类](sql-database-data-discovery-and-classification.md)。|
| 弹性数据库作业 | 有关信息，请参阅[创建、配置和管理弹性作业](elastic-jobs-overview.md)。 |
| 弹性查询 | 有关信息，请参阅[弹性查询概述](sql-database-elastic-query-overview.md)。 |
| 弹性事务 | [跨云数据库的分布式事务](sql-database-elastic-transactions-overview.md)。 |
|内存授予反馈（行模式）（在兼容性级别 150 下）|有关信息，请参阅[内存授予反馈（行模式）](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)。|
| Azure 门户中的查询编辑器 |有关信息，请参阅[使用 Azure 门户的 SQL 查询编辑器进行连接并查询数据](sql-database-connect-query-portal.md)。|
| 带有单一数据库和弹性池的 R services/机器学习 |有关信息，请参阅[AZURE SQL 数据库中的机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)。|
|SQL Analytics|有关信息，请参阅[Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)。|
|表变量延迟编译（在兼容性级别 150 下）|有关信息，请参阅[表变量延迟的编译](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)。|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[托管实例](#tab/managed-instance)

| 功能 | 详细信息 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">服务辅助子网配置</a> | 管理子网配置的一种安全且方便的方式。 |
| <a href="/azure/sql-database/sql-database-instance-pools">实例池</a> | 将较小的 SQL 实例迁移到云的方便且经济高效的方式。 |
| <a href="https://aka.ms/managed-instance-tde-byok">使用“创建自己的密钥”(BYOK) 进行透明数据加密 (TDE)</a> |有关信息，请参阅[Azure Key Vault 中的客户托管密钥的 AZURE SQL 透明数据加密：创建自己的密钥支持](transparent-data-encryption-byok-azure-sql.md)。|
| <a href="https://aka.ms/managed-instance-aadlogins">实例级 Azure AD 服务器主体（登录名）</a> | 使用 <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> 语句创建服务器级登录名。 |
| [事务复制](sql-database-managed-instance-transactional-replication.md) | 将表中的更改复制到托管实例、单一数据库或 SQL Server 实例上放置的其他数据库中，或者在其他托管实例或 SQL Server 实例中的某些行发生更改时更新表。 有关信息，请参阅[在 Azure SQL 数据库托管实例数据库中配置复制](replication-with-sql-database-managed-instance.md)。 |
| 威胁检测 |有关信息，请参阅[在 Azure SQL 数据库托管实例中配置威胁检测](sql-database-managed-instance-threat-detection.md)。|
| 使用托管实例重新创建已删除的数据库 |有关信息，请参阅[在 Azure SQL 托管实例中重新创建已删除的数据库](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)。|
| &nbsp; |

---

## <a name="new-features"></a>新增功能

### <a name="managed-instance-h2-2019-updates"></a>托管实例 H2 2019 更新

- 使用[自动故障转移组](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/)可以将主实例中的所有数据库复制到另一个区域中的辅助实例。
- 使用[全局跟踪标志](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)配置托管实例行为。

### <a name="managed-instance-h1-2019-updates"></a>托管实例 H1 2019 更新

2019 年上半年在托管实例部署模型中启用了以下功能：
  - 支持具有 <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio 订阅者的 Azure 每月额度</a>和增加的[区域限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)的订阅。
  - 支持 <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">SharePoint 2016 和 sharepoint 2019</a> 以及 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a>
  - 使用所选<a href="https://aka.ms/managed-instance-collation">服务器级排序规则</a>和<a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">时区</a>创建实例。
  - 托管实例现在使用<a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">内置防火墙</a>进行保护。
  - 配置实例以使用[公共终结点](sql-database-managed-instance-public-endpoint-configure.md)、[代理覆盖](sql-database-connectivity-architecture.md#connection-policy)连接以获得更好的网络性能，<a href="https://aka.ms/four-cores-sql-mi-update">Gen5 硬件代次上有 4 个 vCore</a> 或<a href="https://aka.ms/managed-instance-configurable-backup-retention">将备份保留期配置为最多 35 天</a>以便进行时间点还原。 长期备份保留（最长 10 年）仍未启用，因此可以使用<a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">仅复制备份</a>作为替代方法。
  - 利用新功能，可以<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">使用 PowerShell 将数据库异地还原到另一个数据中心</a>、[重命名数据库](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[删除虚拟群集](sql-database-managed-instance-delete-virtual-cluster.md)。
  - 新的内置[实例参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor)使职责分离 (SoD) 遵从安全原则并符合企业标准。
  - 托管实例在以下 Azure 政府版区域中提供（US Gov 德克萨斯州、US Gov 亚利桑那州）以及中国北部2和中国东部2。 它还在以下公共区域中提供：澳大利亚中部、澳大利亚中部2、巴西南部、法国南部、阿拉伯联合酋长国中部、阿拉伯联合酋长国北部、南非北部、南非西北部。

## <a name="fixed-known-issues"></a>修复了已知问题

- **2019 年 8 月** - 托管实例完全支持包含的数据库。
- **10 月 2019** -如果源数据库包含内存中 OLTP 对象，则在业务关键层到常规用途层的内置时间点数据库还原不会成功。
- **Oct 2019** -可以将**数据库邮件**功能与使用安全连接的外部（非 Azure）邮件服务器一起使用。
- **11 月 2019** -从 Azure Blob 存储还原数据库后，使用 `DBCC CHECKDB` 验证数据库一致性。

## <a name="updates"></a>更新

有关 SQL 数据库更新和改进的列表，请参阅[Sql 数据库服务更新](https://azure.microsoft.com/updates/?product=sql-database)。

有关所有 Azure 服务的更新和改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="contribute-to-content"></a>参与内容制作

若要参与 Azure SQL 数据库文档制作，请参阅[文档参与者指南](https://docs.microsoft.com/contribute/)。
