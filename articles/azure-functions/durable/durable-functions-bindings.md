---
title: Associazioni per Funzioni permanenti - Azure
description: Come usare trigger e associazioni per l'estensione Durable Functions di Funzioni di Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 40b5f0f17cbb6867a6ef293a485d728141a012ef
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233032"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associazioni per Funzioni permanenti (Funzioni di Azure)

L'estensione [Funzioni permanenti](durable-functions-overview.md) introduce due nuove associazioni di trigger che controllano l'esecuzione delle funzioni dell'agente di orchestrazione e di attività. L'estensione include inoltre un'associazione di output che funge da client per il runtime di Funzioni permanenti.

## <a name="orchestration-trigger"></a>Orchestration trigger

The orchestration trigger enables you to author [durable orchestrator functions](durable-functions-types-features-overview.md#orchestrator-functions). Il trigger supporta l'avvio di nuove istanze di funzioni dell'agente di orchestrazione e la ripresa di istanze di funzioni analoghe esistenti in attesa di un'attività.

Quando si usano gli strumenti di Visual Studio per Funzioni di Azure, il trigger di orchestrazione viene configurato tramite l'attributo .NET [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet).

Quando si scrivono funzioni dell'agente di orchestrazione in linguaggi di script (ad esempio JavaScript o C#), il trigger di orchestrazione viene definito dall'oggetto JSON seguente nella matrice `bindings` del file *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` is the name of the orchestration that clients must use when they want to start new instances of this orchestrator function. Questa proprietà è facoltativa. Se non specificato, viene usato il nome della funzione.

Internamente questa associazione di trigger esegue il polling di una serie di code nell'account di archiviazione predefinito per l'app per le funzioni. Tali code sono dettagli di implementazione interna dell'estensione, motivo per cui non sono configurate in modo esplicito nelle proprietà dell'associazione.

### <a name="trigger-behavior"></a>Comportamento di trigger

Di seguito vengono indicate alcune note relative al trigger di orchestrazione:

* **Thread singolo** - Un singolo thread del dispatcher viene usato per l'esecuzione di tutte le funzioni dell'agente di orchestrazione in un'unica istanza dell'host. Per questo motivo, è importante verificare che codice della funzione dell'agente di orchestrazione sia efficiente e non esegua alcuna operazioni di I/O. È anche importante garantire che il thread non esegua alcuna operazione asincrona, ad eccezione di quando è in attesa su tipi di attività specifiche di Funzioni permanenti.
* **Gestione di messaggi non elaborabili** - I trigger di orchestrazione non forniscono supporto per i messaggi non elaborabili.
* **Visibilità dei messaggi** - I messaggi dei trigger di orchestrazione vengono rimossi dalla coda e resi invisibili per un periodo di tempo configurabile. La visibilità di questi messaggi viene rinnovata automaticamente fino a quando l'app per le funzioni è in esecuzione e integra.
* **Valori restituiti** - I valori restituiti vengono serializzati in JSON e salvati in modo permanente nella tabella di cronologia di orchestrazione nell'archiviazione tabelle di Azure. Su tali valori l'associazione client di orchestrazione può eseguire query, come descritto in seguito.

> [!WARNING]
> Le funzioni dell'agente di orchestrazione non devono mai usare alcuna associazione di input o output diversa da quella di trigger di orchestrazione. In caso contrario, possono verificarsi problemi con l'estensione di attività permanenti in quanto tali associazioni potrebbero non rispettare le regole di thread singolo e di I/O. If you'd like to use other bindings, add them to an Activity function called from your Orchestrator function.

> [!WARNING]
> Le funzioni di orchestrazione di JavaScript non devono mai essere dichiarate `async`.

### <a name="trigger-usage-net"></a>Utilizzo dei trigger (.NET)

L'associazione di trigger di orchestrazione supporta sia input che output. Di seguito sono illustrati alcuni aspetti da conoscere sulla gestione di input e output.

* **inputs** - .NET orchestration functions support only `DurableOrchestrationContext` as a parameter type. La deserializzazione degli input direttamente nella firma della funzione non è supportata. Code must use the `GetInput<T>` (.NET) or `getInput` (JavaScript) method to fetch orchestrator function inputs. I dati di input devono essere tipi serializzabili in JSON.
* **output** -I trigger di orchestrazione supportano valori di output in modo analogo ai valori di input. Il valore restituito della funzione viene usato per assegnare il valore di output e deve essere serializzabile in JSON. Se una funzione .NET restituisce `Task` o `void`, un valore `null` viene salvato come output.

### <a name="trigger-sample"></a>Esempio di trigger

The following example code shows what the simplest "Hello World" orchestrator function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> The `context` object in JavaScript does not represent the DurableOrchestrationContext, but the [function context as a whole](../functions-reference-node.md#context-object). È possibile accedere ai metodi di orchestrazione tramite la proprietà `df` dell'oggetto `context`.

> [!NOTE]
> Gli orchestratori JavaScript devono usare `return`. La libreria `durable-functions` provvede alla chiamata del metodo `context.done`.

La maggior parte delle funzioni dell'agente di orchestrazione chiama funzioni di attività, pertanto viene riportato un esempio "Hello World" che illustra come chiamare una funzione di attività:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Activity trigger

The activity trigger enables you to author functions that are called by orchestrator functions, known as [activity functions](durable-functions-types-features-overview.md#activity-functions).

If you're using Visual Studio, the activity trigger is configured using the `ActivityTriggerAttribute` .NET attribute.

Se si usa VS Code o il portale di Azure per lo sviluppo, il trigger di attività viene definito per l'oggetto JSON seguente nella matrice `bindings` di *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` è il nome dell'attività. This value is the name that orchestrator functions use to invoke this activity function. Questa proprietà è facoltativa. Se non specificato, viene usato il nome della funzione.

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

* **inputs** - .NET activity functions natively use `DurableActivityContext` as a parameter type. In alternativa, una funzione di attività può essere dichiarata con qualsiasi tipo di parametro serializzabile in JSON. When you use `DurableActivityContext`, you can call `GetInput<T>` to fetch and deserialize the activity function input.
* **output** - Le funzioni di attività supportano i valori di output in modo analogo a quelli di input. Il valore restituito della funzione viene usato per assegnare il valore di output e deve essere serializzabile in JSON. Se una funzione .NET restituisce `Task` o `void`, un valore `null` viene salvato come output.
* **metadati** - Le funzioni di attività .NET possono eseguire l'associazione a un parametro `string instanceId` per ottenere l'ID istanza dell'orchestrazione padre.

### <a name="trigger-sample"></a>Esempio di trigger

The following example code shows what a simple "Hello World" activity function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableActivityContext` instead of `IDurableActivityContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

Il tipo di parametro predefinito per l'associazione .NET `ActivityTriggerAttribute` è `IDurableActivityContext`. I trigger di attività .NET, tuttavia, supportano anche l'associazione diretta a tipi serializzabili in JSON, ad esempio tipi primitivi, e di conseguenza la stessa funzione potrebbe essere semplificata nel modo seguente:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

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


### <a name="using-input-and-output-bindings"></a>Using input and output bindings

You can use regular input and output bindings in addition to the activity trigger binding. For example, you can take the input to your activity binding, and send a message to an EventHub using the EventHub output binding:

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

The orchestration client binding enables you to write functions that interact with orchestrator functions. These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions). È possibile ad esempio agire su istanze di orchestrazione nei modi seguenti:

