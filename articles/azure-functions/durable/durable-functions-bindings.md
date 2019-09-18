---
title: Associazioni per Funzioni permanenti - Azure
description: Come usare trigger e associazioni per l'estensione Durable Functions di Funzioni di Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: f297c89d2c3ba5692a44fab631c0d46c75f48692
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033592"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associazioni per Funzioni permanenti (Funzioni di Azure)

L'estensione [Funzioni permanenti](durable-functions-overview.md) introduce due nuove associazioni di trigger che controllano l'esecuzione delle funzioni dell'agente di orchestrazione e di attività. L'estensione include inoltre un'associazione di output che funge da client per il runtime di Funzioni permanenti.

## <a name="orchestration-trigger"></a>Trigger di orchestrazione

Il trigger di orchestrazione consente di creare funzioni dell'agente di [orchestrazione durevoli](durable-functions-types-features-overview.md#orchestrator-functions). Il trigger supporta l'avvio di nuove istanze di funzioni dell'agente di orchestrazione e la ripresa di istanze di funzioni analoghe esistenti in attesa di un'attività.

Quando si usano gli strumenti di Visual Studio per Funzioni di Azure, il trigger di orchestrazione viene configurato tramite l'attributo .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html).

Quando si scrivono funzioni dell'agente di orchestrazione in linguaggi di script (ad esempio JavaScript o C#), il trigger di orchestrazione viene definito dall'oggetto JSON seguente nella matrice `bindings` del file *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` è il nome dell'orchestrazione. Questo è il valore che i client devono usare quando si desidera avviare nuove istanze di questa funzione dell'agente di orchestrazione. Questa proprietà è facoltativa. Se non specificato, viene usato il nome della funzione.

Internamente questa associazione di trigger esegue il polling di una serie di code nell'account di archiviazione predefinito per l'app per le funzioni. Tali code sono dettagli di implementazione interna dell'estensione, motivo per cui non sono configurate in modo esplicito nelle proprietà dell'associazione.

### <a name="trigger-behavior"></a>Comportamento di trigger

Di seguito vengono indicate alcune note relative al trigger di orchestrazione:

* **Thread singolo** - Un singolo thread del dispatcher viene usato per l'esecuzione di tutte le funzioni dell'agente di orchestrazione in un'unica istanza dell'host. Per questo motivo, è importante verificare che codice della funzione dell'agente di orchestrazione sia efficiente e non esegua alcuna operazioni di I/O. È anche importante garantire che il thread non esegua alcuna operazione asincrona, ad eccezione di quando è in attesa su tipi di attività specifiche di Funzioni permanenti.
* **Gestione di messaggi non elaborabili** - I trigger di orchestrazione non forniscono supporto per i messaggi non elaborabili.
* **Visibilità dei messaggi** - I messaggi dei trigger di orchestrazione vengono rimossi dalla coda e resi invisibili per un periodo di tempo configurabile. La visibilità di questi messaggi viene rinnovata automaticamente fino a quando l'app per le funzioni è in esecuzione e integra.
* **Valori restituiti** - I valori restituiti vengono serializzati in JSON e salvati in modo permanente nella tabella di cronologia di orchestrazione nell'archiviazione tabelle di Azure. Su tali valori l'associazione client di orchestrazione può eseguire query, come descritto in seguito.

> [!WARNING]
> Le funzioni dell'agente di orchestrazione non devono mai usare alcuna associazione di input o output diversa da quella di trigger di orchestrazione. In caso contrario, possono verificarsi problemi con l'estensione di attività permanenti in quanto tali associazioni potrebbero non rispettare le regole di thread singolo e di I/O. Se si vuole usare altre associazioni, aggiungerle a una funzione di attività chiamata dalla funzione dell'agente di orchestrazione.

> [!WARNING]
> Le funzioni di orchestrazione di JavaScript non devono mai essere dichiarate `async`.

### <a name="trigger-usage-net"></a>Utilizzo dei trigger (.NET)

L'associazione di trigger di orchestrazione supporta sia input che output. Di seguito sono illustrati alcuni aspetti da conoscere sulla gestione di input e output.

* **input** - Le funzioni di orchestrazione .NET supportano solo [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) come tipo di parametro. La deserializzazione degli input direttamente nella firma della funzione non è supportata. Il codice deve usare il metodo [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) o `getInput` (JavaScript) per recuperare gli input della funzione dell'agente di orchestrazione. I dati di input devono essere tipi serializzabili in JSON.
* **output** -I trigger di orchestrazione supportano valori di output in modo analogo ai valori di input. Il valore restituito della funzione viene usato per assegnare il valore di output e deve essere serializzabile in JSON. Se una funzione .NET restituisce `Task` o `void`, un valore `null` viene salvato come output.

### <a name="trigger-sample"></a>Esempio di trigger

Nell'esempio di codice seguente viene illustrata la funzione dell'agente di orchestrazione "Hello World" più semplice:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> L' `context` oggetto in JavaScript non rappresenta DurableOrchestrationContext, ma il contesto della [funzione nel suo complesso](../functions-reference-node.md#context-object). È possibile accedere ai metodi di orchestrazione tramite la proprietà `df` dell'oggetto `context`.

> [!NOTE]
> Gli orchestratori JavaScript devono usare `return`. La libreria `durable-functions` provvede alla chiamata del metodo `context.done`.

La maggior parte delle funzioni dell'agente di orchestrazione chiama funzioni di attività, pertanto viene riportato un esempio "Hello World" che illustra come chiamare una funzione di attività:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Trigger attività

Il trigger di attività consente di creare funzioni chiamate da funzioni dell'agente di orchestrazione, note come [funzioni di attività](durable-functions-types-features-overview.md#activity-functions).

Se si usa Visual Studio, il trigger di attività viene configurato tramite l'attributo .NET [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html).

Se si usa VS Code o il portale di Azure per lo sviluppo, il trigger di attività viene definito per l'oggetto JSON seguente nella matrice `bindings` di *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` è il nome dell'attività. Questo valore è il nome usato dalle funzioni dell'agente di orchestrazione per richiamare questa funzione di attività. Questa proprietà è facoltativa. Se non specificato, viene usato il nome della funzione.

