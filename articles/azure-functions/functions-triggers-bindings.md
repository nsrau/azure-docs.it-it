---
title: Trigger e associazioni in Funzioni di Azure
description: Informazioni su come usare trigger e associazioni in Funzioni di Azure per connettere l'esecuzione del codice a eventi online e servizi basati su cloud.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/24/2018
ms.author: cshoe
ms.openlocfilehash: df722f305d60eb0ab53964bfc4e3f48961036708
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984852"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure

In questo articolo viene fornita una panoramica concettuale di trigger e associazioni in Funzioni di Azure. Le funzionalità comuni a tutte le associazioni e a tutti i linguaggi supportati sono descritte di seguito.

## <a name="overview"></a>Panoramica

Un *trigger* definisce come viene richiamata una funzione. Una funzione deve avere esattamente un trigger. I trigger hanno dei dati associati, ovvero in genere il payload che ha attivato la funzione.

Le *associazioni* di input e output forniscono una modalità dichiarativa per connettersi ai dati dall'interno del codice. Le associazioni sono facoltative e una funzione può avere più associazioni di input e output. 

I trigger e le associazioni permettono di evitare di inserire dettagli hardcoded dei servizi usati. La funzione riceve i dati, ad esempio il contenuto di un messaggio della coda, nei parametri della funzione. I dati vengono inviati, ad esempio per creare un messaggio della coda, usando il valore restituito della funzione. Negli script C# e C# è in alternativa possibile inviare dati tramite i parametri `out` e gli [oggetti dell'agente di raccolta](functions-reference-csharp.md#writing-multiple-output-values).

Quando si sviluppano funzioni tramite il portale di Azure, i trigger e le associazioni sono configurati in un file *function.json*. Il portale fornisce un'interfaccia utente per questa configurazione, ma è possibile modificare direttamente il file passando all'**editor avanzato**.

Se le funzioni vengono sviluppate in Visual Studio per creare una libreria di classi, i trigger e le associazioni vengono configurati tramite la decorazione di metodi e parametri con attributi.

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

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
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
        ILogger log)
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

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="register-binding-extensions"></a>Registrare le estensioni delle associazioni

In alcuni ambienti di sviluppo è necessario *registrare* in modo esplicito un'associazione che si desidera usare. Le estensioni di associazione sono incluse nei pacchetti NuGet e per registrare un'estensione si installa un pacchetto. La tabella seguente indica come e quando registrare le estensioni di associazione.

|Ambiente di sviluppo |Registrazione<br/> in Funzioni 1.x  |Registrazione<br/> in Funzioni 2.x  |
|---------|---------|---------|
|Portale di Azure|Automatico|[Automatico con prompt](#azure-portal-development)|
|A livello locale con gli strumenti di base di Funzioni di Azure|Automatico|[Usare i comandi CLI degli strumenti di base](#local-development-azure-functions-core-tools)|
|Libreria di classi C# usando Visual Studio 2017|[Usare gli strumenti di NuGet](#c-class-library-with-visual-studio-2017)|[Usare gli strumenti di NuGet](#c-class-library-with-visual-studio-2017)|
|Libreria di classi C# usando Visual Studio Code|N/D|[Usare l'interfaccia della riga di comando di .NET Core](#c-class-library-with-visual-studio-code)|

I tipi di associazione HTTP e timer sono eccezioni che non richiedono la registrazione esplicita, perché vengono registrati automaticamente in tutte le versioni e in tutti gli ambienti.

### <a name="azure-portal-development"></a>Sviluppo con il portale di Azure

Questa sezione si applica solo a Funzioni 2.x. Le estensioni di associazione non devono essere registrate in modo esplicito in Funzioni 1.x.

Quando si crea una funzione o si aggiunge un'associazione, compare un avviso quando è necessario registrare l'estensione dell'associazione o del trigger. Rispondere all'avviso facendo clic **Installa** per registrare l'estensione. L'installazione può richiedere fino a 10 minuti in un piano a consumo.

È necessario installare ogni estensione una sola volta per una determinata app per le funzioni. Per le associazioni supportate non disponibili nel portale o per aggiornare un'estensione installata, è anche possibile [installare o aggiornare manualmente le estensioni delle associazioni di Funzioni di Azure dal portale](install-update-binding-extensions-manual.md).  

### <a name="local-development-azure-functions-core-tools"></a>Sviluppo a livello locale con gli strumenti di base di Funzioni di Azure

Questa sezione si applica solo a Funzioni 2.x. Le estensioni di associazione non devono essere registrate in modo esplicito in Funzioni 1.x.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
### <a name="c-class-library-with-visual-studio-2017"></a>Libreria di classi C# con Visual Studio 2017

In **Visual Studio 2017** è possibile installare pacchetti dalla Console di Gestione pacchetti usando il comando [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package), come illustrato nell'esempio seguente:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Il nome del pacchetto da usare per una determinata associazione è fornito nell'articolo di riferimento per quell'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#packages---functions-1x).

Sostituire `<target_version>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

### <a name="c-class-library-with-visual-studio-code"></a>Libreria di classi C# con Visual Studio Code

In **Visual Studio Code** è possibile installare i pacchetti dal prompt dei comandi usando il comando [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) nell'interfaccia della riga di comando di .NET Core, come indicato nell'esempio seguente:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

L'interfaccia della riga di comando di .NET Core può essere usata solo per lo sviluppo di Funzioni di Azure 2.x.

Il nome del pacchetto da usare per una determinata associazione è fornito nell'articolo di riferimento per quell'associazione. Per un esempio, vedere la [sezione sui pacchetti dell'articolo di riferimento sull'associazione del bus di servizio](functions-bindings-service-bus.md#packages---functions-1x).

Sostituire `<target_version>` nell'esempio con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org). Le versioni principali che corrispondono al runtime di Funzioni 1.x o 2.x sono specificate nell'articolo di riferimento per l'associazione.

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

Se sono presenti più associazioni di output, usare il valore restituito per una sola di tali associazioni.

Negli script C# e C# è in alternativa possibile inviare dati alle associazioni di output dati tramite i parametri `out` e gli [oggetti dell'agente di raccolta](functions-reference-csharp.md#writing-multiple-output-values).

Vedere l'esempio specifico del linguaggio che mostra l'utilizzo del valore restituito:

* [C#](#c-example)
* [Script C# (file con estensione csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>Esempio in C#

Di seguito è riportato il codice C# che usa il valore restituito per un'associazione di output, seguito da un esempio asincrono:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
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
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
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
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
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

### <a name="python-example"></a>Esempio in Python

Di seguito è riportata l'associazione di output nel file *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Ecco il codice Python:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
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
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
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

#### <a name="dot-notation"></a>Notazione con il punto

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

## <a name="testing-bindings"></a>Test delle associazioni

Quando si sviluppano funzioni in locale, è possibile testare le associazioni usando Visual Studio 2017 o Visual Studio Code. Per altre informazioni, vedere [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md). È anche possibile richiamare le associazioni non HTTP con le API REST. Per altre informazioni, vedere [Eseguire manualmente una funzione non attivata da HTTP](functions-manually-run-non-http.md).

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
