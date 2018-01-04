---
title: Usare trigger e associazioni in Funzioni di Azure
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
ms.openlocfilehash: ab5550ee0c057c9abc4b706929d780a495aaff65
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2017
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure
Funzioni di Azure consente di scrivere codice in risposta agli eventi in Azure e in altri servizi, tramite *trigger* e *associazioni*. In questo articolo viene fornita una panoramica concettuale di trigger e associazioni per tutti i linguaggi di programmazione supportati. Le funzionalità comuni a tutte le associazioni sono descritte di seguito.

## <a name="overview"></a>Panoramica

I trigger e le associazioni sono un modo dichiarativo per definire come viene invocata una funzione e con quali dati opera. Un *trigger* definisce come viene richiamata una funzione. Una funzione deve avere esattamente un trigger. I trigger hanno dei dati associati, ovvero in genere il payload che ha attivato la funzione.

Le *associazioni* di input e output forniscono una modalità dichiarativa per connettersi ai dati dall'interno del codice. Analogamente ai trigger, specificare le stringhe di connessione e le altre proprietà nella configurazione della funzione. Le associazioni sono facoltative e una funzione può avere più associazioni di input e output. 

Usando i trigger e le associazioni, è possibile scrivere codice più generico e non impostare come hardcoded i dettagli dei servizi con cui interagisce. I dati provenienti dai servizi diventano semplicemente valori di input per il codice della funzione. Per restituire i dati a un altro servizio (ad esempio la creazione di una nuova riga nell'archiviazione tabelle di Azure), usare il valore restituito del metodo. In alternativa, se è necessario restituire più valori, usare un oggetto di supporto. I trigger e le associazioni presentano una proprietà **nome**, che è un identificatore che si usa nel codice per accedere all'associazione.

È possibile configurare i trigger e le associazioni nella scheda **Integrazione** nel portale delle Funzioni di Azure. Dietro le quinte, l'interfaccia utente modifica un file denominato file *function.json* nella directory della funzione. È possibile modificare questo file passando all'**Editor avanzato**.

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Esempio: trigger di coda e tabella di associazione di output

Si supponga di voler scrivere una nuova riga in archiviazione tabelle di Azure ogni volta che viene visualizzato un messaggio nuovo in archiviazione code di Azure. Questo scenario può essere implementato tramite un trigger della coda di Azure e un'associazione di output di archiviazione tabelle di Azure. 

Un trigger di archiviazione code di Azure richiede le informazioni seguenti nella scheda **Integrazione**:

* Il nome dell'impostazione dell'app che contiene la stringa di connessione dell'account di archiviazione di Azure per archiviazione code di Azure
* Il nome della coda
* L'identificatore nel codice per leggere il contenuto del messaggio in coda, ad esempio `order`.

Per scrivere in archiviazione tabelle di Azure, usare un'associazione di output con i dettagli seguenti:

* Il nome dell'impostazione dell'app che contiene la stringa di connessione dell'account di archiviazione di Azure per archiviazione tabelle di Azure
* Il nome della tabella
* L'identificatore nel codice per creare elementi di output o il valore restituito dalla funzione.

Le associazioni usano valori di stringhe di connessione archiviati nelle impostazioni dell'app per applicare la procedura consigliata in base alla quale *function.json* non contiene segreti del servizio ma semplicemente i nomi delle impostazioni dell'app.

Quindi, usare gli identificatori che vengono forniti per l'integrazione con archiviazione di Azure nel codice.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
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

Ecco il *function.json* che corrisponde al codice precedente. Si noti che si può usare la stessa configurazione, indipendentemente dal linguaggio di implementazione della funzione.

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
Per visualizzare e modificare i contenuti della *funzione .json* nel portale di Azure, fare clic sull'opzione **Editor avanzato** nella scheda **Integrazione** della funzione.

Per altri esempi di codice e informazioni dettagliate sull'integrazione con archiviazione di Azure, vedere [Associazioni del BLOB del servizio di archiviazione di Funzioni di Azure](functions-bindings-storage.md).

## <a name="binding-direction"></a>Direzione dell'associazione

Tutti i trigger e le associazioni hanno una proprietà `direction` nel file *function.json*:

- Per i trigger, la direzione è sempre `in`
- Le associazioni di input e di output usano `in` e `out`
- Alcune associazioni supportano una direzione speciale `inout`. Se si usa `inout`, solo l'**Editor avanzato** è disponibile nelle scheda **Integrazione**.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Uso del tipo restituito della funzione per restituire un singolo output

Nell'esempio precedente viene illustrato come usare il valore restituito della funzione per offrire l'output a un'associazione, che si può fare tramite il parametro nome speciale `$return`. (Questa opzione è supportata solo nei linguaggi che dispongono di un valore restituito, ad esempio C#, JavaScript e F#). Se una funzione dispone di più associazioni di output, usare `$return` per una sola delle associazioni di output. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Gli esempi seguenti mostrano come i tipi restituiti vengono usati con le associazioni di output in C#, JavaScript e F#.

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

In .NET usare i tipi per definire il tipo di dati per i dati di input. Usare ad esempio `string` da associare al testo di un trigger di coda, una matrice di byte da leggere in formato binario e un tipo personalizzato per deserializzare un oggetto POCO.

Per le lingue che vengono digitate in modo dinamico, ad esempio JavaScript, usare la proprietà `dataType` nella definizione di associazione. Ad esempio, per leggere il contenuto di una richiesta HTTP in formato binario, usare il tipo `binary`:

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

## <a name="trigger-metadata-properties"></a>Proprietà dei metadati di trigger

Oltre al payload dei dati offerto da un trigger (ad esempio, il messaggio di coda che ha attivato una funzione), molti trigger forniscono i valori dei metadati aggiuntivi. Questi valori possono essere usati come parametri di input in C# e F# o come proprietà nell'oggetto `context.bindings` in JavaScript. 

Un trigger di archiviazione code di Azure ad esempio supporta le proprietà seguenti:

* QueueTrigge: attivazione del contenuto del messaggio, se una stringa valida
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

I dettagli delle proprietà dei metadati per ogni trigger sono descritti nell'argomento di riferimento corrispondente. La documentazione è disponibile anche nella scheda **Integrazione** del portale nella sezione **Documentazione** sotto l'area di configurazione dell'associazione.  

Poiché ad esempio i trigger BLOB presentano alcuni ritardi, è possibile usare un trigger di coda per l'esecuzione della funzione (vedere [Trigger del BLOB del servizio di archiviazione](functions-bindings-storage-blob.md#trigger)). Il messaggio della coda contiene il filename del BLOB da attivare. Con l'uso della proprietà dei metadati `queueTrigger`, è possibile specificareper intero questo comportamento nella configurazione, invece che nel codice.

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

Le proprietà dei metadati da un trigger possono anche essere usate in una *espressione dell'associazione* per un'altra associazione, come descritto nella sezione seguente.

## <a name="binding-expressions-and-patterns"></a>Modelli ed espressioni di associazione

Una delle funzionalità più potenti di trigger e associazioni sono le *espressioni di associazione*. All'interno dell'associazione, è possibile definire delle espressioni di modello che possono quindi essere usate in altre associazioni o nel codice. I metadati del trigger possono essere usati anche nelle espressioni di associazione, come illustrato nell'esempio nella sezione precedente.

Ad esempio, si supponga che si desidera ridimensionare le immagini in un contenitore di archiviazione BLOB specifico, simile al modello di **ridimensionamento immagine** nella pagina **Nuova funzione**. Passare a **Nuova funzione** -> Linguaggio **C#** -> Scenario **Esempi** -> **ImageResizer-CSharp**. 

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

Si noti che il parametro `filename` viene usato nella definizione del trigger BLOB e anche nell'associazione output di BLOB. Questo parametro può essere usato anche nel codice della funzione.

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


### <a name="random-guids"></a>GUID casuali
Funzioni di Azure fornisce una sintassi utile per la generazione di GUID nelle associazioni, tramite l'espressione dell'associazione `{rand-guid}`. Nell'esempio seguente questa operazione viene usata per generare un nome del BLOB univoco: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Ora corrente

È possibile usare l'espressione di associazione `DateTime`, che viene risolta in `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>Associare le proprietà di input personalizzate in un'espressione di associazione

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

In C# e altri linguaggi .NET, è possibile usare un metodo di associazione imperativa anziché dichiarativa in *function.json*. L'associazione imperativa è utile quando i parametri di associazione devono essere calcolati in fase di runtime invece che in fase di progettazione. Per altre informazioni, vedere [Associazione in fase di runtime tramite le associazioni imperative](functions-reference-csharp.md#imperative-bindings) nel riferimento per sviluppatori C#.

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