Internamente questa associazione di trigger esegue il polling di una coda nell'account di archiviazione predefinito per l'app per le funzioni. Tale coda è un dettaglio di implementazione interna dell'estensione, motivo per cui non è configurata in modo esplicito nelle proprietà dell'associazione.

### <a name="trigger-behavior"></a>Comportamento di trigger

Di seguito vengono indicate alcune note relative al trigger di attività:

* **Threading** - A differenza del trigger di orchestrazione, i trigger di attività non presentano restrizioni relative a threading oppure a operazioni di I/O e possono essere considerati come normali funzioni.
* **Gestione di messaggi non elaborabili** - I trigger di attività non supportano i messaggi non elaborabili.
* **Visibilità dei messaggi** - I messaggi dei trigger di attività vengono rimossi dalla coda e resi invisibili per un periodo di tempo configurabile. La visibilità di questi messaggi viene rinnovata automaticamente fino a quando l'app per le funzioni è in esecuzione e integra.
* **Valori restituiti** - I valori restituiti vengono serializzati in JSON e salvati in modo permanente nella tabella di cronologia di orchestrazione nell'archiviazione tabelle di Azure.

> [!WARNING]
> Il back-end di archiviazione per le funzioni di attività è un dettaglio di implementazione e il codice utente non deve interagire direttamente con tali entità di archiviazione.

### <a name="trigger-usage-net"></a>Utilizzo dei trigger (.NET)

In modo analogo al trigger di orchestrazione, l'associazione di trigger di attività supporta sia input che output. Di seguito sono illustrati alcuni aspetti da conoscere sulla gestione di input e output.

