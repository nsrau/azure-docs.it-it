---
title: 'Application Insights: Esempi di codice e procedure dettagliate'
description: Esempi che è possibile adattare alle proprie applicazioni.
services: application-insights
documentationcenter: windows
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: awills

---
# Application Insights: esempi di codice e procedure dettagliate
*Application Insights è disponibile in anteprima.*

Questo articolo illustra esempi di codice relativi all'uso di [Application Insights per Visual Studio](app-insights-overview.md).

## Laboratori SDK
[Laboratori SDK](https://www.myget.org/gallery/applicationinsights-sdk-labs)è una raccolta di pacchetti NuGet sperimentali che è possibile installare e disinstallare come componenti aggiuntive per il SDK di Application Insights. Provarli e inviare i commenti!

## Servizi Web
* [Aggiungere dati di telemetria a ruoli Web e di lavoro di Azure](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService).
* [App universale di Windows 8.1.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal/)

## Esportazione continua
Per analizzare i dati di telemetria con strumenti personalizzati, [esportarli](app-insights-export-telemetry.md) nell'archivio, in cui è possibile analizzarli ed elaborarli.

* [Esportare in Power BI](app-insights-export-power-bi.md) 
* [Analizzare i dati esportati con un ruolo di lavoro](app-insights-code-sample-export-telemetry-sql-database.md)
* [Eseguire l'esportazione in SQL usando l'analisi di flusso](app-insights-code-sample-export-sql-stream-analytics.md)
* [Abilitazione della telemetria per Microsoft Dynamics CRM](app-insights-sample-mscrm.md)

## Automatizzare le attività
* [Uso di PowerShell per l'invio dei dati del servizio Diagnostica di Azure ad Application Insights](app-insights-powershell.md)
* [Script per creare una nuova risorsa di Application Insights](app-insights-powershell-script-create-resource.md)
* [Uso di PowerShell per l'invio dei dati del servizio Diagnostica di Azure ad Application Insights](app-insights-powershell-azure-diagnostics.md)

<!---HONumber=AcomDC_0107_2016-->