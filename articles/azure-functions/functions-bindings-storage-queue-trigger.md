---
title: Azure Queue storage trigger for Azure Functions
description: Informazioni sull'esecuzione di una funzione di Azure quando cambiano i dati di archiviazione della coda di Azure.Learn to run an Azure Function as Azure Queue storage data changes.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277375"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure Queue storage trigger for Azure Functions

Il trigger di archiviazione della coda esegue una funzione quando i messaggi vengono aggiunti all'archiviazione delle code di Azure.The queue storage trigger runs a function as messages are added to Azure Queue storage.

## <a name="encoding"></a>Codifica

Le funzioni richiedono una stringa codificata *base64*. Le modifiche al tipo di codifica, per preparare i dati come una stringa con codifica *base64*, devono essere implementate nel servizio di chiamata.

## <a name="example"></a>Esempio

Usare il trigger della coda per avviare una funzione quando viene ricevuto un nuovo elemento in una coda. Il messaggio in coda viene fornito come input alla funzione.

# <a name="c"></a>[C #](#tab/csharp)

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che esegue il polling della coda `myqueue-items` e scrive un log a ogni elaborazione di un elemento della coda.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente illustra un'associazione di trigger della coda in un file *function.json* e il codice [script C# (file con estensione csx)](functions-reference-csharp.md) che usa l'associazione. La funzione esegue il polling della coda `myqueue-items` e scrive un log a ogni elaborazione di un elemento della coda.

Ecco il file *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

