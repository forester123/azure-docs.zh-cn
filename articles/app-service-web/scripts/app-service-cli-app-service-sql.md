---
title: "Azure CLI 脚本示例 - 将 Web 应用连接到 SQL 数据库 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 将 Web 应用连接到 SQL 数据库"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 56fe6c0f943d9b5940278a09bc09bf54efbdcc6a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---

# <a name="connect-a-web-app-to-a-sql-database"></a>将 Web 应用连接到 SQL 数据库

在此方案中，你将了解如何创建 Azure SQL 数据库和 Azure Web 应用。 然后，将使用应用设置将 SQL 数据库链接到 Web 应用。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[主要](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL 数据库")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Web 应用、SQL 数据库和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 这与 Azure Web 应用的服务器场类似。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | 创建应用服务计划中的 Azure Web 应用。 |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | 创建 SQL 数据库服务器。  |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | 使用 SQL 数据库服务器创建新的数据库。 |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | 创建或更新 Azure Web 应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。

