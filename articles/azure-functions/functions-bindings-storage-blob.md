---
title: Associazioni dell'archiviazione BLOB di Funzioni di Azure | Microsoft Docs
description: Informazioni su come usare trigger e associazioni di Archiviazione di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b123578dbac48018f674f85ec923e4c6e65fb9f8
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-blob-storage-bindings"></a>Binding dell'archiviazione BLOB di Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e operare con le associazioni dell'archiviazione BLOB in Funzioni di Azure. Funzioni di Azure supporta trigger, associazioni di input e di output per l'archiviazione BLOB di Azure. Per le funzionalità disponibili in tutte le associazioni, vedere [Concetti di Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Non sono supportati [account di archiviazione solo BLOB](../storage/common/storage-create-storage-account.md#blob-storage-accounts). I trigger e le associazioni di archiviazione BLOB richiedono un account di archiviazione generico. 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>Trigger e associazioni do archiviazione BLOB

Con l'uso del trigger di archiviazione BLOB di Azure, il codice della funzione viene chiamato quando viene rilevato un BLOB nuovo o aggiornato. I contenuti del BLOB vengono dati come input alla funzione.

Definire un'archiviazione BLOB usando la scheda **Integrazione** nel portale Funzioni. Il portale crea la definizione seguente nella sezione **associazioni** di *function.json*:

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

