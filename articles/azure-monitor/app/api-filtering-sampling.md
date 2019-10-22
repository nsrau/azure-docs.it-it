---
title: Filtri e pre-elaborazione in Azure Application Insights SDK | Microsoft Docs
description: Scrivere processori e inizializzatori di telemetria per l'SDK per filtrare i dati o aggiungere proprietà prima dell'invio della telemetria al portale di Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/23/2016
ms.openlocfilehash: 1e02e227180bb0082dd87ab8f5d2fe64e19b60f2
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677816"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtri e pre-elaborazione della telemetria in Application Insights SDK

È possibile scrivere e configurare i plug-in per il Application Insights SDK per personalizzare il modo in cui i dati di telemetria possono essere arricchiti ed elaborati prima di essere inviati al servizio Application Insights.

* [campionamento](sampling.md) riduce il volume della telemetria senza effetti sulle statistiche. Tiene insieme i punti dati correlati per poter passare da uno all'altro quando si diagnostica un problema. Nel portale i conteggi totali vengono moltiplicati per compensare il campionamento.
* L'applicazione di filtri con processori di telemetria consente di filtrare i dati di telemetria nell'SDK prima che vengano inviati al server. È possibile, ad esempio, ridurre il volume della telemetria escludendo le richieste dei robot. Il filtro è un approccio più semplice per ridurre il traffico rispetto al campionamento. Offre un controllo maggiore su ciò che viene trasmesso, ma è necessario tenere presente che influisce sulle statistiche, ad esempio se si filtrano tutte le richieste riuscite.
* Gli [inizializzatori di telemetria aggiungono o modificano le proprietà](#add-properties) ai dati di telemetria inviati dall'app, inclusi i dati di telemetria dei moduli standard. È possibile, ad esempio, aggiungere valori calcolati oppure i numeri di versione in base a cui filtrare i dati nel portale.
* [L'API SDK](../../azure-monitor/app/api-custom-events-metrics.md) viene usata per inviare metriche ed eventi personalizzati.

Prima di iniziare:

* Installare l'SDK appropriato per l'applicazione: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [non http/Worker per .NET/.NET Core](worker-service.md)o [Java](../../azure-monitor/app/java-get-started.md).

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtro: ITelemetryProcessor

Questa tecnica offre il controllo diretto sugli elementi inclusi o esclusi dal flusso di dati di telemetria. È possibile usare il filtro per eliminare gli elementi di telemetria dall'invio a Application Insights. È possibile usarla insieme al campionamento oppure separatamente.

Per filtrare i dati di telemetria, scrivere un processore di telemetria e registrarlo con il `TelemetryConfiguration`. Tutti i dati di telemetria passano attraverso il processore ed è possibile scegliere di eliminarli dal flusso o assegnarli al processore successivo nella catena. Sono inclusi i dati di telemetria dei moduli standard come l'agente di raccolta delle richieste HTTP e l'agente di raccolta delle dipendenze e i dati di telemetria rilevati. È possibile, ad esempio, filtrare la telemetria sulle richieste dei robot o le chiamate di dipendenza riuscite.

> [!WARNING]
> Se si filtra la telemetria inviata dall'SDK usando i processori, le statistiche visualizzate nel portale possono essere alterate e può risultare difficile seguire gli elementi correlati.
>
> In alternativa, valutare la possibilità di usare il [campionamento](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Creare un processore di telemetria (C#)

1. Per creare un filtro, implementare `ITelemetryProcessor`.

    Si noti che i processori di telemetria creano una catena di elaborazione. Quando si crea un'istanza di un processore di telemetria, viene fornito un riferimento al processore successivo nella catena. Quando un punto dati di telemetria viene passato al metodo Process, esegue le operazioni e quindi chiama (o non chiama) il processore di telemetria successivo nella catena.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Aggiungere il processore.

**App ASP.NET** Inserire il frammento di codice in ApplicationInsights. config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

È possibile passare i valori della stringa dal file .config fornendo proprietà denominate come pubbliche nella classe.

> [!WARNING]
> Prestare attenzione a fare corrispondere il nome del tipo e i nomi delle proprietà nel file. config ai nomi di classe e di proprietà nel codice. Se il file. config fa riferimento a un tipo inesistente o una proprietà, l’SDK potrebbe automaticamente non riuscire a inviare nessuna telemetria.
>

**In alternativa** , è possibile inizializzare il filtro nel codice. In una classe di inizializzazione appropriata, ad esempio AppStart in `Global.asax.cs`, inserire il processore nella catena:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Gli elementi TelemetryClient creati dopo questo punto useranno i processori dell'utente.

**App del servizio ASP.NET Core/Worker**

> [!NOTE]
> L'aggiunta di un processore con `ApplicationInsights.config` o con `TelemetryConfiguration.Active` non è valida per le applicazioni ASP.NET Core o se si usa Microsoft. ApplicationInsights. WorkerService SDK.

Per le app scritte con [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) o [WorkerService](worker-service.md#adding-telemetry-processors), l'aggiunta di una nuova `TelemetryProcessor` viene eseguita usando `AddApplicationInsightsTelemetryProcessor` metodo di estensione su `IServiceCollection`, come illustrato di seguito. Questo metodo viene chiamato nel metodo `ConfigureServices` della classe `Startup.cs`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Filtri di esempio

#### <a name="synthetic-requests"></a>Richieste sintetiche

Filtrare i robot e i test Web. Anche se Esplora metriche offre la possibilità di filtrare le origini sintetiche, questa opzione riduce il traffico e le dimensioni di inserimento filtrando gli stessi nell'SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Autenticazione non riuscita

Filtrare le richieste con una risposta "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrare le chiamate di dipendenza remote rapide

Per diagnosticare solo le chiamate lente, filtrare quelle rapide.

> [!NOTE]
> In questo modo le statistiche visualizzate nel portale verranno modificate.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnosticare i problemi di dipendenza

[Questo blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descrive un progetto per diagnosticare i problemi di dipendenza con l'invio automatico di ping regolari alle dipendenze.

<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Aggiungere proprietà: ITelemetryInitializer

Usare gli inizializzatori di telemetria per arricchire la telemetria con altre informazioni e/o per eseguire l'override delle proprietà di telemetria impostate dai moduli di telemetria standard.

Il Application Insights per il pacchetto Web, ad esempio, raccoglie i dati di telemetria sulle richieste HTTP. per impostazione predefinita, contrassegna come non riuscita qualsiasi richiesta con un codice di risposta > = 400. Tuttavia, se si vuole considerare 400 come un risultato positivo, è possibile fornire un inizializzatore di telemetria che imposti la proprietà Success.

In tal modo, l'inizializzatore di telemetria verrà chiamato ogni volta che viene chiamato uno dei metodi Track*(). Sono inclusi `Track()` metodi chiamati dai moduli di telemetria standard. Per convenzione, questi moduli non impostano le proprietà che sono già state impostate da un inizializzatore. Gli inizializzatori di telemetria vengono chiamati prima di chiamare i processori di telemetria. Quindi, eventuali arricchimenti eseguiti dagli inizializzatori sono visibili ai processori.

**Definire l'inizializzatore**

*C#*

```csharp
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
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**App ASP.NET: caricare l'inizializzatore**

In ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*In alternativa,* è possibile creare un'istanza dell'inizializzatore nel codice, ad esempio nel file Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Vedere questo esempio nel dettaglio.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**App del servizio ASP.NET Core/Worker: caricare l'inizializzatore**

> [!NOTE]
> L'aggiunta di un inizializzatore usando `ApplicationInsights.config` o l'uso di `TelemetryConfiguration.Active` non è valida per le applicazioni ASP.NET Core o se si usa Microsoft. ApplicationInsights. WorkerService SDK.

Per le app scritte con [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) o [WorkerService](worker-service.md#adding-telemetryinitializers), l'aggiunta di una nuova `TelemetryInitializer` viene eseguita aggiungendola al contenitore di inserimento delle dipendenze, come illustrato di seguito. Questa operazione viene eseguita nel metodo `Startup.ConfigureServices`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Inizializzatori di telemetria Java

[Documentazione di Java SDK](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Registrare quindi l'inizializzatore personalizzato nel file applicationinsights.xml.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Inizializzatori di telemetria JavaScript
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

            // To check the telemetry items type - for example PageView:
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

Per un riepilogo delle proprietà non personalizzate disponibili in telemetryItem, vedere [Modello di dati di esportazione di Application Insights](../../azure-monitor/app/export-data-model.md).

È possibile aggiungere tutti gli inizializzatori desiderati e vengono chiamati nell'ordine in cui vengono aggiunti.

### <a name="example-telemetryinitializers"></a>TelemetryInitializers di esempio

#### <a name="add-custom-property"></a>Aggiungi proprietà personalizzata

L'inizializzatore di esempio seguente aggiunge una proprietà personalizzata a tutti i dati di telemetria rilevati.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Aggiungere il nome del ruolo Cloud

Il seguente inizializzatore di esempio imposta il nome del ruolo Cloud su ogni telemetria rilevata.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

Qual è la differenza tra processori di telemetria e inizializzatori di telemetria?

* Sono presenti alcune sovrapposizioni in ciò che è possibile fare con questi elementi: entrambi possono essere usati per aggiungere o modificare le proprietà dei dati di telemetria, anche se è consigliabile usare gli inizializzatori a tale scopo.
* Gli inizializzatori di telemetria vengono sempre eseguiti prima dei processori di telemetria.
* TelemetryInitializers può essere chiamato più di una volta. Per convenzione, non imposta alcuna proprietà che sia già stata impostata.
* I processori di telemetria consentono di sostituire o rimuovere completamente un elemento di telemetria.
* Tutti i TelemetryInitializers registrati vengono chiamati per ogni elemento di telemetria. Per i processori di telemetria, SDK garantisce la chiamata del primo processore di telemetria. Se il resto dei processori viene chiamato o meno, viene deciso dai precedenti processori di telemetria.
* Usare TelemetryInitializers per arricchire la telemetria con proprietà aggiuntive o per eseguire l'override di quella esistente. Usare TelemetryProcessor per filtrare i dati di telemetria.

## <a name="troubleshooting-applicationinsightsconfig"></a>Risoluzione dei problemi relativi a ApplicationInsights.config

* Verificare che il nome di tipo completo e il nome di assembly siano corretti.
* Verificare che il file applicationinsights.config si trovi nella directory di output e includa le ultime modifiche apportate.

## <a name="reference-docs"></a>Documenti di riferimento

* [Panoramica API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Riferimento ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Codice SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Passaggi successivi
* [Ricerca di eventi e log](../../azure-monitor/app/diagnostic-search.md)
* [Campionamento](../../azure-monitor/app/sampling.md)
* [risoluzione dei problemi](../../azure-monitor/app/troubleshoot-faq.md)
