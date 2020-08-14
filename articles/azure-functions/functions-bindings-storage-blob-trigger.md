---
title: Trigger di archiviazione BLOB di Azure per funzioni di Azure
description: Informazioni su come eseguire una funzione di Azure come modifiche ai dati di archiviazione BLOB di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 67e1f1dff43939ce7ef279db57bee4b18bd12dc8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213950"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Trigger di archiviazione BLOB di Azure per funzioni di Azure

Il trigger di archiviazione BLOB avvia una funzione quando viene rilevato un BLOB nuovo o aggiornato. Il contenuto del BLOB viene fornito come [input alla funzione](./functions-bindings-storage-blob-input.md).

Il trigger di archiviazione BLOB di Azure richiede un account di archiviazione per utilizzo generico. Sono supportati anche gli account di archiviazione V2 con [spazi dei nomi gerarchici](../storage/blobs/data-lake-storage-namespace.md) . Per usare un account solo BLOB o se l'applicazione ha esigenze specializzate, esaminare le alternative all'uso di questo trigger.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](./functions-bindings-storage-blob.md).

## <a name="alternatives"></a>Alternativi

### <a name="event-grid-trigger"></a>Trigger griglia di eventi

Il [trigger griglia di eventi](functions-bindings-event-grid.md) include anche il supporto predefinito per [gli eventi BLOB](../storage/blobs/storage-blob-event-overview.md). Usare Griglia eventi anziché il trigger di archiviazione BLOB per gli scenari seguenti:

- **Account di archiviazione solo BLOB**: gli [account di archiviazione solo BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts) sono supportati per le associazioni di input e di output di BLOB, ma non per i trigger di BLOB.

- **Scalabilità elevata**: la scalabilità elevata può essere definita in modo generico come contenitori con più di 100.000 BLOB o account di archiviazione con più di 100 aggiornamenti BLOB al secondo.

