---
title: 多类决策林：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的多类决策林模块来基于*决策林*算法创建机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 47cea412489693cedb05bd8a94a914b1757b8058
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152153"
---
# <a name="multiclass-decision-forest-module"></a>多类决策林模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块可以基于*决策林*算法创建机器学习模型。 决策林是一种系综的模型，可快速生成一系列决策树，同时从标记的数据进行学习。

## <a name="more-about-decision-forests"></a>有关决策林的更多信息

决策林算法是一种用于分类的系综学习方法。 该算法的工作原理是构建多个决策树，然后对最常见的输出类进行*投票*。 投票是一种聚合形式，在这种形式中，分类决策林中的每个树都输出标签的非规范化频率直方图。 聚合过程对这些直方图求和并规范化结果，以获取每个标签的 "概率"。 预测置信度较高的树在最终决定系综时具有更大的权重。

通常，决策树是非参数模型，这意味着它们支持具有不同分布的数据。 在每个树中，将对每个类运行一系列简单测试，并提高树的级别，直到到达叶节点（决策）。

决策树具有很多优势：

+ 可以表示非线性决策边界。
+ 在训练和预测期间，可以提高计算资源和内存的使用效率。
+ 执行集成的特征选择和分类。
+ 在出现干扰特征时保持弹性。

Azure 机器学习中的决策林分类器由决策树的系综组成。 一般而言，与单一决策树相比，系综模型提供更好的覆盖范围和准确性。 有关详细信息，请参阅[决策树](https://go.microsoft.com/fwlink/?LinkId=403677)。

## <a name="how-to-configure-multiclass-decision-forest"></a>如何配置多类决策林

1. 在设计器中将**多类决策林**模块添加到管道。 可以在 "**机器学习**"、"**初始化模型**" 和 "**分类**" 下找到此模块。

2. 双击该模块以打开 "**属性**" 窗格。

3. 对于 "重新**采样" 方法**，请选择用于创建单个树的方法。  可以从装袋或复制中选择。

    + **装袋**：装袋也称为*启动聚合*。 在此方法中，每个树都在新示例上增长，这是通过在数据集的大小为原始数据集的情况下随机采样原始数据集而创建的。 模型的输出通过*投票*合并，这是一种聚合形式。 有关详细信息，请参阅用于启动聚合的维基百科条目。

    + **复制**：在复制中，每个树都在完全相同的输入数据上定型。 确定对每个树节点使用哪个拆分谓词会保持不变，从而创建不同的树。

   

4. 通过设置 "**创建训练人员模式**" 选项，指定要如何定型模型。

    + **单个参数**：如果你知道想要如何配置模型，并提供一组值作为参数，请选择此选项。


5. **决策树的数量**：键入可在系综中创建的决策树的最大数目。 通过创建更多决策树，你可能获得更好的覆盖范围，但训练时间可能会增加。

    可视化定型模型时，此值还控制结果中显示的树的数目。 若要查看或打印单个树，可将值设置为 1;但是，这意味着只能生成一个树（带有初始参数集的树），而不执行进一步的迭代。

6. **决策树的最大深度**：键入一个数字，以限制任何决策树的最大深度。 增加树的深度可以提高精确性，但你可能会面临一些过度拟合和训练时间增加的风险。

7. **每个节点的随机拆分数**：在生成树的每个节点时，键入要使用的拆分数。 *拆分*意味着将随机划分树的每个级别（节点）中的功能。

8. **每个叶节点的最小样本数**：指示在树中创建任何终端节点（叶）所需的最小事例数。 通过增加此值，你可以增加用于创建新规则的阈值。

    例如，默认值为 1 时，甚至单个案例也会导致创建新规则。 如果您将此值增大至 5，则定型数据中至少必须包含 5 个符合相同条件的用例。



10. 连接带标签的数据集和训练模块之一：

    + 如果将 "**创建**训练人员模式" 设置为 "**单个参数**"，请使用 "[训练模型](./train-model.md)" 模块。

11. 运行管道。



## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 