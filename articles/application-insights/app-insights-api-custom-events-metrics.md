---
title: API di Application Insights per metriche ed eventi personalizzati | Documentazione Microsoft
description: Inserire alcune righe di codice nell&quot;app desktop o per dispositivi, nella pagina Web o nel servizio per tenere traccia dell&quot;utilizzo e diagnosticare i problemi.
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7bd26ffdec185a1ebd71fb88383c2ae4cd6d504f
ms.openlocfilehash: 460b9e3cf2b4c7a85895c2db872344a1a5bc0ff9


---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API di Application Insights per metriche ed eventi personalizzati


Inserire alcune righe di codice nell'applicazione per scoprire come viene usato dagli utenti o per agevolare la diagnosi dei problemi. È possibile inviare i dati di telemetria dalle app desktop e per dispositivi, dai client Web e dai server Web. L'API di telemetria principale di [Azure Application Insights](app-insights-overview.md) consente di inviare metriche ed eventi personalizzati e le versioni personalizzate dei dati di telemetria standard. Questa API è la stessa usata per gli agenti di raccolta dati di Application Insights standard.

## <a name="api-summary"></a>Riepilogo dell'API
L'API è uniforme in tutte le piattaforme, a parte alcune variazioni di lieve entità.

| Metodo | Usato per |
| --- | --- |
| [`TrackPageView`](#page-views) |Pagine, schermate, pannelli o form. |
| [`TrackEvent`](#track-event) |Azioni dell'utente e altri eventi. Usato per tenere traccia del comportamento dell'utente o per monitorare le prestazioni. |
| [`TrackMetric`](#track-metric) |Misurazioni delle prestazioni, ad esempio la lunghezza della coda, non correlate a eventi specifici. |
| [`TrackException`](#track-exception) |Registrare le eccezioni per la diagnosi. Tenere traccia del punto in cui si verificano in relazione ad altri eventi ed esaminare le analisi dello stack. |
| [`TrackRequest`](#track-request) |Registrare la frequenza e la durata delle richieste del server per l'analisi delle prestazioni. |
| [`TrackTrace`](#track-trace) |Messaggi nei log di diagnostica. È anche possibile acquisire i log di terze parti. |
| [`TrackDependency`](#track-dependency) |Registrare la durata e la frequenza delle chiamate ai componenti esterni da cui dipende l'app. |

È possibile [associare proprietà e metriche](#properties) alla maggior parte di queste chiamate di telemetria.

## <a name="a-nameprepabefore-you-start"></a><a name="prep"></a>Prima di iniziare
Se queste operazioni non sono state ancora eseguite, completarle:

* Aggiungere Application Insights SDK al progetto:

  * [Progetto ASP.NET][greenbrown]
  * [Progetto Java][java]
  * [JavaScript in ogni pagina Web][client]   
* Nel dispositivo o nel codice del server Web includere:

    *C#:* `using Microsoft.ApplicationInsights;`

    *VB:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## <a name="construct-a-telemetryclient"></a>Costruire un oggetto TelemetryClient
Costruire un'istanza di TelemetryClient (tranne che in JavaScript nelle pagine Web):

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient è thread-safe.

È consigliabile usare un'istanza di `TelemetryClient` per ogni modulo dell'app. Ad esempio, è possibile che il servizio Web includa un `TelemetryClient` per segnalare le richieste HTTP in entrata e un altro oggetto in una classe middleware per segnalare gli eventi di logica di business. È possibile impostare proprietà quali `TelemetryClient.Context.User.Id` per tenere traccia degli utenti e delle sessioni o `TelemetryClient.Context.Device.Id` per identificare il computer. Queste informazioni sono associate a tutti gli eventi inviati dall'istanza.

## <a name="track-event"></a>Tenere traccia di un evento
In Application Insights un *evento personalizzato* è un punto dati che è possibile visualizzare sia in [Esplora metriche][metrics] come conteggio aggregato sia come singole occorrenze in [Ricerca diagnostica][diagnostic]. Non è correlato a MVC o ad altri "eventi" del framework.

Inserire chiamate TrackEvent nel codice per contare la frequenza d'uso di una particolare funzionalità, la frequenza di raggiungimento di obiettivi specifici o la frequenza di particolari tipi di errore.

Ad esempio, in un'app di gioco è possibile inviare un evento ogni volta che un utente vince il gioco:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*VB*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### <a name="view-your-events-in-the-azure-portal"></a>Visualizzare gli eventi nel portale di Azure
Per visualizzare un conteggio degli eventi, aprire un pannello [Esplora metriche](app-insights-metrics-explorer.md) , aggiungere un nuovo grafico e selezionare gli eventi.  

![](./media/app-insights-api-custom-events-metrics/01-custom.png)

Per confrontare il conteggio di eventi diversi, impostare il tipo di grafico a Griglia e raggruppare in base al nome dell’evento:

![](./media/app-insights-api-custom-events-metrics/07-grid.png)

Nella griglia, fare clic su un nome di evento per visualizzare le singole occorrenze di quell'evento.

![Eseguire il drill-through degli eventi](./media/app-insights-api-custom-events-metrics/03-instances.png)

Per visualizzare altri dettagli, fare clic su qualsiasi occorrenza.

Per concentrarsi sugli eventi specifici in ricerca o in Esplora metriche, impostare il filtro del pannello sui nomi degli eventi a cui si è interessati:

![Aprire i filtri, espandere il nome dell’evento e selezionare uno o più valori](./media/app-insights-api-custom-events-metrics/06-filter.png)

## <a name="track-metric"></a>Tenere traccia delle metriche
Usare TrackMetric per inviare le metriche che non sono associate a determinati eventi. Ad esempio, è possibile monitorare la lunghezza di una coda a intervalli regolari.

Le metriche vengono visualizzate come grafici statistici in Esplora metriche, ma a differenza degli eventi, non è possibile cercare singole occorrenze nella ricerca diagnostica.

I valori metrici devono essere >= 0 per essere visualizzati correttamente.

*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

Infatti, è possibile eseguire questa operazione in un thread in background:

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


Per visualizzare i risultati, aprire Esplora metriche e aggiungere un nuovo grafico. Impostarlo in modo che visualizzi le metriche.

![Aggiungere un nuovo grafico o selezionare un grafico e selezionare le metriche in Personalizzato](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

Esistono tuttavia alcuni [limiti sul numero di metriche](#limits) da usare.

## <a name="page-views"></a>Visualizzazioni pagina
In un'app per dispositivo o pagine Web i dati di telemetria delle visualizzazioni pagina vengono inviati per impostazione predefinita quando viene caricata ogni schermata o pagina. È tuttavia possibile modificare questa impostazione per tenere traccia delle visualizzazioni pagina in momenti diversi o aggiuntivi. Ad esempio, in un'app che visualizza schede o pannelli, è possibile tenere traccia di una "pagina" ogni volta che l'utente apre un nuovo pannello.

![Sezione Utilizzo nel pannello Panoramica](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

I dati relativi a utente e sessione vengono inviati come proprietà insieme alle visualizzazioni pagina, in modo che i grafici utente e sessione si attivino in presenza dei dati di telemetria delle visualizzazioni pagina.

#### <a name="custom-page-views"></a>Visualizzazioni pagina personalizzate
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


Se sono presenti alcune schede in pagine HTML diverse, è possibile specificare anche l'URL:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### <a name="timing-page-views"></a>Temporizzazione delle visualizzazioni delle pagine
Per impostazione predefinita, gli intervalli di tempo indicati come "Tempo di caricamento della visualizzazione pagina" sono misurati dal momento in cui il browser invia la richiesta al momento della chiamata all'evento di caricamento pagina del browser.

In alternativa, è possibile:

* Impostare una durata esplicita nella chiamata [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) .
  * `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* Usare le chiamate relative ai tempi di visualizzazione della pagina `startTrackPage` e `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

Il nome usato come primo parametro associa le chiamate di avvio e arresto. Il valore predefinito corrisponde al nome della pagina corrente.

I tempi di caricamento delle pagine visualizzati in Esplora metriche sono calcolati in base all'intervallo tra la chiamata di avvio e la chiamata di arresto. È possibile specificare l'intervallo effettivo calcolato desiderato.

## <a name="track-request"></a>Tenere traccia di una richiesta
Questo metodo viene usato dall'SDK del server per registrare le richieste HTTP.

È anche possibile chiamarlo manualmente se si vogliono simulare le richieste in un contesto in cui il modulo del servizio Web non è in esecuzione.

*C#*

    // At start of processing this request:

    // Operation Id and Name are attached to all telemetry and help you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    telemetry.Context.Operation.Name = requestName;

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetry.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed,
       "200", true);  // Response code, success



## <a name="operation-context"></a>Contesto dell'operazione
Gli elementi di telemetria possono essere associati tra loro mediante il collegamento di un ID operazione comune. Il modulo di rilevamento delle richieste standard esegue questa operazione per le eccezioni e gli altri eventi inviati durante l'elaborazione di una richiesta HTTP. In [Ricerca](app-insights-diagnostic-search.md) e [Analisi](app-insights-analytics.md) è possibile usare l'ID per trovare facilmente gli eventi associati alla richiesta.

Il modo più semplice per impostare l'ID è selezionare un contesto dell'operazione mediante questo modello:

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item - for example:
        operation.Telemetry.ResponseCode = "200";

        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.

Oltre a impostare un contesto dell'operazione, `StartOperation` crea un elemento di telemetria del tipo specificato e lo invia quando l'operazione viene eliminata o se si chiama in modo esplicito `StopOperation`. Se si usa `RequestTelemetry` come tipo di telemetria, la durata viene impostata sull'intervallo di tempo compreso tra l'avvio e l'arresto.

I contesti dell’operazione non possono essere annidati. Se è già presente un contesto dell'operazione, il suo ID corrispondente viene associato a tutti gli elementi contenuti, compreso l'elemento creato con StartOperation.

In Ricerca il contesto dell'operazione viene usato per creare l'elenco di elementi correlati:

![Elementi correlati](./media/app-insights-api-custom-events-metrics/21.png)

## <a name="track-exception"></a>Tenere traccia di un'eccezione
Inviare le eccezioni ad Application Insights: per [contarle][metrics], come un'indicazione della frequenza di un problema e per [esaminare le singole occorrenze][diagnostic]. I report includono le analisi dello stack.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

Gli SDK rilevano molte eccezioni automaticamente, quindi non è sempre necessario richiamare TrackException in modo esplicito.

* ASP.NET: [Scrivere codice per intercettare le eccezioni](app-insights-asp-net-exceptions.md)
* J2EE: [Le eccezioni vengono rilevate automaticamente](app-insights-java-get-started.md#exceptions-and-request-failures)
* JavaScript: rilevato automaticamente. Se si vuole disabilitare la raccolta automatica, aggiungere una riga al frammento di codice che si inserisce nelle pagine Web:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

## <a name="track-trace"></a>Monitorare una traccia
Usare questo metodo per diagnosticare i problemi mediante l'invio di una traccia di navigazione ad Application Insights. È possibile inviare blocchi di dati di diagnostica e controllarli in [Ricerca diagnostica][diagnostic].

Gli [adattatori di log][trace] usano questa API per inviare i log di terze parti al portale.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


È possibile eseguire ricerche nel contenuto del messaggio, ma, a differenza dei valori delle proprietà, non è possibile filtrarlo.

Il limite delle dimensioni in `message` è molto superiore al limite per le proprietà.
Un vantaggio di TrackTrace è che è possibile inserire dati relativamente lunghi nel messaggio. Ad esempio, è possibile codificare dati POST.  

È anche possibile aggiungere al messaggio un livello di gravità. E come per altri tipi di dati di telemetria, si possono aggiungere valori di proprietà che è possibile usare per filtrare o cercare set di tracce diversi, Ad esempio:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

In [Ricerca][diagnostic] questo consentirà di filtrare facilmente tutti i messaggi di un determinato livello di gravità relativi a un database specifico.

## <a name="track-dependency"></a>Tenere traccia delle dipendenze
Usare questa chiamata per rilevare i tempi di risposta e le percentuali di successo delle chiamate a un frammento di codice esterno. I risultati vengono visualizzati nei grafici dipendenze nel portale.

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

Tenere presente che il server SDK include un [modulo dipendenza](app-insights-asp-net-dependencies.md) che consente di individuare e tracciare alcune chiamate della dipendenza automaticamente, ad esempio a database e API REST. È necessario installare un agente nel server per consentire il funzionamento del modulo. Usare questa chiamata se si vuole tenere traccia di chiamate che non vengono intercettate dal rilevamento automatico o se non si vuole installare l'agente.

Per disattivare il modulo standard per il rilevamento delle dipendenze, modificare il file [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) ed eliminare il riferimento a `DependencyCollector.DependencyTrackingTelemetryModule`.

## <a name="flushing-data"></a>Scaricamento dei dati
In genere l'SDK invia i dati in momenti scelti per ridurre al minimo l'impatto sull'utente. In alcuni casi tuttavia è possibile che si voglia scaricare il buffer, ad esempio, se si sta usando l'SDK in un'applicazione che si arresta.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

Si noti che la funzione è asincrona per il [canale di telemetria del server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

## <a name="authenticated-users"></a>utenti autenticati
In un'app Web gli utenti sono identificati dai cookie per impostazione predefinita. Un utente può essere conteggiato più volte, se accede all'app da un computer o un browser diverso o se elimina i cookie.

Se gli utenti accedono all'app, è possibile ottenere un conteggio più preciso impostando l'ID dell'utente autenticato nel codice del browser:

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

In un'applicazione MVC Web ASP.NET, ad esempio:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Non è necessario usare il nome di accesso effettivo dell'utente. È sufficiente usare un ID univoco per l'utente. Non deve includere spazi o i caratteri `,;=|`.

L'ID utente viene inoltre impostato in un cookie di sessione e inviato al server. Se l'SDK del server è installato, l'ID dell'utente autenticato verrà inviato come parte delle proprietà di contesto della telemetria del client e del server, affinché sia possibile filtrarlo ed eseguire ricerche al suo interno.

Se l'app raggruppa gli utenti in account, è anche possibile passare un identificatore per l'account, con le stesse limitazioni di caratteri.

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

In [Esplora metriche](app-insights-metrics-explorer.md) è possibile creare un grafico che conta i valori **Utenti, Autenticati** e **Account utente**.

È inoltre possibile [ricercare][diagnostic] i punti dati del client con account e nomi utente specifici.

## <a name="a-namepropertiesafilter-search-and-segment-your-data-with-properties"></a><a name="properties"></a>Filtrare, cercare e segmentare i dati con proprietà
È possibile associare proprietà e misure agli eventi e anche a metriche, visualizzazioni pagine, eccezioni e altri dati di telemetria.

**proprietà** sono valori di stringa che è possibile usare per filtrare i dati di telemetria nei report di utilizzo. Ad esempio, se l'app offre più giochi, è possibile associare il nome del gioco a ogni evento per vedere quali sono i giochi più diffusi.

Esiste un limite di circa 1 KB per la lunghezza di stringa. Se si vogliono inviare grandi quantità di dati, usare il parametro del messaggio di [TrackTrace](#track-trace).

**metriche** sono valori numerici che possono essere rappresentati graficamente. Ad esempio, è possibile verificare se esiste un aumento graduale nei punteggi raggiunti dai giocatori. I grafici possono essere segmentati in base alle proprietà inviate con l'evento, in modo da ottenere grafici separati o in pila per giochi diversi.

I valori metrici devono essere >= 0 per essere visualizzati correttamente.

Esistono tuttavia alcuni [limiti sul numero di proprietà, di valori delle proprietà e di metriche](#limits) che è possibile usare.

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> Assicurarsi di non registrare informazioni personali nelle proprietà.
>
>

**Se è stata usata la metrica**, aprire Esplora metriche e selezionare la metrica dal gruppo personalizzato:

![Aprire Esplora metrica, selezionare il grafico e selezionare la metrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*Se non viene visualizzata l'unità di misura, o se l'intestazione personalizzata non è presente, chiudere il pannello di selezione e riprovare più tardi. A volte potrebbe essere necessaria un'ora per aggregare le metriche attraverso la pipeline.*

**Se si usano proprietà e metriche**, segmentare la metrica in base alla proprietà:

![Impostare Raggruppamento e quindi selezionare la proprietà in Raggruppa per](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

In **Ricerca diagnostica** è possibile visualizzare le proprietà e le metriche di singole occorrenze di un evento.

![Selezionare un'istanza e quindi scegliere '...'](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Usare il campo Ricerca per visualizzare le occorrenze di eventi con un valore della proprietà particolare.

![Digitare un termine in Ricerca](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Altre informazioni sulle espressioni di ricerca][diagnostic].

#### <a name="alternative-way-to-set-properties-and-metrics"></a>Metodo alternativo per impostare proprietà e metriche
Se si preferisce, è possibile raccogliere i parametri di un evento in un oggetto separato:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Non riusare la stessa istanza dell'elemento di telemetria, `event` in questo esempio, per chiamare Track*() più volte. Potrebbe causare l'invio della telemetria con una configurazione errata.
>
>

## <a name="a-nametimeda-timing-events"></a><a name="timed"></a> Temporizzazione degli eventi
A volte si vuole rappresentare in un grafico il tempo necessario per eseguire un'azione. Ad esempio, si potrebbe voler sapere quanto tempo occorre agli utenti per scegliere tra le opzioni disponibili in un gioco. Questo è un esempio utile dell'uso del parametro di misurazione.

*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="a-namedefaultsadefault-properties-for-custom-telemetry"></a><a name="defaults"></a>Proprietà predefinite per i dati di telemetria personalizzati
Se si intende impostare solo i valori di proprietà predefiniti per alcuni eventi personalizzati scritti, è possibile impostarli in un TelemetryClient. Vengono associati a ogni elemento di telemetria inviato da quel client.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");



Le singole chiamate di telemetria possono sostituire i valori predefiniti nei relativi dizionari delle proprietà.

**Per i client Web di JavaScript**, [usare gli inizializzatori di telemetria JavaScript](#js-initializer).

**Per aggiungere proprietà a tutti i dati di telemetria**, inclusi i dati dei moduli di raccolta standard, [implementare `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Campionamento, filtri ed elaborazione dei dati di telemetria
È possibile scrivere il codice per elaborare i dati di telemetria prima che venga inviato da SDK. L'elaborazione include i dati inviati dai moduli telemetria standard come la raccolta delle richieste HTTP e la raccolta delle dipendenze.

* Per [aggiungere proprietà](app-insights-api-filtering-sampling.md#add-properties) ai dati di telemetria, implementare `ITelemetryInitializer`, ad esempio se si vogliono aggiungere numeri di versione o valori calcolati da altre proprietà.
* L'[applicazione di filtri](app-insights-api-filtering-sampling.md#filtering) consente di modificare o rimuovere i dati di telemetria prima che vengano inviati dall'SDK implementando `ITelemetryProcesor`. È possibile controllare gli elementi inviati o eliminati, ma è necessario tener conto dell'effetto sulle metriche. A seconda di come si eliminano gli elementi, si potrebbe perdere la possibilità di navigare tra elementi correlati.
* Il [campionamento](app-insights-api-filtering-sampling.md) è una soluzione in pacchetto che consente di ridurre il volume dei dati inviati dall'app al portale, senza influenzare le metriche visualizzate e senza influire sulla possibilità di diagnosticare i problemi navigando tra elementi correlati come eccezioni, richieste e visualizzazioni di pagina.

[Altre informazioni](app-insights-api-filtering-sampling.md)

## <a name="disabling-telemetry"></a>Disabilitazione della telemetria
Per **avviare e arrestare in modo dinamico** la raccolta e la trasmissione di dati di telemetria:

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Per **disabilitare gli agenti di raccolta standard selezionati** (ad esempio, contatori delle prestazioni, richieste HTTP o dipendenze), eliminare o impostare come commento le righe pertinenti in [Applicationinsights.config][config]. È possibile eseguire questa operazione, ad esempio, se si vogliono inviare i propri dati TrackRequest.

## <a name="a-namedebugadeveloper-mode"></a><a name="debug"></a>Modalità di sviluppo
Durante il debug, è utile accelerare i dati di telemetria venga nella pipeline in modo da visualizzare immediatamente i risultati. È possibile che vengano visualizzati anche altri messaggi che consentono di tracciare eventuali problemi con i dati di telemetria. Disattivare questa modalità in fase di produzione, poiché potrebbe rallentare l'app.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="a-nameikeya-set-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a> Impostare la chiave di strumentazione per la telemetria personalizzata selezionata
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="a-namedynamic-ikeya-dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Chiave di strumentazione dinamica
Per evitare di combinare i dati di telemetria da ambienti di sviluppo, test e produzione, è possibile [creare risorse distinte di Application Insights][create] e modificare le relative chiavi a seconda dell'ambiente.

Invece di ottenere la chiave di strumentazione dal file di configurazione, è possibile impostarla nel codice. Impostare la chiave in un metodo di inizializzazione, ad esempio global.aspx.cs in un servizio ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



Nelle pagine Web è possibile impostarla dallo stato del server Web anziché codificarla nello script. Ad esempio, in una pagina Web generata in un'app ASP.NET:

*JavaScript in Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient dispone di una proprietà Context, che contiene un numero di valori che vengono inviati insieme a tutti i dati di telemetria. Sono in genere impostati dai moduli di telemetria standard, ma è possibile anche impostarli manualmente. Ad esempio:

    telemetry.Context.Operation.Name = "MyOperationName";

Se si imposta uno di questi valori manualmente, è possibile rimuovere la riga pertinente da [ApplicationInsights.config][config], in modo che i valori personali e quelli standard non si confondano.

* **Componente** identifica l'app e la relativa versione
* **Dispositivo**: dati relativi al dispositivo in cui è in esecuzione l'app. Nelle App Web questo è il dispositivo client o server da cui vengono inviati i dati di telemetria)
* **InstrumentationKey** identifica la risorsa di Application Insights in Azure dove verranno visualizzati i dati di telemetria. Viene in genere presa dal file ApplicationInsights.config
* **Percorso** identifica la posizione geografica del dispositivo.
* **Operazione** nelle app Web, la richiesta HTTP corrente. In altri tipi di app è possibile impostarla in modo da raggruppare gli eventi tra loro.
  * L'**ID**: un valore generato che mette in correlazione eventi diversi, in modo che quando si analizza qualsiasi evento in Ricerca diagnostica, è possibile trovare "Elementi correlati"
  * **Nome**: un identificatore, in genere l'URL della richiesta HTTP.
  * **SyntheticSource**: se non è null o vuota, questa stringa indica che l'origine della richiesta è stata identificata come un test Web o un robot. Per impostazione predefinita, viene esclusa dai calcoli in Esplora metriche.
* **Proprietà** proprietà che vengono inviate con tutti i dati di telemetria. È possibile eseguire l'override di questo valore in singole chiamate Track*.
* **Sessione** identifica la sessione dell'utente. L'ID viene impostato su un valore generato, che viene modificato quando l'utente non è stato attivo per un periodo di tempo specifico.
* **Utente** : le informazioni dell'utente.

## <a name="limits"></a>Limiti
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

*Come è possibile evitare di raggiungere il limite di velocità dei dati?*

* Usare il [campionamento](app-insights-sampling.md).

*Per quanto tempo vengono conservati i dati?*

* Vedere [Conservazione dei dati e privacy][dati].

## <a name="reference-docs"></a>Documentazione di riferimento
* [Riferimento ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Riferimento Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Informazioni di riferimento su JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Codice SDK
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Pacchetti per Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [SDK per Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [Tutte le piattaforme](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Domande
* *Quali eccezioni potrebbero essere generate dalle chiamate Track_()?*

    Nessuna. Non è necessario eseguirne il wrapping in clausole try-catch. Se l'SDK rileva un problema, registrerà un messaggio che verrà visualizzato nell'output della console di debug e quindi nella ricerca diagnostica per approfondirne i dettagli.
* *Esiste un'API REST per ottenere dati dal portale?*

    Sì, l'[API di accesso ai dati](https://dev.applicationinsights.io/). Altri modi per estrarre i dati sono l'[esportazione da Analytics a Power BI](app-insights-export-power-bi.md) e l'[esportazione continua](app-insights-export-telemetry.md).

## <a name="a-namenextanext-steps"></a><a name="next"></a>Passaggi successivi
[Ricerca di eventi e log][diagnostic]

[Esempi e procedure dettagliate](app-insights-code-samples.md)

[Risoluzione dei problemi][qna]

<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[dati]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md



<!--HONumber=Nov16_HO3-->