Nella sezione [usage](#usage) è illustrato `myQueueItem`, denominato dalla proprietà `name` in function.json.  Nella sezione [message metadata](#message-metadata) sono illustrate tutte le altre variabili indicate.

# <a name="javascript"></a>[Javascript](#tab/javascript)

L'esempio seguente illustra un'associazione di trigger della coda in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione esegue il polling della coda `myqueue-items` e scrive un log a ogni elaborazione di un elemento della coda.

Ecco il file *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

> [!NOTE]
> Il parametro Name viene visualizzato come `context.bindings.<name>` nel codice JavaScript che contiene il payload dell'elemento della coda. Il payload viene anche passato come secondo parametro alla funzione.

Ecco il codice JavaScript:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

Nella sezione [usage](#usage) è illustrato `myQueueItem`, denominato dalla proprietà `name` in function.json.  Nella sezione [message metadata](#message-metadata) sono illustrate tutte le altre variabili indicate.

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrato come leggere un messaggio della coda passato a una funzione tramite un trigger.

Un trigger della coda di archiviazione *type* viene definito `queueTrigger`in *function.json* dove type è impostato su .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Il codice * _ \_init_\_.py* dichiara `func.ServiceBusMessage`un parametro come , che consente di leggere il messaggio della coda nella funzione.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

Nell'esempio Java riportato di seguito viene illustrata una `myqueuename`funzione di trigger della coda di archiviazione, che registra il messaggio attivato inserito nella coda.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C #](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare i seguenti attributi per configurare un trigger di coda:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Il costruttore dell'attributo accetta il nome della coda da monitorare, come illustrato nell'esempio seguente:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  È possibile `Connection` impostare la proprietà per specificare l'impostazione dell'app che contiene la stringa di connessione dell'account di archiviazione da usare, come illustrato nell'esempio seguente:You can set the property to specify the app setting that contains the storage account connection string to use, as shown in the following example:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Per un esempio completo, vedere [esempio](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Offre un altro modo per specificare l'account di archiviazione da usare. Il costruttore accetta il nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. L'attributo può essere applicato a livello di parametro, metodo o classe. L'esempio seguente illustra il livello classe e il livello metodo:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

L'account di archiviazione da usare è determinato nell'ordine seguente:

* La proprietà `Connection` dell'attributo `QueueTrigger`.
* L'attributo `StorageAccount` applicato allo stesso parametro dell'attributo `QueueTrigger`.
* L'attributo `StorageAccount` applicato alla funzione.
* L'attributo `StorageAccount` applicato alla classe.
* L'impostazione dell'app "AzureWebJobsStorage".

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script di C.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L'annotazione `QueueTrigger` consente di accedere alla coda che attiva la funzione. L'esempio seguente rende il messaggio della `message` coda disponibile per la funzione tramite il parametro .

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Proprietà    | Descrizione |
|-------------|-----------------------------|
|`name`       | Dichiara il nome del parametro nella firma della funzione. Quando la funzione viene attivata, il valore di questo parametro ha il contenuto del messaggio della coda. |
|`queueName`  | Dichiara il nome della coda nell'account di archiviazione. |
|`connection` | Punta alla stringa di connessione dell'account di archiviazione. |

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente vengono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `QueueTrigger` .

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d| Il valore deve essere impostato su `queueTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction**| n/d | Solo nel file *function.json*. Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**name** | n/d |Nome della variabile che contiene il payload dell'elemento della coda nel codice della funzione.  |
|**Queuename** | **QueueName**| Nome della coda sulla quale eseguire il polling. |
|**Connessione** | **Connessione** |Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se `connection` si imposta "MyStorage", il runtime di Funzioni cerca un'impostazione dell'app denominata "MyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Accedere ai dati del messaggio `string paramName`utilizzando un parametro del metodo, ad esempio . È possibile definire associazioni con uno dei seguenti tipi:

* Object: il runtime di Funzioni deserializza un payload JSON in un'istanza di una classe arbitraria definita nel codice. 
* `string`
* `byte[]`
* [Messaggio relativo a CloudQueueMessage]

Se si prova a eseguire l'associazione a `CloudQueueMessage` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Accedere ai dati del messaggio `string paramName`utilizzando un parametro del metodo, ad esempio . Il `paramName` è il valore `name` specificato nella proprietà di *function.json*. È possibile definire associazioni con uno dei seguenti tipi:

* Object: il runtime di Funzioni deserializza un payload JSON in un'istanza di una classe arbitraria definita nel codice. 
* `string`
* `byte[]`
* [Messaggio relativo a CloudQueueMessage]

Se si prova a eseguire l'associazione a `CloudQueueMessage` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[Javascript](#tab/javascript)

Il payload dell'elemento `context.bindings.<NAME>` `<NAME>` della coda è disponibile tramite il punto in cui corrisponde al nome definito in *function.json*. Se il payload è JSON, il valore viene deserializzato in un oggetto.

# <a name="python"></a>[Python](#tab/python)

Accedere al messaggio della coda tramite il parametro tipizzato [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

L'annotazione [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) consente di accedere al messaggio della coda che ha attivato la funzione.

---

## <a name="message-metadata"></a>Metadati del messaggioMessage metadata

Il trigger della coda fornisce diverse [proprietà di metadati](./functions-bindings-expressions-patterns.md#trigger-metadata). Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Le proprietà sono membri della classe [CloudQueueMessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)

|Proprietà|Type|Descrizione|
|--------|----|-----------|
|`QueueTrigger`|`string`|Payload della coda, se si tratta di una stringa valida. Se il payload del messaggio `QueueTrigger` della coda è una stringa, ha lo stesso valore della variabile denominata dalla `name` proprietà in *function.json*.|
|`DequeueCount`|`int`|Il numero di volte in cui questo messaggio è stato rimosso dalla coda.|
|`ExpirationTime`|`DateTimeOffset`|Ora di scadenza del messaggio.|
|`Id`|`string`|ID del messaggio in coda.|
|`InsertionTime`|`DateTimeOffset`|L'ora in cui il messaggio è stato aggiunto alla coda.|
|`NextVisibleTime`|`DateTimeOffset`|Ora in cui il messaggio sarà visibile.|
|`PopReceipt`|`string`|Ricezione del messaggio.|

## <a name="poison-messages"></a>Messaggi non elaborabili

Quando una funzione di trigger della coda ha esito negativo, Funzioni di Azure ritenta l'esecuzione fino a cinque volte per un dato messaggio della coda, incluso il primo tentativo. Se tutti e cinque i tentativi hanno esito negativo, il runtime delle funzioni aggiunge un messaggio a una coda denominata * &lt;originalqueuename>-poison*. È possibile scrivere una funzione per elaborare i messaggi dalla coda non elaborabile archiviandoli o inviando una notifica della necessità di un intervento manuale.

Per gestire manualmente i messaggi non elaborabili, controllare [dequeueCount](#message-metadata) nel messaggio della coda.

## <a name="polling-algorithm"></a>Algoritmo di polling

Il trigger della coda implementa un algoritmo di backoff esponenziale casuale per ridurre l'effetto del polling delle code inattive sui costi delle transazioni di archiviazione.

L'algoritmo utilizza la logica seguente:The algorithm uses the following logic:

- Quando viene trovato un messaggio, il runtime attende due secondi e quindi verifica la presenza di un altro messaggio
- Quando non viene trovato alcun messaggio, attende circa quattro secondi prima di riprovare.
- Dopo alcuni tentativi non riusciti per ottenere un messaggio nella coda, il tempo di attesa continua ad aumentare finché non raggiunge il tempo massimo di attesa, che per impostazione predefinita è di un minuto.
- Il tempo di attesa massimo può essere configurato tramite la proprietà `maxPollingInterval` nel [file host.json](functions-host-json.md#queues).

Per lo sviluppo locale l'intervallo di polling massimo per impostazione predefinita è di due secondi.

Per quanto riguarda la fatturazione, il tempo trascorso dal polling da parte del runtime è "gratuito" e non conteggiato con il tuo account.

## <a name="concurrency"></a>Concorrenza

Quando sono in attesa più messaggi della coda, il trigger della coda recupera un batch di messaggi e richiama le istanze della funzione in modo simultaneo per l'elaborazione. Per impostazione predefinita, la dimensione del batch è pari a 16. Quando il numero elaborato scende a 8, il runtime ottiene un altro batch e inizia l'elaborazione dei messaggi. Di conseguenza, il numero massimo di messaggi simultanei elaborati per ogni funzione in una singola macchina virtuale è pari a 24. Questo limite si applica separatamente a ogni funzione attivata dalla coda in ogni macchina virtuale. Se il numero di istanze dell'app per le funzioni aumenta includendo più macchine virtuali, ogni macchina virtuale attenderà i trigger e proverà a eseguire le funzioni. Se ad esempio il numero di istanze di un'app per le funzioni aumento includendo 3 macchine virtuali, il numero massimo predefinito di istanze simultanee di una funzione attivata dalla coda è pari a 72.

La dimensione del batch e la soglia ottenere un nuovo batch possono essere configurate nel [file host.json](functions-host-json.md#queues). Se si desidera ridurre al minimo l'esecuzione parallela per le funzioni attivate dalla coda in un'app per le funzioni, è possibile impostare la dimensione del batch su 1. Questa impostazione elimina la concorrenza solo fino a quando l'app per le funzioni viene eseguita in una singola macchina virtuale. 

Il trigger della coda impedisce automaticamente a una funzione di elaborare un messaggio della coda più volte. Le funzioni non devono essere scritte per essere idempotenti.

## <a name="hostjson-properties"></a>proprietà host.json

Il file [host.json](functions-host-json.md#queues) contiene le impostazioni che controllano il comportamento del trigger della coda. Vedere la sezione [impostazioni host.json](functions-bindings-storage-queue-output.md#hostjson-settings) per informazioni dettagliate sulle impostazioni disponibili.

## <a name="next-steps"></a>Passaggi successivi

- [Scrivere i messaggi di archiviazione della coda (associazione di output)Write queue storage messages (Output binding)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[Messaggio relativo a CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
