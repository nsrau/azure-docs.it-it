---
title: Sfruttare al meglio le funzionalità di Azure Application Insights | Documentazione Microsoft
description: Dopo l'introduzione a Application Insights, ecco un riepilogo delle funzionalità che è possibile esplorare.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540027"
---
# <a name="more-telemetry-from-application-insights"></a>Altri dati di telemetria da Application Insights
Dopo aver [aggiunto Application Insights al codice ASP.NET](../../azure-monitor/app/asp-net.md), è possibile eseguire alcune operazioni per ottenere altri dati di telemetria. 

| Azione | Componenti|
|---|---|
|(Server IIS) [Installare Status Monitor](https://go.microsoft.com/fwlink/?LinkId=506648) in ogni computer server.<br/>(App Web di Azure) Nel pannello di controllo di Azure per l'app Web aprire il pannello di Application Insights.| [**Contatori delle prestazioni**](../../azure-monitor/app/performance-counters.md)<br/>[**Eccezioni**](asp-net-exceptions.md): analisi dello stack dettagliate<br/>[**Dipendenze**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Aggiungere il frammento di codice JavaScript alle pagine web](../../azure-monitor/app/javascript.md)|[Prestazioni delle pagine](../../azure-monitor/app/usage-overview.md), eccezioni del browser, prestazioni AJAX. Telemetria personalizzata sul lato client.|
|[Creare test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)|Ricevere avvisi se il sito diventa non disponibile|
|[Assicurarsi che buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) sia generato da MSBuild|[Creare annotazioni nei grafici di metriche](./annotations.md)
|[Scrivere eventi e metriche personalizzati](../../azure-monitor/app/api-custom-events-metrics.md)|Conteggiare eventi aziendali e metriche, tenere traccia dell'uso dettagliato e altro ancora.|
|[Profilatura del sito live](https://aka.ms/AIProfilerPreview)|Intervalli di funzione dettagliati dall'app Web live|
