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
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74796147"
---
| Limite | Descrizione |
|:---|:---|
| Linguaggio di query | Monitoraggio di Azure usa lo stesso linguaggio di query Kusto di Azure Data Explorer.Azure Monitor uses the same [Kusto query language](/azure/kusto/query/) as Azure Data Explorer. Vedere Differenze del [linguaggio](../articles/azure-monitor/log-query/data-explorer-difference.md) di query del log di Monitoraggio di Azure per gli elementi del linguaggio KQL non supportati in Monitoraggio di Azure.See Azure Monitor log query language differences for KQL language elements not supported in Azure Monitor. |
| Aree di Azure | Le query di log possono verificarsi un sovraccarico eccessivo quando i dati si estendono su aree di lavoro di Log Analytics in più aree di Azure.Log queries can experience excessive overhead when data spans Log Analytics workspaces in multiple Azure regions. Per informazioni dettagliate, vedere [Limiti delle query.](../articles/azure-monitor/log-query/scope.md#query-limits) |
| Query tra risorse | Numero massimo di risorse di Application Insights e aree di lavoro di Log Analytics in una singola query limitata a 100.Maximum number of Application Insights resources and Log Analytics workspaces in a single query limited to 100.<br>La query tra risorse non è supportata in Progettazione viste.<br>Le query su più risorse negli avvisi dei log sono supportate nella nuova API scheduledQueryRules.<br>Per informazioni [dettagliate,](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) vedere Limiti delle query tra risorse. |
| Limitazione delle query | Un utente è limitato a 200 query ogni 30 secondi in un numero qualsiasi di aree di lavoro. Questo limite si applica alle query a livello di codice o alle query avviate da parti di visualizzazione, ad esempio dashboard di Azure e la pagina di riepilogo dell'area di lavoro di Log Analytics.This limit applies to programmatic queries or to queries initiated by visualization parts such as Azure dashboards and the Log Analytics workspace summary page. |
