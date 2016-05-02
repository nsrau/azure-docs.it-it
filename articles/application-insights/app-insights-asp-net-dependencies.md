<properties 
	pageTitle="Rilevamento della dipendenza in Application Insights" 
	description="Analizzare l'uso, la disponibilità e le prestazioni dell'applicazione locale o Web di Microsoft Azure con Application Insights." 
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
	ms.date="04/13/2016" 
	ms.author="awills"/>


# Impostare Application Insights: tenere traccia delle dipendenze


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



Una *dipendenza* è un componente esterno chiamato dall'app. In genere è un servizio chiamato con il protocollo HTTP, oppure un database o un file system. In Application Insights di Visual Studio, è possibile vedere facilmente per quanto tempo l'applicazione attende le dipendenze e la frequenza con la quale una chiamata alle dipendenze non riesce.

![grafici di esempio](./media/app-insights-asp-net-dependencies/10-intro.png)

Il monitoraggio predefinito delle dipendenze attualmente segnala chiamate ai seguenti tipi di dipendenze:

* ASP.NET
 * Database SQL
 * Servizi Web ASP.NET e WCF che usano i binding basati su HTTP
 * Chiamate HTTP locali o remote
 * Azure DocumentDB, tabelle, archivio BLOB e coda
* Java
 * Chiamate a un database tramite un driver[JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), ad esempio MySQL, SQL Server, PostgreSQL o SQLite.
* JavaScript nelle pagine Web: l'[SDK della pagina Web](app-insights-javascript.md) registra automaticamente le chiamate Ajax come dipendenze.

È possibile scrivere chiamate SDK per monitorare altre dipendenze usando l'[API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


## Configurazione del monitoraggio delle dipendenze

