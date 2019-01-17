---
title: Filtri e pre-elaborazione in Azure Application Insights SDK | Microsoft Docs
description: Scrivere processori e inizializzatori di telemetria per l'SDK per filtrare i dati o aggiungere proprietà prima dell'invio della telemetria al portale di Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 1b55a2b053b86d3260fdca201357445d2556c444
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199035"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtri e pre-elaborazione della telemetria in Application Insights SDK


È possibile scrivere e configurare plug-in per Application Insights SDK per personalizzare l'acquisizione e l'elaborazione della telemetria prima che venga inviata al servizio Application Insights.

* [campionamento](../../azure-monitor/app/sampling.md) riduce il volume della telemetria senza effetti sulle statistiche. Tiene insieme i punti dati correlati per poter passare da uno all'altro quando si diagnostica un problema. Nel portale i conteggi totali vengono moltiplicati per compensare il campionamento.
* L'applicazione di filtri con processori di telemetria [per ASP.NET](#filtering) o [Java](../../azure-monitor/app/java-filter-telemetry.md) consente di selezionare o di modificare i dati di telemetria nell'SDK prima che vengano inviati al server. È possibile, ad esempio, ridurre il volume della telemetria escludendo le richieste dei robot. L'applicazione di filtri è però un approccio di riduzione del traffico più semplice rispetto al campionamento. Offre un controllo maggiore su ciò che viene trasmesso, ma è necessario tenere presente che influisce sulle statistiche, ad esempio se si filtrano tutte le richieste riuscite.
* [inizializzatori di telemetria aggiungono proprietà](#add-properties) a tutti i dati di telemetria inviati dall'app, inclusi quelli dei moduli standard. È possibile, ad esempio, aggiungere valori calcolati oppure i numeri di versione in base a cui filtrare i dati nel portale.
* [L'API SDK](../../azure-monitor/app/api-custom-events-metrics.md) viene usata per inviare metriche ed eventi personalizzati.

Prima di iniziare:

* Installare Application Insights [SDK per ASP.NET](../../azure-monitor/app/asp-net.md) o [SDK per Java](../../azure-monitor/app/java-get-started.md) nell'app.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtri: ITelemetryProcessor
Questa tecnica offre un controllo più diretto su ciò che viene incluso o escluso dal flusso di telemetria. È possibile usarla insieme al campionamento oppure separatamente.

Per filtrare la telemetria, scrivere un processore di telemetria e registrarlo con l'SDK. Tutta la telemetria passa attraverso il processore ed è possibile scegliere di eliminarla dal flusso o di aggiungere le proprietà. È inclusa la telemetria dei moduli standard, ad esempio l'agente di raccolta delle richieste HTTP e l'agente di raccolta delle dipendenze, oltre alla telemetria scritta manualmente. È possibile, ad esempio, filtrare la telemetria sulle richieste dei robot o le chiamate di dipendenza riuscite.

> [!WARNING]
> Se si filtra la telemetria inviata dall'SDK usando i processori, le statistiche visualizzate nel portale possono essere alterate e può risultare difficile seguire gli elementi correlati.
>
> In alternativa, valutare la possibilità di usare il [campionamento](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Creare un processore di telemetria (C#)
1. Verificare che la versione di Application Insights SDK usata nel progetto sia 2.0.0 o successiva. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni di Visual Studio e scegliere Gestisci pacchetti NuGet. In Gestione pacchetti NuGet selezionare Microsoft.ApplicationInsights.Web.
2. Per creare un filtro, implementare ITelemetryProcessor, un altro punto di estendibilità come il modulo di telemetria, l'inizializzatore di telemetria e il canale di telemetria.

    Si noti che i processori di telemetria creano una catena di elaborazione. Quando si crea un'istanza di un processore di telemetria, si passa un collegamento al processore successivo nella catena. Quando un punto dati della telemetria viene passato al metodo Process, esegue le operazioni necessarie e quindi chiama il processore di telemetria successivo nella catena.

    ```csharp

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
3. Inserirlo in ApplicationInsights.config:

```xml

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

È la stessa sezione in cui viene inizializzato un filtro di campionamento.

È possibile passare i valori della stringa dal file .config fornendo proprietà denominate come pubbliche nella classe.

> [!WARNING]
> Prestare attenzione a fare corrispondere il nome del tipo e i nomi delle proprietà nel file. config ai nomi di classe e di proprietà nel codice. Se il file. config fa riferimento a un tipo inesistente o una proprietà, l’SDK potrebbe automaticamente non riuscire a inviare nessuna telemetria.
>
>

**In alternativa** , è possibile inizializzare il filtro nel codice. In una classe di inizializzazione adatta, ad esempio AppStart in Global.asax.cs, inserire il processore nella catena:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Gli elementi TelemetryClient creati dopo questo punto useranno i processori dell'utente.

### <a name="example-filters"></a>Filtri di esempio
#### <a name="synthetic-requests"></a>Richieste sintetiche
Filtrare i robot e i test Web. Anche se Esplora metriche consente di filtrare le origini sintetiche, questa opzione riduce il traffico filtrandole nell'SDK.

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
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrare le chiamate di dipendenza remote rapide
Per diagnosticare solo le chiamate lente, filtrare quelle rapide.

> [!NOTE]
> In questo modo le statistiche visualizzate nel portale verranno modificate. Il grafico delle dipendenze apparirà come se tutte le chiamate di dipendenza fossero non riuscite.
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

## <a name="add-properties-itelemetryinitializer"></a>Aggiungere le proprietà: ITelemetryInitializer
Utilizzare gli inizializzatori di telemetria per definire le proprietà globali che vengono inviate con tutti i dati di telemetria; eseguire l'override del comportamento selezionato dei moduli di telemetria standard.

Ad esempio, il pacchetto Application Insights per il Web raccoglie dati di telemetria relativi alle richieste HTTP e, per impostazione predefinita, contrassegna come non riuscita qualsiasi richiesta con un codice di risposta > = 400. Tuttavia, se si vuole considerare 400 come un risultato positivo, è possibile fornire un inizializzatore di telemetria che imposti la proprietà Success.

In tal modo, l'inizializzatore di telemetria verrà chiamato ogni volta che viene chiamato uno dei metodi Track*(). Sono inclusi i metodi chiamati dai moduli di telemetria standard. Per convenzione, questi moduli non impostano le proprietà che sono già state impostate da un inizializzatore.

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
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Caricare l'inizializzatore**

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
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Vedere questo esempio nel dettaglio.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

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

                // To check the telemetry item�s type - for example PageView:
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

È possibile aggiungere tutti gli inizializzatori desiderati.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer
Qual è la differenza tra processori di telemetria e inizializzatori di telemetria?

* Alcune funzioni si sovrappongono: entrambi possono essere usati per aggiungere proprietà a dati di telemetria.
* Gli inizializzatori di telemetria vengono sempre eseguiti prima dei processori di telemetria.
* I processori di telemetria consentono di sostituire o rimuovere completamente un elemento di telemetria.
* I processori di telemetria non elaborano dati di telemetria dei contatori delle prestazioni.

## <a name="troubleshooting-applicationinsightsconfig"></a>Risoluzione dei problemi relativi a ApplicationInsights.config
* Verificare che il nome di tipo completo e il nome di assembly siano corretti.
* Verificare che il file applicationinsights.config si trovi nella directory di output e includa le ultime modifiche apportate.

## <a name="reference-docs"></a>Documentazione di riferimento
* [Panoramica API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Riferimento ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Codice SDK
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Passaggi successivi
* [Ricerca di eventi e log](../../azure-monitor/app/diagnostic-search.md)
* [Campionamento](../../azure-monitor/app/sampling.md)
* [Risoluzione dei problemi](../../azure-monitor/app/troubleshoot-faq.md)
