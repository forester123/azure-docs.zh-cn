---
title: "教程：Azure Active Directory 与 ADP eTime 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 ADP eTime 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a3e9f0be-19ed-4b99-a180-619e7624fc26
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 56e3a4ee3cc52fc2b18e78a42a65af33a61ff349


---
# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>教程：Azure Active Directory 与 ADP eTime 集成
本教程旨在展示如何将 ADP eTime 与 Azure Active Directory (Azure AD) 集成。  
将 ADP eTime 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 ADP eTime
* 可使用户通过其 Azure AD 帐户自动登录 ADP eTime（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 ADP eTime 的集成，需要以下项目：

* 一个 Azure AD 订阅
* 启用的订阅上的 ADP eTime 单一登录

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  
本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 ADP eTime
2. 配置和测试 Azure AD 单一登录

## <a name="adding-adp-etime-from-the-gallery"></a>从库中添加 ADP eTime
若要配置 ADP eTime 与 Azure AD 的集成，需要将库中的 ADP eTime 添加到托管的 SaaS 应用列表。

**若要从库中添加 ADP eTime，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“ADP eTime”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)
7. 在“结果”窗格中，选择“ADP eTime”，然后单击“完成”，添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分旨在展示如何根据名为“Britta Simon”的测试用户的指令配置和测试 ADP eTime 的 Azure AD 单一登录。

对于单一登录到工作帐户，Azure AD 需要知道 Azure AD 用户在 ADP eTime 中的对应用户是谁。 换句话说，需要建立 Azure AD 用户与 ADP eTime 中相关用户之间的链接关系。  
通过将 Azure AD 中的 **user name** 值分配为 ADP eTime 中的 **Username** 值，建立此链接关系。

若要通过 ADP eTime 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 ADP eTime 测试用户](#creating-a-adpetime-test-user)** - 在 ADP eTime 中创建 Britta Simon 的对立用户，并且将其链接到她在 Azure AD 中的代表。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分要在 Azure 经典门户中启用 Azure AD 单一登录，并且在 ADP eTime 应用程序中配置单一登录。

ADP eTime 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示一个示例。 声明名称将始终是**“PersonImmutableID”**和映射到包含用户 EmployeeID 的 ExtensionAttribute2 的值。 将用户从 Azure AD 映射到 ADP eTime 在此是在 EmployeeID 上完成，但可将其映射到同样基于应用程序设置的其他值。 所以，首先请与 ADP eTime 团队合作，使用用户的正确标识符，并将该值与**“PersonImmutableID”**声明一起映射。  

![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png) 

在配置 SAML 断言前，需要联系 ADP eTime 支持团队，并为租户请求唯一标识符属性值。 拥有此值才能为应用程序配置自定义声明。

**若要通过 ADP eTime 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“ADP eTime”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 ADP eTime”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“回复 URL”文本框中，使用以下模式键入用户用于登录 ADP eTime 应用程序的 URL：`https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer`。

    b. 单击“资源组名称” 的 Azure 数据工厂。

1. 在“配置 ADP eTime 的单一登录”页面上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载元数据”，然后在计算机上保存该文件。
   
    b. 单击“资源组名称” 的 Azure 数据工厂。
2. 若要为应用程序配置 SSO，请联系 ADP eTime 支持团队，并将下载的元数据文件附加到电子邮件中，以便可以针对 SSO 集成配置它们。
   
   > [!NOTE]
   > 在 **ADP eTime** 团队配置此实例以后，可从该该团队获取 **RelayState** 值。按照下述步骤对其进行配置。 这样配置后，可以测试集成。 因此，请注意，若要使此应用程序集成正常运行，这是一项重要配置。
   > 
   > 
3. 若要在 Azure AD 中配置 RelayState 值，请执行以下步骤： 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 以管理员身份登录 [Azure 管理门户](https://portal.azure.com)。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在左侧导航窗格中，单击“更多服务”。 
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_07.png)
   
    c. 在“搜索”文本框中，键入 **Azure Active Directory**，然后单击相关链接。
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_08.png)
   
    d.单击“下一步”。 单击“企业应用程序”。
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_09.png)
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在“管理”部分单击“所有应用程序”。
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_10.png)
   
    f. 在“搜索”文本框中，键入 **ADP eTime**，然后单击相关链接。 
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_11.png)
   
    g. 在“管理”部分单击“单一登录”。
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_12.png)
   
    h. 选择“显示高级 URL 设置”。
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_13.png)
   
    i. 在“回复状态”文本框中，使用以下格式键入值：
   
   * 生产环境：`https://fed.adp.com/saml/fedlanding.html?<id>` 
   * 过渡环境：`https://fed-stag.adp.com/saml/fedlanding.html?PORTAL`
     
     ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_14.png)
     
     j. 保存设置。
4. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
5. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为“Britta Simon”的测试用户。  
在“用户列表”中，选择“Britta Simon”。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png) 
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-adp-etime-test-user"></a>创建 ADP eTime 测试用户
本部分要在 ADP eTime 中创建名为“Britta Simon”的用户。 请与 ADP eTime 支持团队合作，添加 ADP eTime 帐户中的用户。 

> [!NOTE]
> 如果需要手动创建用户，需要联系 ADP eTime 支持团队。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分旨在通过授予 Britta Simon 访问 ADP eTime 的权限，支持她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 ADP eTime，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在“应用程序列表”中，选择“ADP eTime”。
   
    ![配置单一登录](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。  
当在访问面板中单击“ADP eTime”磁贴时，应该会自动登录“ADP eTime”应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->

