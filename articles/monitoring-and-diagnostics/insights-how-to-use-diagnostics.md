---
title: "在 Microsoft Azure 中启用监视和诊断 | Microsoft Docs"
description: "了解如何在 Azure 中为资源设置诊断。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: af1947a9-c211-4aa1-8924-880a86240be4
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2015
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c30ffa9e392bb6411312b0789ae6e8218f770bc1


---
# <a name="enable-monitoring-and-diagnostics"></a>启用监视和诊断
在 [Azure 门户](https://portal.azure.com)中，可以配置有关资源的丰富和频繁的监视和诊断数据。 还可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931932.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以编程方式配置诊断。

Azure 中的诊断、监视和指标数据会保存到所选择的存储帐户中。 这使你可以使用所需的任何工具将数据从存储浏览器读取到 Power BI，再到第三方工具。

## <a name="when-you-create-a-resource"></a>创建资源时
在 [Azure 门户](https://portal.azure.com)中首次创建大多数服务时，这些服务会允许用户启用诊断。

1. 转到“新建”并选择感兴趣的资源。
2. 选择“可选配置”。
    ![诊断边栏选项卡](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)
3. 选择“诊断”，然后单击“打开”。 需要选择要将诊断保存到其中的存储帐户。 向存储帐户发送诊断时，会针对存储和事务向你收取正常数据费率。
4. 单击“确定”并创建资源。

## <a name="change-settings-for-an-existing-resource"></a>为现有资源更改设置
如果已创建了资源并且要更改诊断设置（例如更改数据收集级别），则可以直接在 Azure 门户中执行该操作。

1. 转到相应资源，然后单击“设置”命令。
2. 选择“诊断”。
3. “诊断”边栏选项卡包含该资源的所有可能诊断和监视收集数据。 对于某些资源，还可以为数据选择“保留期”策略，以从存储帐户中清理它。
    ![存储诊断](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. 选择了设置之后，单击“保存”命令。 如果你是首次启用它，则监视数据可能需要一小段时间才会显示。

### <a name="categories-of-data-collection-for-virtual-machines"></a>虚拟机的数据收集类别
对于虚拟机，所有指标和日志都会按一分钟间隔进行记录，因此你可以始终获得有关计算机的最新信息。

* **基本指标**：有关虚拟机的运行状况指标（如处理器和内存）
* **网络和 web 指标**：有关网络连接和 Web 服务的指标
* **.NET 指标**：有关虚拟机上运行的 .NET 和 ASP.NET 应用程序的指标
* **SQL 指标**：如果在运行 Microsoft SQL Service，这些是其性能指标
* **Windows 事件应用程序日志**：发送到应用程序通道的 Windows 事件
* **Windows 事件系统日志**：发送到系统通道的 Windows 事件。 这还包括来自 [Microsoft 反恶意软件](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)的所有事件。
* **Windows 事件安全日志**：发送到安全通道的 Windows 事件
* **诊断基础结构日志**：有关诊断收集基础结构的日志记录
* **IIS 日志**：有关 IIS 服务器的日志

请注意，当前不支持 Linux 的某些分发，并且必须在虚拟机上安装来宾代理。

## <a name="next-steps"></a>后续步骤
* 每当操作事件发生或指标超过阈值时[接收警报通知](insights-receive-alert-notifications.md)。
* [监视服务指标](insights-how-to-customize-monitoring.md)以确保你的服务可用且响应迅速。
* [自动缩放实例计数](insights-how-to-scale.md)以确保服务基于需求进行缩放。
* 要确切了解代码在云中的执行情况时[监视应用程序性能](../application-insights/app-insights-azure-web-apps.md)。
* [查看事件和活动日志](insights-debugging-with-events.md)，以了解服务中发生的所有事件。
* [跟踪服务运行状况](insights-service-health.md)，以在 Azure 遇到性能下降或服务中断时及时发现。




<!--HONumber=Nov16_HO3-->


