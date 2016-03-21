<properties 
	pageTitle="Application Analytics - lo strumento di ricerca avanzato per Application Insights." 
	description="Panoramica di Application Analytics - lo strumento di ricerca avanzato per Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/07/2016" 
	ms.author="awills"/>




# Application Insights Analytics: Language Overview


[Application Insights Analytics](app-analytics.md) è un motore di query avanzato per i dati di telemetria di [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Application Insights (AIQL).

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
Una query AIQL tipica è composta da una tabella *di origine* seguita da una serie di *operatori* separati da `|`.

Ad esempio, è possibile sapere a che ora del giorno gli abitanti di Hyderabad sperimentano l'app Web. Inoltre, è possibile sapere quali codici di risultato sono restituiti per le richieste HTTP.

```AIQL

    requests 
    | where timestamp > ago(30d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Si contano indirizzi IP del client distinti, raggruppandoli in base all'ora del giorno negli ultimi 30 giorni.

Verranno visualizzati i risultati in una presentazione con grafico a barre, con lo stack dei risultati ottenuti da codici di risposta diversi:

![Scegliere il grafico a barre, gli assi X e Y, quindi la segmentazione](./media/app-analytics/020.png)

L'app sembra riscuotere molto successo a Hyderabad all'ora di pranzo e prima di andare a dormire. (Si dovrebbe anche esaminare quei 500 codici).

Il linguaggio include molte delle funzionalità di SQL e altro ancora. Analogamente a SQL, è possibile filtrare dati, raggruppare record, ordinare e unire tabelle. È anche possibile eseguire calcoli sui campi. A differenza di SQL, queste funzioni sono suddivise in diverse operazioni e, anziché annidare le query, si reindirizzano i dati da un'operazione alla successiva in modo molto intuitivo. Questo rende più semplice la scrittura di query piuttosto complesse.


>[AZURE.NOTE] È consigliabile iniziare con una [panoramica sul linguaggio](app-analytics-tour.md).


## Connettersi ai dati di Application Insights


Aprire Analytics dal [pannello Panoramica](app-insights-dashboards.md) dell'app in Application Insights:

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->