- **Riduzione della latenza**: se l'app per le funzioni è nel piano a consumo, è possibile che si verifichi un ritardo massimo di 10 minuti nell'elaborazione di nuovi BLOB se un'app per le funzioni è diventata inattiva. Per evitare questa latenza, è possibile passare a un piano di servizio app con AlwaysOn abilitato. È anche possibile usare un [trigger griglia di eventi](functions-bindings-event-grid.md) con l'account di archiviazione BLOB. Per un esempio, vedere l'[esercitazione di Griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Vedere l'esercitazione [ridimensionare l'immagine con griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md) di un esempio di griglia di eventi.

### <a name="queue-storage-trigger"></a>Trigger per l'archiviazione code

Un altro approccio all'elaborazione dei BLOB consiste nello scrivere messaggi della coda che corrispondono ai BLOB creati o modificati e quindi usare un [trigger di archiviazione di Accodamento](./functions-bindings-storage-queue.md) per iniziare l'elaborazione.

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che scrive un log quando viene aggiunto o aggiornato un BLOB nel contenitore `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](./functions-bindings-expressions-patterns.md) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#blob-name-patterns) più avanti in questo articolo.

Per ulteriori informazioni sull' `BlobTrigger` attributo, vedere [attributi e annotazioni](#attributes-and-annotations).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Nell'esempio seguente viene illustrata un'associazione di trigger di BLOB in una *function.jsnel* file e nel codice che usa l'associazione. La funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel `samples-workitems` [contenitore](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](./functions-bindings-expressions-patterns.md) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sulle proprietà del file *function.json*, vedere la sezione [Configurazione](#configuration) che spiega queste proprietà.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](./functions-bindings-expressions-patterns.md) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sulle proprietà del file *function.json*, vedere la sezione [Configurazione](#configuration) che spiega queste proprietà.

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

L'esempio seguente illustra un'associazione di trigger di BLOB in un file *function.json* e il [codice Python](functions-reference-python.md) che usa l'associazione. La funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel `samples-workitems` [contenitore](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](./functions-bindings-expressions-patterns.md) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sulle proprietà del file *function.json*, vedere la sezione [Configurazione](#configuration) che spiega queste proprietà.

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Questa funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel `myblob` contenitore.

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

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi](functions-dotnet-class-library.md) usare i seguenti attributi per configurare un trigger di BLOB:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  L'attributo del costruttore accetta una stringa di percorso che indica il contenitore per il controllo e, facoltativamente, un [modello di nome per il BLOB](#blob-name-patterns). Ecco un esempio:

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

  Per un esempio completo, vedere [esempio di trigger](#example).

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

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L' `@BlobTrigger` attributo viene usato per fornire l'accesso al BLOB che ha attivato la funzione. Per informazioni dettagliate, vedere l' [esempio di trigger](#example) .

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `BlobTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blobTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. Le eccezioni sono indicate nella sezione [usage](#usage). |
|**nome** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione. |
|**path** | **BlobPath** |[Contenitore](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) da monitorare.  Può essere un [modello di nome per il BLOB](#blob-name-patterns). |
|**connection** | **Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Accedere ai dati BLOB usando `context.bindings.<NAME>` Where `<NAME>` corrisponde al valore definito in *function.json*.

# <a name="python"></a>[Python](#tab/python)

Accedere ai dati BLOB tramite il parametro tipizzato come [InputStream](/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Per informazioni dettagliate, vedere l' [esempio di trigger](#example) .

# <a name="java"></a>[Java](#tab/java)

L' `@BlobTrigger` attributo viene usato per fornire l'accesso al BLOB che ha attivato la funzione. Per informazioni dettagliate, vedere l' [esempio di trigger](#example) .

---

## <a name="blob-name-patterns"></a>Modelli di nome BLOB

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

Se il nome del BLOB è *original-Blob1.txt*, il valore della variabile `name` nel codice della funzione sarà `Blob1.txt`.

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

Se il BLOB è denominato * {20140101}-soundfile.mp3*, il `name` valore della variabile nel codice della funzione viene *soundfile.mp3*.

## <a name="metadata"></a>Metadati

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I metadati non sono disponibili in Python.

# <a name="java"></a>[Java](#tab/java)

I metadati non sono disponibili in Java.

---

## <a name="blob-receipts"></a>Conferme di BLOB

Il runtime di Funzioni di Azure verifica che nessuna funzione trigger di BLOB venga chiamata più volte per lo stesso BLOB nuovo o aggiornato. Gestisce *conferme di BLOB* per determinare se una versione di BLOB specifica è stata elaborata.

Funzioni di Azure archivia le conferme di BLOB in un contenitore denominato *azure-webjobs-hosts* nell'account di archiviazione di Azure per l'app per le funzioni, specificato dall'impostazione app `AzureWebJobsStorage`. Una conferma di BLOB contiene le seguenti informazioni:

* Funzione attivata ("nome dell'app per le* &lt; funzioni>*. Funzioni. * &lt; nome della funzione>*", ad esempio:" MyFunctionApp. Functions. CopyBlob ")
* Il nome del contenitore
* Il tipo di BLOB ("BlockBlob" o "PageBlob")
* Il nome del BLOB
* Il valore ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

Per forzare la rielaborazione di un BLOB è possibile eliminare manualmente la conferma del BLOB dal contenitore *azure-webjobs-hosts*. Mentre la rielaborazione potrebbe non essere immediatamente eseguita, è garantita in un momento successivo. Per la rielaborazione immediata, il BLOB *ScanInfo* in *Azure-webjobs-hosts/blobscaninfo* può essere aggiornato. Tutti i BLOB con il timestamp dell'Ultima modifica dopo la `LatestScan` proprietà verranno analizzati nuovamente.

## <a name="poison-blobs"></a>BLOB non elaborabili

Se una funzione di trigger del BLOB ha esito negativo per un determinato BLOB, per impostazione predefinita Funzioni di Azure ritenta l'esecuzione fino a 5 volte.

Se tutti i 5 tentativi non riescono, Funzioni di Azure aggiunge un messaggio a una coda di archiviazione denominata *webjobs-blobtrigger-poison*. Il numero massimo di tentativi è configurabile. La stessa impostazione MaxDequeueCount viene usata per la gestione dei BLOB non elaborabili e per la gestione dei messaggi della coda non elaborabile. Il messaggio di coda per i BLOB non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato nome dell'app per le * &lt; funzioni>*. Funzioni. * &lt; nome funzione>*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Concorrenza e utilizzo della memoria

Il trigger del BLOB usa una un servizio di accodamento interno, quindi il numero massimo di chiamate di funzione simultanee è controllato dalla [configurazione delle code in host.json](functions-host-json.md#queues). Le impostazioni predefinite limitano la concorrenza a 24 chiamate. Questo limite si applica separatamente a ciascuna funzione che usa un trigger di BLOB.

[Il piano a consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita un'app per le funzioni in una macchina virtuale (VM) a 1,5 GB di memoria. La memoria viene usata da ogni istanza della funzione attualmente in esecuzione e dal runtime di funzioni stesso. Se una funzione di attivazione del BLOB carica l'intero BLOB nella memoria, la memoria massima usata da tale funzione solo per i BLOB è pari a 24 * le dimensioni massime del BLOB. Ad esempio, un'app per le funzioni con tre funzioni attivate dal BLOB e le impostazioni predefinite avrebbe una concorrenza per macchina virtuale massima pari a 3 * 24 = 72 chiamate di funzione.

Le funzioni JavaScript e Java caricano l'intero BLOB in memoria e le funzioni C# eseguono questa operazione se si esegue l'associazione a `string` o `Byte[]` .

## <a name="polling"></a>Polling

Il polling funziona come ibrido tra l'ispezione dei log e l'esecuzione di analisi periodiche dei contenitori. I BLOB vengono analizzati in gruppi di 10.000 alla volta con un token di continuazione usato tra gli intervalli.

> [!WARNING]
> Per di più [i log di archiviazione vengono creati in base al principio del "massimo sforzo"](/rest/api/storageservices/About-Storage-Analytics-Logging). Non è garantito che tutti gli eventi vengano acquisiti. In alcune condizioni, l'acquisizione dei log può non riuscire.
> 
> Se è necessaria un'elaborazione BLOB più veloce o affidabile, è consigliabile creare un [messaggio della coda](../storage/queues/storage-dotnet-how-to-use-queues.md) quando si crea il BLOB. Usare quindi un [trigger di coda](functions-bindings-storage-queue.md) invece di un trigger di BLOB per elaborare il BLOB. Un'altra opzione consiste nell'usare Griglia di eventi. Vedere l'esercitazione [Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Passaggi successivi

- [Leggere i dati di archiviazione BLOB quando viene eseguita una funzione](./functions-bindings-storage-blob-input.md)
- [Scrivere dati di archiviazione BLOB da una funzione](./functions-bindings-storage-blob-output.md)
