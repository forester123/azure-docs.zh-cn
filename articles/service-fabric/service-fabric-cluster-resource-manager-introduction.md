---
title: "Service Fabric 群集资源管理器简介 | Microsoft 文档"
description: "介绍 Service Fabric 群集资源管理器。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 490ec2933130f15d707a06206e452f47554acdeb


---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Service Fabric 群集资源管理器简介
在传统上，管理 IT 系统或一组服务意味着要使一些物理机或虚拟机专用于这些特定的服务或系统。 许多主要服务划分为“Web”层和“数据”或“存储”层，此外可能还有其他一些专用组件，例如缓存。 其他类型的应用程序有消息层，请求在其中流进和流出、连接到工作层以进行任何分析，或者在消息传送过程中进行必要的转换。 每种类型的工作负荷都有专用的计算机：数据库需要一些专用计算机，Web 服务器也需要一些。 如果特定类型的工作负荷导致其所在的计算机运行太过繁忙，则可以添加更多计算机，并在其上配置要运行该类型的工作负荷，或者使用更大型计算机来取代某些计算机。 这很容易理解。 如果某台计算机发生故障，则在还原该计算机之前，整个应用程序中的该部件将以较低容量来运行。 这仍然很容易理解（但不一定有趣）。

但是，我们现在假设你已发现有扩展的需要并采用了容器和/或微服务。 我们突然发现自己拥有数十、数百甚至数千台计算机、数十种不同类型的服务，或许针对这些服务拥有数百个不同的实例，而每一个都拥有一个或多个实例或副本以实现高可用性 (HA)。

突然间，管理环境并不像管理一些专用于单一类型工作负荷的计算机一样简单。 服务器是虚拟的且不再具有名称（毕竟*现在*要管理的是[一大堆](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20)而不是几台计算机）。 有关计算机的配置减少了，有关服务本身的配置增多了。 专用硬件已是过去的产物，服务已经变成小型分布式系统，跨越多个较小的商用硬件。

由于这会解构以前的单体式分层应用并分解到在商用硬件上运行的独立服务，因此，现在有许多更高程度的组合需要处理。 哪种因素决定了哪种类型的工作负荷可在特定的硬件上，或者可以运行多少个工作负荷？ 哪些工作负荷可在相同的硬件上运行得更好，哪些会发生冲突？ 当一台计算机出现故障... 哪些程序仍在运行？ 哪种机制负责确保该工作负荷可再次开始运行？ 是否正在等待（虚拟）计算机恢复正常，或者工作负荷自动故障转移到其他计算机并保持运行？ 是否需要人工干预？ 如何在这种环境中升级？

身为这种领域中的开发人员和操作员，我们需要有人帮助控制这种复杂性，同时我们有一种观念：大量招聘以及尝试使用人员来掩饰复杂性并非正确解答。

怎么办？

## <a name="introducing-orchestrators"></a>协调器简介
“协调器”是软件片段中使用的一般术语，可帮助管理员管理这种类型的环境。 协调器是以类似于“我想要在环境中运行此服务的 5 个副本”的形式发出请求的组件，实现该请求，然后（尝试）以该方式不断发出请求。

协调器（不是人类）是当计算机故障或工作负荷出于某种意外原因而终止时，要快速采取措施的组件。 大多数协调器除了处理故障以外还能执行更多操作，例如，帮助完成新部署、处理升级，以及处理资源消耗，但基本上全部都与在环境中保持某种所需配置状态有关。 你可以将自己的预期告诉协调器，让它帮助你完成繁重的工作。 例如，位于 Mesos、Chronos、Fleet、Swarm、Kubernetes 和 Service Fabric 顶层的 Chronos 或 Marathon 都是协调器（或者有内置的协调器）。 由于在不同类型的环境中管理现实部署的复杂性和条件不断加剧和变化，持续创建的协调器越来越多。

## <a name="orchestration-as-a-service"></a>协调即服务
在 Service Fabric 群集中，协调器的作业主要由群集资源管理器来处理。 Service Fabric 群集资源管理器是 Service Fabric 中的系统服务之一，将在每个群集中自动启动。  一般而言，群集资源管理器的作业可划分为三个部分：

1. 强制实施规则
2. 优化你的环境
3. 为其他进程提供协助

### <a name="what-it-isnt"></a>它不是什么
在传统的 N 层 Web 应用中，一直有“负载均衡器”的概念存在，根据其在网络堆栈中的所在位置，通常称为网络负载均衡器 (NLB) 或应用程序负载均衡器 (ALB)。 有些负载均衡器基于硬件（例如 F5 的 BigIP 产品），有些则是基于软件（例如 Microsoft 的 NLB）。 在其他环境中，可能会在此角色中看到类似于 HAProxy 的组件。 在这些体系结构中，负载平衡的作业是确定所有不同的无状态前端计算机或群集中的不同计算机接收到（大致上）相同数量的工作。 实现此目的的策略各不相同，有的是将每个调用发送到不同的服务器，有的采用会话固定/粘滞，还有的是根据预期成本和当前计算机负载来进行实际估计和调用分配。

请注意，这是确保 Web 层大致保持平衡的最佳机制。 平衡数据层的策略则完全不同并取决于数据存储机制，通常集中在数据分片、缓存、数据库托管视图和存储过程等方面。

尽管其中有些策略很有作用，但 Service Fabric 群集资源管理器的功能并不像网络负载均衡器或缓存一样。 尽管网络负载均衡器可确保前端能够通过将流量移到服务运行所在的位置来实现平衡，但 Service Fabric 群集资源管理器采取完全不同的策略 – 本质上，Service Fabric *服务*移到最有利的位置（流量或负载随之也会如此）。 例如，该位置可能是当前较冷的节点，因为服务目前并未执行很多工作，或者已删除或移到其他位置。 另一个示例是群集资源管理器可能将服务从以下计算机中移走：即将升级的计算机，或由于在其上运行的服务耗用量突然增加而导致超载的计算机。 由于群集资源管理器负责移动服务（不提供对服务所在位置的网络流量），因此它提供一个截然不同于网络负载均衡器的功能，采用本质上不同的策略来确保群集中的硬件资源得到充分利用。

## <a name="next-steps"></a>后续步骤
* 有关群集资源管理器中的体系结构和信息流的信息，请查看[此文](service-fabric-cluster-resource-manager-architecture.md)
* 群集资源管理器提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇有关[描述 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章
* 有关可用于配置服务的其他选项的详细信息，请查看[了解如何配置服务](service-fabric-cluster-resource-manager-configure-services.md)中提供的其他群集资源管理器配置的相关主题
* 指标是 Service Fabric 群集资源管理器在群集中管理消耗和容量的方式。 若要详细了解指标及其配置方式，请查看[此文](service-fabric-cluster-resource-manager-metrics.md)
* 群集资源管理器可与 Service Fabric 的管理功能配合使用。 若要了解有关这种集成的详细信息，请阅读[此文](service-fabric-cluster-resource-manager-management-integration.md)
* 若要了解群集资源管理器如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章




<!--HONumber=Nov16_HO3-->

