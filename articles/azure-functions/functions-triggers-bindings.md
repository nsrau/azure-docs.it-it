---
title: I trigger e le associazioni in funzioni di Azure
description: Informazioni su come usare trigger e associazioni in Funzioni di Azure per connettere l'esecuzione del codice a eventi online e servizi basati su cloud.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 92194b0d54de1271580a237e16e652b761b4d6d4
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure

In questo articolo offre una panoramica concettuale di trigger e le associazioni in funzioni di Azure. Funzionalità comuni a tutte le associazioni e tutte le lingue supportate sono descritte di seguito.

## <a name="overview"></a>Panoramica

Un *trigger* definisce come viene richiamata una funzione. Una funzione deve avere esattamente un trigger. I trigger hanno dei dati associati, ovvero in genere il payload che ha attivato la funzione.

Le *associazioni* di input e output forniscono una modalità dichiarativa per connettersi ai dati dall'interno del codice. Le associazioni sono facoltative e una funzione può avere più associazioni di input e output. 

I trigger e le associazioni consentono di evitare hardcoded i dettagli dei servizi che si sta lavorando. Funzione si riceve dati (ad esempio, il contenuto di un messaggio della coda) in parametri di funzione. Invio di dati (ad esempio, per creare un messaggio nella coda) utilizzando il valore restituito della funzione, un `out` parametro, o un [oggetto raccolta dati](functions-reference-csharp.md#writing-multiple-output-values).

Quando si sviluppano le funzioni utilizzando il portale di Azure, trigger e le associazioni vengono configurate in un *function.json* file. Il portale fornisce un'interfaccia utente per questa configurazione, ma è possibile modificare il file direttamente modificando nel **editor avanzato**.

Quando si sviluppano le funzioni con Visual Studio per creare una libreria di classi, configurare i trigger e le associazioni dichiarando i metodi e i parametri con attributi.

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Esempio: trigger di coda e tabella di associazione di output

Si supponga di che voler scrivere una nuova riga per l'archiviazione tabelle di Azure ogni volta che viene visualizzato un messaggio nuovo in archiviazione di Accodamento di Azure. Questo scenario può essere implementato utilizzando una coda di Azure trigger di archiviazione e un archivio tabelle di Azure di associazione di output. 

Ecco un *function.json* file per questo scenario. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Il primo elemento di `bindings` matrice è il trigger di archiviazione della coda. Il `type` e `direction` proprietà consentono di identificare il trigger. Il `name` proprietà identifica il parametro della funzione che riceverà il contenuto del messaggio della coda. Il nome della coda per il monitoraggio è in `queueName`, e la stringa di connessione è in base alle impostazioni app identificato da `connection`.

Il secondo elemento di `bindings` matrice è l'archiviazione tabelle Azure associazione di output. Il `type` e `direction` proprietà consentono di identificare l'associazione. Il `name` proprietà specifica come la funzione fornirà la nuova riga di tabella, in questo caso utilizzando il valore restituito della funzione. Il nome della tabella è in `tableName`, e la stringa di connessione è in base alle impostazioni app identificato da `connection`.

Per visualizzare e modificare i contenuti della *funzione .json* nel portale di Azure, fare clic sull'opzione **Editor avanzato** nella scheda **Integrazione** della funzione.

> [!NOTE]
> Il valore di `connection` è il nome di un'impostazione di app che contiene la stringa di connessione, non la stringa di connessione. Associazioni utilizzano connessione stringhe archiviate nelle impostazioni dell'app per applicare le migliori procedure consigliate che *function.json* non contiene i segreti di servizio.

Ecco il codice di script c# che funziona con questo trigger e l'associazione. Si noti che il nome del parametro che fornisce il contenuto del messaggio della coda è `order`; questo nome è obbligatorio poiché il `name` nel valore della proprietà *function.json* è`order` 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Il file di function.json stesso può essere utilizzato con una funzione JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

In una libreria di classi, il trigger stesso e le informazioni di associazione &mdash; coda e tabella, nomi di account di archiviazione, i parametri per l'input e output di funzione &mdash; viene fornito dagli attributi:

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>Direzione dell'associazione

Tutti i trigger e le associazioni hanno una proprietà `direction` nel file *function.json*:

- Per i trigger, la direzione è sempre `in`
- Le associazioni di input e di output usano `in` e `out`
- Alcune associazioni supportano una direzione speciale `inout`. Se si usa `inout`, solo l'**Editor avanzato** è disponibile nelle scheda **Integrazione**.

Quando si utilizza [attributi in una libreria di classi](functions-dotnet-class-library.md) per configurare i trigger e le associazioni, la direzione è fornita in un costruttore di attributo o dedotto dal tipo di parametro.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Uso del tipo restituito della funzione per restituire un singolo output

Nell'esempio precedente viene illustrato come utilizzare il valore restituito di funzione per fornire output di un'associazione, che viene specificata in *function.json* utilizzando il valore speciale `$return` per il `name` proprietà. (Questo è solo supportato nelle lingue che presentano un valore restituito, ad esempio script c#, JavaScript e F #). Se una funzione dispone di più associazioni di output, usare `$return` per una sola delle associazioni di output. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Negli esempi seguenti Mostra come visualizzare i tipi utilizzati con le associazioni di output in c# script, JavaScript e F #.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>Proprietà Binding dataType

In .NET, utilizzare il tipo di parametro per definire il tipo di dati per dati di input. Usare ad esempio `string` da associare al testo di un trigger di coda, una matrice di byte da leggere in formato binario e un tipo personalizzato per deserializzare un oggetto POCO.

Per le lingue che vengono digitate in modo dinamico, ad esempio JavaScript, usare il `dataType` proprietà il *function.json* file. Ad esempio, per leggere il contenuto di una richiesta HTTP in formato binario, impostare `dataType` a `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Altre opzioni per `dataType` sono `stream` e `string`.

## <a name="resolving-app-settings"></a>Risoluzione di impostazioni app

Come procedura consigliata, i segreti e le stringhe di connessione devono essere gestiti tramite le impostazioni dell'app, invece dei file di configurazione. Ciò limita l'accesso a questi segreti e rende sicuro archiviare *function.json* in un repository di controllo sorgente pubblico.

Le impostazioni dell'app sono utili anche ogni volta che si desidera modificare la configurazione in base all'ambiente. Ad esempio, in un ambiente di test, si potrebbe voler monitorare un contenitore di archiviazione BLOB o di coda diverso.

Le impostazioni dell'app vengono risolte ogni volta che un valore è racchiuso tra simboli di percentuale, ad esempio `%MyAppSetting%`. Si noti che la proprietà `connection` di trigger e associazioni è un caso speciale e risolve automaticamente i valori come impostazioni dell'app. 

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

È possibile utilizzare lo stesso approccio nelle librerie di classi:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-metadata-properties"></a>Proprietà dei metadati di trigger

Oltre al payload dei dati offerto da un trigger (ad esempio, il messaggio di coda che ha attivato una funzione), molti trigger forniscono i valori dei metadati aggiuntivi. Questi valori possono essere usati come parametri di input in C# e F# o come proprietà nell'oggetto `context.bindings` in JavaScript. 

Ad esempio, un trigger di archiviazione della coda di Azure supporta le proprietà seguenti:

* QueueTrigge: attivazione del contenuto del messaggio, se una stringa valida
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

Questi valori di metadati sono accessibili in *function.json* proprietà del file. Si supponga, ad esempio, si utilizza un trigger di coda e il messaggio della coda contiene il nome di un blob da leggere. Nel *function.json* file, è possibile utilizzare `queueTrigger` proprietà dei metadati del blob `path` proprietà, come illustrato nell'esempio seguente:

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

Dettagli delle proprietà dei metadati per tutti i trigger sono descritti nell'articolo di riferimento corrispondente. Per un esempio, vedere [i metadati della coda trigger](functions-bindings-storage-queue.md#trigger---message-metadata). La documentazione è disponibile anche nella scheda **Integrazione** del portale nella sezione **Documentazione** sotto l'area di configurazione dell'associazione.  

## <a name="binding-expressions-and-patterns"></a>Modelli ed espressioni di associazione

Una delle funzionalità più potenti di trigger e associazioni sono le *espressioni di associazione*. Nella configurazione per un'associazione, è possibile definire espressioni di modello che possono quindi essere usate in altri binding o il codice. Anche i metadati di trigger possono essere utilizzati nelle espressioni di associazione, come illustrato nella sezione precedente.

Ad esempio, si supponga di voler ridimensionare le immagini in un contenitore di archiviazione blob specifico, simile al **dimensioni immagine** modello il **nuova funzione** pagina del portale di Azure (vedere il **esempi**  scenario). 

Ecco la definizione di *function.json*:

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

Si noti che il `filename` parametro viene utilizzato nella definizione del trigger blob sia il blob di associazione di output. Questo parametro può essere usato anche nel codice della funzione.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

La stessa possibilità di utilizzare modelli e le espressioni di associazione si applica agli attributi nelle librerie di classi. Di seguito è ad esempio, una funzione in una libreria di classi di ridimensionamento delle immagini:

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
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

### <a name="create-guids"></a>Crea GUID

Il `{rand-guid}` espressione di associazione crea un GUID. Nell'esempio seguente viene usato un GUID per creare un nome univoco di blob: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Ora corrente

L'espressione di associazione `DateTime` si risolve in `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>Associare alle proprietà di input personalizzata

Le espressioni di associazione possono anche fare riferimento alle proprietà definite nel payload del trigger stesso. Ad esempio, si potrebbe voler associare in modo dinamico ad un file di archiviazione BLOB un filename fornito da un webhook.

Ad esempio, la seguente *function.json* usa una proprietà denominata `BlobName` dal payload del trigger:

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

A tale scopo in C# e F #, è necessario definire un POCO che definisce i campi che saranno deserializzati nel payload del trigger.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

In JavaScript, viene eseguita automaticamente la deserializzazione di JSON ed è possibile usare direttamente le proprietà.

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

## <a name="configuring-binding-data-at-runtime"></a>Configurazione dell'associazione di dati in fase di runtime

In c# e altri linguaggi .NET, è possibile utilizzare un modello di associazione imperativo, anziché le associazioni dichiarative *function.json* e attributi. L'associazione imperativa è utile quando i parametri di associazione devono essere calcolati in fase di runtime invece che in fase di progettazione. Per altre informazioni, vedere [Associazione in fase di runtime tramite le associazioni imperative](functions-reference-csharp.md#imperative-bindings) nel riferimento per sviluppatori C#.

## <a name="functionjson-file-schema"></a>schema di file Function.JSON

Il *function.json* dello schema di file è disponibile all'indirizzo [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito:

- [HTTP e webhook](functions-bindings-http-webhook.md)
- [Timer](functions-bindings-timer.md)
- [Archiviazione code](functions-bindings-storage-queue.md)
- [Archiviazione BLOB](functions-bindings-storage-blob.md)
- [Archiviazione tabelle](functions-bindings-storage-table.md)
- [Hub eventi](functions-bindings-event-hubs.md)
- [Bus di servizio](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-documentdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Hub di notifica di Azure](functions-bindings-notification-hubs.md)
- [App per dispositivi mobili](functions-bindings-mobile-apps.md)
- [File esterno](functions-bindings-external-file.md)
