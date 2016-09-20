<properties 
	pageTitle="Filtri e pre-elaborazione in Application Insights SDK | Microsoft Azure" 
	description="Scrivere processori e inizializzatori di telemetria per l'SDK per filtrare i dati o aggiungere proprietà prima dell'invio della telemetria al portale di Application Insights." 
	services="application-insights"
    documentationCenter="" 
	authors="beckylino" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="borooji"/>  

# Filtri e pre-elaborazione della telemetria in Application Insights SDK

*Application Insights è disponibile in anteprima.*

È possibile scrivere e configurare plug-in per Application Insights SDK per personalizzare l'acquisizione e l'elaborazione della telemetria prima che venga inviata al servizio Application Insights.

Queste funzionalità attualmente sono disponibili per ASP.NET SDK.

* Il [campionamento](app-insights-sampling.md) riduce il volume della telemetria senza effetti sulle statistiche. Tiene insieme i punti dati correlati per poter passare da uno all'altro quando si diagnostica un problema. Nel portale i conteggi totali vengono moltiplicati per compensare il campionamento.
* L'[applicazione di filtri con processori di telemetria](#filtering) consente di selezionare o di modificare i dati di telemetria nell'SDK prima che vengano inviati al server. È possibile, ad esempio, ridurre il volume della telemetria escludendo le richieste dei robot. L'applicazione di filtri è però un approccio di riduzione del traffico più semplice rispetto al campionamento. Offre un controllo maggiore su ciò che viene trasmesso, ma è necessario tenere presente che influisce sulle statistiche, ad esempio se si filtrano tutte le richieste riuscite.
* Gli [inizializzatori di telemetria aggiungono proprietà](#add-properties) a tutti i dati di telemetria inviati dall'app, inclusi quelli dei moduli standard. È possibile, ad esempio, aggiungere valori calcolati oppure i numeri di versione in base a cui filtrare i dati nel portale.
* [L'API SDK](app-insights-api-custom-events-metrics.md) viene usata per inviare metriche ed eventi personalizzati.


Prima di iniziare:

* Installare [Application Insights SDK per ASP.NET v2](app-insights-asp-net.md) nell'app.


<a name="filtering"></a>
## Filtro: ITelemetryProcessor

Questa tecnica offre un controllo più diretto su ciò che viene incluso o escluso dal flusso di telemetria. È possibile usarla insieme al campionamento oppure separatamente.

Per filtrare la telemetria, scrivere un processore di telemetria e registrarlo con l'SDK. Tutta la telemetria passa attraverso il processore ed è possibile scegliere di eliminarla dal flusso o di aggiungere le proprietà. È inclusa la telemetria dei moduli standard, ad esempio l'agente di raccolta delle richieste HTTP e l'agente di raccolta delle dipendenze, oltre alla telemetria scritta manualmente. È possibile, ad esempio, filtrare la telemetria sulle richieste dei robot o le chiamate di dipendenza riuscite.

> [AZURE.WARNING] Se si filtra la telemetria inviata dall'SDK usando i processori, le statistiche visualizzate nel portale possono essere alterate e può risultare difficile seguire gli elementi correlati.
> 
> In alternativa, valutare la possibilità di usare il [campionamento](app-insights-sampling.md).

### Creare un processore di telemetria

1. Verificare che la versione di Application Insights SDK usata nel progetto sia 2.0.0 o successiva. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni di Visual Studio e scegliere Gestisci pacchetti NuGet. In Gestione pacchetti NuGet selezionare Microsoft.ApplicationInsights.Web.

1. Per creare un filtro, implementare ITelemetryProcessor, un altro punto di estendibilità come il modulo di telemetria, l'inizializzatore di telemetria e il canale di telemetria.

    Si noti che i processori di telemetria creano una catena di elaborazione. Quando si crea un'istanza di un processore di telemetria, si passa un collegamento al processore successivo nella catena. Quando un punto dati della telemetria viene passato al metodo Process, esegue le operazioni necessarie e quindi chiama il processore di telemetria successivo nella catena.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Inserirlo in ApplicationInsights.config:

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

È la stessa sezione in cui viene inizializzato un filtro di campionamento.

È possibile passare i valori della stringa dal file .config fornendo proprietà denominate come pubbliche nella classe.

> [AZURE.WARNING] Prestare attenzione a fare corrispondere il nome del tipo e i nomi delle proprietà nel file. config ai nomi di classe e di proprietà nel codice. Se il file. config fa riferimento a un tipo inesistente o una proprietà, l’SDK potrebbe automaticamente non riuscire a inviare nessuna telemetria.

 
**In alternativa**, è possibile inizializzare il filtro nel codice. In una classe di inizializzazione adatta, ad esempio AppStart in Global.asax.cs, inserire il processore nella catena:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Gli elementi TelemetryClient creati dopo questo punto useranno i processori dell'utente.

### Filtri di esempio

#### Richieste sintetiche

Filtrare i robot e i test Web. Anche se Esplora metriche consente di filtrare le origini sintetiche, questa opzione riduce il traffico filtrandole nell'SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### Autenticazione non riuscita

Filtrare le richieste con una risposta "401".

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### Filtrare le chiamate di dipendenza remote rapide

Per diagnosticare solo le chiamate lente, filtrare quelle rapide.

> [AZURE.NOTE] In questo modo le statistiche visualizzate nel portale verranno modificate. Il grafico delle dipendenze apparirà come se tutte le chiamate di dipendenza fossero non riuscite.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### Diagnosticare i problemi di dipendenza

[Questo blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descrive un progetto per diagnosticare i problemi di dipendenza con l'invio automatico di ping regolari alle dipendenze.


<a name="add-properties"></a>
## Aggiungere proprietà: ITelemetryInitializer

Utilizzare gli inizializzatori di telemetria per definire le proprietà globali che vengono inviate con tutti i dati di telemetria; eseguire l'override del comportamento selezionato dei moduli di telemetria standard.

Ad esempio, il pacchetto Application Insights per il Web raccoglie dati di telemetria relativi alle richieste HTTP e, per impostazione predefinita, contrassegna come non riuscita qualsiasi richiesta con un codice di risposta > = 400. Tuttavia, se si vuole considerare 400 come un risultato positivo, è possibile fornire un inizializzatore di telemetria che imposti la proprietà Success.

In tal modo, verrà chiamato ogni volta che viene chiamato il metodo Track*(). Sono inclusi i metodi chiamati dai moduli di telemetria standard. Per convenzione, questi moduli non impostano le proprietà che sono già state impostate da un inizializzatore.

**Definire l'inizializzatore**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Caricare l'inizializzatore**

In ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*In alternativa,* è possibile creare un'istanza dell'inizializzatore nel codice, ad esempio nel file Global.aspx.cs:


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Vedere questo esempio nel dettaglio.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### Inizializzatori di telemetria JavaScript

*JavaScript*

Inserire un inizializzatore di telemetria immediatamente dopo il codice di inizializzazione ottenuto dal portale:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Per un riepilogo delle proprietà non personalizzate disponibili in telemetryItem, vedere il [modello di dati](app-insights-export-data-model.md#lttelemetrytypegt).

È possibile aggiungere tutti gli inizializzatori desiderati.


## ITelemetryProcessor e ITelemetryInitializer

Qual è la differenza tra processori di telemetria e inizializzatori di telemetria?

* Alcune funzioni si sovrappongono: entrambi possono essere usati per aggiungere proprietà a dati di telemetria.
* Gli inizializzatori di telemetria vengono sempre eseguiti prima dei processori di telemetria.
* I processori di telemetria consentono di sostituire o rimuovere completamente un elemento di telemetria.
* I processori di telemetria non elaborano dati di telemetria dei contatori delle prestazioni.



## Canale di persistenza 

Se l'app viene eseguita in ambienti in cui la connessione Internet non è sempre disponibile o è lenta, è possibile usare il canale di persistenza invece del canale in memoria predefinito.

Il canale in memoria predefinito perde tutti i dati di telemetria che non sono stati inviati prima della chiusura dell'app. Anche se è possibile usare `Flush()` per provare a inviare i dati rimanenti nel buffer, i dati andranno comunque persi in assenza di una connessione Internet o in caso di arresto dell'app prima del completamento della trasmissione.

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

Si supponga di voler monitorare le eccezioni non gestite. Sottoscrivere l'evento `UnhandledException`. Includere nel callback una chiamata a Flush per garantire la persistenza della telemetria.
 
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


## Documentazione di riferimento

* [Panoramica API](app-insights-api-custom-events-metrics.md)

* [Riferimento ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## Codice SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Passaggi successivi


* [Cercare eventi e log][diagnostic]
* [Campionamento](app-insights-sampling.md)
* [Risoluzione dei problemi][qna]


<!--Link references-->  

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0907_2016-->