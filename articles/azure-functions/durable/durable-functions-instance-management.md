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
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730770"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gestire le istanze in Durable Functions in Azure

Se si usa la [funzioni permanenti](durable-functions-overview.md) estensione per funzioni di Azure, o si vuole avviare in questo modo, assicurarsi di ottenere il miglior utilizzo esplicitamente. Per altre informazioni su come gestirli, è possibile ottimizzare le istanze di orchestrazione di funzioni permanenti. Questo articolo riporta informazioni dettagliate su ogni operazione di gestione delle istanze.

È possibile iniziare e terminare le istanze, ad esempio, ed è possibile eseguire query di istanze, inclusa la possibilità di eseguire query di tutte le istanze e le istanze di query con filtri. Inoltre, si può inviare eventi alle istanze, attendere il completamento dell'orchestrazione e recuperare gli URL di webhook HTTP management. Questo articolo descrive altre operazioni di gestione, troppo, tra cui riavvolgimento istanze eliminazione cronologia istanze e l'eliminazione di un hub attività.

In funzioni permanenti, sono disponibili opzioni per la modalità implementare ognuna di queste operazioni di gestione. Questo articolo fornisce esempi che usano il [Azure Functions Core Tools](../functions-run-local.md) entrambi per.NET (C#) e JavaScript.

## <a name="start-instances"></a>Avviare le istanze

È importante essere in grado di avviare un'istanza di orchestrazione. Questa operazione viene in genere eseguita quando si utilizza un'associazione di funzioni permanenti nel trigger della funzione.

Il [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metodo sulle [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) o `startNew` sul `DurableOrchestrationClient` (JavaScript) avvia una nuova istanza. Acquisire le istanze di questa classe usando la `orchestrationClient` associazione. Internamente, questo metodo accoda un messaggio nella coda di controllo, che poi attiva l'avvio di una funzione con il nome specificato che usa l'associazione del trigger `orchestrationTrigger`.

Questa operazione asincrona viene completata quando il processo di orchestrazione viene pianificato correttamente. Il processo di orchestrazione dovrebbe essere avviato entro 30 secondi. Se richiede più tempo, si noterà un `TimeoutException`.

> [!WARNING]
> Quando si sviluppa in locale in JavaScript, impostare la variabile di ambiente `WEBSITE_HOSTNAME` al `localhost:<port>` (ad esempio `localhost:7071`) per utilizzare i metodi `DurableOrchestrationClient`. Per altre informazioni su questo requisito, vedere il [problema descritto in GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

I parametri per [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) sono i seguenti:

* **Name**: nome della funzione dell'agente di orchestrazione da pianificare.
* **Input**: dati serializzabili in JSON che devono essere passati come input alla funzione dell'agente di orchestrazione.
* **InstanceId**: (facoltativo) ID univoco dell'istanza. Se non si specifica questo parametro, il metodo Usa un ID casuale.

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
* **InstanceId**: (facoltativo) ID univoco dell'istanza. Se non si specifica questo parametro, il metodo Usa un ID casuale.
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

> [!TIP]
> Usare un identificatore casuale per l'ID istanza. Ciò consente di garantire una distribuzione del carico equa quando si eseguono il ridimensionamento le funzioni di orchestrazione tra più macchine virtuali. Il momento opportuno per usare gli ID istanza non casuali è quando l'ID deve provenire da un'origine esterna o quando si implementa il [agente di orchestrazione singleton](durable-functions-singletons.md) pattern.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile avviare un'istanza direttamente utilizzando il [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` comando. È necessario specificare i seguenti parametri:

* **`function-name` (obbligatorio)**: Nome della funzione da avviare.
* **`input` (facoltativo)**: Input alla funzione inline o tramite un file JSON. Per i file, aggiungere un prefisso per il percorso del file con `@`, ad esempio `@path/to/file.json`.
* **`id` (facoltativo)**: ID dell'istanza di orchestrazione. Se non si specifica questo parametro, il comando Usa un GUID casuale.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è DurableFunctionsHub. È possibile impostarla anche nella [host. JSON](durable-functions-bindings.md#host-json) usando durableTask:HubName.

> [!NOTE]
> Comandi di strumenti di base presuppongono che siano in esecuzione dalla directory radice dell'app per le funzioni. Se si specifica in modo esplicito il `connection-string-setting` e `task-hub-name` parametri, è possibile eseguire i comandi da qualsiasi directory. Sebbene sia possibile eseguire questi comandi senza un host di app di funzione in esecuzione, si noterà che è non è possibile osservare alcuni effetti, a meno che l'host è in esecuzione. Ad esempio, il `start-new` comando Accoda un messaggio di avvio in hub di attività di destinazione, ma l'orchestrazione non viene effettivamente eseguito a meno che non è presente un processo di host di app di funzione in esecuzione che può elaborare il messaggio.

Il comando seguente avvia la funzione denominata HelloWorld e passa il contenuto del file `counter-data.json` ad esso:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Istanze di query

Come parte dei propri sforzi per la gestione delle orchestrazioni, è necessario molto probabilmente raccogliere informazioni sullo stato di un'istanza di orchestrazione (ad esempio, se ha in genere completata o non è riuscita).

Il metodo [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) o il metodo `getStatus` della classe `DurableOrchestrationClient` (JavaScript) esegue una query sullo stato dell'istanza di orchestrazione.

Accetta `instanceId` (obbligatorio), `showHistory` (facoltativo), `showHistoryOutput` (facoltativo) e `showInput` (facoltativo, solo .NET) come parametri.

* **`showHistory`**: Se impostato su `true`, la risposta contiene la cronologia di esecuzione.
* **`showHistoryOutput`**: Se impostato su `true`, la cronologia di esecuzione contiene gli output di attività.
* **`showInput`**: Se impostato su `false`, la risposta non includerà l'input della funzione. Il valore predefinito è `true`. (Solo .NET)

Il metodo restituisce un oggetto JSON con le proprietà seguenti:

* **Name**: nome della funzione dell'agente di orchestrazione.
* **InstanceId**: ID istanza dell'orchestrazione (deve essere lo stesso dell'input `instanceId`).
* **CreatedTime**: ora in cui la funzione dell'agente di orchestrazione ha avviato l'esecuzione.
* **LastUpdatedTime**: ora in cui l'orchestrazione ha eseguito l'ultimo checkpoint.
* **Input**: input della funzione come valore JSON. Questo campo non viene popolato se `showInput` è false.
* **CustomStatus**: stato personalizzato dell'orchestrazione in formato JSON.
* **Output**: output della funzione come valore JSON (se la funzione è stata completata). Se la funzione di orchestrazione non è riuscita, questa proprietà include i dettagli dell'errore. Se la funzione di orchestrazione è terminata, questa proprietà include il motivo della chiusura (se presente).
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

### <a name="javascript-functions-2x-only"></a>JavaScript (solo funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile ottenere lo stato di un'istanza di orchestrazione, direttamente tramite il [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` comando. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione.
* **`show-input` (facoltativo)**: Se impostato su `true`, la risposta contiene l'input della funzione. Il valore predefinito è `false`.
* **`show-output` (facoltativo)**: Se impostato su `true`, la risposta contiene l'output della funzione. Il valore predefinito è `false`.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è `DurableFunctionsHub`. È possibile impostarla anche [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

Il comando seguente recupera lo stato (inclusi input e output) di un'istanza con un ID istanza orchestrazione 0ab8c55a66644d68a3a8b220b12d209c. Si presuppone che sia in esecuzione il `func` comando dalla directory radice dell'app per le funzioni:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

È possibile usare il `durable get-history` comando per recuperare la cronologia di un'istanza di orchestrazione. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è `DurableFunctionsHub`. Si può anche essere impostato nell'host. JSON, con durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Tutte le istanze di query

Invece di query un'istanza dell'orchestrazione alla volta, può risultare più efficiente per tutti gli elementi di query in una sola volta.

È possibile usare il metodo `GetStatusAsync` (.NET) o il metodo `getStatusAll` (JavaScript) per eseguire query sugli stati delle istanze di orchestrazione. In .NET, è possibile passare un `CancellationToken` dell'oggetto nel caso in cui si vuole annullare l'operazione. Il metodo restituisce oggetti con le stesse proprietà del metodo `GetStatusAsync` con parametri.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo funzioni 2.x)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile per le istanze di query direttamente, usando il [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` comando. È necessario specificare i seguenti parametri:

* **`top` (facoltativo)**: questo comando supporta il paging. Questo parametro corrisponde al numero di istanze recuperate per ogni richiesta. Il valore predefinito è 10.
* **`continuation-token` (facoltativo)**: Un token per indicare la pagina o sezione di istanze da recuperare. Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è `DurableFunctionsHub`. È possibile impostarla anche [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Istanze di query con filtri

Cosa accade se non è strettamente necessario tutte le informazioni che può fornire una query di istanze standard? Ad esempio, cosa accade se si sta semplicemente cercando l'ora di creazione di orchestrazioni o lo stato di runtime dell'orchestrazione? È possibile limitare la query applicando filtri.

Usare la `GetStatusAsync` (.NET) o `getStatusBy` (metodo) (JavaScript) per ottenere un elenco di istanze di orchestrazione che corrispondono a un set di filtri predefiniti.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (solo funzioni 2.x)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

In Azure Functions Core Tools, è anche possibile usare il `durable get-instances` comando con i filtri. Oltre a citato in precedenza `top`, `continuation-token`, `connection-string-setting`, e `task-hub-name` parametri, è possibile utilizzare tre parametri di filtro (`created-after`, `created-before`, e `runtime-status`).

* **`created-after` (facoltativo)**: consente di recuperare le istanze create dopo questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`created-before` (facoltativo)**: consente di recuperare le istanze create prima di questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`runtime-status` (facoltativo)**: Recuperare le istanze con uno stato specifico (ad esempio, in esecuzione o completato). Può fornire più stati (separati da spazi).
* **`top` (facoltativo)**: numero di istanze recuperate per ogni richiesta. Il valore predefinito è 10.
* **`continuation-token` (facoltativo)**: Un token per indicare la pagina o sezione di istanze da recuperare. Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è `DurableFunctionsHub`. È possibile impostarla anche [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

Se non si forniscono tutti i filtri (`created-after`, `created-before`, o `runtime-status`), il comando recupera semplicemente `top` istanze, senza considerare lo stato o la creazione di runtime di integrazione.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Terminare le istanze

Se si dispone di un'istanza di orchestrazione che sta richiedendo troppo tempo per l'esecuzione o è sufficiente arrestarla prima che venga completato per qualsiasi motivo, si ha la possibilità di terminare l'operazione.

È possibile usare il [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metodo per il [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classi (.NET), o il `terminate` metodo del `DurableOrchestrationClient` classe (JavaScript). I due parametri sono un' `instanceId` e un `reason` stringa, che vengono scritti nei log e nello stato dell'istanza. Arresta un'istanza terminata, non appena raggiunge il successivo `await` (.NET) o `yield` punto (JavaScript) oppure verrà interrotta immediatamente se è già un `await` o `yield`.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (solo funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Attualmente non propaga terminazione delle istanze. Le orchestrazioni secondarie e le funzioni di attività eseguite fino al completamento, indipendentemente dal fatto se è stata terminata l'istanza di orchestrazione che sono state richiamate.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile terminare un'istanza di orchestrazione a direttamente, usando il [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` comando. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione per terminare.
* **`reason` (facoltativo)**: Motivo della terminazione.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è `DurableFunctionsHub`. È possibile impostarla anche [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

Il comando seguente consente di terminare un'istanza di orchestrazione con l'ID di 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Inviare eventi alle istanze

In alcuni scenari, è importante per le funzioni dell'agente di orchestrazione essere in grado di attendere e restare in ascolto di eventi esterni. Sono inclusi [monitorare funzioni](durable-functions-concepts.md#monitoring) e le funzioni che sono in attesa [interazione umana](durable-functions-concepts.md#human).

Inviare le notifiche degli eventi alle istanze in esecuzione usando il [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metodo il [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classi (.NET) o il `raiseEvent` metodo del `DurableOrchestrationClient` classe ( JavaScript). Le istanze che possono gestire questi eventi sono quelle in attesa di una chiamata a [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) o `waitForExternalEvent` (JavaScript).

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

### <a name="javascript-functions-2x-only"></a>JavaScript (solo funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Se non esiste alcuna istanza di orchestrazione con l'ID istanza specificato oppure se l'istanza non è in attesa sul nome dell'evento specificato, il messaggio di evento viene eliminato. Per altre informazioni su questo comportamento, vedere il [problema GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È inoltre possibile generare un evento a un'istanza di orchestrazione a direttamente, usando il [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` comando. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione.
* **`event-name` (facoltativo)**: nome dell'evento da generare. Il valore predefinito è `$"Event_{RandomGUID}"`.
* **`event-data` (facoltativo)**: dati da inviare all'istanza di orchestrazione. Ciò può essere il percorso di un file JSON, o è possibile fornire i dati direttamente nella riga di comando.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è `DurableFunctionsHub`. È possibile impostarla anche [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Attendere il completamento dell'orchestrazione

In orchestrazioni a esecuzione prolungata, è possibile attendere e ottenere i risultati di un'orchestrazione. In questi casi, è anche utile essere in grado di definire un periodo di timeout nell'orchestrazione. Se il timeout viene superato, lo stato dell'orchestrazione deve essere restituito anziché i risultati.

Il [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe espone un [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API in .NET. È possibile usare questa API per ottenere l'output effettivo da un'istanza di orchestrazione in modo sincrono. In JavaScript la classe `DurableOrchestrationClient` espone un'API `waitForCompletionOrCreateCheckStatusResponse` per lo stesso scopo. Quando non sono impostati, i metodi usano un valore predefinito di 10 secondi `timeout`e di 1 secondo per `retryInterval`.  

Di seguito è riportato un esempio di funzione trigger HTTP che illustra come usare questa API:

```C#
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace VSSample
{
    public static class HttpSyncStart
    {
        private const string Timeout = "timeout";
        private const string RetryInterval = "retryInterval";

        [FunctionName("HttpSyncStart")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/wait")]
            HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClientBase starter,
            string functionName,
            ILogger log)
        {
            // Function input comes from the request content.
            dynamic eventData = await req.Content.ReadAsAsync<object>();
            string instanceId = await starter.StartNewAsync(functionName, eventData);

            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

            TimeSpan timeout = GetTimeSpan(req, Timeout) ?? TimeSpan.FromSeconds(30);
            TimeSpan retryInterval = GetTimeSpan(req, RetryInterval) ?? TimeSpan.FromSeconds(1);
            
            return await starter.WaitForCompletionOrCreateCheckStatusResponseAsync(
                req,
                instanceId,
                timeout,
                retryInterval);
        }

        private static TimeSpan? GetTimeSpan(HttpRequestMessage request, string queryParameterName)
        {
            string queryParameterStringValue = request.RequestUri.ParseQueryString()[queryParameterName];
            if (string.IsNullOrEmpty(queryParameterStringValue))
            {
                return null;
            }

            return TimeSpan.FromSeconds(double.Parse(queryParameterStringValue));
        }
    }
}
```

```Javascript
const df = require("durable-functions");

const timeout = "timeout";
const retryInterval = "retryInterval";

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = await client.startNew(req.params.functionName, undefined, req.body);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    const timeoutInMilliseconds = getTimeInSeconds(req, timeout) || 30000;
    const retryIntervalInMilliseconds = getTimeInSeconds(req, retryInterval) || 1000;

    return client.waitForCompletionOrCreateCheckStatusResponse(
        context.bindingData.req,
        instanceId,
        timeoutInMilliseconds,
        retryIntervalInMilliseconds);
};

function getTimeInSeconds (req, queryParameterName) {
    const queryValue = req.query[queryParameterName];
    return queryValue
        ? queryValue // expected to be in seconds
        * 1000 : undefined;
}
```

Chiamare la funzione con la riga seguente. Utilizzare 2 secondi per il timeout e 0,5 secondi per l'intervallo tra tentativi:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

A seconda del tempo necessario per ottenere la risposta dall'istanza di orchestrazione, si verificano due casi:

* Le istanze di orchestrazione completate entro il timeout definito (in questo caso, 2 secondi) e la risposta è l'output di istanza di orchestrazione effettiva, recapitato in modo sincrono:

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

* Le istanze di orchestrazione non sono completata entro il timeout definito e la risposta è quella predefinita illustrata nel [rilevamento dell'URL di API HTTP](durable-functions-http-api.md):

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
> Il formato dell'URL webhook possono essere diverse, a seconda di quale versione dell'host di funzioni di Azure in esecuzione. L'esempio precedente riguarda l'host di Funzioni di Azure 2.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperare gli URL di webhook HTTP management

Eseguire il monitoraggio o generare eventi a un'orchestrazione, è possibile usare un sistema esterno. I sistemi esterni possono comunicare con funzioni permanenti tramite gli URL di webhook che fanno parte della risposta predefinito descritto nella [rilevamento dell'URL di API HTTP](durable-functions-http-api.md). Tuttavia, gli URL di webhook inoltre sono accessibili a livello di codice nel client di orchestrazione o in una funzione di attività. Eseguire questa operazione usando il [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) metodo del [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classi (.NET), o la `createHttpManagementPayload` metodo del `DurableOrchestrationClient` classe (JavaScript).

Le classi [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) e `createHttpManagementPayload` dispongono di un unico parametro:

* **instanceId**: ID univoco dell'istanza.

I metodi restituiscono un'istanza di [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) o un oggetto (JavaScript), con le proprietà di stringa seguenti:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (solo funzioni 2.x)

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

## <a name="rewind-instances-preview"></a>Rewind istanze (anteprima)

Se si dispone di un errore di orchestrazione per un motivo non previsto, è possibile *rewind* l'istanza da uno stato integro in precedenza usando un'API creata per tale scopo.

> [!NOTE]
> Questa API non deve sostituire una corretta gestione degli errori e i criteri di ripetizione dei tentativi, ma deve essere usata solo nei casi in cui le istanze di orchestrazione hanno esito negativo per motivi non previsti. Per altre informazioni sui criteri di ripetizione dei tentativi e la gestione degli errori, vedere la [gestione degli errori](durable-functions-error-handling.md) argomento.

Usare la [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) o `rewindAsync` API (JavaScript) per inserire l'orchestrazione allo stato del *in esecuzione* dello stato. Eseguire di nuovo gli errori di esecuzione di attività o suborchestration che ha causato l'errore di orchestrazione.

Ad esempio, si supponga che comportano una serie di un flusso di lavoro [approvazioni umane](durable-functions-concepts.md#human). Si supponga che esistono una serie di funzioni di attività che inviano una notifica di un utente che è necessaria l'approvazione e attendere la risposta in tempo reale. Dopotutto delle attività di approvazione volta ricevute le risposte o timeout, si supponga che un'altra attività ha esito negativo a causa di un errore di configurazione dell'applicazione, ad esempio una stringa di connessione di database non è valida. Ne consegue un errore di orchestrazione nel flusso di lavoro. Con il `RewindAsync` (.NET) o `rewindAsync` API, un'applicazione (JavaScript) amministratore può risolvere l'errore di configurazione e rewind l'orchestrazione non riuscita di stato immediatamente prima dell'errore. Nessuna delle operazioni di interazione tra uomo dovranno essere nuovamente approvato e l'orchestrazione può essere completata correttamente.

> [!NOTE]
> Il *rewind* funzionalità non supporta riavvolgimento le istanze di orchestrazione che usano i timer permanenti.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (solo funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile tornare indietro un'istanza di orchestrazione direttamente tramite il [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` comando. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione.
* **`reason` (facoltativo)**: Motivo per l'istanza di orchestrazione riavvolgimento.
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è `DurableFunctionsHub`. È possibile impostarla anche [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Eliminare la cronologia di istanza

Per rimuovere tutti i dati associati a un'orchestrazione, è possibile eliminare la cronologia di istanza. Ad esempio, è possibile eliminare le righe della tabella di Azure e BLOB di messaggi di grandi dimensioni, se esistono. A tale scopo, usare il [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API.

> [!NOTE]
> L'API `PurgeInstanceHistoryAsync` è attualmente disponibile solo per C#.

 Il metodo presenta due overload. Il primo Cancella cronologia dall'ID dell'istanza di orchestrazione:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Nel secondo esempio viene illustrata una funzione attivata tramite timer che ripulisce la cronologia per tutte le istanze di orchestrazione completate dopo l'intervallo di tempo specificato. In questo caso, rimuove i dati per tutte le istanze completate 30 o più giorni fa. Viene pianificata per eseguire una volta al giorno, alle ore 12:

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
> Per il processo della funzione attivata dalla fase abbia esito positivo, lo stato di runtime deve essere **Completed**, **Terminated**, o **Failed**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Si possono eliminare la cronologia dell'istanza di un'orchestrazione utilizzando la [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` comando. Simile al secondo C# esempio nella sezione precedente, Elimina la cronologia per tutte le istanze di orchestrazione create durante un intervallo di tempo specificato. È possibile filtrare ulteriormente le istanze eliminate in base allo stato di runtime. Il comando ha diversi parametri:

* **`created-after` (facoltativo)**: consente di ripulire la cronologia delle istanze create dopo questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`created-before` (facoltativo)**: consente di ripulire la cronologia delle istanze create prima di questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`runtime-status` (facoltativo)**: Eliminare la cronologia delle istanze con un determinato stato (ad esempio, in esecuzione o completato). Può fornire più stati (separati da spazi).
* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è `DurableFunctionsHub`. È possibile impostarla anche [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

Il comando seguente elimina la cronologia di tutte le istanze non riuscite creato prima del 14 novembre 2018 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Eliminare un hub attività

Usando il [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` comando, è possibile eliminare tutti gli elementi di archiviazione associati a un hub attività particolare. Sono inclusi i BLOB, le code e le tabelle di archiviazione di Azure. Il comando presenta due parametri:

* **`connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* **`task-hub-name` (facoltativo)**: Nome dell'hub attività funzioni durevoli da usare. Il valore predefinito è `DurableFunctionsHub`. È possibile impostarla anche [host. JSON](durable-functions-bindings.md#host-json), usando durableTask:HubName.

Il seguente comando Elimina tutti i dati di archiviazione di Azure associati il `UserTest` hub attività.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare le API HTTP per la gestione delle istanze](durable-functions-http-api.md)

<!-- Update_Description: wording update -->