---
title: Binding di output di archiviazione BLOB di Azure per funzioni di Azure
description: Informazioni su come fornire dati di associazione di output dell'archiviazione BLOB di Azure a una funzione di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 56d8078347b5de775b30c8db2c9412598070046c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285367"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Binding di output di archiviazione BLOB di Azure per funzioni di Azure

L'associazione di output consente di modificare ed eliminare i dati di archiviazione BLOB in una funzione di Azure.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](./functions-bindings-storage-blob.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente è una [funzione in C#](functions-dotnet-class-library.md) che usa un trigger di BLOB di input e due associazioni di BLOB di output. La funzione viene attivata dalla creazione di un BLOB dell'immagine nel contenitore *sample-images*. Crea copie di piccole e medie dimensioni del BLOB dell'immagine.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

<!--Same example for input and output. -->

L'esempio seguente mostra associazioni di input e di output di BLOB in un file *function.json* e codice [script C# (.csx)](functions-reference-csharp.md) che usa le associazioni. La funzione crea una copia di un BLOB di testo. La funzione viene attivata da un messaggio della coda che contiene il nome del BLOB da copiare. Il nuovo BLOB è denominato *{originalblobname}-Copy*.

Nel file *function.json* viene usata la proprietà dei metadati `queueTrigger` per specificare il nome del BLOB nelle proprietà `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

L'esempio seguente mostra associazioni di input e di output di BLOB in un file *function.json* e codice [JavaScript](functions-reference-node.md) che usa le associazioni. La funzione crea una copia di un BLOB. La funzione viene attivata da un messaggio della coda che contiene il nome del BLOB da copiare. Il nuovo BLOB è denominato *{originalblobname}-Copy*.

Nel file *function.json* viene usata la proprietà dei metadati `queueTrigger` per specificare il nome del BLOB nelle proprietà `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

L'esempio seguente mostra associazioni di input e di output di BLOB in un file *function.json* e codice [Python](functions-reference-python.md) che usa le associazioni. La funzione crea una copia di un BLOB. La funzione viene attivata da un messaggio della coda che contiene il nome del BLOB da copiare. Il nuovo BLOB è denominato *{originalblobname}-Copy*.

Nel file *function.json* viene usata la proprietà dei metadati `queueTrigger` per specificare il nome del BLOB nelle proprietà `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Questa sezione contiene gli esempi seguenti:

* [Trigger HTTP, uso di OutputBinding](#http-trigger-using-outputbinding-java)
* [Trigger di coda, uso del valore restituito della funzione](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Trigger HTTP, uso di OutputBinding (Java)

 L'esempio seguente mostra una funzione Java che usa l'annotazione `HttpTrigger` per ricevere un parametro che contiene il nome di un file in un contenitore di archiviazione BLOB. L'annotazione `BlobInput` legge quindi il file e passa il relativo contenuto alla funzione come `byte[]`. L'annotazione `BlobOutput` viene associata a `OutputBinding outputItem`, che viene quindi usato dalla funzione per scrivere il contenuto del BLOB di input nel contenitore di archiviazione configurato.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Trigger di coda, uso del valore restituito della funzione (Java)

 L'esempio seguente mostra una funzione Java che usa l'annotazione `QueueTrigger` per ricevere un messaggio che contiene il nome di un file in un contenitore di archiviazione BLOB. L'annotazione `BlobInput` legge quindi il file e passa il relativo contenuto alla funzione come `byte[]`. L'annotazione `BlobOutput` viene associata al valore restituito dalla funzione, che viene quindi usato dal runtime per scrivere il contenuto del BLOB di input nel contenitore di archiviazione configurato.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime) usare `@BlobOutput` l'annotazione per i parametri di funzione il cui valore viene scritto in un oggetto nell’archiviazione BLOB.  Il tipo di parametro deve essere `OutputBinding<T>`, dove T corrisponde a un qualsiasi tipo Java nativo o a un oggetto POJO.

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Il costruttore dell'attributo accetta il percorso del BLOB e un parametro `FileAccess` che indica la lettura o la scrittura, come illustrato nell'esempio seguente:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

È possibile impostare la proprietà `Connection` per specificare l'account di archiviazione da usare, come illustrato nell'esempio seguente:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L' `@BlobOutput` attributo consente di accedere al BLOB che ha attivato la funzione. Se si usa una matrice di byte con l'attributo, impostare `dataType` su `binary` . Per informazioni dettagliate, vedere l' [esempio di output](#example) .

---

Per un esempio completo, vedere [esempio di output](#example).

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere [Trigger - attributi](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Blob`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blob`. |
|**direction** | n/d | Deve essere impostato su `out` per un'associazione di output. Le eccezioni sono indicate nella sezione [usage](#usage). |
|**nome** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione.  Impostare su `$return` per fare riferimento al valore restituito della funzione.|
|**path** |**BlobPath** | Percorso del contenitore BLOB. |
|**connection** |**Connection**| Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione solo BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/d | **Accesso** | Indica se eseguire la lettura o la scrittura. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In JavaScript si accede a dati del BLOB di input usando `context.bindings.<name from function.json>`.

# <a name="python"></a>[Python](#tab/python)

È possibile dichiarare i parametri della funzione come i seguenti tipi per scrivere nell'archivio BLOB:

* Stringhe come `func.Out(str)`
* Flussi come `func.Out(func.InputStream)`

Per informazioni dettagliate, vedere l' [esempio di output](#example) .

# <a name="java"></a>[Java](#tab/java)

L' `@BlobOutput` attributo consente di accedere al BLOB che ha attivato la funzione. Se si usa una matrice di byte con l'attributo, impostare `dataType` su `binary` . Per informazioni dettagliate, vedere l' [esempio di output](#example) .

---

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Binding |  Informazioni di riferimento |
|---|---|
| BLOB | [Codici di errore BLOB](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Table, Queue |  [Codici di errore di archiviazione](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Risoluzione dei problemi](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione quando i dati dell'archivio BLOB cambiano](./functions-bindings-storage-blob-trigger.md)
- [Leggere i dati di archiviazione BLOB quando viene eseguita una funzione](./functions-bindings-storage-blob-input.md)
