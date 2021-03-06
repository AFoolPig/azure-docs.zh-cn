---
title: 选择定价层或 SKU
titleSuffix: Azure Cognitive Search
description: 可以在这些 Sku 中预配 Azure 认知搜索：免费版、基本版和标准版，以及标准版在各种资源配置和容量级别中可用。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 35dbd064a09a96dae58e1b15a6d8889bda45ee0d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899850"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>选择 Azure 认知搜索的定价层

创建 Azure 认知搜索服务时，会在服务生存期固定的定价层（或 SKU）上[创建资源](search-create-service-portal.md)。 层包括免费、基本、标准和优化存储。 标准和存储优化可用于多个配置和容量。

大多数客户都从免费层开始，以便他们能够评估服务。 评估后，在开发和生产部署的更高级别中创建第二个服务是很常见的。

## <a name="feature-availability-by-tier"></a>按层提供的功能可用性

几乎每个功能都可在每个层上使用（包括免费），但如果你赋予了足够的容量，则消耗大量资源的功能或工作流可能会无法正常工作。 例如， [AI 扩充](cognitive-search-concept-intro.md)具有长时间运行的技能，在免费服务上超时，除非数据集很小。

下表描述了与层相关的功能约束。

| 功能 | 限制 |
|---------|-------------|
| [索引器](search-indexer-overview.md) | 索引器在 S3 HD 上不可用。 |
| [客户托管的加密密钥](search-security-manage-encryption-keys.md) | 在免费层上不可用。 |

## <a name="tiers-skus"></a>层（Sku）

可以通过以下方式区分层：

+ 可以创建的索引和索引器数量
+ 分区（物理存储）的大小和速度