* Avviare le istanze.
* Eseguire query sullo stato delle istanze.
* Terminare le istanze.
* Inviare eventi alle istanze mentre sono in esecuzione.
* Ripulire la cronologia di istanza.

If you're using Visual Studio, you can bind to the orchestration client by using the `OrchestrationClientAttribute` .NET attribute for Durable Functions 1.0. Starting in the Durable Functions 2.0, you can bind to the orchestration client by using the `DurableClientAttribute` .NET attribute.

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the orchestration trigger is defined by the following JSON object in the `bindings` array of *function.json*:

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

In .NET functions, you typically bind to `IDurableOrchestrationClient`, which gives you full access to all orchestration client APIs supported by Durable Functions. In the older Durable Functions 2.x releases, you instead bind to the `DurableOrchestrationClient` class. In JavaScript, the same APIs are exposed by the object returned from `getClient`. Le API per l'oggetto client includono:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternatively, .NET functions can bind to `IAsyncCollector<T>` where `T` is `StartOrchestrationArgs` or `JObject`.

For more information on these operations, see the `IDurableOrchestrationClient` API documentation.

### <a name="client-sample-visual-studio-development"></a>Esempio di client (sviluppo di Visual Studio)

Di seguito viene riportata una funzione di esempio attivata dalla coda che avvia un'orchestrazione "HelloWorld".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> The previous JSON is for Durable Functions 2.x. For Durable Functions 1.x, you must use `orchestrationClient` instead of the `durableClient` as the trigger type. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

