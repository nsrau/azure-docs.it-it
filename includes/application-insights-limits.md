---
title: File di inclusione
description: File di inclusione
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 31ff6ae66b13eb5e5abcde94e8d3df7f6e32f63c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179906"
---
Vi sono alcuni limiti sul numero di metriche ed eventi per ogni applicazione, vale a dire, per ogni chiave di strumentazione. I limiti dipendono dal [piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/) scelto.

| Resource | Limite predefinito | Note
| --- | --- | --- |
| Totale dati al giorno | 100 GB | È possibile ridurre i dati impostando un limite. Se sono necessari più dati, è possibile aumentare il limite nel portale fino a 1.000 GB. Per capacità superiori a 1.000 GB, inviare posta elettronica a AIDataCap@microsoft.com.
| Limitazione | 32.000 eventi/secondo | Il limite viene misurato nell'arco di un minuto.
| Conservazione dei dati | 90 giorni | Questa risorsa è destinata a [Ricerca](../articles/azure-monitor/app/diagnostic-search.md), [Analisi](../articles/azure-monitor/app/analytics.md) e [Esplora metriche](../articles/azure-monitor/app/metrics-explorer.md).
| Conservazione dei risultati dettagliati di [test di disponibilità in più passi](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) | 90 giorni | Questa risorsa fornisce risultati dettagliati per ogni passaggio.
| Dimensioni massime dell'evento | 64.000 |
| Lunghezza nomi di proprietà e metriche | 150 | Vedere gli [schemi per tipo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Lunghezza stringa valore di proprietà | 8,192 | Vedere gli [schemi per tipo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Lunghezza messaggio di traccia e di eccezione | 32.768  | Vedere gli [schemi per tipo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Numero di [test di disponibilità](../articles/azure-monitor/app/monitor-web-app-availability.md) per app | 100 |
| Conservazione dati [profiler](../articles/azure-monitor/app/profiler.md) | 5 giorni |
| Dati [profiler](../articles/azure-monitor/app/profiler.md) inviati al giorno | 10 GB |

Per altre informazioni, vedere [Informazioni su prezzi e quote in Application Insights](../articles/azure-monitor/app/pricing.md).