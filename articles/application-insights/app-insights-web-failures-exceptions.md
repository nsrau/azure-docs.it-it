<properties 
	pageTitle="Rilevare e diagnosticare errori ed eccezioni nelle app Web" 
	description="Rilevare e diagnosticare errori ed eccezioni nelle app Web" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="awills"/>
 
# Diagnosticare errori ed eccezioni nelle app Web con Application Insights

[Application Insights per Visual Studio][start] è un potente strumento per rilevare e diagnosticare errori nelle applicazioni. Questo articolo è incentrato sulle applicazioni Web, ma è anche possibile applicare Application Insights a una [vasta gamma di altre piattaforme][platforms].

## Configurare l'app con Application Insights

Aggiungere l'SDK al progetto dell'applicazione. Quando si esegue l'app rilasciata, l'SDK invierà dati di telemetria sulle prestazioni dell'app al portale di Application Insights.

* [Aggiungere Application Insights ad app ASP.NET][greenbrown]
* [Aggiungere Application Insights ad app Java][java]

Se le pagine Web contengono script sostanziali, è possibile che si voglia anche: * [Aggiungere Application Insights alle pagine Web][track]


## Ricerca diagnostica



Aprire Ricerca diagnostica per visualizzare i dati di telemetria inviati automaticamente dall'SDK.

![](./media/app-insights-web-failures-exceptions/appinsights-45diagnostic.png)

![](./media/app-insights-web-failures-exceptions/appinsights-31search.png)

I dettagli variano da un tipo di applicazione all'altro. È possibile fare clic su un singolo evento per ottenere altri dettagli.

##<a name="events"></a>Eventi personalizzati

Gli eventi personalizzati vengono visualizzati in [Ricerca diagnostica][diagnostic] e in [Esplora metriche][metrics]. È possibile inviarli da dispositivi, pagine web e applicazioni server. Possono essere usati per scopi diagnostici e per [comprendere i criteri di utilizzo][track].

Un evento personalizzato è contraddistinto da un nome e può contenere anche proprietà che è possibile filtrare, insieme alle misurazioni numeriche.

JavaScript nel client

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# nel server

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);


VB nel server

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### Eseguire l'app e visualizzare i risultati.

Aprire Ricerca diagnostica.

Selezionare Evento personalizzato e selezionare un nome di evento specifico.

![](./media/app-insights-web-failures-exceptions/appinsights-332filterCustom.png)


Filtrare i dati ulteriormente immettendo un termine di ricerca per un valore della proprietà.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-5.png)

Analizzare un singolo evento per visualizzarne le proprietà dettagliate.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-4.png)



##<a name="trace"></a> Telemetria di traccia

La telemetria di traccia rappresenta il codice che viene inserito specificamente per creare i log di diagnostica.

È, ad esempio, possibile inserire chiamate simili alla seguente:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


####  Installare un adattatore per il framework di registrazione

È anche possibile eseguire la ricerca nei log generati con un framework di registrazione come log4Net, NLog o System.Diagnostics.Trace.

