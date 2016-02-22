<properties 
	pageTitle="Diagnosticare i problemi delle dipendenze in Application Insights" 
	description="Trovare gli errori i e rallentamenti delle prestazioni causati dalle dipendenze" 
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
	ms.date="02/09/2016" 
	ms.author="awills"/>
 
# Diagnosticare i problemi delle dipendenze in Application Insights


Una *dipendenza* è un componente esterno chiamato dall'app. In genere è un servizio chiamato con il protocollo HTTP, oppure un database o un file system. Nello script della pagina Web, può essere una chiamata AJAX al server. In Application Insights di Visual Studio, è possibile vedere facilmente per quanto tempo l'applicazione attende le dipendenze e la frequenza con la quale una chiamata alle dipendenze non riesce.

## Dove è possibile usarlo

Il monitoraggio predefinito delle dipendenze è attualmente disponibile per:

* App Web e servizi di ASP.NET in esecuzione su un server IIS oppure su Azure
* [Applicazioni web Java](app-insights-java-agent.md)
* [Pagina Web](https://azure.microsoft.com/blog/ajax-collection-in-application-insights/)

Per altri tipi, ad esempio applicazioni per dispositivi, è possibile scrivere il proprio codice di monitoraggio usando l'[API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).

Il monitoraggio predefinito delle dipendenze attualmente segnala chiamate ai seguenti tipi di dipendenze:

* ASP.NET
 * Database SQL
 * Servizi Web ASP.NET e WCF che usano i binding basati su HTTP
 * Chiamate HTTP locali o remote
 * Azure DocumentDB, tabelle, archivio BLOB e coda
* Java
 * Chiamate a un database tramite un driver[JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), ad esempio MySQL, SQL Server, PostgreSQL o SQLite.
* Pagina Web
 * [Chiamate AJAX](app-insights-javascript.md)

Anche in questo caso, è possibile scrivere le proprie chiamate SDK per monitorare altre dipendenze.

## Configurazione del monitoraggio delle dipendenze

Installare l'agente appropriato per il server host.

Piattaforma | Installa
---|---
Server IIS | [Monitoraggio stato](app-insights-monitor-performance-live-website-now.md)
App Web di Azure | [Informazioni sull'estensione dell'applicazione](../azure-portal/insights-perf-analytics.md)
Server web Java | [Applicazioni web Java](app-insights-java-agent.md)
Pagina Web | [Monitoraggio JavaScript](app-insights-javascript.md) (nessuna impostazione aggiuntiva oltre al monitoraggio della pagina Web)

Il monitoraggio dello stato per i server IIS non necessita che si ricompili il progetto di origine con il SDK di Application Insights.

## <a name="diagnosis"></a> Diagnosi dei problemi relativi alle prestazioni delle dipendenze nel server Web

Per valutare le prestazioni delle richieste al server:

![Nella pagina Panoramica dell'applicazione in Application Insights fare clic sul riquadro Prestazioni](./media/app-insights-dependencies/01-performance.png)

Scorrere fino a esaminare la griglia di richieste:

![Elenco di richieste con conteggi e medie](./media/app-insights-dependencies/02-reqs.png)

Quella superiore impiega molto tempo. È necessario indagare per scoprire in che modo viene impiegato il tempo.

Fare clic su tale riga per visualizzare gli eventi di richiesta singola:


![Elenco di occorrenze di richiesta](./media/app-insights-dependencies/03-instances.png)

Fare clic su qualsiasi istanza con esecuzione prolungata per esaminarla ulteriormente.

> [AZURE.NOTE] Scorrere verso il basso per scegliere un'istanza. Una latenza nella pipeline potrebbe indicare che i dati per le istanze superiore sono incompleti.

Scorrere in basso fino alle chiamate alle dipendenze remote correlate a questa richiesta:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/app-insights-dependencies/04-dependencies.png)

Sembra che gran parte del tempo dedicato a questa richiesta sia stato impiegato in una chiamata a un servizio locale.

Selezionare la riga per ottenere altre informazioni:


![Fare clic su tale dipendenza remota per identificare il motivo del problema](./media/app-insights-dependencies/05-detail.png)

Il dettaglio include informazioni sufficienti a diagnosticare il problema.



## Errori

Se sono presenti richieste non riuscite, fare clic sul grafico.

![Fare clic sul grafico delle richieste non riuscite](./media/app-insights-dependencies/06-fail.png)

Fare clic in un tipo di richiesta e un'istanza di richiesta per trovare una chiamata non riuscita a una dipendenza remota.


![Fare clic su un tipo di richiesta, fare clic sull'istanza per ottenere una vista diversa della stessa istanza, fare clic su di essa per ottenere informazioni dettagliate sull'eccezione.](./media/app-insights-dependencies/07-faildetail.png)


## Rilevamento personalizzato delle dipendenze

Il modulo standard per il rilevamento delle dipendenze rileva automaticamente le dipendenze esterne, ad esempio database e API REST. È tuttavia possibile che si vogliano gestire allo stesso modo altri componenti.

È possibile scrivere il codice che invia informazioni sulle dipendenze, mediante la stessa [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency) usata dai moduli standard.

Ad esempio, se si compila il codice con un assembly non scritto personalmente, sarà possibile misurare il tempo necessario per tutte le chiamate all'assembly, per individuare il contributo dell'assembly ai tempi di risposta. Per visualizzare i dati nei grafici relativi alle dipendenze in Application Insights, inviarli mediante `TrackDependency`.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Per disattivare il modulo standard per il rilevamento delle dipendenze, rimuovere il riferimento a DependencyTrackingTelemetryModule in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).


## Ajax

Vedere [Pagina Web](app-insights-javascript.md)


 

<!---HONumber=AcomDC_0211_2016-->