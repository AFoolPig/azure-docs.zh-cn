---
title: 规范化数据：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的规范化数据模块通过*规范化*转换数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 8eb54e232478ae24e1efb49a8ad43dc827aa2b6a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150674"
---
# <a name="normalize-data-module"></a>规范化数据模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块通过*规范化*转换数据集。

规范化是一种通常适用于机器学习数据准备的一种方法。 规范化的目标是将数据集中数值列的值更改为使用通用比例，而不会在值范围或丢失信息的范围内发生扭曲差异。 对于正确为数据建模的某些算法，还需要规范化。

例如，假设输入数据集包含一列，其值范围从0到1，另一个列的值范围从10000到100000。 当您尝试在建模过程中将值作为特征进行组合时，数字的*规模*差别会导致问题。

*规范化*通过创建新值来维护源数据中的常规分布和比率，并在模型中使用的所有数值列之间保留值，从而避免这些问题。

此模块提供若干用于转换数值数据的选项：

- 您可以将所有值更改为0-1 刻度，或通过将值表示为百分比排名而不是绝对值来转换值。
- 你可以对单个列或者同一数据集中的多个列应用规范化。
- 如果需要重复管道，或对其他数据应用相同的规范化步骤，则可以将这些步骤保存为规范化转换，并将其应用于具有相同架构的其他数据集。

> [!WARNING]
> 有些算法要求在训练模型之前先对数据进行规范化。 有些算法可以执行自己的数据缩放或规范化。 因此，当您选择在构建预测模型时要使用的机器学习算法时，请确保在对定型数据应用规范化之前查看算法的数据要求。

##  <a name="configure-normalize-data"></a>配置规范化数据

使用此模块时，一次只能应用一个规范化方法。 因此，相同的规范化方法应用于您选择的所有列。 若要使用不同的规范化方法，请使用**规范化数据**的第二个实例。

1. 将**规范化数据**模块添加到管道。 您可以在 "**数据转换**" 下的 "**缩放和缩小**" 类别中的 Azure 机器学习中找到该模块。

2. 连接包含所有数字的至少一列的数据集。

3. 使用列选择器选择要规范化的数字列。 如果未选择单个列，则默认情况下将包含输入中的**所有**数值类型列，并且会将相同的规范化过程应用于所有选定的列。 

    如果包含不应规范化的数字列，这可能会导致奇怪的结果！ 请始终仔细检查列。

    如果未检测到任何数值列，请检查列元数据，以验证列的数据类型是否为支持的数值类型。

    > [!TIP]
    > 若要确保将特定类型的列作为输入提供，请在**规范化数据**之前尝试使用 "[选择数据集中的列](./select-columns-in-dataset.md)" 模块。

4. **选中时，对常量列使用 0**：如果任意数值列包含一个不变的值，请选择此选项。 这可确保在规范化操作中不使用此类列。

5. 从 "**转换方法**" 下拉列表中，选择要应用于所有选定列的单个数学函数。 
  
    - **Zscore**：将所有值都转换为 z 分数。
    
      列中的值使用以下公式进行转换：  
  
      ![使用 z&#45;评分的规范化](media/module/aml-normalization-z-score.png)
  
      平均方差和标准方差是分别针对每个列计算的。 使用总体标准方差。
  
    - **MinMax**：最小-最大规范化器线性重新缩放 [0，1] 间隔内的每个功能。
    
      重新调整为 [0,1] 的间隔通过以下方式完成：对每个特征的值进行转换，使最小值为 0，然后除以新的最大值（这是原始最大值和最小值的差）。
      
      列中的值使用以下公式进行转换：  
  
      ![使用最小&#45;值函数的规范化](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **逻辑**：使用以下公式转换列中的值：

      ![通过逻辑函数进行标准化的公式](media/module/aml-normalization-logistic.png "AML_normalization-逻辑")  
  
    - **对数**：此选项将所有值都转换为对数刻度。
  
      列中的值使用以下公式进行转换：
  
      ![公式日志&#45;正态分布](media/module/aml-normalization-lognormal.png "AML_normalization-对数")
    
      此处的μ和σ是分布的参数，作为每列的最大可能性估算，计算出的数据凭经验。  
  
    - **TanH**：所有值都转换为双曲正切值。
    
      列中的值使用以下公式进行转换：
    
      ![使用 tanh 函数的规范化](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. 运行管道，或双击**规范化数据**模块并选择 "**运行所选项**"。 

## <a name="results"></a>结果

**规范化数据**模块生成两个输出：

- 若要查看已转换的值，请右键单击该模块，然后选择 "**可视化**"。

    默认情况下，会就地转换值。 如果要将变换后的值与原始值进行比较，请使用 "[添加列](./add-columns.md)" 模块重新组合数据集，并并排查看这些列。

- 若要保存转换，以便可以将相同的规范化方法应用到另一个数据集，请选择该模块，然后在右侧面板中的 "**输出**" 选项卡下选择 "**注册数据集**"。

    然后，可以从左侧导航窗格的 "**转换**" 组加载保存的转换，并使用[/Apply 转换](apply-transformation.md)将其应用到具有相同架构的数据集。  


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 