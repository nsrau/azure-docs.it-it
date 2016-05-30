<properties
	pageTitle="Trigger e associazioni di Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come usare i trigger e le associazioni in Funzioni di Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Guida di riferimento per gli sviluppatori di trigger e associazioni di Funzioni di Azure

Questo articolo illustra come configurare e scrivere codice per trigger e associazioni in Funzioni di Azure. La maggior parte di queste associazioni viene gestita facilmente dall'interfaccia utente delle **funzionalità di integrazione** del portale di Azure, ma il portale non illustra tutte le funzionalità e le opzioni per ogni associazione.

Questo articolo presuppone che l'utente abbia già letto gli articoli di riferimento per sviluppatori [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md) e [C#](functions-reference-csharp.md) o [Node](functions-reference-node.md).

## Trigger e associazioni HTTP e webhook

È possibile usare un trigger HTTP o webhook per chiamare una funzione in risposta a una richiesta HTTP. La richiesta deve includere una chiave API, che attualmente è disponibile solo nell'interfaccia utente del portale di Azure.

L'URL della funzione è una combinazione dell'URL del nome dell'app per le funzioni e del nome della funzione:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

Il file *function.json* fornisce le proprietà relative alla richiesta e alla risposta HTTP.

Proprietà per la richiesta HTTP:

