---
title: Monitorare Funzioni di Azure
description: Informazioni su come usare Azure Application Insights con funzioni di Azure per monitorare l'esecuzione della funzione.
services: functions
author: ggailey777
manager: jeconnoc
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: glenga
ms.openlocfilehash: 0224d9ba5a430635e4675c2fb2bf354e7c975f31
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518729"
---
# <a name="monitor-azure-functions"></a>Monitorare Funzioni di Azure

[Funzioni di Azure](functions-overview.md) offre integrazione predefinita con [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) monitorare funzioni. Questo articolo illustra come configurare funzioni di Azure per inviare i file di log generati dal sistema in Application Insights.

![Esplora metriche di Application Insights](media/functions-monitoring/metrics-explorer.png)

Funzioni di Azure offre anche funzionalità di monitoraggio incorporate che non usano Application Insights. Si consiglia di usare Application Insights perché offre un numero maggiore di dati e modalità di analisi dei dati migliori.

## <a name="application-insights-pricing-and-limits"></a>Prezzi e limiti di Application Insights

È possibile provare gratuitamente l'integrazione di Application Insights con le app per le funzioni. È previsto un limite giornaliero per la quantità di dati possono essere elaborati gratuitamente. Si potrebbe raggiunge questo limite durante i test. Quando il limite giornaliero è quasi raggiunto, Azure invia notifiche tramite il portale e messaggi di posta elettronica. Se si perdere tali avvisi e ha raggiunto il limite, i nuovi log non sarà più visualizzato nelle query di Application Insights. Tenere presente il limite per evitare inutili tempi di risoluzione dei problemi. Per altre informazioni, vedere [Gestire volumi di dati e prezzi in Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Abilitare l'integrazione di Application Insights

Affinché un'app per le funzioni invii dati ad Application Insights, è necessario conoscere la chiave di strumentazione di una risorsa di Application Insights. La chiave deve essere specificata in un'impostazione dell'app denominata **APPINSIGHTS_INSTRUMENTATIONKEY**.

