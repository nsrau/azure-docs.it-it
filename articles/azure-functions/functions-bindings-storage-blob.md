---
title: Binding dell'archiviazione BLOB di Azure per Funzioni di Azure
description: Informazioni su come usare trigger e associazioni dell'archiviazione BLOB di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.service: azure-functions
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: b565a48b56162d19a07f0f54bfe780b7dda04b96
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177389"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Binding dell'archiviazione BLOB di Azure per Funzioni di Azure

Questo articolo illustra come operare con le associazioni dell'archiviazione BLOB di Azure in Funzioni di Azure. Funzioni di Azure supporta le associazioni di trigger, input e output per i BLOB. L'articolo include una sezione per ogni binding:

* [Trigger di BLOB](#trigger)
* [Associazione di input BLOB](#input)
* [Associazione di output BLOB](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Usare il trigger di Griglia di eventi anziché il trigger di archiviazione BLOB per gli account di archiviazione solo BLOB, per la scalabilità elevata, o per ridurre la latenza. Per altre informazioni, vedere la sezione [Trigger](#trigger).

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Le associazioni di archiviazione BLOB sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

Le associazioni di archiviazione BLOB sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Il trigger di archiviazione BLOB avvia una funzione quando viene rilevato un BLOB nuovo o aggiornato. I contenuti del BLOB vengono dati come input alla funzione.

Il [trigger di Griglia di eventi](functions-bindings-event-grid.md) include il supporto incorporato per gli [eventi BLOB](../storage/blobs/storage-blob-event-overview.md) e può essere usato anche per avviare una funzione quando viene rilevato un BLOB nuovo o aggiornato. Per un esempio, vedere l'esercitazione [Ridimensionamento di immagini con Griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md).

Usare Griglia eventi anziché il trigger di archiviazione BLOB per gli scenari seguenti:

* Account di archiviazione BLOB
* Scalabilità elevata
* Ridurre al minimo la latenza

### <a name="blob-storage-accounts"></a>Account di archiviazione BLOB

Gli [account di archiviazione BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts) sono supportati per le associazioni di input e output BLOB, ma non per i trigger BLOB. I trigger di BLOB richiedono un account di archiviazione generico.

### <a name="high-scale"></a>Scalabilità elevata

La scalabilità elevata può essere definita in modo generico come i contenitori con più di 100.000 BLOB o gli account di archiviazione con più di 100 aggiornamenti BLOB al secondo.

### <a name="latency-issues"></a>Problemi di latenza

Se l'app per le funzioni è inclusa nel piano a consumo, è possibile che si verifichi un ritardo di un massimo di 10 minuti per l'elaborazione di nuovi BLOB in caso di inattività di un'app per le funzioni. Per evitare questa latenza, è possibile passare a un piano di servizio app con AlwaysOn abilitato. È anche possibile usare un [trigger griglia di eventi](functions-bindings-event-grid.md) con l'account di archiviazione BLOB. Per un esempio, vedere l'[esercitazione di Griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Trigger per l'archiviazione code

Oltre alla Griglia di eventi, un'alternativa per l'elaborazione di BLOB è il trigger di archiviazione code, che tuttavia non include un supporto integrato per gli eventi BLOB. È necessario creare messaggi della coda quando si creano o aggiornano i BLOB. Per un esempio in cui si presuppone che sia stata eseguita l'operazione, vedere l'[esempio di associazione di input BLOB più avanti in questo articolo](#input---example).

## <a name="trigger---example"></a>Trigger - esempio

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che scrive un log quando viene aggiunto o aggiornato un BLOB nel contenitore `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](./functions-bindings-expressions-patterns.md) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#trigger---blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sull'attributo `BlobTrigger`, vedere [Trigger - attributi](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

L'esempio seguente illustra un'associazione di trigger di BLOB in un file *Function. JSON* e il codice che usa l'associazione. La funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel [contenitore](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) `samples-workitems`.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](./functions-bindings-expressions-patterns.md) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#trigger---blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sulle proprietà del file *function.json*, vedere la sezione [Configurazione](#trigger---configuration) che spiega queste proprietà.

Ecco il codice script C# associato a un oggetto `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ecco il codice script C# associato a un oggetto `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente illustra un'associazione di trigger di BLOB in un file *function.json* e il [codice JavaScript](functions-reference-node.md) che usa l'associazione. La funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel contenitore `samples-workitems`.

Ecco il file *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](./functions-bindings-expressions-patterns.md) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#trigger---blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sulle proprietà del file *function.json*, vedere la sezione [Configurazione](#trigger---configuration) che spiega queste proprietà.

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

L'esempio seguente illustra un'associazione di trigger di BLOB in un file *function.json* e il [codice Python](functions-reference-python.md) che usa l'associazione. La funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel [contenitore](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) `samples-workitems`.

Ecco il file *function.json*:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](./functions-bindings-expressions-patterns.md) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#trigger---blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sulle proprietà del file *function.json*, vedere la sezione [Configurazione](#trigger---configuration) che spiega queste proprietà.

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

L'esempio seguente illustra un'associazione di trigger di BLOB in un file *function.json* e il [codice Java](functions-reference-java.md) che usa l'associazione. La funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel contenitore `myblob`.

Ecco il file *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Ecco il codice Java:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="trigger---attributes"></a>Trigger - attributi

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nelle [librerie di classi](functions-dotnet-class-library.md) usare i seguenti attributi per configurare un trigger di BLOB:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  L'attributo del costruttore accetta una stringa di percorso che indica il contenitore per il controllo e, facoltativamente, un [modello di nome per il BLOB](#trigger---blob-name-patterns). Di seguito è riportato un esempio:

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

  Per un esempio completo, vedere [esempio di trigger](#trigger---example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="javatabjava"></a>[Java](#tab/java)

L'attributo `@BlobTrigger` viene usato per fornire l'accesso al BLOB che ha attivato la funzione. Per informazioni dettagliate, vedere l' [esempio di trigger](#trigger---example) .

---

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `BlobTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blobTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. Le eccezioni sono indicate nella sezione [usage](#trigger---usage). |
|**name** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione. |
|**path** | **BlobPath** |[Contenitore](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) da monitorare.  Può essere un [modello di nome per il BLOB](#trigger---blob-name-patterns). |
|**connessione** | **Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - uso

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Accedere ai dati BLOB utilizzando `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Accedere ai dati BLOB tramite il parametro tipizzato come [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Per informazioni dettagliate, vedere l' [esempio di trigger](#trigger---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

L'attributo `@BlobTrigger` viene usato per fornire l'accesso al BLOB che ha attivato la funzione. Per informazioni dettagliate, vedere l' [esempio di trigger](#trigger---example) .

---

## <a name="trigger---blob-name-patterns"></a>Trigger - modelli di nome dei BLOB

È possibile specificare un modello di nome di BLOB nella proprietà `path` in *function.json* o nel costruttore dell'attributo `BlobTrigger`. Il modello di nome può essere un'[espressione di filtro o di associazione](./functions-bindings-expressions-patterns.md). Per esempi, vedere le sezioni seguenti.

### <a name="get-file-name-and-extension"></a>Ottenere l'estensione e il nome del file

L'esempio seguente illustra come associare il nome e l'estensione del file BLOB separatamente:

```json
"path": "input/{blobname}.{blobextension}",
```

Se il BLOB è denominato *original-Blob1.txt*, i valori delle variabili `blobname` e `blobextension` nel codice della funzione saranno *original-Blob1* e *txt*.

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

## <a name="trigger---metadata"></a>Trigger - metadati

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I metadati non sono disponibili in Python.

# <a name="javatabjava"></a>[Java](#tab/java)

I metadati non sono disponibili in Java.

---

## <a name="trigger---blob-receipts"></a>Trigger - conferme di BLOB

Il runtime di Funzioni di Azure verifica che nessuna funzione trigger di BLOB venga chiamata più volte per lo stesso BLOB nuovo o aggiornato. Gestisce *conferme di BLOB* per determinare se una versione di BLOB specifica è stata elaborata.

Funzioni di Azure archivia le conferme di BLOB in un contenitore denominato *azure-webjobs-hosts* nell'account di archiviazione di Azure per l'app per le funzioni, specificato dall'impostazione app `AzureWebJobsStorage`. Una conferma di BLOB contiene le seguenti informazioni:

* La funzione attivata, ovvero " *&lt;nome dell'app per le funzioni>* .Functions. *&lt;nome della funzione>* ", ad esempio: "MyFunctionApp.Functions.CopyBlob"
* Il nome del contenitore
* Il tipo di BLOB ("BlockBlob" o "PageBlob")
* Il nome del BLOB
* Il valore ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

Per forzare la rielaborazione di un BLOB è possibile eliminare manualmente la conferma del BLOB dal contenitore *azure-webjobs-hosts*. Mentre la rielaborazione potrebbe non essere immediatamente eseguita, è garantita in un momento successivo.

## <a name="trigger---poison-blobs"></a>Trigger - BLOB non elaborabili

Se una funzione di trigger del BLOB ha esito negativo per un determinato BLOB, per impostazione predefinita Funzioni di Azure ritenta l'esecuzione fino a 5 volte.

Se tutti i 5 tentativi non riescono, Funzioni di Azure aggiunge un messaggio a una coda di archiviazione denominata *webjobs-blobtrigger-poison*. Il messaggio di coda per i BLOB non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato *&lt;nome dell'app per le funzioni>* .Functions. *&lt;nome della funzione>* )
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* Valore ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Trigger: concorrenza e utilizzo della memoria

Il trigger del BLOB usa una un servizio di accodamento interno, quindi il numero massimo di chiamate di funzione simultanee è controllato dalla [configurazione delle code in host.json](functions-host-json.md#queues). Le impostazioni predefinite limitano la concorrenza a 24 chiamate. Questo limite si applica separatamente a ciascuna funzione che usa un trigger di BLOB.

[Il piano di consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita un'app per le funzioni in una macchina virtuale (VM) a 1,5 GB di memoria. La memoria viene usata da ogni istanza della funzione attualmente in esecuzione e dal runtime di funzioni stesso. Se una funzione di attivazione del BLOB carica l'intero BLOB nella memoria, la memoria massima usata da tale funzione solo per i BLOB è pari a 24 * le dimensioni massime del BLOB. Ad esempio, un'app per le funzioni con tre funzioni attivate dal BLOB e le impostazioni predefinite avrebbe una concorrenza per macchina virtuale massima pari a 3 * 24 = 72 chiamate di funzione.

Le funzioni JavaScript e Java caricano in memoria l'intero BLOB e le funzioni C# eseguono questa operazione se si esegue l'associazione a `string`, `Byte[]` o POCO.

## <a name="trigger---polling"></a>Trigger - polling

Se il contenitore BLOB monitorato contiene più di 10.000 BLOB (in tutti i contenitori), il runtime di funzioni analizza i file di log per controllare i BLOB nuovi o modificati. Questo processo può causare ritardi. È possibile quindi che una funzione non venga attivata per diversi minuti o più dopo la creazione del BLOB.

> [!WARNING]
> Per di più [i log di archiviazione vengono creati in base al principio del "massimo sforzo"](/rest/api/storageservices/About-Storage-Analytics-Logging). Non è garantito che tutti gli eventi vengano acquisiti. In alcune condizioni, l'acquisizione dei log può non riuscire.
> 
> Se è necessaria un'elaborazione dei BLOB più veloce o affidabile, valutare la possibilità di creare un [messaggio della coda](../storage/queues/storage-dotnet-how-to-use-queues.md) quando si crea il BLOB. Usare quindi un [trigger di coda](functions-bindings-storage-queue.md) invece di un trigger di BLOB per elaborare il BLOB. Un'altra opzione consiste nell'usare Griglia di eventi. Vedere l'esercitazione [Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Input

Usare un'associazione di input dell'archiviazione BLOB per leggere i BLOB.

## <a name="input---example"></a>Input - esempio

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L'esempio seguente è una [funzione in C#](functions-dotnet-class-library.md) che usa un trigger della coda e un'associazione di input BLOB. Il messaggio della coda contiene il nome del BLOB e la funzione registra la dimensione del BLOB.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

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

Queste proprietà sono descritte nella sezione [configuration](#input---configuration).

Ecco il codice script C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Queste proprietà sono descritte nella sezione [configuration](#input---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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
      "name": "$return",
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

Queste proprietà sono descritte nella sezione [configuration](#input---configuration).

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

Questa sezione contiene gli esempi seguenti:

* [Trigger HTTP, ricerca del nome BLOB da una stringa di query](#http-trigger-look-up-blob-name-from-query-string)
* [Trigger di coda, ricezione del nome BLOB da un messaggio in coda](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Trigger HTTP, Cerca il nome del BLOB dalla stringa di query

 L'esempio seguente mostra una funzione Java che usa l'annotazione `HttpTrigger` per ricevere un parametro che contiene il nome di un file in un contenitore di archiviazione BLOB. L'annotazione `BlobInput` legge quindi il file e passa il relativo contenuto alla funzione come `byte[]`.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Trigger della coda, nome del BLOB di ricezione dal messaggio della coda

 L'esempio seguente mostra una funzione Java che usa l'annotazione `QueueTrigger` per ricevere un messaggio che contiene il nome di un file in un contenitore di archiviazione BLOB. L'annotazione `BlobInput` legge quindi il file e passa il relativo contenuto alla funzione come `byte[]`.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare `@BlobInput` l'annotazione per i parametri il cui valore deriva da BLOB.  Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

---

## <a name="input---attributes"></a>Input - attributi

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Il costruttore dell'attributo accetta il percorso del BLOB e un parametro `FileAccess` che indica la lettura o la scrittura, come illustrato nell'esempio seguente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

È possibile impostare la proprietà `Connection` per specificare l'account di archiviazione da usare, come illustrato nell'esempio seguente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere [Trigger - attributi](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="javatabjava"></a>[Java](#tab/java)

L'attributo `@BlobInput` consente di accedere al BLOB che ha attivato la funzione. Se si usa una matrice di byte con l'attributo, impostare `dataType` su `binary`. Per informazioni dettagliate, vedere l' [esempio di input](#input---example) .

---

## <a name="input---configuration"></a>Input - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Blob`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blob`. |
|**direction** | n/d | Il valore deve essere impostato su `in`. Le eccezioni sono indicate nella sezione [usage](#input---usage). |
|**name** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione.|
|**path** |**BlobPath** | Percorso del BLOB. |
|**connessione** |**Connection**| Nome di un'impostazione dell'app che contiene la [stringa di connessione di archiviazione](../storage/common/storage-configure-connection-string.md) da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione solo BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/d | **Accedere** | Indica se eseguire la lettura o la scrittura. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Input - uso

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Accedere ai dati BLOB utilizzando `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Accedere ai dati BLOB tramite il parametro tipizzato come [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Per informazioni dettagliate, vedere l' [esempio di input](#input---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

L'attributo `@BlobInput` consente di accedere al BLOB che ha attivato la funzione. Se si usa una matrice di byte con l'attributo, impostare `dataType` su `binary`. Per informazioni dettagliate, vedere l' [esempio di input](#input---example) .

---

## <a name="output"></a>Output

Usare associazioni di output di archiviazione BLOB per scrivere i BLOB.

## <a name="output---example"></a>Output - esempio

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L'esempio seguente è una [funzione in C#](functions-dotnet-class-library.md) che usa un trigger di BLOB di input e due associazioni di BLOB di output. La funzione viene attivata dalla creazione di un BLOB dell'immagine nel contenitore *sample-images*. Crea copie di piccole e medie dimensioni del BLOB dell'immagine.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    IImageFormat format;

    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageSmall, ImageSize.Small, format);
    }

    image.Position = 0;
    using (Image<Rgba32> input = Image.Load(image, out format))
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
```

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

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

Queste proprietà sono descritte nella sezione [configuration](#output---configuration).

Ecco il codice script C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Queste proprietà sono descritte nella sezione [configuration](#output---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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

Queste proprietà sono descritte nella sezione [configuration](#output---configuration).

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

## <a name="output---attributes"></a>Output - attributi

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="javatabjava"></a>[Java](#tab/java)

L'attributo `@BlobOutput` consente di accedere al BLOB che ha attivato la funzione. Se si usa una matrice di byte con l'attributo, impostare `dataType` su `binary`. Per informazioni dettagliate, vedere l' [esempio di output](#output---example) .

---

Per un esempio completo, vedere [esempio di output](#output---example).

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere [Trigger - attributi](#trigger---attributes).

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Blob`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blob`. |
|**direction** | n/d | Deve essere impostato su `out` per un'associazione di output. Le eccezioni sono indicate nella sezione [usage](#output---usage). |
|**name** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione.  Impostare su `$return` per fare riferimento al valore restituito della funzione.|
|**path** |**BlobPath** | Percorso del contenitore BLOB. |
|**connessione** |**Connection**| Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione solo BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/d | **Accedere** | Indica se eseguire la lettura o la scrittura. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Output - uso

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In JavaScript si accede a dati del BLOB di input usando `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

È possibile dichiarare i parametri della funzione come i seguenti tipi per scrivere nell'archivio BLOB:

* Stringhe come `func.Out(str)`
* Flussi come `func.Out(func.InputStream)`

Per informazioni dettagliate, vedere l' [esempio di output](#output---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

L'attributo `@BlobOutput` consente di accedere al BLOB che ha attivato la funzione. Se si usa una matrice di byte con l'attributo, impostare `dataType` su `binary`. Per informazioni dettagliate, vedere l' [esempio di output](#output---example) .

---

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione |  Riferimenti |
|---|---|
| Blob | [Codici di errore BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Table, Queue |  [Codici di errore di archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Risoluzione dei problemi](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
