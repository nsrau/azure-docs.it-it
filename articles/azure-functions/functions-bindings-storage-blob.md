---
title: Binding dell'archiviazione BLOB di Azure per Funzioni di Azure
description: Informazioni su come usare trigger e associazioni dell'archiviazione BLOB di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 576167502fdb77c98c449dc5a448323dc5b23f35
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Binding dell'archiviazione BLOB di Azure per Funzioni di Azure

Questo articolo illustra come operare con le associazioni dell'archiviazione BLOB di Azure in Funzioni di Azure. Funzioni di Azure supporta le associazioni di trigger, input e output per i BLOB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [Gli account di archiviazione solo BLOB](../storage/common/storage-create-storage-account.md#blob-storage-accounts) non sono supportati. I trigger e le associazioni di archiviazione BLOB richiedono un account di archiviazione generico. 

## <a name="trigger"></a>Trigger

Usare un trigger di archiviazione BLOB per l'avvio di una funzione quando viene rilevato un BLOB nuovo o aggiornato. I contenuti del BLOB vengono dati come input alla funzione.

> [!NOTE]
> Quando si usa un trigger di tipo BLOB in un piano a consumo, è possibile che si verifichi un ritardo di un massimo di 10 minuti per l'elaborazione di nuovi BLOB in caso di inattività di un'app per le funzioni. Quando l'app per le funzioni è in esecuzione, i BLOB vengono elaborati immediatamente. Per evitare questo ritardo iniziale, prendere in considerazione una delle opzioni seguenti:
> - Usare un piano di servizio app con l'opzione Always On abilitata.
> - Usare un altro meccanismo per attivare l'elaborazione dei BLOB, ad esempio un messaggio della coda che contiene il nome del BLOB. Per un esempio, vedere l'[esempio di associazioni di input/output BLOB più avanti in questo articolo](#input--output---example).

## <a name="trigger---example"></a>Trigger - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C# precompilato](#trigger---c-example)
* [Script C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Trigger - esempio in C#

L'esempio seguente illustra codice [C# precompilato](functions-dotnet-class-library.md) che scrive un log quando viene aggiunto o aggiornato un BLOB nel contenitore `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Per altre informazioni sull'attributo `BlobTrigger`, vedere [Trigger - attributi](#trigger---attributes-for-precompiled-c).

### <a name="trigger---c-script-example"></a>Trigger - esempio di script C#

L'esempio seguente illustra un'associazione di trigger di BLOB in un file *function.json* e codice [script C#](functions-reference-csharp.md) che usa l'associazione. La funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel contenitore `samples-workitems`.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice script C# associato a un oggetto `Stream`:

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ecco il codice script C# associato a un oggetto `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Trigger - esempio JavaScript

L'esempio seguente illustra un'associazione di trigger di BLOB in un file *function.json* e [codice JavaScript] (functions-reference-node.md) che usa l'associazione. La funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel contenitore `samples-workitems`.

Ecco il file *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#trigger---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>Trigger - attributi

Per le funzioni di [C# precompilato](functions-dotnet-class-library.md), usare i seguenti attributi per configurare un trigger di BLOB:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs), definito nel pacchetto NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  L'attributo del costruttore accetta una stringa di percorso che indica il contenitore per il controllo e, facoltativamente, un [modello di nome per il BLOB](#trigger---blob-name-patterns). Ad esempio:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  È possibile impostare la proprietà `Connection` per specificare l'account di archiviazione da usare, come illustrato nell'esempio seguente:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Per un esempio completo, vedere [Trigger - esempio in C# precompilato](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), definito nel pacchetto NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Offre un altro modo per specificare l'account di archiviazione da usare. Il costruttore accetta il nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. L'attributo può essere applicato a livello di parametro, metodo o classe. L'esempio seguente illustra il livello classe e il livello metodo:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

L'account di archiviazione da usare è determinato nell'ordine seguente:

* La proprietà `Connection` dell'attributo `BlobTrigger`.
* L'attributo `StorageAccount` applicato allo stesso parametro dell'attributo `BlobTrigger`.
* L'attributo `StorageAccount` applicato alla funzione.
* L'attributo `StorageAccount` applicato alla classe.
* L'account di archiviazione predefinito per l'app per le funzioni (impostazione dell'app "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `BlobTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blobTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. Le eccezioni sono indicate nella sezione [usage](#trigger---usage). |
|**nome** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione. | 
|**path** | **BlobPath** |Contenitore da monitorare.  Può essere un [modello di nome per il BLOB](#trigger-blob-name-patterns). | 
|**connessione** | **Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione solo BLOB](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - uso

In C# e negli script C# è possibile accedere ai dati del BLOB con un parametro del metodo, ad esempio `Stream paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. È possibile definire associazioni con uno dei seguenti tipi:

* `TextReader`
* `Stream`
* `ICloudBlob` (è necessaria la direzione di associazione "inout" in *function.json*)
* `CloudBlockBlob` (è necessaria la direzione di associazione "inout" in *function.json*)
* `CloudPageBlob` (è necessaria la direzione di associazione "inout" in *function.json*)
* `CloudAppendBlob` (è necessaria la direzione di associazione "inout" in *function.json*)

Come accennato, alcuni di questi tipi richiedono una direzione di associazione `inout` in *function.json*. Questa direzione non è supportata dall'editor standard nel portale di Azure, pertanto è necessario usare l'editor avanzato.

Se sono previsti BLOB di testo, è possibile eseguire l'associazione al tipo `string`. È consigliabile solo se le dimensioni del BLOB sono ridotte, in quanto l'intero contenuto del BLOB viene caricato in memoria. In genere, è preferibile usare un tipo `Stream` o `CloudBlockBlob`.

In JavaScript si accede a dati del BLOB di input usando `context.bindings.<name>`.

## <a name="trigger---blob-name-patterns"></a>Trigger - modelli di nome dei BLOB

È possibile specificare un modello di nome di BLOB nella proprietà `path` in *function.json* o nel costruttore dell'attributo `BlobTrigger`. Il modello di nome può essere un'[espressione di filtro o di associazione](functions-triggers-bindings.md#binding-expressions-and-patterns).

### <a name="filter-on-blob-name"></a>Filtrare in base al nome del BLOB

L'esempio seguente attiva un trigger solo per i BLOB nel contenitore `input` che iniziano con la stringa "original-":

```json
"path": "input/original-{name}",
```
 
Se il nome del BLOB è *original-Blob1.txt*, il valore della variabile `name` nel codice della funzione sarà `Blob1`.

### <a name="filter-on-file-type"></a>Filtrare in base al tipo di file

L'esempio seguente attiva un trigger solo per i file con estensione *png*:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrare in base alle parentesi graffe nei nomi dei file

Per cercare le parentesi graffe nei nomi dei file, raddoppiare le parentesi graffe per eseguirne l'escape. L'esempio seguente filtra i BLOB che contengono parentesi graffe nel nome:

```json
"path": "images/{{20140101}}-{name}",
```

Se il BLOB è denominato *{20140101}-soundfile.mp3*, il valore della variabile `name` nel codice della funzione sarà *soundfile.mp3*. 

### <a name="get-file-name-and-extension"></a>Ottenere l'estensione e il nome del file

L'esempio seguente illustra come associare il nome e l'estensione del file BLOB separatamente:

```json
"path": "input/{blobname}.{blobextension}",
```
Se il BLOB è denominato *original-Blob1.txt*, i valori delle variabili `blobname` e `blobextension` nel codice della funzione saranno *original-Blob1* e *txt*.

## <a name="trigger---metadata"></a>Trigger - metadati

Il trigger del BLOB contiene diverse proprietà di metadati. Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Questi valori hanno la stessa semantica del tipo [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).


|Proprietà  |Tipo  |Descrizione  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Percorso del BLOB trigger.|
|`Uri`|`System.Uri`|L'URI del BLOB per la posizione primaria.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Le proprietà di sistema del BLOB. |
|`Metadata` |`IDictionary<string,string>`|I metadati definiti dall'utente per il BLOB.|

## <a name="trigger---blob-receipts"></a>Trigger - conferme di BLOB

Il runtime di Funzioni di Azure verifica che nessuna funzione trigger di BLOB venga chiamata più volte per lo stesso BLOB nuovo o aggiornato. Gestisce *conferme di BLOB* per determinare se una versione di BLOB specifica è stata elaborata.

Funzioni di Azure archivia le conferme di BLOB in un contenitore denominato *azure-webjobs-hosts* nell'account di archiviazione di Azure per l'app per le funzioni, specificato dall'impostazione app `AzureWebJobsStorage`. Una conferma di BLOB contiene le seguenti informazioni:

* La funzione attivata, ovvero "*&lt;nome dell'app per le funzioni>*.Functions.*&lt;nome della funzione>*", ad esempio: "MyFunctionApp.Functions.CopyBlob"
* Il nome del contenitore
* Il tipo di BLOB ("BlockBlob" o "PageBlob")
* Il nome del BLOB
* Il valore ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

Per forzare la rielaborazione di un BLOB è possibile eliminare manualmente la conferma del BLOB dal contenitore *azure-webjobs-hosts*.

## <a name="trigger---poison-blobs"></a>Trigger - BLOB non elaborabili

Se una funzione di trigger del BLOB ha esito negativo per un determinato BLOB, per impostazione predefinita Funzioni di Azure ritenta l'esecuzione fino a 5 volte. 

Se tutti i 5 tentativi non riescono, Funzioni di Azure aggiunge un messaggio a una coda di archiviazione denominata *webjobs-blobtrigger-poison*. Il messaggio di coda per i BLOB non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato *&lt;nome dell'app per le funzioni>*.Functions.*&lt;nome della funzione>*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

## <a name="trigger---polling-for-large-containers"></a>Trigger - polling per i contenitori di grandi dimensioni

Se il contenitore BLOB monitorato contiene più di 10.000 BLOB, il runtime di Funzioni analizza i file di log per cercare i BLOB nuovi o modificati. Questo processo può causare ritardi. È possibile quindi che una funzione non venga attivata per diversi minuti o più dopo la creazione del BLOB. Per di più [i log di archiviazione vengono creati in base al principio del "massimo sforzo"](/rest/api/storageservices/About-Storage-Analytics-Logging). Non è garantito che tutti gli eventi vengano acquisiti. In alcune condizioni, l'acquisizione dei log può non riuscire. Se è necessaria un'elaborazione dei BLOB più veloce o affidabile, valutare la possibilità di creare un [messaggio della coda](../storage/queues/storage-dotnet-how-to-use-queues.md) quando si crea il BLOB. Usare quindi un [trigger di coda](functions-bindings-storage-queue.md) invece di un trigger di BLOB per elaborare il BLOB. Un'altra opzione consiste nell'usare Griglia di eventi. Vedere l'esercitazione [Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input--output"></a>Input e output

Usare le associazioni di input e di output per l'archiviazione BLOB per la lettura e la scrittura dei BLOB.

## <a name="input--output---example"></a>Input e output - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C# precompilato](#input--output---c-example)
* [Script C#](#input--output---c-script-example)
* [JavaScript](#input--output---javascript-example)

### <a name="input--output---c-example"></a>Input e output - esempio in C#

L'esempio seguente è una funzione in [C# precompilato](functions-dotnet-class-library.md) che usa un trigger di BLOB di input e due associazioni di BLOB di output. La funzione viene attivata dalla creazione di un BLOB dell'immagine nel contenitore *sample-images*. Crea copie di piccole e medie dimensioni del BLOB dell'immagine. 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="input--output---c-script-example"></a>Input e output - esempio di script C#

L'esempio seguente mostra associazioni di input e di output di BLOB in un file *function.json* e codice [script C#](functions-reference-csharp.md) che usa le associazioni. La funzione crea una copia di un BLOB di testo. La funzione viene attivata da un messaggio della coda che contiene il nome del BLOB da copiare. Il nuovo BLOB è denominato *{originalblobname}-Copy*.

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

Queste proprietà sono descritte nella sezione [configuration](#input--output---configuration).

Ecco il codice script C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input--output---javascript-example"></a>Input e output - esempio JavaScript

L'esempio seguente mostra associazioni di input e di output di BLOB in un file *function.json* e [codice JavaScript] (functions-reference-node.md) che usa le associazioni. La funzione crea una copia di un BLOB. La funzione viene attivata da un messaggio della coda che contiene il nome del BLOB da copiare. Il nuovo BLOB è denominato *{originalblobname}-Copy*.

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

Queste proprietà sono descritte nella sezione [configuration](#input--output---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input--output---attributes"></a>Input e output - attributi

Per [C# precompilato](functions-dotnet-class-library.md) usare [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

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

Per un esempio completo, vedere [Input e output - esempio in C# precompilato](#input--output---c-example).

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere [Trigger - attributi](#trigger---attributes-for-precompiled-c).

## <a name="input--output---configuration"></a>Input e output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Blob`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blob`. |
|**direction** | n/d | Deve essere impostato su `in` per un'associazione di input o su out per un'associazione di output. Le eccezioni sono indicate nella sezione [usage](#input--output---usage). |
|**nome** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione.  Impostare su `$return` per fare riferimento al valore restituito della funzione.|
|**path** |**BlobPath** | Percorso del BLOB. | 
|**connessione** |**Connection**| Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione solo BLOB](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|n/d | **Accedere** | Indica se eseguire la lettura o la scrittura. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input--output---usage"></a>Input e output - uso

In C# precompilato e negli script C# è possibile accedere al BLOB con un parametro del metodo, ad esempio `Stream paramName`. Negli script C#, `paramName` è il valore specificato nella proprietà `name` di *function.json*. È possibile definire associazioni con uno dei seguenti tipi:

* `out string`
* `TextWriter` 
* `TextReader`
* `Stream`
* `ICloudBlob` (è necessaria la direzione di associazione "inout" in *function.json*)
* `CloudBlockBlob` (è necessaria la direzione di associazione "inout" in *function.json*)
* `CloudPageBlob` (è necessaria la direzione di associazione "inout" in *function.json*)
* `CloudAppendBlob` (è necessaria la direzione di associazione "inout" in *function.json*)

Come accennato, alcuni di questi tipi richiedono una direzione di associazione `inout` in *function.json*. Questa direzione non è supportata dall'editor standard nel portale di Azure, pertanto è necessario usare l'editor avanzato.

Se si esegue la lettura di BLOB di testo, è possibile eseguire l'associazione a un tipo `string`. È consigliabile usare questo tipo solo se le dimensioni del BLOB sono ridotte, in quanto l'intero contenuto del BLOB viene caricato in memoria. In genere, è preferibile usare un tipo `Stream` o `CloudBlockBlob`.

In JavaScript si accede a dati del BLOB di input usando `context.bindings.<name>`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare a una guida introduttiva che usa un trigger per l'archiviazione BLOB](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