Le associazioni di input e output BLOB vengono definite usando `blob` come tipo di associazione:

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* La proprietà `path` supporta le espressioni di associazione e parametri di filtro. Vedere [Modelli di nome](#pattern).
* La proprietà `connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione alla risorsa di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integrazione** configura questa impostazione app quando si sceglie un account di archiviazione.

> [!NOTE]
> Quando si usa un trigger di tipo BLOB in un piano a consumo, è possibile che si verifichi un ritardo di un massimo di 10 minuti per l'elaborazione di nuovi BLOB in caso di inattività di un'app per le funzioni. Quando l'app per le funzioni è in esecuzione, i BLOB vengono elaborati immediatamente. Per evitare questo ritardo iniziale, prendere in considerazione una delle opzioni seguenti:
> - Usare un piano di servizio app con l'opzione Always On abilitata.
> - Usare un altro meccanismo per attivare l'elaborazione dei BLOB, ad esempio un messaggio della coda che contiene il nome del BLOB. Per un esempio, vedere il [Queue trigger with blob input binding](#input-sample) (Trigger della coda con associazione di input del BLOB).

<a name="pattern"></a>

### <a name="name-patterns"></a>Modelli di nome
È possibile specificare un modello di nome del BLOB nella proprietà `path`, che può essere un'espressione di filtro o di associazione. Vedere [Modelli ed espressioni di associazione](functions-triggers-bindings.md#binding-expressions-and-patterns).

Ad esempio, per filtrare i BLOB che iniziano con la stringa "original", usare la definizione seguente. Questo percorso troverà un BLOB denominato *original-Blob1.txt* nel contenitore *input* e il valore della variabile `name` nel codice della funzione sarà `Blob1`.

```json
"path": "input/original-{name}",
```

Per associare il nome del file BLOB e l'estensione separatamente, usare due modelli. Anche questo percorso troverà un BLOB denominato *original-Blob1.txt* e i valori delle variabili `blobname` e `blobextension` nel codice della funzione saranno *original-Blob1* e *txt*.

```json
"path": "input/{blobname}.{blobextension}",
```

È possibile limitare il tipo di file dei BLOB usando un valore fisso per l'estensione del file. Ad esempio, per attivare solo i file con estensione PNG, usare il modello seguente:

```json
"path": "samples/{name}.png",
```

Le parentesi graffe sono caratteri speciali nei modelli di nome. Per specificare i nomi di BLOB con parentesi graffe nel nome, raddoppiare le parentesi graffe. L'esempio seguente troverà un BLOB denominato *{20140101}-soundfile.mp3* nel contenitore *images* e il valore della variabile `name` nel codice della funzione sarà *soundfile.mp3*. 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>Metadati del trigger

Il trigger del BLOB contiene diverse proprietà di metadati. Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Questi valori hanno la stessa semantica di [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

- **BlobTrigger**. Digitare `string`. Il percorso del BLOB trigger
- **Uri**. Digitare `System.Uri`. L'URI del BLOB per la posizione primaria.
- **Properties**. Digitare `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`. Le proprietà di sistema del BLOB.
- **Metadata**. Digitare `IDictionary<string,string>`. I metadati definiti dall'utente per il BLOB.

<a name="receipts"></a>

### <a name="blob-receipts"></a>Conferme di BLOB
Il runtime di Funzioni di Azure verifica che nessuna funzione trigger di BLOB venga chiamata più volte per lo stesso BLOB nuovo o aggiornato. Gestisce *conferme di BLOB* per determinare se una versione di BLOB specifica è stata elaborata.

Funzioni di Azure archivia le conferme di BLOB in un contenitore denominato *azure-webjobs-hosts* nell'account di archiviazione di Azure per l'app per le funzioni, specificato dall'impostazione app `AzureWebJobsStorage`. Una conferma di BLOB contiene le seguenti informazioni:

* La funzione attivata, ovvero "*&lt;nome dell'app per le funzioni>*.Functions.*&lt;nome della funzione>*", ad esempio: "MyFunctionApp.Functions.CopyBlob"
* Il nome del contenitore
* Il tipo di BLOB ("BlockBlob" o "PageBlob")
* Il nome del BLOB
* Il valore ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

Per forzare la rielaborazione di un BLOB è possibile eliminare manualmente la conferma del BLOB dal contenitore *azure-webjobs-hosts*.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Gestione di BLOB non elaborabili
Se una funzione di trigger del BLOB ha esito negativo per un determinato BLOB, per impostazione predefinita Funzioni di Azure ritenta l'esecuzione fino a 5 volte. 

Se tutti i 5 tentativi non riescono, Funzioni di Azure aggiunge un messaggio a una coda di archiviazione denominata *webjobs-blobtrigger-poison*. Il messaggio di coda per i BLOB non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato *&lt;nome dell'app per le funzioni>*.Functions.*&lt;nome della funzione>*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>Polling dei BLOB per contenitori di grandi dimensioni
Se il contenitore BLOB monitorato contiene più di 10.000 BLOB, il runtime di Funzioni analizza i file di log per cercare i BLOB nuovi o modificati. Questo processo non avviene in tempo reale. È possibile quindi che una funzione non venga attivata per diversi minuti o più dopo la creazione del BLOB. Per di più [i log di archiviazione vengono creati in base al principio del "massimo sforzo"](/rest/api/storageservices/About-Storage-Analytics-Logging). Non è garantito che tutti gli eventi vengano acquisiti. In alcune condizioni, l'acquisizione dei log può non riuscire. Se è necessaria un'elaborazione dei BLOB più veloce o affidabile, valutare la possibilità di creare un [messaggio della coda](../storage/queues/storage-dotnet-how-to-use-queues.md) quando si crea il BLOB. Usare quindi un [trigger di coda](functions-bindings-storage-queue.md) invece di un trigger di BLOB per elaborare il BLOB.

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>Uso di un trigger di BLOB e dell'associazione di input
Nelle funzioni .NET è possibile accedere ai dati del BLOB con un parametro del metodo, ad esempio `Stream paramName`. In questo caso, `paramName` è il valore specificato nella [configurazione del trigger](#trigger). Nelle funzioni Node.js si accede a dati del BLOB di input usando `context.bindings.<name>`.

In .NET è possibile eseguire l'associazione a uno tipo qualsiasi nell'elenco riportato di seguito. Se usati come associazione di input, alcuni di questi tipi richiedono una direzione di associazione `inout` in *function.json*. Questa direzione non è supportata dall'editor standard, pertanto è necessario usare l'editor avanzato.

* `TextReader`
* `Stream`
* `ICloudBlob`, è necessaria la direzione di associazione "inout"
* `CloudBlockBlob`, è necessaria la direzione di associazione "inout"
* `CloudPageBlob`, è necessaria la direzione di associazione "inout"
* `CloudAppendBlob`, è necessaria la direzione di associazione "inout"

Se sono previsti BLOB di testo, è anche possibile eseguire l'associazione a un tipo `string` .NET. È consigliabile solo se le dimensioni del BLOB sono ridotte, in quanto l'intero contenuto del BLOB viene caricato in memoria. In genere, è preferibile usare un tipo `Stream` o `CloudBlockBlob`.

## <a name="trigger-sample"></a>Esempio di trigger
Si supponga di avere function.json seguente, che definisce un trigger di archiviazione del BLOB:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

Vedere l'esempio specifico del linguaggio che registra i contenuti di ogni BLOB aggiunto al contenitore monitorato.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>Esempi di trigger BLOB in C# #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Esempio di trigger in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>Uso dell'associazione di output nel BLOB

Nelle funzioni .NET, è necessario usare un parametro `out string` nella firma della funzione o uno dei tipi nell'elenco seguente. Nelle funzioni Node.js si accede al BLOB di output usando `context.bindings.<name>`.

Nelle funzioni .NET è possibile eseguire l'output in uno dei tipi seguenti:

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>Trigger di coda con esempio di input e output del BLOB
Si supponga di avere function.json seguente, che definisce un [trigger di archiviazione della coda](functions-bindings-storage-queue.md), un input di archiviazione del BLOB e un output di archiviazione del BLOB. Si noti l'uso della proprietà dei metadati `queueTrigger` nelle proprietà `path` di input e output del BLOB:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Vedere l'esempio specifico del linguaggio che copia il BLOB di input nel BLOB di output.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>Esempio di associazione del BLOB in C# #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Esempio di associazione del BLOB in Node.js

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


