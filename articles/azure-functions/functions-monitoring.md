---
title: Monitorare Funzioni di Azure
description: Informazioni su come usare Azure Application Insights con Funzioni di Azure per monitorare l'esecuzione delle funzioni.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 08da17f1ef023676aa0c499cf4e7e1bb9687f1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257854"
---
# <a name="monitor-azure-functions"></a>Monitorare Funzioni di Azure

[Funzioni](functions-overview.md) di Azure offre l'integrazione incorporata con [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) per monitorare le funzioni. Questo articolo illustra come configurare Funzioni di Azure per inviare file di log generati dal sistema ad Application Insights.This article shows you how to configure Azure Functions to send system-generated log files to Application Insights.

È consigliabile usare Application Insights perché raccoglie i dati di log, le prestazioni e gli errori. Rileva automaticamente le anomalie delle prestazioni e include potenti strumenti di analisi che consentono di diagnosticare i problemi e di comprendere come vengono utilizzate le funzioni. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità. È anche possibile usare Application Insights durante lo sviluppo di progetti di app per le funzioni locali. Per ulteriori informazioni, vedere [Che cos'è Application Insights?](../azure-monitor/app/app-insights-overview.md).

Poiché la strumentazione di Application Insights necessaria è incorporata in Funzioni di Azure, è sufficiente una chiave di strumentazione valida per connettere l'app per le funzioni a una risorsa di Application Insights.As the required Application Insights instrumentation is built into Azure Functions, all you need is a valid instrumentation key to connect your function app to an Application Insights resource. La chiave di strumentazione deve essere aggiunta alle impostazioni dell'applicazione quando viene creata la risorsa dell'app per le funzioni in Azure.The instrumentation key should be added to your application settings when your function app resource is created in Azure. Se l'app per le funzioni non dispone già di questa chiave, è possibile [impostarla manualmente.](#enable-application-insights-integration)  

## <a name="application-insights-pricing-and-limits"></a>Prezzi e limiti di Application Insights

È possibile provare gratuitamente l'integrazione di Application Insights con le app per le funzioni. C'è un limite giornaliero alla quantità di dati che possono essere elaborati gratuitamente. È possibile raggiungere questo limite durante il test. Quando il limite giornaliero è quasi raggiunto, Azure invia notifiche tramite il portale e messaggi di posta elettronica. Se questi avvisi vengono persi e si raggiunge il limite, i nuovi log non verranno visualizzati nelle query di Application Insights.If you miss those alerts and hit the limit, new logs won't appear in Application Insights queries. Essere consapevoli del limite per evitare inutili tempi di risoluzione dei problemi. Per altre informazioni, vedere [Gestire volumi di dati e prezzi in Application Insights](../azure-monitor/app/pricing.md).

L'elenco completo delle funzionalità di Application Insights disponibili per l'app per le funzioni è descritto in dettaglio nelle funzionalità supportate da [Application Insights per Funzioni di Azure.](../azure-monitor/app/azure-functions-supported-features.md)

## <a name="view-telemetry-in-monitor-tab"></a>Visualizzare dati di telemetria nella scheda Monitoraggio

Con [l'integrazione](#enable-application-insights-integration)di Application Insights abilitata , è possibile visualizzare i dati di telemetria nella scheda **Monitoraggio.**

1. Nella pagina dell'app per le funzioni selezionare una funzione che è stata eseguita almeno una volta dopo la configurazione di Application Insights.In the function app page, select a function that has run at least once after Application Insights was configured. Selezionare quindi la scheda **Monitoraggio.** Selezionare **Aggiorna** periodicamente, fino a quando non viene visualizzato l'elenco delle chiamate di funzione.

   ![Elenco di chiamate](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > La visualizzazione dell'elenco può richiedere fino a cinque minuti mentre il client di telemetria batchi dati per la trasmissione al server. Il ritardo non si applica al [flusso Live Metrics Stream](../azure-monitor/app/live-stream.md). Tale servizio si connette all'host di funzioni quando si carica la pagina, in modo che i log vengono trasmessi direttamente alla pagina.

1. Per visualizzare i log per una particolare chiamata di funzione, selezionare il collegamento di colonna **Data (UTC)** per tale chiamata. L'output di registrazione per tale chiamata viene visualizzato in una nuova pagina.

   ![Dettagli della chiamata](media/functions-monitoring/invocation-details-ai.png)

1. Scegliere il collegamento **Esegui in Application Insights** per visualizzare l'origine della query che recupera i dati del log di Monitoraggio di Azure nel log di Azure Se questa è la prima volta che si usa Azure Log Analytics nella sottoscrizione, viene chiesto di abilitarlo.

1. Quando si sceglie tale collegamento e si sceglie di abilitare Log Analytic. viene visualizzata la query seguente. È possibile vedere che i risultati della query`where timestamp > ago(30d)`sono limitati agli ultimi 30 giorni ( ). Inoltre, i risultati mostrano non più`take 20`di 20 righe ( ). Al contrario, l'elenco dei dettagli di chiamata per la funzione è per gli ultimi 30 giorni senza limiti.

   ![Elenco di chiamate di analisi di Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Per altre informazioni, vedere [Eseguire query sui dati di telemetria](#query-telemetry-data) più avanti in questo articolo.

## <a name="view-telemetry-in-application-insights"></a>Visualizzare i dati di telemetria in Application Insights

Per aprire Application Insights da un'app per le funzioni nel portale di Azure, passare alla pagina **Panoramica** dell'app per le funzioni. In **Funzionalità configurate**selezionare **Application Insights**.

![Aprire Application Insights dalla pagina Panoramica dell'app per le funzioniOpen Application Insights from the function app Overview page](media/functions-monitoring/ai-link.png)

Per informazioni su come usare Application Insights, vedere la [documentazione su Application Insights](https://docs.microsoft.com/azure/application-insights/). Questa sezione mostra alcuni esempi su come visualizzare i dati in Application Insights. Se si ha già familiarità con Application Insights, è possibile passare direttamente alle sezioni su come configurare e personalizzare i dati di [telemetria.](#configure-categories-and-log-levels)

![Scheda Panoramica di Application Insights](media/functions-monitoring/metrics-explorer.png)

Le aree seguenti di Application Insights possono essere utili quando si valutano il comportamento, le prestazioni e gli errori nelle funzioni:The following areas of Application Insights can be helpful when evaluating the behavior, performance, and errors in your functions:

| Analisi dei problemi | Descrizione |
| ---- | ----------- |
| **[Errori](../azure-monitor/app/asp-net-exceptions.md)** |  Creare grafici e avvisi in base agli errori delle funzioni e alle eccezioni del server. Il **nome dell'operazione** corrisponde al nome della funzione. Gli errori nelle dipendenze non vengono visualizzati a meno che non si implementi dati di telemetria personalizzati per le dipendenze. |
| **[Prestazioni](../azure-monitor/app/performance-counters.md)** | Analizzare i problemi di prestazioni visualizzando l'utilizzo delle risorse e la velocità effettiva per le istanze del **ruolo Cloud.** Questi dati possono essere utili negli scenari di debug in cui le funzioni bloccano le risorse sottostanti. |
| **[Metriche](../azure-monitor/app/metrics-explorer.md)** | Creare grafici e avvisi basati sulle metriche. Le metriche includono il numero di chiamate di funzione, il tempo di esecuzione e le frequenze di successo. |
| **[Metriche in tempo reale](../azure-monitor/app/live-stream.md)** | Visualizza i dati delle metriche così come vengono creati quasi in tempo reale. |

## <a name="query-telemetry-data"></a>Query sui dati di telemetria

[Application Insights Analytics](../azure-monitor/app/analytics.md) consente di accedere a tutti i dati di telemetria sotto forma di tabelle in un database. Analytics offre un linguaggio di query per l'estrazione, la manipolazione e la visualizzazione dei dati. 

Scegliere **Registri** per esplorare o eseguire una query per gli eventi registrati.

![Esempio di Analytics](media/functions-monitoring/analytics-traces.png)

Questo è un esempio che mostra la distribuzione delle richieste per ruolo di lavoro negli ultimi 30 minuti.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

Le tabelle disponibili vengono visualizzate nella scheda **Schema** a sinistra. Nelle tabelle seguenti è possibile trovare i dati generati dalle chiamate alla funzione:

| Tabella | Descrizione |
| ----- | ----------- |
| **Tracce** | Registri creati dal runtime e dal codice della funzione. |
| **richieste** | Una richiesta per ogni chiamata di funzione. |
| **Eccezioni** | Eventuali eccezioni generate dal runtime. |
| **customMetrics** | Conteggio delle chiamate riuscite e non riuscite, del tasso di successo e della durata. |
| **customEvents** | Eventi rilevati dal runtime, ad esempio: richieste HTTP che attivano una funzione. |
| **Performancecounters** | Informazioni sulle prestazioni dei server su cui sono in esecuzione le funzioni. |

Le altre tabelle sono per i test di disponibilità e i dati di telemetria client e browser. È possibile implementare i dati di telemetria personalizzati per aggiungerne altri.

All'interno di ogni tabella alcuni dati specifici per Funzioni si trovano nel campo `customDimensions`.  Ad esempio, la query seguente recupera tutte le tracce con livello di registrazione `Error`.

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Il runtime `customDimensions.LogLevel` fornisce `customDimensions.Category` i campi e . È possibile fornire campi aggiuntivi nei log scritti nel codice della funzione. Vedere la sezione [Registrazione strutturata](#structured-logging) più avanti in questo articolo.

## <a name="configure-categories-and-log-levels"></a>Configurare le categorie e i livelli di registrazione

È possibile usare Application Insights senza alcuna configurazione personalizzata. La configurazione predefinita può comportare volumi elevati di dati. Se si usa una sottoscrizione di Azure di Visual Studio, si potrebbe raggiunge il limite d'uso dati per Application Insights. Più avanti in questo articolo viene illustrato come configurare e personalizzare i dati inviati dalle funzioni ad Application Insights.Later in this article, you learn how to configure and customize the data that your functions send to Application Insights. Per un'app per le funzioni, la registrazione è configurata nel file [host.json.]

### <a name="categories"></a>Categorie

Il logger di Funzioni di Azure include un *categoria* per ogni log. La categoria indica quale parte del codice runtime o del codice funzione è stata scritta dal log. Nel grafico seguente vengono descritte le principali categorie di log create dal runtime. 

| Category | Descrizione |
| ----- | ----- | 
| Host.Results | Questi log vengono visualizzati come richieste in Application Insights.These logs show as **requests** in Application Insights. Indicano l'esito positivo o negativo di una funzione. Tutti questi registri vengono `Information` scritti a livello. Se filtri `Warning` in corrispondenza o al di sopra, non vedrai nessuno di questi dati. |
| Host.Aggregator | Questi log indicano il numero e le medie di chiamate alla funzione in un periodo di tempo [configurabile](#configure-the-aggregator). Il periodo predefinito è 30 secondi o 1000 risultati, ovvero quello che viene prima. I log sono disponibili nella tabella **customMetrics** in Application Insights. Esempi sono il numero di esecuzioni, la percentuale di operazioni riuscite e la durata. Tutti questi registri vengono `Information` scritti a livello. Se filtri `Warning` in corrispondenza o al di sopra, non vedrai nessuno di questi dati. |

Tutti i log per categorie diverse da questi sono disponibili nella tabella delle tracce in Application Insights.All logs for categories other than these are available in the **traces** table in Application Insights.

Tutti i log con `Host` categorie che iniziano con vengono scritti dal runtime di Funzioni.All logs with categories that begin with are written by the Functions runtime. I registri **Function started** e **Function completed** hanno categoria `Host.Executor`. Per le esecuzioni `Information` riuscite, questi log sono di livello. Le eccezioni `Error` vengono registrate a livello. Il runtime crea anche il livello di registrazione `Warning`, ad esempio: messaggi di coda inviati alla coda non elaborabile.

Il runtime di Funzioni crea log con una categoria che iniziano con "Host". Nella versione `function started`1.x, `function executed`i `function completed` registri , `Host.Executor`e hanno la categoria . A partire dalla versione 2.x, `Function.<YOUR_FUNCTION_NAME>`questi registri hanno la categoria .

Se si scrivono log nel codice `Function.<YOUR_FUNCTION_NAME>.User` della funzione, la categoria è e può essere qualsiasi livello di log. Nella versione 1.x del runtime di `Function`Funzioni, la categoria è .

### <a name="log-levels"></a>Livelli di registrazione

Il logger di Funzioni di Azure include anche un livello di *log* per ogni log. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) è un'enumerazione e il codice integer ne indica la relativa importanza:

|LogLevel    |Codice|
|------------|---|
|Trace       | 0 |
|Debug       | 1 |
|Informazioni | 2 |
|Avviso     | 3 |
|Errore       | 4 |
|Critico    | 5 |
|nessuno        | 6 |

Il livello di registrazione `None` è illustrato nella sezione successiva. 

### <a name="log-configuration-in-hostjson"></a>Configurazione del log in host.jsonLog configuration in host.json

Il file [host.json] configura il livello di registrazioni che un'app per le funzioni invia ad Application Insights. Per ogni categoria, si indica il livello di registrazione minimo da inviare. Esistono due esempi: il primo esempio è destinato alla versione 2.x e successiva del runtime di Funzioni (con .NET Core) e il secondo esempio è per il runtime versione 1.x.There are two examples: the first example targets [version 2.x and later](functions-versions.md#version-2x) of the Functions runtime (with .NET Core), and the second example is for the version 1.x runtime.

### <a name="version-2x-and-higher"></a>Versione 2.x e successive

Versione v2.x e versioni successive del runtime di Funzioni utilizzano la gerarchia del filtro di [registrazione di .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Versione 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

In questo esempio vengono impostate due regole:

* Per i `Host.Results` log `Function`con `Error` categoria o , inviare solo livello e versioni successive ad Application Insights. I log di livello `Warning` e livelli inferiori vengono ignorati.
* Per i log con categoria `Host.Aggregator`, inviare tutti i log ad Application Insights. Il livello del log `Trace` corrisponde a quello che alcuni logger chiamano `Verbose`, ma usano `Trace` nel file [host.json].
* Per tutti gli altri log, inviare ad Application Insights solo i log di livello `Information` e superiori.

Il valore della categoria in [host.json] controlla la registrazione di tutte le categorie che iniziano con lo stesso valore. `Host`in [host.json] controlla `Host.General` `Host.Executor`la `Host.Results`registrazione per , , e così via.

Se [host.json] include più categorie che iniziano con la stessa stringa, viene rilevata prima la corrispondenza con quelle più lunghe. Si supponga di voler `Host.Aggregator` evitare `Error` che il runtime `Host.Aggregator` non si `Information` dia il log a livello, ma che si desideri registrare a livello:

### <a name="version-2x-and-later"></a>Versione 2.x e successive

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Versione 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Per eliminare tutti i log di una categoria, è possibile usare il livello di registrazione `None`. Nessun log viene scritto con tale categoria e non c'è alcun livello di log al di sopra di esso.

## <a name="configure-the-aggregator"></a>Configurare l'aggregatore

Come indicato nella sezione precedente, il runtime aggrega i dati sulle esecuzioni di funzioni in un periodo di tempo. Il periodo predefinito è 30 secondi o 1000 esecuzioni, ovvero quello che viene prima. È possibile configurare questa impostazione nel file [host.json].  Ad esempio:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurare il campionamento

Application Insights include una funzionalità di [campionamento](../azure-monitor/app/sampling.md) che consente di proteggere la produzione di troppi dati di telemetria sulle esecuzioni completate nei momenti di picco del carico. Quando la frequenza delle esecuzioni in ingresso supera una soglia specificata, Application Insights inizia a ignorare in modo casuale alcune delle esecuzioni in ingresso. L'impostazione predefinita per il numero massimo di esecuzioni al secondo è 20 (cinque nella versione 1.x). È possibile configurare il campionamento nel file [host.json].  Ad esempio:

### <a name="version-2x-and-later"></a>Versione 2.x e successive

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Versione 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> Il [campionamento](../azure-monitor/app/sampling.md) è abilitato per impostazione predefinita. Se i dati sembrano mancare, potrebbe essere necessario modificare le impostazioni di campionamento per adattarle al particolare scenario di monitoraggio.

## <a name="write-logs-in-c-functions"></a>Scrivere i log nelle funzioni C#

È possibile scrivere log nel codice funzione che vengono visualizzati come tracce in Application Insights.

### <a name="ilogger"></a>ILogger

Usare il parametro [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) nelle funzioni anziché il parametro `TraceWriter`. I log `TraceWriter` creati utilizzando passano `ILogger` ad Application Insights, ma consentono di eseguire [la registrazione strutturata.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)

Con un oggetto `ILogger` è possibile chiamare i  [metodi di estensione su ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods)`Log<level>` per creare i log. Il codice `Information` seguente scrive i log con la categoria "Function.<YOUR_FUNCTION_NAME>. L'utente."

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registrazione strutturata

I parametri usati nel messaggio del log sono determinati dall'ordine dei segnaposto, non dai nomi. Si supponga di disporre del codice seguente:For you have the following code:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se si mantenere la stessa stringa del messaggio e si inverte l'ordine dei parametri, il testo del messaggio risultante mostrerebbe i valori in maniera errata.

I segnaposto vengono gestiti in modo da poter eseguire la registrazione strutturata. Application Insights archivia le coppie nome-valore del parametro e la stringa del messaggio. Il risultato è che gli argomenti del messaggio diventano campi su cui è possibile eseguire delle query.

Se la chiamata al metodo del logger è `customDimensions.prop__rowKey`simile all'esempio precedente, è possibile eseguire una query sul campo . Il `prop__` prefisso viene aggiunto per garantire che non vi siano conflitti tra i campi aggiunti dal runtime e i campi aggiunti dal codice della funzione.

È anche possibile eseguire query sulla stringa del messaggio originale facendo riferimento al campo `customDimensions.prop__{OriginalFormat}`.  

Ecco una rappresentazione JSON di esempio dei dati `customDimensions`:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Registrazione delle metriche personalizzate

Nelle funzioni di script C# è possibile usare il metodo di estensione `LogMetric` su `ILogger` per creare metriche personalizzate in Application Insights. Ecco un esempio di chiamata al metodo:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Questo codice è un'alternativa alla chiamata `TrackMetric` tramite l'API Application Insights per .NET.

## <a name="write-logs-in-javascript-functions"></a>Scrivere i log in funzioni JavaScript

Nelle funzioni di Node.js usare `context.log` per scrivere i log. La registrazione strutturata non è abilitata.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Registrazione delle metriche personalizzate

Quando si esegue la [versione 1.x](functions-versions.md#creating-1x-apps) del runtime di Funzioni, `context.log.metric` le funzioni Node.js possono usare il metodo per creare metriche personalizzate in Application Insights. Questo metodo non è attualmente supportato nella versione 2.x e successive. Ecco un esempio di chiamata al metodo:

```javascript
context.log.metric("TestMetric", 1234);
```

Questo codice è un'alternativa alla chiamata tramite node.js SDK per Application Insights.This code is an alternative to calling `trackMetric` by using the Node.js SDK for Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Registrare dati di telemetria personalizzati nelle funzioni C#

È disponibile una versione specifica di Funzioni di Application Insights SDK che è possibile usare per inviare dati di telemetria personalizzati dalle funzioni ad Application Insights: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Utilizzare il comando seguente dal prompt dei comandi per installare questo pacchetto:

# <a name="command"></a>[Comando](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

In questo comando `<VERSION>` sostituire con una versione di questo pacchetto che supporta la versione installata di [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

Negli esempi di Cè riportati di seguito viene utilizzata l'API di [telemetria personalizzata.](../azure-monitor/app/api-custom-events-metrics.md) L'esempio fa riferimento a una libreria di classi .NET, ma il codice di Application Insights è lo stesso per lo script C#.

### <a name="version-2x-and-later"></a>Versione 2.x e successive

La versione 2.x e le versioni successive del runtime usano le funzionalità più recenti in Application Insights per correlare automaticamente i dati di telemetria con l'operazione corrente. Non è necessario impostare manualmente `Id` `ParentId`l'operazione , o `Name` i campi.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) è l'API attualmente consigliata per la creazione di una metrica.

### <a name="version-1x"></a>Versione 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Non chiamare `TrackRequest` o `StartOperation<RequestTelemetry>` perché vedrai richieste duplicate per una chiamata di funzione.  Il runtime di Funzioni rileva automaticamente le richieste.

Non impostare `telemetryClient.Context.Operation.Id`. Questa impostazione globale causa una correlazione non corretta quando molte funzioni vengono eseguite contemporaneamente. Creare invece una nuova istanza di telemetria (`DependencyTelemetry`, `EventTelemetry`) e modificare la relativa proprietà `Context`. Passare quindi l'istanza di `Track` telemetria al metodo corrispondente su `TelemetryClient` (`TrackDependency()`, `TrackEvent()`, `TrackMetric()`). Questo metodo assicura che i dati di telemetria dispongano dei dettagli di correlazione corretti per la chiamata di funzione corrente.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Registrare dati di telemetria personalizzati nelle funzioni JavaScript

Ecco un frammento di codice di esempio che invia dati di telemetria personalizzati con [l'SDK di Application Insights Node.js:Here](https://github.com/microsoft/applicationinsights-node.js)is a sample code snippet that sends custom telemetry with the Application Insights Node.js SDK :

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

Il `tagOverrides` parametro `operation_Id` imposta l'oggetto sull'ID di chiamata della funzione. Questa impostazione consente di correlare tutti i dati di telemetria personalizzati e generati automaticamente per una chiamata di funzione specifica.

## <a name="dependencies"></a>Dependencies

Funzioni v2 raccoglie automaticamente le dipendenze per le richieste HTTP, ServiceBus, EventHub e SQL.

È possibile scrivere codice personalizzato per mostrare le dipendenze. Per alcuni esempi, vedere il codice di esempio nella sezione relativa [ai dati di telemetria personalizzati](#log-custom-telemetry-in-c-functions)di C. Il codice di esempio genera una *mappa dell'applicazione* in Application Insights simile all'immagine seguente:The sample code results in an application map in Application Insights that looks like the following image:

![Mappa delle applicazioni](./media/functions-monitoring/app-map.png)

## <a name="enable-application-insights-integration"></a>Abilitare l'integrazione di Application Insights

Affinché un'app per le funzioni invii dati ad Application Insights, è necessario conoscere la chiave di strumentazione di una risorsa di Application Insights. La chiave deve essere specificata in un'impostazione dell'app denominata **APPINSIGHTS_INSTRUMENTATIONKEY**.

Quando si crea [l'app](functions-create-first-azure-function.md)per le funzioni nel portale di Azure, dalla riga di comando tramite gli strumenti di base di Funzioni di [Azure](functions-create-first-azure-function-azure-cli.md)o il codice di [Visual Studio](functions-create-first-function-vs-code.md), l'integrazione di Application Insights è abilitata per impostazione predefinita. La risorsa Application Insights ha lo stesso nome dell'app per le funzioni e viene creata nella stessa area o nell'area più vicina.

### <a name="new-function-app-in-the-portal"></a>Nuova app per le funzioni nel portale

Per esaminare la risorsa di Application Insights in fase di creazione, selezionarla per espandere la finestra **Application Insights.To** review the Application Insights resource being created, select it to expand the Application Insights window. È possibile modificare il nome della **nuova risorsa** o scegliere un **percorso** diverso in un'area geografica di [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vogliono archiviare i dati.

![Abilitare Application Insights mentre si crea un'app per le funzioni](media/functions-monitoring/enable-ai-new-function-app.png)

Quando si sceglie **Crea**, viene creata una risorsa di `APPINSIGHTS_INSTRUMENTATIONKEY` Application Insights con l'app per le funzioni, con le impostazioni dell'applicazione impostate. Tutto è pronto.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Aggiungere a un'app per le funzioni esistente 

Quando si crea un'app per le funzioni utilizzando [Visual Studio](functions-create-your-first-function-visual-studio.md), è necessario creare la risorsa Application Insights. È quindi possibile aggiungere la chiave di strumentazione da tale risorsa come impostazione dell'applicazione nell'app per le funzioni.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Le prime versioni di Funzioni utilizzavano il monitoraggio incorporato, che non è più consigliato. Quando si abilita l'integrazione di Application Insights per un'app per le funzioni di questo tipo, è necessario [disabilitare](#disable-built-in-logging)anche la registrazione incorporata.  

## <a name="report-issues"></a>Segnalare i problemi

Per segnalare un problema con l'integrazione di Application Insights in Funzioni o per inviare un suggerimento o una richiesta, [creare un problema in GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Log in streaming

Durante lo sviluppo di un'applicazione, spesso si vuole vedere cosa viene scritto nei log quasi in tempo reale durante l'esecuzione in Azure.While developing an application, you often want to see what's being written to the logs in near real-time when running in Azure.

Esistono due modi per visualizzare un flusso di file di log generati dalle esecuzioni delle funzioni.

* **Streaming log integrato:** la piattaforma del servizio app consente di visualizzare un flusso dei file di log dell'applicazione. Ciò equivale all'output visualizzato quando si esegue il debug delle funzioni durante [lo sviluppo locale](functions-develop-local.md) e quando si usa la scheda **Test** nel portale. Vengono visualizzate tutte le informazioni basate su log. Per ulteriori informazioni, consultate [Registri di](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)flusso. Questo metodo di streaming supporta solo una singola istanza e non può essere usato con un'app in esecuzione su Linux in un piano di consumo.

* **Live Metrics Stream:** quando l'app per le funzioni è [connessa ad Application Insights,](#enable-application-insights-integration)è possibile visualizzare i dati di log e altre metriche quasi in tempo reale nel portale di Azure usando [Live Metrics Stream](../azure-monitor/app/live-stream.md). Utilizzare questo metodo quando si monitorano le funzioni in esecuzione su più istanze o su Linux in un piano di consumo. Questo metodo utilizza [dati campionati](#configure-sampling).

I flussi di log possono essere visualizzati sia nel portale che nella maggior parte degli ambienti di sviluppo locali. 

### <a name="portal"></a>Portale

È possibile visualizzare entrambi i tipi di flussi di log nel portale.

#### <a name="built-in-log-streaming"></a>Streaming dei log integrato

Per visualizzare i log di streaming nel portale, selezionare la scheda **Funzionalità della** piattaforma nell'app per le funzioni. Quindi, in **Monitoraggio**, scegliere **Flusso di log**.

![Abilitare i log di streaming nel portaleEnable streaming logs in the portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

In questo modo l'app viene collegata al servizio di flusso di log e i log dell'applicazione vengono visualizzati nella finestra. È possibile alternare tra **i registri applicazioni** e i registri del server **Web.**  

![Visualizzare i log di streaming nel portaleView streaming logs in the portal](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Flusso di metriche live

Per visualizzare lo stream delle metriche Live per l'app, seleziona la scheda **Panoramica** dell'app per le funzioni. Quando Application Insights è abilitato, viene visualizzato un collegamento **di Application Insights** in Funzionalità **configurate**. Questo collegamento consente di accedere alla pagina Application Insights per l'app.

In Application Insights selezionare **Live Metrics Stream**. [Le voci di log campionate](#configure-sampling) vengono visualizzate in **Telemetria di esempio**.

![Visualizza streaming metriche dal vivo nel portale](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Strumenti di base

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile abilitare i log di streaming usando l'interfaccia della riga di comando di [Azure.](/cli/azure/install-azure-cli) Usare i comandi seguenti per accedere, scegliere la sottoscrizione e trasmettere i file di log:Use the following commands to sign in, choose your subscription, and stream log files:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

È possibile abilitare i log di streaming tramite [Azure PowerShell.You](/powershell/azure/overview)can enable streaming logs by using Azure PowerShell . Per PowerShell usare i comandi seguenti per aggiungere l'account Azure, scegliere la sottoscrizione e trasmettere i file di log:For PowerShell, use the following commands to add your Azure account, choose your subscription, and stream log files:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Disabilitare la registrazione predefinita

Quando si abilita Application Insights, disabilitare la registrazione predefinita che usa Archiviazione di Azure.When you enable Application Insights, disable the built-in logging that uses Azure Storage. La registrazione incorporata è utile per i test con carichi di lavoro leggeri, ma non è destinata all'utilizzo di produzione a carico elevato. Per il monitoraggio della produzione, è consigliabile Application Insights.For production monitoring, we recommend Application Insights. Se nella produzione viene usata la registrazione predefinita, il record di registrazione potrebbe essere incompleto a causa della limitazione di Archiviazione di Azure.If built-in logging is used in production, the logging record might be incomplete because to throttling on Azure Storage.

Per disabilitare la registrazione predefinita, eliminare l'impostazione app `AzureWebJobsDashboard`. Per informazioni su come eliminare le impostazioni app nel portale di Azure, vedere la sezione relativa alle **impostazioni dell'applicazione** in [Come gestire un'app per le funzioni nel portale di Azure](functions-how-to-use-azure-function-app-settings.md#settings). Prima di eliminare l'impostazione dell'app, assicurarsi che nessuna funzione esistente nella stessa app per le funzioni usi l'impostazione per i trigger o le associazioni di Archiviazione di Azure.Before you delete the app setting, make sure that no existing functions in the same function app use the setting for Azure Storage triggers or bindings.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Application Insights](/azure/application-insights/)
* [Registrazione di ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