1. Se si intende usare log4Net o NLog, installarlo nel progetto. 
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
3. Selezionare Online > Tutti, quindi selezionare **Includi versione preliminare** e cercare "Microsoft.ApplicationInsights".

    ![Ottenere la versione preliminare dell'adattatore appropriato](./media/app-insights-web-failures-exceptions/appinsights-36nuget.png)

4. Selezionare il pacchetto appropriato tra:
  + Microsoft.ApplicationInsights.TraceListener (per acquisire le chiamate System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Il pacchetto NuGet installa gli assembly necessari e modifica inoltre web.config o app.config.

#### <a name="pepper"></a>Inserire chiamate di log di diagnostica

Se si usa System.Diagnostics.Trace, una tipica chiamata sarà simile alla seguente:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se si preferisce log4net o NLog:

    logger.Warn("Slow response - database01");

Eseguire l'app in modalità debug o distribuirla.

I messaggi verranno visualizzati in Ricerca diagnostica quando si seleziona il filtro di traccia.

### <a name="exceptions"></a>Eccezioni

La visualizzazione dei report delle eccezioni in Application Insights è molto utile, soprattutto perché è possibile spostarsi tra le richieste non riuscite e le eccezioni e leggere lo stack dell'eccezione.

È possibile scrivere codice per inviare dati di telemetria per le eccezioni:

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

I parametri delle proprietà e delle misurazioni sono facoltativi, ma sono utili per filtrare e aggiungere altre informazioni. Ad esempio, per un'app in grado di eseguire diversi giochi, è possibile trovare tutti i report di eccezione correlati a un gioco specifico. È possibile aggiungere qualsiasi numero di elementi a ogni dizionario.

#### Visualizzazione delle eccezioni

Nel pannello Panoramica viene visualizzato un riepilogo delle eccezioni segnalate ed è possibile fare clic sulle singole eccezioni per visualizzare altri dettagli. Ad esempio:


![](./media/app-insights-web-failures-exceptions/appinsights-039-1exceptions.png)

Fare clic su qualsiasi tipo di eccezione per visualizzare occorrenze specifiche:

![](./media/app-insights-web-failures-exceptions/appinsights-333facets.png)

È anche possibile aprire direttamente Ricerca diagnostica, filtrare le eccezioni e scegliere il tipo di eccezione che si vuole visualizzare.

### Segnalazione di eccezioni non gestite

Quando possibile, Application Insights segnala le eccezioni non gestite restituite da dispositivi, [Web browser][usage] o server Web, indipendentemente se instrumentati da [Status Monitor][redfield] o [Application Insights SDK][greenbrown].

> [AZURE.NOTE]Se in un Web browser si includono file script da reti CDN o da altri domini, verificare che il tag script includa l'attributo ```crossorigin="anonymous"``` e che il server invii intestazioni CORS per ottenere un'analisi dello stack e informazioni dettagliate per le eccezioni JavaScript non gestite da queste risorse.

Tuttavia, in alcuni casi non è sempre possibile eseguire questa operazione perché .NET Framework rileva le eccezioni. Per fare in modo che vengano visualizzate tutte le eccezioni, è pertanto necessario scrivere un semplice gestore di eccezioni. La procedura migliore dipende dalla tecnologia. Per informazioni dettagliate, vedere [questo post di blog](http://blogs.msdn.com/b/visualstudioalm/archive/2014/12/12/application-insights-exception-telemetry.aspx).

### Correlazione con una compilazione

Quando si leggono i log di diagnostica, è probabile che il codice sorgente sia stato modificato rispetto a quando è stato distribuito il codice attivo.

È pertanto utile inserire le informazioni di compilazione, ad esempio l'URL della versione corrente, in una proprietà insieme a ogni eccezione o a una traccia.

Invece di aggiungere la proprietà separatamente a ogni chiamata di eccezione, è possibile impostare le informazioni nel contesto predefinito.

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

Nell'inizializzatore di app, ad esempio Global.asax.cs, procedere come segue:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

###<a name="requests"></a> Richieste del server Web

I dati di telemetria delle richieste vengono inviati automaticamente quando si [installa Status Monitor nel server Web][redfield] o quando si [aggiunge Application Insights al progetto Web][greenbrown]. Vengono inoltre aggiunti nei grafici dei tempi di richiesta e di risposta in Esplora metriche e nella pagina Panoramica.

Se si vogliono inviare eventi aggiuntivi, è possibile usare l'API TrackRequest().

## <a name="questions"></a>Domande e risposte

### <a name="emptykey"></a>Viene visualizzato l'errore: "La chiave di strumentazione non può essere vuota"

Risulta che l'utente abbia installato il pacchetto Nuget dell'adattatore di registrazione senza aver installato Application Insights.

In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. Verrà visualizzata una finestra di dialogo che invita ad accedere ad Azure e a creare una risorsa Application Insights o a riusarne una esistente. Il problema verrà in tal modo risolto.

### <a name="limits"></a>Quanti dati vengono conservati?

Fino a 500 eventi al secondo da ciascuna applicazione. Gli eventi vengono conservati per sette giorni.

## <a name="add"></a>Passaggi successivi

* [Configurare i test di disponibilità e velocità di risposta][availability]
* [Risoluzione dei problemi][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[platforms]: app-insights-platforms.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->