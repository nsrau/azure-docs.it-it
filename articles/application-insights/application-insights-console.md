---
title: Azure Application Insights per le applicazioni Console | Documenti Microsoft
description: "Monitorare la disponibilità, le prestazioni e l'utilizzo delle applicazioni Web."
services: application-insights
documentationcenter: .net
author: lmolkova
manager: bfung
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 1b6bef88d729a2febfca2bd236a5382d2c11bd69
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="application-insights-for-net-console-applications"></a>Applicazioni console Application Insights per .NET
[Application Insights](app-insights-overview.md) consente di monitorare l'applicazione web per la disponibilità, prestazioni e utilizzo.

È necessaria una sottoscrizione con [Microsoft Azure](http://azure.com). Accedere con un account Microsoft, che potrebbe essere per Windows, Xbox Live o altri servizi cloud Microsoft. Se il team ha una sottoscrizione di Azure per l'organizzazione, chiedere al proprietario di aggiungere l'utente alla sottoscrizione usando il rispettivo account Microsoft.

## <a name="getting-started"></a>Introduzione

* Nel [portale di Azure](https://portal.azure.com) [creare una risorsa di Application Insights](app-insights-create-new-resource.md). Scegliere l'app ASP.NET per il tipo di applicazione.
* Eseguire una copia della chiave di strumentazione. Trovare la chiave nell'elenco a discesa Essentials della nuova risorsa creata. 
* Installare una versione più recente [Microsoft. applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) pacchetto.
* Impostare la chiave di strumentazione nel codice prima di registrare qualsiasi telemetria (o variabile di ambiente APPINSIGHTS_INSTRUMENTATIONKEY set). Successivamente, deve essere in grado di controllare la telemetria manualmente e visualizzarlo nel portale di Azure

```C#
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Installare la versione più recente di [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) pacchetto - tiene automaticamente traccia HTTP, SQL o altre chiamate di dipendenza esterna.

È possibile inizializzare e configurare Application Insights dal codice o tramite `ApplicationInsights.config` file. Verificare che l'inizializzazione viene eseguita non appena possibile.

### <a name="using-config-file"></a>Utilizzando il file di configurazione

Per impostazione predefinita, Application Insights SDK Cerca `ApplicationInsights.config` file nella directory di lavoro quando `TelemetryConfiguration` viene creato

```C#
TelemetryConfiguration config = TelemetryConfiguration.Active; // Read ApplicationInsights.config file if present
```

È inoltre possibile specificare percorso al file di configurazione.

```C#
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration("ApplicationInsights.config");
```

Per ulteriori informazioni, vedere [file di configurazione](app-insights-configuration-with-applicationinsights-config.md).

È possibile che venga visualizzato un esempio completo del file di configurazione installando la versione più recente di [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) pacchetto. Ecco il **minimo** configurazione per la raccolta di dipendenza che è equivalente all'esempio di codice.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
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

* Durante l'avvio dell'applicazione, creare e configurare `DependencyTrackingTelemetryModule` istanza - deve essere singleton e deve essere mantenuto per la durata dell'applicazione.

```C#
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Aggiungere gli inizializzatori di telemetria comuni

```C#
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* Per app di Windows di .NET Framework, è anche possibile installare e inizializzare il modulo di agente di raccolta contatore delle prestazioni, come descritto [qui](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

#### <a name="full-example"></a>Esempio completo

```C#
static void Main(string[] args)
{
    TelemetryConfiguration configuration = TelemetryConfiguration.Active;

    configuration.InstrumentationKey = "removed";
    configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
    configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

    var telemetryClient = new TelemetryClient();
    using (IntitializeDependencyTracking(configuration))
    {
        telemetryClient.TrackTrace("Hello World!");

        using (var httpClient = new HttpClient())
        {
            // Http dependency is automatically tracked!
            httpClient.GetAsync("https://microsoft.com").Wait();
        }
    }

    // run app...

    // when application stops or you are done with dependency tracking, do not forget to dispose the module
    dependencyTrackingModule.Dispose();

    telemetryClient.Flush();
}

static DependencyTrackingTelemetryModule IntitializeDependencyTracking(TelemetryConfiguration configuration)
{
    // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");    
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

    // enable known dependency tracking, note that in future versions, we will extend this list. 
    // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

    // initialize the module
    module.Initialize(configuration);

    return module;
}
```

## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio dipendenze](app-insights-asp-net-dependencies.md) per verificare se altre risorse esterne, SQL o REST rallentano è.
* [Utilizzare l'API](app-insights-api-custom-events-metrics.md) per inviare gli eventi e le metriche per una visualizzazione più dettagliata dell'utilizzo e delle prestazioni dell'app.
