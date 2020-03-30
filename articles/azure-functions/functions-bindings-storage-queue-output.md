---
title: Azure Queue storage output binding for Azure Functions
description: Informazioni su come creare messaggi di archiviazione della coda di Azure in Funzioni di Azure.Learn to create Azure Queue storage messages in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277336"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Azure Queue storage output bindings for Azure Functions

Funzioni di Azure possono creare nuovi messaggi di archiviazione della coda di Azure impostando un'associazione di output.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](./functions-bindings-storage-queue.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C #](#tab/csharp)

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che crea un messaggio nella coda per ogni richiesta HTTP ricevuta.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente illustra un'associazione di trigger HTTP in un file *function.json* e il codice [script C# (file con estensione csx)](functions-reference-csharp.md) che usa l'associazione. La funzione crea un elemento della coda con un payload dell'oggetto **CustomQueueMessage** per ogni richiesta HTTP ricevuta.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C# che crea un singolo messaggio nella coda:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

È possibile inviare più messaggi contemporaneamente usando `ICollector` o il parametro `IAsyncCollector`. Ecco il codice script C# che invia più messaggi, uno con i dati della richiesta HTTP e uno con i valori hardcoded:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

L'esempio seguente illustra un'associazione di trigger HTTP in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione crea un elemento della coda per ogni richiesta HTTP ricevuta.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

È possibile inviare più messaggi contemporaneamente definendo un array di messaggio per l'associazione di output `myQueueItem`. Il codice JavaScript seguente invia due messaggi della coda con valori hardcoded per ogni richiesta HTTP ricevuta.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrato come restituire valori singoli e multipli alle code di archiviazione. La configurazione necessaria per *function.json* è la stessa in entrambi i casi.

Un binding della coda di archiviazione *type* viene definito `queue`in *function.json* dove type è impostato su .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Per impostare un singolo messaggio nella coda, `set` si passa un singolo valore al metodo .

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Per creare più messaggi nella coda, dichiarare un parametro come tipo di elenco appropriato e `set` passare una matrice di valori (che corrispondono al tipo di elenco) al metodo.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 Nell'esempio seguente viene illustrata una funzione Java che crea un messaggio della coda per quando viene attivato da una richiesta HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime) usare l'annotazione `@QueueOutput` per i parametri il cui valore viene scritto nell'archiviazione code.  Il tipo di `OutputBinding<T>`parametro deve essere , dove `T` è qualsiasi tipo Java nativo di un POJO.

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C #](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

L'attributo si applica a un parametro `out` o al valore restituito della funzione. Il costruttore dell'attributo accetta il nome della coda, come illustrato nell'esempio seguente:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

