---
title: "Azure SQL 数据库安全概述 | Microsoft 文档"
description: "了解有关 Azure SQL 数据库和 SQL Server 安全性的信息，包括云与本地 SQL Server 在身份验证、授权、连接安全性、加密和合规性方面的差异。"
services: sql-database
documentationcenter: 
author: tmullaney
manager: jhubbard
editor: 
ms.assetid: a012bb85-7fb4-4fde-a2fc-cf426c0a56bb
ms.service: sql-database
ms.custom: security-overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/01/2017
ms.author: thmullan;jackr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 957644b81d373ce8d60a8c57c1ba6431bbdf83cb
ms.contentlocale: zh-cn
ms.lasthandoff: 04/28/2017


---
# <a name="securing-your-sql-database"></a>保护你的 SQL 数据库

本文介绍了使用 Azure SQL 数据库保护应用程序数据层的基础知识。 具体而言，本文将介绍如何使用相应的资源来保护数据、控制访问和执行主动监视。 

有关 SQL 各版本上提供的安全功能的完整概述，请参阅 [SQL Server 数据库引擎和 Azure SQL 数据库安全中心](https://msdn.microsoft.com/library/bb510589)。 [安全与 Azure SQL 数据库技术白皮书](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF) 中提供了其他信息。

## <a name="protect-data"></a>保护数据
SQL 数据库可以保护数据。对于动态数据，它使用[传输层安全性](https://support.microsoft.com/kb/3135244)提供加密；对于静态数据，使用[透明数据加密](http://go.microsoft.com/fwlink/?LinkId=526242)提供加密；对于使用中的数据，将使用 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 提供加密。 

> [!IMPORTANT]
>在与数据库相互“传输”数据时，与 Azure SQL 数据库建立的所有连接都需要经过加密 (SSL/TLS)。 必须在应用程序连接字符串中指定用于加密连接的参数，而*不要*信任服务器证书（服务器证书用于将连接字符串复制到 Azure 经典门户外部），否则，连接将不会验证服务器的身份，并且容易受到“中间人”攻击。 例如，对于 ADO.NET 驱动程序，这些连接字符串参数为 **Encrypt=True** 和 **TrustServerCertificate=False**。 

若要通过其他方法加密数据，请考虑：

* 使用[单元格级加密](https://msdn.microsoft.com/library/ms179331.aspx)，借助不同的加密密钥来加密特定的数据列甚至数据单元格。
* 如果需要硬件安全模块或需要加密密钥层次结构的集中管理，请考虑[对 Azure VM 中的 SQL Server 使用 Azure 密钥保管库](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)。

## <a name="control-access"></a>控制访问
SQL 数据库通过限制对数据库的访问来保护数据，具体措施包括：使用防火墙规则，实施要求用户证明其身份的身份验证机制，通过基于角色的成员身份和权限实施数据访问授权，以及使用行级别安全性和动态数据屏蔽。 有关使用 SQL 数据库中的访问控制功能的介绍，请参阅[控制访问](sql-database-control-access.md)。

> [!IMPORTANT]
> 管理 Azure 中的数据库和逻辑服务器由门户用户帐户的角色分配控制。 有关此主题的详细信息，请参阅 [Azure 门户中基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。
>

### <a name="firewall-and-firewall-rules"></a>防火墙和防火墙规则
为了帮助保护数据，在使用[防火墙规则](sql-database-firewall-configure.md)指定哪些计算机具有访问权限之前，防火墙将禁止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。

### <a name="authentication"></a>身份验证
SQL 数据库身份验证是指连接到数据库时如何证明用户的身份。 SQL 数据库支持两种类型的身份验证：

* **SQL 身份验证**，使用用户名和密码。 在为数据库创建逻辑服务器时，你已指定一个包含用户名和密码的“服务器管理员”登录名。 通过这些凭据，你可以使用数据库所有者（即“dbo”）的身份通过服务器上任何数据库的身份验证。 
* **Azure Active Directory 身份验证**，使用 Azure Active Directory 管理的标识，支持托管域和集成域。 请[尽可能](https://msdn.microsoft.com/library/ms144284.aspx)使用 Active Directory 身份验证（集成安全性）。 如果想要使用 Azure Active Directory 身份验证，则必须创建名为“Azure AD 管理员”的另一个服务器管理员，用于管理 Azure AD 用户和组。 此管理员还能执行普通服务器管理员可以执行的所有操作。 有关如何创建 Azure AD 管理员以启用 Azure Active Directory 身份验证的演练，请参阅[通过使用 Azure Active Directory 身份验证连接到 SQL 数据库](sql-database-aad-authentication.md)。

### <a name="authorization"></a>授权
授权是指用户可以在 Azure SQL 数据库中执行哪些操作，这由用户帐户的数据库角色成员身份和对象级权限来控制。 作为最佳实践，应向用户授予所需的最低权限。 用于连接的服务器管理员帐户是 db_owner 所有者的成员，该帐户有权在数据库中执行任何操作。 请保存此帐户，以便部署架构升级并执行其他管理操作。 权限受到更多限制的“ApplicationUser”帐户可让用户使用应用程序所需的最低权限从应用程序连接到数据库。

### <a name="row-level-security"></a>行级别安全性
行级别安全性使客户能够根据执行查询的用户的特征（例如，组成员身份或执行上下文），控制对数据库表中的行的访问。 有关详细信息，请参阅[行级别安全性](https://msdn.microsoft.com/library/dn765131)。

### <a name="data-masking"></a>数据屏蔽 
SQL 数据库动态数据屏蔽通过向无特权用户屏蔽敏感数据来控制此类数据的透漏。 动态数据屏蔽可自动发现 Azure SQL 数据库中潜在的敏感数据，提供可行的建议来屏蔽这些字段，对应用程序层造成的影响可忽略不计。 它的工作原理是在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。 有关详细信息，请参阅 [SQL 数据库动态数据屏蔽入门](sql-database-dynamic-data-masking-get-started.md)，了解如何限制敏感数据的透露。

## <a name="proactive-monitoring"></a>主动监视
SQL 数据库通过提供审核和威胁检测功能来保护数据。 

### <a name="auditing"></a>审核
Azure SQL 数据库审核可跟踪数据库活动，通过将数据库事件记录到 Azure 存储帐户中的审核日志，帮助用户保持合规性。 使用审核可以了解正在进行的数据库活动，以及分析和调查历史活动，标识潜在威胁或可疑的滥用行为和安全违规。 有关更多信息，请参阅 [SQL 数据库审核入门](sql-database-auditing.md)。  

### <a name="threat-detection"></a>威胁检测
威胁检测是审核的补充，它在 Azure SQL 数据库服务中提供一个内置的附加安全智能层，用于检测企图访问或使用数据库的异常的潜在有害尝试。 出现可疑活动、潜在漏洞、 SQL 注入攻击和异常数据库访问模式时，它会发出警报。 可在 [Azure 安全中心](https://azure.microsoft.com/en-us/services/security-center/)查看威胁检测警报，此警报提供可疑活动的详细信息以及如何调查和缓解威胁的建议操作。 威胁检测费用为每服务器每月 15 美元。 前 60 天可免费使用。有关详细信息，请参阅 [SQL 数据库威胁检测入门](sql-database-threat-detection.md)
 
### <a name="data-masking"></a>数据屏蔽 
SQL 数据库动态数据屏蔽通过向无特权用户屏蔽敏感数据来控制此类数据的透漏。 动态数据屏蔽可自动发现 Azure SQL 数据库中潜在的敏感数据，提供可行的建议来屏蔽这些字段，对应用程序层造成的影响可忽略不计。 它的工作原理是在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。 有关详细信息，请参阅 [SQL 数据库动态数据屏蔽入门](sql-database-dynamic-data-masking-get-started.md)。
 
## <a name="compliance"></a>合规性
除了上述可帮助应用程序符合各项安全法规要求的特性和功能以外，Azure SQL 数据库还定期参与审核，并已通过许多法规标准的认证。 有关详细信息，请参阅 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)，你可以从中找到 [SQL 数据库法规认证](https://azure.microsoft.com/support/trust-center/services/)的最新列表。

## <a name="next-steps"></a>后续步骤

- 有关使用 SQL 数据库中的访问控制功能的介绍，请参阅[控制访问](sql-database-control-access.md)。
- 有关数据库审核的讨论，请参阅 [SQL 数据库审核](sql-database-auditing.md)。
- 有关威胁检测的讨论，请参阅 [SQL 数据库威胁检测](sql-database-threat-detection.md)。