- `name`: nome della variabile usato nel codice della funzione per l'oggetto richiesta (o per il corpo della richiesta nel caso di funzioni Node.js).
- `type`: deve essere impostato su *httpTrigger*.
- `direction`: deve essere impostato su *in*. 
- `webHookType`: per i trigger webhook, i valori validi sono *github*, *slack* e *genericJson*. Per un trigger HTTP diverso da un webhook, impostare questa proprietà su una stringa vuota. Per altre informazioni sui webhook, vedere la sezione [Trigger webhook](#webhook-triggers) seguente.
- `authLevel`: non si applica ai trigger webhook. Impostare su "function" per richiedere la chiave API, su "anonymous" per rimuovere il requisito della chiave API o su "admin" per richiedere la chiave API master. Per altre informazioni, vedere [Chiavi API](#apikeys) più avanti.

Proprietà per la risposta HTTP:

- `name`: nome della variabile usato nel codice della funzione per l'oggetto risposta.
- `type`: deve essere impostato su *http*.
- `direction`: deve essere impostato su *out*. 
 
*Function.json* di esempio:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

### Trigger webhook

Un trigger webhook è un trigger HTTP con le funzionalità seguenti progettate per i webhook:

* Per provider di webhook specifici (attualmente sono supportati GitHub e Slack), il runtime di Funzioni convalida la firma del provider.
* Per le funzioni Node.js, il runtime di Funzioni fornisce il corpo della richiesta invece dell'oggetto richiesta. Non esiste una gestione speciale per le funzioni C#, perché l'utente controlla ciò che viene fornito specificando il tipo di parametro. Se si specifica `HttpRequestMessage`, si ottiene l'oggetto richiesta. Se si specifica un tipo POCO, il runtime di Funzioni cerca di analizzare un oggetto JSON nel corpo della richiesta per popolare le proprietà dell'oggetto.
* Per attivare una funzione webhook, la richiesta HTTP deve includere una chiave API. Per i trigger HTTP non webhook, questo requisito è facoltativo.

Per informazioni su come configurare un webhook GitHub, vedere la pagina relativa alla [creazione di webhook in GitHub Developer](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

### Chiavi API

Per impostazione predefinita, una chiave API deve essere inclusa con una richiesta HTTP per attivare una funzione HTTP o webhook. La chiave può essere inclusa in una variabile della stringa di query denominata `code` oppure in un'intestazione HTTP `x-functions-key`. Per le funzioni non webhook, è possibile indicare che una chiave API non è obbligatoria impostando la proprietà `authLevel` su "anonymous" nel file *function.json*.

È possibile trovare i valori della chiave API nella cartella *D:\\home\\data\\Functions\\secrets* nel file system dell'app per le funzioni. La chiave master e la chiave della funzione vengono impostate nel file *host.json*, come illustrato in questo esempio.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

La chiave della funzione di *host.json* può essere usata per attivare qualsiasi funzione, ma non per attivare una funzione disabilitata. La chiave master può essere usata per attivare qualsiasi funzione, anche una che è stata disabilitata. È possibile configurare una funzione per richiedere la chiave master impostando la proprietà `authLevel` su "admin".

Se la cartella *secrets* contiene un file JSON con lo stesso nome della funzione, la proprietà `key` in tale file può essere usata anche per attivare la funzione e questa chiave funzionerà solo con la funzione a cui si riferisce. Ad esempio, la chiave API per una funzione denominata `HttpTrigger` viene specificata in *HttpTrigger.json* nella cartella *secrets*. Di seguito è fornito un esempio:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Quando si configura un trigger webhook, non condividere la chiave master con il provider webhook. Usare una chiave che funzioni solo con la funzione che elabora il webhook. La chiave master può essere usata per attivare qualsiasi funzione, anche quelle disabilitate.

### Codice C# di esempio per una funzione trigger HTTP 

Il codice di esempio cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### Codice Node.js di esempio per una funzione trigger HTTP 

Questo codice di esempio cerca un parametro `name` nella stringa di query o nel corpo della richiesta HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### Codice C# di esempio per una funzione webhook di GitHub 

Questo codice di esempio registra i commenti sui problemi di GitHub.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### Codice Node.js di esempio per una funzione webhook di GitHub 

Questo codice di esempio registra i commenti sui problemi di GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Trigger timer

Il file *function.json* fornisce un'espressione schedule e un'opzione che indica se la funzione deve essere attivata subito.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Il trigger timer gestisce automaticamente la scalabilità orizzontale di più istanze: solo un'unica istanza di una determinata funzione timer sarà in esecuzione in tutte le istanze.

### Formato dell'espressione schedule

L'espressione schedule può essere un'[espressione CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) che include 6 campi: {secondo} {minuto} {ora} {giorno} {mese} {giorno della settimana}. In molti documenti con l'espressione cron disponibili online viene omesso il campo {secondo}, quindi, se si copia il contenuto da uno di questi documenti, sarà necessario inserire il campo aggiuntivo.

L'espressione schedule può essere anche nel formato *hh:mm:ss* per specificare il ritardo tra ogni attivazione della funzione.

Ecco alcuni esempi di espressione schedule.

Per attivare una volta ogni 5 minuti:

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Per attivare immediatamente e poi ogni due ore:

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

Per attivare ogni 15 secondi:

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

### Esempio di codice C# del trigger timer

Questo esempio di codice C# scrive un singolo log ogni volta che viene attivata la funzione.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Trigger e associazioni di archiviazione di Azure (code, BLOB, tabelle)

In questa sezione sono disponibili le procedure seguenti:

* [Trigger della coda di archiviazione di Azure](#storagequeuetrigger)
* [Associazione di output della coda di archiviazione di Azure](#storagequeueoutput)
* [Trigger del BLOB di archiviazione di Azure](#storageblobtrigger)
* [Associazioni di input e di output dei BLOB di archiviazione di Azure](#storageblobbindings)
* [Associazioni di input e di output delle tabelle di archiviazione di Azure](#storagetablesbindings)

### <a id="storagequeuetrigger"></a> Trigger della coda di archiviazione di Azure

Il file *function.json* per un trigger della coda di archiviazione specifica le seguenti proprietà.

- `name`: nome della variabile usato nel codice della funzione per la coda o il messaggio della coda. 
- `queueName`: nome della coda sulla quale eseguire il polling. Per le regole di denominazione della coda vedere [Denominazione di code e metadati](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia `connection` vuoto il trigger funziona con la stringa di connessione di archiviazione predefinita dell'app per le funzioni, specificata dall'impostazione dell'app AzureWebJobsStorage.
- `type`: deve essere impostato su *queueTrigger*.
- `direction`: deve essere impostato su *in*. 

#### Esempio di *function.json* per un trigger della coda di archiviazione

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### Tipi supportati di trigger della coda

Il messaggio della coda può essere deserializzato in uno qualsiasi dei seguenti tipi:

* Object (da JSON)
* Stringa
* Matrice di byte 
* `CloudQueueMessage` (C#) 

#### Metadati del trigger della coda

È possibile ottenere i metadati della coda nella funzione usando questi nomi di variabili:

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (un altro modo per recuperare il testo del messaggio della coda come stringa)

Questo esempio di codice C# recupera e registra i metadati della coda:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### Gestione di messaggi della coda non elaborabili

I messaggi il cui contenuto comporta l'esito negativo di una funzione sono denominati *messaggi non elaborabili*. Quando la funzione non riesce, il messaggio in coda non viene eliminato e infine viene prelevato, causando la ripetizione del ciclo. L'SDK può interrompere automaticamente il ciclo dopo un numero limitato di iterazioni oppure è possibile farlo manualmente.

L'SDK chiamerà una funzione fino a 5 volte per elaborare un messaggio nella coda. Se il quinto tentativo non riesce, il messaggio viene spostato in una coda non elaborabile.

La coda non elaborabile è denominata *{nomecodaoriginale}*-poison. È possibile scrivere una funzione per elaborare i messaggi dalla coda non elaborabile archiviandoli o inviando una notifica della necessità di un intervento manuale.

Per gestire manualmente i messaggi non elaborabili, è possibile verificare in `dequeueCount` quante volte un messaggio è stato prelevato per l'elaborazione.

### <a id="storagequeueoutput"></a> Associazione di output della coda di archiviazione di Azure

Il file *function.json* per un'associazione di output della coda di archiviazione specifica le seguenti proprietà.

- `name`: nome della variabile usato nel codice della funzione per la coda o il messaggio della coda. 
- `queueName`: nome della coda. Per le regole di denominazione della coda vedere [Denominazione di code e metadati](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia `connection` vuoto il trigger funziona con la stringa di connessione di archiviazione predefinita dell'app per le funzioni, specificata dall'impostazione dell'app AzureWebJobsStorage.
- `type`: deve essere impostato su *queue*.
- `direction`: deve essere impostato su *out*. 

#### Esempio di *function.json* per un'associazione di output della coda di archiviazione

Questo esempio usa un trigger della coda e scrive un messaggio della coda.

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
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Tipi supportati di associazione di output della coda

L'associazione `queue` può serializzare i seguenti tipi in un messaggio della coda:

* Object (`out T` in C#, crea un messaggio con un oggetto null se il parametro è null quando termina la funzione)
* String (`out string` in C#, crea il messaggio di coda se il valore del parametro è diverso da null quando termina la funzione)
* Matrice di byte (`out byte[]` in C#, funziona come string) 
* `out CloudQueueMessage` (C#, funziona come string) 

In C#, è anche possibile definire l'associazione a `ICollector<T>` o `IAsyncCollector<T>` dove `T` è uno dei tipi supportati.

#### Esempi di codice dell'associazione di output della coda

Questo esempio di codice C# scrive un singolo messaggio della coda di output per ogni messaggio della coda di input.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Questo esempio di codice C# scrive più messaggi usando `ICollector<T>` (usare `IAsyncCollector<T>` in una funzione asincrona):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

### <a id="storageblobtrigger"></a> Trigger del BLOB di archiviazione di Azure

Il file *function.json* per un trigger del BLOB di archiviazione specifica le seguenti proprietà.

- `name`: nome della variabile usato nel codice della funzione per il BLOB. 
- `path`: percorso che specifica il contenitore da monitorare e facoltativamente un modello di nome BLOB.
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia `connection` vuoto il trigger funziona con la stringa di connessione di archiviazione predefinita dell'app per le funzioni, specificata dall'impostazione dell'app AzureWebJobsStorage.
- `type`: deve essere impostato su *blobTrigger*.
- `direction`: deve essere impostato su *in*.

#### Esempio di *function.json* per un trigger del BLOB di archiviazione

Questo esempio attiva i BLOB aggiunti al contenitore samples-workitems.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### Tipi supportati di trigger dei BLOB

Il BLOB può essere deserializzato in uno dei seguenti tipi in funzioni Node o C#:

* Object (da JSON)
* Stringa

Nelle funzioni C# è anche possibile definire associazioni con uno dei seguenti tipi:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Altri tipi deserializzati da [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

#### Esempio di codice C# del trigger dei BLOB

Questo codice di esempio C# registra i contenuti di ogni BLOB aggiunto al contenitore monitorato.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### Modelli di nome di trigger dei BLOB

È possibile specificare un modello di nome di BLOB nella proprietà `path`, ad esempio:

```json
"path": "input/original-{name}",
```

Questo percorso troverà un BLOB denominato *original-Blob1.txt* nel contenitore *input* e il valore della variabile `name` nel codice della funzione sarà `Blob1`.

Un altro esempio:

```json
"path": "input/{blobname}.{blobextension}",
```

Anche questo percorso troverà un BLOB denominato *original-Blob1.txt* e il valore delle variabili `blobname` e `blobextension` nel codice della funzione sarà *original-Blob1* e *txt*.

È possibile limitare i tipi di BLOB che attivano la funzione specificando un modello con un valore fisso per l'estensione di file. Se si imposta `path` su *samples/{nome}.png*, la funzione verrà attivata solo dai BLOB *.png* nel contenitore *samples*.

Se è necessario specificare un modello di nome per i nomi di BLOB con parentesi graffe nel nome, raddoppiare le parentesi graffe. Se ad esempio si desidera trovare i BLOB nel contenitore *images* con nomi simile al seguente:

		{20140101}-soundfile.mp3

usare la seguente notazione per la proprietà `path`:

		images/{{20140101}}-{name}

Nell'esempio il valore della variabile `name` sarebbe *soundfile.mp3*.

#### Conferme di BLOB

Il runtime di Funzioni di Azure verifica che nessuna funzione trigger di BLOB venga chiamata più volte per lo stesso BLOB nuovo o aggiornato. A tale scopo, gestisce *conferme di BLOB* per determinare se una versione di BLOB specifica è stata elaborata.

Le conferme di BLOB vengono archiviate in un contenitore denominato *azure-webjobs-hosts* nell'account di archiviazione di Azure specificato dalla stringa di connessione AzureWebJobsStorage. Una conferma di BLOB contiene le seguenti informazioni:

* La funzione chiamata per il BLOB ("*{nome dell'app per le funzioni}*.Functions.*{nome della funzione}*", ad esempio: "functionsf74b96f7.Functions.CopyBlob")
* Il nome del contenitore
* Il tipo di BLOB ("BlockBlob" o "PageBlob")
* Il nome del BLOB
* Il valore ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

Se si desidera forzare la rielaborazione di un BLOB, è possibile eliminare manualmente la conferma per tale BLOB dal contenitore *azure-webjobs-hosts*.

#### Gestione di BLOB non elaborabili

Quando una funzione trigger di BLOB ha esito negativo, l'SDK la chiama nuovamente in caso in cui il problema sia stato causato da un errore temporaneo. Se il problema è causato dal contenuto del BLOB, la funzione ha esito negativo ogni volta che tenta di elaborare il BLOB. Per impostazione predefinita, l'SDK chiama una funzione fino a cinque volte per un determinato BLOB. Se il quinto tentativo ha esito negativo, l'SDK aggiunge un messaggio a una coda denominata *webjobs-blobtrigger-poison*.

Il messaggio di coda per i BLOB non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato *{nome dell'app per le funzioni}*.Functions.*{nome della funzione}*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

#### Polling dei BLOB per contenitori di grandi dimensioni

Se il contenitore BLOB monitorato dal trigger contiene più di 10.000 BLOB, il runtime di Funzioni analizza i file di log per cercare i BLOB nuovi o modificati. Questo processo non avviene in tempo reale. Una funzione potrebbe non essere attivata per diversi minuti o più dopo la creazione del BLOB. Inoltre, [i log di archiviazione vengono creati in base al principio del "massimo impegno"](https://msdn.microsoft.com/library/azure/hh343262.aspx). Non è garantito che tutti gli eventi vengano acquisiti. In alcune condizioni, l'acquisizione dei log potrebbe non riuscire. Se le limitazioni di velocità e affidabilità dei trigger dei BLOB per i contenitori di grandi dimensioni non sono accettabili per l'applicazione, il metodo consigliato consiste nel creare un messaggio nella coda quando si crea il BLOB e usare un trigger della coda invece di un trigger di BLOB per elaborare il BLOB.
 
### <a id="storageblobbindings"></a> Associazioni di input e di output dei BLOB di archiviazione di Azure

Il file *function.json* per l'associazione di input o di output di un BLOB di archiviazione specifica le seguenti proprietà.

- `name`: nome della variabile usato nel codice della funzione per il BLOB. 
- `path`: percorso che specifica il contenitore dal quale leggere o nel quale aggiungere dati al BLOB e facoltativamente un modello di nome di BLOB.
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia `connection` vuota l'associazione funziona con la stringa di connessione di archiviazione predefinita dell'app per le funzioni, specificata dall'impostazione app AzureWebJobsStorage.
- `type`: deve essere impostato su *blob*.
- `direction` : impostare su *in* o *out*. 

#### Esempio di *function.json* per un'associazione di input o output con un BLOB di archiviazione

Questo esempio usa un trigger della coda per copiare un BLOB:

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

#### Tipi supportati di input e di output di BLOB

L'associazione `blob` può serializzare o deserializzare i tipi seguenti nelle funzioni Node.js o C#:

* Object (`out T` in C# per il BLOB di output: crea un BLOB come oggetto null se il valore del parametro è null quando la funzione termina)
* String (`out string` in C# per il BLOB di output: crea un BLOB solo se il parametro di stringa è diverso da null quando la funzione restituisce un valore)

Nelle funzioni C# è anche possibile definire associazioni con i seguenti tipi:

* `TextReader` (solo input)
* `TextWriter` (solo output)
* `Stream`
* `CloudBlobStream` (solo output)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### Esempio di codice C# di output di BLOB

Questo esempio di codice C# copia un BLOB il cui nome viene ricevuto in un messaggio della coda.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a id="storagetablesbindings"></a> Associazioni di input e di output delle tabelle di archiviazione di Azure

Il file *function.json* per le tabelle di archiviazione specifica le seguenti proprietà.

- `name`: nome della variabile usato nel codice della funzione per l'associazione della tabella. 
- `tableName` : nome della tabella.
- `partitionKey` e `rowKey`: usati insieme per leggere una singola entità in una funzione C# o Node oppure per scrivere una singola entità in una funzione Node.
- `take`: numero massimo di righe da leggere per l'input tabella in una funzione Node.
- `filter`: espressione di filtro OData per l'input tabella in una funzione Node.
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia `connection` vuota l'associazione funziona con la stringa di connessione di archiviazione predefinita dell'app per le funzioni, specificata dall'impostazione app AzureWebJobsStorage.
- `type`: deve essere impostato su *table*.
- `direction` : impostare su *in* o *out*. 

#### Tipi di input e output supportati nelle tabelle di archiviazione

L'associazione `table` può serializzare o deserializzare oggetti nelle funzioni Node.js o C#. Gli oggetti avranno le proprietà PartitionKey e RowKey.

Nelle funzioni C# è anche possibile definire associazioni con i seguenti tipi:

* `T` dove `T` implementa `ITableEntity`
* `IQueryable<T>` (solo input)
* `ICollector<T>` (solo output)
* `IAsyncCollector<T>` (solo output)

#### Scenari di associazione delle tabelle di archiviazione

L'associazione delle tabelle supporta gli scenari seguenti:

* Leggere una singola riga in una funzione C# o Node.

	Impostare `partitionKey` e `rowKey`. Le proprietà `filter` e `take` non vengono usate in questo scenario.

* Leggere più righe in una funzione C#.

	Il runtime di Funzioni offre un oggetto `IQueryable<T>` associato alla tabella. Il tipo `T` deve derivare da `TableEntity` o implementare `ITableEntity`. Le proprietà `partitionKey`, `rowKey`, `filter` e `take` non vengono usate in questo scenario. È possibile usare l'oggetto `IQueryable` per qualsiasi operazione di filtro necessaria.

* Leggere più righe in una funzione Node.

	Impostare le proprietà `filter` e `take`. Non impostare `partitionKey` o `rowKey`.

* Scrivere una o più righe in una funzione C#.

	Il runtime di Funzioni offre un oggetto `ICollector<T>` o `IAsyncCollector<T>` associato alla tabella, dove `T` specifica lo schema delle entità che si vuole aggiungere. In genere, ma non necessariamente, il tipo `T` deriva da `TableEntity` o implementa `ITableEntity`. Le proprietà `partitionKey`, `rowKey`, `filter` e `take` non vengono usate in questo scenario.

#### Esempio di tabelle di archiviazione: lettura di una singola entità di tabella in C# o in Node

Questo esempio di *function.json* usa un trigger della coda per leggere una singola riga della tabella, con un valore della chiave di partizione hardcoded e la chiave della riga specificata nel messaggio della coda.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

L'esempio di codice C# seguente usa il file *function.json* precedente per leggere una singola entità tabella. Il messaggio della coda ha il valore della chiave della riga e l'entità tabella viene letta in un tipo definito nel file *run.csx*. Il tipo include le proprietà `PartitionKey` e `RowKey` e non deriva da `TableEntity`.

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Anche l'esempio di codice Node seguente usa il file *function.json* precedente per leggere una singola entità di tabella.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Esempio di tabelle di archiviazione: lettura di più entità di tabella in C# 

Il file *function.json* e l'esempio di codice C# seguenti leggono le entità per una chiave di partizione specificata nel messaggio della coda.

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Il codice C# aggiunge un riferimento ad Azure Storage SDK in modo che il tipo di entità possa derivare da `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### Esempio di tabelle di archiviazione: creazione di più entità di tabella in C# 

L'esempio di *function.json* e *run.csx* seguente illustra come scrivere entità di tabella in C#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### Esempio di tabelle di archiviazione: creazione un'entità di tabella in Node

L'esempio di *function.json* e *run.csx* seguente illustra come scrivere un'entità di tabella in Node.

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
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Trigger e associazioni del bus di servizio di Azure

In questa sezione sono disponibili le procedure seguenti:

* [Bus di servizio di Azure: comportamento PeekLock](#sbpeeklock)
* [Bus di servizio di Azure: gestione di messaggi non elaborabili](#sbpoison)
* [Bus di servizio di Azure: threading singolo](#sbsinglethread)
* [Trigger della coda o dell'argomento del bus di servizio di Azure](#sbtrigger)
* [Associazione di output della coda o dell'argomento del bus di servizio di Azure](#sboutput)

### <a id="sbpeeklock"></a> Bus di servizio di Azure: comportamento PeekLock

Il runtime di Funzioni riceve un messaggio in modalità `PeekLock` e chiama `Complete` sul messaggio se la funzione viene completata correttamente oppure `Abandon` se la funzione ha esito negativo. Se il tempo di esecuzione della funzione supera il timeout di `PeekLock`, il blocco viene rinnovato automaticamente.

### <a id="sbpoison"></a> Bus di servizio di Azure: gestione di messaggi non elaborabili

Il bus di servizio esegue la gestione dei messaggi non elaborabili in modo autonomo, non controllabile o modificabile nella configurazione o nel codice di Funzioni di Azure.

### <a id="sbsinglethread"></a> Bus di servizio di Azure: threading singolo

Per impostazione predefinita il runtime di Funzioni elabora più messaggi della coda contemporaneamente. Per impostare il runtime in modo che elabori un solo messaggio della coda o dell'argomento alla volta, impostare `serviceBus.maxConcurrrentCalls` su 1 nel file *host.json*. Per informazioni sul file *host.json* vedere [Struttura di cartelle](functions-reference.md#folder-structure) nell'articolo Guida di riferimento per gli sviluppatori e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) nella wiki del repository WebJobs.Script.

### <a id="sbtrigger"></a> Trigger della coda o dell'argomento del bus di servizio di Azure

Il file *function.json* per un trigger del bus di servizio specifica le seguenti proprietà.

- `name`: nome della variabile usata nel codice della funzione per la coda o l'argomento o per il messaggio della coda o dell'argomento. 
- `queueName`: solo per il trigger della coda, il nome della coda in cui eseguire il polling.
- `topicName`: solo per il trigger dell'argomento, il nome dell'argomento in cui eseguire il polling.
- `subscriptionName`: solo per il trigger dell'argomento, il nome della sottoscrizione.
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione del bus di servizio. La stringa di connessione deve essere relativa a uno spazio dei nomi del bus di servizio e non limitata a una coda o un argomento specifico. Se la stringa di connessione non dispone di diritti di gestione impostare la proprietà `accessRights`. Se si lascia `connection` vuota il trigger o l'associazione funzionano con la stringa di connessione del bus di servizio predefinita dell'app per le funzioni, specificata dall'impostazione app AzureWebJobsServiceBus.
- `accessRights`: specifica i diritti di accesso disponibili per la stringa di connessione. Il valore predefinito è `manage`. Impostare su `listen` se si usa una stringa di connessione che non specifica autorizzazioni di gestione. In caso contrario il runtime di Funzioni potrebbe provare senza successo a eseguire operazioni che richiedono diritti di gestione.
- `type`: deve essere impostato su *serviceBusTrigger*.
- `direction`: deve essere impostato su *in*. 

Il messaggio di coda del bus di servizio può essere deserializzato in uno qualsiasi dei seguenti tipi:

* Object (da JSON)
* string
* matrice di byte 
* `BrokeredMessage` (C#) 

#### Esempio di *function.json* per l'uso di un trigger di coda del bus di servizio

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Esempio di codice C# che elabora un messaggio di coda del bus di servizio

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Esempio di codice Node.js che elabora un messaggio di coda del bus di servizio

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

### <a id="sboutput"></a> Output della coda o dell'argomento del bus di servizio di Azure

Il file *function.json* per un'associazione di output del bus di servizio specifica le seguenti proprietà.

- `name`: nome della variabile usato nel codice della funzione per la coda o il messaggio della coda. 
- `queueName`: solo per il trigger della coda, il nome della coda in cui eseguire il polling.
- `topicName`: solo per il trigger dell'argomento, il nome dell'argomento in cui eseguire il polling.
- `subscriptionName`: solo per il trigger dell'argomento, il nome della sottoscrizione.
- `connection`: identico al trigger del bus di servizio.
- `accessRights`: specifica i diritti di accesso disponibili per la stringa di connessione. Il valore predefinito è `manage`. Impostare su `send` se si usa una stringa di connessione che non specifica autorizzazioni di gestione. In caso contrario il runtime di Funzioni potrebbe provare senza successo a eseguire operazioni che richiedono diritti di gestione, ad esempio la creazione di code.
- `type`: deve essere impostato su *serviceBus*.
- `direction`: deve essere impostato su *out*. 

Funzioni di Azure può creare un messaggio di coda del bus di servizio da uno qualsiasi dei tipi seguenti.

* Object (crea sempre un messaggio JSON; crea il messaggio con un oggetto null se il valore è null quando la funzione termina)
* string (crea il messaggio della coda se il valore è diverso da null quando la funzione termina)
* matrice di byte (funziona come string) 
* `BrokeredMessage` (C#, funziona come string)

Per la creazione di più messaggi in una funzione C# è possibile usare `ICollector<T>` o `IAsyncCollector<T>`. Quando si chiama il metodo `Add` viene creato un messaggio.

#### Esempio di *function.json* che usa un trigger timer per scrivere messaggi di coda del bus di servizio

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Esempio di codice C# che crea messaggi di coda del bus di servizio

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Esempio di codice Node.js che crea un messaggio di coda del bus di servizio

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Associazioni di Azure DocumentDB

In questa sezione sono disponibili le procedure seguenti:

* [Associazione di input di Azure DocumentDB](#docdbinput)
* [Associazione di output di Azure DocumentDB](#docdboutput)

### <a id="docdbinput"></a> Associazione di input di Azure DocumentDB

Le associazioni di input possono caricare un documento da una raccolta DocumentDB e passarlo direttamente all'associazione. L'ID documento può essere determinato in base al trigger che ha richiamato la funzione. In una funzione C# eventuali modifiche apportate al record verranno automaticamente inviate alla raccolta quando la funzione termina correttamente.

Il file function.json fornisce le proprietà seguenti da usare con l'associazione di input di DocumentDB:

- `name`: nome della variabile usato nel codice della funzione per il documento.
- `type`: deve essere impostato su "documentdb".
- `databaseName`: database che contiene il documento.
- `collectionName`: raccolta che contiene il documento.
- `id`: ID del documento da recuperare. Questa proprietà supporta associazioni simili a "{queueTrigger}", che useranno il valore della stringa del messaggio della coda come ID documento.
- `connection`: questa stringa deve essere un'Impostazione applicazione configurata sull'endpoint per l'account DocumentDB. Se si sceglie l'account dalla scheda Integra, verrà automaticamente creata una nuova impostazione dell'app con un nome nel formato yourAccount\_DOCUMENTDB. Se è necessario creare manualmente l'impostazione dell'app, la stringa di connessione effettiva deve avere il formato AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;.
- `direction: deve essere impostato su *"in"*.

Function.json di esempio:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Esempio di codice di input di Azure DocumentDB per un trigger della coda C#
 
Usando il file function.json di esempio precedente, l'associazione di input di DocumentDB recupererà il documento con l'ID corrispondente alla stringa del messaggio della coda e lo passerà al parametro "document". Se tale documento non viene trovato, il parametro "document" sarà null. Il documento viene quindi aggiornato con il nuovo valore di text quando la funzione termina.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Esempio di codice di input di Azure DocumentDB per un trigger della coda di Node.js
 
Usando il file function.json di esempio precedente, l'associazione di input di DocumentDB recupererà il documento con l'ID corrispondente alla stringa del messaggio della coda e lo passerà alla proprietà dell'associazione `documentIn`. Nelle funzioni Node.js i documenti aggiornati non vengono inviati alla raccolta. È tuttavia possibile passare l'associazione di input direttamente a un'associazione di output di DocumentDB denominata `documentOut` per supportare gli aggiornamenti. Questo esempio di codice aggiorna la proprietà text del documento di input e la imposta come documento di output.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

### <a id="docdboutput"></a> Associazioni di output di Azure DocumentDB

Le funzioni possono scrivere documenti JSON in un database Azure DocumentDB usando l'associazione di output **Azure DocumentDB Document**. Per altre informazioni su Azure DocumentDB vedere [Introduzione a DocumentDB](../documentdb/documentdb-introduction.md) ed [Esercitazione introduttiva](../documentdb/documentdb-get-started.md).

Il file function.json fornisce le proprietà seguenti da usare con l'associazione di output di DocumentDB:

- `name`: nome della variabile usato nel codice della funzione per il nuovo documento.
- `type`: deve essere impostato su *"documentdb"*.
- `databaseName`: database contenente la raccolta in cui verrà creato il nuovo documento.
- `collectionName`: raccolta in cui verrà creato il nuovo documento.
- `createIfNotExists`: valore booleano che indica se la raccolta verrà creata quando non esiste. Il valore predefinito è *false*. Il motivo è che le nuove raccolte vengono create con una velocità effettiva riservata, che ha implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: questa stringa deve essere un'**Impostazione applicazione** configurata sull'endpoint per l'account DocumentDB. Se si sceglie l'account dalla scheda **Integra**, verrà automaticamente creata una nuova impostazione dell'app con un nome nel formato `yourAccount_DOCUMENTDB`. Se è necessario creare manualmente l'impostazione dell'app, la stringa di connessione effettiva deve avere il formato `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: deve essere impostato su *"out"*. 
 
Function.json di esempio:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Esempio di codice di output di Azure DocumentDB per un trigger della coda di Node.js

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

Documento di output:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Esempio di codice di output di Azure DocumentDB per un trigger della coda C#


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Impostazione del nome file nell'esempio di codice di output di Azure DocumentDB

Per impostare il nome del documento nella funzione, è sufficiente impostare il valore `id`. Se, ad esempio, il contenuto JSON di un dipendente venisse rilasciato nella coda in modo simile al seguente:

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

Sarebbe possibile usare il codice C# seguente in una funzione trigger della coda:
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

Output di esempio:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Associazioni delle app per dispositivi mobili di Azure

Le app per dispositivi mobili del servizio app di Azure consentono di esporre i dati degli endpoint tabella ai client per dispositivi mobili. Questi stessi dati tabulari possono essere usati con le associazioni sia di input che di output in Funzioni di Azure. Grazie al supporto dello schema dinamico, un'app per dispositivi mobili back-end Node.js è ideale per l'esposizione di dati tabulari da usare con le funzioni. Lo schema dinamico è abilitato per impostazione predefinita ed è consigliabile disabilitarlo in un'app per dispositivi mobili di produzione. Per altre informazioni sugli endpoint di tabella in un back-end Node. js vedere [Panoramica: operazioni su tabella](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations). Nelle app per dispositivi mobili il back-end Node.js supporta l'esplorazione e la modifica di tabelle nel portale. Per altre informazioni vedere la sezione relativa alla [modifica nel portale](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing) nell'argomento che illustra l'uso del SDK Node.js. Quando si usa un'app per dispositivi mobili del back-end .NET con le funzioni di Azure è necessario aggiornare manualmente il modello di dati come richiesto dalla funzione. Per altre informazioni sugli endpoint tabella in un'app per dispositivi mobili del back-end .NET vedere [Procedura: Definire un controller tabelle](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) nell'argomento relativo al SDK del back-end .NET.

In questa sezione sono disponibili le procedure seguenti:

* [Chiave API delle tabelle per le app per dispositivi mobili di Azure](#mobiletablesapikey)
* [Associazione di input delle tabelle per le app per dispositivi mobili di Azure](#mobiletablesinput)
* [Associazione di output delle tabelle per le app per dispositivi mobili di Azure](#mobiletablesoutput)

### <a id="mobiletablesapikey"></a> Usare una chiave API per proteggere l'accesso agli endpoint delle tabelle delle app per dispositivi mobili.

In Funzioni di Azure le associazioni delle tabelle delle app per dispositivi mobili consentono di specificare una chiave API, ovvero un segreto condiviso che può essere usato per impedire l'accesso indesiderato ad app diverse dalle proprie funzioni. Le app per dispositivi mobili non hanno il supporto predefinito per l'autenticazione con la chiave API. È tuttavia possibile implementare una chiave API nell'app per dispositivi mobili back-end Node.js seguendo gli esempi nell'articolo [Azure App Service Mobile Apps backend implementing an API key](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) (Back-end dell'app per dispositivi mobili del servizio app di Azure che implementa una chiave API). In modo simile è possibile implementare una chiave API in un' [app per dispositivi mobili back-end .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key).

>[AZURE.IMPORTANT] Questa chiave API non deve essere distribuita con i client app per dispositivi mobili, ma è consigliabile distribuirla in modo sicuro solo ai client lato servizio, ad esempio Funzioni di Azure.

### <a id="mobiletablesinput"></a> Associazione di input per le app per dispositivi mobili di Azure

Le associazioni di input possono caricare un record da un endpoint tabella per dispositivi mobili e passarlo direttamente all'associazione. L'ID record viene determinato in base al trigger che ha richiamato la funzione. In una funzione C# eventuali modifiche apportate al record vengono automaticamente inviate alla tabella quando la funzione termina correttamente.

Il file function.json supporta l'uso delle seguenti proprietà con le associazioni di input delle app per dispositivi mobili:

- `name`: nome della variabile usato nel codice della funzione per il nuovo record.
- `type`: il tipo di associazione deve essere impostato su *mobileTable*.
- `tableName`: tabella in cui verrà creato il nuovo record.
- `id`: ID del record da recuperare. Questa proprietà supporta associazioni simili a `{queueTrigger}` che useranno il valore stringa del messaggio della coda come ID record.
- `apiKey`: stringa corrispondente all'impostazione applicazione che specifica la chiave API facoltativa per l'app per dispositivi mobili. È necessaria quando l'app per dispositivi mobili usa una chiave API per limitare l'accesso client.
- `connection`: stringa corrispondente all'impostazione applicazione che specifica l'URI dell'app per dispositivi mobili.
- `direction`: direzione dell'associazione, che deve essere impostata su *in*.

Function.json di esempio:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Esempio di codice di app per dispositivi mobili di Azure per un trigger della coda C#

In base al file function.json di esempio precedente l'associazione di input recupera da un endpoint tabella delle app per dispositivi mobili il record con l'ID corrispondente alla stringa del messaggio della coda e lo passa al parametro *record*. Quando il record non viene trovato, il parametro è null. Il record viene quindi aggiornato con il nuovo valore di *Text* quando la funzione termina.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Esempio di codice di app per dispositivi mobili di Azure per un trigger della coda Node.js

In base al file function.json di esempio precedente l'associazione di input recupera da un endpoint tabella delle app per dispositivi mobili il record con l'ID corrispondente alla stringa del messaggio della coda e lo passa al parametro *record*. Nelle funzioni Node.js i record aggiornati non vengono inviati alla tabella. Questo esempio di codice scrive il record recuperato nel log.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


### <a id="mobiletablesoutput"></a> Associazione di output per le app per dispositivi mobili di Azure

La funzione può scrivere un record in un endpoint tabella delle app per dispositivi mobili usando un'associazione di output.

Il file function.json supporta le proprietà seguenti da usare con l'associazione di output delle tabelle per dispositivi mobili:

- `name`: nome della variabile usato nel codice della funzione per il nuovo record.
- `type`: tipo di associazione che deve essere impostato su *mobileTable*.
- `tableName`: tabella in cui viene creato il nuovo record.
- `apiKey`: stringa corrispondente all'impostazione applicazione che specifica la chiave API facoltativa per l'app per dispositivi mobili. È necessaria quando l'app per dispositivi mobili usa una chiave API per limitare l'accesso client.
- `connection`: stringa corrispondente all'impostazione applicazione che specifica l'URI dell'app per dispositivi mobili.
- `direction`: direzione dell'associazione, che deve essere impostata su *out*.

Function.json di esempio:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Esempio di codice di app per dispositivi mobili di Azure per un trigger della coda C#

Questo esempio di codice C# inserisce un nuovo record in un endpoint tabella dell'app per dispositivi mobili con una proprietà *Text* nella tabella specificata nell'associazione precedente.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Esempio di codice di app per dispositivi mobili di Azure per un trigger della coda Node.js

Questo esempio di codice Node.js inserisce un nuovo record in un endpoint tabella dell'app per dispositivi mobili con una proprietà *text* nella tabella specificata nell'associazione precedente.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Associazione di output di hub di notifica di Azure

Le funzioni possono inviare notifiche push usando un hub di notifica di Azure configurato con poche righe di codice. L'hub di notifica tuttavia deve essere configurato per i servizi di notifiche della piattaforma che si vuole usare. Per altre informazioni sulla configurazione di un hub di notifica di Azure e sullo sviluppo di applicazioni client che eseguono la registrazione per le notifiche vedere [Introduzione a Hub di notifica](../notification-hubs/notification-hubs-windows-store-dotnet-get-started.md) e fare clic sulla piattaforma client di destinazione nella parte superiore.

Il file function.json fornisce le proprietà seguenti da usare con l'associazione di output di un hub di notifica:

- `name`: nome della variabile usato nel codice della funzione per il messaggio dell'hub di notifica.
- `type`: deve essere impostato su *"notificationHub"*.
- `tagExpression`: le espressioni tag consentono di specificare che le notifiche vanno recapitate a un set di dispositivi che hanno eseguito la registrazione per ricevere le notifiche corrispondenti all'espressione tag. Per altre informazioni vedere [Routing ed espressioni tag](../notification-hubs/notification-hubs-routing-tag-expressions.md).
- `hubName`: nome della risorsa hub di notifica nel portale di Azure.
- `connection`: questa stringa di connessione deve essere una stringa di connessione **Application Setting** (Impostazione applicazione) impostata sul valore *DefaultFullSharedAccessSignature* per l'hub di notifica.
- `direction`: deve essere impostato su *"out"*. 
 
Function.json di esempio:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

### Configurazione della stringa di connessione dell'hub di notifica di Azure

Per usare un'associazione di output dell'hub di notifica, è necessario configurare la stringa di connessione per l'hub. A questo scopo, nella scheda *Integra* selezionare l'hub di notifica o crearne uno nuovo.

È anche possibile aggiungere manualmente una stringa di connessione per un hub esistente aggiungendo una stringa di connessione per *DefaultFullSharedAccessSignature* all'hub di notifica. Questa stringa di connessione fornisce l'autorizzazione di accesso alla funzione per inviare messaggi di notifica. Il valore della stringa di connessione *DefaultFullSharedAccessSignature* è accessibile dal pulsante **keys** (Chiavi) nel pannello principale della risorsa hub di notifica nel portale di Azure. Per aggiungere manualmente una stringa di connessione per l'hub, usare la procedura seguente:

1. Nel pannello **App per le funzioni** del portale di Azure fare clic su **Function App Settings (Impostazioni dell'app per le funzioni) > Go to App Service settings (Vai alle impostazioni del servizio app)**.

2. Nel pannello **Impostazioni** fare clic su **Impostazioni applicazione**.

3. Scorrere verso il basso fino alla sezione **Stringhe di connessione** e aggiungere una voce chiamata per il valore *DefaultFullSharedAccessSignature* per l'hub di notifica. Impostare il tipo su **Personalizzato**.
4. Fare riferimento al nome della stringa di connessione nelle associazioni di output. È simile a **MyHubConnectionString** usato nell'esempio precedente.

### Esempio di codice di hub di notifica di Azure per un trigger timer Node.js 

Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates.md) contenente `location` e `message`.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

### Esempio di codice di hub di notifica di Azure per un trigger della coda C#

Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates.md) contenente `message`.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

Questo esempio invia una notifica per la [registrazione di un modello](../notification-hubs/notification-hubs-templates.md) contenente `message` usando una stringa JSON valida.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

### Esempio di codice C# del trigger della coda dell'hub di notifica di Azure con il tipo Notification

Questo esempio illustra come usare il tipo `Notification` definito nella [libreria Hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Per usare questo tipo e la libreria è necessario caricare un file *project.json* per l'app per le funzioni. Il file project.json è un file di testo JSON simile al seguente:

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

Per altre informazioni sul caricamento del file project.json vedere [Uploading a project.json file](http://stackoverflow.com/questions/36411536/how-can-i-use-nuget-packages-in-my-azure-functions) (Caricamento di un file project.json).

Codice di esempio:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-reference-csharp.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](functions-reference-node.md)

<!---HONumber=AcomDC_0518_2016-->