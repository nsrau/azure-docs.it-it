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
ms.date: 11/27/2018
ms.author: azfuncdf
ms.openlocfilehash: 9b85ce6bdb7f72c5e4f1cf0d47cc324f5f75ec20
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637266"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Gestire le istanze in Funzioni permanenti (Funzioni di Azure)

Le istanze di orchestrazione di [Funzioni permanenti](durable-functions-overview.md) possono essere avviate e terminate, è possibile eseguire query su esse e inviare a esse eventi di notifica. La gestione delle istanza viene eseguita usando l'[associazione del client di orchestrazione](durable-functions-bindings.md). Questo articolo riporta informazioni dettagliate su ogni operazione di gestione delle istanze.

## <a name="starting-instances"></a>Avvio di istanze

Il metodo [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) di [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) avvia una nuova istanza di una funzione dell'agente di orchestrazione. Le istanze di questa classe possono essere acquisite tramite l'associazione `orchestrationClient`. Internamente, questo metodo accoda un messaggio nella coda di controllo, che poi attiva l'avvio di una funzione con il nome specificato che usa l'associazione del trigger `orchestrationTrigger`. 

L'attività viene completata quando viene avviato il processo di orchestrazione. Il processo di orchestrazione dovrebbe essere avviato entro 30 secondi. Se l'avvio richiede più tempo, viene generata un'eccezione`TimeoutException`. 

I parametri per [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) sono i seguenti:

* **Nome**: il nome della funzione dell'agente di orchestrazione da pianificare.
* **Input**: dati serializzabili in JSON che devono essere passati come input alla funzione dell'agente di orchestrazione.
* **InstanceId**: (facoltativo) l'ID univoco dell'istanza. Se non è specificato, viene generato un ID istanza casuale.

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

Per i linguaggi non .NET, l'associazione dell'output della funzione può essere utilizzata anche per avviare nuove istanze. In questo caso può essere usato qualsiasi oggetto serializzabile in JSON che presenta i tre parametri indicati sopra come campi. Si consideri ad esempio la seguente funzione JavaScript:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

Il codice sopra riportato presuppone che nel file function.json sia stata definita un'associazione di uscita con nome `starter` e di tipo `orchestrationClient`. Se l'associazione non è stata definita, l'istanza della funzione permanente non verrà creata.

Per poter richiamare la funzione permanente, è necessario modificare il file function.json in modo che contenga un'associazione per il client di orchestrazione come descritto di seguito

```js
{
    "bindings": [{
        "name":"starter",
        "type":"orchestrationClient",
        "direction":"out"
    }]
}
```

> [!NOTE]
> Usare un identificatore casuale per l'ID istanza. Questo contribuisce a garantire una distribuzione del carico equa quando si ridimensionano le funzioni dell'agente di orchestrazione tra più VM. Il momento opportuno per usare ID istanza non casuali è quando l'ID deve provenire da un'origine esterna o quando si implementa il modello [agente di orchestrazione singleton](durable-functions-singletons.md).

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile avviare un'istanza direttamente tramite il comando [Strumenti di base di Funzioni di Azure](../functions-run-local.md) `durable start-new`. È necessario specificare i seguenti parametri:

* **`function-name` (obbligatorio)** : Nome della funzione per iniziare
* **`input` (facoltativo)** : Input alla funzione, entrambi in linea o tramite un file JSON. Per i file, aggiungere al percorso del file il prefisso `@`, ad esempio `@path/to/file.json`.
* **`id` (facoltativo)**: ID dell'istanza di orchestrazione. Se non specificato, viene generato un GUID casuale.
* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName. 

> [!NOTE]
> I comandi degli strumenti di base presuppongono che vengano eseguiti dalla directory radice di un'app per le funzioni. Se `connection-string-setting` e `task-hub-name` vengono fornite in modo esplicito, i comandi possono essere eseguiti da qualsiasi directory. Anche se questi comandi possono essere eseguiti senza un host di app per le funzione in esecuzione, alcuni effetti potrebbero non essere osservati, a meno che l'host non sia in esecuzione. Ad esempio, il comando `start-new` accoderà un messaggio di avvio nell'hub attività di destinazione, ma l'orchestrazione non verrà effettivamente eseguita a meno che non sia presente un processo host di app per le funzioni in esecuzione in grado di elaborare il messaggio.

