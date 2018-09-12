---
title: Binding dell'archiviazione BLOB di Azure per Funzioni di Azure
description: Informazioni su come usare trigger e associazioni dell'archiviazione BLOB di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/03/2018
ms.author: glenga
ms.openlocfilehash: b9dcc4d7edd24d545722dde85e682a658d5ef2bc
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667946"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Binding dell'archiviazione BLOB di Azure per Funzioni di Azure

Questo articolo illustra come operare con le associazioni dell'archiviazione BLOB di Azure in Funzioni di Azure. Funzioni di Azure supporta le associazioni di trigger, input e output per i BLOB. L'articolo include una sezione per ogni binding:

* [Trigger di BLOB](#trigger)
* [Associazione di input BLOB](#input)
* [Associazione di output BLOB](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Usare il trigger di Griglia di eventi anziché il trigger di archiviazione BLOB per gli account di archiviazione solo BLOB, per la scalabilità elevata, o per evitare ritardi di avvio a freddo. Per altre informazioni, vedere la sezione [Trigger](#trigger). 

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Le associazioni di archiviazione BLOB sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

Le associazioni di archiviazione BLOB sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Il trigger di archiviazione BLOB avvia una funzione quando viene rilevato un BLOB nuovo o aggiornato. I contenuti del BLOB vengono dati come input alla funzione.

Il [trigger di Griglia di eventi](functions-bindings-event-grid.md) include il supporto incorporato per gli [eventi BLOB](../storage/blobs/storage-blob-event-overview.md) e può essere usato anche per avviare una funzione quando viene rilevato un BLOB nuovo o aggiornato. Per un esempio, vedere l'esercitazione [Ridimensionamento di immagini con Griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md).

Usare Griglia eventi anziché il trigger di archiviazione BLOB per gli scenari seguenti:

* Account di archiviazione solo BLOB
* Scalabilità elevata
* Riduzione al minimo del ritardo di avvio a freddo

### <a name="blob-only-storage-accounts"></a>Account di archiviazione solo BLOB

Gli [account di archiviazione solo BLOB](../storage/common/storage-create-storage-account.md#blob-storage-accounts) sono supportati per le associazioni di input e output BLOB, ma non per i trigger BLOB. I trigger di BLOB richiedono un account di archiviazione generico.

### <a name="high-scale"></a>Scalabilità elevata

La scalabilità elevata può essere definita in modo generico come i contenitori con più di 100.000 BLOB o gli account di archiviazione con più di 100 aggiornamenti BLOB al secondo.

### <a name="cold-start-delay"></a>Ritardo di avvio a freddo

Se l'app per le funzioni è inclusa nel piano a consumo, è possibile che si verifichi un ritardo di un massimo di 10 minuti per l'elaborazione di nuovi BLOB in caso di inattività di un'app per le funzioni. Per evitare questo ritardo di avvio a freddo, è possibile passare a un piano di servizio app con Always On abilitato o usare un tipo di trigger diverso.

### <a name="queue-storage-trigger"></a>Trigger per l'archiviazione code

Oltre alla Griglia di eventi, un'alternativa per l'elaborazione di BLOB è il trigger di archiviazione code, che tuttavia non include un supporto integrato per gli eventi BLOB. È necessario creare messaggi della coda quando si creano o aggiornano i BLOB. Per un esempio in cui si presuppone che sia stata eseguita l'operazione, vedere l'[esempio di associazione di input BLOB più avanti in questo articolo](#input---example).

## <a name="trigger---example"></a>Trigger - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#trigger---c-example)
* [Script C# (file con estensione csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Trigger - esempio in C#

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che scrive un log quando viene aggiunto o aggiornato un BLOB nel contenitore `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](functions-triggers-bindings.md#binding-expressions-and-patterns) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#trigger---blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sull'attributo `BlobTrigger`, vedere [Trigger - attributi](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Trigger - esempio di script C#

L'esempio seguente illustra un'associazione di trigger di BLOB in un file *function.json* e codice [script C# (.csx)](functions-reference-csharp.md) che usa l'associazione. La funzione scrive un log quando viene aggiunto o aggiornato un BLOB nel contenitore `samples-workitems`.

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

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](functions-triggers-bindings.md#binding-expressions-and-patterns) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#trigger---blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sulle proprietà del file *function.json*, vedere la sezione [Configurazione](#trigger---configuration) che spiega queste proprietà.

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

La stringa `{name}` nel percorso del trigger di BLOB `samples-workitems/{name}` crea un'[espressione di associazione](functions-triggers-bindings.md#binding-expressions-and-patterns) che può essere usata nel codice della funzione per accedere al nome file del BLOB di attivazione. Per altre informazioni, vedere [Modelli di nome dei BLOB](#trigger---blob-name-patterns) più avanti in questo articolo.

Per altre informazioni sulle proprietà del file *function.json*, vedere la sezione [Configurazione](#trigger---configuration) che spiega queste proprietà.

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---java-example"></a>Trigger - Esempio Java

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
                  path = "myblob/filepath",
                  connection = "myconnvarname") byte[] content,
    @BindingName("name") String filename,
     final ExecutionContext context
 ) {
     context.getLogger().info("Name: " + name + " Size: " + content.length + " bytes");
 }

```


## <a name="trigger---attributes"></a>Trigger - attributi

Nelle [librerie di classi](functions-dotnet-class-library.md) usare i seguenti attributi per configurare un trigger di BLOB:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

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

  Per un esempio completo, vedere [Trigger - esempio in C#](#trigger---c-example).

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

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `BlobTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blobTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. Le eccezioni sono indicate nella sezione [usage](#trigger---usage). |
|**nome** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione. | 
|**path** | **BlobPath** |Contenitore da monitorare.  Può essere un [modello di nome per il BLOB](#trigger-blob-name-patterns). | 
|**connessione** | **Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione solo BLOB](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - uso

In C# e nello script C# è possibile usare i tipi di parametro seguenti per il BLOB di attivazione:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Un oggetto POCO serializzabile come JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> richiede l'associazione "inout" `direction` in *function.json* o `FileAccess.ReadWrite` in una libreria di classi C#.

Se si prova a eseguire l'associazione a uno dei tipi di Storage SDK e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

L'associazione a `string`, `Byte[]` o POCO è consigliabile solo se le dimensioni del BLOB sono ridotte, in quanto l'intero contenuto del BLOB viene caricato in memoria. In genere, è preferibile usare un tipo `Stream` o `CloudBlockBlob`. Per altre informazioni, vedere [Concorrenza e utilizzo della memoria](#trigger---concurrency-and-memory-usage) più avanti in questo articolo.

In JavaScript si accede a dati del BLOB di input usando `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Trigger - modelli di nome dei BLOB

È possibile specificare un modello di nome di BLOB nella proprietà `path` in *function.json* o nel costruttore dell'attributo `BlobTrigger`. Il modello di nome può essere un'[espressione di filtro o di associazione](functions-triggers-bindings.md#binding-expressions-and-patterns). Per esempi, vedere le sezioni seguenti.

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

Il trigger del BLOB contiene diverse proprietà di metadati. Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Questi valori hanno la stessa semantica del tipo [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

|Proprietà  |type  |DESCRIZIONE  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Percorso del BLOB trigger.|
|`Uri`|`System.Uri`|L'URI del BLOB per la posizione primaria.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Le proprietà di sistema del BLOB. |
|`Metadata` |`IDictionary<string,string>`|I metadati definiti dall'utente per il BLOB.|

Ad esempio, gli esempi seguenti di script C# e JavaScript registrano il percorso nel BLOB di attivazione, incluso il contenitore:

```csharp
public static void Run(string myBlob, string blobTrigger, TraceWriter log)
{
    log.Info($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

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

## <a name="trigger---concurrency-and-memory-usage"></a>Trigger: concorrenza e utilizzo della memoria

Il trigger del BLOB usa una un servizio di accodamento interno, quindi il numero massimo di chiamate di funzione simultanee è controllato dalla [configurazione delle code in host.json](functions-host-json.md#queues). Le impostazioni predefinite limitano la concorrenza a 24 chiamate. Questo limite si applica separatamente a ciascuna funzione che usa un trigger di BLOB.

[Il piano di consumo](functions-scale.md#how-the-consumption-plan-works) limita un'app per le funzioni in una macchina virtuale (VM) a 1,5 GB di memoria. La memoria viene usata da ogni istanza della funzione attualmente in esecuzione e dal runtime di funzioni stesso. Se una funzione di attivazione del BLOB carica l'intero BLOB nella memoria, la memoria massima usata da tale funzione solo per i BLOB è pari a 24 * le dimensioni massime del BLOB. Ad esempio, un'app per le funzioni con tre funzioni attivate dal BLOB e le impostazioni predefinite avrebbe una concorrenza per macchina virtuale massima pari a 3 * 24 = 72 chiamate di funzione.

Le funzioni JavaScript caricano nella memoria l'intero BLOB e le funzioni C# eseguono questa operazione se si esegue l'associazione a `string`, `Byte[]` o POCO.

## <a name="trigger---polling"></a>Trigger - polling

Se il contenitore BLOB monitorato contiene più di 10.000 BLOB, il runtime di Funzioni analizza i file di log per cercare i BLOB nuovi o modificati. Questo processo può causare ritardi. È possibile quindi che una funzione non venga attivata per diversi minuti o più dopo la creazione del BLOB. Per di più [i log di archiviazione vengono creati in base al principio del "massimo sforzo"](/rest/api/storageservices/About-Storage-Analytics-Logging). Non è garantito che tutti gli eventi vengano acquisiti. In alcune condizioni, l'acquisizione dei log può non riuscire. Se è necessaria un'elaborazione dei BLOB più veloce o affidabile, valutare la possibilità di creare un [messaggio della coda](../storage/queues/storage-dotnet-how-to-use-queues.md) quando si crea il BLOB. Usare quindi un [trigger di coda](functions-bindings-storage-queue.md) invece di un trigger di BLOB per elaborare il BLOB. Un'altra opzione consiste nell'usare Griglia di eventi. Vedere l'esercitazione [Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Input

Usare un'associazione di input dell'archiviazione BLOB per leggere i BLOB.

## <a name="input---example"></a>Input - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#input---c-example)
* [Script C# (file con estensione csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example"></a>Input - esempio in C#

L'esempio seguente è una [funzione in C#](functions-dotnet-class-library.md) che usa un trigger della coda e un'associazione di input BLOB. Il messaggio della coda contiene il nome del BLOB e la funzione registra la dimensione del BLOB.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```        

### <a name="input---c-script-example"></a>Input - esempio di script C#

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
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Input - esempio JavaScript

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

### <a name="input---java-example"></a>Input - Esempio di Java

L'esempio seguente è una funzione in Java che usa un trigger della coda e un'associazione di input BLOB. Il messaggio della coda contiene il nome del BLOB e la funzione registra la dimensione del BLOB.

```java
@FunctionName("getBlobSize")
@StorageAccount("AzureWebJobsStorage")
public void blobSize(@QueueTrigger(name = "filename",  queueName = "myqueue-items") String filename,
                    @BlobInput(name = "file", dataType = "binary", path = "samples-workitems/{queueTrigger") byte[] content,
       final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
 }
 ```

  Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare `@BlobInput` l'annotazione per i parametri il cui valore deriva da BLOB.  Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`. 


## <a name="input---attributes"></a>Input - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

Il costruttore dell'attributo accetta il percorso del BLOB e un parametro `FileAccess` che indica la lettura o la scrittura, come illustrato nell'esempio seguente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

È possibile impostare la proprietà `Connection` per specificare l'account di archiviazione da usare, come illustrato nell'esempio seguente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere [Trigger - attributi](#trigger---attributes).

## <a name="input---configuration"></a>Input - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Blob`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blob`. |
|**direction** | n/d | Il valore deve essere impostato su `in`. Le eccezioni sono indicate nella sezione [usage](#input---usage). |
|**nome** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione.|
|**path** |**BlobPath** | Percorso del BLOB. | 
|**connessione** |**Connection**| Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione solo BLOB](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|n/d | **Accedere** | Indica se eseguire la lettura o la scrittura. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Input - uso

In C# e nello script C# è possibile usare i tipi di parametro seguenti per l'associazione di input BLOB:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> richiede l'associazione "inout" `direction` in *function.json* o `FileAccess.ReadWrite` in una libreria di classi C#.

Se si prova a eseguire l'associazione a uno dei tipi di Storage SDK e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

L'associazione a `string` o `Byte[]` è consigliabile solo se le dimensioni del BLOB sono ridotte, in quanto l'intero contenuto del BLOB viene caricato in memoria. In genere, è preferibile usare un tipo `Stream` o `CloudBlockBlob`. Per altre informazioni, vedere [Concorrenza e utilizzo della memoria](#trigger---concurrency-and-memory-usage) più indietro in questo articolo.

In JavaScript si accede a dati del BLOB di input usando `context.bindings.<name from function.json>`.

## <a name="output"></a>Output

Usare associazioni di output di archiviazione BLOB per scrivere i BLOB.

## <a name="output---example"></a>Output - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#output---c-example)
* [Script C# (file con estensione csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Output - esempio in C#

L'esempio seguente è una [funzione in C#](functions-dotnet-class-library.md) che usa un trigger di BLOB di input e due associazioni di BLOB di output. La funzione viene attivata dalla creazione di un BLOB dell'immagine nel contenitore *sample-images*. Crea copie di piccole e medie dimensioni del BLOB dell'immagine. 

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

### <a name="output---c-script-example"></a>Output - esempio di script C#

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
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Output - esempio JavaScript

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

### <a name="output---java-example"></a>Output - Esempio Java

L'esempio seguente mostra un input BLOB e le associazioni in una funzione Java di output. La funzione crea una copia di un BLOB di testo. La funzione viene attivata da un messaggio della coda che contiene il nome del BLOB da copiare. Il nuovo BLOB è denominato {originalblobname}-Copy

```java
@FunctionName("copyTextBlob")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "target", path = "samples-workitems/{queueTrigger}-Copy")
public String blobCopy(
    @QueueTrigger(name = "filename", queueName = "myqueue-items") String filename,
    @BlobInput(name = "source", path = "samples-workitems/{queueTrigger}") String content ) {
      return content;
 }
 ```

 Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime) usare `@BlobOutput` l'annotazione per i parametri di funzione il cui valore viene scritto in un oggetto nell’archiviazione BLOB.  Il tipo di parametro deve essere `OutputBinding<T>`, dove T corrisponde a un qualsiasi tipo Java nativo di un oggetto POJO.


## <a name="output---attributes"></a>Output - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

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

Per un esempio completo, vedere [Output - esempio in C#](#output---c-example).

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere [Trigger - attributi](#trigger---attributes).

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Blob`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blob`. |
|**direction** | n/d | Deve essere impostato su `out` per un'associazione di output. Le eccezioni sono indicate nella sezione [usage](#output---usage). |
|**nome** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione.  Impostare su `$return` per fare riferimento al valore restituito della funzione.|
|**path** |**BlobPath** | Percorso del BLOB. | 
|**connessione** |**Connection**| Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Ad esempio, se si imposta `connection` su "MyStorage", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione solo BLOB](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|n/d | **Accedere** | Indica se eseguire la lettura o la scrittura. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Output - uso

In C# e negli script C#, è possibile eseguire l'associazione ai seguenti tipi per la scrittura dei BLOB:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> richiede l'associazione "in" `direction` in *function.json* o `FileAccess.Read` in una libreria di classi C#. Tuttavia, è possibile usare l'oggetto contenitore che il runtime fornisce per scrivere operazioni, come ad esempio il caricamento di BLOB nel contenitore.

<sup>2</sup> richiede l'associazione "inout" `direction` in *function.json* o `FileAccess.ReadWrite` in una libreria di classi C#.

Se si prova a eseguire l'associazione a uno dei tipi di Storage SDK e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

Nelle funzioni asincrone usare il valore restituito o `IAsyncCollector` anziché un parametro `out`.

L'associazione a `string` o `Byte[]` è consigliabile solo se le dimensioni del BLOB sono ridotte, in quanto l'intero contenuto del BLOB viene caricato in memoria. In genere, è preferibile usare un tipo `Stream` o `CloudBlockBlob`. Per altre informazioni, vedere [Concorrenza e utilizzo della memoria](#trigger---concurrency-and-memory-usage) più indietro in questo articolo.


In JavaScript si accede a dati del BLOB di input usando `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione |  riferimento |
|---|---|
| BLOB | [Codici di errore BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Table, Queue |  [Codici di errore di archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Risoluzione dei problemi](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Passare a una guida introduttiva che usa un trigger per l'archiviazione BLOB](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
