---
title: Azure Application Insights sostituisce gli endpoint SDK predefinitiAzure Application Insights override default SDK endpoints
description: Modificare gli endpoint SDK di Azure Monitor Application Insights predefiniti per aree come Azure per enti pubblici.
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: b43bd13c73f77c6292e2062db88d68a20e5bf480
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729521"
---
# <a name="application-insights-overriding-default-endpoints"></a>Application Insights esegue l'override degli endpoint predefiniti

Per inviare dati da Application Insights a determinate aree, è necessario eseguire l'override degli indirizzi degli endpoint predefiniti. Ogni SDK richiede modifiche leggermente diverse, tutte descritte in questo articolo. Queste modifiche richiedono la modifica del codice di `QuickPulse_Endpoint_Address` `TelemetryChannel_Endpoint_Address`esempio `Profile_Query_Endpoint_address` e la sostituzione dei valori segnaposto per , e con gli indirizzi endpoint effettivi per l'area specifica. La fine di questo articolo contiene collegamenti agli indirizzi degli endpoint per le aree in cui è necessaria questa configurazione.

> [!NOTE]
> [Le stringhe](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net) di connessione sono il nuovo metodo preferito per impostare endpoint personalizzati in Application Insights.Connection strings are the new preferred method of setting custom endpoints within Application Insights.

---

## <a name="sdk-code-changes"></a>Modifiche al codice SDK

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Il file applicationinsights.config viene automaticamente sovrascritto ogni volta che viene eseguito un aggiornamento SDK. Dopo aver eseguito un aggiornamento SDK, assicurarsi di immettere nuovamente i valori dell'endpoint specifici dell'area.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Modificare il file appsettings.json nel progetto come segue per regolare l'endpoint principale:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

I valori per le metriche dinamiche e l'endpoint di query del profilo possono essere impostati solo tramite codice. Per eseguire l'override dei valori predefiniti per tutti `ConfigureServices` i valori `Startup.cs` dell'endpoint tramite codice, apportare le modifiche seguenti nel metodo del file:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Funzioni di Azure](#tab/functions)

### <a name="azure-functions-v2x"></a>Funzioni di Azure v2.xAzure Functions v2.x

Installare i seguenti pacchetti nel progetto di funzione:

- Microsoft.ApplicationInsights versione 2.10.0
- Microsoft.ApplicationInsights.PerfCounterCollector versione 2.10.0
- Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel versione 2.10.0

Quindi, aggiungere (o modificare) il codice di avvio per l'applicazione di funzione:Then, add (or modify) the startup code for your function application:

```csharp
[assembly: WebJobsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IWebJobsBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "QuickPulse_Endpoint_Address"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });
      }
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Modificare il file applicationinsights.xml per modificare l'indirizzo dell'endpoint predefinito.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Modificare `application.properties` il file e aggiungere:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Gli endpoint possono essere configurati anche tramite variabili di ambiente:The endpoints can also be configured through environment variables:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

# <a name="javascript"></a>[JavaScript](#tab/js)

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

# <a name="python"></a>[Python](#tab/python)

Per indicazioni sulla modifica dell'endpoint di inserimento per l'SDK opencensus-python, consultare il [repository opencensus-python.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

---

## <a name="regions-that-require-endpoint-modification"></a>Aree che richiedono la modifica dell'endpointRegions that require endpoint modification

Attualmente le uniche aree che richiedono modifiche agli endpoint sono [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) e Azure [Cina.](https://docs.microsoft.com/azure/china/resources-developer-guide)

|Region |  Nome endpoint | valore |
|-----------------|:------------|:-------------|
| Azure Cina | Canale di telemetria | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure Cina | QuickPulse (metriche live) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure Cina | Query profilo |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Canale di telemetria |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (metriche live) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Query profilo |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Se attualmente si usa l'API REST di Application Insights a cui si accede normalmente tramite 'api.applicationinsights.io', è necessario usare un endpoint locale per l'area geografica:If you currently use the [Application Insights REST API](https://dev.applicationinsights.io/
) that is normally accessed via 'api.applicationinsights.io' you will need to use an endpoint that is local to your region:

|Region |  Nome endpoint | valore |
|-----------------|:------------|:-------------|
| Azure Cina | API REST | `api.applicationinsights.azure.cn` |
| Azure Government | API REST | `api.applicationinsights.us`|

> [!NOTE]
> Il monitoraggio basato su agente/estensione senza codice per i servizi app di Azure non è **attualmente supportato** in queste aree. Non appena questa funzionalità diventa disponibile questo articolo verrà aggiornato.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle modifiche personalizzate per Azure per enti pubblici, vedere le indicazioni dettagliate per il monitoraggio e la gestione di [Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)
- Per altre informazioni su Azure China, vedere Azure [China Playbook](https://docs.microsoft.com/azure/china/).
