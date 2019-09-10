---
title: File di inclusione
description: File di inclusione
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "69657802"
---
| Limite | Descrizione |
|:---|:---|
| Linguaggio di query | Monitoraggio di Azure usa lo stesso [linguaggio di query di Kusto](/azure/kusto/query/) di Azure Esplora dati. Vedere le [differenze del linguaggio di query del log di monitoraggio di Azure](../articles/azure-monitor/log-query/data-explorer-difference.md) per gli elementi del linguaggio KQL non supportati in monitoraggio di Azure. |
| Aree di Azure | Le query di log possono avere un sovraccarico eccessivo quando i dati si estendono Log Analytics aree di lavoro in più aree di Azure. Per informazioni dettagliate, vedere [limiti della query](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Query tra risorse | Il numero massimo di risorse di Application Insights e di Log Analytics aree di lavoro in una singola query è limitato a 100.<br>La query tra risorse non è supportata in Progettazione viste.<br>La query tra risorse negli avvisi del log è supportata nella nuova API scheduledQueryRules.<br>Per informazioni dettagliate, vedere [limiti di query tra risorse](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |