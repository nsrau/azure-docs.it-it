---
title: Diagnostica in Funzioni permanenti - Azure
description: Informazioni su come eseguire la diagnostica dei problemi con l'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: d2badee3eaa5a9af48e89adc1b59beacc1571792
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933511"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostica in Durable Functions in Azure

Per la diagnostica dei problemi con [Funzioni permanenti](durable-functions-overview.md) sono disponibili diverse opzioni. Alcune opzioni sono le stesse disponibili per le funzioni normali e alcune sono specifiche di Funzioni permanenti.

## <a name="application-insights"></a>Application Insights

Per eseguire la diagnostica e il monitoraggio in Funzioni di Azure, è consigliabile usare [Application Insights](../../azure-monitor/app/app-insights-overview.md). Lo stesso consiglio vale per Funzioni permanenti. Per una panoramica su come usare Application Insights nell'app per le funzioni, vedere [Monitorare Funzioni di Azure](../functions-monitoring.md).

L'estensione Funzioni permanenti di Funzioni di Azure genera anche *eventi di rilevamento* che consentono di tenere traccia dell'esecuzione end-to-end di un'orchestrazione. È possibile individuarli ed eseguire query su di essi usando lo strumento [Application Insights Analytics](../../azure-monitor/app/analytics.md) nel portale di Azure.

### <a name="tracking-data"></a>Dati di rilevamento

Ogni evento del ciclo di vita di un'istanza di orchestrazione genera la scrittura di un evento di rilevamento nella raccolta **traces** in Application Insights. Questo evento contiene un payload **customDimensions** con diversi campi.  Ai nomi dei campi viene anteposta la stringa `prop__`.

* **hubName**: nome dell'hub attività in cui vengono eseguite le orchestrazioni.
* **appName**: Nome dell'app per le funzioni. Questo campo è utile quando più app per le funzioni condividono la stessa istanza di Application Insights.
* **slotName**: [slot di distribuzione](../functions-deployment-slots.md) in cui è in esecuzione l'app per le funzioni corrente. Questo campo è utile quando si usano gli slot di distribuzione per controllare le versioni delle orchestrazioni.
* **functionName**: nome della funzione dell'agente di orchestrazione o di attività.
* **functionType**: tipo di funzione, ad esempio **agente di orchestrazione** o **attività**.
* **instanceId**: ID univoco dell'istanza di orchestrazione.
* **state**: stato di esecuzione del ciclo di vita dell'istanza. I valori validi includono:
  * **Scheduled**: la funzione è stata pianificata per l'esecuzione, ma non è stata ancora avviata.
  * **Started**: la funzione è stata avviata, ma non è ancora in stato di attesa o non è stata completata.
  * **Awaited**: l'agente di orchestrazione ha pianificato un lavoro ed è in attesa del completamento.
  * **Listening**: l'agente di orchestrazione è in ascolto di una notifica degli eventi esterni.
  * **Completed**: la funzione è stata completata.
  * **Failed**: la funzione ha avuto esito negativo generando un errore.
* **reason**: dati aggiuntivi associati all'evento di rilevamento. Ad esempio, se un'istanza è in attesa di una notifica degli eventi esterni, questo campo indica il nome dell'evento di cui è in attesa. Se una funzione ha avuto esito negativo, il campo contiene i dettagli dell'errore.
* **isReplay**: valore booleano che indica se per l'evento di rilevamento è prevista la riesecuzione.
* **extensionVersion**: versione dell'estensione Durable Task. Questi dati sono particolarmente importanti quando si segnalano possibili bug nell'estensione. Le istanze con esecuzione prolungata possono segnalare più versioni, se si verifica un aggiornamento durante l'esecuzione.
* **sequenceNumber**: numero di sequenza di esecuzione per un evento. In combinazione con il timestamp consente di ordinare gli eventi per ora di esecuzione. *Si noti che questo numero verrà reimpostato su zero se l'host viene riavviato durante l'esecuzione dell'istanza. È quindi importante eseguire sempre l'ordinamento prima per timestamp e quindi per sequenceNumber.*

