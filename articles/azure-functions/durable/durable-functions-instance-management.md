---
title: Gestire le istanze in Funzioni permanenti - Azure
description: Informazioni su come gestire le istanze nell'estensione Funzioni permanenti per le Funzioni di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 8dbf7b6f6741998972070234d90e87baca1154a4
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042462"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gestire le istanze in Durable Functions in Azure

Le istanze di orchestrazione di [Funzioni permanenti](durable-functions-overview.md) possono essere avviate e terminate, è possibile eseguire query su esse e inviare a esse eventi di notifica. La gestione delle istanza viene eseguita usando l'[associazione del client di orchestrazione](durable-functions-bindings.md). Questo articolo riporta informazioni dettagliate su ogni operazione di gestione delle istanze.

## <a name="starting-instances"></a>Avvio di istanze

[StartNewAsync] (metodo https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) o metodo `startNew` della classe `DurableOrchestrationClient` (JavaScript)) avvia una nuova istanza della funzione dell'agente di orchestrazione. Le istanze di questa classe possono essere acquisite tramite l'associazione `orchestrationClient`. Internamente, questo metodo accoda un messaggio nella coda di controllo, che poi attiva l'avvio di una funzione con il nome specificato che usa l'associazione del trigger `orchestrationTrigger`.

Questa operazione asincrona viene completata quando il processo di orchestrazione viene pianificato correttamente. Il processo di orchestrazione dovrebbe essere avviato entro 30 secondi. Se l'avvio richiede più tempo, viene generata un'eccezione`TimeoutException`.

> [!WARNING]
> Quando si sviluppa in locale con JavaScript, è necessario impostare la variabile di ambiente `WEBSITE_HOSTNAME` su `localhost:<port>`, ad esempio `localhost:7071` per usare i metodi su `DurableOrchestrationClient`. Per altre informazioni su questo requisito, vedere il [problema di GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

I parametri per [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) sono i seguenti:

* **Name**: nome della funzione dell'agente di orchestrazione da pianificare.
* **Input**: dati serializzabili in JSON che devono essere passati come input alla funzione dell'agente di orchestrazione.
* **InstanceId**: (facoltativo) ID univoco dell'istanza. Se non è specificato, viene generato un ID istanza casuale.

Di seguito è riportato un semplice esempio in C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

I parametri per `startNew` sono i seguenti:

* **Name**: nome della funzione dell'agente di orchestrazione da pianificare.
* **InstanceId**: (facoltativo) ID univoco dell'istanza. Se non è specificato, viene generato un ID istanza casuale.
* **Input**: (facoltativo) dati serializzabili in JSON che devono essere passati come input alla funzione dell'agente di orchestrazione.

Ecco un semplice esempio di JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!NOTE]
> Usare un identificatore casuale per l'ID istanza. Questo contribuisce a garantire una distribuzione del carico equa quando si ridimensionano le funzioni dell'agente di orchestrazione tra più VM. Il momento opportuno per usare ID istanza non casuali è quando l'ID deve provenire da un'origine esterna o quando si implementa il modello [agente di orchestrazione singleton](durable-functions-singletons.md).

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile avviare un'istanza direttamente tramite il comando [Strumenti di base di Funzioni di Azure](../functions-run-local.md) `durable start-new`. È necessario specificare i seguenti parametri:

* **`function-name` (obbligatorio)**: nome della funzione da avviare
* **`input` (facoltativo)**: input alla funzione, in linea o tramite un file JSON. Per i file, aggiungere al percorso del file il prefisso `@`, ad esempio `@path/to/file.json`.
* **`id` (facoltativo)**: ID dell'istanza di orchestrazione. Se non specificato, viene generato un GUID casuale.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

> [!NOTE]
> I comandi degli strumenti di base presuppongono che vengano eseguiti dalla directory radice di un'app per le funzioni. Se `connection-string-setting` e `task-hub-name` vengono fornite in modo esplicito, i comandi possono essere eseguiti da qualsiasi directory. Anche se questi comandi possono essere eseguiti senza un host di app per le funzione in esecuzione, alcuni effetti potrebbero non essere osservati, a meno che l'host non sia in esecuzione. Ad esempio, il comando `start-new` accoderà un messaggio di avvio nell'hub attività di destinazione, ma l'orchestrazione non verrà effettivamente eseguita a meno che non sia presente un processo host di app per le funzioni in esecuzione in grado di elaborare il messaggio.

