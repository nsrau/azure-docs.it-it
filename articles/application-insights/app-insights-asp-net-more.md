<properties 
	pageTitle="Ottenere il massimo da Application Insights" 
	description="Dopo l'introduzione a Application Insights, ecco un riepilogo delle funzionalità che è possibile esplorare." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2016" 
	ms.author="awills"/>

# Altri dati di telemetria da Application Insights

Di seguito è riportato un riepilogo delle funzionalità che l'utente potrebbe non avere provato in [Visual Studio Application Insights](app-insights-overview.md). Si presuppone che l'utente abbia già consultato l'[introduzione](app-insights-asp-net.md). Application Insights consente di monitorare la disponibilità, le prestazioni e l'uso dell'applicazione Web. Con il feedback ottenuto sulle prestazioni e sull'efficacia dell'app in circostanze normali, è possibile prendere decisioni informate sulla direzione della progettazione in ogni ciclo di vita di sviluppo.

## Altri dati di telemetria

||
|---|---
|[**Test di disponibilità**](app-insights-monitor-web-app-availability.md)<br/>Consente di inviare le richieste HTTP dell'app Web a intervalli regolari da tutto il mondo. Se la risposta è lenta o non affidabile, l'utente verrà avvisato.| 
|[**Chiamate di dipendenza**](app-insights-asp-net-dependencies.md)<br/>Consente di monitorare le query SQL e le chiamate a REST o ad altre risorse.|
|[**Eccezioni**](app-insights-asp-net-exceptions.md)<br/>Consente di contare le eccezioni gestite e non gestite, eseguire l'analisi dello stack e fare clic per visualizzare il codice.|
|[**Pagine Web**](app-insights-javascript.md)<br/>Consente di monitorare l'uso delle pagine, le prestazioni e le chaimate AJAX instrumentando le pagine Web.
|**Prestazioni dell'host: [Diagnostica di Azure](app-insights-azure-diagnostics.md), [Contatori delle prestazioni di Windows](app-insights-web-monitor-performance.md)**<br/>Consente di visualizzare l'uso della CPU e altre metriche sul contesto. |![](./media/app-insights-asp-net-more/04.png)
|[**API SDK: telemetria personalizzata**](app-insights-api-custom-events-metrics.md)<br/>Consente di inviare gli eventi e le metriche personali per una visualizzazione più dettagliata dell'uso e delle prestazioni dell'app, sia nel codice del server sia nel codice del client.|
|[**Integrazione del log**](app-insights-asp-net-trace-logs.md)<br/>Se si usa un framework di registrazione, ad esempio Log4Net, NLog o System.Diagnostics.Trace, è presente un adattatore che invia le analisi ad Application Insights insieme ad altri dati di telemetria.|
|[**TelemetryProcessors**](app-insights-api-filtering-sampling.md)<br/>Consente di filtrare, modificare o aumentare i dati di telemetria inviati dalll'SDK nell'app. |


## Analisi e presentazione efficienti

||
|---|---
|[**Ricerca diagnostica per i dati dell'istanza**](app-insights-visual-studio.md)<br/>Consente di cercare e filtrare eventi quali richieste, eccezioni, chiamate di dipendenza, analisi dei log e visualizzazioni di pagina. In Visual Studio, passare al codice dall'analisi dello stack.|![Visual Studio](./media/app-insights-asp-net/61.png)
|[**Esplora metriche per i dati aggregati**](app-insights-metrics-explorer.md)<br/>Consente di esplorare, filtrare e segmentare i dati aggregati, ad esempio la frequenza delle richieste, gli errori e le eccezioni, i tempi di risposta e i tempi di caricamento della pagina.|![Visual Studio](./media/app-insights-asp-net-more/060.png)
|[**Dashboard**](app-insights-dashboards.md#dashboards)<br/>Consente di combinare dati da più risorse e di condividerli con altri utenti. Ideale per le applicazioni multi-componente e per la visualizzazione continua negli spazi del team. |![Esempio di dashboard](./media/app-insights-asp-net/62.png)
|[**Flusso di metriche live**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>Quando si distribuisce una nuova compilazione,è possibile controllare questi indicatori di prestazioni in tempo quasi reale per verificare che tutto funzioni come previsto.|![Esempio di analisi](./media/app-insights-asp-net-more/050.png)
|[**Analisi**](app-insights-analytics.md)<br/>Consente di rispondere a domande approfondite sull'uso e sulle prestazioni dell'applicazione tramite l'efficiente linguaggio delle query.|![Esempio di analisi](./media/app-insights-asp-net-more/010.png)
|[**Avvisi automatici e manuali**](app-insights-alerts.md)<br/>Gli avvisi automatici si adattano ai modelli di telemetria dell'applicazione e si attivano quando si verificano eventi diversi dal modello normale. È anche possibile impostare avvisi su livelli particolari delle metriche standard o personalizzate.|![Esempio di avviso](./media/app-insights-asp-net-more/020.png)

## Gestione dati

|||
|---|---|
|[**Esportazione continua**](app-insights-export-telemetry.md)<br/>Consente di copiare tutti i dati di telemetria nell'archiviazione in modo da poterli analizzare nel modo scelto.|
|**API di accesso ai dati**<br/>Presto disponibile.|
|[**Campionamento**](app-insights-sampling.md)<br/>Riduce la velocità dei dati e permette di rimanere entro il limite del piano tariffario.|![Riquadro del campionamento](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0713_2016-->