Il livello di dettaglio dei dati di rilevamento generati ad Application Insights può essere configurato nella sezione `logger` (Funzioni 1.x) o `logging` (Funzioni 2.x) del file `host.json`.

#### <a name="functions-1x"></a>Funzioni 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Funzioni 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Per impostazione predefinita vengono generati tutti gli eventi di rilevamento senza riesecuzione. Il volume dei dati può essere ridotto impostando `Host.Triggers.DurableTask` su `"Warning"` o `"Error"`; in questo caso gli eventi di rilevamento verranno generati solo per le situazioni eccezionali.

Per abilitare la creazione di eventi di riproduzione con orchestrazione verbose, `LogReplayEvents` può essere impostato su `true` nel file `host.json` sotto `durableTask` come illustrato:

#### <a name="functions-1x"></a>Funzioni 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Funzioni 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Per impostazione predefinita, la telemetria di Application Insights viene campionata dal runtime di Funzioni di Azure per evitare di generare dati con eccessiva frequenza. In questo modo è possibile che le informazioni di rilevamento vengano perse quando si verificano molti eventi del ciclo di vita in un breve periodo di tempo. L'[articolo sul monitoraggio in Funzioni di Azure](../functions-monitoring.md#configure-sampling) illustra come configurare questo comportamento.

### <a name="single-instance-query"></a>Query per singola istanza

