---
title: 教程：为 Fuze 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Fuze 以及取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: a58402297380116f83214e52ae7f2796412755b9
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057850"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>教程：为 Fuze 配置自动用户预配

本教程的目的是演示要在 Fuze 和 Azure Active Directory （Azure AD）中执行的步骤，以配置 Azure AD 自动将用户和/或组预配到 Fuze 以及取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [Fuze 租户](https://www.fuze.com/)。
* Fuze 中具有管理员权限的用户帐户。

## <a name="assigning-users-to-fuze"></a>将用户分配到 Fuze

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中哪些用户和/或组需要访问 Fuze。 确定后，可按照此处的说明将这些用户和/或组分配到 Fuze：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-fuze"></a>将用户分配到 Fuze 的重要提示

* 建议将单个 Azure AD 用户分配到 Fuze 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 Fuze 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="setup-fuze-for-provisioning"></a>设置 Fuze 以进行预配

将 Fuze 配置为使用 Azure AD 进行自动用户预配之前，需要在 Fuze 上启用 SCIM 设置。 

1. 首先联系 Fuze 代表以获取以下必需信息：

    * 当前在公司中使用的 Fuze Product Sku 列表。
    * 公司位置的位置代码列表。
    * 公司的部门代码列表。

2. 你可以在 Fuze 合同和配置文档中找到这些 Sku 和代码，或者联系 Fuze 代表。

3. 收到要求后，Fuze 代表将为你提供启用集成所需的 Fuze authentication 令牌。 此值将在 Azure 门户的 Fuze 应用程序的 "预配" 选项卡的 "机密令牌" 字段中输入。

## <a name="add-fuze-from-the-gallery"></a>从库中添加 Fuze

在将 Fuze 配置为 Azure AD 的自动用户预配之前，需要从 Azure AD 应用程序库将 Fuze 添加到托管 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Fuze，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **Fuze**"，在结果面板中选择 " **Fuze** "，然后单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的 Fuze](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-fuze"></a>配置 Fuze 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 Fuze 中创建、更新和禁用用户和/或组。

> [!TIP]
> 你还可以选择按照[Fuze 单一登录教程](fuze-tutorial.md)中提供的说明为 Fuze 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>若要在 Azure AD 中配置 Fuze 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Fuze”。

    ![应用程序列表中的 Fuze 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下的 "**租户 URL**" 中输入 `https://api.fuze.com/scim/v2`。 输入先前从**机密令牌**中的 Fuze 代表那里检索到的**SCIM Authentication 令牌**值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 Fuze。 如果连接失败，请确保 Fuze 帐户具有管理员权限，然后重试。

    ![租户 URL 标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击 **“保存”** 。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 Fuze**"。

    ![Fuze 用户映射](media/fuze-provisioning-tutorial/image01.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Fuze 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 Fuze 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Fuze 用户映射](media/fuze-provisioning-tutorial/image00.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Fuze 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Fuze 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 Fuze 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Fuze 支持称为 "**权利**" 的自定义 SCIM 属性。 仅可创建和不更新这些属性。 

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户设置](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报表](../app-provisioning/check-status-user-account-provisioning.md)。