È possibile impostare la proprietà `Connection` per specificare l'account di archiviazione da usare, come illustrato nell'esempio seguente:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [Esempio di output](#example).

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere Trigger - attributi.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script di C.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L'annotazione `QueueOutput` consente di scrivere un messaggio come output di una funzione. Nell'esempio seguente viene illustrata una funzione attivata da HTTP che crea un messaggio della coda.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Proprietà    | Descrizione |
|-------------|-----------------------------|
|`name`       | Dichiara il nome del parametro nella firma della funzione. Quando la funzione viene attivata, il valore di questo parametro ha il contenuto del messaggio della coda. |
|`queueName`  | Dichiara il nome della coda nell'account di archiviazione. |
|`connection` | Punta alla stringa di connessione dell'account di archiviazione. |

Il parametro `QueueOutput` associato all'annotazione è tipizzato come istanza Di OutputBinding T.The parameter associated with the annotation is typed as an [OutputBinding\<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) instance.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente vengono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Queue` .

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `queue`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `out`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**name** | n/d | Nome della variabile che rappresenta la coda nel codice della funzione. Impostare su `$return` per fare riferimento al valore restituito della funzione.|
|**Queuename** |**QueueName** | Nome della coda. |
|**Connessione** | **Connessione** |Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se `connection` si imposta "MyStorage", il runtime di Funzioni cerca un'impostazione dell'app denominata "MyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Scrivere un singolo messaggio della coda `out T paramName`utilizzando un parametro del metodo, ad esempio . È possibile usare il tipo restituito del metodo anziché un parametro `out` e `T` può essere uno dei seguenti tipi:

* Un oggetto serializzabile come JSON
* `string`
* `byte[]`
* [Messaggio relativo a CloudQueueMessage] 

Se si prova a eseguire l'associazione a `CloudQueueMessage` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

In C# e negli script C# scrivere più messaggi nella coda usando uno dei seguenti tipi: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Scrivere un singolo messaggio della coda `out T paramName`utilizzando un parametro del metodo, ad esempio . Il `paramName` è il valore `name` specificato nella proprietà di *function.json*. È possibile usare il tipo restituito del metodo anziché un parametro `out` e `T` può essere uno dei seguenti tipi:

* Un oggetto serializzabile come JSON
* `string`
* `byte[]`
* [Messaggio relativo a CloudQueueMessage] 

Se si prova a eseguire l'associazione a `CloudQueueMessage` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

In C# e negli script C# scrivere più messaggi nella coda usando uno dei seguenti tipi: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[Javascript](#tab/javascript)

L'elemento della coda `context.bindings.<NAME>` `<NAME>` di output è disponibile tramite il punto in cui corrisponde al nome definito in *function.json*. È possibile usare una stringa o un oggetto serializzabile in JSON per il payload dell'elemento della coda.

# <a name="python"></a>[Python](#tab/python)

Sono disponibili due opzioni per l'output di un messaggio dell'Hub eventi da una funzione:There are two options for outputting an Event Hub message from a function:

- **Valore restituito**: `name` impostare la `$return`proprietà in *function.json su* . Con questa configurazione, il valore restituito della funzione viene mantenuto come messaggio di archiviazione della coda.

- **Imperative**: Passare un valore al metodo [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) del parametro dichiarato come tipo [Out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Il valore `set` passato a viene salvato in modo permanente come messaggio di archiviazione della coda.

# <a name="java"></a>[Java](#tab/java)

Sono disponibili due opzioni per l'output di un messaggio dell'Hub eventi da una funzione tramite l'annotazione [QueueOutput:There](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) are two options for output an Event Hub message from a function by using the QueueOutput annotation:

- **Valore restituito**: applicando l'annotazione alla funzione stessa, il valore restituito della funzione viene mantenuto come messaggio dell'Hub eventi.

- **Imperative**: per impostare in modo esplicito il valore [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)del `T` messaggio, applicare l'annotazione a un parametro specifico del tipo , dove è un POJO o qualsiasi tipo Java nativo. Con questa configurazione, il `setValue` passaggio di un valore al metodo rende persistente il valore come messaggio dell'Hub eventi.

---

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione |  Riferimento |
|---|---|
| Coda | [Codici di errore della coda](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Table, Queue | [Codici di errore di archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Risoluzione dei problemi](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>impostazioni host.json

In questa sezione vengono descritte le impostazioni di configurazione globali disponibili per questa associazione nelle versioni 2.x e successive. Il file host.json di esempio riportato di seguito contiene solo le impostazioni della versione 2.x e per questa associazione. Per altre informazioni sulle impostazioni di configurazione globali nelle versioni 2.x e successive, vedere informazioni di riferimento su host.json per Funzioni di Azure.For more information about global configuration settings in versions 2.x and beyond, see [host.json reference for Azure Functions](functions-host-json.md).

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|maxPollingInterval|00:00:01|L'intervallo massimo tra i polling di coda. Il valore minimo è 00:00:00.100 (100 ms) e incrementa fino a 00:01:00 (1 min).  In 1.x the data type is milliseconds, and in 2.x and higher it is a TimeSpan.|
|visibilityTimeout|00:00:00|L'intervallo di tempo tra i tentativi se l'elaborazione di un messaggio ha esito negativo. |
|batchSize|16|Il numero di messaggi in coda che il runtime di Funzioni recupera simultaneamente e di processi in parallelo. Quando il numero elaborato viene ridotto a `newBatchThreshold`, il runtime ottiene un altro batch e inizia l'elaborazione dei messaggi. Di conseguenza, il numero massimo di messaggi simultanei elaborati per ogni funzione è `batchSize` più `newBatchThreshold`. Questo limite si applica separatamente a ogni funzione attivata dalla coda. <br><br>Se si vuole evitare l'esecuzione in parallelo per i messaggi ricevuti su una coda, è possibile impostare `batchSize` su 1. Tuttavia, questa impostazione elimina solo la concorrenza se l'app per le funzioni viene eseguita su una singola macchina virtuale (VM). Se l'app per le funzioni scala orizzontalmente più macchine virtuali, ogni macchina virtuale potrebbe eseguire un'istanza di ogni funzione attivata dalla coda.<br><br>Il valore massimo per `batchSize` è 32. |
|maxDequeueCount|5|Il numero di volte per provare l'elaborazione di un messaggio prima di essere spostato nella coda non elaborabile.|
|newBatchThreshold|batchSize/2|Ogni volta che il numero di messaggi elaborati simultaneamente viene ridotto a questo numero, il runtime recupera un altro batch.|

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione come modifiche ai dati di archiviazione della coda (Trigger)Run a function as queue storage data changes (Trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[Messaggio relativo a CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