* **input** - Le funzioni di attività .NET usano [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) come tipo di parametro in modo nativo. In alternativa, una funzione di attività può essere dichiarata con qualsiasi tipo di parametro serializzabile in JSON. Quando si usa `DurableActivityContext`, è possibile chiamare [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) per recuperare e deserializzare l'input della funzione di attività.
* **output** - Le funzioni di attività supportano i valori di output in modo analogo a quelli di input. Il valore restituito della funzione viene usato per assegnare il valore di output e deve essere serializzabile in JSON. Se una funzione .NET restituisce `Task` o `void`, un valore `null` viene salvato come output.
* **metadati** - Le funzioni di attività .NET possono eseguire l'associazione a un parametro `string instanceId` per ottenere l'ID istanza dell'orchestrazione padre.

### <a name="trigger-sample"></a>Esempio di trigger

Il codice di esempio seguente mostra l'aspetto di una semplice funzione di attività "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Il tipo di parametro predefinito per l'associazione .NET `ActivityTriggerAttribute` è `DurableActivityContext`. I trigger di attività .NET, tuttavia, supportano anche l'associazione diretta a tipi serializzabili in JSON, ad esempio tipi primitivi, e di conseguenza la stessa funzione potrebbe essere semplificata nel modo seguente:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Le associazioni JavaScript possono anche essere passate come parametri aggiuntivi, di conseguenza la stessa funzione potrebbe essere semplificata nel modo seguente:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Uso delle associazioni di input e output

È possibile utilizzare le associazioni di input e output regolari oltre al binding del trigger di attività. Ad esempio, è possibile prendere l'input dell'associazione di attività e inviare un messaggio a un EventHub usando l'associazione di output di EventHub:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Client di orchestrazione

