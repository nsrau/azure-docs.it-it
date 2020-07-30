---
title: Stringhe di connessione in applicazione Azure Insights | Microsoft Docs
description: Come utilizzare le stringhe di connessione.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-javascript
ms.reviewer: mbullwin
ms.openlocfilehash: 375929a983c5dfea01a88fb64fd5ab19bf105c0c
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87383631"
---
# <a name="connection-strings"></a>Stringhe di connessione

## <a name="overview"></a>Panoramica

Le stringhe di connessione forniscono agli utenti di Application Insight una singola impostazione di configurazione, eliminando la necessità di più impostazioni proxy. Particolarmente utile per i server Web Intranet, gli ambienti di cloud ibrido o sovrano che vogliono inviare dati al servizio di monitoraggio.

Le coppie chiave-valore forniscono agli utenti un modo semplice per definire una combinazione di suffisso di prefisso per ogni servizio/prodotto Application Insights (AI).

> [!IMPORTANT]
> Non è consigliabile impostare sia la stringa di connessione che la chiave di strumentazione. Nel caso in cui un utente imposti entrambi, a seconda del valore impostato per ultimo, avrà la precedenza. 


## <a name="scenario-overview"></a>Panoramica dello scenario 

Scenari per i clienti in cui viene visualizzato questo comportamento con maggiore effetto:

