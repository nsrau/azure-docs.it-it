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
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405682"
---
| Limite | DESCRIZIONE |
|:---|:---|
| Linguaggio di query | Monitoraggio di Azure usa lo stesso [linguaggio di query di Kusto](/azure/kusto/query/) di Azure Esplora dati. Vedere le [differenze del linguaggio di query del log di monitoraggio di Azure](../articles/azure-monitor/log-query/data-explorer-difference.md) per gli elementi del linguaggio KQL non supportati in monitoraggio di Azure. |
| Aree di Azure | Le query di log possono avere un sovraccarico eccessivo quando i dati si estendono Log Analytics aree di lavoro in più aree di Azure. Per informazioni dettagliate, vedere [limiti della query](../articles/azure-monitor/log-query/scope.md#query-limits) . |
