<properties
   pageTitle="Application Insights per Servizi cloud di Azure"
   description="Monitorare i ruoli Web e di lavoro in modo efficace con Application Insights"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="victormu"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="05/21/2015"
   ms.author="sdash"/>

# Application Insights per Servizi cloud di Azure


*Application Insights è disponibile in anteprima*

Le [app del servizio Cloud di Microsoft Azure](http://azure.microsoft.com/services/cloud-services/) possono essere monitorate da [Visual Studio Application Insights][start] in termini di disponibilità, prestazioni, errori e utilizzo.

<!-- For illustration purposes, we have added Application Insights to this [sample Azure cloud service](sample link). This code is available [here](git link), for you to follow along with the steps below. -->

## Creare una risorsa Application Insights per ogni ruolo

Una risorsa Application Insights rappresenta il punto in cui vengono analizzati e visualizzati i dati di telemetria.

1.  Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Scegliere l'app ASP.NET per il tipo di applicazione. 

    ![Fare clic su Nuovo, Application Insights](./media/app-insights-cloudservices/01-new.png)

2.  Eseguire una copia della chiave di strumentazione. Sarà necessaria per configurare l'SDK.

    ![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/app-insights-cloudservices/02-props.png)


È in genere preferibile creare una risorsa separata per i dati da ogni ruolo Web e di lavoro.

In alternativa, è possibile inviare dati da tutti i ruoli a una sola risorsa, ma impostare una [proprietà predefinita][apidefaults] in modo da potere filtrare o raggruppare i risultati di ogni ruolo.

## <a name="sdk"></a>Installare l'SDK in ogni progetto


1. In Visual Studio è possibile modificare i pacchetti NuGet del progetto di app desktop. ![Fare clic con il pulsante destro del mouse sul progetto e selezionare Gestisci pacchetti Nuget](./media/app-insights-cloudservices/03-nuget.png)

2. Installare Application Insights SDK per app Web.

    ![Selezionare **Online**, **Includi versione preliminare** e cercare "Application Insights"](./media/app-insights-cloudservices/04-ai-nuget.png)

    In alternativa, è possibile scegliere Application Insights SDK per app Web. In questo modo si fornisce la telemetria del contatore delle prestazioni predefinite.

3. Configurare l'SDK per inviare i dati alla risorsa Application Insights.

    Aprire `ApplicationInsights.config` e inserire la riga seguente:

    `<InstrumentationKey>` *chiave di strumentazione copiata* `</InstrumentationKey>`

    Usare la chiave di strumentazione copiata dalla risorsa Application Insights.

    In alternativa, è possibile [impostare la chiave in fase di esecuzione][apidynamicikey]. Ciò consente di controllare la telemetria da diversi ambienti (sviluppo, test, produzione) per diverse risorse.

## Eseguire l'app

Eseguire il progetto in modalità debug nel computer di sviluppo o pubblicarlo in Azure. Usarlo per alcuni minuti per generare i dati di telemetria.

## <a name="monitor"></a> Visualizzare gli eventi di telemetria

Tornare al [portale di Azure][portal] e passare alla risorsa Application Insights.


Cercare i dati nei grafici Panoramica. All'inizio si vedranno solo uno o due punti. Ad esempio:

![Fare clic per visualizzare altri dati](./media/app-insights-asp-net/12-first-perf.png)

Fare clic su qualsiasi grafico per visualizzare metriche più dettagliate. [Altre informazioni sulle metriche.][perf]

Ora pubblicare l'applicazione e osservare l'accumulo dei dati.


#### Dati non visualizzati

* Aprire il riquadro [Ricerca][diagnostic] per visualizzare i singoli eventi.
* Usare l'applicazione, aprendo pagine diverse in modo da generare alcuni dati di telemetria.
* Attendere alcuni secondi e fare clic su Aggiorna.
* Vedere [Risoluzione dei problemi][qna].


## Completare l'installazione

Per ottenere una visione completa a 360 gradi dell'applicazione, è necessario eseguire ancora alcune operazioni:


* [Aggiungere l'SDK per JavaScript alle pagine Web][client] per ottenere dati di telemetria basati su browser quali i conteggi delle visualizzazioni delle pagine, i tempi di caricamento delle pagina e le eccezioni di script e per consentire la scrittura dei dati di telemetria negli script delle pagine.
* Aggiungere il rilevamento delle dipendenze per diagnosticare i problemi causati da database o da altri componenti usati dall'app:
 * [Nell'app Web o nella macchina virtuale di Azure][azure]
 * [Nel server IIS locale][redfield]
* [Acquisire le tracce dei log][netlogs] dal framework di registrazione preferito
* [Tenere traccia di eventi personalizzati e metriche][api] nei client, nel server o in entrambi per altre informazioni sulle modalità di uso dell'applicazione.
* [Configurare i test Web][availability] in modo da assicurarsi che l'applicazione sia disponibile e reattiva.

#### Tenere traccia delle richieste per i ruoli di lavoro

È possibile acquisire le prestazioni delle chiamate ai ruoli di lavoro tenendone traccia nello stesso modo in cui avviene per le richieste HTTP. In Application Insights, il tipo di telemetria per le richieste misura un'unità di lavoro lato server denominata che può essere programmata e può avere indipendentemente esito negativo o positivo. Mentre le richieste HTTP vengono acquisite automaticamente dall'SDK, è possibile inserire il proprio codice per tenere traccia delle richieste ai ruoli di lavoro.

Di seguito è riportato un tipico ciclo di esecuzione per un ruolo di lavoro:

```C#

    while (true)
    {
      Stopwatch s1 = Stopwatch.StartNew();
      var startTime = DateTimeOffset.UtcNow;
      try
      {
        // ... get and process messages ...

        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable",
            startTime, s1.Elapsed, SUCCESS_CODE, true);
      }
      catch (Exception ex)
      {
        string err = ex.Message;
        if (ex.InnerException != null)
        {
           err += " Inner Exception: " + ex.InnerException.Message;
        }
        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable", 
            startTime, s1.Elapsed, FAILURE_CODE, false);
        telemetryClient.TrackException(ex);

        // Don't flood if we have a bug in queue process loop.
        System.Threading.Thread.Sleep(60 * 1000);
      }
    }
```




[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=58-->