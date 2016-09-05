<properties
	pageTitle="Trigger e associazioni di Archiviazione di Azure in Funzioni di Azure | Microsoft Azure"
	description="Informazioni su come usare trigger e associazioni di Archiviazione di Azure in Funzioni di Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande"/>  

# Trigger e associazioni di Archiviazione di Azure in Funzioni di Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice per trigger e associazioni di Archiviazione di Azure in Funzioni di Azure

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="storagequeuetrigger"></a> Trigger della coda di Archiviazione di Azure

#### function.json per trigger della coda di archiviazione

Il file *function.json* specifica le proprietà seguenti.

- `name`: nome della variabile usato nel codice della funzione per la coda o il messaggio della coda.
- `queueName`: nome della coda sulla quale eseguire il polling. Per le regole di denominazione della coda, vedere [Denominazione di code e metadati](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia vuota la proprietà `connection`, il trigger userà la stringa di connessione di archiviazione predefinita per l'app per le funzioni, specificata dall'impostazione dell'app AzureWebJobsStorage.
- `type`: deve essere impostato su *queueTrigger*.
- `direction`: deve essere impostato su *in*.

Esempio di *function.json* per un trigger della coda di archiviazione:

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
* String
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

## <a id="storagequeueoutput"></a> Associazione di output della coda di Archiviazione di Azure

#### function.json per associazione di output della coda di archiviazione

Il file *function.json* specifica le proprietà seguenti.

- `name`: nome della variabile usato nel codice della funzione per la coda o il messaggio della coda.
- `queueName`: nome della coda. Per le regole di denominazione della coda, vedere [Denominazione di code e metadati](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia vuota la proprietà `connection`, il trigger userà la stringa di connessione di archiviazione predefinita per l'app per le funzioni, specificata dall'impostazione dell'app AzureWebJobsStorage.
- `type`: deve essere impostato su *queue*.
- `direction`: deve essere impostato su *out*.

Esempio di *function.json* per un'associazione di output della coda di archiviazione che usa il trigger della coda e scrive un messaggio della coda:

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

L'associazione `queue` può serializzare i tipi seguenti in un messaggio della coda:

* Object (`out T` in C#, crea un messaggio con un oggetto Null se il parametro è Null quando termina la funzione)
* String (`out string` in C#, crea un messaggio della coda se il valore del parametro è diverso da Null quando termina la funzione)
* Matrice di byte (`out byte[]` in C#, funziona come string)
* `out CloudQueueMessage` (C#, funziona come string)

In C# è anche possibile definire l'associazione a `ICollector<T>` o `IAsyncCollector<T>` dove `T` è uno dei tipi supportati.

#### Esempi di codice dell'associazione di output della coda

Questo esempio di codice C# scrive un singolo messaggio della coda di output per ogni messaggio della coda di input.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Questo esempio di codice C# scrive più messaggi usando `ICollector<T>`. Usare `IAsyncCollector<T>` in una funzione asincrona:

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a> Trigger di BLOB del servizio di archiviazione di Azure

#### function.json per trigger di BLOB di archiviazione

Il file *function.json* specifica le proprietà seguenti.

- `name`: nome della variabile usato nel codice della funzione per il BLOB.
- `path`: percorso che specifica il contenitore da monitorare e facoltativamente un modello di nome per il BLOB.
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia vuota la proprietà `connection`, il trigger userà la stringa di connessione di archiviazione predefinita per l'app per le funzioni, specificata dall'impostazione dell'app AzureWebJobsStorage.
- `type`: deve essere impostato su *blobTrigger*.
- `direction`: deve essere impostato su *in*.

Esempio di *function.json* per un trigger di BLOB di archiviazione che monitora i BLOB aggiunti al contenitore samples-workitems:

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
* String

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

È possibile specificare un modello di nome per il BLOB nella proprietà `path`, ad esempio:

```json
"path": "input/original-{name}",
```

Questo percorso troverà un BLOB denominato *original-Blob1.txt* nel contenitore *input* e il valore della variabile `name` nel codice della funzione sarà `Blob1`.

Un altro esempio:

```json
"path": "input/{blobname}.{blobextension}",
```

Anche questo percorso troverà un BLOB denominato *original-Blob1.txt* e i valori delle variabili `blobname` e `blobextension` nel codice della funzione saranno *original-Blob1* e *txt*.

È possibile limitare i tipi di BLOB che attivano la funzione specificando un modello con un valore fisso per l'estensione di file. Se si imposta `path` su *samples/{nome}.png*, la funzione verrà attivata solo dai BLOB *.png* nel contenitore *samples*.

Se è necessario specificare un modello di nome per i nomi di BLOB con parentesi graffe nel nome, raddoppiare le parentesi graffe. Se ad esempio si desidera trovare i BLOB nel contenitore *images* con nomi simile al seguente:

		{20140101}-soundfile.mp3

Usare la notazione seguente per la proprietà `path`:

		images/{{20140101}}-{name}

Nell'esempio il valore della variabile `name` è *soundfile.mp3*.

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

Se il contenitore BLOB monitorato dal trigger contiene più di 10.000 BLOB, il runtime di Funzioni analizza i file di log per cercare i BLOB nuovi o modificati. Questo processo non avviene in tempo reale. Una funzione potrebbe non essere attivata per diversi minuti o più dopo la creazione del BLOB. I [log di archiviazione vengono creati in base al principio del "massimo sforzo"](https://msdn.microsoft.com/library/azure/hh343262.aspx). Non è garantito che tutti gli eventi vengano acquisiti. In alcune condizioni, l'acquisizione dei log potrebbe non riuscire. Se le limitazioni di velocità e affidabilità dei trigger dei BLOB per i contenitori di grandi dimensioni non sono accettabili per l'applicazione, il metodo consigliato consiste nel creare un messaggio nella coda quando si crea il BLOB e usare un trigger della coda invece di un trigger di BLOB per elaborare il BLOB.
 
## <a id="storageblobbindings"></a> Associazioni di input e di output dei BLOB del servizio di archiviazione di Azure

#### function.json per un'associazione di input o output dei BLOB di archiviazione

Il file *function.json* specifica le proprietà seguenti.

- `name`: nome della variabile usato nel codice della funzione per il BLOB.
- `path`: percorso che specifica il contenitore dal quale leggere o nel quale scrivere il BLOB e facoltativamente un modello di nome di BLOB.
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia vuota la proprietà `connection`, l'associazione userà la stringa di connessione di archiviazione predefinita per l'app per le funzioni, specificata dall'impostazione dell'app AzureWebJobsStorage.
- `type`: deve essere impostato su *blob*.
- `direction`: deve essere impostato su *in* o *out*.

Esempio di *function.json* per un'associazione di input o di output dei BLOB di archiviazione con un trigger di coda per copiare un BLOB:

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

* Object (`out T` in C# per il BLOB di output. Crea un BLOB come oggetto Null se il valore del parametro è Null quando la funzione termina)
* String (`out string` in C# per il BLOB di output. Crea un BLOB solo se il parametro di stringa è diverso da Null quando la funzione restituisce un valore)

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

## <a id="storagetablesbindings"></a> Associazioni di input e di output delle tabelle di Archiviazione di Azure

#### function.json per tabelle di archiviazione

Il file *function.json* specifica le proprietà seguenti.

- `name`: nome della variabile usato nel codice della funzione per l'associazione delle tabelle.
- `tableName`: nome della tabella.
- `partitionKey` e `rowKey`: usati insieme per leggere una singola entità in una funzione C# o Node oppure per scrivere una singola entità in una funzione Node.
- `take`: numero massimo di righe da leggere per l'input della tabella in una funzione Node.
- `filter`: espressione di filtro OData per l'input della tabella in una funzione Node.
- `connection`: nome di un'impostazione dell'app che contiene una stringa di connessione di archiviazione. Se si lascia vuota la proprietà `connection`, l'associazione userà la stringa di connessione di archiviazione predefinita per l'app per le funzioni, specificata dall'impostazione dell'app AzureWebJobsStorage.
- `type`: deve essere impostato su *table*.
- `direction`: deve essere impostato su *in* o *out*.

Nell'esempio seguente *function.json* usa un trigger della coda per leggere una riga della tabella. JSON indica il valore di una chiave della partizione hardcoded e specifica che la chiave della riga è recuperata dal messaggio della coda.

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

L'esempio di codice C# seguente usa il file *function.json* precedente per leggere una singola entità della tabella. Il messaggio della coda ha il valore della chiave della riga e l'entità della tabella viene letta in un tipo definito nel file *run.csx*. Il tipo include le proprietà `PartitionKey` e `RowKey` e non deriva da `TableEntity`.

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

Anche l'esempio di codice Node seguente usa il file *function.json* precedente per leggere una singola entità della tabella.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Esempio di tabelle di archiviazione: lettura di più entità di tabella in C# 

Il codice di esempio di *function.json* e di C# seguente legge le entità per una chiave di partizione specificata nel messaggio della coda.

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

## Passaggi successivi

[AZURE.INCLUDE [Passaggi successivi](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->