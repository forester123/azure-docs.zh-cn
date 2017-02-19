---
title: "Azure 中 Linux VM 的可用性集 | Microsoft 文档"
description: "了解用于在 Azure 基础结构服务中部署可用性集的关键设计和实施准则。"
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 24f1d91c-8cc0-4251-bb67-ac4c4c37e8cd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 233116deaaaf2ac62981453b05c4a5254e836806
ms.openlocfilehash: 0c31fb1d02e26491de8d1076d074a2021906999f


---
# <a name="azure-availability-sets-guidelines"></a>Azure 可用性集准则
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

本文重点介绍可用性集的必需规划步骤，按照这些步骤可确保应用程序在计划内或计划外事件期间保持可访问性。

## <a name="implementation-guidelines-for-availability-sets"></a>可用性集的实施准则
决策：

* 你的应用程序基础结构中的各种角色和层需要多少可用性集？

任务：

* 定义所需的每个应用程序层中的 VM 数量。
* 确定是否需要调整用于你的应用程序的容错域或更新域数。
* 使用命名约定定义所需的可用性集以及其中包含的 VM。 一台 VM 只能驻留在一个可用性集内。 

## <a name="availability-sets"></a>可用性集
在 Azure 中，可将虚拟机 (VM) 置于名为可用性集的逻辑分组中。 在可用性集内创建 VM 时，Azure 平台在底层基础结构内分布放置这些 VM。 发生 Azure 平台或底层硬件/基础结构故障的计划内维护事件时，使用可用性集确保至少一台 VM 保持运行。

最佳做法是，不应使应用程序于仅驻留在单一 VM 上。 当 Azure 平台内发生计划内或计划外事件时，包含单一 VM 的可用性集得不到任何保护。 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) 要求一个可用性集内有两台或更多 VM，以允许在底层基础结构内分布 VM。

Azure 中的底层基础结构分为更新域和容错域。 这些域是按共享公用更新周期或共享相同物理基础结构（如电源和网络）的主机定义的。 Azure 自动跨域分布可用性集中的 VM 来维持可用性和容错能力。 根据你的应用程序的大小和可用性集内的 VM 数，你可以调整要使用的域的数目。 参阅有关[管理更新域和容错域的可用性和使用](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的详细信息。

设计应用程序基础结构时，应规划好要使用的应用程序层。 将服务于同一目的的 VM 分为一组，放到可用性集中，例如用于容纳运行 nginx 或 Apache 的前端 VM 的可用性集。 为运行 MongoDB 或 MySQL 的后端 VM 创建另一个可用性集。 目标是确保应用程序的每个组件都受可用性集保护，且其至少一个实例将始终保持运行。

可对每个应用程序层利用负载平衡器，使其与可用性集一起工作，确保始终可将流量路由到正在运行的实例。 不使用负载平衡器，你的 VM 可能在计划内和计划外维护事件中继续运行，但如果主 VM 不可用，最终用户可能无法解决这些问题。

在存储层针对高可用性设计应用程序。 最佳实践是为可用性集中的每个 VM 使用单独的存储帐户。 将与同一 VM 关联的所有磁盘（OS 和数据）放置在同一存储帐户中。 向存储帐户中添加更多 VHD 时，考虑存储帐户[限制](../storage/storage-scalability-targets.md)。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Jan17_HO5-->