Il comando seguente avvia la funzione denominata HelloWorld e passa il contenuto del file "counter-data.json":

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>Esecuzione di query sulle istanze

Il metodo [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) o il metodo `getStatus` della classe `DurableOrchestrationClient` (JavaScript) esegue una query sullo stato dell'istanza di orchestrazione.

Accetta `instanceId` (obbligatorio), `showHistory` (facoltativo), `showHistoryOutput` (facoltativo) e `showInput` (facoltativo, solo .NET) come parametri.

* **`showHistory`**: se impostato su `true`, la risposta contiene la cronologia dell'esecuzione.
* **`showHistoryOutput`**: se impostato su `true`, la cronologia dell'esecuzione contiene gli output delle attività.
* **`showInput`**: se impostato su `false`, la risposta non contiene l'input della funzione. Il valore predefinito è `true`. (Solo .NET)

Il metodo restituisce un oggetto JSON con le proprietà seguenti:

* **Name**: nome della funzione dell'agente di orchestrazione.
* **InstanceId**: ID istanza dell'orchestrazione (deve essere lo stesso dell'input `instanceId`).
* **CreatedTime**: ora in cui la funzione dell'agente di orchestrazione ha avviato l'esecuzione.
* **LastUpdatedTime**: ora in cui l'orchestrazione ha eseguito l'ultimo checkpoint.
* **Input**: input della funzione come valore JSON. Questo campo non viene popolato se `showInput` è false.
* **CustomStatus**: stato personalizzato dell'orchestrazione in formato JSON.
* **Output**: output della funzione come valore JSON (se la funzione è stata completata). Se l'esecuzione della funzione dell'agente di orchestrazione non è riuscita, questa proprietà include i dettagli dell'errore. Se la funzione dell'agente di orchestrazione è stata terminata, questa proprietà include il motivo dell'interruzione (se presente).
* **RuntimeStatus**: Uno dei valori seguenti:
  * **Pending**: l'istanza è stata pianificata ma non è ancora avviata l'esecuzione.
  * **Running**: l'istanza ha iniziato l'esecuzione.
  * **Completed**: l'istanza è stata completata normalmente.
  * **ContinuedAsNew**: l'istanza si è riavviata con una nuova cronologia. Si tratta di uno stato temporaneo.
  * **Failed**: l'esecuzione dell'istanza non è riuscita e ha generato un errore.
  * **Terminated**: l'istanza è stata terminata in modo anomalo.
* **History**: cronologia di esecuzione dell'orchestrazione. Questo campo viene popolato solo se `showHistory` è impostato su `true`.

Questo metodo restituisce `null` se l'istanza non esiste o non è ancora iniziata la sua esecuzione.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile ottenere lo stato di un'istanza di orchestrazione direttamente tramite il comando [Strumenti di base di Funzioni di Azure](../functions-run-local.md) `durable get-runtime-status`. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione.
* **`show-input` (facoltativo)**: se impostato su `true`, la risposta contiene l'input della funzione. Il valore predefinito è `false`.
* **`show-output` (facoltativo)**: se impostato su `true`, la risposta contiene l'output della funzione. Il valore predefinito è `false`.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Il comando seguente recupera lo stato (inclusi input e output) di un'istanza con un ID istanza di orchestrazione di 0ab8c55a66644d68a3a8b220b12d209c. Si presuppone che il comando `func` venga eseguito dalla directory radice dell'app per le funzioni:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Il comando `durable get-history` può essere utilizzato per recuperare la cronologia di un'istanza di orchestrazione. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in host.json tramite durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>Esecuzione di query su tutte le istanze

