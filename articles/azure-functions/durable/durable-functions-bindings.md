---
title: Associazioni per Funzioni permanenti - Azure
description: Come usare trigger e associazioni per l'estensione Durable Functions di Funzioni di Azure.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278220"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associazioni per Funzioni permanenti (Funzioni di Azure)

L'estensione [Funzioni permanenti](durable-functions-overview.md) introduce due nuove associazioni di trigger che controllano l'esecuzione delle funzioni dell'agente di orchestrazione e di attività. L'estensione include inoltre un'associazione di output che funge da client per il runtime di Funzioni permanenti.

## <a name="orchestration-trigger"></a>Trigger di orchestrazione

Il trigger di orchestrazione consente di creare [funzioni dell'agente di orchestrazione durevoli.](durable-functions-types-features-overview.md#orchestrator-functions) Il trigger supporta l'avvio di nuove istanze di funzioni dell'agente di orchestrazione e la ripresa di istanze di funzioni analoghe esistenti in attesa di un'attività.

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

* `orchestration`è il nome dell'orchestrazione che i client devono utilizzare quando desiderano avviare nuove istanze di questa funzione dell'agente di orchestrazione. Questa proprietà è facoltativa. Se non specificato, viene usato il nome della funzione.

Internamente questa associazione di trigger esegue il polling di una serie di code nell'account di archiviazione predefinito per l'app per le funzioni. Tali code sono dettagli di implementazione interna dell'estensione, motivo per cui non sono configurate in modo esplicito nelle proprietà dell'associazione.

### <a name="trigger-behavior"></a>Comportamento di trigger

Di seguito vengono indicate alcune note relative al trigger di orchestrazione:

* **Thread singolo** - Un singolo thread del dispatcher viene usato per l'esecuzione di tutte le funzioni dell'agente di orchestrazione in un'unica istanza dell'host. Per questo motivo, è importante verificare che codice della funzione dell'agente di orchestrazione sia efficiente e non esegua alcuna operazioni di I/O. È anche importante garantire che il thread non esegua alcuna operazione asincrona, ad eccezione di quando è in attesa su tipi di attività specifiche di Funzioni permanenti.
* **Gestione di messaggi non elaborabili** - I trigger di orchestrazione non forniscono supporto per i messaggi non elaborabili.
* **Visibilità dei messaggi** - I messaggi dei trigger di orchestrazione vengono rimossi dalla coda e resi invisibili per un periodo di tempo configurabile. La visibilità di questi messaggi viene rinnovata automaticamente fino a quando l'app per le funzioni è in esecuzione e integra.
* **Valori restituiti** - I valori restituiti vengono serializzati in JSON e salvati in modo permanente nella tabella di cronologia di orchestrazione nell'archiviazione tabelle di Azure. Su tali valori l'associazione client di orchestrazione può eseguire query, come descritto in seguito.

> [!WARNING]
> Le funzioni dell'agente di orchestrazione non devono mai usare alcuna associazione di input o output diversa da quella di trigger di orchestrazione. In caso contrario, possono verificarsi problemi con l'estensione di attività permanenti in quanto tali associazioni potrebbero non rispettare le regole di thread singolo e di I/O. Se si desidera utilizzare altre associazioni, aggiungerle a una funzione di attività chiamata dalla funzione Orchestrator.If you're like to use other bindings, add them to an Activity function called from your Orchestrator function.

> [!WARNING]
> Le funzioni di orchestrazione di JavaScript non devono mai essere dichiarate `async`.

### <a name="trigger-usage-net"></a>Utilizzo dei trigger (.NET)

L'associazione di trigger di orchestrazione supporta sia input che output. Di seguito sono illustrati alcuni aspetti da conoscere sulla gestione di input e output.

* **input:** le funzioni di orchestrazione `DurableOrchestrationContext` .NET supportano solo come tipo di parametro.inputs - .NET orchestration functions support only as a parameter type. La deserializzazione degli input direttamente nella firma della funzione non è supportata. Il codice `GetInput<T>` deve usare il `getInput` metodo (.NET) o (JavaScript) per recuperare gli input della funzione dell'agente di orchestrazione. I dati di input devono essere tipi serializzabili in JSON.
* **output** -I trigger di orchestrazione supportano valori di output in modo analogo ai valori di input. Il valore restituito della funzione viene usato per assegnare il valore di output e deve essere serializzabile in JSON. Se una funzione .NET restituisce `Task` o `void`, un valore `null` viene salvato come output.

### <a name="trigger-sample"></a>Esempio di trigger

Il codice di esempio seguente mostra l'aspetto della più semplice funzione dell'agente di orchestrazione "Hello World":

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
> Il codice precedente è per Funzioni durevoli 2.x. Per funzioni durevoli 1.x, è necessario utilizzare `DurableOrchestrationContext` al posto di `IDurableOrchestrationContext`. Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [funzioni permanenti.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> L'oggetto `context` in JavaScript non rappresenta DurableOrchestrationContext, ma il contesto della [funzione nel suo complesso.](../functions-reference-node.md#context-object) È possibile accedere ai metodi di orchestrazione tramite la proprietà `df` dell'oggetto `context`.

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
> Il codice precedente è per Funzioni durevoli 2.x. Per funzioni durevoli 1.x, è necessario utilizzare `DurableOrchestrationContext` al posto di `IDurableOrchestrationContext`. Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Trigger attività

Il trigger di attività consente di creare funzioni chiamate dalle funzioni dell'agente di orchestrazione, note come funzioni di [attività](durable-functions-types-features-overview.md#activity-functions).

Se si utilizza Visual Studio, il trigger `ActivityTriggerAttribute` di attività viene configurato utilizzando l'attributo .NET.

Se si usa VS Code o il portale di Azure per lo sviluppo, il trigger di attività viene definito per l'oggetto JSON seguente nella matrice `bindings` di *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` è il nome dell'attività. Questo valore è il nome utilizzato dalle funzioni dell'agente di orchestrazione per richiamare questa funzione di attività. Questa proprietà è facoltativa. Se non specificato, viene usato il nome della funzione.

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

* **inputs:** le funzioni di `DurableActivityContext` attività .NET vengono usate in modo nativo come tipo di parametro.inputs - .NET activity functions natively use as a parameter type. In alternativa, una funzione di attività può essere dichiarata con qualsiasi tipo di parametro serializzabile in JSON. Quando si `DurableActivityContext`utilizza , `GetInput<T>` è possibile chiamare per recuperare e deserializzare l'input della funzione di attività.
* **output** - Le funzioni di attività supportano i valori di output in modo analogo a quelli di input. Il valore restituito della funzione viene usato per assegnare il valore di output e deve essere serializzabile in JSON. Se una funzione .NET restituisce `Task` o `void`, un valore `null` viene salvato come output.
* **metadati** - Le funzioni di attività .NET possono eseguire l'associazione a un parametro `string instanceId` per ottenere l'ID istanza dell'orchestrazione padre.

### <a name="trigger-sample"></a>Esempio di trigger

Il codice di esempio seguente mostra l'aspetto di una semplice funzione di attività "Hello World":

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
> Il codice precedente è per Funzioni durevoli 2.x. Per funzioni durevoli 1.x, è necessario utilizzare `DurableActivityContext` al posto di `IDurableActivityContext`. Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [funzioni permanenti.](durable-functions-versions.md)

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


### <a name="using-input-and-output-bindings"></a>Utilizzo di associazioni di input e outputUsing input and output bindings

È possibile usare associazioni di input e output regolari oltre all'associazione del trigger dell'attività. Ad esempio, è possibile accettare l'input per l'associazione di attività e inviare un messaggio a un EventHub usando l'associazione di output EventHub:For example, you can take the input to your activity binding, and send a message to an EventHub using the EventHub output binding:

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

L'associazione client dell'orchestrazione consente di scrivere funzioni che interagiscono con le funzioni dell'agente di orchestrazione. Queste funzioni sono talvolta definite [funzioni client](durable-functions-types-features-overview.md#client-functions). È possibile ad esempio agire su istanze di orchestrazione nei modi seguenti:

* Avviare le istanze.
* Eseguire query sullo stato delle istanze.
* Terminare le istanze.
* Inviare eventi alle istanze mentre sono in esecuzione.
* Ripulire la cronologia di istanza.

Se si usa Visual Studio, è possibile eseguire l'associazione al client di orchestrazione utilizzando l'attributo `OrchestrationClientAttribute` .NET per Funzioni durevoli 1.0. A partire da Funzioni durevoli 2.0, è possibile eseguire `DurableClientAttribute` l'associazione al client di orchestrazione utilizzando l'attributo .NET.

Se si utilizzano linguaggi di script (ad esempio, file *con estensione csx* o *js)* per lo `bindings` sviluppo, il trigger di orchestrazione è definito dal seguente oggetto JSON nella matrice di *function.json*:

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

Nelle funzioni .NET in `IDurableOrchestrationClient`genere si esegue l'associazione a , che consente l'accesso completo a tutte le API client di orchestrazione supportate da funzioni permanenti. Nelle versioni precedenti di Funzioni durevoli 2.x, è invece possibile eseguire l'associazione alla `DurableOrchestrationClient` classe. In JavaScript, le stesse API vengono esposte `getClient`dall'oggetto restituito da . Le API per l'oggetto client includono:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

In alternativa, le funzioni `IAsyncCollector<T>` .NET possono essere associate a where `T` is `StartOrchestrationArgs` o `JObject`.

Per altre informazioni su queste `IDurableOrchestrationClient` operazioni, vedere la documentazione dell'API.

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
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [funzioni permanenti.](durable-functions-versions.md)

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
> Il codice JSON precedente è per funzioni durevoli 2.x.The previous JSON is for Durable Functions 2.x. Per funzioni durevoli 1.x, è necessario utilizzare `orchestrationClient` al posto del `durableClient` tipo di trigger. Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [funzioni permanenti.](durable-functions-versions.md)

Di seguito vengono riportati esempi specifici del linguaggio che avviano nuove istanze della funzione dell'agente di orchestrazione.

#### <a name="c-script-sample"></a>Esempio di script di C

Nell'esempio seguente viene illustrato come utilizzare l'associazione client di orchestrazione durevole per avviare una nuova istanza della funzione da una funzione di C.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Il codice precedente è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, `DurableOrchestrationClient` è necessario `IDurableOrchestrationClient`utilizzare il tipo di parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [funzioni permanenti.](durable-functions-versions.md)

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

I trigger di entità consentono di creare funzioni di [entità](durable-functions-entities.md). Questo trigger supporta l'elaborazione di eventi per un'istanza di entità specifica.

Quando si usano gli strumenti di Visual Studio per `EntityTriggerAttribute` Funzioni di Azure, il trigger di entità viene configurato usando l'attributo .NET.

> [!NOTE]
> I trigger di entità sono disponibili a partire da Funzioni durevoli 2.x.

Internamente questa associazione di trigger esegue il polling di una serie di code nell'account di archiviazione predefinito per l'app per le funzioni. Tali code sono dettagli di implementazione interna dell'estensione, motivo per cui non sono configurate in modo esplicito nelle proprietà dell'associazione.

### <a name="trigger-behavior"></a>Comportamento di trigger

Ecco alcune note sul trigger dell'entità:

* **A thread singolo:** un singolo thread del dispatcher viene utilizzato per elaborare le operazioni per una determinata entità. Se più messaggi vengono inviati contemporaneamente a una singola entità, le operazioni verranno elaborate una alla volta.
* **Gestione dei messaggi non** elaborabili: nei trigger di entità non è disponibile alcun supporto per i messaggi non elaborabili.
* **Visibilità dei messaggi:** i messaggi del trigger dell'entità vengono rimossi dalla coda e mantenuti invisibili per una durata configurabile. La visibilità di questi messaggi viene rinnovata automaticamente fino a quando l'app per le funzioni è in esecuzione e integra.
* **Valori restituiti:** le funzioni di entità non supportano i valori restituiti. Esistono API specifiche che possono essere usate per salvare lo stato o passare i valori alle orchestrazioni.

Tutte le modifiche di stato apportate a un'entità durante l'esecuzione verranno automaticamente rese persistenti dopo il completamento dell'esecuzione.

### <a name="trigger-usage-net"></a>Utilizzo dei trigger (.NET)

Ogni funzione di entità `IDurableEntityContext`ha un tipo di parametro di , che dispone dei seguenti membri:

* **EntityName:** il nome dell'entità attualmente in esecuzione.
* **EntityKey:** la chiave dell'entità attualmente in esecuzione.
* **EntityId:** l'ID dell'entità attualmente in esecuzione.
* **NomeOperazione**: il nome dell'operazione corrente.
* **HasState**: indica se l'entità esiste, ovvero ha uno stato. 
* **GetState\<TState>()**: ottiene lo stato corrente dell'entità. Se non esiste già, viene creato e `default<TState>`inizializzato su . Il `TState` parametro deve essere un tipo primitivo o serializzabile in JSON. 
* **GetState\<TState>(initfunction):** ottiene lo stato corrente dell'entità. Se non esiste già, viene creato chiamando `initfunction` il parametro fornito. Il `TState` parametro deve essere un tipo primitivo o serializzabile in JSON. 
* **SetState(arg)**: crea o aggiorna lo stato dell'entità. Il `arg` parametro deve essere un oggetto serializzabile JSON o primitivo.
* **DeleteState()**: elimina lo stato dell'entità. 
* **GetInput\<TInput>()**: ottiene l'input per l'operazione corrente. Il `TInput` parametro di tipo deve essere un tipo primitivo o serializzabile in JSON.
* **Return(arg)**: restituisce un valore all'orchestrazione che ha chiamato l'operazione. Il `arg` parametro deve essere un oggetto primitivo o serializzabile in JSON.
* **SignalEntity(EntityId, scheduledTimeUtc, operazione, input):** invia un messaggio unidirezionale a un'entità. Il `operation` parametro deve essere una stringa `scheduledTimeUtc` non null, l'facoltativo deve essere `input` un datetime UTC in cui richiamare l'operazione e il parametro deve essere un oggetto primitivo o serializzabile in JSON.
* **CreateNewOrchestration(orchestratorFunctionName, input):** avvia una nuova orchestrazione. Il `input` parametro deve essere un oggetto primitivo o serializzabile in JSON.

È `IDurableEntityContext` possibile accedere all'oggetto passato `Entity.Current` alla funzione entity utilizzando la proprietà async-local. Questo approccio è utile quando si utilizza il modello di programmazione basato su classi.

### <a name="trigger-sample-c-function-based-syntax"></a>Esempio di trigger (sintassi basata su funzioni C

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

Per ulteriori informazioni sulla sintassi basata su funzioni e su come utilizzarla, vedere [Sintassi basata su](durable-functions-dotnet-entities.md#function-based-syntax)funzioni .

### <a name="trigger-sample-c-class-based-syntax"></a>Esempio di trigger (sintassi basata su classi C

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
> Il metodo del punto di ingresso della funzione con l'attributo `[FunctionName]`*deve* essere dichiarato come `static` quando si usano le classi di entità. I metodi del punto di ingresso non statici possono comportare l'inizializzazione di più oggetti e potenzialmente altri comportamenti non definiti.

Le classi di entità dispongono di meccanismi speciali per l'interazione con le associazioni e l'inserimento delle dipendenze .NET.Entity classes have special mechanisms for interacting with bindings and .NET dependency injection. Per ulteriori informazioni, consultate [Costruzione di entità.](durable-functions-dotnet-entities.md#entity-construction)

### <a name="trigger-sample-javascript"></a>Esempio di trigger (JavaScript)Trigger sample (JavaScript)

Il codice seguente è un esempio di una semplice entità Counter implementata come funzione durevole scritta in JavaScript.The following code is an example of a simple *Counter* entity implemented as a durable function written in JavaScript. Questa funzione definisce tre operazioni, `add`, `reset` e `get`, ciascuna delle quali opera con uno stato integer.

**function.json (funzione.json)**
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

## <a name="entity-client"></a>Client entità

L'associazione client di entità consente di attivare in modo asincrono [le funzioni di entità.](#entity-trigger) Queste funzioni sono talvolta definite [funzioni client](durable-functions-types-features-overview.md#client-functions).

Se si usa Visual Studio, è possibile eseguire l'associazione al client di entità usando l'attributo `DurableClientAttribute` .NET.

> [!NOTE]
> L'oggetto `[DurableClientAttribute]` può essere utilizzato anche per l'associazione al client di [orchestrazione.](#orchestration-client)

Se si utilizzano linguaggi di script (ad esempio, file *con estensione csx* o *js)* per `bindings` lo sviluppo, il trigger di entità viene definito dal seguente oggetto JSON nella matrice di *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` - Usato in scenari in cui più app per le funzioni condividono lo stesso account di archiviazione, ma devono essere isolate tra loro. Se non specificato, viene usato il valore predefinito da `host.json`. Questo valore deve corrispondere al valore utilizzato dalle funzioni dell'entità di destinazione.
* `connectionName` - Il nome di un'impostazione dell'app che contiene la stringa di connessione di un account di archiviazione. L'account di archiviazione rappresentato da questa stringa di connessione deve essere lo stesso usato dalle funzioni dell'entità di destinazione. Se non è specificato, viene usata la stringa di connessione dell'account di archiviazione predefinito dell'app per le funzioni.

> [!NOTE]
> Nella maggior parte dei casi, è consigliabile omettere le proprietà facoltative e basarsi sul comportamento predefinito.

### <a name="entity-client-usage"></a>Utilizzo del client di entità

Nelle funzioni .NET in `IDurableEntityClient`genere si esegue l'associazione a , che consente l'accesso completo a tutte le API client supportate da entità permanenti. È anche possibile `IDurableOrchestrationClient` eseguire l'associazione all'interfaccia, che fornisce l'accesso alle API client sia per le entità che per le orchestrazioni. Le API per l'oggetto client includono:

* **ReadEntityStateAsync\<T>**: legge lo stato di un'entità. Restituisce una risposta che indica se l'entità di destinazione esiste e, in caso affermativo, qual è il relativo stato.
* **SignalEntityAsync:** invia un messaggio unidirezionale a un'entità e attende che venga accodato.
* **ListEntitiesAsync:** esegue query per lo stato di più entità. Le entità possono essere interrogate in base *al nome* e *all'ora dell'ultima operazione.*

Non è necessario creare l'entità di destinazione prima di inviare un segnale: lo stato dell'entità può essere creato dall'interno della funzione di entità che gestisce il segnale.

> [!NOTE]
> È importante comprendere che i "segnali" inviati dal client vengono semplicemente accodati, per essere elaborati in modo asincrono in un secondo momento. In particolare, `SignalEntityAsync` il solito restituisce prima che l'entità ancora avvia l'operazione e non è possibile ottenere il valore restituito o osservare le eccezioni. Se sono necessarie garanzie più forti (ad esempio per i flussi di lavoro), è necessario usare le funzioni dell'agente di *orchestrazione,* che possono attendere il completamento delle operazioni dell'entità e elaborare i valori restituiti e osservare le eccezioni.

### <a name="example-client-signals-entity-directly---c"></a>Esempio: entità segnali client direttamente - CExample: client signals entity directly - C #

Di seguito è riportato un esempio di funzione attivata dalla coda che richiama un'entità "Contatore".

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

### <a name="example-client-signals-entity-via-interface---c"></a>Esempio: entità dei segnali client tramite interfaccia - CExample: client signals entity via interface - C #

Se possibile, è [consigliabile accedere alle entità tramite interfacce](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) perché fornisce un maggiore controllo del tipo. Si supponga, `Counter` ad esempio, `ICounter` che l'entità menzionata in precedenza implementi un'interfaccia, definita come segue:For example, suppose the entity mentioned earlier implemented an interface, defined as follows:

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

Il codice client `SignalEntityAsync<ICounter>` può quindi utilizzare per generare un proxy indipendente dai tipi:Client code can then use to generate a type-safe proxy:

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

Il `proxy` parametro è un'istanza generata dinamicamente `ICounter` `Add` di , che converte internamente la chiamata nella chiamata equivalente (non tipizzata) a `SignalEntityAsync`.

> [!NOTE]
> Le `SignalEntityAsync` API rappresentano operazioni unidirezionali. Se un'interfaccia `Task<T>`di entità restituisce , il valore del `T` parametro sarà sempre null o `default`.

In particolare, non ha senso `Get` segnalare l'operazione, in quanto non viene restituito alcun valore. Al contrario, `ReadStateAsync` i client possono utilizzare direttamente per accedere allo stato `Get` del contatore oppure possono avviare una funzione dell'agente di orchestrazione che chiama l'operazione.

### <a name="example-client-signals-entity---javascript"></a>Esempio: entità dei segnali client - JavaScriptExample: client signals entity - JavaScript

Ecco un esempio di funzione attivata dalla coda che segnala un'entità "Contatore" in JavaScript.Here is an example queue-triggered function that signals a "Counter" entity in JavaScript.

**function.json (funzione.json)**
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
> [Informazioni di riferimento sulle API HTTP predefinite per la gestione delle istanzeBuilt-in HTTP API reference for instance management](durable-functions-http-api.md)