L'associazione del client di orchestrazione consente di scrivere funzioni che interagiscono con funzioni dell'agente di orchestrazione. Queste funzioni vengono talvolta definite [funzioni client](durable-functions-types-features-overview.md#client-functions). È possibile ad esempio agire su istanze di orchestrazione nei modi seguenti:

* Avviare le istanze.
* Eseguire query sullo stato delle istanze.
* Terminare le istanze.
* Inviare eventi alle istanze mentre sono in esecuzione.
* Ripulire la cronologia di istanza.

Se si usa Visual Studio, è possibile eseguire l'associazione al client di orchestrazione usando l'attributo .NET [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) per Durable functions 1,0. A partire da Durable Functions 2,0 Preview, è possibile eseguire l'associazione al client di orchestrazione `DurableClientAttribute` usando l'attributo .NET.

Se si usano linguaggi di scripting, ad esempio file con *estensione CSX* o *JS* , per lo sviluppo, il trigger di orchestrazione viene definito dall' `bindings` oggetto JSON seguente nella matrice di *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - Usato in scenari in cui più app per le funzioni condividono lo stesso account di archiviazione, ma devono essere isolate tra loro. Se non specificato, viene usato il valore predefinito da `host.json`. Tale valore deve corrispondere a quello usato dalle funzioni dell'agente di orchestrazione di destinazione.
* `connectionName` - Il nome di un'impostazione dell'app che contiene la stringa di connessione di un account di archiviazione. L'account di archiviazione rappresentato da questa stringa di connessione deve essere lo stesso usato dalle funzioni dell'agente di orchestrazione di destinazione. Se non è specificato, viene usata la stringa di connessione dell'account di archiviazione predefinito dell'app per le funzioni.

> [!NOTE]
> Nella maggior parte dei casi è consigliabile omettere queste proprietà e basarsi sul comportamento predefinito.

### <a name="client-usage"></a>Uso del client

Nelle funzioni .NET in genere se esegue l'associazione a `DurableOrchestrationClient` in modo da disporre dell'acceso completo a tutte le API client supportate da Durable Functions. A partire da Durable Functions 2,0, viene invece associato all' `IDurableOrchestrationClient` interfaccia. In JavaScript, le stesse API sono esposte dall'oggetto restituito `getClient`da. Le API per l'oggetto client includono:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

In alternativa, le funzioni .NET possono eseguire l'associazione a `IAsyncCollector<T>`, dove `T` è [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) o `JObject`.

Per ulteriori informazioni su queste operazioni, vedere la documentazione dell'API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) .

### <a name="client-sample-visual-studio-development"></a>Esempio di client (sviluppo di Visual Studio)

Di seguito viene riportata una funzione di esempio attivata dalla coda che avvia un'orchestrazione "HelloWorld".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Esempio di client (non Visual Studio)

Se per lo sviluppo non si usa Visual Studio, è possibile creare il file *function.json* seguente. Questo esempio illustra come configurare una funzione attivata dalla coda che usa l'associazione di client di orchestrazione permanente:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Di seguito vengono riportati esempi specifici del linguaggio che avviano nuove istanze della funzione dell'agente di orchestrazione.

#### <a name="c-sample"></a>Esempio in C#

L'esempio seguente illustra come usare l'associazione di client di orchestrazione permanente per avviare una nuova istanza della funzione da una funzione di script C#:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Esempio di JavaScript

L'esempio seguente illustra come usare l'associazione di client di orchestrazione permanente per avviare una nuova istanza della funzione da una funzione di JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Per altre informazioni sull'avvio di istanze, vedere [Instance management](durable-functions-instance-management.md) (Gestione di istanze).

## <a name="entity-trigger"></a>Trigger di entità

I trigger di entità consentono di creare [funzioni dell'entità](durable-functions-entities.md). Questo trigger supporta l'elaborazione di eventi per un'istanza di entità specifica.

Quando si usano le funzioni di Visual Studio Tools per Azure, il trigger di entità viene configurato `EntityTriggerAttribute` usando l'attributo .NET.

> [!NOTE]
> I trigger di entità sono disponibili in Durable Functions 2,0 e versioni successive. I trigger di entità non sono ancora disponibili per JavaScript.

Internamente questa associazione di trigger esegue il polling di una serie di code nell'account di archiviazione predefinito per l'app per le funzioni. Tali code sono dettagli di implementazione interna dell'estensione, motivo per cui non sono configurate in modo esplicito nelle proprietà dell'associazione.

### <a name="trigger-behavior"></a>Comportamento di trigger

Di seguito sono riportate alcune note sul trigger di entità:

* **A thread singolo**: Un singolo thread Dispatcher viene utilizzato per elaborare le operazioni per un'entità specifica. Se più messaggi vengono inviati contemporaneamente a una singola entità, le operazioni verranno elaborate una alla volta.
* **Gestione dei messaggi non elaborabili** : nessun supporto dei messaggi non elaborabili nei trigger di entità.
* **Visibilità del messaggio** : i messaggi del trigger dell'entità vengono rimossi dalla coda e mantenuti invisibili per una durata configurabile. La visibilità di questi messaggi viene rinnovata automaticamente fino a quando l'app per le funzioni è in esecuzione e integra.
* **Valori restituiti** : le funzioni di entità non supportano valori restituiti. Sono disponibili API specifiche che possono essere utilizzate per salvare lo stato o passare i valori alle orchestrazioni.

Eventuali modifiche di stato apportate a un'entità durante la relativa esecuzione verranno rese automatiche automaticamente dopo il completamento dell'esecuzione.

### <a name="trigger-usage-net"></a>Utilizzo dei trigger (.NET)

Ogni funzione di entità ha un tipo di `IDurableEntityContext`parametro, che dispone dei membri seguenti:

* **EntityName**: Ottiene il nome dell'entità attualmente in esecuzione.
* **EntityKey**: Ottiene la chiave dell'entità attualmente in esecuzione.
* **EntityId**: Ottiene l'ID dell'entità attualmente in esecuzione.
* **OperationName**: Ottiene il nome dell'operazione corrente.
* **IsNewlyConstructed**: restituisce `true` se l'entità non esisteva prima dell'operazione.
* **GetState\<tstate > ()** : ottiene lo stato corrente dell'entità. Il `TState` parametro deve essere un tipo primitivo o serializzabile in JSON.
* **Sestate (Object)** : aggiorna lo stato dell'entità. Il `object` parametro deve essere un oggetto primitivo o serializzabile in JSON.
* **GetInput\<TInput > ()** : Ottiene l'input per l'operazione corrente. Il `TInput` parametro di tipo deve rappresentare un tipo primitivo o serializzabile in JSON.
* **Return (Object)** : restituisce un valore all'orchestrazione che ha chiamato l'operazione. Il `object` parametro deve essere un oggetto primitivo o serializzabile in JSON.
* **DestructOnExit ()** : Elimina l'entità dopo aver terminato l'operazione corrente.
* **SignalEntity (EntityId, String, Object)** : Invia un messaggio unidirezionale a un'entità. Il `object` parametro deve essere un oggetto primitivo o serializzabile in JSON.

Quando si usa la modalità di programmazione dell'entità basata su `IDurableEntityContext` classi, è possibile fare riferimento all' `Entity.Current` oggetto tramite la proprietà thread-static.

### <a name="trigger-sample---entity-function"></a>Trigger sample-funzione Entity

Il codice seguente è un esempio di un'entità *contatore* semplice implementata come funzione standard. Questa funzione definisce tre *operazioni*, `add` `reset`, e `get`, ciascuna delle quali opera su un valore di stato Integer, `currentValue`.

```csharp
[FunctionName(nameof(Counter))]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

### <a name="trigger-sample---entity-class"></a>Esempio di trigger-classe di entità

L'esempio seguente è un'implementazione equivalente dell'entità precedente `Counter` con le classi e i metodi .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> Il metodo del punto di ingresso della `[FunctionName]` funzione con l'attributo `static` *deve* essere dichiarato quando si utilizzano le classi di entità. I metodi del punto di ingresso non statico possono comportare l'inizializzazione di più oggetti e potenzialmente altri comportamenti non definiti.

Le classi di entità hanno meccanismi speciali per interagire con le associazioni e l'inserimento di dipendenze .NET. Per altre informazioni, vedere l'articolo sulle [entità durevoli](durable-functions-entities.md) .

## <a name="entity-client"></a>Client entità

L'associazione client di entità consente di attivare in modo asincrono le [funzioni dell'entità](#entity-trigger). Queste funzioni vengono talvolta definite [funzioni client](durable-functions-types-features-overview.md#client-functions).

Se si usa Visual Studio, è possibile eseguire l'associazione al client di entità usando l' `DurableClientAttribute` attributo .NET.

> [!NOTE]
> Può inoltre essere utilizzato per eseguire l'associazione al [client di orchestrazione.](#orchestration-client) `[DurableClientAttribute]`

Se si usano linguaggi di scripting, ad esempio file con *estensione CSX* o *JS* , per lo sviluppo, il trigger di entità viene definito dall' `bindings` oggetto JSON seguente nella matrice di *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` - Usato in scenari in cui più app per le funzioni condividono lo stesso account di archiviazione, ma devono essere isolate tra loro. Se non specificato, viene usato il valore predefinito da `host.json`. Questo valore deve corrispondere al valore usato dalle funzioni dell'entità di destinazione.
* `connectionName` - Il nome di un'impostazione dell'app che contiene la stringa di connessione di un account di archiviazione. L'account di archiviazione rappresentato da questa stringa di connessione deve essere uguale a quello usato dalle funzioni dell'entità di destinazione. Se non è specificato, viene usata la stringa di connessione dell'account di archiviazione predefinito dell'app per le funzioni.

> [!NOTE]
> Nella maggior parte dei casi, è consigliabile omettere le proprietà facoltative e basarsi sul comportamento predefinito.

### <a name="entity-client-usage"></a>Utilizzo di Entity client

Nelle funzioni .NET, in genere si esegue `IDurableEntityClient`l'associazione a, che consente l'accesso completo a tutte le API client supportate da entità durevoli. È anche possibile eseguire l'associazione `IDurableClient` all'interfaccia, che fornisce l'accesso alle API client per le entità e le orchestrazioni. Le API per l'oggetto client includono:

* **ReadEntityStateAsync\<T >** : legge lo stato di un'entità.
* **SignalEntityAsync**: Invia un messaggio unidirezionale a un'entità e ne attende l'accodamento.
* **SignalEntityAsync\<TEntityInterface >** : uguale a `SignalEntityAsync` , ma usa un oggetto proxy generato di `TEntityInterface`tipo.
* **CreateEntityProxy\<TEntityInterface >** : genera dinamicamente un proxy dinamico di tipo `TEntityInterface` per eseguire chiamate indipendenti dai tipi alle entità.

> [!NOTE]
> È importante comprendere che le operazioni "Signal" precedenti sono tutte asincrone. Non è possibile richiamare una funzione di entità e ottenere un valore restituito da un client. Analogamente, `SignalEntityAsync` può restituire prima che l'entità inizi a eseguire l'operazione. Solo le funzioni dell'agente di orchestrazione possono richiamare funzioni di entità in modo sincrono ed elaborare valori restituiti.

Per le `SignalEntityAsync` API è necessario specificare l'identificatore univoco dell'entità come. `EntityId` Queste API accettano facoltativamente anche il nome dell'operazione dell'entità come `string` e il payload dell'operazione come `object`serializzabile JSON. Se l'entità di destinazione non esiste, verrà creata automaticamente con l'ID entità specificato.

### <a name="client-sample-untyped"></a>Esempio client (non tipizzato)

Di seguito è riportato un esempio di funzione attivata dalla coda che richiama un'entità "contatore".

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="client-sample-typed"></a>Esempio client (tipizzato)

È possibile generare un oggetto proxy per l'accesso indipendente dai tipi alle operazioni dell'entità. Per generare un proxy indipendente dai tipi, il tipo di entità deve implementare un'interfaccia. Si supponga, ad esempio `Counter` , che l'entità citata in precedenza abbia implementato un' `ICounter` interfaccia, definita come segue:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Il codice client può quindi `SignalEntityAsync<TEntityInterface>` utilizzare e specificare `ICounter` l'interfaccia come parametro di tipo per generare un proxy indipendente dai tipi. Questo utilizzo di proxy indipendenti dai tipi viene illustrato nell'esempio di codice seguente:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

Nell'esempio precedente, `proxy` il parametro è un'istanza generata dinamicamente di `ICounter`, che converte internamente la chiamata a `Add` nella chiamata equivalente (non tipizzata) a `SignalEntityAsync`.

Esistono alcune regole per la definizione delle interfacce di entità:

* Il parametro `TEntityInterface` di tipo `SignalEntityAsync<TEntityInterface>` in deve essere un'interfaccia.
* Le interfacce di entità devono definire solo metodi.
* I metodi dell'interfaccia di entità non devono definire più di un parametro.
* I metodi dell'interfaccia di `void`entità `Task`devono restituire `Task<T>` , `T` o dove è un valore restituito.
* Le interfacce di entità devono avere esattamente una classe di implementazione concreta nello stesso assembly (ovvero la classe di entità).

Se una di queste regole viene violata, verrà `InvalidOperationException` generata un'eccezione in fase di esecuzione. Il messaggio di eccezione spiegherà quale regola è stata interrotta.

> [!NOTE]
> Le `SignalEntityAsync` API rappresentano operazioni unidirezionali. Se un'interfaccia `T` di entità `Task<T>`restituisce, il valore del parametro sarà sempre null o. `default`

<a name="host-json"></a>

## <a name="hostjson-settings"></a>impostazioni host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API HTTP predefinite per la gestione delle istanze](durable-functions-http-api.md)