---
title: "Azure 资源策略 | Microsoft Docs"
description: "介绍如何使用 Azure Resource Manager 策略，以确保部署期间资源属性设置的一致性。 可在订阅或资源组中应用策略。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 951a7849beb9653083ed0112dbbb6cf57175469d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---
# <a name="resource-policy-overview"></a>资源策略概述
通过资源策略，可在组织中建立资源约定。 通过定义约定，可以控制成本并更轻松地管理资源。 例如，可以指定仅允许某些类型的虚拟机，或者可以要求所有资源都有特定标记。 策略由所有子资源继承。 因此，如果将策略应用到资源组，则会将其应用到该资源组中的所有资源。

了解策略的两个概念：

* 策略定义 - 描述何时强制执行策略，以及要采取的操作
* 策略分配 - 应用策略定义的范围（订阅或资源组）

本主题重点介绍策略定义。 有关策略分配的信息，请参阅[使用 Azure 门户分配和管理资源策略](resource-manager-policy-portal.md)或[通过脚本分配和管理策略](resource-manager-policy-create-assign.md)。

Azure 提供了一些可降低必须要定义的策略数目的内置策略定义。 如果内置策略定义适用于你的方案，则请在分配范围时使用该定义。

在创建和更新资源（PUT 和 PATCH 操作）时评估策略。

> [!NOTE]
> 当前，策略不对不支持标记、种类和位置的资源类型进行评估，例如 Microsoft.Resources/deployments 资源类型。 将来会添加此支持。 若要避免向后兼容问题，创作策略时应显式指定类型。 例如，未指定类型的标记策略会应用于所有类型。 在此情况下，如果有嵌套资源不支持标记，并且部署资源类型已添加到策略评估中，则模板部署可能会失败。 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>策略与 RBAC 有什么不同？
策略和基于角色的访问控制 (RBAC) 之间存在一些主要区别。 RBAC 关注不同范围内的**用户**操作。 例如，将你添加到所需范围的资源组的参与者角色后，你可对该资源组做出更改。 策略关注部署期间的“资源”属性。 例如，通过策略，你可以控制可预配的资源类型，或限制可以预配资源的位置。 不同于 RBAC，策略是默认的允许和明确拒绝系统。 

若要使用策略，必须通过 RBAC 完成身份验证。 具体而言，你的帐户需要：

* `Microsoft.Authorization/policydefinitions/write` 定义策略的权限
* `Microsoft.Authorization/policyassignments/write` 分配策略的权限 

**参与者**角色中未包括这些权限。

## <a name="policy-definition-structure"></a>策略定义结构
使用 JSON 创建策略定义。 策略定义包含以下项的元素：

* parameters
* 显示名称
* description
* 策略规则
  * 逻辑评估
  * 效果

以下示例说明限制资源部署位置的策略：

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="parameters"></a>parameters
使用参数可减少策略定义的数量，有助于简化策略管理。 为资源属性定义策略（如限制资源部署的位置），并在定义中包含参数。 然后，通过在分配策略时传递不同的值（例如为订阅指定一组位置），针对不同的方案重复使用该策略定义。

在创建策略定义时声明参数。

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

参数类型可以是字符串，也可以是数组。 Azure 门户等工具使用元数据属性显示用户友好信息。 

在策略规则中，使用下列语法引用参数： 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>显示名称和说明

使用**显示名称**和**说明**来标识策略定义，并提供其使用情景。

## <a name="policy-rule"></a>策略规则

策略规则包括 **If** 和 **Then** 块。 在 **If** 块中，定义强制执行策略时指定的一个或多个条件。 可以对这些条件应用逻辑运算符，以精确定义策略的方案。 在 **Then** 块中，定义满足 **If** 条件时产生的效果。

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>逻辑运算符
支持的逻辑运算符为：

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

**not** 语法反转条件的结果。 **allOf** 语法（与逻辑 **And** 操作相似）要求所有条件为 true。 **anyOf** 语法（与逻辑 **Or** 操作相似）要求一个或多个条件为 true。

可以嵌套逻辑运算符。 以下示例显示了嵌套在 **And** 操作中的 **Not** 操作。 

```json
"if": {
  "allOf": [
    {
      "not": {
        "field": "tags",
        "containsKey": "application"
      }
    },
    {
      "field": "type",
      "equals": "Microsoft.Storage/storageAccounts"
    }
  ]
},
```

### <a name="conditions"></a>条件
条件评估**字段**是否符合特定的准则。 支持的条件有：

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

