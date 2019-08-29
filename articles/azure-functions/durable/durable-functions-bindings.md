---
title: Associazioni per Funzioni permanenti - Azure
description: Come usare trigger e associazioni per l'estensione Durable Functions di Funzioni di Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fbd645ef9f5e687e71ce110fc84b8342e31defed
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087544"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associazioni per Funzioni permanenti (Funzioni di Azure)

L'estensione [Funzioni permanenti](durable-functions-overview.md) introduce due nuove associazioni di trigger che controllano l'esecuzione delle funzioni dell'agente di orchestrazione e di attività. L'estensione include inoltre un'associazione di output che funge da client per il runtime di Funzioni permanenti.

## <a name="orchestration-triggers"></a>Trigger di orchestrazione

Il trigger di orchestrazione consente di creare funzioni dell'agente di orchestrazione permanenti. Il trigger supporta l'avvio di nuove istanze di funzioni dell'agente di orchestrazione e la ripresa di istanze di funzioni analoghe esistenti in attesa di un'attività.

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
> Le funzioni dell'agente di orchestrazione non devono mai usare alcuna associazione di input o output diversa da quella di trigger di orchestrazione. In caso contrario, possono verificarsi problemi con l'estensione di attività permanenti in quanto tali associazioni potrebbero non rispettare le regole di thread singolo e di I/O.

> [!WARNING]
> Le funzioni di orchestrazione di JavaScript non devono mai essere dichiarate `async`.

### <a name="trigger-usage-net"></a>Utilizzo dei trigger (.NET)

L'associazione di trigger di orchestrazione supporta sia input che output. Di seguito sono illustrati alcuni aspetti da conoscere sulla gestione di input e output.

* **input** - Le funzioni di orchestrazione .NET supportano solo [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) come tipo di parametro. La deserializzazione degli input direttamente nella firma della funzione non è supportata. Il codice deve usare il metodo [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) o `getInput` (JavaScript) per recuperare gli input della funzione dell'agente di orchestrazione. I dati di input devono essere tipi serializzabili in JSON.
* **output** -I trigger di orchestrazione supportano valori di output in modo analogo ai valori di input. Il valore restituito della funzione viene usato per assegnare il valore di output e deve essere serializzabile in JSON. Se una funzione .NET restituisce `Task` o `void`, un valore `null` viene salvato come output.

### <a name="trigger-sample"></a>Esempio di trigger

Di seguito è riportato un esempio dell'aspetto della più semplice funzione dell'agente di orchestrazione "Hello World":

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
> L'oggetto `context` in JavaScript non rappresenta DurableOrchestrationContext, ma il [contesto funzione nel suo complesso.](../functions-reference-node.md#context-object). È possibile accedere ai metodi di orchestrazione tramite la proprietà `df` dell'oggetto `context`.

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

## <a name="activity-triggers"></a>Trigger di attività

Il trigger di attività consente di creare le funzioni chiamate dalle funzioni dell'agente di orchestrazione.

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

* `activity` è il nome dell'attività. Questo è il valore che le funzioni dell'agente di orchestrazione usano per richiamare questa funzione di attività. Questa proprietà è facoltativa. Se non specificato, viene usato il nome della funzione.

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

Di seguito è riportato un esempio dell'aspetto di una semplice funzione di attività "Hello World":

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

### <a name="passing-multiple-parameters"></a>Passaggio di più parametri

Non è possibile passare più parametri direttamente a una funzione di attività. In questo caso è consigliabile passare una matrice di oggetti o usare oggetti [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) in .NET.

L'esempio seguente usa le nuove funzionalità di [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) aggiunte con [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>Client di orchestrazione

L'associazione del client di orchestrazione consente di scrivere funzioni che interagiscono con le funzioni dell'agente di orchestrazione. È possibile ad esempio agire su istanze di orchestrazione nei modi seguenti:

* Avviare le istanze.
* Eseguire query sullo stato delle istanze.
* Terminare le istanze.
* Inviare eventi alle istanze mentre sono in esecuzione.
* Ripulire la cronologia di istanza.

Se si usa Visual Studio, è possibile eseguire l'associazione al client di orchestrazione tramite l'attributo .NET [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html).

Se per lo sviluppo si usano linguaggi di scripting, ad esempio file con estensione *CSX* o *JS*, il trigger di orchestrazione viene definito dall'oggetto JSON seguente nella matrice `bindings` di *function.json*:

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

Nelle funzioni .NET in genere se esegue l'associazione a `DurableOrchestrationClient` in modo da disporre dell'acceso completo a tutte le API client supportate da Durable Functions. In JavaScript, le stesse API sono esposte dall'oggetto `DurableOrchestrationClient` restituito da `getClient`. Le API per l'oggetto client includono:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (attualmente solo .NET)

In alternativa, le funzioni .NET possono eseguire l'associazione a `IAsyncCollector<T>`, dove `T` è [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) o `JObject`.

Per altre informazioni su queste operazioni, vedere la documentazione dell'API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html).

> [!WARNING]
> Quando si sviluppa in locale con JavaScript, è necessario impostare la variabile di ambiente `WEBSITE_HOSTNAME` su `localhost:<port>`, ad esempio `localhost:7071` per usare i metodi su `DurableOrchestrationClient`. Per altre informazioni su questo requisito, vedere il [problema GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

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

<a name="host-json"></a>

## <a name="hostjson-settings"></a>impostazioni host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sul funzionamento dei checkpoint e della riesecuzione](durable-functions-checkpointing-and-replay.md)