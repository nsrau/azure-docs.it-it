---
title: 'Monitoraggio di Azure: applicazione Azure Insights sostituisce gli endpoint SDK predefiniti | Microsoft Docs'
description: Modificare gli endpoint predefiniti di applicazione Azure Insights SDK per le aree come Azure per enti pubblici.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mbullwin
ms.openlocfilehash: c086f94a161853cba3a9ed2b98f13ea17b90dd20
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478809"
---
 # <a name="application-insights-overriding-default-endpoints"></a>Application Insights l'override degli endpoint predefiniti

Per inviare dati da Application Insights a determinate aree, è necessario eseguire l'override degli indirizzi endpoint predefiniti. Ogni SDK richiede modifiche leggermente diverse, descritte in questo articolo. Per queste modifiche è necessario modificare il codice di esempio e sostituire i `QuickPulse_Endpoint_Address`valori `TelemetryChannel_Endpoint_Address`segnaposto `Profile_Query_Endpoint_address` per, e con gli indirizzi endpoint effettivi per l'area specifica. La fine di questo articolo contiene i collegamenti agli indirizzi degli endpoint per le aree in cui è necessaria questa configurazione.

## <a name="sdk-code-changes"></a>Modifiche al codice SDK

### <a name="net-with-applicationinsightsconfig"></a>.NET con applicationinsights. config

> [!NOTE]
> Il file applicationinsights. config viene sovrascritto automaticamente ogni volta che viene eseguito un aggiornamento dell'SDK. Dopo l'esecuzione di un aggiornamento dell'SDK, assicurarsi di immettere nuovamente i valori dell'endpoint specifico dell'area.

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

### <a name="net-core"></a>.NET Core

Modificare il file appSettings. JSON nel progetto come indicato di seguito per modificare l'endpoint principale:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

I valori per la metrica dinamica e l'endpoint di query del profilo possono essere impostati solo tramite codice. Per eseguire l'override dei valori predefiniti per tutti i valori dell'endpoint tramite codice, apportare le `ConfigureServices` modifiche seguenti nel `Startup.cs` metodo del file:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton(new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="java"></a>Java

Modificare il file applicationinsights. XML per modificare l'indirizzo dell'endpoint predefinito.

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

Modificare il `application.properties` file e aggiungere:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Gli endpoint possono anche essere configurati tramite le variabili di ambiente:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"INSTRUMENTATION_KEY"
      endpointUrl: "TelemetryChannel_Endpoint_Address"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

## <a name="regions-that-require-endpoint-modification"></a>Aree che richiedono la modifica dell'endpoint

Attualmente le uniche aree che richiedono modifiche all'endpoint sono [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) e [Azure Cina](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Region |  Nome endpoint | Value |
|-----------------|:------------|:-------------|
| Azure Cina | Canale di telemetria | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure Cina | QuickPulse (metriche attive) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure Cina | Query del profilo |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Canale di telemetria |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (metriche attive) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Query del profilo |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

> [!NOTE]
> Il monitoraggio di agenti/estensioni senza codice per i servizi di app Azure **non è attualmente supportato** in queste aree. Non appena questa funzionalità diventa disponibile, questo articolo verrà aggiornato.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle modifiche personalizzate per Azure per enti pubblici, vedere le indicazioni dettagliate per il [monitoraggio e la gestione di Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
- Per altre informazioni su Azure Cina, vedere il [PlayBook di Azure China](https://docs.microsoft.com/azure/china/).