<properties 
	pageTitle="API di Application Insights per metriche ed eventi personalizzati" 
	description="Inserire alcune righe di codice nell'app desktop o per dispositivi, nella pagina Web o nel servizio per tenere traccia dell'utilizzo e diagnosticare i problemi." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Elaborazione della telemetria in Application Insights SDK

*Application Insights è disponibile in anteprima.*

È possibile scrivere e configurare plug-in per Application Insights SDK per personalizzare l'acquisizione e l'elaborazione della telemetria prima che venga inviata al servizio Application Insights.

* Il [campionamento](#sampling) riduce il volume della telemetria senza alterare le statistiche. Tiene insieme i punti dati correlati per facilitare la ricerca.
* I [processori di telemetria](#telemetry-processors) inizializzano, filtrano e campionano i dati nell'SDK prima che vengano inviati al server. È possibile, ad esempio, aggiungere proprietà calcolate a una telemetria o ridurre il volume della telemetria escludendo le richieste dei robot. 
* Gli [inizializzatori di telemetria](#telemetry-initializers) aggiungono proprietà predefinite a tutta la telemetria inviata e possono eseguire l'override di parte del comportamento dei moduli di telemetria standard. Dato che le proprietà vengono valutate per ogni chiamata di rilevamento, è possibile cambiare questi valori.
* Anche gli [inizializzatori di contesto](#context-initializers) impostano proprietà globali. Le proprietà vengono impostate una sola volta, in fase di avvio, e non possono più essere modificate. Questi inizializzatori, anche se permettono di risparmiare un po' di tempo di CPU (diversamente dagli inizializzatori di telemetria), sono anche molto poco flessibili.
* [L'API SDK](app-insights-api-custom-events-metrics.md) viene usata per inviare metriche ed eventi personalizzati.

Esiste una ridondanza tra questi diversi meccanismi ed è probabile che in futuro gli inizializzatori verranno modificati.

Prima di iniziare:

* Installare nell'app [Application Insights SDK](app-insights-asp-net.md), necessario per i processori di telemetria. 
* Provare l'[API Application Insights](app-insights-api-custom-events-metrics.md). 


## Campionamento

*Questa funzionalità è nella versione beta.*

Modo consigliato per ridurre il traffico mantenendo accurate le statistiche. Il filtro seleziona gli elementi correlati per poter passare da uno all'altro nella diagnosi. I conteggi eventi vengono modificati in Esplora metriche per compensare gli elementi filtrati.

Per i server ASP.NET, inserire il codice seguente in una posizione di inizializzazione appropriata, ad esempio Application\_Start:

*C#*

```C#

    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```

Per le pagine Web, immettere una riga aggiuntiva nel [frammento di Application Insights](app-insights-javascript.md) inserito (in genere una pagina master, ad esempio \_Layout.cshtml):

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Impostare una percentuale (10 in questi esempi) uguale a 100/N dove N è un numero intero, ad esempio 50 (=1/2), 33,33 (=1/3), 25 (=1/4), 10 (=1/5).
* Se si imposta il campionamento sia nella pagina Web che nel server, assicurarsi di impostare la stessa percentuale di campionamento in entrambi.
* I lati client e server coordineranno la selezione degli elementi correlati.

[Altre informazioni sul campionamento](app-insights-sampling.md).

## Processori di telemetria

Scrivere processori di telemetria per filtrare e pre-elaborare la telemetria generata da Application Insights SDK prima che venga inviata al portale back-end. È inclusa la telemetria dei moduli standard, ad esempio l'agente di raccolta delle richieste HTTP e l'agente di raccolta delle dipendenze. È possibile, ad esempio, filtrare la telemetria sulle richieste dei robot o le chiamate di dipendenza riuscite.

> [AZURE.WARNING]Se si filtra la telemetria inviata dall'SDK usando i processori, le statistiche visualizzate nel portale possono essere alterate e può risultare difficile seguire gli elementi correlati.
> 
> In alternativa, valutare la possibilità di usare il [campionamento](#sampling).

### Creare un processore di telemetria

1. Per creare un filtro, implementare ITelemetryProcessor, un altro punto di estendibilità come il modulo di telemetria, l'inizializzatore di telemetria e il canale di telemetria. 

    Si noti che i processori di telemetria creano una catena di elaborazione. Quando si crea un'istanza di un processore di telemetria, si passa un collegamento al processore successivo nella catena. Quando un punto dati della telemetria viene passato al metodo Process, esegue le operazioni necessarie e quindi chiama il processore di telemetria successivo nella catena.

    ``` C#

    namespace FilteringTelemetryProcessor
    {
      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.DataContracts;

      class UnauthorizedRequestFilteringProcessor : ITelemetryProcessor
      {
        public UnauthorizedRequestFilteringProcessor(ITelemetryProcessor next)
		//Initialization will fail without this constructor. Link processors to each other
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
        }      private ITelemetryProcessor Next { get; set; }
      }
    }

    ```
2. In una classe di inizializzazione adatta, ad esempio AppStart in Global.asax.cs, inserire il processore nella catena:

    ```C#

    var builder = new TelemetryChannelBuilder();
    builder.Use((next) => new UnauthorizedRequestFilteringProcessor(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    TelemetryConfiguration.Active.TelemetryChannel = builder.Build();

    ```

    Gli elementi TelemetryClient creati dopo questo punto useranno i processori dell'utente.

### Filtri di esempio

#### Richieste sintetiche

Filtrare i robot e i test Web. Anche se Esplora metriche consente di filtrare le origini sintetiche, questa opzione riduce il traffico filtrandole nell'SDK.

``` C#

public void Process(ITelemetry item)
{
    if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource))
    { return; }

    this.Next.Process(item);
}

```

#### Autenticazione non riuscita

Filtrare le richieste con risposta "401".

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

> [AZURE.NOTE]In questo modo le statistiche visualizzate nel portale verranno modificate. Il grafico delle dipendenze apparirà come se tutte le chiamate di dipendenza fossero non riuscite.

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


## Inizializzatori di telemetria

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

Per un riepilogo delle proprietà non personalizzate disponibili in telemetryItem, vedere il [modello di dati](app-insights-export-data-model.md/#lttelemetrytypegt).

È possibile aggiungere tutti gli inizializzatori desiderati.






## <a name="default-properties"></a>Inizializzatori di contesto - Impostare proprietà predefinite per tutti i dati di telemetria

È possibile impostare un inizializzatore universale in modo che tutti i nuovi TelemetryClient usino automaticamente il contesto. Sono inclusi i dati di telemetria standard inviati dai moduli di telemetria specifici della piattaforma, ad esempio la traccia delle richieste del server Web.

Un uso tipico consiste nell'identificare i dati di telemetria provenienti da diverse versioni o componenti dell'app. Nel portale, è possibile filtrare o raggruppare i risultati in base alla proprietà "Application Version".

In generale, [è consigliabile usare gli inizializzatori di telemetria invece degli inizializzatori di contesto](http://apmtips.com/blog/2015/06/09/do-not-use-context-initializers/).

#### Definire un inizializzatore di contesto


*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MyNamespace
    {
      // Context initializer class
      public class MyContextInitializer : IContextInitializer
      {
        public void Initialize (TelemetryContext context)
        {
            if (context == null) return;

            context.Component.Version = "v2.1";
        }
      }
    }
```

*Java*

```Java

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyContextInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.Component.Version = "2.1";
      }
    }
```

#### Caricare l'inizializzatore di contesto

In ApplicationInsights.config:

    <ApplicationInsights>
      <ContextInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MyNamespace.MyContextInitializer, MyAssemblyName"/> 
        ...
      </ContextInitializers>
    </ApplicationInsights>

*In alternativa,* è possibile creare un'istanza dell'inizializzatore nel codice:

*C#*

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyContextInitializer());
    }
```

*Java*

```Java

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyContextInitializer());
```



## Documentazione di riferimento

* [Panoramica API](app-insights-api-custom-events-metrics.md)

* [Riferimento ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Riferimento Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Informazioni di riferimento su JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)


## Codice SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [SDK per Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)
* [Tutte le piattaforme](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## Domande

* *Le chiamate Track\_() quali eccezioni potrebbero generare?*
    
    Nessuna. Non è necessario eseguirne il wrapping in clausole try-catch. Se l'SDK rileva un problema, registrerà un messaggio che verrà visualizzato nell'output della console di debug e quindi nella ricerca diagnostica per approfondirne i dettagli.



* *Esiste un'API REST?*

    Sì, ma è non ancora pubblicata.

## <a name="next"></a>Passaggi successivi


[Cercare eventi e log][diagnostic]

[Esempi e procedure dettagliate](app-insights-code-samples.md)

[Risoluzione dei problemi][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO4-->