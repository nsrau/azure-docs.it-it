<properties 
	pageTitle="Application Insights per servizi e app desktop di Windows" 
	description="Analizzare l'uso e le prestazioni dell'app desktop di Windows con Application Insights." 
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
	ms.date="09/07/2015" 
	ms.author="awills"/>

# Application Insights in app desktop, servizi e ruoli di lavoro di Windows

*Application Insights è disponibile in anteprima.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights consente di monitorare un'applicazione distribuita in base all'uso e alle prestazioni.

Tutte le applicazioni Windows, incluse le app desktop, i servizi in background e i ruoli di lavoro, possono usare Application Insights SDK per inviare dati di telemetria ad Application Insights. È anche possibile aggiungere Application Insights SDK a un progetto di tipo libreria di classi.

L'SDK di base fornisce solo un'API: a differenza degli SDK per dispositivi o Web non include moduli che raccolgono dati automaticamente, quindi è necessario scrivere codice per l'invio di dati di telemetria personalizzati. Alcuni degli altri pacchetti, ad esempio l'agente di raccolta del contatore delle prestazioni, funzioneranno anche in un'app desktop.


## <a name="add"></a> Creare una risorsa Application Insights


1.  Nel [portale di Azure][portal] creare una nuova risorsa di Application Insights. Come tipo di applicazione scegliere l'app di Windows Store. 

    ![Fare clic su Nuovo, Application Insights](./media/app-insights-windows-desktop/01-new.png)

    La scelta del tipo di applicazione imposta il contenuto del pannello Panoramica e le proprietà disponibili nell'[area di esplorazione delle metriche][metrics].

2.  Eseguire una copia della chiave di strumentazione.

    ![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Installare SDK nell'applicazione


1. In Visual Studio è possibile modificare i pacchetti NuGet del progetto di app desktop.

    ![Fare clic con il pulsante destro del mouse sul progetto e selezionare Gestisci pacchetti NuGet](./media/app-insights-windows-desktop/03-nuget.png)

2. Installare il pacchetto dell'API di base di Application Insights, Microsoft.ApplicationInsights.

    ![Cercare "Application Insights"](./media/app-insights-windows-desktop/04-core-nuget.png)

    *È possibile usare altri pacchetti?*

    Sì, si possono installare altri pacchetti, ad esempio pacchetti di contatori delle prestazioni o di agenti di raccolta dipendenze se si vogliono usare i relativi moduli. Microsoft.ApplicationInsights.Web include molti di questi pacchetti. Per usare i [pacchetti di agenti di raccolta dati di traccia o di log](app-insights-asp-net-trace-logs.md), iniziare con il pacchetto del server Web.

    Non usare però Microsoft.ApplicationInsights.Windows, che è destinato alle app di Windows Store.

3. Impostare InstrumentationKey.

    * Se è stato installato solo il pacchetto dell'API di base Microsoft.ApplicationInsights, è necessario impostare la chiave nel codice, ad esempio in main(): 

     `TelemetryConfiguration.Active.InstrumentationKey = "`*nome della chiave*`";`

    * Se è stato installato uno degli altri pacchetti, è possibile impostare la chiave tramite il codice o in ApplicationInsights.config:
 
     `<InstrumentationKey>`*nome della chiave*`</InstrumentationKey>`



## <a name="telemetry"></a>Inserire le chiamate di telemetria

Creare un'`TelemetryClient` istanza e quindi [usarla per inviare dati di telemetria][api].


Ad esempio, in un'applicazione Windows Form, è possibile scrivere:

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

Usare l'[API di Application Insights][api] per inviare dati di telemetria. Nelle applicazioni Desktop di Windows, non vengono inviati automaticamente i dati di telemetria. In genere è necessario usare:

* `TrackPageView(pageName)` per il passaggio a form, pagine o schede
* `TrackEvent(eventName)` per altre azioni utente
* `TrackMetric(name, value)` in un'attività in background per inviare report periodici della metrica non associata a eventi specifici.
* `TrackTrace(logEvent)` per la [registrazione diagnostica][diagnostic]
* `TrackException(exception)` nelle clausole catch
* `Flush()` per assicurarsi che tutti i dati di telemetria vengano inviati prima della chiusura dell'app. Usare questa chiamata solo se si usa l'API di base Microsoft.ApplicationInsights. Gli SDK per dispositivi e Web implementano automaticamente questo comportamento. Se l'app è in esecuzione in contesti in cui Internet non è sempre disponibile, vedere anche [Canale di persistenza](#persistence-channel).


#### Inizializzatori di contesto

Per visualizzare i conteggi di utenti e sessioni, è possibile impostare i valori in ciascuna istanza di `TelemetryClient`. In alternativa, è possibile usare un inizializzatore di contesto per eseguire questa aggiunta per tutti i client:

```C#

    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>Eseguire il progetto

[Eseguire l'applicazione con F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) e usarla in modo da generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati inviati.

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>Visualizzare i dati monitorati

Tornare al pannello dell'applicazione nel portale di Azure.

I primi eventi verranno visualizzati in [Ricerca diagnostica](app-insights-diagnostic-search.md).

Se si prevedono più dati, fare clic su Aggiorna dopo pochi secondi.

Se si usa TrackMetric o il parametro delle misurazioni di TrackEvent, aprire [Esplora metriche][metrics] e quindi il pannello Filtri. Dovrebbe essere visualizzata la metrica, ma a volte il trasferimento dei dati attraverso la pipeline richiede alcuni istanti, quindi potrebbe essere necessario chiudere il pannello Filtri, attendere qualche minuto e quindi aggiornare la visualizzazione.



## Canale di persistenza 

Se l'app viene eseguita in ambienti in cui la connessione Internet non è sempre disponibile o è lenta, è possibile usare il canale di persistenza invece del canale in memoria predefinito.

Il canale in memoria predefinito perde tutti i dati di telemetria che non sono stati inviati prima della chiusura dell'app. Sebbene sia possibile utilizzare `Flush()` per tentare di inviare i dati rimanenti nel buffer, i dati andranno persi comunque se non è presente alcuna connessione a Internet o se l'app viene chiusa prima del completamento della trasmissione.

Al contrario, il canale di persistenza memorizza la telemetria in un file prima di inviare i dati al portale. `Flush()` assicura che i dati siano archiviati nel file. Gli eventuali dati non inviati prima della chiusura dell'app rimangono nel file. Al riavvio dell'app, i dati saranno inviati se è disponibile una connessione Internet. I dati si accumulano nel file per tutto il tempo necessario, finché non sarà disponibile una connessione.

### Per usare il canale di persistenza

1. Importare il pacchetto NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel).
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


Il codice del canale di persistenza è disponibile in [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/master/src/TelemetryChannels/PersistenceChannel).


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
 

<!---HONumber=Oct15_HO1-->