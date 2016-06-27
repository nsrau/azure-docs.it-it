<properties 
	pageTitle="Application Insights per i servizi di Windows" 
	description="Analizzare l'utilizzo e le prestazioni dei servizi di Windows in background con Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Application Insights in servizi in background di Windows

*Application Insights è disponibile in anteprima.*

[Visual Studio Application Insights](app-insights-get-started.md) consente di monitorare l'utilizzo e le prestazioni di un'applicazione distribuita.

Tutte le applicazioni per Windows, inclusi i servizi in background e i ruoli di lavoro, possono usare Application Insights SDK per inviare dati di telemetria ad Application Insights. È anche possibile l'SDK a un progetto di tipo libreria di classi.

È possibile scegliere quali agenti di raccolta dati standard si desidera utilizzare (ad esempio per il monitoraggio dei contatori delle prestazioni o delle chiamate di dipendenza) oppure usare solo l'API della memoria centrale e scrivere i propri dati di telemetria.

Controllare prima se si sta lavorando con un altro tipo di applicazione Windows:

* App Web: passare ad [ASP.NET 4](app-insights-asp-net.md), [ASP.NET 5](app-insights-asp-net-five.md).
* [Servizi cloud di Azure](app-insights-cloudservices.md)
* Applicazioni desktop: è consigliabile usare [HockeyApp](https://hockeyapp.net). Con HockeyApp è possibile gestire la distribuzione, i test live e i commenti degli utenti, oltre a monitorare i report di utilizzo e di arresto anomalo. È anche possibile [inviare dati di telemetria ad Application Insights da un'applicazione desktop](app-insights-windows-desktop.md). 


## <a name="add"></a> Creare una risorsa Application Insights


1.  Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Scegliere l'app ASP.NET per il tipo di applicazione. 

    ![Fare clic su Nuovo, Application Insights](./media/app-insights-windows-services/01-new.png)


2.  Eseguire una copia della chiave di strumentazione. Trovare la chiave nell'elenco a discesa Informazioni di base della nuova risorsa appena creata. Chiudere la mappa dell'applicazione o scorrere verso sinistra sul pannello Panoramica per la risorsa.

    ![Fare clic su Informazioni di base, selezionare la chiave e premere CTRL+C](./media/app-insights-windows-services/10.png)

## <a name="sdk"></a>Installare SDK nell'applicazione


1. In Visual Studio modificare i pacchetti NuGet del progetto dell'applicazione Web.

    ![Fare clic con il pulsante destro del mouse sul progetto e selezionare Gestisci pacchetti NuGet](./media/app-insights-windows-services/03-nuget.png)

2. Installare il pacchetto Windows Server di Application Insights: Microsoft.ApplicationInsights.WindowsServer

    ![Cercare "Application Insights"](./media/app-insights-windows-services/04-ai-nuget.png)

    *È possibile usare altri pacchetti?*

    Sì. Se si desidera usare l'API per inviare i propri dati di telemetria, scegliere l'API di base (Microsoft.ApplicationInsights). Il pacchetto di Windows Server include automaticamente l'API di base e in più altri pacchetti, come ad esempio la raccolta dei contatori delle prestazioni e il monitoraggio della dipendenza.


3. Impostare InstrumentationKey.

    * Se è stato installato solo il pacchetto dell'API di base Microsoft.ApplicationInsights, è necessario impostare la chiave nel codice, ad esempio in main(): 

    `TelemetryConfiguration.Active.InstrumentationKey = "` *nome della chiave* `";`

    Se è stato installato uno degli altri pacchetti, è possibile impostare la chiave tramite il codice o in ApplicationInsights.config:
 
    `<InstrumentationKey>`*nome della chiave*`</InstrumentationKey>`

    Se si utilizza Applicationinsights.config, assicurarsi che le proprietà in Esplora soluzione siano impostate su **Build Action = Content, Copy to Output Directory = Copy**.

## <a name="telemetry"></a>Inserire le chiamate di telemetria

Usare l'[API di Application Insights][api] per inviare dati di telemetria. Se si utilizza l'API di base, non viene automaticamente inviata nessuna telemetria. In genere è necessario usare:

* `TrackPageView(pageName)` per il passaggio a form, pagine o schede
* `TrackEvent(eventName)` per altre azioni utente
* `TrackMetric(name, value)` in un'attività in background per inviare report periodici della metrica non associata a eventi specifici.
* `TrackTrace(logEvent)` per la [registrazione diagnostica][diagnostic]
* `TrackException(exception)` nelle clausole catch
* `Flush()` per assicurarsi che tutti i dati di telemetria vengano inviati prima della chiusura dell'app. Usare questa chiamata solo se si usa l'API di base Microsoft.ApplicationInsights. Gli SDK web implementano questo comportamento automaticamente. Se l'app è in esecuzione in contesti in cui Internet non è sempre disponibile, vedere anche [Canale di persistenza](#persistence-channel).


#### Inizializzatori di telemetria

Per visualizzare i conteggi di utenti e sessioni, è possibile impostare i valori in ciascuna istanza di `TelemetryClient`. In alternativa, è possibile usare un inizializzatore di telemetria per eseguire questa aggiunta per tutti i client:

```C#

    class UserSessionInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            telemetry.Context.User.Id = Environment.UserName;
            telemetry.Context.Session.Id = Guid.NewGuid().ToString();
        }
        
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>Eseguire il progetto

[Eseguire l'applicazione con F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) e usarla in modo da generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati inviati.

![](./media/app-insights-windows-services/appinsights-09eventcount.png)

Gli eventi vengono anche visualizzati nelle finestre di diagnostica e di output.

## <a name="monitor"></a>Visualizzare i dati monitorati

Tornare al pannello dell'applicazione nel portale di Azure.

I primi eventi verranno visualizzati nel [Flusso di metriche live](app-insights-metrics-explorer.md#live-metrics-stream).


## Canale di persistenza 

Se l'app viene eseguita in ambienti in cui la connessione Internet non è sempre disponibile o è lenta, è possibile usare il canale di persistenza invece del canale in memoria predefinito.

Il canale in memoria predefinito perde tutti i dati di telemetria che non sono stati inviati prima della chiusura dell'app. Sebbene sia possibile utilizzare `Flush()` per tentare di inviare i dati rimanenti nel buffer, i dati andranno persi comunque se non è presente alcuna connessione a Internet o se l'app viene chiusa prima del completamento della trasmissione.

Al contrario, il canale di persistenza memorizza la telemetria in un file prima di inviare i dati al portale. `Flush()` assicura che i dati siano archiviati nel file. Gli eventuali dati non inviati prima della chiusura dell'app rimangono nel file. Al riavvio dell'app, i dati saranno inviati se è disponibile una connessione Internet. I dati si accumulano nel file per tutto il tempo necessario, finché non sarà disponibile una connessione.

### Per usare il canale di persistenza

1. Importare il pacchetto NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Includere questo codice nell'app in una posizione di inizializzazione appropriata:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Usare `telemetryClient.Flush()` prima della chiusura dell'app, per assicurare che i dati siano inviati al portale o salvati nel file.

    Si noti che Flush () è sincrono per il canale di persistenza, ma asincrono per altri canali.

 
Il canale di persistenza è ottimizzato per gli scenari con dispositivi in cui il numero di eventi prodotti dall'applicazione è relativamente piccolo e la connessione è spesso inaffidabile. Questo canale scriverà gli eventi prima sul disco in uno spazio di archiviazione affidabile e quindi proverà a inviarli.

#### Esempio

Si supponga di voler monitorare le eccezioni non gestite. Sottoscrivere l'evento `UnhandledException`. Includere nel callback una chiamata a Flush per assicurarsi che la telemetria sia persistente.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

All'arresto dell'app verrà visualizzato un file in `%LocalAppData%\Microsoft\ApplicationInsights` che contiene gli eventi compressi.
 
Al prossimo avvio dell'applicazione il canale usa questo file e recapita la telemetria ad Application Insights, se possibile.

#### Esempio di test

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


Il codice del canale di persistenza è disponibile in [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel).


## <a name="usage"></a>Passaggi successivi

[Tenere traccia dell'utilizzo dell'app][knowUsers]

[Acquisire ed eseguire ricerche nei log di diagnostica][diagnostic]

[Risoluzione dei problemi][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=AcomDC_0615_2016-->