Di seguito vengono riportati esempi specifici del linguaggio che avviano nuove istanze della funzione dell'agente di orchestrazione.

#### <a name="c-script-sample"></a>C# Script Sample

The following sample shows how to use the durable orchestration client binding to start a new function instance from a queue-triggered C# function:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

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

## <a name="entity-trigger"></a>Entity trigger

Entity triggers allow you to author [entity functions](durable-functions-entities.md). This trigger supports processing events for a specific entity instance.

When you use the Visual Studio tools for Azure Functions, the entity trigger is configured using the `EntityTriggerAttribute` .NET attribute.

> [!NOTE]
> Entity triggers are available starting in Durable Functions 2.x.

Internamente questa associazione di trigger esegue il polling di una serie di code nell'account di archiviazione predefinito per l'app per le funzioni. Tali code sono dettagli di implementazione interna dell'estensione, motivo per cui non sono configurate in modo esplicito nelle proprietà dell'associazione.

### <a name="trigger-behavior"></a>Comportamento di trigger

Here are some notes about the entity trigger:

* **Single-threaded**: A single dispatcher thread is used to process operations for a particular entity. If multiple messages are sent to a single entity concurrently, the operations will be processed one-at-a-time.
* **Poison-message handling** - There is no poison message support in entity triggers.
* **Message visibility** - Entity trigger messages are dequeued and kept invisible for a configurable duration. La visibilità di questi messaggi viene rinnovata automaticamente fino a quando l'app per le funzioni è in esecuzione e integra.
* **Return values** - Entity functions do not support return values. There are specific APIs that can be used to save state or pass values back to orchestrations.

Any state changes made to an entity during its execution will be automatically persisted after execution has completed.

### <a name="trigger-usage-net"></a>Utilizzo dei trigger (.NET)

Every entity function has a parameter type of `IDurableEntityContext`, which has the following members:

* **EntityName**: the name of the currently executing entity.
* **EntityKey**: the key of the currently executing entity.
* **EntityId**: the ID of the currently executing entity.
* **OperationName**: the name of the current operation.
* **HasState**: whether the entity exists, that is, has some state. 
* **GetState\<TState>()** : gets the current state of the entity. If it does not already exist, it is created and initialized to `default<TState>`. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **GetState\<TState>(initfunction)** : gets the current state of the entity. If it does not already exist, it is created by calling the provided `initfunction` parameter. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **SetState(arg)** : creates or updates the state of the entity. The `arg` parameter must be a JSON-serializeable object or primitive.
* **DeleteState()** : deletes the state of the entity. 
* **GetInput\<TInput>()** : gets the input for the current operation. The `TInput` type parameter must be a primitive or JSON-serializeable type.
* **Return(arg)** : returns a value to the orchestration that called the operation. The `arg` parameter must be a primitive or JSON-serializeable object.
* **SignalEntity(EntityId, operation, input)** : sends a one-way message to an entity. The `operation` parameter must be a non-null string, and the `input` parameter must be a primitive or JSON-serializeable object.
* **CreateNewOrchestration(orchestratorFunctionName, input)** : starts a new orchestration. The `input` parameter must be a primitive or JSON-serializeable object.

The `IDurableEntityContext` object passed to the entity function can be accessed using the `Entity.Current` async-local property. This approach is convenient when using the class-based programming model.

### <a name="trigger-sample-c-function-based-syntax"></a>Trigger sample (C# function-based syntax)

