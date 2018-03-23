---
title: Trigger e associazioni in Funzioni di Azure
description: Informazioni su come usare trigger e associazioni in Funzioni di Azure per connettere l'esecuzione del codice a eventi online e servizi basati su cloud.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/07/2018
ms.author: glenga
ms.openlocfilehash: 559cfee1a8116703371a5641cf4534b7ad6f7578
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
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

## <a name="register-binding-extensions"></a>Registrare le estensioni delle associazioni

Nella versione 2.x del runtime di Funzioni di Azure è necessario registrare in modo esplicito le [estensioni delle associazioni](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) usate nell'app per le funzioni. 

Le estensioni vengono distribuite come pacchetti NuGet il cui nome inizia in genere con [microsoft.azure.webjobs.extensions](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions).  La modalità di installazione e registrazione delle estensioni delle associazioni dipende da come si sviluppano le funzioni: 

+ [A livello locale in C# con Visual Studio o VSCode](#precompiled-functions-c)
+ [A livello locale con gli strumenti di base di Funzioni di Azure](#local-development-azure-functions-core-tools)
+ [Nel portale di Azure](#azure-portal-development) 

Nella versione 2.x è presente un set di associazioni di base che non vengono fornite come estensioni. Non è necessario registrare le estensioni dei trigger e delle associazioni seguenti: HTTP, timer e Archiviazione di Azure. 

Per informazioni su come impostare un'app per le funzioni per l'uso della versione 2.x del runtime di Funzioni, vedere [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md). La versione 2.x del runtime di Funzioni è attualmente in anteprima. 

Le versioni dei pacchetti illustrate in questa sezione sono fornite solo a scopo esemplificativo. Controllare il [sito NuGet.org](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions) per stabilire quale versione di una determinata estensione è necessaria per le altre dipendenze dell'app per le funzioni.    

###  <a name="local-c-development-using-visual-studio-or-vs-code"></a>Sviluppo in C# a livello locale con Visual Studio o VSCode 

Quando si usa Visual Studio o Visual Studio Code per sviluppare funzioni in C# a livello locale, è sufficiente aggiungere il pacchetto NuGet dell'estensione. 

+ **Visual Studio**: usare gli strumenti di Gestione pacchetti NuGet. Il comando [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) seguente consente di installare l'estensione di Azure Cosmos DB dalla Console di Gestione pacchetti:

    ```
    Install-Package Microsoft.Azure.WebJobs.Extensions.CosmosDB -Version 3.0.0-beta6 
    ```
+ **Visual Studio Code**: è possibile installare i pacchetti dal prompt dei comandi usando il comando [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) nell'interfaccia della riga di comando di .NET, come indicato di seguito:

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
    ```

### <a name="local-development-azure-functions-core-tools"></a>Sviluppo a livello locale con gli strumenti di base di Funzioni di Azure

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

### <a name="azure-portal-development"></a>Sviluppo con il portale di Azure

Quando si crea una funzione o si aggiunge un'associazione a una funzione esistente, viene richiesto quando è necessario registrare l'estensione dell'associazione o del trigger aggiunto.   

Dopo che è stato visualizzato un avviso relativo alla specifica estensione installata, fare clic su **Installa** per registrare l'estensione. È necessario installare ogni estensione una sola volta per una determinata app per le funzioni. 

>[!Note] 
>In un piano a consumo il processo di installazione nel portale può richiedere fino a 10 minuti.

## <a name="example-trigger-and-binding"></a>Trigger e associazione di esempio

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

Il primo elemento nella matrice `bindings` è il trigger di archiviazione code. Le proprietà `type` e `direction` identificano il trigger. La proprietà `name` identifica il parametro della funzione che riceve il contenuto del messaggio della coda. Il nome della coda da monitorare si trova in `queueName` e la stringa di connessione si trova nell'impostazione app identificata da `connection`.

Il secondo elemento nella matrice `bindings` è l'associazione di output dell'archiviazione tabelle di Azure. Le proprietà `type` e `direction` identificano l'associazione. La proprietà `name` specifica in che modo la funzione fornisce la nuova riga di tabella, in questo caso usando il valore restituito della funzione. Il nome della tabella si trova in `tableName` e la stringa di connessione si trova nell'impostazione app identificata da `connection`.

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

In una libreria di classi le stesse informazioni sui trigger e sulle associazioni, ovvero i nomi di coda e tabella, gli account di archiviazione, i parametri di funzione per l'input e l'output, vengono fornite dagli attributi anziché da un file function.json. Ad esempio:

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

## <a name="using-the-function-return-value"></a>Uso del valore restituito della funzione

Nei linguaggi che hanno un valore restituito è possibile associare a tale valore un'associazione di output:

* In una libreria di classi C# applicare l'attributo dell'associazione di output al valore restituito del metodo.
* In altri linguaggi impostare la proprietà `name` in *function.json* su `$return`.

Se è necessario scrivere più elementi, usare un [oggetto agente di raccolta](functions-reference-csharp.md#writing-multiple-output-values) anziché il valore restituito. Se sono presenti più associazioni di output, usare il valore restituito per una sola di tali associazioni.

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#c-example)
* [Script C# (file con estensione csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Esempio in C#

Di seguito è riportato il codice C# che usa il valore restituito per un'associazione di output, seguito da un esempio asincrono:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Esempio di script C#

Di seguito è riportata l'associazione di output nel file *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Di seguito è riportato il codice di script C#, seguito da un esempio asincrono:

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>Esempio F#

Di seguito è riportata l'associazione di output nel file *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Ecco il codice F#:

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>Esempio JavaScript

Di seguito è riportata l'associazione di output nel file *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

In JavaScript il valore restituito viene inserito nel secondo parametro per `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
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

## <a name="binding-expressions-and-patterns"></a>Modelli ed espressioni di associazione

Una delle funzionalità più potenti di trigger e associazioni sono le *espressioni di associazione*. Nel file *function.json* e nel codice e nei parametri della funzione è possibile usare espressioni risolvibili in valori di varie origini.

Quasi tutte le espressioni sono identificate tramite la disposizione del testo tra parentesi graffe. Ad esempio, in una funzione di trigger della coda `{queueTrigger}` viene risolto nel testo dei messaggi della coda. Se la proprietà `path` dell'associazione di output di un BLOB è `container/{queueTrigger}` e la funzione viene attivata da un messaggio della coda `HelloWorld`, viene creato un BLOB denominato `HelloWorld`.

Tipi di espressioni di associazione

* [Impostazioni dell'app](#binding-expressions---app-settings)
* [Nome file del trigger](#binding-expressions---trigger-file-name)
* [Metadati dei trigger](#binding-expressions---trigger-metadata)
* [Payload JSON](#binding-expressions---json-payloads)
* [Nuovo GUID](#binding-expressions---create-guids)
* [Data e ora correnti](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Espressioni di associazione - impostazioni dell'app

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
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

### <a name="binding-expressions---trigger-file-name"></a>Espressioni di associazione - nome file del trigger

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
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
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
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

È anche possibile creare espressioni per parti del nome file, ad esempio per l'estensione. Per altre informazioni su come usare le espressioni e i modelli nella stringa del percorso del BLOB, vedere la [documentazione di riferimento sulle associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Espressioni di associazione - metadati dei trigger

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

### <a name="binding-expressions---json-payloads"></a>Espressioni di associazione - payload JSON

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
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
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

#### <a name="dot-notation"></a>Notazione con il punto

Se alcune proprietà nel payload JSON sono oggetti con proprietà, è possibile fare riferimento direttamente a tali oggetti tramite la notazione con il punto. Ad esempio, si supponga che il file JSON sia simile al seguente:

```json
{"BlobName": {
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

### <a name="binding-expressions---create-guids"></a>Espressioni di associazione - creare GUID

L'espressione di associazione `{rand-guid}` crea un GUID. Il percorso del BLOB seguente in un file `function.json` crea un BLOB con un nome, ad esempio *50710cb5 84b9 - 4d 87-9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Espressioni di associazione - data e ora correnti

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

## <a name="functionjson-file-schema"></a>Schema del file function.json

Lo schema del file *function.json* è disponibile all'indirizzo [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Gestione degli errori di associazione

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Per i collegamenti a tutti gli argomenti relativi agli errori pertinenti per i diversi servizi supportati da Funzioni, vedere la sezione [Binding error codes](functions-bindings-error-pages.md#binding-error-codes) (Codici degli errori di associazione) dell'argomento introduttivo [Azure Functions error handling](functions-bindings-error-pages.md) (Gestione degli errori di Funzioni di Azure).  

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