È possibile usare il metodo `GetStatusAsync` (.NET) o il metodo `getStatusAll` (JavaScript) per eseguire query sugli stati delle istanze di orchestrazione. In .NET è possibile passare un oggetto `CancellationToken` nel caso lo si volesse cancellare. Il metodo restituisce oggetti con le stesse proprietà del metodo `GetStatusAsync` con parametri.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile eseguire query delle istanze direttamente tramite il comando [Strumenti di base di Funzioni di Azure](../functions-run-local.md) `durable get-instances`. È necessario specificare i seguenti parametri:

* **`top` (facoltativo)**: questo comando supporta il paging. Questo parametro corrisponde al numero di istanze recuperate per ogni richiesta. Il valore predefinito è 10.
* **`continuation-token` (facoltativo)**: token per indicare quale pagina o sezione di istanze deve essere recuperata. Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>Esecuzione di query sulle istanze con i filtri

È anche possibile usare il metodo `GetStatusAsync` (.NET) o il metodo `getStatusBy` (JavaScript) per ottenere un elenco di istanze di orchestrazione che corrispondono a un set di filtri predefiniti. Le opzioni di filtro possibili includono l'ora di creazione e lo stato di runtime dell'orchestrazione.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-the-functions-core-tools"></a>Uso degli strumenti di base di Funzioni di Azure

Il comando `durable get-instances` può essere utilizzato anche con i filtri. Oltre ai parametri citati in precedenza `top`, `continuation-token`, `connection-string-setting`, e `task-hub-name`, possono essere utilizzati tre parametri di filtro (`created-after`, `created-before`, e `runtime-status`).

* **`created-after` (facoltativo)**: consente di recuperare le istanze create dopo questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`created-before` (facoltativo)**: consente di recuperare le istanze create prima di questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`runtime-status` (facoltativo)**: consente di recuperare le istanze il cui stato corrisponde ai valori seguenti ("in esecuzione", "completato" e così via). Può fornire più stati (separati da spazi).
* **`top` (facoltativo)**: numero di istanze recuperate per ogni richiesta. Il valore predefinito è 10.
* **`continuation-token` (facoltativo)**: token per indicare quale pagina o sezione di istanze deve essere recuperata. Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Se non viene specificato nessun filtro (`created-after`, `created-before` o `runtime-status`), allora le istanze `top` verranno recuperate senza considerare lo stato o la data di creazione di runtime.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>Terminazione delle istanze

Un'istanza di orchestrazione in esecuzione può essere terminata usando il metodo [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) o il metodo `terminate` della classe `DurableOrchestrationClient` (JavaScript). I due parametri sono un `instanceId` e una stringa `reason` che saranno scritte nei log e nello stato dell'istanza. Un'istanza terminata arresta l'esecuzione non appena raggiunge il successivo punto `await` (.NET) o `yield` (JavaScript) oppure termina immediatamente se è già in un `await` (.NET) o `yield` (JavaScript).

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Al momento, la terminazione delle istanze non viene propagata. Le funzioni di attività e le orchestrazioni secondarie verranno eseguite fino al completamento anche se l'istanza di orchestrazione che le ha chiamate è stata terminata.

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile terminare un'istanza di orchestrazione direttamente tramite il comando [Strumenti di base di Funzioni di Azure](../functions-run-local.md) `durable terminate`. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione da terminare.
* **`reason` (facoltativo)**: Motivo dell'interruzione.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Un'istanza di orchestrazione con l'ID di 0ab8c55a66644d68a3a8b220b12d209c interrompe il comando seguente:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>Invio di eventi alle istanze

È possibile inviare notifiche di eventi alle istanze in esecuzione usando il metodo [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) o il metodo `raiseEvent` della classe `DurableOrchestrationClient` (JavaScript). Le istanze che possono gestire questi eventi sono quelle in attesa di una chiamata a [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) o `waitForExternalEvent` (JavaScript).

I parametri per [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) e `raiseEvent` (JavaScript) sono i seguenti:

