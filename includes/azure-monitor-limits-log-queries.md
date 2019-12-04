---
title: file di inclusione
description: file di inclusione
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796147"
---
| Limite | Description |
|:---|:---|
| Linguaggio di query | Monitoraggio di Azure usa lo stesso [linguaggio di query di Kusto](/azure/kusto/query/) di Azure Esplora dati. Vedere le [differenze del linguaggio di query del log di monitoraggio di Azure](../articles/azure-monitor/log-query/data-explorer-difference.md) per gli elementi del linguaggio KQL non supportati in monitoraggio di Azure. |
| Aree di Azure | Le query di log possono avere un sovraccarico eccessivo quando i dati si estendono Log Analytics aree di lavoro in più aree di Azure. Per informazioni dettagliate, vedere [limiti della query](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Query tra risorse | Il numero massimo di risorse di Application Insights e di Log Analytics aree di lavoro in una singola query è limitato a 100.<br>La query tra risorse non è supportata in Progettazione viste.<br>La query tra risorse negli avvisi del log è supportata nella nuova API scheduledQueryRules.<br>Per informazioni dettagliate, vedere [limiti di query tra risorse](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |
| Limitazione query | Un utente è limitato a 200 query per 30 secondi su un numero qualsiasi di aree di lavoro. Questo limite si applica alle query a livello di codice o alle query avviate da parti di visualizzazione come i dashboard di Azure e la pagina di riepilogo dell'area di lavoro Log Analytics. |
