---
title: Monitorare Funzioni di Azure
description: Imparare a usare Azure Application Insights con Funzioni di Azure per il monitoraggio dell'esecuzione delle funzioni.
services: functions
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: glenga
ms.openlocfilehash: ba820c594b5afb34c050c74de30300b0dfc8c3a6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344056"
---
# <a name="monitor-azure-functions"></a>Monitorare Funzioni di Azure

## <a name="overview"></a>Panoramica 

[Funzioni di Azure](functions-overview.md) offre l'integrazione predefinita con [Azure Application Insights](../application-insights/app-insights-overview.md) per il monitoraggio delle funzioni. Questo articolo illustra come configurare Funzioni per inviare i dati di telemetria ad Application Insights.

![Esplora metriche di Application Insights](media/functions-monitoring/metrics-explorer.png)

Funzioni dispone anche di funzionalità di [monitoraggio incorporate che non usano Application Insights](#monitoring-without-application-insights). Si consiglia di usare Application Insights perché offre un numero maggiore di dati e modalità di analisi dei dati migliori.

## <a name="application-insights-pricing-and-limits"></a>Prezzi e limiti di Application Insights

È possibile provare gratuitamente l'integrazione di Application Insights con le app per le funzioni. È tuttavia previsto un limite giornaliero per la quantità di dati che possono essere elaborati gratuitamente e tale limite potrebbe essere raggiunto durante i test. Quando il limite giornaliero è quasi raggiunto, Azure invia tramite il portale e messaggi di posta elettronica.  Se tali avvisi non vengono ricevuti e il limite è stato raggiunto, i nuovi registri non verranno visualizzati nelle query di Application Insights. È pertanto necessario conoscere il limite per evitare di perdere tempo non necessario per la risoluzione dei problemi. Per altre informazioni, vedere [Gestire volumi di dati e prezzi in Application Insights](../application-insights/app-insights-pricing.md).

## <a name="enable-app-insights-integration"></a>Abilitare l'integrazione di App Insights

Affinché un'app per le funzioni invii dati ad Application Insights, è necessario conoscere la chiave di strumentazione di una risorsa di Application Insights. La chiave deve essere specificata in un'impostazione dell'app denominata APPINSIGHTS_INSTRUMENTATIONKEY.

È possibile configurare la connessione nel [portale di Azure](https://portal.azure.com):

* [Automaticamente per una nuova app per le funzioni](#new-function-app)
* [Connettere manualmente una risorsa di App Insights](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Nuova app per le funzioni

1. Passare alla pagina **Crea** dell'app per le funzioni.

1. Impostare l'interruttore di **Application Insights** su **On** (Attivo).

2. Selezionare una **Località di Application Insights**.

   Scegliere l'area più vicina all'area dell'app per le funzioni, in un'[area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si intende archiviare i dati.

   ![Abilitare Application Insights mentre si crea un'app per le funzioni](media/functions-monitoring/enable-ai-new-function-app.png)

3. Immettere le altre informazioni richieste.

1. Selezionare **Crea**.

Il passaggio successivo consiste nel [disabilitare la registrazione incorporata](#disable-built-in-logging).

### <a name="manually-connect-an-app-insights-resource"></a>Connettere manualmente una risorsa di App Insights 

1. Creare la risorsa di Application Insights. Impostare il tipo di applicazione su **Generale**.

   ![Creare una risorsa di Application Insights, digitare Generale](media/functions-monitoring/ai-general.png)

2. Copiare la chiave di strumentazione dalla pagina **Informazioni di base** della risorsa di Application Insights. Passare il mouse sulla parte finale del valore della chiave visualizzata per far comparire il pulsante **Fare clic per copiare**.

   ![Copiare la chiave di strumentazione di Application Insights](media/functions-monitoring/copy-ai-key.png)

1. Nella pagina **Impostazioni applicazione** dell'app per le funzioni [aggiungere un'impostazione applicazione](functions-how-to-use-azure-function-app-settings.md#settings) facendo clic su **Aggiungi nuova impostazione**. Denominare la nuova impostazione APPINSIGHTS_INSTRUMENTATIONKEY e incollare la chiave di strumentazione copiata.

   ![Aggiungere la chiave di strumentazione alle impostazioni dell'app](media/functions-monitoring/add-ai-key.png)

1. Fare clic su **Save**.

## <a name="disable-built-in-logging"></a>Disabilitare la registrazione predefinita

Se si abilita Application Insights, è consigliabile disabilitare la [registrazione predefinita che usa Archiviazione di Azure](#logging-to-storage). La registrazione predefinita risulta utile per i test con i carichi di lavoro leggeri, ma non è destinata all'uso in ambienti di produzione con carichi elevati. Per il monitoraggio della produzione, è consigliabile usare Application Insights. Se la registrazione predefinita viene usata in ambienti di produzione, è possibile che il record di registrazione non sia completo a causa delle limitazioni a livello di Archiviazione di Azure.

Per disabilitare la registrazione predefinita, eliminare l'impostazione app `AzureWebJobsDashboard`. Per informazioni su come eliminare le impostazioni app nel portale di Azure, vedere la sezione relativa alle **impostazioni dell'applicazione** in [Come gestire un'app per le funzioni nel portale di Azure](functions-how-to-use-azure-function-app-settings.md#settings). Prima di eliminare l'impostazione dell'app, verificare che tale impostazione non venga usata da alcun'altra funzione esistente nell'app per le funzioni per trigger o binding di Archiviazione di Azure.

## <a name="view-telemetry-in-monitor-tab"></a>Visualizzare dati di telemetria nella scheda Monitoraggio

Dopo aver impostato l'integrazione di Application Insights come illustrato nelle sezioni precedenti, è possibile visualizzare i dati di telemetria nella scheda **Monitoraggio**.

1. Nella pagina dell'app per le funzioni selezionare una funzione eseguita almeno una volta dopo la configurazione di Application Insights, quindi selezionare la scheda **Monitor**.

   ![Selezionare la scheda Monitoraggio](media/functions-monitoring/monitor-tab.png)

2. Selezionare **Aggiorna** periodicamente fino a quando non viene visualizzato l'elenco di chiamate di funzione.

   La visualizzazione dell'elenco può richiedere fino a 5 minuti, a causa del modo in cui il client di telemetria include in batch i dati da trasmettere al server. Questo ritardo non si applica a [Live Metrics Stream](../application-insights/app-insights-live-stream.md). Tale servizio si connette all'host di Funzioni quando si carica la pagina, pertanto i registri vengono trasmessi direttamente alla pagina.

   ![Elenco di chiamate](media/functions-monitoring/monitor-tab-ai-invocations.png)

2. Per visualizzare i registri per una chiamata di funzione particolare, selezionare il collegamento alla colonna **Dati** per tale chiamata.

   ![Collegamento ai dettagli di chiamata](media/functions-monitoring/invocation-details-link-ai.png)

   L'output di registrazione per tale chiamata viene visualizzato in una nuova pagina.

   ![Dettagli della chiamata](media/functions-monitoring/invocation-details-ai.png)

Entrambe le pagine (elenco e dettagli di chiamate) collegano la query di analisi di Application Insights che recupera i dati:

![Esecuzione in Application Insights](media/functions-monitoring/run-in-ai.png)

![Elenco di chiamate di analisi di Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Da queste query è possibile notare che l'elenco di chiamate è limitato agli ultimi 30 giorni e a non più di 20 righe (`where timestamp > ago(30d) | take 20`) e che l'elenco dei dettagli delle chiamate è relativo agli ultimi 30 giorni senza alcun limite.

Per altre informazioni, vedere [Eseguire query sui dati di telemetria](#query-telemetry-data) più avanti in questo articolo.

## <a name="view-telemetry-in-app-insights"></a>Visualizzare dati di telemetria in App Insights

Per aprire Application Insights da un'app per le funzioni nel portale di Azure, selezionare il collegamento **Application Insights** nella sezione **Funzionalità configurate** della pagina **Panoramica** dell'app.

![Collegamento ad Application Insights nella pagina Panoramica](media/functions-monitoring/ai-link.png)


Per informazioni su come usare Application Insights, vedere la [documentazione su Application Insights](https://docs.microsoft.com/azure/application-insights/). Questa sezione mostra alcuni esempi su come visualizzare i dati in Application Insights. Se si ha già familiarità con Application Insights, è possibile passare direttamente alle [sezioni sulla configurazione e la personalizzazione dei dati di telemetria](#configure-categories-and-log-levels).

In [Esplora metriche](../application-insights/app-insights-metrics-explorer.md) è possibile creare grafici e avvisi in base alle metriche ad esempio numero di chiamate alla funzione, tempo di esecuzione e percentuale di successo.

![Esplora metriche](media/functions-monitoring/metrics-explorer.png)

Nella scheda [Errori](../application-insights/app-insights-asp-net-exceptions.md) è possibile creare grafici e avvisi in base agli errori di funzione e alle eccezioni del server. Il **nome dell'operazione** corrisponde al nome della funzione. Gli errori nelle dipendenze non vengono mostrati a meno che non si implementino [i dati di telemetria personalizzati](#custom-telemetry-in-c-functions) per le dipendenze.

![Errori](media/functions-monitoring/failures.png)

Nella scheda [Prestazioni](../application-insights/app-insights-performance-counters.md) è possibile analizzare i problemi di prestazioni.

![Prestazioni](media/functions-monitoring/performance.png)

La scheda **Server** mostra l'uso delle risorse e la velocità effettiva per ogni server. Questi dati possono essere utili negli scenari di debug in cui le funzioni bloccano le risorse sottostanti. I server sono denominati **Istanze del ruolo del cloud**.

![Server](media/functions-monitoring/servers.png)

La scheda [Live Metrics Stream](../application-insights/app-insights-live-stream.md) mostra i dati delle metriche man mano che vengono creati in tempo reale.

![Live stream](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Query sui dati di telemetria

[Analytics di Application Insights](../application-insights/app-insights-analytics.md) consente di accedere a tutti i dati di telemetria sotto forma di tabelle in un database. Analytics offre un linguaggio di query per l'estrazione, la manipolazione e la visualizzazione dei dati.

![Selezionare Analytics](media/functions-monitoring/select-analytics.png)

![Esempio di Analytics](media/functions-monitoring/analytics-traces.png)

Di seguito è riportato un esempio di query, che mostra la distribuzione delle richieste per ruolo di lavoro negli ultimi 30 minuti.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Le tabelle disponibili vengono mostrate nella scheda **Schema** del riquadro a sinistra. Nelle tabelle seguenti è possibile trovare i dati generati dalle chiamate alla funzione:

* **tracce**: log creato dal runtime e dal codice della funzione.
* **richieste**: uno per ogni chiamata alla funzione.
* **eccezioni**: tutte le eccezioni generate dal runtime.
* **customMetrics**: numero di chiamate con esito positivo e negativo, percentuale di successo, durata.
* **customEvents**: eventi rilevati dal runtime, ad esempio richieste HTTP che attivano una funzione.
* **performanceCounters**: informazioni sulle prestazioni dei server su cui sono in esecuzione le funzioni.

Le altre tabelle sono riservate ai test di disponibilità e ai dati di telemetria del browser o del client. È possibile implementare i dati di telemetria personalizzati per aggiungerne altri.

All'interno di ogni tabella alcuni dati specifici per Funzioni si trovano nel campo `customDimensions`.  Ad esempio, la query seguente recupera tutte le tracce con livello di registrazione `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Il runtime indica `customDimensions.LogLevel` e `customDimensions.Category`. È possibile specificare campi aggiuntivi nei log in cui si scrive il codice funzione. Vedere la sezione [Registrazione strutturata](#structured-logging) più avanti in questo articolo.

## <a name="configure-categories-and-log-levels"></a>Configurare le categorie e i livelli di registrazione

È possibile usare Application Insights senza una configurazione personalizzata, ma la configurazione predefinita può creare volumi elevati di dati. Se si usa una sottoscrizione di Azure di Visual Studio, si potrebbe raggiunge il limite d'uso dati per Application Insights. La parte rstante di questo articolo illustra come configurare e personalizzare i dati inviati dalle funzioni ad Application Insights.

### <a name="categories"></a>Categorie

Il logger di Funzioni di Azure include un *categoria* per ogni log. La categoria indica quale parte del codice runtime o del codice funzione è stata scritta dal log. 

Il runtime di Funzioni crea log con una categoria che inizia con "Host". Ad esempio i log "funzione avviata," "funzione eseguita" e "funzione completata" rientrano nella categoria "Host.Executor". 

Se si scrivono i log nel codice funzione, la categoria è "Function".

### <a name="log-levels"></a>Livelli di registrazione

Il logger delle funzioni di Azure include anche un *livello di registrazione* per ogni log. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) è un'enumerazione e il codice integer ne indica la relativa importanza:

|LogLevel    |Codice|
|------------|---|
|Trace       | 0 |
|Debug       | 1 |
|Informazioni | 2 |
|Avviso     | 3 |
|Tipi di errore       | 4 |
|Critico    | 5 |
|Nessuna        | 6 |

Il livello di registrazione `None` è illustrato nella sezione successiva. 

### <a name="configure-logging-in-hostjson"></a>Configurare la registrazione nel file host.json

Il file *host.json* configura il numero di registrazioni che un'app per le funzioni invia ad Application Insights. Per ogni categoria, si indica il livello di registrazione minimo da inviare. Ad esempio:

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

1. Per i log con categoria "Host.Results" o "Function", inviare ad Application Insights solo i log di livello `Error` e livelli superiori. I log di livello `Warning` e livelli inferiori vengono ignorati.
2. Per i log con categoria Host.Aggregator, inviare tutti i log ad Application Insights. Il livello del log `Trace` corrisponde a quello che alcuni logger chiamano `Verbose`, ma usano `Trace` nel file *host.json*.
3. Per tutti gli altri log, inviare ad Application Insights solo i log di livello `Information` e superiori.

Il valore della categoria in *host.json* controlla la registrazione di tutte le categorie che iniziano con lo stesso valore. Ad esempio, "Host" in *host.json* controlla la registrazione di "Host.General", "Host.Executor", "Host.Results" e così via.

Se *host.json* include più categorie che iniziano con la stessa stringa, viene rilevata prima la corrispondenza con quelle più lunghe. Ad esempio, si supponga di volere che tutti gli elementi dalla fase di runtime, ad eccezione di "Host.Aggregator", siano registrati al livello `Error` mentre i log "Host.Aggregator" vengono registrati al livello `Information`:

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

Per eliminare tutti i log di una categoria, è possibile usare il livello di registrazione `None`. Nessun log viene scritto con quella categoria e non vi è alcun livello di registrazione superiore.

Le sezioni seguenti descrivono le categorie principali di log create dal runtime. 

### <a name="category-hostresults"></a>Categoria Host.Results

Questi log vengono visualizzati come "richieste" in Application Insights. Indicano l'esito positivo o negativo di una funzione.

![Grafico delle richieste](media/functions-monitoring/requests-chart.png)

Tutti questi log vengono scritti al livello `Information`, pertanto se si applica un filtro al livello `Warning` o superiore, questi dati non verranno visualizzati.

### <a name="category-hostaggregator"></a>Categoria Host.Aggregator

Questi log indicano il numero e le medie di chiamate alla funzione in un periodo di tempo [configurabile](#configure-the-aggregator). Il periodo predefinito è 30 secondi o 1000 risultati, ovvero quello che viene prima. 

I log sono disponibili nella tabella **customMetrics** in Application Insights. Gli esempi indicano il numero di esecuzioni, la percentuale di successo e la durata.

![query customMetrics](media/functions-monitoring/custom-metrics-query.png)

Tutti questi log vengono scritti al livello `Information`, pertanto se si applica un filtro al livello `Warning` o superiore, questi dati non verranno visualizzati.

### <a name="other-categories"></a>Altre categorie

Tutti i log per le categorie diverse da quelle già elencate sono disponibili nella tabella **tracce** in Application Insights.

![query di tracce](media/functions-monitoring/analytics-traces.png)

Tutti i log con le categorie che iniziano con "Host" vengono scritti dal runtime di Funzioni. I log "Funzione avviata e "Funzione completata" rientrano nella categoria "Host.Executor". Affinché vengano eseguiti correttamente, questi log devono avere il livello `Information`, mentre le eccezioni vengono registrate al livello `Error`. Il runtime crea anche il livello di registrazione `Warning`, ad esempio: messaggi di coda inviati alla coda non elaborabile.

I log scritti dal codice funzione rientrano nella categoria "Function" e possono avere qualsiasi livello di registrazione.

## <a name="configure-the-aggregator"></a>Configurare l'aggregatore

Come indicato nella sezione precedente, il runtime aggrega i dati sulle esecuzioni di funzioni in un periodo di tempo. Il periodo predefinito è 30 secondi o 1000 esecuzioni, ovvero quello che viene prima. È possibile configurare questa impostazione nel file *host.json*.  Ad esempio:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurare il campionamento

Application Insights ha una funzionalità di [campionamento](../application-insights/app-insights-sampling.md) che consente di evitare la produzione di un numero eccessivo di dati di telemetria nei picchi di carico. Quando il numero degli elementi di telemetria supera una frequenza specificata, Application Insights inizia a ignorare in modo casuale alcuni degli elementi in ingresso. Il valore predefinito per il numero massimo di elementi al secondo è 5. È possibile configurare il campionamento nel file *host.json*.  Ad esempio:

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

## <a name="write-logs-in-c-functions"></a>Scrivere i log nelle funzioni C#

È possibile scrivere log nel codice funzione che vengono visualizzati come tracce in Application Insights.

### <a name="ilogger"></a>ILogger

Usare il parametro [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) nelle funzioni anziché il parametro `TraceWriter`. I log creati con `TraceWriter` passano in Application Insights, ma `ILogger` consente di eseguire una [registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Con un oggetto `ILogger` è possibile chiamare [i metodi di estensione ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) `Log<level>` per creare i log. Ad esempio, il codice seguente scrive i log `Information` con la categoria "Function".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registrazione strutturata

I parametri usati nel messaggio del log sono determinati dall'ordine dei segnaposto, non dai nomi. Ad esempio, si supponga di disporre del codice seguente:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se si mantenere la stessa stringa del messaggio e si inverte l'ordine dei parametri, il testo del messaggio risultante mostrerebbe i valori in maniera errata.

I segnaposto vengono gestiti in modo da poter eseguire la registrazione strutturata. Oltre alla stringa del messaggio, Application Insights archivia le coppie nome-valore del parametro. Il risultato è che gli argomenti del messaggio diventano campi su cui è possibile eseguire delle query.

Ad esempio, se la chiamata al metodo del logger è simile all'esempio precedente, è possibile eseguire una query per il campo `customDimensions.prop__rowKey`. Il prefisso `prop__` viene aggiunto per verificare che non ci siano conflitti tra i campi aggiunti dal runtime e i campi aggiunti dal codice funzione.

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

### <a name="logging-custom-metrics"></a>Registrazione delle metriche personalizzate  

Nelle funzioni di script C# è possibile usare il metodo di estensione `LogMetric` su `ILogger` per creare metriche personalizzate in Application Insights. Ecco un esempio di chiamata al metodo:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Questo codice è un'alternativa alla chiamata di `TrackMetric` con [l'API di Application Insights per .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Scrivere i log in funzioni JavaScript

Nelle funzioni di Node.js usare `context.log` per scrivere i log. La registrazione strutturata non è abilitata.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Registrazione delle metriche personalizzate  

Nelle funzioni Node.js è possibile usare il metodo di estensione `context.log.metric` per creare metriche personalizzate in Application Insights. Ecco un esempio di chiamata al metodo:

```javascript
context.log.metric("TestMetric", 1234); 
```

Questo codice è un'alternativa alla chiamata di `trackMetric` con [l'SDK di Node.js per Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Dati di telemetria personalizzata nelle funzioni C#

È possibile usare il pacchetto NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) per inviare i dati di telemetria personalizzati ad Application Insights.

Di seguito è riportato un esempio di codice C# che usa l'[API di telemetria personalizzata](../application-insights/app-insights-api-custom-events-metrics.md). L'esempio fa riferimento a una libreria di classi .NET, ma il codice di Application Insights è lo stesso per lo script C#.

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

            // parse query parameter
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
            
            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
        
        // This correllates all telemetry with the current Function invocation
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

Non chiamare `TrackRequest` o `StartOperation<RequestTelemetry>`, in quanto compariranno le richieste di duplicazione per una chiamata alla funzione.  Il runtime di Funzioni rileva automaticamente le richieste.

Non impostare `telemetryClient.Context.Operation.Id`. Questa è un'impostazione globale e causerà errori di correlazione quando molte funzioni vengono eseguite contemporaneamente. Creare invece una nuova istanza di telemetria (`DependencyTelemetry`, `EventTelemetry`) e modificare la relativa proprietà `Context`. Passare quindi l'istanza di telemetria al metodo `Track` corrispondente `TelemetryClient` in (`TrackDependency()`, `TrackEvent()`). Questo assicura che la telemetria disponga dei dettagli di correlazione corretti per la chiamata di funzione corrente.

## <a name="custom-telemetry-in-javascript-functions"></a>Dati di telemetria personalizzati nelle funzioni JavaScript

L'[SDK di Node.js per Application Insights](https://www.npmjs.com/package/applicationinsights) è attualmente in versione beta. Di seguito è riportato un codice di esempio che invia i dati di telemetria personalizzati ad Application Insights:

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

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

Il parametro `tagOverrides` imposta `operation_Id` sull'ID di chiamata alla funzione. Questa impostazione consente di correlare tutti i dati di telemetria personalizzati e generati automaticamente per una chiamata alla funzione specifica.

## <a name="known-issues"></a>Problemi noti

### <a name="dependencies"></a>Dependencies

Le dipendenze tra la funzione e gli altri servizi non vengono visualizzate automaticamente, ma è possibile scrivere codice personalizzato per visualizzarle. Il codice di esempio nella [sezione relativa ai dati di telemetria personalizzati C#](#custom-telemetry-in-c-functions) ne illustra le modalità. Il codice di esempio crea una *mappa dell'applicazione* in Application Insights simile alla seguente:

![Mappa delle applicazioni](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Segnalare i problemi

Per segnalare un problema con l'integrazione di Application Insights in Funzioni o per inviare un suggerimento o una richiesta, [creare un problema in GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Monitoraggio senza Application Insights

Si consiglia di usare Application Insights per monitorare le funzioni perché offre un numero maggiore di dati e modalità di analisi dei dati migliori. Se si preferisce il sistema di registrazione incorporato che usa Archiviazione di Azure, è possibile continuare a usarlo.

### <a name="logging-to-storage"></a>Registrazione per l'archiviazione

La registrazione predefinita usa l'account di archiviazione specificato dalla stringa di connessione nell'impostazione app `AzureWebJobsDashboard`. In una pagina dell'app per le funzioni selezionare una funzione, selezionare la scheda **Monitoraggio** e quindi scegliere di mantenerla nella visualizzazione classica.

![Passaggio alla visualizzazione classica](media/functions-monitoring/switch-to-classic-view.png)

 Viene visualizzato un elenco di esecuzioni di funzioni. Selezionare l'esecuzione della funzione per esaminare la durata, i dati di input, gli errori e i file di log associati.

Se Application Insights è stato abilitato in precedenza, ma ora si intende passare alla registrazione predefinita, disabilitare manualmente Application Insights e quindi selezionare la scheda **Monitoraggio**. Per disabilitare l'integrazione di Application Insights, eliminare l'impostazione dell'app APPINSIGHTS_INSTRUMENTATIONKEY.

Anche se nella scheda **Monitoraggio** vengono visualizzati i dati di Application Insights, è possibile visualizzare i dati dei registri nel file system se la [registrazione incorporata non è stata disabilitata](#disable-built-in-logging). Nella risorsa di archiviazione, passare a File, selezionare il servizio file per la funzione e quindi passare a `LogFiles > Application > Functions > Function > your_function` per visualizzare il file di log.

### <a name="real-time-monitoring"></a>Monitoraggio in tempo reale

È possibile trasmettere in streaming i file di log a una sessione della riga di comando su una workstation locale usando l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).  

Per l'interfaccia della riga di comando di Azure 2.0 usare i comandi seguenti per eseguire l'accesso, scegliere la sottoscrizione e trasmettere in streaming i file di log:

```
az login
az account list
az account set <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

Per Azure PowerShell usare i comandi seguenti per aggiungere l'account di Azure, scegliere la sottoscrizione e trasmettere in streaming i file di log:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

Per altre informazioni, vedere [Procedura: Eseguire lo streaming dei log](../app-service/web-sites-enable-diagnostic-log.md#streamlogs).

### <a name="viewing-log-files-locally"></a>Visualizzazione dei file di log in locale

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Application Insights](/azure/application-insights/)
* [Registrazione di ASP.NET Core](/aspnet/core/fundamentals/logging/)
