---
title: "Azure 托管应用程序 PublicIpAddressCombo UI 元素 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的 Microsoft.Network.PublicIpAddressCombo UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 71d8249046ed74f67514a93ece4efc2996ad9b4c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI 元素
一组用于选择新的或现有公用 IP 地址的控件。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- 如果用户针对公用 IP 地址选择了“None”，则域名标签文本框将被隐藏。
- 如果用户选择了一个现有公用 IP 地址，则域名标签文本框将被禁用。 它的值是所选 IP 地址的域名标签。
- 域名后缀（例如 westus.cloudapp.azure.com）会基于所选位置自动更新。

## <a name="schema"></a>架构
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "foobar"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>备注
- 如果 `constraints.required.domainNameLabel` 设置为 **true**，则用户在创建新的公用 IP 地址时必须提供域名标签。 没有标签的现有公用 IP 地址不可供选择。
- 如果 `options.hideNone` 设置为 **true**，则用于为公用 IP 地址选择 **None** 的选项将被隐藏。 默认值为 **false**。
- 如果 `options.hideDomainNameLabel` 设置为 **true**，则域名标签的文本框将被隐藏。 默认值为 **false**。
- 如果 `options.hideExisting` 为 true，则用户无法选择现有的公用 IP 地址。 默认值为 **false**。

## <a name="sample-output"></a>示例输出
如果用户未选择公用 IP 地址，则应当生成以下输出：
```json
{
  "newOrExistingOrNone": "none"
}
```

如果用户选择了新的或现有 IP 地址，则应当生成以下输出：
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "foobar",
  "newOrExistingOrNone": "new"
}
```
- 当指定了 `options.hideNone` 时，`newOrExistingOrNone` 始终返回 **none**。
- 当指定了 `options.hideDomainNameLabel` 时，将取消声明 `domainNameLabel`。

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

