---
title: Gestire le istanze in Funzioni permanenti - Azure
description: Informazioni su come gestire le istanze nell'estensione Funzioni permanenti per le Funzioni di Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 07a96fdd6350d8db38a92c23e510afb05f7416fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277752"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gestire le istanze in Durable Functions in Azure

Se si usa l'estensione [Funzioni permanenti](durable-functions-overview.md) per Funzioni di Azure o si vuole iniziare a eseguire questa operazione, assicurarsi di sfruttarla al meglio. È possibile ottimizzare le istanze di orchestrazione di Funzioni permanenti per ulteriori informazioni su come gestirle. Questo articolo riporta informazioni dettagliate su ogni operazione di gestione delle istanze.

È possibile avviare e terminare le istanze, ad esempio, ed è possibile eseguire query sulle istanze, inclusa la possibilità di eseguire query su tutte le istanze e le istanze di query con filtri. Inoltre, è possibile inviare eventi alle istanze, attendere il completamento dell'orchestrazione e recuperare gli URL webhook di gestione HTTP. Questo articolo illustra anche altre operazioni di gestione, tra cui il riavvolgimento delle istanze, l'eliminazione della cronologia delle istanze e l'eliminazione di un hub attività.

In Funzioni durevoli sono disponibili opzioni per l'implementazione di ognuna di queste operazioni di gestione. In questo articolo vengono forniti esempi che usano gli strumenti di base di Funzioni di [Azure](../functions-run-local.md) sia per .NET (C) che per JavaScript.

## <a name="start-instances"></a>Avviare le istanze

È importante poter avviare un'istanza di orchestrazione. Questa operazione viene in genere eseguita quando si usa un'associazione di funzioni durevoli nel trigger di un'altra funzione.