- Eccezioni del firewall o reindirizzamenti proxy 

    Nei casi in cui è richiesto il monitoraggio per il server Web Intranet, la soluzione precedente ha richiesto ai clienti di aggiungere singoli endpoint di servizio alla configurazione. Per altre informazioni, vedere [qui](../faq.md#can-i-monitor-an-intranet-web-server). 
    Le stringhe di connessione offrono un'alternativa migliore riducendo questo impegno a una singola impostazione. Un prefisso semplice, la modifica del suffisso consente il popolamento automatico e il reindirizzamento di tutti gli endpoint ai servizi corretti. 

- Ambienti cloud sovrani o ibridi

    Gli utenti possono inviare dati a un' [area di Azure per enti pubblici](../../azure-government/compare-azure-government-global-azure.md#application-insights)definita.
    Le stringhe di connessione consentono di definire le impostazioni degli endpoint per i server Intranet o le impostazioni del cloud ibrido. 

## <a name="getting-started"></a>Introduzione

### <a name="finding-my-connection-string"></a>Trovare la stringa di connessione?

La stringa di connessione viene visualizzata nel pannello panoramica della risorsa Application Insights.

![stringa di connessione nel pannello Panoramica](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schema

#### <a name="max-length"></a>Lunghezza massima

La lunghezza massima supportata per la connessione è di 4096 caratteri.

#### <a name="key-value-pairs"></a>Coppie chiave-valore

La stringa di connessione è costituita da un elenco di impostazioni rappresentate come coppie chiave-valore separate da punto e virgola:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Sintassi

- `InstrumentationKey`(ad esempio: 00000000-0000-0000-0000-000000000000)  La stringa di connessione è un campo **obbligatorio** .
- `Authorization`(ad esempio: Ikey) Questa impostazione è facoltativa perché oggi è supportata solo l'autorizzazione Ikey.
- `EndpointSuffix`(ad esempio: applicationinsights.azure.cn) L'impostazione del suffisso dell'endpoint indicherà all'SDK il cloud di Azure a cui connettersi. L'SDK assembla il resto dell'endpoint per i singoli servizi.
- Endpoint espliciti.
  Qualsiasi servizio può essere sottoposto a override in modo esplicito nella stringa di connessione.
   - `IngestionEndpoint`(ad esempio: `https://dc.applicationinsights.azure.com` )
   - `LiveEndpoint`(ad esempio: `https://live.applicationinsights.azure.com` )
   - `ProfilerEndpoint`(ad esempio: `https://profiler.applicationinsights.azure.com` )
   - `SnapshotEndpoint`(ad esempio: `https://snapshot.applicationinsights.azure.com` )

#### <a name="endpoint-schema"></a>Schema dell'endpoint

`<prefix>.<suffix>`
- Prefix: definisce un servizio. 
- Suffisso: definisce il nome di dominio comune.

##### <a name="valid-suffixes"></a>Suffissi validi

Ecco un elenco di suffissi validi 
- applicationinsights.azure.cn
- applicationinsights.us


Vedere anche: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Prefissi validi

- Inserimento di dati di [telemetria](./app-insights-overview.md):`dc`
- [Metriche attive](./live-stream.md):`live`
- [Profiler](./profiler-overview.md):`profiler`
- [Snapshot](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Esempi di stringhe di connessione


### <a name="minimal-valid-connection-string"></a>Stringa di connessione valida minima

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

In questo esempio è stata impostata solo la chiave di strumentazione.

- Per impostazione predefinita, lo schema di autorizzazione è "iKey" 
- Chiave di strumentazione: 00000000-0000-0000-0000-000000000000
- Gli URI del servizio regionale sono basati sulle [impostazioni predefinite dell'SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) e si connetteranno all'Azure globale pubblico:
   - Ingestione`https://dc.services.visualstudio.com/`
   - Metriche attive:`https://rt.services.visualstudio.com/`
   - Profiler`https://agent.azureserviceprofiler.net/`
   - Debugger`https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>Stringa di connessione con suffisso endpoint

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

In questo esempio, questa stringa di connessione specifica il suffisso dell'endpoint e l'SDK creerà gli endpoint di servizio.

- Per impostazione predefinita, lo schema di autorizzazione è "iKey" 
- Chiave di strumentazione: 00000000-0000-0000-0000-000000000000
- Gli URI del servizio regionale sono basati sul suffisso dell'endpoint fornito: 
   - Ingestione`https://dc.ai.contoso.com`
   - Metriche attive:`https://live.ai.contoso.com`
   - Profiler`https://profiler.ai.contoso.com`
   - Debugger`https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Stringa di connessione con override esplicito dell'endpoint 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

In questo esempio, questa stringa di connessione specifica le sostituzioni esplicite per ogni servizio. L'SDK userà gli endpoint esatti forniti senza modifiche.

- Per impostazione predefinita, lo schema di autorizzazione è "iKey" 
- Chiave di strumentazione: 00000000-0000-0000-0000-000000000000
- Gli URI del servizio regionale sono basati sui valori di override espliciti: 
   - Ingestione`https://custom.com:111/`
   - Metriche attive:`https://custom.com:222/`
   - Profiler`https://custom.com:333/`
   - Debugger`https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Come impostare una stringa di connessione

Le stringhe di connessione sono supportate nelle versioni dell'SDK seguenti:
- .NET e .NET Core v 2.12.0
- Java v 2.5.1 e Java 3,0
- JavaScript v 2.3.0
- NodeJS v 1.5.0
- Python v 1.0.0

Una stringa di connessione può essere impostata sia nel codice, nella variabile di ambiente o nel file di configurazione.



### <a name="environment-variable"></a>Variabile di ambiente

- Stringa di connessione: `APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

TelemetryConfiguration. ConnectionString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

Set .NET in modo esplicito:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

File di configurazione .NET:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore impostati in modo esplicito:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.js: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v 2.5. x) impostato in modo esplicito:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Importante: JavaScript non supporta l'uso di variabili di ambiente.

Usando il frammento di codice:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Installazione manuale:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Si consiglia agli utenti di impostare la variabile di ambiente.

Per impostare in modo esplicito la stringa di connessione:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Passaggi successivi

Introduzione in fase di esecuzione a:

* [App ospitate in IIS in macchine virtuali di Azure e set di scalabilità di macchine virtuali di Azure](./azure-vm-vmss-apps.md)
* [Server IIS](./monitor-performance-live-website-now.md)
* [App Web di Azure](./azure-web-apps.md)

Introduzione in fase di sviluppo a:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

