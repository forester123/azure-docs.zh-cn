---
title: "使用查询跟踪 AS2、X12 和 EDIFACT 消息 - Azure 逻辑应用 | Microsoft 文档"
description: "在 Operations Management Suite 门户中使用查询跟踪企业到企业消息"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 67313e4ff780e6c64dfc3d767ea49167c2a390f4
ms.openlocfilehash: 701a3bbadb340c9390b486a5295eced056db70a0
ms.lasthandoff: 01/31/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>使用查询在 Operations Management Suite 门户中跟踪 B2B 消息
若要在 Operations Management Suite 门户中跟踪企业到企业 (B2B) 消息，可创建一个用于筛选特定交换控制编号的数据的查询。

## <a name="prereqs"></a>先决条件

对于调试和更详细的诊断信息，请在[集成帐户](logic-apps-monitor-b2b-message.md)中为具有 X12 控制器的[逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)启用诊断。 然后，按照此处的步骤[发布诊断数据](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)到 Operations Management Suite 门户。

## <a name="search-for-an-interchange-control-number"></a>搜索交换控制编号

1. 在主页上，选择“日志搜索”。  
![选择“日志搜索”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. 在搜索框中，键入 **Type="AzureDiagnostics"**。 若要筛选数据，请选择“添加”。  
![选择“查询”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. 键入 **interchange**，选择 **event_record_messageProperties_interchangeControlNumber_s**，然后选择“添加”。  
![添加筛选器](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. 选择想要筛选数据的控制编号，然后选择“应用”。  
![对控制编号进行搜索](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. 查找为筛选所选控制编号的数据而创建的查询。   
![查找查询](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. 键入查询名称，然后进行保存。   
![保存查询](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. 若要查看查询并将其用在以后的搜索中，请选择“收藏夹”。  
![查看查询](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. 可更新查询，搜索新的交换控制编号。  
![更新查询](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>后续步骤
* 了解有关[自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)的详细信息。   
* 了解有关 [AS2 跟踪架构](logic-apps-track-integration-account-as2-tracking-schemas.md)的详细信息。    
* 了解有关 [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)的详细信息。  
* 了解有关 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 的详细信息。

