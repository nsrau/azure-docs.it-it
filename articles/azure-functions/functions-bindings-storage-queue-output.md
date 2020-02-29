---
title: Binding di output di archiviazione code di Azure per funzioni di Azure
description: Informazioni su come creare messaggi di archiviazione code di Azure in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164645"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Associazioni di output di archiviazione code di Azure per funzioni di Azure

Funzioni di Azure può creare nuovi messaggi di archiviazione code di Azure impostando un'associazione di output.

Per informazioni sui dettagli di configurazione e configurazione, vedere la [Panoramica](./functions-bindings-storage-queue.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Script](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Nell'esempio seguente viene illustrato come restituire valori singoli e multipli nelle code di archiviazione. La configurazione necessaria per *Function. JSON* è identica in entrambi i casi.

Un binding della coda di archiviazione è definito in *Function. JSON,* dove *Type* è impostato su `queue`.

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

Per impostare un singolo messaggio nella coda, passare un singolo valore al metodo `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Per creare più messaggi nella coda, dichiarare un parametro come tipo di elenco appropriato e passare una matrice di valori (che corrispondono al tipo di elenco) al metodo `set`.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 Nell'esempio seguente viene illustrata una funzione Java che consente di creare un messaggio in coda per quando viene attivato da una richiesta HTTP.

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

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime) usare l'annotazione `@QueueOutput` per i parametri il cui valore viene scritto nell'archiviazione code.  Il tipo di parametro deve essere `OutputBinding<T>`, dove `T` è qualsiasi tipo Java nativo di un POJO.

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

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

Per un esempio completo, vedere [esempio di output](#example).

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere Trigger - attributi.

# <a name="c-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L'annotazione `QueueOutput` consente di scrivere un messaggio come output di una funzione. Nell'esempio seguente viene illustrata una funzione attivata tramite HTTP che consente di creare un messaggio in coda.

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
|`name`       | Dichiara il nome del parametro nella firma della funzione. Quando la funzione viene attivata, il valore di questo parametro presenta il contenuto del messaggio della coda. |
|`queueName`  | Dichiara il nome della coda nell'account di archiviazione. |
|`connection` | Punta alla stringa di connessione dell'account di archiviazione. |

Il parametro associato all'annotazione `QueueOutput` viene tipizzato come [output\<t\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) istanza.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Queue`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `queue`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `out`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta la coda nel codice della funzione. Impostare su `$return` per fare riferimento al valore restituito della funzione.|
|**queueName** |**QueueName** | Nome della coda. |
|**connection** | **Connection** |Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Se, ad esempio, si imposta `connection` su "Storage", il runtime di funzioni Cerca un'impostazione dell'app denominata "" Storage ". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Scrivere un singolo messaggio della coda usando un parametro di metodo, ad esempio `out T paramName`. È possibile usare il tipo restituito del metodo anziché un parametro `out` e `T` può essere uno dei seguenti tipi:

* Un oggetto serializzabile come JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se si prova a eseguire l'associazione a `CloudQueueMessage` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

In C# e negli script C# scrivere più messaggi nella coda usando uno dei seguenti tipi: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C#Script](#tab/csharp-script)

Scrivere un singolo messaggio della coda usando un parametro di metodo, ad esempio `out T paramName`. Il `paramName` è il valore specificato nella proprietà `name` di *Function. JSON*. È possibile usare il tipo restituito del metodo anziché un parametro `out` e `T` può essere uno dei seguenti tipi:

* Un oggetto serializzabile come JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se si prova a eseguire l'associazione a `CloudQueueMessage` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

In C# e negli script C# scrivere più messaggi nella coda usando uno dei seguenti tipi: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'elemento della coda di output è disponibile tramite `context.bindings.<NAME>` dove `<NAME>` corrisponde al nome definito in *Function. JSON*. È possibile usare una stringa o un oggetto serializzabile in JSON per il payload dell'elemento della coda.

# <a name="python"></a>[Python](#tab/python)

Sono disponibili due opzioni per l'output di un messaggio di hub eventi da una funzione:

- **Valore restituito**: impostare la proprietà `name` in *Function. JSON* su `$return`. Con questa configurazione, il valore restituito della funzione viene reso permanente come messaggio di archiviazione di Accodamento.

- **Imperativo**: passare un valore al metodo [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) del parametro dichiarato come tipo [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . Il valore passato a `set` viene reso permanente come messaggio di archiviazione di Accodamento.

# <a name="java"></a>[Java](#tab/java)

Sono disponibili due opzioni per l'output di un messaggio di hub eventi da una funzione tramite l'annotazione [QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) :

- **Valore restituito**: applicando l'annotazione alla funzione stessa, il valore restituito della funzione viene reso persistente come messaggio dell'hub eventi.

- **Imperativo**: per impostare in modo esplicito il valore del messaggio, applicare l'annotazione a un parametro specifico del tipo [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), dove `T` è un POJO o qualsiasi tipo Java nativo. Con questa configurazione, il passaggio di un valore al metodo `setValue` rende permanente il valore come messaggio dell'hub eventi.

---

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione |  Riferimento |
|---|---|
| Coda | [Codici di errore della coda](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Table, Queue | [Codici di errore di archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Risoluzione dei problemi](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>impostazioni host.json

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione nelle versioni 2. x e successive. Il file host. JSON di esempio seguente contiene solo le impostazioni della versione 2. x + per questa associazione. Per ulteriori informazioni sulle impostazioni di configurazione globali nelle versioni 2. x e successive, vedere informazioni di [riferimento su host. JSON per funzioni di Azure](functions-host-json.md).

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
|maxPollingInterval|00:00:01|L'intervallo massimo tra i polling di coda. Il valore minimo è 00:00:00.100 (100 ms) e incrementa fino a 00:01:00 (1 min).  In 1. x il tipo di dati è millisecondi e in 2. x e versioni successive si tratta di un intervallo di tempo.|
|visibilityTimeout|00:00:00|L'intervallo di tempo tra i tentativi se l'elaborazione di un messaggio ha esito negativo. |
|batchSize|16|Il numero di messaggi in coda che il runtime di Funzioni recupera simultaneamente e di processi in parallelo. Quando il numero elaborato viene ridotto a `newBatchThreshold`, il runtime ottiene un altro batch e inizia l'elaborazione dei messaggi. Di conseguenza, il numero massimo di messaggi simultanei elaborati per ogni funzione è `batchSize` più `newBatchThreshold`. Questo limite si applica separatamente a ogni funzione attivata dalla coda. <br><br>Se si vuole evitare l'esecuzione in parallelo per i messaggi ricevuti su una coda, è possibile impostare `batchSize` su 1. Tuttavia, questa impostazione elimina solo la concorrenza se l'app per le funzioni viene eseguita su una singola macchina virtuale (VM). Se l'app per le funzioni scala orizzontalmente più macchine virtuali, ogni macchina virtuale potrebbe eseguire un'istanza di ogni funzione attivata dalla coda.<br><br>Il valore massimo per `batchSize` è 32. |
|maxDequeueCount|5|Il numero di volte per provare l'elaborazione di un messaggio prima di essere spostato nella coda non elaborabile.|
|newBatchThreshold|batchSize/2|Ogni volta che il numero di messaggi elaborati simultaneamente viene ridotto a questo numero, il runtime recupera un altro batch.|

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione come modifiche ai dati di archiviazione di Accodamento (trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
