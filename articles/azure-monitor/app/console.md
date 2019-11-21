---
title: Azure Application Insights per le applicazioni console | Microsoft Docs
description: Monitorare la disponibilità, le prestazioni e l'utilizzo delle applicazioni Web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/19/2019
ms.reviewer: lmolkova
ms.openlocfilehash: ee8dabcc957364bade36608067aad568662c24ae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232727"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights per le applicazioni console .NET

[Application Insights](../../azure-monitor/app/app-insights-overview.md) consente di monitorare la disponibilità, le prestazioni e l'uso dell'applicazione Web.

È necessaria una sottoscrizione a [Microsoft Azure](https://azure.com). È possibile accedere con un account Microsoft, che in genere si ottiene per Windows, XBox Live o altri servizi cloud Microsoft. Se il team ha una sottoscrizione di Azure per l'organizzazione, chiedere al proprietario di aggiungere l'utente alla sottoscrizione usando il rispettivo account Microsoft.

> [!NOTE]
> There is a new Application Insights SDK called [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) which can used to enable Application Insights for any Console Applications. It is recommended to use this package and associated instructions from [here](../../azure-monitor/app/worker-service.md). This package targets [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard), and hence can be used in .NET Core 2.0 or higher, and .NET Framework 4.7.2 or higher.

## <a name="getting-started"></a>Inizia ora

* Nel [portale di Azure](https://portal.azure.com) [creare una risorsa di Application Insights](../../azure-monitor/app/create-new-resource.md). Per il tipo di applicazione, scegliere **Generale**.
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
> Reading config file is not supported on .NET Core. You may consider using [Application Insights SDK for ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

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

If you created configuration with plain `TelemetryConfiguration()` constructor, you need to enable correlation support additionally. **It is not needed** if you read configuration from file, used `TelemetryConfiguration.CreateDefault()` or `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* You may also want to install and initialize Performance Counter collector module as described [here](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


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

            // flush is not blocking so wait a bit
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
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

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
* [Monitoraggio delle dipendenze](../../azure-monitor/app/asp-net-dependencies.md) per vedere se REST, SQL o altre risorse esterne rallentano l'utente.
* [Utilizzare l'API](../../azure-monitor/app/api-custom-events-metrics.md) per inviare gli eventi e le metriche per una visualizzazione più dettagliata dell'utilizzo e delle prestazioni dell'app.