Il codice seguente è un esempio di semplice entità *Counter* implementata come funzione durevole. Questa funzione definisce tre operazioni, `add`, `reset` e `get`, ciascuna delle quali opera con uno stato integer.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Per altre informazioni sulla sintassi basata su funzione e su come usarla, vedere [Sintassi basata su funzione](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Trigger sample (C# class-based syntax)

L'esempio seguente usa un'implementazione equivalente dell'entità `Counter` mediante classi e metodi.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

Lo stato di questa entità è un oggetto di tipo `Counter`, che contiene un campo in cui è archiviato il valore corrente del contatore. Per renderlo persistente nell'archiviazione, questo oggetto viene serializzato e deserializzato dalla libreria [Json.NET](https://www.newtonsoft.com/json). 

Per altre informazioni sulla sintassi basata su classe e su come usarla, vedere [Definizione di classi di entità](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Il metodo del punto di ingresso della funzione con l'attributo `[FunctionName]` *deve* essere dichiarato come `static` quando si usano le classi di entità. I metodi del punto di ingresso non statici possono comportare l'inizializzazione di più oggetti e potenzialmente altri comportamenti non definiti.

Entity classes have special mechanisms for interacting with bindings and .NET dependency injection. For more information, see [Entity construction](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Trigger sample (JavaScript)

The following code is an example of a simple *Counter* entity implemented as a durable function written in JavaScript. Questa funzione definisce tre operazioni, `add`, `reset` e `get`, ciascuna delle quali opera con uno stato integer.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Le entità durevoli sono disponibili in JavaScript a partire dalla versione **1.3.0** del pacchetto npm `durable-functions`.

## <a name="entity-client"></a>Entity client

The entity client binding enables you to asynchronously trigger [entity functions](#entity-trigger). These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions).

If you're using Visual Studio, you can bind to the entity client by using the `DurableClientAttribute` .NET attribute.

> [!NOTE]
> The `[DurableClientAttribute]` can also be used to bind to the [orchestration client](#orchestration-client).

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the entity trigger is defined by the following JSON object in the `bindings` array of *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` - Usato in scenari in cui più app per le funzioni condividono lo stesso account di archiviazione, ma devono essere isolate tra loro. Se non specificato, viene usato il valore predefinito da `host.json`. This value must match the value used by the target entity functions.
* `connectionName` - Il nome di un'impostazione dell'app che contiene la stringa di connessione di un account di archiviazione. The storage account represented by this connection string must be the same one used by the target entity functions. Se non è specificato, viene usata la stringa di connessione dell'account di archiviazione predefinito dell'app per le funzioni.

> [!NOTE]
> In most cases, we recommend that you omit the optional properties and rely on the default behavior.

### <a name="entity-client-usage"></a>Entity client usage

In .NET functions, you typically bind to `IDurableEntityClient`, which gives you full access to all client APIs supported by Durable Entities. You can also bind to the `IDurableOrchestrationClient` interface, which provides access to client APIs for both entities and orchestrations. Le API per l'oggetto client includono:

* **ReadEntityStateAsync\<T>** : reads the state of an entity. It returns a response that indicates whether the target entity exists, and if so, what its state is.
* **SignalEntityAsync**: sends a one-way message to an entity, and waits for it to be enqueued.

There is no need to create the target entity before sending a signal - the entity state can be created from within the entity function that handles the signal.

> [!NOTE]
> It's important to understand that the "signals" sent from the client are simply enqueued, to be processed asynchronously at a later time. In particular, the `SignalEntityAsync` usually returns before the entity even starts the operation, and it is not possible to get back the return value or observe exceptions. If stronger guarantees are required (e.g. for workflows), *orchestrator functions* should be used, which can wait for entity operations to complete, and can process return values and observe exceptions.

### <a name="example-client-signals-entity-directly---c"></a>Example: client signals entity directly - C#

Here is an example queue-triggered function that invokes a "Counter" entity.

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

### <a name="example-client-signals-entity-via-interface---c"></a>Example: client signals entity via interface - C#

Where possible, we recommend [accessing entities through interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) because it provides more type checking. For example, suppose the `Counter` entity mentioned earlier implemented an `ICounter` interface, defined as follows:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Client code can then use `SignalEntityAsync<ICounter>` to generate a type-safe proxy:

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

The `proxy` parameter is a dynamically generated instance of `ICounter`, which internally translates the call to `Add` into the equivalent (untyped) call to `SignalEntityAsync`.

> [!NOTE]
> The `SignalEntityAsync` APIs represent one-way operations. If an entity interfaces returns `Task<T>`, the value of the `T` parameter will always be null or `default`.

In particular, it does not make sense to signal the `Get` operation, as no value is returned. Instead, clients can use either `ReadStateAsync` to access the counter state directly, or can start an orchestrator function that calls the `Get` operation.

### <a name="example-client-signals-entity---javascript"></a>Example: client signals entity - JavaScript

Here is an example queue-triggered function that signals a "Counter" entity in JavaScript.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
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

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Le entità durevoli sono disponibili in JavaScript a partire dalla versione **1.3.0** del pacchetto npm `durable-functions`.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>impostazioni host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Built-in HTTP API reference for instance management](durable-functions-http-api.md)