Il `StartNewAsync` metodo (.NET) o `startNew` (JavaScript) nell'associazione client dell'orchestrazione avvia una nuova istanza. [orchestration client binding](durable-functions-bindings.md#orchestration-client) Internamente, questo metodo accoda un messaggio nella coda di controllo, che quindi attiva l'inizio di una funzione con il nome specificato che utilizza l'associazione del [trigger di orchestrazione.](durable-functions-bindings.md#orchestration-trigger)

Questa operazione asincrona viene completata quando il processo di orchestrazione viene pianificato correttamente.

I parametri per l'avvio di una nuova istanza di orchestrazione sono i seguenti:The parameters for starting a new orchestration instance are as follows:

* **Nome**: il nome della funzione dell'agente di orchestrazione da pianificare.
* **Input**: dati serializzabili in JSON che devono essere passati come input alla funzione dell'agente di orchestrazione.
* **InstanceId**: (facoltativo) l'ID univoco dell'istanza. Se non si specifica questo parametro, il metodo utilizza un ID casuale.

> [!TIP]
> Usare un identificatore casuale per l'ID istanza. Gli ID istanza casuali contribuiscono a garantire una distribuzione del carico uguale quando si ridimensionano le funzioni dell'agente di orchestrazione tra più macchine virtuali. Il momento corretto per utilizzare ID di istanza non casuali è quando l'ID deve provenire da un'origine esterna o quando si implementa il modello di [orchestrazione singleton.](durable-functions-singletons.md)

Il codice seguente è una funzione di esempio che avvia una nuova istanza di orchestrazione:The following code is an example function that starts a new orchestration instance:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

<a name="javascript-function-json"></a>Se non diversamente specificato, gli esempi in questa pagina utilizzano il trigger HTTP con il seguente function.json.

**function.json (funzione.json)**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Questo esempio è destinato a Durable Functions versione 2.x.This example targets Durable Functions version 2.x. Nella versione 1.x, `orchestrationClient` `durableClient`utilizzare instead di .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile avviare direttamente un'istanza usando il comando Strumenti di base di Funzioni di [Azure.You](../functions-run-local.md) `durable start-new` can also start an instance directly by using the Azure Functions Core Tools command. È necessario specificare i seguenti parametri:

* (obbligatorio) : Nome della funzione da avviare. ** `function-name` **
* (facoltativo): input per la funzione, inline o tramite un file JSON. ** `input` ** Per i file, aggiungere un prefisso `@`al `@path/to/file.json`percorso del file con , ad esempio .
* (facoltativo): ID dell'istanza dell'orchestrazione. ** `id` ** Se non si specifica questo parametro, il comando utilizza un GUID casuale.
* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è AzureWebJobsStorage.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Il valore predefinito è DurableFunctionsHub. È anche possibile impostarlo in host.json usando durableTask:HubName.You can also set this in [host.json](durable-functions-bindings.md#host-json) by using durableTask:HubName.

> [!NOTE]
> I comandi strumenti di base presuppongono che vengano eseguiti dalla directory radice di un'app per le funzioni. Se si forniscono in modo esplicito i `connection-string-setting` parametri e `task-hub-name` , è possibile eseguire i comandi da qualsiasi directory. Sebbene sia possibile eseguire questi comandi senza un host dell'app per le funzioni in esecuzione, è possibile che non sia possibile osservare alcuni effetti a meno che l'host non sia in esecuzione. Ad esempio, `start-new` il comando accoda un messaggio di avvio nell'hub attività di destinazione, ma l'orchestrazione non viene effettivamente eseguita a meno che non sia in esecuzione un processo host dell'app per le funzioni in grado di elaborare il messaggio.

Il comando seguente avvia la funzione denominata HelloWorld `counter-data.json` e vi passa il contenuto del file:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Istanze di query

Come parte dello sforzo per gestire le orchestrazioni, è molto probabile che sia necessario raccogliere informazioni sullo stato di un'istanza di orchestrazione (ad esempio, se è stata completata normalmente o non è riuscita).

Il `GetStatusAsync` metodo (.NET) o `getStatus` (JavaScript) nell'associazione client di [orchestrazione](durable-functions-bindings.md#orchestration-client) esegue una query sullo stato di un'istanza di orchestrazione.

Accetta `instanceId` (obbligatorio), `showHistory` (facoltativo), `showHistoryOutput` (facoltativo) e `showInput` (facoltativo) come parametri.

* **`showHistory`**: se `true`impostato su , la risposta contiene la cronologia di esecuzione.
* **`showHistoryOutput`**: se `true`impostato su , la cronologia di esecuzione contiene gli output dell'attività.
* **`showInput`**: se `false`impostato su , la risposta non conterrà l'input della funzione. Il valore predefinito è `true`.

Il metodo restituisce un oggetto con le proprietà seguenti:

* **Name**: il nome della funzione dell'agente di orchestrazione.
* **InstanceId**: l'ID istanza dell'orchestrazione (deve essere lo stesso dell'input `instanceId`).
* **CreatedTime**: l'ora in cui la funzione dell'agente di orchestrazione ha iniziato l'esecuzione.
* **LastUpdatedTime**: l'ora in cui l'orchestrazione ha eseguito l'ultimo checkpoint.
* **Input**: l'input della funzione come valore JSON. Questo campo non viene `showInput` popolato se è false.
* **CustomStatus**: stato personalizzato dell'orchestrazione in formato JSON.
* **Output**: l'output della funzione come valore JSON (se la funzione è stata completata). Se la funzione dell'agente di orchestrazione non è riuscita, questa proprietà include i dettagli dell'errore. Se la funzione dell'agente di orchestrazione è stata terminata, questa proprietà include il motivo della terminazione (se presente).
* **RuntimeStatus**: uno dei valori seguenti:
  * **In sospeso**: l'istanza è stata pianificata ma non è ancora iniziata l'esecuzione.
  * **Running**: l'istanza ha iniziato l'esecuzione.
  * **Completed**: l'istanza è stata completata normalmente.
  * **ContinuedAsNew**: l'istanza si è riavviata con una nuova cronologia. Questo stato è uno stato temporaneo.
  * **Failed**: l'esecuzione dell'istanza non è riuscita e ha generato un errore.
  * **Terminata**: l'istanza è stata terminata in modo anomalo.
* **History**: cronologia di esecuzione dell'orchestrazione. Questo campo viene popolato solo se `showHistory` è impostato su `true`.

Questo metodo `null` restituisce (.NET) o `undefined` (JavaScript) se l'istanza non esiste.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Vedere [Istanze di avvio](#javascript-function-json) per la configurazione function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile ottenere direttamente lo stato di un'istanza di orchestrazione usando il comando Strumenti di base di Funzioni di Azure.It's also possible to get the status of an orchestration instance directly, by using the [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` command. È necessario specificare i seguenti parametri:

* (obbligatorio): ID dell'istanza dell'orchestrazione. ** `id` **
* (facoltativo): se `true`impostato su , la risposta contiene l'input della funzione. ** `show-input` ** Il valore predefinito è `false`.
* (facoltativo): se `true`impostato su , la risposta contiene l'output della funzione. ** `show-output` ** Il valore predefinito è `false`.
* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è `AzureWebJobsStorage`.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), utilizzando durableTask:HubName.

Il comando seguente recupera lo stato (inclusi input e output) di un'istanza con un ID istanza orchestrazione di 0ab8c55a66644d68a3a8b220b12d209c. Si presuppone che si `func` sta eseguendo il comando dalla directory radice dell'app per le funzioni:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

È possibile `durable get-history` utilizzare il comando per recuperare la cronologia di un'istanza dell'orchestrazione. È necessario specificare i seguenti parametri:

* (obbligatorio): ID dell'istanza dell'orchestrazione. ** `id` **
* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è `AzureWebJobsStorage`.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in host.json, usando durableTask:HubName.It can also be set in host.json, by using durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Interroga tutte le istanze

Anziché eseguire una query su un'istanza nell'orchestrazione alla volta, potrebbe essere più efficiente eseguire una query tutti contemporaneamente.

È possibile usare il metodo `GetStatusAsync` (.NET) o il metodo `getStatusAll` (JavaScript) per eseguire query sugli stati delle istanze di orchestrazione. In .NET è possibile `CancellationToken` passare un oggetto nel caso in cui si desideri annullarlo. Il metodo restituisce oggetti con le stesse proprietà del metodo `GetStatusAsync` con parametri.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Vedere [Istanze di avvio](#javascript-function-json) per la configurazione function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile eseguire query direttamente sulle istanze usando il comando Strumenti di base di Funzioni di Azure.It's also possible to query instances directly, by using the [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` command. È necessario specificare i seguenti parametri:

* (facoltativo): questo comando supporta il paging. ** `top` ** Questo parametro corrisponde al numero di istanze recuperate per ogni richiesta. Il valore predefinito è 10.
* (facoltativo): un token per indicare la pagina o la sezione delle istanze da recuperare. ** `continuation-token` ** Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è `AzureWebJobsStorage`.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), utilizzando durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Istanze di query con filtri

Cosa succede se non sono realmente necessarie tutte le informazioni che una query di istanza standard può fornire? Ad esempio, cosa succede se si sta solo cercando l'ora di creazione dell'orchestrazione o lo stato di runtime dell'orchestrazione? È possibile restringere la query applicando filtri.

Utilizzare `GetStatusAsync` il metodo (.NET) o `getStatusBy` (JavaScript) per ottenere un elenco di istanze di orchestrazione che corrispondono a un set di filtri predefiniti.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

> [!NOTE]
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Vedere [Istanze di avvio](#javascript-function-json) per la configurazione function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Negli strumenti di base di Funzioni `durable get-instances` di Azure è anche possibile usare il comando con i filtri. Oltre ai suddetti `top` `continuation-token`parametri `connection-string-setting`, `task-hub-name` , , e ,`created-after`è `created-before`possibile `runtime-status`utilizzare tre parametri di filtro ( , , e ).

* (facoltativo): recupera le istanze create dopo questa data/ora (UTC). ** `created-after` ** Datetime in formato ISO 8601 accettati.
* (facoltativo): consente di recuperare le istanze create prima di questa data/ora (UTC). ** `created-before` ** Datetime in formato ISO 8601 accettati.
* (facoltativo): recupera le istanze con un determinato stato (ad esempio, in esecuzione o completato). ** `runtime-status` ** Può fornire più stati (separati da spazi).
* (facoltativo): numero di istanze recuperate per richiesta. ** `top` ** Il valore predefinito è 10.
* (facoltativo): un token per indicare la pagina o la sezione delle istanze da recuperare. ** `continuation-token` ** Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è `AzureWebJobsStorage`.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), utilizzando durableTask:HubName.

Se non si specifica alcun`created-after` `created-before`filtro `runtime-status`( , , `top` o ), il comando recupera semplicemente le istanze, senza considerare lo stato di runtime o l'ora di creazione.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Terminare le istanze

Se si dispone di un'istanza di orchestrazione che richiede troppo tempo per l'esecuzione o è sufficiente interromperla prima del completamento per qualsiasi motivo, è possibile scegliere di terminarla.

È possibile `TerminateAsync` utilizzare il metodo `terminate` (.NET) o (JavaScript) dell'associazione client di [orchestrazione](durable-functions-bindings.md#orchestration-client) per terminare le istanze. I due parametri `instanceId` sono `reason` una e una stringa, che vengono scritti nei log e nello stato dell'istanza.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Vedere [Istanze di avvio](#javascript-function-json) per la configurazione function.json.

---

Un'istanza terminata alla `Terminated` fine passerà allo stato. Tuttavia, questa transizione non avverrà immediatamente. Piuttosto, l'operazione di terminazione verrà accodata nell'hub attività insieme ad altre operazioni per tale istanza. È possibile usare le API di query dell'istanza per `Terminated` sapere quando un'istanza terminata ha effettivamente raggiunto lo stato. [instance query](#query-instances)

> [!NOTE]
> La terminazione dell'istanza non viene attualmente propagata. Le funzioni di attività e le sottoorchestrazioni vengono eseguite fino al completamento, indipendentemente dal fatto che l'istanza dell'orchestrazione che le chiamasse sia stata terminata.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile terminare direttamente un'istanza di orchestrazione usando il comando Strumenti di base di Funzioni di [Azure.You](../functions-run-local.md) `durable terminate` can also terminate an orchestration instance directly, by using the Azure Functions Core Tools command. È necessario specificare i seguenti parametri:

* (obbligatorio): ID dell'istanza dell'orchestrazione da terminare. ** `id` **
* (opzionale) : Motivo della chiusura. ** `reason` **
* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è `AzureWebJobsStorage`.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), utilizzando durableTask:HubName.

Il comando seguente termina un'istanza dell'orchestrazione con ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Inviare eventi alle istanzeSend events to instances

In alcuni scenari, è importante che le funzioni dell'agente di orchestrazione siano in grado di attendere e ascoltare gli eventi esterni. Sono incluse [le funzioni](durable-functions-overview.md#monitoring) di monitoraggio e le funzioni in attesa [dell'interazione umana.](durable-functions-overview.md#human)

Inviare notifiche di eventi `RaiseEventAsync` alle istanze in `raiseEvent` esecuzione utilizzando il metodo (.NET) o il metodo (JavaScript) dell'associazione client di [orchestrazione.](durable-functions-bindings.md#orchestration-client) Le istanze in grado di gestire questi `WaitForExternalEvent` eventi sono quelle in attesa `waitForExternalEvent` di una chiamata a (.NET) o che cedono a una chiamata (JavaScript).

I parametri `RaiseEventAsync` per (.NET) e `raiseEvent` (JavaScript) sono i seguenti:

* **InstanceId**: l'ID univoco dell'istanza.
* **EventName**: il nome dell'evento da inviare.
* **EventData**: un payload serializzabile in JSON da inviare all'istanza.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Vedere [Istanze di avvio](#javascript-function-json) per la configurazione function.json.

---

> [!NOTE]
> Se non esiste alcuna istanza di orchestrazione con l'ID istanza specificato, il messaggio di evento viene rimosso. Se esiste un'istanza ma non è ancora in attesa dell'evento, l'evento verrà archiviato nello stato dell'istanza fino a quando non sarà pronto per essere ricevuto ed elaborato.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile generare un evento direttamente a un'istanza di orchestrazione usando il comando Strumenti di base di Funzioni di [Azure.You](../functions-run-local.md) `durable raise-event` can also raise an event to an orchestration instance directly, by using the Azure Functions Core Tools command. È necessario specificare i seguenti parametri:

* (obbligatorio): ID dell'istanza dell'orchestrazione. ** `id` **
* **`event-name`**: nome dell'evento da generare.
* (facoltativo): dati da inviare all'istanza dell'orchestrazione. ** `event-data` ** Può trattarsi del percorso di un file JSON oppure è possibile fornire i dati direttamente nella riga di comando.
* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è `AzureWebJobsStorage`.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), utilizzando durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Attendere il completamento dell'orchestrazione

Nelle orchestrazioni a esecuzione prolungata, è possibile attendere e ottenere i risultati di un'orchestrazione. In questi casi, è anche utile essere in grado di definire un periodo di timeout nell'orchestrazione. Se il timeout viene superato, deve essere restituito lo stato dell'orchestrazione anziché i risultati.

Il `WaitForCompletionOrCreateCheckStatusResponseAsync` metodo (.NET) o `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) può essere utilizzato per ottenere l'output effettivo da un'istanza di orchestrazione in modo sincrono. Per impostazione predefinita, questi metodi utilizzano `timeout`un valore `retryInterval`predefinito di 10 secondi per e 1 secondo per .  

Di seguito è riportato un esempio di funzione trigger HTTP che illustra come usare questa API:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Vedere [Istanze di avvio](#javascript-function-json) per la configurazione function.json.

---

Chiamare la funzione con la riga seguente. Utilizzare 2 secondi per il timeout e 0,5 secondi per l'intervallo tra tentativi:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

A seconda del tempo necessario per ottenere la risposta dall'istanza di orchestrazione, si verificano due casi:

* Le istanze dell'orchestrazione vengono completate entro il timeout definito (in questo caso 2 secondi) e la risposta è l'output effettivo dell'istanza dell'orchestrazione, recapitato in modo sincrono:The orchestration instances complete within the defined timeout (in this case 2 seconds), and the response is the actual orchestration instance output, delivered synchronously:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Le istanze dell'orchestrazione non possono essere completate entro il timeout definito e la risposta è quella predefinita descritta in [Individuazione URL API HTTP:](durable-functions-http-api.md)

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Il formato degli URL webhook potrebbe variare a seconda della versione dell'host Funzioni di Azure in esecuzione. L'esempio precedente è per l'host di Funzioni di Azure 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperare gli URL Webhook di gestione HTTP

È possibile utilizzare un sistema esterno per monitorare o generare eventi a un'orchestrazione. I sistemi esterni possono comunicare con Funzioni permanenti tramite gli URL webhook che fanno parte della risposta predefinita descritta in [Individuazione URL API HTTP](durable-functions-http-features.md#http-api-url-discovery). È possibile accedere agli URL webhook in alternativa a livello di codice utilizzando l'associazione client di [orchestrazione.](durable-functions-bindings.md#orchestration-client) I `CreateHttpManagementPayload` metodi (.NET) o `createHttpManagementPayload` (JavaScript) possono essere utilizzati per ottenere un oggetto serializzabile che contiene questi URL webhook.

I `CreateHttpManagementPayload` metodi (.NET) e `createHttpManagementPayload` (JavaScript) hanno un parametro:

* **instanceId**: L'ID univoco dell'istanza.

I metodi restituiscono un oggetto con le seguenti proprietà stringa:

* **Id**: ID istanza dell'orchestrazione (deve essere lo stesso dell'input `InstanceId`).
* **StatusQueryGetUri**: URL di stato dell'istanza di orchestrazione.
* **SendEventPostUri**: URL di generazione di eventi dell'istanza di orchestrazione.
* **TerminatePostUri**: URL di terminazione dell'istanza di orchestrazione.
* **PurgeHistoryDeleteUri**: URL di "cronologia eliminazione" dell'istanza dell'orchestrazione.

Le funzioni possono inviare istanze di questi oggetti a sistemi esterni per monitorare o generare eventi sulle orchestrazioni corrispondenti, come illustrato negli esempi seguenti:Functions can send instances of these objects to external systems to monitor or raise events on the corresponding orchestrations, as shown in the following examples:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
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

> [!NOTE]
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è necessario utilizzare `DurableActivityContext` invece di `IDurableActivityContext`, è necessario utilizzare l'attributo `OrchestrationClient` anziché l'attributo `DurableClient` ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Vedere [Istanze di avvio](#javascript-function-json) per la configurazione function.json.

---

## <a name="rewind-instances-preview"></a>Riavvolgere le istanze (anteprima)Rewind instances (preview)

Se si verifica un errore di orchestrazione per un motivo imprevisto, è possibile *riavvolgere* l'istanza a uno stato precedentemente integro usando un'API compilata per tale scopo.

> [!NOTE]
> Questa API non deve sostituire una corretta gestione degli errori e i criteri di ripetizione dei tentativi, ma deve essere usata solo nei casi in cui le istanze di orchestrazione hanno esito negativo per motivi non previsti. Per altre informazioni sulla gestione degli errori e sui criteri di ripetizione dei tentativi, vedere l'articolo [Gestione degli errori.](durable-functions-error-handling.md)

Utilizzare `RewindAsync` il metodo (.NET) o `rewind` (JavaScript) dell'associazione client dell'orchestrazione per riportare l'orchestrazione nello stato In *esecuzione.* [orchestration client binding](durable-functions-bindings.md#orchestration-client) Questo metodo eseguirà anche gli errori di esecuzione dell'attività o della sottoorchestrazione che hanno causato l'errore dell'orchestrazione.

Si supponga, ad esempio, di disporre di un flusso di lavoro che prevede una serie di [approvazioni umane.](durable-functions-overview.md#human) Si supponga che esista una serie di funzioni di attività che notificano a qualcuno che è necessaria l'approvazione e attendere la risposta in tempo reale. Dopo che tutte le attività di approvazione hanno ricevuto risposte o scaduta, si supponga che un'altra attività non riesca a causa di una configurazione errata dell'applicazione, ad esempio una stringa di connessione al database non valida. Ne consegue un errore di orchestrazione nel flusso di lavoro. Con `RewindAsync` l'API (.NET) o `rewind` (JavaScript), un amministratore dell'applicazione può correggere l'errore di configurazione e riavvolgere l'orchestrazione non riuscita allo stato immediatamente prima dell'errore. Nessuno dei passaggi di interazione umana deve essere riapprovato e l'orchestrazione può ora essere completata correttamente.

> [!NOTE]
> La funzionalità di *riavvolgimento* non supporta il riavvolgimento di istanze di orchestrazione che utilizzano timer durevoli.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Vedere [Istanze di avvio](#javascript-function-json) per la configurazione function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile riavvolgere un'istanza dell'orchestrazione direttamente usando il comando Strumenti di base di Funzioni di [Azure.You](../functions-run-local.md) `durable rewind` can also rewind an orchestration instance directly by using the Azure Functions Core Tools command. È necessario specificare i seguenti parametri:

* (obbligatorio): ID dell'istanza dell'orchestrazione. ** `id` **
* (facoltativo): motivo per il riavvolgimento dell'istanza dell'orchestrazione. ** `reason` **
* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è `AzureWebJobsStorage`.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Per impostazione predefinita, viene utilizzato il nome dell'hub attività nel file [host.json.](durable-functions-bindings.md#host-json)

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Elimina cronologia istanze

Per rimuovere tutti i dati associati a un'orchestrazione, è possibile eliminare la cronologia dell'istanza. Ad esempio, è possibile eliminare tutte le righe della tabella di Azure e i BLOB di messaggi di grandi dimensioni associati a un'istanza completata. A tale scopo, `PurgeInstanceHistoryAsync` utilizzare il `purgeInstanceHistory` metodo (.NET) o (JavaScript) dell'associazione client di [orchestrazione.](durable-functions-bindings.md#orchestration-client)

Questo metodo dispone di due overload. Il primo overload elimina la cronologia in base all'ID dell'istanza dell'orchestrazione:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Vedere [Istanze di avvio](#javascript-function-json) per la configurazione function.json.

---

L'esempio seguente mostra una funzione attivata dal timer che elimina la cronologia per tutte le istanze di orchestrazione completate dopo l'intervallo di tempo specificato. In questo caso, rimuove i dati per tutte le istanze completate 30 o più giorni fa. È pianificato per l'esecuzione una volta al giorno, alle 12:00:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
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
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Il `purgeInstanceHistoryBy` metodo può essere utilizzato per eliminare in modo condizionale la cronologia delle istanze per più istanze.

**function.json (funzione.json)**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Questo esempio è destinato a Durable Functions versione 2.x.This example targets Durable Functions version 2.x. Nella versione 1.x, `orchestrationClient` `durableClient`utilizzare instead di .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> Affinché l'operazione di eliminazione della cronologia abbia esito positivo, lo stato di runtime dell'istanza di destinazione deve essere **Completed**, **Terminated**o **Failed**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È possibile eliminare la cronologia di un'istanza di orchestrazione usando il comando Strumenti di base di Funzioni di [Azure.You](../functions-run-local.md) `durable purge-history` can purge an orchestration instance's history by using the Azure Functions Core Tools command. Analogamente al secondo esempio in C, nella sezione precedente, viene eliminata la cronologia per tutte le istanze di orchestrazione create durante un intervallo di tempo specificato. È possibile filtrare ulteriormente le istanze eliminate in base allo stato di runtime. Il comando ha diversi parametri:

* (facoltativo): consente di eliminare la cronologia delle istanze create dopo questa data/ora (UTC). ** `created-after` ** Datetime in formato ISO 8601 accettati.
* (facoltativo): consente di eliminare la cronologia delle istanze create prima di questa data/ora (UTC). ** `created-before` ** Datetime in formato ISO 8601 accettati.
* (facoltativo): consente di eliminare la cronologia delle istanze con un determinato stato (ad esempio, in esecuzione o completata). ** `runtime-status` ** Può fornire più stati (separati da spazi).
* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è `AzureWebJobsStorage`.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Per impostazione predefinita, viene utilizzato il nome dell'hub attività nel file [host.json.](durable-functions-bindings.md#host-json)

Il comando seguente elimina la cronologia di tutte le istanze non riuscite create prima del 14 novembre 2018 alle 19:35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Eliminare un hub attivitàDelete a task hub

Usando il comando Strumenti `durable delete-task-hub` di base di Funzioni di [Azure,](../functions-run-local.md) è possibile eliminare tutti gli elementi di archiviazione associati a un hub attività specifico, incluse le tabelle di archiviazione di Azure, le code e i BLOB. Il comando presenta due parametri:

* (facoltativo): nome dell'impostazione dell'applicazione contenente la stringa di connessione di archiviazione da utilizzare. ** `connection-string-setting` ** Il valore predefinito è `AzureWebJobsStorage`.
* (facoltativo): nome dell'hub attività Funzioni permanenti da utilizzare. ** `task-hub-name` ** Per impostazione predefinita, viene utilizzato il nome dell'hub attività nel file [host.json.](durable-functions-bindings.md#host-json)

Il comando seguente elimina tutti i `UserTest` dati di archiviazione di Azure associati all'hub attività.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API HTTP predefinite per la gestione delle istanzeBuilt-in HTTP API reference for instance management](durable-functions-http-api.md)
