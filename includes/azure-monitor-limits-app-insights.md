---
title: File di inclusione
description: File di inclusione
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 0185710b6255dabd61d6361ed98c658f1e8075c7
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975314"
---
Esistono alcuni limiti per il numero di metriche ed eventi per ogni applicazione, ovvero per ogni chiave di strumentazione. I limiti dipendono dal [piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/) scelto.

| Resource | Limite predefinito | Nota
| --- | --- | --- |
| Totale dati al giorno | 100 GB | È possibile ridurre i dati impostando un limite. Se sono necessari più dati, è possibile aumentare il limite nel portale fino a 1.000 GB. Per le capacità superiori a 1.000 GB, inviare un messaggio di posta elettronica a AIDataCap@microsoft.com.
| Limitazione | 32.000 eventi/secondo | Il limite viene misurato nell'arco di un minuto.
| Conservazione dei dati | 90 giorni | Questa risorsa è destinata a [Ricerca](../articles/azure-monitor/app/diagnostic-search.md), [Analisi](../articles/azure-monitor/app/analytics.md) e [Esplora metriche](../articles/azure-monitor/app/metrics-explorer.md).
| Conservazione dei risultati dettagliati di [test di disponibilità in più passi](../articles/azure-monitor/app/availability-multistep.md) | 90 giorni | Questa risorsa fornisce risultati dettagliati per ogni passaggio.
| Dimensioni massime dell'evento | 64.000 |
| Lunghezza nomi di proprietà e metriche | 150 | Vedere gli [schemi per tipo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Lunghezza stringa valore di proprietà | 8,192 | Vedere gli [schemi per tipo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Lunghezza messaggio di traccia e di eccezione | 32.768  | Vedere gli [schemi per tipo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Numero di [test di disponibilità](../articles/azure-monitor/app/monitor-web-app-availability.md) per app | 100 |
| Conservazione dati [profiler](../articles/azure-monitor/app/profiler.md) | 5 giorni |
| Dati [profiler](../articles/azure-monitor/app/profiler.md) inviati al giorno | 10 GB |

Per altre informazioni, vedere [Informazioni su prezzi e quote in Application Insights](../articles/azure-monitor/app/pricing.md).
