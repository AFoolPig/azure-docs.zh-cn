---
title: 分区和示例：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的分区和示例模块来对数据集执行采样或从数据集创建分区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 772c16dc292d8bce4b927c9c2ce3ff6ee0ed399d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152119"
---
# <a name="partition-and-sample-module"></a>分区和示例模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块可对数据集执行采样或从数据集创建分区。

采样是机器学习中一个重要的工具，因为它可以在保持相同比率的值的同时降低数据集的大小。 此模块支持在机器学习中非常重要的几个相关任务： 

- 将你的数据划分为多个大小相同的子节。 

    可以将分区用于交叉验证，或将事例分配给随机组。

- 将数据分为组，然后使用特定组中的数据。 

    将事例随机分配给不同组后，你可能需要修改只与一个组相关联的功能。

- 样本. 

    您可以提取一定百分比的数据，应用随机抽样，或选择要用于平衡数据集的列并对其值执行分层采样。

- 创建用于测试的较小的数据集。 

    如果有大量数据，则在设置管道时最好只使用前*n*行，然后在生成模型时切换到使用完整数据集。 你还可以使用采样来创建更小的数据集，以便在开发中使用。

## <a name="configure-partition-and-sample"></a>配置分区和示例

此模块支持多种方法，将数据划分为多个分区或进行采样。 首先选择方法，然后设置方法所需的其他选项。

- Head
- 采样
- 分配给折叠
- 选择折叠

### <a name="get-top-n-rows-from-a-dataset"></a>获取数据集中的前 N 行

使用此模式可仅获取前 *n* 行。 如果要对少量行测试管道，而不需要以任何方式平衡或采样数据，则此选项很有用。

1. 将**分区和示例**模块添加到接口中的管道，并连接数据集。  

2. **分区或采样模式**：将此选项设置为**Head**。

3. **要选择的行数**：键入要返回的行数。

    指定的行数必须为非负整数。 如果所选行的数目大于数据集中的行数，则返回整个数据集。

4. 运行管道。

该模块输出只包含指定行数的单个数据集。 行始终从数据集的顶部读取。

### <a name="create-a-sample-of-data"></a>创建数据示例

此选项支持简单随机采样或分层随机采样。 如果要创建较小的代表示例数据集进行测试，这会很有用。

1. 向管道添加**分区和示例**模块，并连接数据集。

2. **分区或采样模式**：将此设置为 "**采样**"。

3. **采样速率**：键入一个介于0和1之间的值。 此值指定源数据集中应包括在输出数据集中的行的百分比。

    例如，如果只需要原始数据集的一半，则键入 "`0.5`"，指示采样率应为50%。

    输入数据集的行无序，并根据指定的比率选择性地放入输出数据集。

4. **采样的随机种子**：（可选）键入要用作种子值的整数。

    如果希望每次都按相同的方式划分行，则此选项非常重要。 默认值为0，表示根据系统时钟生成起始种子。 每次运行管道时，这可能会导致结果略有不同。

5. **采样的分层 split**：如果数据集中的行应在采样之前除以某些键列，请选择此选项。

    对于**采样的分层键列**，请选择要在对数据集进行划分时使用的单个*阶层列*。 然后，将数据集中的行划分如下：

    1. 所有输入行都按指定的阶层列中的值分组（分层）。

    2. 每个组中的行随机排布。

    3. 有选择性地将每个组添加到输出数据集以满足指定的比率。


6. 运行管道。

    使用此选项时，模块输出单个数据集，其中包含数据的代表性采样。 数据集的其余 unsampled 部分不是输出。 

## <a name="split-data-into-partitions"></a>将数据拆分为分区

如果要将数据集划分为数据的子集，请使用此选项。 当您想要创建自定义数量的用于交叉验证的折叠或将行拆分为多个组时，此选项也很有用。

1. 向管道添加**分区和示例**模块，并连接数据集。

2. 对于 "**分区" 或 "采样模式**"，选择 "**分配到折叠**"。

3. **在分区中使用替换**：如果想要将抽样行放回到行池中以供可能重用，请选择此选项。 因此，同一行可能会分配给几个折叠。

    如果不使用 "替换" （默认选项），则采样行不会被放回到行池中以供可能重用。 因此，每行只能分配给一个折叠。

4. **随机拆分**：如果想要将行随机分配到折叠，请选择此选项。

    如果不选择此选项，则使用轮循机制方法将行分配到折叠。

5. **随机种子**：（可选）键入要用作种子值的整数。 如果希望每次都按相同的方式划分行，则此选项非常重要。 否则，默认值 0 表示使用随机起始种子。

6. **指定分区程序方法**：使用以下选项指示如何将数据分配到每个分区：

    - **均匀分区**：使用此选项可在每个分区中放置相同数量的行。 若要指定输出分区数，请在 "**指定要均衡拆分成的折叠数**" 文本框中输入一个整数。

    - **具有自定义比例的分区**：使用此选项可将每个分区的大小指定为以逗号分隔的列表。

        例如，如果想要创建三个分区，其中第一个分区包含50% 的数据，其余两个分区包含25% 的数据，则单击 "以**逗号分隔的比例列表**" 文本框，然后键入以下数字： `.5, .25, .25`

        所有分区大小的总和必须加上1。

        - 如果输入的数字加上**小于 1**，则会创建一个额外的分区来保存剩余的行。 例如，如果键入值 .2 和 .3，则会创建第三个分区，其中包含所有行的剩余50%。

        - 如果输入的数字最多添加**1 个**，则在运行管道时将引发错误。

7. **分层 split**：如果想要在拆分时分层行，请选择此选项，然后选择_阶层列_。

8. 运行管道。

    使用此选项时，模块会输出多个数据集，并使用您指定的规则进行分区。

### <a name="use-data-from-a-predefined-partition"></a>使用预定义分区中的数据  

在将数据集划分为多个分区，并且现在想要依次加载每个分区以便进一步分析或处理时，可以使用此选项。

1. 向管道添加**分区和示例**模块。

2. 将其连接到**分区和示例**的上一个实例的输出。 该实例必须使用 "**分配给折叠**" 选项来生成一定数量的分区。

3. **分区或采样模式**：选择 "选择**折叠**"。

4. **指定要从中进行抽样的折叠**：通过键入要使用的分区的索引来选择要使用的分区。 分区索引从1开始。 例如，如果将数据集划分为三个部分，则分区的索引为1、2和3。

    如果键入无效的索引值，则会引发设计时错误： "错误0018： Dataset 包含无效数据。"

    除了通过折叠将数据集分组以外，还可以将数据集分成两个组：目标折叠和其他所有内容。 为此，请键入单个折页的索引，然后选择该选项，**选取所选折叠的补**数，以获取除指定折叠之外的所有数据。

5. 如果使用多个分区，则必须添加**分区和示例**模块的其他实例来处理每个分区。

    例如，假设之前已使用 age 将患者分区为四个折叠。 若要处理每个折叠，需要**分区和示例**模块的四个副本，并在每个副本中选择不同的折叠，如下所示。 使用 "**分配到折叠**" 输出是不正确的。  

    [![分区和示例](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. 运行管道。

    使用此选项时，该模块将输出只包含分配给该折叠的行的单个数据集。

> [!NOTE]
>  您无法直接查看折叠标识;它们仅出现在元数据中。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 