---
title: Associazioni del BLOB del servizio di archiviazione di Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare trigger e associazioni di Archiviazione di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/02/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 880ea646b1e976975f610ce81d0b372e81d2e34a


---
# <a name="azure-functions-storage-blob-bindings"></a>Associazioni del BLOB del servizio di archiviazione di Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice delle associazioni del BLOB del servizio di archiviazione di Azure in Funzioni di Azure. Funzioni di Azure supporta il trigger e le associazioni di output per i BLOB del servizio di archiviazione di Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Non sono supportati [account di archiviazione solo BLOB](../storage/storage-create-storage-account.md#blob-storage-accounts). Funzioni di Azure richiede un account di archiviazione generico da usare con i BLOB. 
> 
> 

<a name="trigger"></a>

## <a name="storage-blob-trigger"></a>Trigger del BLOB del servizio di archiviazione
Il trigger del BLOB del servizio di archiviazione di Azure consente di verificare la presenza di BLOB nuovi o aggiornati in un contenitore di archiviazione e di agire di conseguenza. 

Il trigger del BLOB del servizio di archiviazione in una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection":"<Name of app setting - see below>"
}
```

Tenere presente quanto segue:

* Per `path`, vedere [Modelli di nome](#pattern) per scoprire come formattare i modelli di nome dei BLOB.
* `connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integra** configura automaticamente questa impostazione app quando si crea un account di archiviazione o si seleziona un account già esistente. Per creare manualmente questa impostazione app, vedere la sezione relativa alla [configurazione manuale dell'impostazione app](). 

Per altre informazioni vedere anche una delle sezioni seguenti:

* [Modelli di nome](#pattern)
* [Conferme di BLOB](#receipts)
* [Gestione di BLOB non elaborabili](#poison)

<a name="pattern"></a>

### <a name="name-patterns"></a>Modelli di nome
È possibile specificare un modello di nome per il BLOB nella proprietà `path`, Ad esempio:

```json
"path": "input/original-{name}",
```

Questo percorso troverà un BLOB denominato *original-Blob1.txt* nel contenitore *input* e il valore della variabile `name` nel codice della funzione sarà `Blob1`.

Un altro esempio:

```json
"path": "input/{blobname}.{blobextension}",
```

Anche questo percorso troverà un BLOB denominato *original-Blob1.txt* e i valori delle variabili `blobname` e `blobextension` nel codice della funzione saranno *original-Blob1* e *txt*.

È possibile limitare il tipo di file dei BLOB usando un valore fisso per l'estensione del file. Ad esempio:

```json
"path": "samples/{name}.png",
```

In questo caso, la funzione verrà attivata solo dai BLOB *.png* nel contenitore *samples*.

Le parentesi graffe sono caratteri speciali nei modelli di nome. Per specificare nomi di BLOB con parentesi graffe nel nome, raddoppiare le parentesi graffe. Ad esempio:

```json
"path": "images/{{20140101}}-{name}",
```

Questo percorso troverà un BLOB denominato *{20140101}-soundfile.mp3* nel contenitore *images* e il valore della variabile `name` nel codice della funzione sarà *soundfile.mp3*. 

<a name"receipts"></a>

### <a name="blob-receipts"></a>Conferme di BLOB
Il runtime di Funzioni di Azure verifica che nessuna funzione trigger di BLOB venga chiamata più volte per lo stesso BLOB nuovo o aggiornato. A questo scopo, gestisce *conferme di BLOB* per determinare se una versione di BLOB specifica è stata elaborata.

Le conferme di BLOB vengono archiviate in un contenitore denominato *azure-webjobs-hosts* nell'account di archiviazione di Azure associato all'app per le funzioni (specificato dall'impostazione app `AzureWebJobsStorage`). Una conferma di BLOB contiene le seguenti informazioni:

* La funzione attivata ("*&lt;nome dell'app per le funzioni>*.Functions.*&lt;nome della funzione>*", ad esempio: "functionsf74b96f7.Functions.CopyBlob")
* Il nome del contenitore
* Il tipo di BLOB ("BlockBlob" o "PageBlob")
* Il nome del BLOB
* Il valore ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

Per forzare la rielaborazione di un BLOB è possibile eliminare manualmente la conferma del BLOB dal contenitore *azure-webjobs-hosts*.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Gestione di BLOB non elaborabili
Se una funzione di trigger del BLOB ha esito negativo, per impostazione predefinita Funzioni di Azure ritenta l'esecuzione fino a 5 volte (incluso il primo tentativo) per ogni BLOB. Se tutti i 5 tentativi non riescono, Funzioni aggiunge un messaggio a una coda di archiviazione denominata *webjobs-blobtrigger-poison*. Il messaggio di coda per i BLOB non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato *&lt;nome dell'app per le funzioni>*.Functions.*&lt;nome della funzione>*)
* BlobType ("BlockBlob" o "PageBlob")
* ContainerName
* BlobName
* ETag (identificatore di versione del BLOB, ad esempio: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>Polling dei BLOB per contenitori di grandi dimensioni
Se il contenitore BLOB monitorato dall'associazione contiene più di 10.000 BLOB, il runtime di Funzioni analizza i file di log per cercare i BLOB nuovi o modificati. Questo processo non avviene in tempo reale. È possibile quindi che una funzione non venga attivata per diversi minuti o più dopo la creazione del BLOB. I log di archiviazione, inoltre, [vengono creati in base al principio del "massimo sforzo"](https://msdn.microsoft.com/library/azure/hh343262.aspx). Non è garantito che tutti gli eventi vengano acquisiti. In alcune condizioni, l'acquisizione dei log può non riuscire. Se le limitazioni di velocità e affidabilità dei trigger dei BLOB per i contenitori di grandi dimensioni non sono accettabili per l'applicazione, il metodo consigliato consiste nel creare un [messaggio nella coda](../storage/storage-dotnet-how-to-use-queues.md) quando si crea il BLOB e usare un [trigger della coda](functions-bindings-storage-queue.md), anziché un trigger di BLOB, per elaborare il BLOB.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso dei trigger
Nelle funzioni C# l'associazione ai dati del BLOB di input viene eseguita usando un parametro denominato nella firma funzione, ad esempio `<T> <name>`.
`T` è il tipo di dati in cui si vogliono deserializzare i dati e `paramName` è il nome specificato nel [JSON del trigger](#trigger). Nelle funzioni Node.js si accede a dati del BLOB di input usando `context.bindings.<name>`.

Il BLOB può essere deserializzato in uno dei tipi seguenti:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx), utile per i dati del BLOB serializzati con JSON.
  Se si dichiara un tipo di input personalizzato, ad esempio `FooType`, Funzioni di Azure tenta di deserializzare i dati JSON nel tipo specificato.
* Stringa, utile per i dati di testo del BLOB.

Nelle funzioni C# è anche possibile eseguire l'associazione a uno dei seguenti tipi e il runtime di Funzioni tenterà di deserializzare i dati del BLOB usando quel tipo:

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

## <a name="trigger-sample"></a>Esempio di trigger
Si supponga di avere il seguente function.json, che definisce un trigger del BLOB del servizio di archiviazione:

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

Vedere l'esempio specifico del linguaggio che registra i contenuti di ogni BLOB aggiunto al contenitore monitorato.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Uso dei trigger in C# #

```cs
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

``` 
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Uso dei trigger in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

<a name="input"></a>

## <a name="storage-blob-input-binding"></a>Associazione di input nel BLOB del servizio di archiviazione
L'associazione di input nel BLOB del servizio di archiviazione di Azure consente di usare un BLOB proveniente da un contenitore di archiviazione nella funzione. 

L'input del BLOB del servizio di archiviazione in una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "blob",
  "direction": "in"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

Tenere presente quanto segue:

* `path` deve contenere il nome del contenitore e il nome del BLOB. Se, ad esempio, nella funzione è presente un [trigger della coda](functions-bindings-storage-queue.md), è possibile usare `"path": "samples-workitems/{queueTrigger}"` in modo che punti a un BLOB nel contenitore `samples-workitems` con un nome che corrisponde al nome di BLOB specificato nel messaggio di trigger.   
* `connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integra** configura automaticamente questa impostazione app quando si crea un account di archiviazione o si seleziona un account già esistente. Per creare manualmente questa impostazione app, vedere la sezione relativa alla [configurazione manuale dell'impostazione app](). 

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso dell'input
Nelle funzioni C# l'associazione ai dati del BLOB di input viene eseguita usando un parametro denominato nella firma funzione, ad esempio `<T> <name>`.
`T` è il tipo di dati in cui si vogliono deserializzare i dati e `paramName` è il nome specificato nell'[associazione di input](#input). Nelle funzioni Node.js si accede a dati del BLOB di input usando `context.bindings.<name>`.

Il BLOB può essere deserializzato in uno dei tipi seguenti:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx), utile per i dati del BLOB serializzati con JSON.
  Se si dichiara un tipo di input personalizzato, ad esempio `FooType`, Funzioni di Azure tenta di deserializzare i dati JSON nel tipo specificato.
* Stringa, utile per i dati di testo del BLOB.

Nelle funzioni C# è anche possibile eseguire l'associazione a uno dei seguenti tipi e il runtime di Funzioni tenterà di deserializzare i dati del BLOB usando quel tipo:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="inputsample"></a>

## <a name="input-sample"></a>Esempio di input
Si supponga di avere il seguente function.json, che definisce un [trigger della coda di archiviazione](functions-bindings-storage-queue.md), un input del BLOB di archiviazione e un output del BLOB di archiviazione:

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

Vedere l'esempio specifico del linguaggio che copia il BLOB di input nel BLOB di output.

* [C#](#incsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>

### <a name="input-usage-in-c"></a>Uso dell'input in C# #

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

``` 
-->

<a name="innodejs"></a>

### <a name="input-usage-in-nodejs"></a>Uso dell'input in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

<a name="output"></a>

## <a name="storage-blob-output-binding"></a>Associazione di output nel BLOB del servizio di archiviazione
L'associazione di output nel BLOB del servizio di archiviazione di Azure consente di scrivere in BLOB presenti in un contenitore di archiviazione nella funzione. 

L'output del BLOB del servizio di archiviazione per una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "blob",
  "direction": "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
}
```

Tenere presente quanto segue:

* `path` deve contenere il nome del contenitore e il nome del BLOB in cui scrivere. Se, ad esempio, nella funzione è presente un [trigger della coda](functions-bindings-storage-queue.md), è possibile usare `"path": "samples-workitems/{queueTrigger}"` in modo che punti a un BLOB nel contenitore `samples-workitems` con un nome che corrisponde al nome di BLOB specificato nel messaggio di trigger.   
* `connection` deve contenere il nome di un'impostazione app che contiene una stringa di connessione di archiviazione. Nel portale di Azure l'editor standard disponibile nella scheda **Integra** configura automaticamente questa impostazione app quando si crea un account di archiviazione o si seleziona un account già esistente. Per creare manualmente questa impostazione app, vedere la sezione relativa alla [configurazione manuale dell'impostazione app](). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso dell'output
Nelle funzioni C# è possibile eseguire l'associazione al BLOB di output usando il parametro denominato `out` nella firma funzione, ad esempio `out <T> <name>`, dove `T` è il tipo di dati in cui si vuole serializzare i dati e `paramName` è il nome specificato nell'[associazione di output](#output). Nelle funzioni Node.js si accede al BLOB di output usando `context.bindings.<name>`.

È possibile scrivere nel BLOB di output usando uno dei tipi seguenti:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx), utile per la serializzazione con JSON.
  Se si dichiara un tipo di output personalizzato (ad esempio `out FooType paramName`), Funzioni di Azure tenta di serializzare l'oggetto in JSON. Se il parametro di output è null quando la funzione viene chiusa, il runtime di Funzioni crea un BLOB come un oggetto null.
* Stringa, (`out string paramName`) utile per i dati di testo del BLOB. Il runtime di Funzioni crea un BLOB solo se il parametro di stringa è diverso da null quando la funzione viene chiusa.

Nelle funzioni C# è anche possibile eseguire l'output in uno dei tipi seguenti:

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="outputsample"></a>

## <a name="output-sample"></a>Esempio di output
Vedere [esempio di input](#inputsample).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