Il comando seguente avvia la funzione denominata HelloWorld e passa il contenuto del file "counter-data.json":
```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>Esecuzione di query sulle istanze

Il metodo [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) esegue una query sullo stato di un'istanza di orchestrazione.

Accetta `instanceId` (obbligatorio), `showHistory` (facoltativo), `showHistoryOutput` (facoltativo) e `showInput` (facoltativo) come parametri. 
* **`showHistory`**: Se è impostato su `true`, la risposta conterrà la cronologia dell'esecuzione. 
* **`showHistoryOutput`**: Se impostato su `true`, la cronologia dell'esecuzione conterrà gli output delle attività. 
* **`showInput`**: Se impostato su `false`, la risposta non conterrà l'input della funzione. Il valore predefinito è `true`.

Il metodo restituisce un oggetto JSON con le proprietà seguenti:

* **Name**: il nome della funzione dell'agente di orchestrazione.
* **InstanceId**: l'ID istanza dell'orchestrazione (deve essere lo stesso dell'input `instanceId`).
* **CreatedTime**: l'ora in cui la funzione dell'agente di orchestrazione ha iniziato l'esecuzione.
* **LastUpdatedTime**: l'ora in cui l'orchestrazione ha eseguito l'ultimo checkpoint.
* **Input**: l'input della funzione come valore JSON. Questo campo non viene popolato se `showInput` è false.
* **CustomStatus**: stato personalizzato dell'orchestrazione in formato JSON. 
* **Output**: l'output della funzione come valore JSON (se la funzione è stata completata). Se l'esecuzione della funzione dell'agente di orchestrazione non è riuscita, questa proprietà include i dettagli dell'errore. Se la funzione dell'agente di orchestrazione è stata terminata, questa proprietà include il motivo dell'interruzione (se presente).
* **RuntimeStatus**: uno dei valori seguenti:
    * **In sospeso**: l'istanza è stata pianificata ma non è ancora iniziata l'esecuzione.
    * **Running**: l'istanza ha iniziato l'esecuzione.
    * **Completed**: l'istanza è stata completata normalmente.
    * **ContinuedAsNew**: l'istanza si è riavviata con una nuova cronologia. Si tratta di uno stato temporaneo.
    * **Failed**: l'esecuzione dell'istanza non è riuscita e ha generato un errore.
    * **Terminata**: l'istanza è stata terminata in modo anomalo.
* **History**: cronologia di esecuzione dell'orchestrazione. Questo campo viene popolato solo se `showHistory` è impostato su `true`.
    
Questo metodo restituisce `null` se l'istanza non esiste o non è ancora iniziata la sua esecuzione.

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

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile ottenere lo stato di un'istanza di orchestrazione direttamente tramite il comando [Strumenti di base di Funzioni di Azure](../functions-run-local.md) `durable get-runtime-status`. È necessario specificare i seguenti parametri: 

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione
* **`show-input`: (facoltativo)**: Se impostato su `true`, la risposta non conterrà l'input della funzione. Il valore predefinito è `false`.
* **`show-output`: (facoltativo)**: Se impostato su `true`, la risposta non conterrà l'output della funzione. Il valore predefinito è `false`.
* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Il comando seguente recupera lo stato (inclusi input e output) di un'istanza con un ID istanza di orchestrazione di 0ab8c55a66644d68a3a8b220b12d209c. Si presuppone che il comando `func` venga eseguito dalla directory radice dell'app per le funzioni:
```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Il comando `durable get-history` può essere utilizzato per recuperare la cronologia di un'istanza di orchestrazione. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione
* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in host.json tramite durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>Esecuzione di query su tutte le istanze

È possibile usare il metodo `GetStatusAsync` per eseguire una query sugli stati di tutte le istanze di orchestrazione. Il metodo non accetta alcun parametro oppure è possibile passare un oggetto `CancellationToken` nel caso in cui si vuole annullarlo. Il metodo restituisce oggetti con le stesse proprietà del metodo `GetStatusAsync` con parametri, ma non restituisce la cronologia. 

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

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile eseguire query delle istanze direttamente tramite il comando [Strumenti di base di Funzioni di Azure](../functions-run-local.md) `durable get-instances`. È necessario specificare i seguenti parametri:

* **`top` (facoltativo)** : Questo comando supporta il paging. Questo parametro corrisponde al numero di istanze recuperate per ogni richiesta. Il valore predefinito è 10.
* **`continuation-token` (facoltativo)** : Un token per indicare quali pagina o sezione di istanze sono da recuperare. Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>Esecuzione di query sulle istanze con i filtri

È anche possibile usare il metodo `GetStatusAsync` per ottenere un elenco di istanze di orchestrazione che corrispondono a un set di filtri predefiniti. Le opzioni di filtro possibili includono l'ora di creazione e lo stato di runtime dell'orchestrazione.

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