在使用 **like** 条件时，可以在值中提供通配符 (*)。

当使用 **match** 条件时，请提供 `#` 来表示数字，提供 `?` 来表示字母，提供任何其他字符来表示该实际字符。 有关示例，请参阅[设置命名约定](#set-naming-convention)。

### <a name="fields"></a>字段
使用字段构成条件。 字段显示用于描述资源状态的资源请求负载属性。  

支持以下字段：

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* 属性别名

可以使用属性别名来访问资源类型的特定属性。 支持的别名为：

* Microsoft.CDN/profiles/sku.name
* Microsoft.Compute/virtualMachines/imageOffer
* Microsoft.Compute/virtualMachines/imagePublisher
* Microsoft.Compute/virtualMachines/sku.name
* Microsoft.Compute/virtualMachines/imageSku 
* Microsoft.Compute/virtualMachines/imageVersion
* Microsoft.SQL/servers/databases/edition
* Microsoft.SQL/servers/databases/elasticPoolName
* Microsoft.SQL/servers/databases/requestedServiceObjectiveId
* Microsoft.SQL/servers/databases/requestedServiceObjectiveName
* Microsoft.SQL/servers/elasticPools/dtu
* Microsoft.SQL/servers/elasticPools/edition
* Microsoft.SQL/servers/version
* Microsoft.Storage/storageAccounts/accessTier
* Microsoft.Storage/storageAccounts/enableBlobEncryption
* Microsoft.Storage/storageAccounts/sku.name
* Microsoft.Web/serverFarms/sku.name

### <a name="effect"></a>效果
策略支持三种类型的效果 - `deny`、`audit` 和 `append`。 

* **Deny** 将在审核日志中生成一个事件，并使请求失败
* **Audit** 将在审核日志中生成一个警告事件，但不会使请求失败
* **Append** 会将定义的字段集添加到请求 

对于 **append**，必须提供以下详细信息：

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

值可以是字符串或 JSON 格式对象。 

## <a name="policy-examples"></a>策略示例

以下主题包含策略示例：

* 有关标记策略的示例，请参阅[将资源策略应用于标记](resource-manager-policy-tags.md)。
* 有关存储策略的示例，请参阅[将资源策略应用于存储帐户](resource-manager-policy-storage.md)。
* 有关虚拟机策略的示例，请参阅[将资源策略应用于 Linux VM](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) 和[将资源策略应用于 Windows WM](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

### <a name="allowed-resource-locations"></a>允许的资源位置
要指定允许的位置，请参阅[策略定义结构](#policy-definition-structure)中的示例。 要分配此策略定义，请使用带有资源 ID `/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c` 的内置策略。

### <a name="not-allowed-resource-locations"></a>不允许的资源位置
要指定不允许的位置，请使用下列策略定义：

```json
{
  "properties": {
    "parameters": {
      "notAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that are not allowed when deploying resources",
          "strongType": "location",
          "displayName": "Not allowed locations"
        }
      }
    },
    "displayName": "Not allowed locations",
    "description": "This policy enables you to block locations that your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "field": "location",
        "in": "[parameters('notAllowedLocations')]"
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="allowed-resource-types"></a>允许的资源类型
以下示例显示只允许对 Microsoft.Resources、Microsoft.Compute、Microsoft.Storage 和 Microsoft.Network 资源类型进行部署的策略。 其余皆拒绝：

```json
{
  "if": {
    "not": {
      "anyOf": [
        {
          "field": "type",
          "like": "Microsoft.Resources/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Compute/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Storage/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Network/*"
        }
      ]
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

### <a name="set-naming-convention"></a>设置命名约定
以下示例演示如何使用 **like** 条件支持的通配符。 该条件指明，如果名称符合所述模式 (namePrefix\*nameSuffix)，则拒绝请求：

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

若要指定与某个模式匹配的资源名称，请使用 match 条件。 下面的示例要求名称以 `contoso` 开头并包含六个其他字母：

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

若要求日期模式为两个数字、短划线、三个字母、短划线和四个数字，请使用以下代码：

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>后续步骤
* 定义策略规则之后，将其分配到某一范围。 若要通过门户分配策略，请参阅[使用 Azure 门户分配和管理资源策略](resource-manager-policy-portal.md)。 若要通过 REST API、PowerShell 或 Azure CLI 分配策略，请参阅[通过脚本分配和管理策略](resource-manager-policy-create-assign.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。
* 该策略架构在 [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json) 中发布。 