* **InstanceId**: ID univoco dell'istanza.
* **EventName**: nome dell'evento da inviare.
* **EventData**: payload serializzabile in JSON da inviare all'istanza.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!WARNING]
> Se non è presente un'istanza di orchestrazione con l'*ID istanza* specificato o se l'istanza non è in attesa del *nome evento* specificato, il messaggio dell'evento viene eliminato. Per altre informazioni su questo comportamento, vedere il [problema GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile generare un evento di un'istanza di orchestrazione direttamente tramite il comando [Strumenti di base](../functions-run-local.md) `durable raise-event`. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione.
* **`event-name` (facoltativo)**: nome dell'evento da generare. Il valore predefinito è `$"Event_{RandomGUID}"`
* **`event-data` (facoltativo)**: dati da inviare all'istanza di orchestrazione. Può essere il percorso di un file JSON o i dati possono essere forniti direttamente nella riga di comando
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Attendere il completamento dell'orchestrazione

La classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) espone un'API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) in .NET che può essere usata per ottenere in modo sincrono l'output effettivo da un'istanza di orchestrazione. In JavaScript la classe `DurableOrchestrationClient` espone un'API `waitForCompletionOrCreateCheckStatusResponse` per lo stesso scopo. I metodi usano un valore predefinito di 10 secondi per `timeout` e 1 secondo per `retryInterval` quando non sono impostati.  

Di seguito è riportato un esempio di funzione trigger HTTP che illustra come usare questa API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

La funzione può essere chiamata con la riga seguente usando un timeout di 2 secondi e un intervallo tra tentativi di 0,5 secondi:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

A seconda del tempo necessario per ottenere la risposta dall'istanza di orchestrazione, si verificano due casi:

* Le istanze di orchestrazione vengono completate entro il timeout definito (in questo caso, 2 secondi) e la risposta è l'output effettivo delle istanze di orchestrazione recapitato in modo sincrono:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Le istanze di orchestrazione non possono essere completate entro il timeout definito (in questo caso, 2 secondi) e la risposta è quella predefinita illustrata in **Rilevamento dell'URL di API HTTP**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Il formato degli URL webhook può variare in base alla versione dell'host di Funzioni di Azure in esecuzione. L'esempio precedente riguarda l'host di Funzioni di Azure 2.

## <a name="retrieving-http-management-webhook-urls"></a>Recupero di URL di webhook di gestione HTTP

I sistemi esterni possono comunicare con Funzioni permanenti tramite gli URL dei webhook che fanno parte della risposta predefinita descritta in [Individuazione degli URL delle API HTTP](durable-functions-http-api.md). È tuttavia possibile accedere agli URL dei webhook anche a livello di codice nel client di orchestrazione o in una funzione di attività tramite il metodo [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) o il metodo `createHttpManagementPayload` della classe `DurableOrchestrationClient` (JavaScript).

Le classi [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) e `createHttpManagementPayload` dispongono di un unico parametro:

* **instanceId**: ID univoco dell'istanza.

I metodi restituiscono un'istanza di [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) o un oggetto (JavaScript) con le seguenti proprietà stringa:

* **Id**: ID istanza dell'orchestrazione (deve essere lo stesso dell'input `InstanceId`).
* **StatusQueryGetUri**: URL di stato dell'istanza di orchestrazione.
* **SendEventPostUri**: URL di generazione evento dell'istanza di orchestrazione.
* **TerminatePostUri**: URL di terminazione dell'istanza di orchestrazione.
* **RewindPostUri**: URL di ripristino dell'istanza di orchestrazione.

Le funzioni di attività possono inviare un'istanza di questi oggetti a sistemi esterni per monitorare o generare eventi relativi a un'orchestrazione:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewinding-instances-preview"></a>Istanze di ripristino (anteprima)

Un'istanza di orchestrazione non riuscita può essere *riportata* a uno stato di integrità precedente usando l'API [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) o `rewindAsync` (JavaScript). A questo scopo, l'orchestrazione viene ripristinata allo stato *In esecuzione* e vengono nuovamente eseguiti l'attività e/o gli errori di esecuzione dell'orchestrazione secondaria che hanno causato l'errore di orchestrazione.

> [!NOTE]
> Questa API non deve sostituire una corretta gestione degli errori e i criteri di ripetizione dei tentativi, ma deve essere usata solo nei casi in cui le istanze di orchestrazione hanno esito negativo per motivi non previsti. Per altre informazioni sulla gestione degli errori e sui criteri di ripetizione dei tentativi, vedere l'argomento [Gestione degli errori](durable-functions-error-handling.md).