È necessaria una sottoscrizione di [Microsoft Azure](http://azure.com).

### Se l'app è in esecuzione nel server IIS

Se l'app Web viene eseguita in .NET 4.6 o versione successiva, è possibile [installare Application Insights SDK](app-insights-asp-net.md) nell'app o installare Application Insights Status Monitor. Non sono necessari entrambi.

Altrimenti, installare Application Insights Status Monitor sul server:

1. Nel server Web IIS accedere con le credenziali di amministratore.
2. Scaricare e installare il [programma di installazione di Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).
4. Nell'Installazione guidata accedere a Microsoft Azure.

    ![Accedere ad Azure con le credenziali dell'account Microsoft](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *Errori di connessione Vedere [Risoluzione dei problemi](#troubleshooting).*

5. Selezionare l'applicazione Web installata o il sito Web da monitorare, quindi configurare la risorsa in cui visualizzare i risultati nel portale Application Insights.

    ![Scegliere un'applicazione e una risorsa.](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    In genere, si sceglie di configurare una nuova risorsa e un nuovo [gruppo di risorse][roles].

    In alternativa, è possibile usare una risorsa esistente se sono già stati configurati [test Web][availability] per il sito o il [monitoraggio del client Web][client].

6. Riavviare IIS.

    ![Scegliere Riavvia nella parte superiore della finestra di dialogo.](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    Il servizio Web verrà interrotto per un breve periodo di tempo.

6. Si noti che ApplicationInsights.config è stato inserito tra le app Web da monitorare.

    ![Trovare il file con estensione config insieme ai file di codice dell'app Web.](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   Sono inoltre state apportate alcune modifiche al file web.config.

#### Configurare o riconfigurare in un secondo momento

Dopo aver completato la procedura guidata, è possibile riconfigurare l'agente in qualsiasi momento. È inoltre possibile usare questa stessa procedura se l'agente è stato installato ma la configurazione iniziale presenta alcuni problemi.

![Fare clic sull'icona di Application Insights sulla barra delle applicazioni](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### Se l'applicazione viene eseguita come un'app Web di Azure

Nel pannello di controllo dell'app Web di Azure aggiungere l'estensione di Application Insights.

![Nell'app Web scegliere Impostazioni, Estensioni, Aggiungi, Application Insights](./media/app-insights-asp-net-dependencies/05-extend.png)


### è un progetto di Servizi cloud di Azure

[Aggiungere gli script ai ruoli Web e di lavoro](app-insights-cloudservices.md#dependencies) oppure [installare .NET Framework 4.6 o versione successiva](../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="diagnosis"></a>Diagnosi dei problemi relativi alle prestazioni delle dipendenze

Per valutare le prestazioni delle richieste al server:

![Nella pagina Panoramica dell'applicazione in Application Insights fare clic sul riquadro Prestazioni](./media/app-insights-asp-net-dependencies/01-performance.png)

Scorrere fino a esaminare la griglia di richieste:

![Elenco di richieste con conteggi e medie](./media/app-insights-asp-net-dependencies/02-reqs.png)

Quella superiore impiega molto tempo. È necessario indagare per scoprire in che modo viene impiegato il tempo.

Fare clic su tale riga per visualizzare gli eventi di richiesta singola:


![Elenco di occorrenze di richiesta](./media/app-insights-asp-net-dependencies/03-instances.png)

Fare clic su qualsiasi istanza con esecuzione prolungata per esaminarla ulteriormente.

> [AZURE.NOTE] Scorrere verso il basso per scegliere un'istanza. Una latenza nella pipeline potrebbe indicare che i dati per le istanze superiore sono incompleti.

Scorrere in basso fino alle chiamate alle dipendenze remote correlate a questa richiesta:

![Trovare le chiamate alle dipendenze remote, identificare una durata insolita](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Sembra che gran parte del tempo dedicato a questa richiesta sia stato impiegato in una chiamata a un servizio locale.

Selezionare la riga per ottenere altre informazioni:


![Fare clic su tale dipendenza remota per identificare il motivo del problema](./media/app-insights-asp-net-dependencies/05-detail.png)

Il dettaglio include informazioni sufficienti a diagnosticare il problema.



## Errori

Se sono presenti richieste non riuscite, fare clic sul grafico.

![Fare clic sul grafico delle richieste non riuscite](./media/app-insights-asp-net-dependencies/06-fail.png)

Fare clic in un tipo di richiesta e un'istanza di richiesta per trovare una chiamata non riuscita a una dipendenza remota.


![Fare clic su un tipo di richiesta, fare clic sull'istanza per ottenere una vista diversa della stessa istanza, fare clic su di essa per ottenere informazioni dettagliate sull'eccezione.](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## Rilevamento personalizzato delle dipendenze

Il modulo standard per il rilevamento delle dipendenze rileva automaticamente le dipendenze esterne, ad esempio database e API REST. È tuttavia possibile che si vogliano gestire allo stesso modo altri componenti.

È possibile scrivere il codice che invia informazioni sulle dipendenze, mediante la stessa [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency) usata dai moduli standard.

Ad esempio, se si compila il codice con un assembly non scritto personalmente, sarà possibile misurare il tempo necessario per tutte le chiamate all'assembly, per individuare il contributo dell'assembly ai tempi di risposta. Per visualizzare i dati nei grafici relativi alle dipendenze in Application Insights, inviarli mediante `TrackDependency`.

```C#

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

## Risoluzione dei problemi

*Il flag di operazione riuscita della dipendenza visualizza sempre true o false*.

* Eseguire l'aggiornamento alla versione più recente dell'SDK. Se la versione di .NET è precedente alla 4.6, installare [Status Monitor](app-insights-monitor-performance-live-website-now.md).

## Passaggi successivi

- [Eccezioni](app-insights-asp-net-exception-mvc.md#selector1)
- [Dati utente & pagina](app-insights-asp-net-client.md#selector1)
- [Disponibilità](app-insights-monitor-web-app-availability.md#selector1)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md

 

<!---HONumber=AcomDC_0420_2016-->