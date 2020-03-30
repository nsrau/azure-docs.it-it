---
title: File di inclusione
description: File di inclusione
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 14e2bd4af2616e9dd33fe8267de132ab6c0f1cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "69982625"
---
Esistono alcuni limiti al numero di metriche ed eventi per applicazione, ovvero per chiave di strumentazione. I limiti dipendono dal [piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/) scelto.

| Risorsa | Limite predefinito | Note
| --- | --- | --- |
| Totale dati al giorno | 100 GB | È possibile ridurre i dati impostando un limite. Se sono necessari più dati, è possibile aumentare il limite nel portale fino a 1.000 GB. Per capacità superiori a 1.000 GB, inviare posta elettronica a AIDataCap@microsoft.com.
| Limitazione | 32.000 eventi al secondo | Il limite viene misurato nell'arco di un minuto.
| Conservazione dei dati | [30 - 730 giorni](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)  | Questa risorsa è destinata a [Ricerca](../articles/azure-monitor/app/diagnostic-search.md), [Analisi](../articles/azure-monitor/app/analytics.md) e [Esplora metriche](../articles/azure-monitor/app/metrics-explorer.md).
| Conservazione dei risultati dettagliati di [test di disponibilità in più passi](../articles/azure-monitor/app/availability-multistep.md) | 90 giorni | Questa risorsa fornisce risultati dettagliati per ogni passaggio.
| Dimensione massima dell'elemento di telemetriaMaximum telemetry item size | 64 KB |
| Numero massimo di elementi di telemetria per batchMaximum telemetry items per batch | 64 K |
| Lunghezza nomi di proprietà e metriche | 150 | Vedere gli [schemi per tipo](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Lunghezza stringa valore di proprietà | 8,192  | Vedere gli [schemi per tipo](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Lunghezza messaggio di traccia e di eccezione | 32.768  | Vedere gli [schemi per tipo](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Numero di [test di disponibilità](../articles/azure-monitor/app/monitor-web-app-availability.md) per app | 100 |
| Conservazione dati [profiler](../articles/azure-monitor/app/profiler.md) | 5 giorni |
| Dati [profiler](../articles/azure-monitor/app/profiler.md) inviati al giorno | 10 GB |

Per altre informazioni, vedere [Informazioni su prezzi e quote in Application Insights](../articles/azure-monitor/app/pricing.md).