La query seguente mostra i dati di rilevamento cronologici per una singola istanza di orchestrazione della funzione [Hello Sequence](durable-functions-sequence.md). È scritta con [Application Insights Query Language (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Esclude la riesecuzione in modo da mostrare solo il percorso di esecuzione *logico*. Gli eventi possono essere ordinati per `timestamp` e `sequenceNumber`, come illustrato nella query seguente:

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Il risultato è un elenco di eventi di traccia che mostrano il percorso di esecuzione dell'orchestrazione, incluse eventuali funzioni di attività ordinate per ora di esecuzione in ordine ascendente.

![Query di Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Query di riepilogo dell'istanza

La query seguente mostra lo stato di tutte le istanze di orchestrazione eseguite in un intervallo di tempo specificato.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Il risultato è un elenco di ID istanza e dello stato di runtime corrente.

![Query di Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registrazione

È importante tenere presente il comportamento di riesecuzione dell'agente di orchestrazione quando si scrivono i log direttamente da una funzione dell'agente di orchestrazione. Ad esempio, si consideri la seguente funzione dell'agente di orchestrazione:

### <a name="precompiled-c"></a>C# precompilato

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

I dati di log risultanti eseguono una ricerca simile alla seguente:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Tenere presente che mentre i log contengono la dichiarazione di chiamata F1, F2 e F3, queste funzioni vengono chiamate *effettivamente* solo al primo rilevamento. Le chiamate successive eseguite durante la riesecuzione vengono ignorate e gli output vengono rieseguiti nella logica dell'agente di orchestrazione.

Se si vuole eseguire la registrazione solo con l'esecuzione senza riesecuzione, è possibile scrivere un'espressione condizionale per eseguire la registrazione solo se `IsReplaying` è `false`. Si consideri l'esempio precedente, ma questa volta con i controlli di riesecuzione.

#### <a name="precompiled-c"></a>C# precompilato

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Con questa modifica, l'output del log è il seguente:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Stato personalizzato

Lo stato dell'orchestrazione personalizzato consente di impostare un valore di stato per la funzione dell'agente di orchestrazione. Questo stato viene fornito tramite l'API della query sullo stato HTTP o l'API `DurableOrchestrationClient.GetStatusAsync`. Lo stato di un'orchestrazione personalizzato consente di eseguire il monitoraggio in modo più completo per le funzioni dell'agente di orchestrazione. Il codice della funzione dell'agente di orchestrazione, ad esempio, può includere chiamate `DurableOrchestrationContext.SetCustomStatus` per aggiornare lo stato di avanzamento per un'operazione con esecuzione prolungata. Un client, ad esempio una pagina Web o un sistema esterno, può eseguire una query periodicamente sulle API di query dello stato HTTP per ottenere informazioni più dettagliate. Un esempio che usa `DurableOrchestrationContext.SetCustomStatus` viene indicato di seguito:

### <a name="precompiled-c"></a>C# precompilato

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Mentre l'orchestrazione è in esecuzione, i client esterni possono recuperare questo stato personalizzato:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

I client visualizzano la risposta seguente:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> Il payload dello stato personalizzato è limitato a 16 KB di testo JSON UTF-16, perché deve rientrare in una colonna dell'archiviazione tabelle di Azure. È possibile usare l'archiviazione esterna se è necessario un payload di dimensioni maggiori.

## <a name="debugging"></a>Debug

Funzioni di Azure supporta direttamente il debug del codice della funzione e lo stesso supporto si estende a Funzioni permanenti, in esecuzione in Azure o in locale. Quando si esegue il debug, è tuttavia opportuno conoscere alcuni comportamenti:

* **Replay**: Le funzioni dell'agente di orchestrazione vengono regolarmente [riprodotte](durable-functions-orchestrations.md#reliability) quando vengono ricevuti nuovi input. Ciò implica che una singola esecuzione *logica* di una funzione dell'agente di orchestrazione può comportare l'accesso più volte allo stesso punto di interruzione, soprattutto se è impostata all'inizio del codice della funzione.
* **Await**: Ogni volta `await` che un oggetto viene rilevato in una funzione dell'agente di orchestrazione, restituisce il controllo al dispatcher di Durable Task Framework. Se è la prima volta che uno specifico `await` è stato rilevato, l'attività associata non viene ripresa *mai*. Poiché l'attività non viene ripresa mai, non è possibile eseguire lo *step over* per await (F10 in Visual Studio). Questa operazione funziona solo se un'attività è in corso di riesecuzione.
* **Timeout di messaggistica**: Durable Functions usa internamente i messaggi della coda per guidare l'esecuzione di funzioni di orchestrazione, attività ed entità. In un ambiente con più macchine virtuali, a causa dell'interruzione del debug per lunghi periodi di tempo è possibile che un'altra macchina virtuale prelevi il messaggio, generando un'esecuzione duplicata. Questo comportamento esiste anche per le normali funzioni attivate da coda, ma è importante sottolinearlo in questo contesto poiché le code sono un dettaglio dell'implementazione.
* **Arresto e avvio**: I messaggi nelle funzioni permanenti vengono mantenuti tra le sessioni di debug. Se si interrompe il debug e si termina il processo host locale mentre è in esecuzione una funzione durevole, la funzione potrebbe essere rieseguita automaticamente in una sessione di debug futura. Questa operazione può generare confusione quando non è prevista. La cancellazione di tutti i messaggi dalle [code di archiviazione interne](durable-functions-perf-and-scale.md#internal-queue-triggers) tra le sessioni di debug è una tecnica per evitare questo comportamento.

> [!TIP]
> Quando si impostano punti di interruzione nelle funzioni dell'agente di orchestrazione, se si desidera interrompere l'esecuzione solo in assenza di riproduzione, è possibile impostare `IsReplaying` un `false`punto di interruzione condizionale che si interrompe solo se è.

## <a name="storage"></a>Archiviazione

Per impostazione predefinita, Funzioni permanenti archivia lo stato in Archiviazione di Azure. È quindi possibile controllare lo stato delle orchestrazioni tramite strumenti come [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Schermata Azure Storage Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Risulta utile per il debug perché viene visualizzato esattamente lo stato in cui potrebbe trovarsi un'orchestrazione. È possibile esaminare i messaggi nelle code anche per individuare le attività in sospeso o, in alcuni casi, bloccate.

> [!WARNING]
> Sebbene sia utile esaminare la cronologia di esecuzioni in archiviazione tabelle, evitare di creare qualsiasi dipendenza in questa tabella, che può cambiare con l'evoluzione dell'estensione Funzioni permanenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sul monitoraggio in funzioni di Azure](../functions-monitoring.md)
