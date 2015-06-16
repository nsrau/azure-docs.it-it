<properties 
	pageTitle="API di Application Insights per metriche ed eventi personalizzati" 
	description="Inserire alcune righe di codice nell'app desktop o per dispositivi, nella pagina Web o nel servizio per tenere traccia dell'utilizzo e diagnosticare i problemi." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="ronmart"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="awills"/>

# API di Application Insights per metriche ed eventi personalizzati 

*Application Insights è disponibile in anteprima.*

Inserire alcune righe di codice nell'applicazione per scoprire come viene usato dagli utenti o per agevolare la diagnosi dei problemi. È possibile inviare i dati di telemetria dalle app desktop e per dispositivi, dai client Web e dai server Web.

Gli agenti di raccolta dati di Application Insights usano questa API per inviare dati di telemetria standard come visualizzazioni pagina e report di eccezioni, ma è possibile usarli anche per inviare i propri dati di telemetria personalizzati.

## API summary

L'API è uniforme in tutte le piattaforme, a parte alcune variazioni di lieve entità.

Metodo | Usato per
---|---
[`TrackPageView`](#page-views) | Pagine, schermate, pannelli o form
[`TrackEvent`](#track-event) | Azioni dell'utente e altri eventi. Usato per tenere traccia del comportamento dell'utente o per monitorare le prestazioni.
[`TrackMetric`](#track-metric) | Misurazioni delle prestazioni, ad esempio la lunghezza della coda, non correlate a eventi specifici
[`TrackException`](#track-exception)|Registrare le eccezioni per la diagnosi. Tracciare dove si verificano in relazione ad altri eventi ed esaminare le analisi dello stack.
[`TrackRequest`](#track-request)| Registrare la frequenza e la durata delle richieste del server per l'analisi delle prestazioni.
[`TrackTrace`](#track-trace)|Messaggi nei log di diagnostica. È anche possibile acquisire i log di terze parti.

È possibile [associare proprietà e metriche](#properties) alla maggior parte di queste chiamate di telemetria.


## <a name="prep"></a>Prima di iniziare

Completare queste operazioni, se non è stato ancora fatto:

* Aggiungere Application Insights SDK al progetto:
 * [Progetto ASP.NET][greenbrown]
 * [Progetto Windows][windows]
 * [Progetto Java][java] 
 * [JavaScript in ogni pagina Web][client]   

* Nel dispositivo o nel codice del server Web includere:

    *C#:* `using Microsoft.ApplicationInsights;`

    *VB:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## Costruire un oggetto TelemetryClient

Costruire un'istanza di TelemetryClient (tranne che in JavaScript nelle pagine Web):

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

Si consiglia di usare un'istanza di `TelemetryClient` per ogni richiesta in un'app Web o per ogni sessione in altre app. È possibile impostare proprietà quali `TelemetryClient.Context.User.Id` per tenere traccia degli utenti e delle sessioni. Queste informazioni sono associate a tutti gli eventi inviati dall'istanza.

TelemetryClient è thread-safe.



## Tenere traccia di un evento

Gli eventi possono essere visualizzati in [Esplora metriche][metrics] come un conteggio aggregato ed è anche possibile visualizzare singole occorrenze in [Ricerca diagnostica][diagnostic].

Inserire eventi nel codice per contare la frequenza d'uso di una particolare funzionalità, la frequenza di raggiungimento di obiettivi specifici o di scelte particolari.

Ad esempio, in un'app di gioco, inviare un evento ogni volta che un utente vince il gioco:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*
    
    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


Fare clic sul riquadro Eventi personalizzati nel pannello Panoramica:

![Individuare la risorsa dell'applicazione in portal.azure.com](./media/app-insights-custom-events-metrics-api/01-custom.png)

Fare clic per visualizzare un grafico di panoramica e un elenco completo.

Selezionare il grafico e segmentarlo in base al Nome evento per visualizzare i relativi contributi degli eventi più significativi.

![Selezionare il grafico e impostare Raggruppamento](./media/app-insights-custom-events-metrics-api/02-segment.png)

Nell'elenco sotto il grafico selezionare un nome evento. Fare clic per visualizzare le singole occorrenze dell'evento.

![Eseguire il drill-through degli eventi](./media/app-insights-custom-events-metrics-api/03-instances.png)

Per visualizzare altri dettagli, fare clic su qualsiasi occorrenza.

## <a name="properties"></a>Filtrare, cercare e segmentare i dati con proprietà

È possibile associare proprietà e misure agli eventi (anche a metriche, visualizzazioni pagine e altri dati di telemetria).

Le **proprietà** sono valori di stringa che è possibile usare per filtrare i dati di telemetria nei report di utilizzo. Ad esempio, se l'app comprende più giochi, è possibile associare il nome del gioco a ogni evento per vedere quali sono i giochi più diffusi.

Esiste un limite di circa 1 KB per la lunghezza di stringa. Se si vogliono inviare grandi quantità di dati, usare il parametro del messaggio di [TrackTrace](#track-trace).

Le **metriche** sono valori numerici che possono essere rappresentati graficamente. Ad esempio, è possibile verificare se esiste un aumento graduale nei punteggi raggiunti dai giocatori. I grafici possono essere segmentati in base alle proprietà inviate con l'evento, in modo da poter avere i grafici separati o in pila per giochi diversi.

I valori metrici devono essere >= 0 per essere visualizzati correttamente.

*JavaScript*

    appInsights.trackEvent // or trackPageView, trackMetric, ...
      ("WinGame",
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
    
    telemetry.trackEvent("WinGame", properties, metrics2/7/2015 12:05:25 AM );


> [AZURE.NOTE]Assicurarsi di non registrare informazioni personali identificabili nelle proprietà.

**Se è stata usata la metrica**, aprire Esplora metriche e selezionare la metrica dal gruppo personalizzato:

![Aprire Esplora metrica, selezionare il grafico e selezionare la metrica](./media/app-insights-custom-events-metrics-api/03-track-custom.png)

*Se la metrica non viene visualizzata, chiudere il pannello di selezione, attendere qualche minuto e fare clic su Aggiorna.*

**Se si usano proprietà e metriche**, segmentare la metrica in base alla proprietà:


![Impostare Raggruppamento e quindi selezionare la proprietà in Raggruppa per](./media/app-insights-custom-events-metrics-api/04-segment-metric-event.png)



In **Ricerca diagnostica** è possibile visualizzare le proprietà e le metriche di singole occorrenze di un evento.


![Selezionare un'istanza e quindi scegliere '...'](./media/app-insights-custom-events-metrics-api/appinsights-23-customevents-4.png)


Usare il campo Ricerca per visualizzare le occorrenze di eventi con un valore della proprietà particolare.


![Digitare un termine in Ricerca](./media/app-insights-custom-events-metrics-api/appinsights-23-customevents-5.png)

[Altre informazioni sulle stringhe di ricerca][diagnostic]

#### Metodo alternativo per impostare proprietà e metriche

Se è più pratico, è possibile raccogliere i parametri di un evento in un oggetto separato:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);


## <a name="timed"></a> Eventi programmati

A volte si vuole rappresentare in un grafico il tempo necessario per eseguire un'azione. Ad esempio, si potrebbe voler sapere quanto tempo occorre agli utenti per scegliere tra le opzioni disponibili in un gioco.

È possibile associare i dati relativi alla durata agli eventi. Nel client Web anziché chiamare trackEvent usare le chiamate seguenti:

*JavaScript nel client Web*

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

Usare la stessa stringa come primo parametro nelle chiamate di avvio e di arresto.

Questa funzionalità non è integrata in altri SDK. Ma è possibile scrivere il proprio codice simile al seguente:

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



## Tenere traccia delle metriche

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

Infatti, è possibile farlo in un thread in background:

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


Per visualizzare i risultati, aprire Esplora metriche e aggiungere un nuovo grafico. Impostarlo per visualizzare le metriche.

![Aggiungere un nuovo grafico o selezionare un grafico e selezionare le metriche in Personalizzato](./media/app-insights-custom-events-metrics-api/03-track-custom.png)


## Visualizzazioni pagina

In un'app per dispositivo o pagine Web i dati di telemetria delle visualizzazioni pagina vengono inviati per impostazione predefinita quando viene caricata ogni schermata o pagina. È tuttavia possibile modificarli per tenere traccia delle visualizzazioni pagina in momenti diversi o aggiuntivi. Ad esempio, in un'app che visualizza schede o pannelli, è possibile tenere traccia di una "pagina" ogni volta che l'utente apre un nuovo pannello.

![Utilizzo di filtri nel pannello Panoramica](./media/app-insights-custom-events-metrics-api/appinsights-47usage-2.png)

I dati utente e sessione vengono inviati come proprietà insieme alle visualizzazioni pagina, in modo che i grafici utente e sessione si attivino in presenza dei dati di telemetria delle visualizzazioni pagina.

#### Visualizzazioni pagina personalizzate

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


Se si hanno più schede in pagine HTML diverse, è possibile specificare anche l'URL:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### Visualizzazioni pagina con durata

Usando questa coppia di chiamate di metodi anziché trackPageView, è possibile analizzare il tempo di visualizzazione delle pagine da parte degli utenti.

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

Usare la stessa stringa come primo parametro nelle chiamate di avvio e di arresto.

Esaminare la metrica della durata della pagina in [Esplora metriche][metrics].


## Tenere traccia di una richiesta

Questo metodo viene usato dal server SDK per registrare le richieste HTTP.

È anche possibile chiamarlo manualmente se si vogliono simulare le richieste in un contesto in cui il modulo del servizio Web non è in esecuzione.

*C#*

    // At start of processing this request:

    // Operation Id is attached to all telemetry and helps you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetryClient.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success

## Tenere traccia di un'eccezione

Inviare le eccezioni ad Application Insights: per [contarle][metrics], come un'indicazione della frequenza di un problema e per [esaminare le singole occorrenze][diagnostic].

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

Nelle app per dispositivi mobili di Windows, l'SDK rileva le eccezioni non gestite, in modo da non doverle registrare.



## Monitorare una traccia 

Usare questa opzione per diagnosticare i problemi mediante l'invio di una traccia di navigazione ad Application Insights. È possibile inviare blocchi di dati di diagnostica e controllarli in [Ricerca diagnostica][diagnostic].

 

Gli [adattatori di log][trace] usano questa API per inviare i log di terze parti al portale.


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

Il limite delle dimensioni in `message` è molto superiore al limite per le proprietà. È possibile eseguire ricerche nel contenuto del messaggio, ma (a differenza dei valori di proprietà) non è possibile filtrarlo.


## Impostare le proprietà predefinite per tutti i dati di telemetria

È possibile impostare un inizializzatore universale in modo che tutti i nuovi TelemetryClient usino automaticamente il contesto.

Sono inclusi i dati di telemetria standard inviati dai moduli di telemetria specifici della piattaforma, ad esempio la traccia delle richieste del server Web.

*C#*

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

*Java*

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyTelemetryInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.getProperties().put("AppVersion", "2.1");
      }
    }

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyTelemetryInitializer());



## Impostare la chiave di strumentazione nel codice per tutti i dati di telemetria

Invece di ottenere la chiave di strumentazione dal file di configurazione, è possibile impostarla nel codice. Questa opzione potrebbe essere utile, ad esempio, per inviare i dati di telemetria da installazioni di prova a una risorsa Application Insights diversa rispetto ai dati di telemetria dell'applicazione attiva.

Impostare la chiave in un metodo di inizializzazione, ad esempio global.aspx.cs in un servizio ASP.NET:

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


## <a name="defaults"></a>Impostare i valori predefiniti per i dati di telemetria personalizzati selezionati

Se si intendono impostare solo i valori di proprietà predefiniti per alcuni eventi personalizzati scritti, è possibile impostarli in un TelemetryClient. Vengono associati a ogni elemento di telemetria inviato da quel client.

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



## <a name="ikey"></a> Impostare la chiave di strumentazione per la telemetria personalizzata selezionata

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.Context.InstrumentationKey = "---my key---";
    // ...


## Disabilitare la telemetria standard

È possibile [disabilitare parti selezionate della telemetria standard][config] modificando `ApplicationInsights.config`. È possibile eseguire questa operazione, ad esempio, se si vogliono inviare i propri dati TrackRequest.

[Altre informazioni][config]


## <a name="debug"></a>Modalità di sviluppo

Durante il debug, è utile che la telemetria venga velocizzata nella pipeline in modo da visualizzare immediatamente i risultati. È possibile che vengano visualizzati anche altri messaggi che consentono di tracciare eventuali problemi con i dati di telemetria. Disattivarla in fase di produzione, poiché potrebbe rallentare l'app.


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True



## Documentazione di riferimento

* [Riferimento ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Riferimento Java](http://dl.windowsazure.com/applicationinsights/javadoc/)


* *D: Esiste un'API REST?*

    Sì, ma è non ancora pubblicata.

## <a name="next"></a>Passaggi successivi


[Cercare eventi e log][diagnostic]

[Risoluzione dei problemi][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

<!---HONumber=58--> 