---
title: "混合标识：目录集成工具比较 | Microsoft Docs"
description: "本页将提供针对可用于目录集成的各种目录集成工具进行比较的综合表。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: facdbff229eaf5ee20d98214c62c8b2b46acc0fd
ms.openlocfilehash: 80617b203bf5efa4cd6bed590ea937b2e68db9e1


---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>混合标识目录集成工具比较
过去数年以来，集成工具已得到发展和演进。  本文档旨在帮助提供这些工具的合并视图，并比较每个工具提供的功能。

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect 整合了以前作为 DirSync 和 AAD Sync 发布的组件和功能。 这些工具不再单独发布，将来所做的改进将包含在 Azure AD Connect 更新中，因此你始终知道从何处获取最新功能。
> 
> DirSync 和 Azure AD Sync 已弃用。 可在 [此处](active-directory-aadconnect-dirsync-deprecated.md)找到更多信息。
> 
> 

每个表中的代号解释如下。

● = 现在可用  
FR = 未来版本  
PP = 公开预览版  

## <a name="on-premises-to-cloud-synchronization"></a>本地到云的同步
| 功能 | Azure Active Directory Connect | Azure Active Directory 同步服务 (AAD Sync) | Azure Active Directory 同步工具 (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 连接到单个本地 AD 林 |● |● |● |● |● |
| 连接到多个本地 AD 林 |● |● | |● |● |
| 连接到多个本地 Exchange 组织 |● | | | | |
| 连接到单个在本地 LDAP 目录 |FR | | |● |● |
| 连接到多个本地 LDAP 目录 |FR | | |● |● |
| 连接到本地 AD 和本地 LDAP 目录 |FR | | |● |● |
| 连接到自定义系统（例如 SQL、Oracle、MySQL 等） |FR | | |● |● |
| 同步客户定义的属性（目录扩展） |● | | | | |
| 连接到本地 HR（即 SAP、Oracle eBusiness、PeopleSoft） |FR | | |● |● |
| 支持用于预配到本地系统的 FIM 同步规则和连接器。 | | | |● |● |

## <a name="cloud-to-on-premises-synchronization"></a>云到本地的同步
| 功能 | Azure Active Directory Connect | Azure Active Directory 同步服务 | Azure Active Directory 同步工具 (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 设备写回 |● | |● | | |
| 属性写回（适用于 Exchange 混合部署） |● |● |● |● |● |
| 组对象写回 |● | | | | |
| 密码写回（通过自助密码重置 (SSPR) 和密码更改） |● |● | | | |

## <a name="authentication-feature-support"></a>身份验证功能支持
| 功能 | Azure Active Directory Connect | Azure Active Directory 同步服务 | Azure Active Directory 同步工具 (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 单个本地 AD 林的密码同步 |● |● |● | | |
| 多个本地 AD 林的密码同步 |● |● | | | |
| 使用联合身份验证的单一登录 |● |● |● |● |● |
| 密码写回（通过 SSPR 和密码更改） |● |● | | | |

## <a name="set-up-and-installation"></a>设置和安装
| 功能 | Azure Active Directory Connect | Azure Active Directory 同步服务 | Azure Active Directory 同步工具 (DirSync) | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| 支持在域控制器上安装 |● |● |● | |
| 支持使用 SQL Express 安装 |● |● |● | |
| 从 DirSync 轻松升级 |● | | | |
| 将管理员 UX 本地化为 Windows Server 语言 |● |● |● | |
| 将最终用户 UX 本地化为 Windows Server 语言 | | | |● |
| 对 Windows Server 2008 和 Windows Server 2008 R2 的支持 |● 支持同步，不支持联合身份验证 |● |● |● |
| 对 Windows Server 2012 和 Windows Server 2012 R2 的支持 |● |● |● |● |

## <a name="filtering-and-configuration"></a>筛选和配置
| 功能 | Azure Active Directory Connect | Azure Active Directory 同步服务 | Azure Active Directory 同步工具 (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 筛选域和组织单位 |● |● |● |● |● |
| 筛选对象的属性值 |● |● |● |● |● |
| 允许同步极少量的属性 (MinSync) |● |● | | | |
| 允许对属性流应用不同的服务模板 |● |● | | | |
| 允许删除从 AD 流向 Azure AD 的属性 |● |● | | | |
| 允许对属性流进行高级自定义 |● |● | |● |● |

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。




<!--HONumber=Jan17_HO3-->


