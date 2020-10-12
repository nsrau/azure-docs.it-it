---
title: Azure Application Insights per le applicazioni console | Microsoft Docs
description: Monitorare la disponibilità, le prestazioni e l'utilizzo delle applicazioni Web.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: 98f1ea8a4994515b437841174ae95563ea53ed88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933342"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights per le applicazioni console .NET

[Application Insights](./app-insights-overview.md) consente di monitorare la disponibilità, le prestazioni e l'uso dell'applicazione Web.

È necessaria una sottoscrizione a [Microsoft Azure](https://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft. Se il team ha una sottoscrizione di Azure per l'organizzazione, chiedere al proprietario di aggiungere l'utente alla sottoscrizione usando il rispettivo account Microsoft.

> [!NOTE]
> Si *consiglia vivamente* di usare il pacchetto [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) e le istruzioni associate da [qui](./worker-service.md) per qualsiasi applicazione console. Questo pacchetto [`NetStandard2.0`](/dotnet/standard/net-standard) è destinato a, quindi può essere usato in .NET Core 2,1 o versione successiva e .NET Framework 4.7.2 o versione successiva.

## <a name="getting-started"></a>Introduzione

* Nel [portale di Azure](https://portal.azure.com)[creare una risorsa di Application Insights](./create-new-resource.md). Per il tipo di applicazione, scegliere **Generale**.
* Eseguire una copia della chiave di strumentazione. Trovare la chiave nell'elenco a discesa **Informazioni di base** della nuova risorsa creata.
* Installare la versione più recente del pacchetto [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights).
* Impostare la chiave di strumentazione nel codice prima di tenere traccia dei dati di telemetria oppure impostare la variabile di ambiente APPINSIGHTS_INSTRUMENTATIONKEY. A questo punto si dovrebbe essere in grado di tenere traccia dei dati di telemetria manualmente e visualizzarli nel portale di Azure.

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> I dati di telemetria non vengono inviati immediatamente. Gli elementi di telemetria vengono inviati in batch e inviati da ApplicationInsights SDK. Nelle app console, che termina subito dopo avere chiamato i metodi `Track()`, i dati di telemetria potrebbero non essere inviati a meno che non siano stati eseguiti `Flush()` e `Sleep`/`Delay` prima che l'app venga chiusa come illustrato nell'[esempio completo](#full-example) più avanti in questo articolo. `Sleep` non è necessario in caso di utilizzo di `InMemoryChannel`. Si è verificato un problema attivo relativo alla necessità di `Sleep`, che viene rilevato qui: [ApplicationInsights-dotnet/issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* Installare la versione più recente del pacchetto [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector), che consente di tenere automaticamente traccia delle chiamate HTTP, SQL o di altre chiamate a dipendenze esterne.

È possibile inizializzare e configurare Application Insights dal codice o tramite il file `ApplicationInsights.config`. Verificare che l'inizializzazione venga eseguita non appena possibile. 

> [!NOTE]
> Le istruzioni che fanno riferimento ad **ApplicationInsights.config** sono applicabili solo alle app destinate al .NET Framework e non alle applicazioni .NET Core.

### <a name="using-config-file"></a>Uso del file di configurazione

Per impostazione predefinita, Application Insights SDK cerca il file `ApplicationInsights.config` nella directory di lavoro quando `TelemetryConfiguration` viene creato.

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

È inoltre possibile specificare il percorso del file di configurazione.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Per altre informazioni, vedere le [informazioni di riferimento sul file di configurazione](configuration-with-applicationinsights-config.md).

Un esempio completo del file di configurazione potrebbe essere disponibile installando la versione più recente del pacchetto [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer). Ecco la configurazione **minima** per la raccolta di dipendenze equivalente all'esempio di codice.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Configurazione della raccolta di dati di telemetria dal codice
> [!NOTE]
> La lettura del file di configurazione non è supportata in .NET Core. È possibile considerare l'uso di [Application Insights SDK per ASP.NET Core](./asp-net-core.md)

* Durante l'avvio dell'applicazione, creare e configurare l'istanza di `DependencyTrackingTelemetryModule`, che deve essere singleton ed essere conservata per la durata dell'applicazione.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Aggiungere gli inizializzatori di telemetria comuni

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Se la configurazione è stata creata con il costruttore `TelemetryConfiguration()` normale, è necessario abilitare anche il supporto per la correlazione. **Non è necessario** se si legge la configurazione da file, `TelemetryConfiguration.CreateDefault()` o `TelemetryConfiguration.Active` usati.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* È anche possibile installare e inizializzare il modulo dell'agente di raccolta dati dei contatori delle prestazioni, come descritto [qui](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>Esempio completo

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio delle dipendenze](./asp-net-dependencies.md) per vedere se REST, SQL o altre risorse esterne rallentano l'utente.
* [Utilizzare l'API](./api-custom-events-metrics.md) per inviare gli eventi e le metriche per una visualizzazione più dettagliata dell'utilizzo e delle prestazioni dell'app.