È possibile configurare la connessione nel [portale di Azure](https://portal.azure.com):

* [Connetti automaticamente una nuova app per le funzioni](#new-function-app)
* [Connettersi manualmente una risorsa di Application Insights](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Nuova app per le funzioni
<!-- Add a transitional sentence to introduce the procedure. -->

1. Passare alla pagina **Crea** dell'app per le funzioni.

1. Impostare l'interruttore di **Application Insights** su **On** (Attivo).

1. Selezionare una **Località di Application Insights**. Scegliere l'area più vicina all'area dell'app per le funzioni e in un' [area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si desidera archiviare i dati.

   ![Abilitare Application Insights mentre si crea un'app per le funzioni](media/functions-monitoring/enable-ai-new-function-app.png)

1. Immettere le altre informazioni richieste e selezionare **Crea**.

Il passaggio successivo consiste nel [disabilitare la registrazione incorporata](#disable-built-in-logging).


<a id="manually-connect-an-app-insights-resource"></a>
### <a name="application-insights-resource"></a>Risorsa di Application Insights 
<!-- Add a transitional sentence to introduce the procedure. -->

1. Creare la risorsa di Application Insights. Impostare il tipo di applicazione su **Generale**.

   ![Creare una risorsa di Application Insights di tipo generale](media/functions-monitoring/ai-general.png)

1. Copiare la chiave di strumentazione dalla pagina **Informazioni di base** della risorsa di Application Insights. Punto alla fine del valore della chiave visualizzata per ottenere un **fare clic per copiare** pulsante.

   ![Copiare la chiave di strumentazione di Application Insights](media/functions-monitoring/copy-ai-key.png)

1. Nella finestra dell'app per le funzioni **le impostazioni dell'applicazione** pagina [aggiungere un'impostazione dell'app](functions-how-to-use-azure-function-app-settings.md#settings) selezionando **Aggiungi nuova impostazione**. Denominare la nuova impostazione **APPINSIGHTS_INSTRUMENTATIONKEY** e incollare la chiave di strumentazione copiata.

   ![Aggiungere la chiave di strumentazione alle impostazioni dell'app](media/functions-monitoring/add-ai-key.png)

1. Selezionare **Salva**.

<!-- Before the next H2 heading, add transitional sentences to summarize why the procedures were necessary. -->

## <a name="disable-built-in-logging"></a>Disabilitare la registrazione predefinita

Quando si abilita Application Insights, disabilitare la registrazione predefinita che usa l'archiviazione di Azure. La registrazione predefinita risulta utile per i test con carichi di lavoro leggeri, ma non è destinata all'uso in produzione di carichi elevati. Per il monitoraggio della produzione, si consiglia di Application Insights. Se la registrazione predefinita viene usata nell'ambiente di produzione, il record di registrazione potrebbe essere incompleto a causa di limitazione delle richieste nell'archiviazione di Azure.

Per disabilitare la registrazione predefinita, eliminare l'impostazione app `AzureWebJobsDashboard`. Per informazioni su come eliminare le impostazioni app nel portale di Azure, vedere la sezione relativa alle **impostazioni dell'applicazione** in [Come gestire un'app per le funzioni nel portale di Azure](functions-how-to-use-azure-function-app-settings.md#settings). Prima di eliminare l'impostazione dell'app, assicurarsi che nessuna funzione esistente nella stessa app per le funzioni usano l'impostazione per i trigger di archiviazione di Azure o le associazioni.

## <a name="view-telemetry-in-monitor-tab"></a>Visualizzare dati di telemetria nella scheda Monitoraggio

Dopo aver impostato l'integrazione di Application Insights come illustrato nelle sezioni precedenti, è possibile visualizzare i dati di telemetria nel **Monitor** scheda.

1. Nella pagina dell'app (funzione), selezionare una funzione che ha eseguito almeno una volta dopo che è stato configurato Application Insights. Quindi selezionare il **Monitor** scheda.

   ![Selezionare la scheda Monitoraggio](media/functions-monitoring/monitor-tab.png)

1. Selezionare **Aggiorna** periodicamente, fino a quando non viene visualizzato l'elenco di chiamate di funzione.

   Possono volerci fino a cinque minuti per l'elenco venga visualizzato anche se il client di telemetria invia in batch dei dati per la trasmissione al server. (Il ritardo non è valida per il [Live Stream metriche](../azure-monitor/app/live-stream.md). Tale servizio si connette all'host di Funzioni quando si carica la pagina, pertanto i registri vengono trasmessi direttamente alla pagina.

   ![Elenco di chiamate](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Per visualizzare i registri per una chiamata di funzione particolare, selezionare il collegamento alla colonna **Dati** per tale chiamata.

   ![Collegamento ai dettagli di chiamata](media/functions-monitoring/invocation-details-link-ai.png)

   L'output di registrazione per tale chiamata viene visualizzato in una nuova pagina.

   ![Dettagli della chiamata](media/functions-monitoring/invocation-details-ai.png)

Entrambe le pagine (elenco di chiamate e i dettagli di chiamata) collegano la query di Application Insights Analitica che recupera i dati:

![Esecuzione in Application Insights](media/functions-monitoring/run-in-ai.png)

![Elenco di chiamate di analisi di Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Queste query, si noterà che l'elenco chiamate è limitato agli ultimi 30 giorni. L'elenco Mostra non più di 20 righe (`where timestamp > ago(30d) | take 20`). L'elenco di dettagli di chiamata è per gli ultimi 30 giorni senza alcun limite.

Per altre informazioni, vedere [Eseguire query sui dati di telemetria](#query-telemetry-data) più avanti in questo articolo.

## <a name="view-telemetry-in-application-insights"></a>Visualizzare i dati di telemetria in Application Insights

Per aprire Application Insights da un'app per le funzioni nel portale di Azure, fare clic per l'app per le funzioni **Panoramica** pagina. Sotto **le funzionalità configurate**, selezionare **Application Insights**.

![Apri Application Insights, dalla pagina di panoramica dell'app (funzione)](media/functions-monitoring/ai-link.png)

Per informazioni su come usare Application Insights, vedere la [documentazione su Application Insights](https://docs.microsoft.com/azure/application-insights/). Questa sezione mostra alcuni esempi su come visualizzare i dati in Application Insights. Se si ha già familiarità con Application Insights, è possibile passare direttamente al [le sezioni su come configurare e personalizzare i dati di telemetria](#configure-categories-and-log-levels).

Nelle [Esplora metriche](../azure-monitor/app/metrics-explorer.md), è possibile creare grafici e avvisi basati sulle metriche. Le metriche includono il numero di chiamate alla funzione, il tempo di esecuzione e le percentuali di successo.

![Esplora metriche](media/functions-monitoring/metrics-explorer.png)

Nella scheda [Errori](../azure-monitor/app/asp-net-exceptions.md) è possibile creare grafici e avvisi in base agli errori di funzione e alle eccezioni del server. Il **nome dell'operazione** corrisponde al nome della funzione. Gli errori di dipendenze non vengono visualizzati solo se si implementano i dati di telemetria personalizzati per le dipendenze.

![Errori](media/functions-monitoring/failures.png)

Nella scheda [Prestazioni](../azure-monitor/app/performance-counters.md) è possibile analizzare i problemi di prestazioni.

![Prestazioni](media/functions-monitoring/performance.png)

La scheda **Server** mostra l'uso delle risorse e la velocità effettiva per ogni server. Questi dati possono essere utili negli scenari di debug in cui le funzioni bloccano le risorse sottostanti. I server sono denominati **Istanze del ruolo del cloud**.

![Server](media/functions-monitoring/servers.png)

Il [Live Stream metriche](../azure-monitor/app/live-stream.md) scheda Mostra i dati di metrica come viene creato in tempo reale.

![Live stream](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Query sui dati di telemetria

[Application Insights Analitica](../azure-monitor/app/analytics.md) consente di accedere a tutti i dati di telemetria sotto forma di tabelle in un database. Analytics offre un linguaggio di query per l'estrazione, la manipolazione e la visualizzazione dei dati.

![Selezionare Analytics](media/functions-monitoring/select-analytics.png)

![Esempio di Analytics](media/functions-monitoring/analytics-traces.png)

Questo è un esempio che mostra la distribuzione delle richieste per ruolo di lavoro negli ultimi 30 minuti.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Le tabelle disponibili vengono visualizzate nei **Schema** scheda a sinistra. Nelle tabelle seguenti è possibile trovare i dati generati dalle chiamate alla funzione:

* **traces**: Log creati dal runtime e dal codice della funzione.
* **Le richieste**: Una richiesta per ogni chiamata di funzione.
* **exceptions**: Tutte le eccezioni generate dal runtime.
* **customMetrics**: Numero di chiamate di esito positivo e negativo, percentuale di successo e la durata.
* **customEvents**: Eventi rilevati dal runtime, ad esempio: richieste HTTP che attivano una funzione.
* **performanceCounters**: Informazioni sulle prestazioni dei server che eseguono le funzioni.

Le altre tabelle sono per i test di disponibilità e i dati di telemetria client e del browser. È possibile implementare i dati di telemetria personalizzati per aggiungerne altri.

All'interno di ogni tabella alcuni dati specifici per Funzioni si trovano nel campo `customDimensions`.  Ad esempio, la query seguente recupera tutte le tracce con livello di registrazione `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Il runtime fornisce il `customDimensions.LogLevel` e `customDimensions.Category` campi. È possibile specificare campi aggiuntivi nei log che scrivono nel codice della funzione. Vedere la sezione [Registrazione strutturata](#structured-logging) più avanti in questo articolo.

## <a name="configure-categories-and-log-levels"></a>Configurare le categorie e i livelli di registrazione

È possibile usare Application Insights senza una configurazione personalizzata. La configurazione predefinita può comportare volumi elevati di dati. Se si usa una sottoscrizione di Azure di Visual Studio, si potrebbe raggiunge il limite d'uso dati per Application Insights. Più avanti in questo articolo descrive come configurare e personalizzare i dati inviati dalle funzioni ad Application Insights.

### <a name="categories"></a>Categorie

Il logger di Funzioni di Azure include un *categoria* per ogni log. La categoria indica quale parte del codice runtime o del codice funzione è stata scritta dal log. 

Il runtime di funzioni crea log con una categoria che iniziano con "Host". Il "funzione avviata," "funzione eseguita" e i log "funzione completata" dispongono della categoria "Host. executor". 

Se si scrivono i log nel codice della funzione, la categoria è "Function".

### <a name="log-levels"></a>Livelli di registrazione

Il logger di funzioni di Azure include anche un *livello di registrazione* ogni log. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) è un'enumerazione e il codice integer ne indica la relativa importanza:

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

### <a name="log-configuration-in-hostjson"></a>Configurazione di log nell'host. JSON

Il file [host.json](functions-host-json.md) configura il numero di registrazioni che un'app per le funzioni invia ad Application Insights. Per ogni categoria, si indica il livello di registrazione minimo da inviare. Ci sono due esempi: gli obiettivi di esempio prima la [funzioni versione 2.x del runtime](functions-versions.md#version-2x) (.NET Core) e il secondo esempio è per la versione 1.x del runtime.

### <a name="version-2x"></a>Versione 2.x

Il runtime della versione 2.x usa la [gerarchia di filtro del log di .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

* Per i log con categoria `Host.Results` oppure `Function`, invia solo `Error` livello e versioni successive per Application Insights. I log di livello `Warning` e livelli inferiori vengono ignorati.
* Per i log con categoria `Host.Aggregator`, inviare tutti i log ad Application Insights. Il livello del log `Trace` corrisponde a quello che alcuni logger chiamano `Verbose`, ma usano `Trace` nel file [host.json](functions-host-json.md).
* Per tutti gli altri log, inviare ad Application Insights solo i log di livello `Information` e superiori.

Il valore della categoria in [host.json](functions-host-json.md) controlla la registrazione di tutte le categorie che iniziano con lo stesso valore. `Host` nelle [host. JSON](functions-host-json.md) la registrazione per i controlli `Host.General`, `Host.Executor`, `Host.Results`e così via.

Se [host.json](functions-host-json.md) include più categorie che iniziano con la stessa stringa, viene rilevata prima la corrispondenza con quelle più lunghe. Si supponga che tutti gli elementi dalla fase di esecuzione, ad eccezione `Host.Aggregator` per accedere alla `Error` livello, ma si desidera `Host.Aggregator` per accedere al `Information` livello:

### <a name="version-2x"></a>Versione 2.x 

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

Per eliminare tutti i log di una categoria, è possibile usare il livello di registrazione `None`. Nessun log viene scritto con quella categoria e non vi è alcun livello di registrazione sopra di esso.

Le sezioni seguenti descrivono le categorie principali di log create dal runtime. 

### <a name="category-hostresults"></a>Categoria Host.Results

Questi log vengono visualizzati come "richieste" in Application Insights. Indicano l'esito positivo o negativo di una funzione.

![Grafico delle richieste](media/functions-monitoring/requests-chart.png)

Tutti questi log vengono scritti in `Information` livello. Se si filtra in `Warning` o versioni successive, non verrà visualizzato uno di questi dati.

### <a name="category-hostaggregator"></a>Categoria Host.Aggregator

Questi log indicano il numero e le medie di chiamate alla funzione in un periodo di tempo [configurabile](#configure-the-aggregator). Il periodo predefinito è 30 secondi o 1000 risultati, ovvero quello che viene prima. 

I log sono disponibili nella tabella **customMetrics** in Application Insights. Esempi sono il numero di esecuzioni, la percentuale di successo e durata.

![query customMetrics](media/functions-monitoring/custom-metrics-query.png)

Tutti questi log vengono scritti in `Information` livello. Se si filtra in `Warning` o versioni successive, non verrà visualizzato uno di questi dati.

### <a name="other-categories"></a>Altre categorie

Tutti i log per le categorie diverse da quelle già elencate sono disponibili nella tabella **tracce** in Application Insights.

![query di tracce](media/functions-monitoring/analytics-traces.png)

Tutti i log con categorie che iniziano con `Host` vengono scritti dal runtime di funzioni. I log "Funzione completata" e "Funzione avviata" hanno categoria `Host.Executor`. Per le esecuzioni riuscite, questi log sono `Information` livello. Le eccezioni vengono registrate in `Error` livello. Il runtime crea anche il livello di registrazione `Warning`, ad esempio: messaggi di coda inviati alla coda non elaborabile.

I log scritti dal codice di funzione hanno categoria `Function` e può essere qualsiasi livello di registrazione.

## <a name="configure-the-aggregator"></a>Configurare l'aggregatore

Come indicato nella sezione precedente, il runtime aggrega i dati sulle esecuzioni di funzioni in un periodo di tempo. Il periodo predefinito è 30 secondi o 1000 esecuzioni, ovvero quello che viene prima. È possibile configurare questa impostazione nel file [host.json](functions-host-json.md).  Ad esempio:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurare il campionamento

Application Insights ha una funzionalità di [campionamento](../azure-monitor/app/sampling.md) che consente di evitare la produzione di un numero eccessivo di dati di telemetria nei picchi di carico. Quando la frequenza dei dati di telemetria supera una soglia specificata, Application Insights inizia a ignorare in modo casuale alcuni degli elementi in ingresso. L'impostazione predefinita per il numero massimo di elementi al secondo è pari a cinque. È possibile configurare il campionamento nel file [host.json](functions-host-json.md).  Ad esempio:

### <a name="version-2x"></a>Versione 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 5
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
> Il [campionamento](../azure-monitor/app/sampling.md) è abilitato per impostazione predefinita. Se sembra che manchino i dati, si potrebbe essere necessario regolare le impostazioni di campionamento per adattarlo allo scenario di monitoraggio particolare.

## <a name="write-logs-in-c-functions"></a>Scrivere i log nelle funzioni C#

È possibile scrivere log nel codice funzione che vengono visualizzati come tracce in Application Insights.

### <a name="ilogger"></a>ILogger

Usare il parametro [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) nelle funzioni anziché il parametro `TraceWriter`. I log creati usando `TraceWriter` passare ad Application Insights, ma `ILogger` permette di svolgere [registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Con un oggetto `ILogger` è possibile chiamare i [metodi di estensione su ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) `Log<level>` per creare i log. Il codice seguente scrive `Information` log con categoria "Function".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registrazione strutturata

I parametri usati nel messaggio del log sono determinati dall'ordine dei segnaposto, non dai nomi. Si supponga di avere il codice seguente:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se si mantenere la stessa stringa del messaggio e si inverte l'ordine dei parametri, il testo del messaggio risultante mostrerebbe i valori in maniera errata.

I segnaposto vengono gestiti in modo da poter eseguire la registrazione strutturata. Application Insights archivia le coppie nome-valore di parametro e la stringa di messaggio. Il risultato è che gli argomenti del messaggio diventano campi su cui è possibile eseguire delle query.

Se la chiamata al metodo del logger è simile a quello precedente, è possibile eseguire query sul campo `customDimensions.prop__rowKey`. Il `prop__` prefisso viene aggiunto per verificare che siano presenti conflitti tra i campi aggiunti dal runtime e il codice della funzione di campi aggiunti.

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

Questo codice è un'alternativa alla chiamata `TrackMetric` usando l'API di Application Insights per .NET.

## <a name="write-logs-in-javascript-functions"></a>Scrivere i log in funzioni JavaScript

Nelle funzioni di Node.js usare `context.log` per scrivere i log. Non è abilitata la registrazione strutturata.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Registrazione delle metriche personalizzate

Quando si eseguono [versione 1.x](functions-versions.md#creating-1x-apps) del runtime di funzioni, è possono usare funzioni di Node. js di `context.log.metric` metodo per creare metriche personalizzate in Application Insights. Questo metodo non è attualmente supportato nella versione 2.x. Ecco un esempio di chiamata al metodo:

```javascript
context.log.metric("TestMetric", 1234);
```

Questo codice è un'alternativa alla chiamata `trackMetric` usando il SDK di Node. js per Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Dati di telemetria personalizzati di log in C# funzioni

È possibile usare il pacchetto NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) per inviare i dati di telemetria personalizzati ad Application Insights. L'esempio C# seguente usa l'[API di telemetria personalizzata](../azure-monitor/app/api-custom-events-metrics.md). L'esempio fa riferimento a una libreria di classi .NET, ma il codice di Application Insights è lo stesso per lo script C#.

### <a name="version-2x"></a>Versione 2.x

La versione 2.x del runtime usa le funzionalità più recenti in Application Insights per correlare automaticamente i dati di telemetria con l'operazione corrente. Non è necessario impostare manualmente l'operazione `Id`, `ParentId`, o `Name` campi.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        // In Functions v2, TelemetryConfiguration.Active is initialized with the InstrumentationKey
        // from APPINSIGHTS_INSTRUMENTATIONKEY. Creating a default TelemetryClient like this will 
        // automatically use that key for all telemetry. It will also enable telemetry correlation
        // with the current operation.
        // If you require a custom TelemetryConfiguration, create it initially with
        // TelemetryConfiguration.CreateDefault() to include this automatic correlation.
        private static TelemetryClient telemetryClient = new TelemetryClient();

        [FunctionName("HttpTrigger2")]
        public static Task<IActionResult> Run(
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
            telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
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
            dependency.Context.User.Id = name;
            telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

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

Non chiamare `TrackRequest` o `StartOperation<RequestTelemetry>` quanto compariranno le richieste di duplicazione per una chiamata alla funzione.  Il runtime di Funzioni rileva automaticamente le richieste.

Non impostare `telemetryClient.Context.Operation.Id`. Questa impostazione globale, non corretta di correlazione quando molte funzioni vengono eseguite contemporaneamente. Creare invece una nuova istanza di telemetria (`DependencyTelemetry`, `EventTelemetry`) e modificare la relativa proprietà `Context`. Passare quindi l'istanza di telemetria al metodo `Track` corrispondente `TelemetryClient` in (`TrackDependency()`, `TrackEvent()`). Questo metodo assicura che i dati di telemetria con i dettagli di correlazione corretti per la chiamata alla funzione corrente.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Dati di telemetria personalizzati log nelle funzioni JavaScript

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

    context.done();
};
```

Il `tagOverrides` set di parametri di `operation_Id` all'ID di chiamata. della funzione Questa impostazione consente di correlare tutti i dati di telemetria personalizzati e generati automaticamente per una chiamata di funzione specifica.

## <a name="known-issues"></a>Problemi noti
<!-- Add a transitional sentence to introduce the section. -->

### <a name="dependencies"></a>Dependencies

Dipendenze con la funzione agli altri servizi non vengono visualizzati automaticamente. È possibile scrivere codice personalizzato per visualizzare le dipendenze. Per esempi, vedere il codice di esempio il [ C# sezione dati di telemetria personalizzati](#log-custom-telemetry-in-c-functions). Il codice di esempio crea un' *mappa delle applicazioni* in Application Insights simile a quello riportato nell'immagine seguente:

![Mappa delle applicazioni](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Segnalare i problemi

Per segnalare un problema con l'integrazione di Application Insights in Funzioni o per inviare un suggerimento o una richiesta, [creare un problema in GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitor-without-application-insights"></a>Monitoraggio senza Application Insights

Si consiglia di Application Insights per monitorare le funzioni. Offre più dati e i modi migliori per analizzare i dati. Ma se si preferisce il sistema di registrazione predefiniti che usa l'archiviazione di Azure, è possibile continuare a utilizzare tale metodo.

### <a name="azure-storage-account-for-logging"></a>Account di archiviazione di Azure per la registrazione

La registrazione predefinita usa l'account di archiviazione specificato dalla stringa di connessione nell'impostazione app `AzureWebJobsDashboard`. In una pagina dell'app funzioni, selezionare una funzione e quindi selezionare il **Monitor** scheda e scegliere di mantenere i dati nelle **visualizzazione classica**.

![Passaggio alla visualizzazione classica](media/functions-monitoring/switch-to-classic-view.png)

Viene visualizzato un elenco di esecuzioni di funzioni. Selezionare l'esecuzione della funzione per esaminare la durata, i dati di input, gli errori e i file di log associati.

Se è stato abilitato Application Insights, è possibile tornare a usando la registrazione predefinita. Disabilitare manualmente Application Insights e quindi selezionare il **Monitor** scheda. Per disabilitare l'integrazione di Application Insights, eliminare il `APPINSIGHTS_INSTRUMENTATIONKEY` impostazione dell'app.

Anche se nella scheda **Monitoraggio** vengono visualizzati i dati di Application Insights, è possibile visualizzare i dati dei registri nel file system se la [registrazione incorporata non è stata disabilitata](#disable-built-in-logging). Nella risorsa di archiviazione, passare a **file**, quindi selezionare il servizio file per la funzione. Passare quindi a **LogFiles** > **applicazione** > **funzioni** > **funzione**  >  **your_function** per visualizzare il file di log.

### <a name="real-time-monitoring"></a>Monitoraggio in tempo reale

È possibile trasmettere i file di log a una sessione della riga di comando su una workstation locale. Usare la [interfaccia della riga di comando Azure (CLI)](/cli/azure/install-azure-cli) oppure [Azure PowerShell](/powershell/azure/overview).  

Per il comando di Azure, usare i comandi seguenti per accedere, scegliere la sottoscrizione e i file di log di flusso:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

Per Azure PowerShell usare i comandi seguenti per aggiungere l'account di Azure, scegliere la sottoscrizione e trasmettere in streaming i file di log:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <function app name> -Tail
```

Per altre informazioni, vedere [Procedura: Eseguire lo streaming dei log](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Application Insights](/azure/application-insights/)
* [Registrazione di ASP.NET Core](/aspnet/core/fundamentals/logging/)
