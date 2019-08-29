---
title: Espressioni e modelli di associazioni di funzioni di Azure
description: Informazioni su come creare diverse espressioni di associazione di funzioni di Azure basate su modelli comuni.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: db6f4f938b1555091dc51e310d4d31f96f93200c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097346"
---
# <a name="azure-functions-binding-expression-patterns"></a>Modelli di espressione di binding di funzioni di Azure

Una delle funzionalità più potenti dei [trigger e delle associazioni](./functions-triggers-bindings.md) è rappresentata dalle *espressioni di associazione*. Nel file *function.json* e nel codice e nei parametri della funzione è possibile usare espressioni risolvibili in valori di varie origini.

Quasi tutte le espressioni sono identificate tramite la disposizione del testo tra parentesi graffe. Ad esempio, in una funzione di trigger della coda `{queueTrigger}` viene risolto nel testo dei messaggi della coda. Se la proprietà `path` dell'associazione di output di un BLOB è `container/{queueTrigger}` e la funzione viene attivata da un messaggio della coda `HelloWorld`, viene creato un BLOB denominato `HelloWorld`.

Tipi di espressioni di associazione

* [Impostazioni dell'app](#binding-expressions---app-settings)
* [Nome file del trigger](#trigger-file-name)
* [Metadati dei trigger](#trigger-metadata)
* [Payload JSON](#json-payloads)
* [Nuovo GUID](#create-guids)
* [Data e ora correnti](#current-time)

## <a name="binding-expressions---app-settings"></a>Espressioni di associazione - impostazioni dell'app

Come procedura consigliata, i segreti e le stringhe di connessione devono essere gestiti tramite le impostazioni dell'app, invece dei file di configurazione. Questo limita l'accesso ai segreti e rende sicuro archiviare i file, ad esempio *function.json*, nei repository pubblici di controllo del codice sorgente.

Le impostazioni dell'app sono utili anche ogni volta che si desidera modificare la configurazione in base all'ambiente. Ad esempio, in un ambiente di test, si potrebbe voler monitorare un contenitore di archiviazione BLOB o di coda diverso.

Le espressioni di associazione gestite tramite le impostazioni dell'app vengono identificate in modo diverso rispetto ad altre espressioni di associazione. Vengono infatti racchiuse tra segni di percentuale anziché tra parentesi graffe. Ad esempio, se è il percorso dell'associazione di output del BLOB è `%Environment%/newblob.txt` e il valore dell'impostazione dell'app `Environment` è `Development`, verrà creato un BLOB nel contenitore `Development`.

Quando una funzione è in esecuzione a livello locale, i valori delle impostazioni dell'app vengono ricavati dal file *local.settings.json*.

Si noti che la proprietà `connection` dei trigger e delle associazioni è un caso speciale e risolve automaticamente i valori come impostazioni dell'app senza segni di percentuale. 

L'esempio seguente è un trigger di archiviazione code di Azure che usa un'impostazione dell'app `%input-queue-name%` per definire la coda di trigger.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

È possibile usare lo stesso approccio nelle librerie di classi:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Nome file del trigger

La proprietà `path` di un trigger BLOB può essere un modello che consente di fare riferimento al nome del BLOB di attivazione in altre associazioni e in altro codice della funzione. Il modello può anche includere criteri di filtro che specificano quali BLOB può attivare una chiamata di funzione.

Ad esempio, nell'associazione del trigger BLOB seguente il modello `path` è `sample-images/{filename}` e crea un'espressione di associazione denominata `filename`:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

L'espressione `filename` può quindi essere usata in un'associazione di output per specificare il nome del BLOB da creare:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Il codice della funzione ha accesso a questo stesso valore usando `filename` come nome di parametro:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

La stessa possibilità di usare criteri ed espressioni di associazione si applica agli attributi nelle librerie di classi. Nell'esempio seguente i parametri del costruttore di attributo corrispondono ai valori di `path` nei precedenti esempi di *function.json*: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

È anche possibile creare espressioni per parti del nome file, ad esempio per l'estensione. Per altre informazioni su come usare le espressioni e i modelli nella stringa del percorso del BLOB, vedere la [documentazione di riferimento sulle associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadati del trigger

Oltre al payload dei dati fornito da un trigger, ad esempio il contenuto del messaggio della coda che ha attivato una funzione, molti trigger forniscono altri valori di metadati. Questi valori possono essere usati come parametri di input in C# e F# o come proprietà nell'oggetto `context.bindings` in JavaScript. 

Un trigger di archiviazione code di Azure, ad esempio, supporta le proprietà seguenti:

* QueueTrigge: attivazione del contenuto del messaggio, se una stringa valida
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

I valori di questi metadati sono accessibili nelle proprietà del file *function.json*. Si supponga, ad esempio, di usare un trigger di accodamento e che il messaggio della coda contenga il nome di un BLOB di cui si vuole eseguire la lettura. Nel file *function.json* è possibile usare la proprietà dei metadati `queueTrigger` nella proprietà `path` del BLOB come mostrato nell'esempio seguente:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

I dettagli delle proprietà dei metadati per ogni trigger sono descritti nell'articolo di riferimento corrispondente. Per un esempio, vedere [metadati dei trigger per le code](functions-bindings-storage-queue.md#trigger---message-metadata). La documentazione è disponibile anche nella scheda **Integrazione** del portale nella sezione **Documentazione** sotto l'area di configurazione dell'associazione.  

## <a name="json-payloads"></a>Payload JSON

Quando il payload di un trigger è JSON, è possibile fare riferimento alle relative proprietà nella configurazione di altre associazioni nella stessa funzione e nel codice della funzione.

L'esempio seguente mostra il file *function.json* di una funzione webhook che riceve un nome di BLOB in JSON: `{"BlobName":"HelloWorld.txt"}`. Un'associazione di input del BLOB legge il BLOB e l'associazione di output HTTP restituisce i contenuti del BLOB nella risposta HTTP. Si noti che l'associazione di input del BLOB ottiene il nome del BLOB facendo riferimento direttamente alla proprietà `BlobName` (`"path": "strings/{BlobName}"`).

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Per il corretto funzionamento di questo meccanismo in C# e F#, è necessaria una classe che definisca i campi da deserializzare, come nell'esempio seguente:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

In JavaScript la deserializzazione JSON viene eseguita automaticamente.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Notazione con il punto

Se alcune proprietà nel payload JSON sono oggetti con proprietà, è possibile fare riferimento direttamente a tali oggetti tramite la notazione con il punto. Ad esempio, si supponga che il file JSON sia simile al seguente:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

È possibile fare riferimento direttamente a `FileName` come `BlobName.FileName`. Con questo formato JSON, la proprietà `path` dell'esempio precedente avrà l'aspetto seguente:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

In C# sono necessarie due classi:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Crea GUID

L'espressione di associazione `{rand-guid}` crea un GUID. Il percorso del BLOB seguente in un file `function.json` crea un BLOB con un nome, ad esempio *50710cb5 84b9 - 4d 87-9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Ora corrente

L'espressione di associazione `DateTime` viene risolta in `DateTime.UtcNow`. Il percorso del BLOB seguente in un file `function.json` crea un BLOB con un nome, ad esempio *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Associazione in fase di esecuzione

In C# e altri linguaggi .NET, è possibile usare criteri di associazione imperativa, rispetto alle associazioni dichiarative nel file *function.json* e negli attributi. L'associazione imperativa è utile quando i parametri di associazione devono essere calcolati in fase di runtime invece che in fase di progettazione. Per altre informazioni, vedere la [documentazione di riferimento per sviluppatori C#](functions-dotnet-class-library.md#binding-at-runtime) o la [documentazione di riferimento per sviluppatori di script C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Uso del valore restituito della funzione di Azure](./functions-bindings-return-value.md)
