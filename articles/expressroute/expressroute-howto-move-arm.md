---
title: "将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型 | Microsoft Docs"
description: "本页面介绍如何将经典线路转移到 Resource Manager 部署模型。"
documentationcenter: na
services: expressroute
author: ganesr
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: ganesr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1c3bd8e01e02fb66bf5e04c307863bbe54176128


---
# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型
## <a name="configuration-prerequisites"></a>配置先决条件
* 需要最新版本的 Azure PowerShell 模块（至少 1.0 版）。
* 在开始配置之前，请务必查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。
* 在继续下一步之前，请先参阅[将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-move.md)中提供的信息。 确保你已完全了解可能的限制和局限性。
* 如果你要将 Azure ExpressRoute 线路从经典部署模型转移到 Azure Resource Manager 部署模型，必须完全配置好该线路，使它能够在经典部署模型中正常工作。
* 确保拥有一个在 Resource Manager 部署模型中创建的资源组。

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>将 ExpressRoute 线路转移到 Resource Manager 部署模型
只有将 ExpressRoute 线路转移到 Resource Manager 部署模型，才可以在经典部署模型和 Resource Manager 部署模型中使用它。 可以运行以下 PowerShell 命令来实现此目的。

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>步骤 1：从经典部署模型收集线路详细信息
首先需要收集有关 ExpressRoute 线路的信息。

登录到 Azure 经典环境并收集服务密钥。 可以使用以下 PowerShell 代码段来收集信息：

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

复制要转移到 Resource Manager 部署模型的线路的**服务密钥**。

### <a name="step-2-sign-in-to-the-resource-manager-environment-and-create-a-new-resource-group"></a>步骤 2：登录到 Resource Manager 环境并创建新的资源组
可以使用以下代码段创建新的资源组：

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

你也可以使用现有的资源组（如果有）。

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>步骤 3：将 ExpressRoute 线路转移到 Resource Manager 部署模型
现在，你可以将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型。 在继续下一步之前，请先参阅[将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-move.md)中提供的信息。

可以运行以下代码段来实现此目的：

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

> [!NOTE]
> 转移完成之后，列在前一个 cmdlet 中的新名称用于处理资源。 线路实质上已重命名。
> 
> 

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>为两种部署模型启用 ExpressRoute 线路
必须先将 ExpressRoute 线路转移到 Resource Manager 部署模型，然后才可以控制对部署模型的访问。

运行以下 cmdlet 来启用对两种部署模型的访问：

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

成功完成此操作后，可以在经典部署模型中查看线路。

运行以下命令获取 ExpressRoute 线路的详细信息：

    get-azurededicatedcircuit

服务密钥必须已列出。 现在，你可以通过适用于经典 VNet 的标准经典部署模型命令以及适用于 ARM VNET 的标准 ARM 命令来管理到 ExpressRoute 线路的链接。 以下文章将引导你了解如何管理 ExpressRoute 线路的链接：

* [在 Resource Manager 部署模型中将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
* [在经典部署模型中将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)

## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>在经典部署模型中禁用 ExpressRoute 线路
运行以下 cmdlet 可禁止访问经典部署模型：

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

成功完成此操作后，你将无法在经典部署模型中查看线路。

## <a name="next-steps"></a>后续步骤
创建你的线路后，请确保执行以下操作：

* [创建和修改 ExpressRoute 线路的路由](expressroute-howto-routing-arm.md)
* [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)




<!--HONumber=Nov16_HO3-->

