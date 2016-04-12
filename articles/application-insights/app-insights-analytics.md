<properties 
	pageTitle="Analytics: lo strumento di ricerca avanzato incluso in Application Insights" 
	description="Panoramica di Analytics: lo strumento di ricerca diagnostica avanzato incluso in Application Insights." 
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
	ms.date="03/30/2016" 
	ms.author="awills"/>


# Analytics in Application Insights


[Analytics](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analisi.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

 
Una query tipica è costituita da una tabella *di origine* seguita da una serie di *operatori* separati da `|`.

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

![Scegliere il grafico a barre, gli assi X e Y, quindi la segmentazione](./media/app-insights-analytics/020.png)

L'app sembra riscuotere molto successo a Hyderabad all'ora di pranzo e prima di andare a dormire. (Si dovrebbe anche esaminare quei 500 codici).


Sono inoltre disponibili operazioni statistiche avanzate:

![](./media/app-insights-analytics/025.png)


Il linguaggio include diverse funzionalità utili, è possibile:

* [Filtrare](app-insights-analytics-queries.md) i dati di telemetria app non elaborati in base a qualsiasi campo, inserendo proprietà personalizzate e metriche.
* [Unire](app-insights-analytics-queries.md#join-operator) più tabelle: correlare le richieste a visualizzazioni di pagina, chiamate di dipendenza, eccezioni e tracce di log.
* [Aggregazioni](app-insights-analytics-aggregations.md) statistiche avanzate.
* Altrettanto efficace come SQL, ma molto più semplice per le query complesse: anziché nidificare le istruzioni, i dati vengono inviati tramite pipe da un'operazione semplice alla successiva.
* Visualizzazioni immediate e avanzate.



>[AZURE.NOTE] È consigliabile iniziare con una [panoramica sul linguaggio](app-insights-analytics-tour.md).




## Connettersi ai dati di Application Insights


Aprire Analytics dal [pannello Panoramica](app-insights-dashboards.md) dell'app in Application Insights:

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-insights-analytics/001.png)


## Limiti

Attualmente sono disponibili i risultati delle query per una settimana di dati.



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->