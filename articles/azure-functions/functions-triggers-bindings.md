---
title: Trigger e associazioni in Funzioni di Azure
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
ms.openlocfilehash: a122271b5fdffd9db33a7dca5908e15f002041d7
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure

In questo articolo viene fornita una panoramica concettuale di trigger e associazioni in Funzioni di Azure. Le funzionalità comuni a tutte le associazioni e a tutti i linguaggi supportati sono descritte di seguito.

## <a name="overview"></a>Panoramica

Un *trigger* definisce come viene richiamata una funzione. Una funzione deve avere esattamente un trigger. I trigger hanno dei dati associati, ovvero in genere il payload che ha attivato la funzione.

Le *associazioni* di input e output forniscono una modalità dichiarativa per connettersi ai dati dall'interno del codice. Le associazioni sono facoltative e una funzione può avere più associazioni di input e output. 

I trigger e le associazioni permettono di evitare di inserire dettagli hardcoded dei servizi usati. La funzione riceve i dati, ad esempio il contenuto di un messaggio della coda, nei parametri della funzione. I dati vengono inviati, ad esempio per creare un messaggio della coda, usando il valore restituito della funzione, un parametro `out` o un [oggetto agente di raccolta](functions-reference-csharp.md#writing-multiple-output-values).

Quando si sviluppano funzioni tramite il portale di Azure, i trigger e le associazioni sono configurati in un file *function.json*. Il portale fornisce un'interfaccia utente per questa configurazione, ma è possibile modificare direttamente il file passando all'**editor avanzato**.

Se le funzioni vengono sviluppate in Visual Studio per creare una libreria di classi, i trigger e le associazioni vengono configurati tramite la decorazione di metodi e parametri con attributi.

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Esempio: trigger di coda e tabella di associazione di output

Si supponga di voler scrivere una nuova riga in archiviazione tabelle di Azure ogni volta che viene visualizzato un messaggio nuovo in archiviazione code di Azure. Questo scenario può essere implementato tramite un trigger di archiviazione code di Azure e un'associazione di output di archiviazione tabelle di Azure. 

Ecco un file *function.json* per questo scenario. 

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

Il primo elemento nella matrice `bindings` è il trigger di archiviazione code. Le proprietà `type` e `direction` identificano il trigger. La proprietà `name` identifica il parametro di funzione che riceverà il contenuto del messaggio della coda. Il nome della coda da monitorare si trova in `queueName` e la stringa di connessione si trova nell'impostazione app identificata da `connection`.

Il secondo elemento nella matrice `bindings` è l'associazione di output dell'archiviazione tabelle di Azure. Le proprietà `type` e `direction` identificano l'associazione. La proprietà `name` specifica in che modo la funzionalità fornirà la nuova riga della tabella, in questo caso usando il valore restituito della funzione. Il nome della tabella si trova in `tableName` e la stringa di connessione si trova nell'impostazione app identificata da `connection`.

Per visualizzare e modificare i contenuti della *funzione .json* nel portale di Azure, fare clic sull'opzione **Editor avanzato** nella scheda **Integrazione** della funzione.

> [!NOTE]
> Il valore di `connection` corrisponde al nome di un'impostazione app che contiene la stringa di connessione e non alla stringa di connessione stessa. Le associazioni usano stringhe di connessione archiviate nelle impostazioni app per imporre la procedura consigliata che prevede che il file *function.json* non contenga i segreti del servizio.

Ecco il codice dello script C# per questo trigger e questa associazione. Si noti che il nome del parametro che fornisce il contenuto del messaggio della coda è `order`. Questo nome è obbligatorio perché il valore della proprietà `name` nel file *function.json* è `order` 

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

Lo stesso file function.json può essere usato con una funzione JavaScript:

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

In una libreria di classi, le stesse informazioni per il trigger e l'associazione, ovvero nomi di coda e tabella, account di archiviazione, parametri di funzione per input e output, sono forniti dagli attributi:

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

Quando si usano gli [attributi in una libreria di classi](functions-dotnet-class-library.md) per configurare i trigger e le associazioni, la direzione viene specificata in un costruttore di attributo o dedotta dal tipo di parametro.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Uso del tipo restituito della funzione per restituire un singolo output

L'esempio precedente illustra come usare il valore restituito della funzione per fornire l'output a un'associazione, che viene specificato nel file *function.json* usando il valore speciale `$return` per la proprietà `name`. Questa opzione è supportata solo nei linguaggi che presentano un valore restituito, ad esempio script C#, JavaScript e F#. Se una funzione dispone di più associazioni di output, usare `$return` per una sola delle associazioni di output. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Gli esempi seguenti mostrano come vengono usati i tipi restituiti con le associazioni di output nello script C#, in JavaScript e F#.

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

In .NET usare il tipo di parametro per definire il tipo di dati per i dati di input. Usare ad esempio `string` da associare al testo di un trigger di coda, una matrice di byte da leggere in formato binario e un tipo personalizzato per deserializzare un oggetto POCO.

Per i linguaggi tipizzati in modo dinamico, ad esempio JavaScript, usare la proprietà `dataType` nel file *function.json*. Ad esempio, per eseguire la lettura del contenuto di una richiesta HTTP in formato binario, impostare `dataType` su `binary`:

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

È possibile usare lo stesso approccio nelle librerie di classi:

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

## <a name="binding-expressions-and-patterns"></a>Modelli ed espressioni di associazione

Una delle funzionalità più potenti di trigger e associazioni sono le *espressioni di associazione*. Nella configurazione per un'associazione è possibile definire espressioni di criteri che possono quindi essere usate in altre associazioni o nel codice. I metadati dei trigger possono essere usati anche nelle espressioni di associazione, come illustrato nell'esempio della sezione precedente.

Ad esempio, si supponga di voler ridimensionare le immagini in un contenitore di archiviazione BLOB specifico, simile al modello di **ridimensionamento immagine** nella pagina **Nuova funzione** del portale di Azure (vedere lo scenario **Esempi**). 

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

Si noti che il parametro `filename` viene usato nella definizione del trigger del BLOB e anche nell'associazione di output del BLOB. Questo parametro può essere usato anche nel codice della funzione.

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

La stessa possibilità di usare criteri ed espressioni di associazione si applica agli attributi nelle librerie di classi. Ad esempio, ecco una funzione di ridimensionamento immagini in una libreria di classi:

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

L'espressione di associazione `{rand-guid}` crea un GUID. Nell'esempio seguente viene usato un GUID per creare un nome BLOB univoco: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Ora corrente

L'espressione di associazione `DateTime` viene risolta in `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>Eseguire l'associazione a proprietà di input personalizzate

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

In C# e altri linguaggi .NET, è possibile usare criteri di associazione imperativa, rispetto alle associazioni dichiarative nel file *function.json* e negli attributi. L'associazione imperativa è utile quando i parametri di associazione devono essere calcolati in fase di runtime invece che in fase di progettazione. Per altre informazioni, vedere [Associazione in fase di runtime tramite le associazioni imperative](functions-reference-csharp.md#imperative-bindings) nel riferimento per sviluppatori C#.

## <a name="functionjson-file-schema"></a>Schema del file function.json

Lo schema del file *function.json* è disponibile all'indirizzo [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito:

- [HTTP e webhook](functions-bindings-http-webhook.md)
- [Timer](functions-bindings-timer.md)
- [Archiviazione code](functions-bindings-storage-queue.md)
- [Archiviazione BLOB](functions-bindings-storage-blob.md)
- [Archiviazione tabelle](functions-bindings-storage-table.md)
- [Hub eventi](functions-bindings-event-hubs.md)
- [Bus di servizio](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Hub di notifica di Azure](functions-bindings-notification-hubs.md)
- [App per dispositivi mobili](functions-bindings-mobile-apps.md)
- [File esterno](functions-bindings-external-file.md)
