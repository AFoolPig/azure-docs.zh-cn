---
title: 删除重复行：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "删除重复行" 模块从数据集中删除可能的重复行。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 429ddd62cccb8657aa18ec844968cc12df778f55
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153785"
---
# <a name="remove-duplicate-rows-module"></a>删除重复行模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块可从数据集中删除可能的重复项。

例如，假设您的数据如下所示，表示患者的多条记录。 

| PatientID | Initials| 性别|年龄|主办|
|----|----|----|----|----|
|1|F.M.| M| 53| 一月|
|2| F.A.M.| M| 53| 一月|
|3| F.A.M.| M| 24| 一月|
|3| F.M.| M| 24| 二月|
|4| F.M.| M| 23| 二月|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

很明显，此示例包含多个包含可能重复数据的列。 它们是否确实是重复项取决于你对数据的了解。 

+ 例如，您可能知道许多患者具有相同的名称。 不能使用任何名称列（仅限**ID**列）消除重复项。 这样，无论患者是否具有相同的名称，都只筛选出具有重复 ID 值的行。

+ 或者，你可以决定在 ID 字段中允许重复，并使用其他文件组合来查找唯一记录，如名字、姓氏、年龄和性别。  

若要设置行是否重复的条件，请指定要用作**键**的一列或一组列。 仅当**所有**键列中的值相等时，才会将两行视为重复。 如果任何行的**键**缺少值，则不会将它们视为重复的行。 例如，如果在上表中将 "性别" 和 "年龄" 设置为 "键"，则第6行和第7行不是重复行，因为其缺少 Age 值。

运行该模块时，它将创建一个候选数据集，并返回一组在指定列集中没有重复项的行。

> [!IMPORTANT]
> 源数据集不会更改;此模块创建一个新的数据集，该数据集根据你指定的条件进行筛选以排除重复项。

## <a name="how-to-use-remove-duplicate-rows"></a>如何使用删除重复行

1. 将模块添加到管道。 可以在 "**数据转换**"、"**操作**" 下找到 "**删除重复行**" 模块。  

2. 连接要检查是否有重复行的数据集。

3. 在 "**属性**" 窗格中的 "**键列选择筛选器表达式**" 下，单击 "**启动列选择器**" 以选择用于标识重复项的列。

    在此上下文中，**键**不表示唯一标识符。 使用列选择器选择的所有列都被指定为**键列**。 所有未选定的列都被视为非键列。 选择作为键的列的组合决定了记录的唯一性。 （可将其视为使用多个 equalities 联接的 SQL 语句。）

    示例：

    + "我想要确保 Id 是唯一的"：仅选择 ID 列。
    + "我想要确保名字、姓氏和 ID 的组合是唯一的"：选择所有三列。

4. 使用 "**保留第一个重复行**" 复选框指示在找到重复项时要返回的行：

    + 如果选择此选项，则返回第一行，其他行将被丢弃。 
    + 如果取消选中此选项，则最后一个重复行将保留在结果中，而其他行将被丢弃。 

5. 运行管道。

6. 若要查看结果，请右键单击该模块，然后选择 "**可视化**"。 

> [!TIP]
> 如果结果难以理解，或者您希望排除某些列，则可以通过使用 "[选择数据集中的列](./select-columns-in-dataset.md)" 模块删除列。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 