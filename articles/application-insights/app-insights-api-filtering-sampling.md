<properties 
	pageTitle="Campionamento, filtro e pre-elaborazione in Application Insights SDK" 
	description="Scrivere plug-in per l'SDK per filtrare, campionare o aggiungere proprietà ai dati prima che la telemetria venga inviata al portale di Application Insights." 
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
	ms.date="11/04/2015" 
	ms.author="awills"/>

# Campionamento, filtro e pre-elaborazione della telemetria in Application Insights SDK

*Application Insights è disponibile in anteprima.*

È possibile scrivere e configurare plug-in per Application Insights SDK per personalizzare l'acquisizione e l'elaborazione della telemetria prima che venga inviata al servizio Application Insights.

Queste funzionalità attualmente sono disponibili per ASP.NET SDK.

* Il [campionamento](#sampling) riduce il volume della telemetria senza effetti sulle statistiche. Tiene insieme i punti dati correlati per poter passare da uno all'altro quando si diagnostica un problema. Nel portale i conteggi totali vengono moltiplicati per compensare il campionamento.
 * Il campionamento a frequenza fissa consente di determinare la percentuale di eventi trasmessi.
 * Il campionamento adattivo (il valore predefinito per ASP.NET SDK da 2.0.0 Beta 3) regola automaticamente la frequenza di campionamento in base al volume della telemetria. È possibile impostare un volume di destinazione.
* I [filtri](#filtering) consentono di selezionare o di modificare la telemetria nell'SDK prima che venga inviata al server. È possibile, ad esempio, ridurre il volume della telemetria escludendo le richieste dei robot. Questo approccio alla riduzione del traffico è più semplice del campionamento. Offre un controllo maggiore su ciò che viene trasmesso, ma occorre tenere presente che avrà effetto sulle statistiche, ad esempio se si filtrano tutte le richieste riuscite.
* [Aggiunta di proprietà](#add-properties) a qualsiasi telemetria inviata dall'app, inclusa la telemetria dai moduli standard. È possibile, ad esempio, aggiungere valori calcolati oppure i numeri di versione in base a cui filtrare i dati nel portale.
* [L'API SDK](app-insights-api-custom-events-metrics.md) viene usata per inviare metriche ed eventi personalizzati.

Prima di iniziare:

* Installare nell'app [Application Insights SDK](app-insights-asp-net.md), Installare manualmente i pacchetti NuGet e selezionare la versione *preliminare* più recente.
* Provare l'[API Application Insights](app-insights-api-custom-events-metrics.md). 


## Campionamento

*Questa funzionalità è nella versione beta.*

Il [campionamento](app-insights-sampling.md) è il modo consigliato per ridurre il traffico mantenendo accurate le statistiche. Il filtro seleziona gli elementi correlati per poter passare da uno all'altro nella diagnosi. I conteggi eventi vengono modificati in Esplora metriche per compensare gli elementi filtrati.

* È consigliabile usare il campionamento adattivo. Regola automaticamente la percentuale di campionamento per raggiungere un volume specifico di richieste. Attualmente disponibile solo per la telemetria lato server di ASP.NET.  
* Il [Campionamento a frequenza fissa](app-insights-sampling.md) è inoltre disponibile. Specificare la percentuale di campionamento. Disponibile per il codice dell'app Web ASP.NET e per le pagine Web JavaScript. Il client e il server sincronizzeranno il rispettivo campionamento in modo che nella ricerca sia possibile spostarsi tra le visualizzazioni pagina e le richieste correlate.

### Per abilitare il campionamento

**Aggiornare i pacchetti del progetto NuGet** all'ultima versione *preliminare* di Application Insights: fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, scegliere Gestisci pacchetti NuGet, selezionare **Includi versione preliminare** e cercare Microsoft.ApplicationInsights.Web.

In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) è possibile regolare la frequenza massima della telemetria che l'algoritmo adattivo deve raggiungere:

    <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>

### Campionamento lato client

Per ottenere il campionamento a frequenza fissa sui dati dalle pagine Web, immettere una riga aggiuntiva nel [frammento di Application Insights](app-insights-javascript.md) inserito (in genere una pagina master, ad esempio \_Layout.cshtml):

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Impostare una percentuale (10 in questo esempio) uguale a 100/N dove N è un numero intero, ad esempio 50 (=100/2), 33,33 (=100/3), 25 (=100/4) o 10 (=100/10). 
* Se si abilita il [campionamento a frequenza fissa](app-insights-sampling.md) sul lato server, il client e il server sincronizzeranno il rispettivo campionamento in modo che nella ricerca sia possibile spostarsi tra le visualizzazioni pagina e le richieste correlate.

[Altre informazioni sul campionamento](app-insights-sampling.md).

## Filtri

Questa tecnica offre un controllo più diretto su ciò che viene incluso o escluso dal flusso di telemetria. È possibile usarla insieme al campionamento oppure separatamente.

Per filtrare la telemetria, scrivere un processore di telemetria e registrarlo con l'SDK. Tutta la telemetria passa attraverso il processore ed è possibile scegliere di eliminarla dal flusso o di aggiungere le proprietà. È inclusa la telemetria dei moduli standard, ad esempio l'agente di raccolta delle richieste HTTP e l'agente di raccolta delle dipendenze, oltre alla telemetria scritta manualmente. È possibile, ad esempio, filtrare la telemetria sulle richieste dei robot o le chiamate di dipendenza riuscite.

> [AZURE.WARNING]Se si filtra la telemetria inviata dall'SDK usando i processori, le statistiche visualizzate nel portale possono essere alterate e può risultare difficile seguire gli elementi correlati.
> 
> In alternativa, valutare la possibilità di usare il [campionamento](#sampling).

### Creare un processore di telemetria

1. Aggiornamento di Application Insights SDK alla versione più recente (2.0.0 Beta 2 o versione successiva). Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni di Visual Studio e scegliere Gestisci pacchetti NuGet. In gestione pacchetti NuGet selezionare **Includi versione preliminare** e cercare Microsoft.ApplicationInsights.Web.

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

(Si noti che questa è la stessa sezione utilizzata per inizializzare un filtro di campionamento.)

È possibile passare i valori della stringa dal file .config fornendo proprietà denominate come pubbliche nella classe.

> [AZURE.WARNING]Prestare attenzione a fare corrispondere il nome del tipo e i nomi delle proprietà nel file. config ai nomi di classe e di proprietà nel codice. Se il file. config fa riferimento a un tipo inesistente o una proprietà, l’SDK potrebbe automaticamente non riuscire a inviare nessuna telemetria.

 
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


## Aggiungere le proprietà

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


## Documentazione di riferimento

* [Panoramica API](app-insights-api-custom-events-metrics.md)

* [Riferimento ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## Codice SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


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
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0121_2016-->