您选择的层决定了计费费率。 Azure 门户中的以下屏幕截图显示可用层，而不是定价（可在门户中找到，也可以在[定价页](https://azure.microsoft.com/pricing/details/search/)上找到）。 "**免费**"、"**基本**" 和 "**标准**" 是最常用的层。

**免费**在群集上创建受限的搜索服务，与其他订阅者共享。 你可以完成小型项目，包括快速入门和教程，但无法缩放服务或运行大量工作负荷。 "**基本**" 和 "**标准**" 是最常用的可计费层，默认值为 "**标准**"。

![Azure 认知搜索的定价层](media/search-sku-tier/tiers.png "Azure 认知搜索的定价层")

某些层针对某些类型的工作进行了优化。 例如，**标准3高密度（S3 HD）** 是 S3 的*托管模式*，其中基础硬件针对大量的小型索引进行了优化，适用于多租户方案。 S3 HD 与 S3 具有相同的单位费用，但硬件经过优化，可快速读取大量较小的索引中的文件。

**存储优化**层提供更大的存储容量，其价格低于每 TB 标准级别。 主要的折衷是更高的查询延迟，应根据特定的应用程序要求对其进行验证。  若要了解有关此层性能注意事项的详细信息，请参阅[性能和优化注意事项](search-performance-optimization.md)。

在设置服务时，你可以在[定价页](https://azure.microsoft.com/pricing/details/search/)上的服务限制中找到有关各层的详细信息，请参阅 Azure 认知搜索文章中的[服务限制](search-limits-quotas-capacity.md)。

## <a name="billable-events"></a>计费事件

基于 Azure 认知搜索构建的解决方案可能会按以下方式产生成本：

+ 最低配置服务的基本成本（创建服务）
+ 增加时增加成本（添加副本或分区）
+ 带宽费用（出站数据传输） 
+ 认知搜索（为 AI 扩充附加认知服务、用于知识库的 Azure 存储）

### <a name="service-costs"></a>服务成本

与可以 "暂停" 以避免收费的虚拟机或其他资源不同，Azure 认知搜索服务在专用于专用的硬件上始终可用。 因此，创建服务是一种可计费事件，该事件在你创建服务时开始，在你删除服务时结束。 

最小费用是按计费费率的第一个搜索单位（1个副本 x 个分区）。 对于服务的生存期，此最小值是固定的，因为服务无法在此配置以外的任何时间运行。 除了最小值以外，还可以单独添加副本和分区。 通过副本和分区增加容量增加时，会根据以下公式增加帐单： [（副本 x 分区 x 速率）](#search-units)，根据所选的定价层，你需要支付的费用取决于所选的定价层。

估计搜索解决方案的成本时，请记住，定价和容量不是线性的。 （增加容量比计算成本翻倍。）有关公式工作方式的示例，请参阅[如何分配副本和分区](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)。

### <a name="bandwidth-charges"></a>带宽费用

使用[Azure 认知搜索索引器](search-indexer-overview.md)可能会影响计费，具体取决于服务的位置。 如果在数据所在的同一区域中创建 Azure 认知搜索服务，则可以完全消除数据传出费用。 下面是[带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中的一些信息：

+ Microsoft 不会向 Azure 上的任何服务或 Azure 认知搜索的任何出站数据收费。
+ 在 multiservice 解决方案中，如果所有服务都位于同一区域，则不会对跨网络数据进行收费。

如果服务在不同区域中，则会对出站数据收费。 这些费用实际上并不是 Azure 认知搜索帐单的一部分。 之所以提到，是因为如果你使用数据或 AI 的索引器从不同的区域中提取数据，你将看到总体帐单中反映出的成本。

### <a name="ai-enrichment-with-cognitive-services"></a>AI 扩充与认知服务

对于[AI 扩充](cognitive-search-concept-intro.md)，应计划将可[计费的 azure 认知服务资源附加](cognitive-search-attach-cognitive-services.md)到位于 Azure 认知搜索的同一区域中，以实现即用即付处理。 附加认知服务没有相关的固定成本。 只需为所需的处理付费。

| 操作 | 计费影响 |
|-----------|----------------|
| 文档破解，文本提取 | 免费 |
| 文档破解，图像提取 | 根据从文档中提取的图像数量进行计费。 在[索引器配置](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)中， **imageAction**是触发图像提取的参数。 如果将**imageAction**设置为 "none" （默认值），则不会为图像提取付费。 Azure 认知搜索的[定价详细信息](https://azure.microsoft.com/pricing/details/search/)页上介绍了图像提取速率。|
| [内置认知技能](cognitive-search-predefined-skills.md) | 按照与你使用认知服务直接执行任务相同的费率进行计费。 |
| 自定义技能 | 自定义技能是您提供的功能。 使用自定义技术的成本完全取决于自定义代码是否调用其他计量服务。 |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>计费公式（R x P = SU）

了解 Azure 认知搜索操作的最重要的计费概念是*搜索单位*（SU）。 由于 Azure 认知搜索依赖于用于索引和查询的副本和分区，因此只需对其进行计费即可。 相反，应基于两者的组合来计费。

SU 是服务使用的*副本*和*分区*的产品： **（R x P = SU）** 。

每个服务至少从 1 个 SU（1 个分区乘以 1 个副本）开始。 任何服务的最大值为36。 例如，可以通过多种方式访问此最大值：6个分区 x 6 副本或3个分区 x 12 个副本。 通常，使用的容量小于总容量（例如，3个副本，3个分区的服务，按9个 SUs 计费）。 有关有效的组合，请参阅[分区和副本组合](search-capacity-planning.md#chart)图表。

每个 SU 的计费费率为每小时。 每个层都有递增的比率。 更高的层附带了更大的 speedier 分区，这会为该层提供总体每小时费率。 可以在 "[定价详细信息](https://azure.microsoft.com/pricing/details/search/)" 页上查看每个层的费率。

大多数客户只是联机使用一部分总容量，将剩余的容器保持预留状态。 对于帐单，你联机的分区和副本的数量（由 SU 公式计算）决定了每小时支付的费用。

## <a name="how-to-manage-costs"></a>如何管理成本

以下建议可帮助你至少保持成本：

- 在同一区域中创建所有资源，或在尽可能少的区域中创建所有资源，以最大程度地减少或消除带宽费用。

- 将所有服务合并为一个资源组，如 Azure 认知搜索、认知服务以及解决方案中使用的任何其他 Azure 服务。 在 Azure 门户中，找到资源组，并使用**成本管理**命令了解实际和预计支出。

- 考虑将 Azure Web 应用用于前端应用程序，以便请求和响应保留在数据中心边界内。

- 增加资源密集型操作（如索引），然后对常规查询工作负荷进行调整。 首先使用 Azure 认知搜索的最低配置（一个 SU 由一个分区和一个副本组成），然后监视用户活动以确定使用模式，这种模式会指示需要更多的容量。 如果有可预测的模式，则可以将缩放与活动同步（需要编写代码来自动执行此操作）。

此外，请访问与支出相关的内置工具和功能的[计费和成本管理](https://docs.microsoft.com/azure/billing/billing-getting-started)。

不可能临时关闭搜索服务。 专用资源始终是可操作的，在服务的生存期内可供独占使用。 删除服务是永久性的，也会删除其关联的数据。

就服务本身而言，降低帐单的唯一方法是将副本和分区减少到仍能提供可接受的性能和[SLA 符合性](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的级别，或在较低层创建服务（S1 小时费率低于 S2 或 S3 速率）。 假设你在负载预测的低端预配服务，则可以创建另一个更大层的服务，在第二个服务上重新生成索引，然后删除第一个服务。

## <a name="how-to-evaluate-capacity-requirements"></a>如何评估容量需求

在 Azure 认知搜索中，容量被构建为*副本*和*分区*。

+ 副本是搜索服务的实例。 每个副本承载一个索引的负载均衡副本。 例如，具有六个副本的服务在服务中加载每个索引有六个副本。

+ 分区存储索引和自动拆分可搜索的数据。 两个分区将索引拆分为一半，三个分区拆分为三分之二，依此类推。 在容量方面，*分区大小*是各层之间的主要区别性功能。

> [!NOTE]
> 所有标准和存储优化层都支持[灵活的副本和分区组合](search-capacity-planning.md#chart)，因此，你可以通过更改平衡来[优化系统的速度或存储](search-performance-optimization.md)。 基本层最多提供三个副本以实现高可用性，但只有一个分区。 免费层不提供专用资源：计算资源由多个订阅者共享。


### <a name="evaluating-capacity"></a>计算容量

容量和运行服务的成本。 层对两个级别施加限制：存储和资源。 你应考虑这两个方面，因为首先要达到的限制是有效限制。

业务要求通常规定了所需的索引数目。 例如，你可能需要大容量的文档存储库的全局索引。 或者，您可能需要基于区域、应用程序或业务小用户的多个索引。

若要确定索引大小，必须[生成一个索引](search-create-index-portal.md)。 其大小取决于导入的数据和索引配置，例如是否启用建议器、筛选和排序。 有关配置对大小的影响的详细信息，请参阅[创建基本索引](search-what-is-an-index.md)。

对于全文搜索，主数据结构是一个[反转索引](https://en.wikipedia.org/wiki/Inverted_index)结构，其特征不同于源数据。 对于反向索引，大小和复杂性由内容决定，而不一定是您向其中送进的数据量。 具有高冗余的大型数据源可能会导致索引比包含高度可变内容的小型数据集更小。 因此，很少可以基于原始数据集的大小推断索引大小。

> [!NOTE] 
> 即使估计索引和存储的未来需求，也可能会喜欢进行猜测，不过这一点很有价值。 如果层容量过低，则需要在更高的层预配新服务，并[重新加载索引](search-howto-reindex.md)。 不会将服务从一个 SKU 就地升级到另一个 SKU。
>

### <a name="estimate-with-the-free-tier"></a>免费层评估

估计容量的一种方法是从免费层开始。 请记住，免费服务提供最多3个索引、50 MB 的存储空间以及2分钟的索引时间。 使用这些约束来估算预计的索引大小可能会很困难，但这些步骤如下：

+ [创建免费服务](search-create-service-portal.md)。
+ 准备小型的、代表性的数据集。
+ [在门户中生成初始索引](search-create-index-portal.md)，并记下其大小。 功能和特性会影响存储。 例如，添加建议器（typeahead）将增加存储需求。 使用相同的数据集，您可以尝试创建索引的多个版本，每个字段都有不同的属性，以了解存储要求的变化情况。 有关详细信息，请参阅[创建基本索引中的 "存储影响"](search-what-is-an-index.md#index-size)。

使用粗略估计时，可以将两个索引（开发和生产）的预算翻倍，然后相应地选择层。

### <a name="estimate-with-a-billable-tier"></a>使用可计费层估算

专用资源可以容纳更大的采样和处理时间，以在开发过程中更逼真地估计索引数量、大小和查询量。 有些客户会直接进入可计费的层，然后在开发项目成熟时重新评估。

1. [查看每个层的服务限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits)，以确定更低的层是否可以支持所需的索引数。 在基本、S1 和 S2 层中，索引限制分别为15、50和200。 存储优化层的索引限制为10个，因为它设计为支持较低数量的超大型索引。

1. [在可计费层中创建服务](search-create-service-portal.md)：

    + 如果你不确定预计的负载，请从 Basic 或 S1 开始。
    + 如果你知道要进行大规模索引和查询加载，请从 S2 甚至 S3 开始。
    + 如果要对大量数据进行索引，并使用内部业务应用程序进行索引，则可以在 L1 或 L2 开始使用优化的存储。

1. [生成初始索引](search-create-index-portal.md)以确定将源数据转换为索引的方式。 这是估计索引大小的唯一方法。

1. 在门户中[监视存储、服务限制、查询量和延迟](search-monitor-usage.md)。 门户会显示每秒查询数、限制查询数和搜索延迟。 所有这些值都可以帮助您决定是否选择了正确的层。 

索引号和大小对于分析同样重要。 这是因为最大限制是通过完全利用存储（分区）或通过资源的最大限制（索引、索引器等）达到的，以先达到的限制为准。 门户可帮助你跟踪两者，并在“概述”页面上并排显示当前使用情况和最大限制。

> [!NOTE]
> 如果文档包含无关数据，则可以扩充存储要求。 理想情况下，文档只包含搜索体验所需的数据。 二进制数据不可搜索，应单独存储（可能位于 Azure 表或 blob 存储中）。 然后，应在索引中添加一个字段，以便保存对外部数据的 URL 引用。 单个文档的最大大小为 16 MB （如果是在一个请求中大容量上传多个文档，则较少）。 有关详细信息，请参阅[Azure 认知搜索中的服务限制](search-limits-quotas-capacity.md)。
>

**查询量注意事项**

每秒查询数（QPS）在性能优化过程中是一项重要的指标，但如果你预计在一开始就需要很高的查询量，通常只需考虑一下。

标准层可以提供副本和分区的平衡。 可以通过添加用于负载平衡的副本或添加分区进行并行处理，从而提高查询周期。 然后，你可以在预配服务后优化性能。

如果需要从一开始就使用较高的查询量，则应考虑使用更高的标准层，并获得更强大的硬件的支持。 然后，你可以脱机拍摄分区和副本，甚至可以切换到较低层的服务。 有关如何计算查询吞吐量的详细信息，请参阅[Azure 认知搜索性能和优化](search-performance-optimization.md)。

存储优化层适用于大数据工作负荷，支持更高的可用索引存储，以满足查询延迟要求的重要性。 你仍应将其他副本用于负载平衡，并使用其他分区进行并行处理。 然后，你可以在预配服务后优化性能。

**服务级别协议**

免费层和预览功能不提供[服务级别协议（sla）](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。 对于所有可计费的层，SLA 将在用户为服务提供足够冗余时生效。 查询（读取） Sla 需要两个或多个副本。 需要有三个或更多的副本用于查询和索引（读写） Sla。 分区数不会影响 Sla。

## <a name="tips-for-tier-evaluation"></a>层级评估提示

+ 允许指标围绕查询构建，并收集有关使用模式的数据（工作时间内的查询，在非高峰时段进行索引）。 使用此数据通知服务预配决定。 尽管在每小时或每日节奏上都不可行，但可以动态调整分区和资源，以适应查询卷中的计划更改。 如果级别的保留时间足以保证采取措施，还可以适应计划外但持续更改。

+ 请记住，underprovisioning 的唯一缺点是，如果实际要求大于预测值，则可能必须将其关闭。 若要避免服务中断，请在更高的层中创建新的服务，并并行运行该服务，直到所有应用和请求都面向新的终结点。

## <a name="next-steps"></a>后续步骤

从免费层开始，并通过使用数据的子集来了解其特性来构建初始索引。 Azure 认知搜索中的数据结构是一个反转索引结构。 反转索引的大小和复杂性由内容决定。 请记住，高度冗余的内容往往会导致比高度不规则的内容更小的索引。 因此内容特征而不是数据集的大小确定索引存储要求。

在对索引大小进行了初步估计后，请在本文所述的某个层上[预配一项可计费服务](search-create-service-portal.md)： "基本"、"标准" 或 "存储优化"。 放宽对数据大小进行的任何人为限制，并[重建索引](search-howto-reindex.md)，使其包含要搜索的所有数据。

根据需要[分配分区和副本](search-capacity-planning.md)以获取所需性能和规模。

如果性能和容量完好，就大功告成了。 否则，请在与你的需求更接近的不同层级上重新创建搜索服务。

> [!NOTE]
> 如果有疑问，请发布到[StackOverflow](https://stackoverflow.com/questions/tagged/azure-search)或[联系 Azure 支持部门](https://azure.microsoft.com/support/options/)。
