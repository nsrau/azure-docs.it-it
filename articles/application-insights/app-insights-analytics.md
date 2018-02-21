---
title: 'Analytics: il potente strumento di ricerca e query di Azure Application Insights | Microsoft Docs'
description: 'Panoramica di Analytics: lo strumento di ricerca diagnostica avanzato incluso in Application Insights. '
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 5f324051a2eeedd35a22f77c771793af9c90c434
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="analytics-in-application-insights"></a>Analytics in Application Insights
Analytics è l'efficace strumento di ricerca e query incluso in [Application Insights](app-insights-overview.md). Analytics è uno strumento Web, pertanto non occorre installarlo. Se Application Insights è già stato configurato per una delle app, è possibile analizzare i dati dell'app aprendo Analytics dal [pannello Panoramica](app-insights-dashboards.md) dell'app.

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-insights-analytics/001.png)

È anche possibile usare l'[ambiente di prova di Analytics](https://go.microsoft.com/fwlink/?linkid=859557), ovvero un ambiente dimostrativo gratuito con una grande quantità di dati di esempio.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

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

![Risultati query](./media/app-insights-analytics/030.png)

Il linguaggio include diverse funzionalità utili, è possibile:

* [Filtrare](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) i dati di telemetria app non elaborati in base a qualsiasi campo, comprese proprietà personalizzate e metriche.
* [Unire](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/join-operator) più tabelle: correlare le richieste a visualizzazioni di pagina, chiamate a dipendenze, eccezioni e tracce di log.
* [Aggregazioni](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions)statistiche avanzate.
* Visualizzazioni immediate e avanzate.
* Usare un'[API REST](https://dev.applicationinsights.io/) per eseguire query in modo programmatico, ad esempio da PowerShell.

Nelle [informazioni di riferimento al linguaggio complete](https://go.microsoft.com/fwlink/?linkid=856079) sono illustrati in dettaglio tutti i comandi supportati e i dati vengono aggiornati regolarmente.

## <a name="next-steps"></a>Passaggi successivi
* Introduzione al [portale di Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* Introduzione alla [scrittura di query](https://go.microsoft.com/fwlink/?linkid=856078)
* Esaminare la [scheda di riferimento rapido per utenti SQL](https://aka.ms/sql-analytics) per le corrispondenze delle istruzioni per le operazioni più comuni.
* Testare Analytics nell'[ambiente di prova](https://analytics.applicationinsights.io/demo) se l'app non invia ancora i dati ad Application Insights.
* Guardare il [video introduttivo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).