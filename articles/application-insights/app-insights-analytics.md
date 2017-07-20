---
title: 'Analisi: il potente strumento di ricerca di Application Insights | Microsoft Docs'
description: 'Panoramica di Analytics: lo strumento di ricerca diagnostica avanzato incluso in Application Insights. '
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: 969d4f5c76c0f91c13622cb91d137c7be8007505
ms.contentlocale: it-it
ms.lasthandoff: 05/19/2017


---
# <a name="analytics-in-application-insights"></a>Analytics in Application Insights
L'[analisi](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analytics. 

* **[Guardare il video introduttivo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Eseguire la versione di test di Analisi sui dati simulati](https://analytics.applicationinsights.io/demo)** se l'app non invia ancora i dati ad Application Insights.
* Il **[foglio informativo sugli utenti SQL](https://aka.ms/sql-analytics)** traduce i linguaggi più comuni.
* **[Informazioni di riferimento sul linguaggio](app-insights-analytics-reference.md)** Informazioni su come usare tutte le funzionalità avanzate del linguaggio di query Analytics.


## <a name="queries-in-analytics"></a>Query in Analytics
Una query tipica è costituita da una tabella di *origine* seguita da una serie di *operatori* separati da `|`. 

Ad esempio, è possibile sapere a che ora del giorno gli abitanti di Hyderabad sperimentano l'app Web. Inoltre, è possibile sapere quali codici di risultato sono restituiti per le richieste HTTP. 

```AIQL

    requests      // Table of events that log HTTP requests.
    | where timestamp > ago(7d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Si contano indirizzi IP client distinti, raggruppandoli in base all'ora del giorno negli ultimi 7 giorni. 

> [!NOTE]
> Per ottenere risultati oltre le 24 ore precedenti, includere "timestamp" in modo esplicito nella query o usare il menu a discesa per l'intervallo di tempo.
>

Verranno visualizzati i risultati in una presentazione con grafico a barre, con lo stack dei risultati ottenuti da codici di risposta diversi:

![Scegliere il grafico a barre, gli assi X e Y, quindi la segmentazione](./media/app-insights-analytics/020.png)

L'app sembra riscuotere molto successo a Hyderabad all'ora di pranzo e prima di andare a dormire. (Si dovrebbe anche esaminare quei 500 codici).

Sono inoltre disponibili operazioni statistiche avanzate:

![Risultati della query statistica](./media/app-insights-analytics/025.png)

Il linguaggio include diverse funzionalità utili, è possibile:


* [Filtrare](app-insights-analytics-reference.md#where-operator) i dati di telemetria app non elaborati in base a qualsiasi campo, comprese proprietà personalizzate e metriche.
* [Unire](app-insights-analytics-reference.md#join-operator) più tabelle: correlare le richieste a visualizzazioni di pagina, chiamate a dipendenze, eccezioni e tracce di log.
* [Aggregazioni](app-insights-analytics-reference.md#aggregations)statistiche avanzate.
* Altrettanto efficace come SQL, ma molto più semplice per le query complesse: anziché nidificare le istruzioni, i dati vengono inviati tramite pipe da un'operazione semplice alla successiva.
* Visualizzazioni immediate e avanzate.
* [Aggiungere grafici ai dashboard di Azure](app-insights-analytics-using.md#pin-to-dashboard).
* [Esportare le query in Power BI](app-insights-analytics-using.md#export-to-power-bi).
* Esiste un'[API REST](https://dev.applicationinsights.io/) che è possibile usare per eseguire query in modo programmatico, ad esempio da PowerShell.


## <a name="connect-to-your-application-insights-data"></a>Connettersi ai dati di Application Insights
Aprire Analisi dal [pannello Panoramica](app-insights-dashboards.md) dell'app in Application Insights: 

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-insights-analytics/001.png)


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]



## <a name="query-examples"></a>Esempi di query

Provare a eseguire queste procedure dettagliate per illustrare le potenzialità di utilizzo di Analytics:

 *    [Diagnostica automatica di picchi e salti procedurali nella durata delle richieste](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Automatic%20diagnostics%20of%20sudden%20spikes%20or%20step%20jumps%20in%20requests%20duration&shared=true)
 *    [Analisi delle riduzioni delle prestazioni con l'analisi delle serie temporali](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20performance%20degradations%20with%20time%20series%20analysis&shared=true)
 *    [Analisi degli errori delle applicazioni con autocluster e diffpatterns](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20application%20failures%20with%20autocluster%20and%20diffpatterns&shared=true)
 *    [Rilevamenti forma avanzati con l'analisi delle serie temporali](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Advanced%20shape%20detection%20with%20time%20series%20analysis&shared=true)
 *    [Uso di operazioni della finestra temporale scorrevole per analizzare l'utilizzo dell'applicazione (come MAU/DAU in sequenza e così via)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Using%20sliding%20window%20calculations%20to%20analyze%20usage%20metrics:%20rolling%20MAU~2FDAU%20and%20cohorts&shared=true)
 *    [Rilevamento di interruzioni del servizio in base all'analisi dei registri di debug](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Detection%20of%20service%20disruptions%20based%20on%20regression%20analysis%20of%20trace%20logs&shared=true) e post di blog corrispondente [qui](https://maximshklar.wordpress.com/2017/02/16/finding-trends-in-traces-with-smart-data-analytics).
 *    [Profilatura delle prestazioni delle applicazioni tramite log di debug semplici](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Profiling%20applications'%20performance%20with%20simple%20debug%20logs&shared=true) e post di blog corrispondente [qui](https://yossiattasblog.wordpress.com/2017/03/13/first-blog-post/)
 *    [Misurazione della durata per ogni passaggio nel flusso di codice tramite log di debug semplici](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Measuring%20the%20duration%20of%20each%20step%20in%20your%20code%20flow%20using%20simple%20debug%20logs&shared=true) e post di blog corrispondente [qui](https://yossiattasblog.wordpress.com/2017/03/14/measuring-the-duration-of-each-step-in-your-code-flow-using-simple-debug-logs/)
 *    [Analisi della concorrenza tramite log di debug semplici](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Analyzing%20concurrency%20with%20simple%20debug%20logs&shared=true) e post di blog corrispondente [qui](https://yossiattasblog.wordpress.com/2017/03/23/analyzing-concurrency-using-simple-debug-logs/)



## <a name="next-steps"></a>Passaggi successivi
* È consigliabile iniziare con una [panoramica sul linguaggio](app-insights-analytics-tour.md). 
* Altre informazioni sull'[uso di Analytics](app-insights-analytics-using.md). 
* [Informazioni di riferimento sul linguaggio](app-insights-analytics-reference.md). 