Un esempio di caso d'uso di *ripristino* è un flusso di lavoro che prevede una serie di [approvazioni umane](durable-functions-concepts.md#human). Si supponga che esista una serie di funzioni di attività in cui un utente viene informato che è necessaria la sua approvazione e che si attenda la risposta in tempo reale. Dopo che tutte le attività di approvazione hanno ricevuto le risposte o sono scadute, un'altra attività ha esito negativo a causa di un errore di configurazione dell'applicazione (come una stringa di connessione del database non valida). Ne consegue un errore di orchestrazione nel flusso di lavoro. Con l'API `RewindAsync` (.NET) o `rewindAsync` (JavaScript), un amministratore dell'applicazione può correggere l'errore di configurazione e *riportare* l'orchestrazione non riuscita allo stato immediatamente precedente all'errore. Nessuna delle operazioni che prevedono l'interazione umana deve essere nuovamente approvata e l'orchestrazione può essere ora completata.

> [!NOTE]
> La funzionalità di *ripristino* non supporta istanze di ripristino dell'orchestrazione in cui siano usati timer permanenti.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile riavvolgere un'istanza di orchestrazione direttamente tramite il comando [Strumenti di base](../functions-run-local.md) `durable rewind`. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione.
* **`reason` (facoltativo)**: motivo del ripristino dell'istanza di orchestrazione.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Ripulire la cronologia di istanza

> [!NOTE]
> L'API `PurgeInstanceHistoryAsync` è attualmente disponibile solo per C#. Verrà aggiunta a JavaScript in una versione futura.

La cronologia di orchestrazione può essere ripulita utilizzando [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_). La funzionalità rimuoverà tutti i dati associati a un'orchestrazione, le righe della tabella di Azure e BLOB di messaggi di grandi dimensioni, se presenti. Il metodo presenta due overload. Il primo ripulisce la cronologia dall'ID dell'istanza di orchestrazione:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Nel secondo esempio viene illustrata una funzione attivata tramite timer che ripulisce la cronologia per tutte le istanze di orchestrazione completate dopo l'intervallo di tempo specificato. In questo caso, rimuoverà i dati per tutte le istanze completate 30 o più giorni fa. È programmata per essere eseguita una volta al giorno alle 12 del mattino:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> L'overload *PurgeInstanceHistory* che accetta il periodo di tempo come parametro elaborerà solo le istanze di orchestrazione in uno degli stati del runtime: Completato, Terminato o Non riuscito.

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È possibile ripulire una cronologia dell'istanza di orchestrazione utilizzando il comando [Strumenti di base](../functions-run-local.md) `durable purge-history`. Simile al secondo esempio C# precedente, ripulisce la cronologia per tutte le istanze di orchestrazione create durante un intervallo di tempo specificato. Le istanze eliminate possono essere ulteriormente filtrate in base allo stato di runtime. Il comando ha diversi parametri:

* **`created-after` (facoltativo)**: consente di ripulire la cronologia delle istanze create dopo questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`created-before` (facoltativo)**: consente di ripulire la cronologia delle istanze create prima di questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`runtime-status` (facoltativo)**: consente di ripulire la cronologia delle istanze il cui stato corrisponde ai valori seguenti ("in esecuzione, "completato" e così via). Può fornire più stati (separati da spazi).
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Il comando seguente eliminerebbe la cronologia di tutte le istanze non riuscite create prima del 14 novembre 2018 alle 19:35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>Eliminazione di un hub attività

Usando il comando [Strumenti di base](../functions-run-local.md) `durable delete-task-hub`, è possibile eliminare tutti gli artefatti di archiviazione associati a un hub attività particolare. Sono inclusi i BLOB, le code e le tabelle di archiviazione di Azure. Il comando presenta due parametri:

* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: nome dell'hub attività permanenti da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Il comando seguente eliminerebbe tutti i dati associati all'hub attività "UserTest" della risorsa di archiviazione di Azure.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare le API HTTP per la gestione delle istanze](durable-functions-http-api.md)