### <a name="using-the-functions-core-tools"></a>Uso degli strumenti di base di Funzioni di Azure

Il comando `durable get-instances` può essere utilizzato anche con i filtri. Oltre ai parametri citati in precedenza `top`, `continuation-token`, `connection-string-setting`, e `task-hub-name`, possono essere utilizzati tre parametri di filtro (`created-after`, `created-before`, e `runtime-status`). 

* **`created-after` (facoltativo)** : Recuperare le istanze create dopo questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`created-before` (facoltativo)** : Recuperare le istanze create prima di questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`runtime-status` (facoltativo)** : Recuperare le istanze il cui stato corrisponde ai valori seguenti ("in esecuzione, "completato", eccetera). Può fornire più stati (separati da spazi).
* **`top` (facoltativo)** : Numero di istanze recuperate per ogni richiesta. Il valore predefinito è 10.
* **`continuation-token` (facoltativo)** : Un token per indicare quali pagina o sezione di istanze sono da recuperare. Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Se non viene specificato nessun filtro (`created-after`, `created-before` o `runtime-status`), allora le istanze `top` verranno recuperate senza considerare lo stato o la data di creazione di runtime.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>Terminazione delle istanze

Un'istanza di orchestrazione in esecuzione può essere terminata usando il metodo [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). I due parametri sono un `instanceId` e una stringa `reason` che saranno scritte nei log e nello stato dell'istanza. Un'istanza terminata termina la sua esecuzione non appena raggiunge il successivo punto `await` oppure termina immediatamente se è già in un `await`. 

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

> [!NOTE]
> Al momento, la terminazione delle istanze non viene propagata. Le funzioni di attività e le orchestrazioni secondarie verranno eseguite fino al completamento anche se l'istanza di orchestrazione che le ha chiamate è stata terminata.

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile terminare un'istanza di orchestrazione direttamente tramite il comando [Strumenti di base di Funzioni di Azure](../functions-run-local.md) `durable terminate`. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione da terminare
* **`reason` (facoltativo)** : Motivo dell'interruzione
* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Un'istanza di orchestrazione con l'ID di 0ab8c55a66644d68a3a8b220b12d209c interrompe il comando seguente:
```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>Invio di eventi alle istanze

È possibile inviare notifiche degli eventi alle istanze in esecuzione usando il metodo [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Le istanze che possono gestire questi eventi sono quelle in attesa di una chiamata di [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

I parametri per [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) sono i seguenti:

* **InstanceId**: l'ID univoco dell'istanza.
* **EventName**: il nome dell'evento da inviare.
* **EventData**: un payload serializzabile in JSON da inviare all'istanza.

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

> [!WARNING]
> Se non è presente un'istanza di orchestrazione con l'*ID istanza* specificato o se l'istanza non è in attesa del *nome evento* specificato, il messaggio dell'evento viene eliminato. Per altre informazioni su questo comportamento, vedere il [problema GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile generare un evento di un'istanza di orchestrazione direttamente tramite il comando [Strumenti di base](../functions-run-local.md) `durable raise-event`. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione
* **`event-name` (facoltativo)** : Nome dell'evento da generare. Il valore predefinito è `$"Event_{RandomGUID}"`
* **`event-data` (facoltativo)** : I dati da inviare all'istanza di orchestrazione. Può essere il percorso di un file JSON o i dati possono essere forniti direttamente nella riga di comando
* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```
```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Attendere il completamento dell'orchestrazione

La classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) espone un'API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) che può essere usata per ottenere in modo sincrono l'output effettivo da un'istanza di orchestrazione. Il metodo usa il valore predefinito di 10 secondi per `timeout` e di 1 secondo per `retryInterval` quando non sono impostati.  

Di seguito è riportato un esempio di funzione trigger HTTP che illustra come usare questa API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

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
> Il formato degli URL webhook può variare in base alla versione dell'host di Funzioni di Azure in esecuzione. L'esempio precedente è per l'host di Funzioni di Azure 2.0.

## <a name="retrieving-http-management-webhook-urls"></a>Recupero di URL di webhook di gestione HTTP

I sistemi esterni possono comunicare con Funzioni permanenti tramite gli URL dei webhook che fanno parte della risposta predefinita descritta in [Individuazione degli URL delle API HTTP](durable-functions-http-api.md). Tuttavia, è possibile accedere agli URL dei webhook anche a livello di codice nel client di orchestrazione o in una funzione di attività tramite il metodo [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) ha un parametro:

* **instanceId**: ID univoco dell'istanza.

Il metodo restituisce un'istanza di [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) con le proprietà stringa seguenti:

* **Id**: ID istanza dell'orchestrazione (deve essere lo stesso dell'input `InstanceId`).
* **StatusQueryGetUri**: URL di stato dell'istanza di orchestrazione.
* **SendEventPostUri**: URL di generazione di eventi dell'istanza di orchestrazione.
* **TerminatePostUri**: URL di terminazione dell'istanza di orchestrazione.
* **RewindPostUri**: URL di ripristino dell'istanza di orchestrazione.

Le funzioni di attività possono inviare un'istanza di [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) a sistemi esterni per monitorare o generare eventi in un'orchestrazione:

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

## <a name="rewinding-instances-preview"></a>Istanze di ripristino (anteprima)

Un'istanza di orchestrazione non riuscita può essere *ripristinata* a uno stato integro precedente usando l'API [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_). A questo scopo, l'orchestrazione viene ripristinata allo stato *In esecuzione* e vengono nuovamente eseguiti l'attività e/o gli errori di esecuzione dell'orchestrazione secondaria che hanno causato l'errore di orchestrazione.

> [!NOTE]
> Questa API non deve sostituire una corretta gestione degli errori e i criteri di ripetizione dei tentativi, ma deve essere usata solo nei casi in cui le istanze di orchestrazione hanno esito negativo per motivi non previsti. Per altre informazioni sulla gestione degli errori e sui criteri di ripetizione dei tentativi, vedere l'argomento [Gestione degli errori](durable-functions-error-handling.md).

Un esempio di caso d'uso di *ripristino* è un flusso di lavoro che prevede una serie di [approvazioni umane](durable-functions-overview.md#pattern-5-human-interaction). Si supponga che esista una serie di funzioni di attività in cui un utente viene informato che è necessaria la sua approvazione e che si attenda la risposta in tempo reale. Dopo che tutte le attività di approvazione hanno ricevuto le risposte o sono scadute, un'altra attività ha esito negativo a causa di un errore di configurazione dell'applicazione (come una stringa di connessione del database non valida). Ne consegue un errore di orchestrazione nel flusso di lavoro. Con l'API `RewindAsync`, un amministratore dell'applicazione può correggere l'errore di configurazione e *ripristinare* l'orchestrazione non riuscita allo stato immediatamente precedente all'errore. Nessuna delle operazioni che prevedono l'interazione umana deve essere nuovamente approvata e l'orchestrazione può essere ora completata.

> [!NOTE]
> La funzionalità di *ripristino* non supporta istanze di ripristino dell'orchestrazione in cui siano usati timer permanenti.

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

### <a name="using-core-tools"></a>Utilizzo degli strumenti di base

È anche possibile riavvolgere un'istanza di orchestrazione direttamente tramite il comando [Strumenti di base](../functions-run-local.md) `durable rewind`. È necessario specificare i seguenti parametri:

* **`id` (obbligatorio)**: ID dell'istanza di orchestrazione
* **`reason` (facoltativo)**: Motivo del ripristino dell'istanza di orchestrazione
* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Ripulire la cronologia di istanza

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
    return client.InnerClient.PurgeInstanceHistoryAsync( 
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

* **`created-after` (facoltativo)** : Ripulire le istanze create dopo questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`created-before` (facoltativo)** : Ripulire le istanze create prima di questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* **`runtime-status` (facoltativo)** : Ripulire le istanze il cui stato corrisponde ai valori seguenti ("in esecuzione, "completato", eccetera). Può fornire più stati (separati da spazi).
* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Il comando seguente eliminerebbe la cronologia di tutte le istanze non riuscite create prima del 14 novembre 2018 alle 19:35 (UTC).
```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>Eliminazione di un hub attività
Usando il comando [Strumenti di base](../functions-run-local.md) `durable delete-task-hub`, è possibile eliminare tutti gli artefatti di archiviazione associati a un hub attività particolare. Sono inclusi i BLOB, le code e le tabelle di archiviazione di Azure. Il comando presenta due parametri: 

* **`connection-string-setting` (facoltativo)** : Nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* **`task-hub-name` (facoltativo)** : Nome dell'hub attività permanenti da utilizzare. Il valore predefinito è DurableFunctionsHub. È possibile impostarlo anche in [host.json](durable-functions-bindings.md#host-json) tramite durableTask:HubName.

Il comando seguente eliminerebbe tutti i dati associati all'hub attività "UserTest" della risorsa di archiviazione di Azure.
```bash
func durable delete-task-hub --task-hub-name UserTest
```


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare le API HTTP per la gestione delle istanze](durable-functions-http-api.md)
