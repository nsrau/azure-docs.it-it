---
title: Sfruttare al meglio le funzionalità di Azure Application Insights | Documentazione Microsoft
description: Dopo l'introduzione a Application Insights, ecco un riepilogo delle funzionalità che è possibile esplorare.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 7e62efb0b64b0b882b36126af4a1f9d520d92475
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230647"
---
# <a name="more-telemetry-from-application-insights"></a>Altri dati di telemetria da Application Insights
Dopo aver [aggiunto Application Insights al codice ASP.NET](../../azure-monitor/app/asp-net.md), è possibile eseguire alcune operazioni per ottenere altri dati di telemetria. 

| Azione | Risultato finale|
|---|---|
|(Server IIS) [Installare Status Monitor](https://go.microsoft.com/fwlink/?LinkId=506648) in ogni computer server.<br/>(App Web di Azure) Nel pannello di controllo di Azure per l'app Web aprire il pannello di Application Insights.| [**Contatori delle prestazioni**](../../azure-monitor/app/performance-counters.md)<br/>[**Eccezioni**](asp-net-exceptions.md): analisi dello stack dettagliate<br/>[**Dipendenze**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Aggiungere il frammento di codice JavaScript alle pagine web](../../azure-monitor/app/javascript.md)|[Prestazioni delle pagine](../../azure-monitor/app/usage-overview.md), eccezioni del browser, prestazioni AJAX. Telemetria personalizzata sul lato client.|
|[Creare test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)|Ricevere avvisi se il sito diventa non disponibile|
|[Assicurarsi che buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) sia generato da MSBuild|[Creare annotazioni nei grafici di metriche](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Scrivere eventi e metriche personalizzati](../../azure-monitor/app/api-custom-events-metrics.md)|Conteggiare eventi aziendali e metriche, tenere traccia dell'uso dettagliato e altro ancora.|
|[Profilatura del sito live](https://aka.ms/AIProfilerPreview)|Intervalli di funzione dettagliati dall'app Web live|






