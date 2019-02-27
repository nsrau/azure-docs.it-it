---
title: 'Analytics: il potente strumento di ricerca e query di Azure Application Insights | Microsoft Docs'
description: 'Panoramica di Analytics: lo strumento di ricerca diagnostica avanzato incluso in Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: mbullwin
ms.openlocfilehash: 4c3ecdd01106cc8d305764206bc75535fa4dac3a
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268601"
---
# <a name="analytics-in-application-insights"></a>Analytics in Application Insights
Analytics è l'efficace strumento di ricerca e query incluso in [Application Insights](app-insights-overview.md). Analytics è uno strumento Web, pertanto non occorre installarlo.
Se Application Insights è già stato configurato per una delle app, è possibile analizzare i dati dell'app aprendo Analytics dal [pannello Panoramica](app-insights-dashboards.md) dell'app.

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/analytics/001.png)

È anche possibile usare l'[ambiente di prova di Analytics](https://go.microsoft.com/fwlink/?linkid=859557), ovvero un ambiente dimostrativo gratuito con una grande quantità di dati di esempio.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="relation-to-azure-monitor-logs"></a>Relazione con i log di Monitoraggio di Azure
Analytics di Application Insights si basa su [Esplora dati di Azure](/azure/data-explorer) come i log di Monitoraggio di Azure e usa anche il [linguaggio di query Kusto](/azure/kusto/query). Usa lo stesso [portale di analisi dei log](../log-query/get-started-portal.md) usato dai log di Monitoraggio di Azure, sebbene i dati vengano archiviati in una partizione separata.

Non è possibile accedere direttamente ai dati in un'area di lavoro di Log Analytics da Analytics di Application Insights né ai dati dell'applicazione da Log Analytics. Per eseguire query contemporaneamente su entrambi i set di dati, scrivere una [query in Log Analytics](../log-query/log-query-overview.md) e quindi usare l'[espressione app()](../log-query/app-expression.md) per accedere ai dati dell'applicazione.


## <a name="query-data-in-analytics"></a>Eseguire query sui dati in Analytics
Una query tipica inizia con un nome di tabella seguito da una serie di *operatori* separati da `|`.
Ad esempio, si supponga di voler scoprire il numero di richieste ricevute dall'app da paesi diversi nelle ultime 3 ore:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Si inizia con il nome della tabella *requests* e si aggiungono elementi introdotti da barra verticale in base alle esigenze.  Viene prima di tutto definito un filtro temporale per visualizzare solo i record delle ultime 3 ore.
Si conteggia quindi il numero di record per ogni paese (questi dati sono disponibili nella colonna *client_CountryOrRegion*). Infine, si esegue il rendering dei risultati in un grafico a torta.
<br>

![Risultati query](./media/analytics/030.png)

Il linguaggio include diverse funzionalità utili, è possibile:

* [Filtrare](/azure/kusto/query/whereoperator) i dati di telemetria app non elaborati in base a qualsiasi campo, comprese proprietà personalizzate e metriche.
* [Unire](/azure/kusto/query/joinoperator) più tabelle: correlare le richieste a visualizzazioni di pagina, chiamate a dipendenze, eccezioni e tracce di log.
* [Aggregazioni](/azure/kusto/query/summarizeoperator)statistiche avanzate.
* Visualizzazioni immediate e avanzate.
* Usare un'[API REST](https://dev.applicationinsights.io/) per eseguire query in modo programmatico, ad esempio da PowerShell.

Nelle [informazioni di riferimento al linguaggio complete](https://go.microsoft.com/fwlink/?linkid=856079) sono illustrati in dettaglio tutti i comandi supportati e i dati vengono aggiornati regolarmente.

## <a name="next-steps"></a>Passaggi successivi
* Introduzione al [portale di Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* Introduzione alla [scrittura di query](https://go.microsoft.com/fwlink/?linkid=856078)
* Esaminare la [scheda di riferimento rapido per utenti SQL](https://aka.ms/sql-analytics) per le corrispondenze delle istruzioni per le operazioni più comuni.
* Testare Analytics nell'[ambiente di prova](https://analytics.applicationinsights.io/demo) se l'app non invia ancora i dati ad Application Insights.
* Guardare il [video introduttivo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).
