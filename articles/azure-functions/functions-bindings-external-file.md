---
title: Associazioni di file esterni in Funzioni di Azure (Anteprima) | Microsoft Docs
description: Utilizzo di associazioni di file esterni in Funzioni di Azure
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 7e3b396d290212d3875385521bd7ae92da196b95
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="azure-functions-external-file-bindings-preview"></a>Associazioni di file esterni in Funzioni di Azure (Anteprima)
Questo articolo illustra come modificare i file da diversi provider di SaaS (ad esempio OneDrive, Dropbox) all'interno della funzione che usa binding incorporati. Funzioni di Azure supporta il trigger e le associazioni di output per i file esterni.

Il binding crea connessioni API ai provider SaaS o usa quelle esistenti prelevandole dal gruppo di risorse dell'app per le funzioni.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>Connessioni di file supportate

|Connettore|Trigger|Input|Output|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive for Business](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> È possibile usare le connessioni ai file esterni anche in [App per la logica di Azure](https://docs.microsoft.com/azure/connectors/apis-list)

## <a name="external-file-trigger-binding"></a>Associazione di trigger di file esterni

Il trigger di file esterni di Azure consente di monitorare una cartella remota ed eseguire il codice della funzione qualora vengano rilevate modifiche.

Tale trigger usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

### <a name="name-patterns"></a>Modelli di nome
È possibile specificare un modello di nome per il file nella proprietà `path`, La cartella a cui si fa riferimento deve esistere nel provider SaaS.
Esempi:

```json
"path": "input/original-{name}",
```

Questo percorso individua il file denominato *original-File1.txt* nella cartella *input* e il valore della variabile `name` nel codice della funzione sarà `File1.txt`.

Un altro esempio:

```json
"path": "input/{filename}.{fileextension}",
```

Anche questo percorso individua un file denominato *original-File1.txt* e i valori delle variabili `filename` e `fileextension` nel codice della funzione saranno *original-File1* e *txt*.

È possibile limitare il tipo di file dei file usando un valore fisso per l'estensione del file. Ad esempio:

```json
"path": "samples/{name}.png",
```

In questo caso, la funzione verrà attivata solo dai file *.png* nella cartella *samples*.

Le parentesi graffe sono caratteri speciali nei modelli di nome. Per specificare nomi di file con parentesi graffe nel nome, raddoppiare le parentesi graffe.
ad esempio:

```json
"path": "images/{{20140101}}-{name}",
```

Questo percorso troverà un file denominato *{20140101}-soundfile.mp3* nella cartella *images* e il valore della variabile `name` nel codice della funzione sarà *soundfile.mp3*.

<a name="receipts"></a>

<!--- ### File receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->
<a name="poison"></a>

### <a name="handling-poison-files"></a>Gestione di file non elaborabili
Quando una funzione di trigger del file esterno ha esito negativo, per impostazione predefinita Funzioni di Azure ritenta l'esecuzione fino a 5 volte (incluso il primo tentativo) per un dato messaggio.
Se tutti i 5 tentativi non riescono, Funzioni di Azure aggiunge un messaggio a una coda di archiviazione denominata *webjobs-apihubtrigger-poison*. Il messaggio di coda per i file non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato *&lt;nome dell'app per le funzioni>*.Functions.*&lt;nome della funzione>*)
* FileType
* FolderName
* FileName
* ETag (identificatore di versione del file, ad esempio: "0x8D1DC6E70A277EF")


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso dei trigger
Nelle funzioni C# l'associazione ai dati del file di input viene eseguita usando un parametro denominato nella firma funzione, ad esempio `<T> <name>`.
`T` è il tipo di dati in cui si vogliono deserializzare i dati e `paramName` è il nome specificato nel [JSON del trigger](#trigger). Nelle funzioni Node.js si accede ai dati del file di input usando `context.bindings.<name>`.

È possibile deserializzare il file in uno dei tipi seguenti:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx): utile per i dati dei file serializzati con JSON.
  Se si dichiara un tipo di input personalizzato, ad esempio `FooType`, Funzioni di Azure tenta di deserializzare i dati JSON nel tipo specificato.
* Stringa: utile per i dati del file di testo.

Nelle funzioni C# è anche possibile eseguire l'associazione a uno dei tipi seguenti e il runtime di Funzioni di Azure tenta di deserializzare i dati del file usando quel tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="trigger-sample"></a>Esempio di trigger
Si supponga di avere il function.json seguente, che definisce un trigger di file esterno:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Vedere l'esempio specifico del linguaggio che registra i contenuti di ogni file aggiunto alla cartella monitorata.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Uso dei trigger in C# #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
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
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>Associazione di input di file esterni
L'associazione di input di file esterni di Azure consente di usare un file da una cartella esterna nella funzione.

L'input del file esterno in una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

Tenere presente quanto segue:

* `path` deve contenere il nome della cartella e il nome del file. Se, ad esempio, nella funzione è presente un [trigger della coda](functions-bindings-storage-queue.md), è possibile usare `"path": "samples-workitems/{queueTrigger}"` in modo che punti a un file nella cartella `samples-workitems` con un nome che corrisponde al nome di file specificato nel messaggio di trigger.   

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso dell'input
Nelle funzioni C# l'associazione ai dati del file di input viene eseguita usando un parametro denominato nella firma funzione, ad esempio `<T> <name>`.
`T` è il tipo di dati in cui si vogliono deserializzare i dati e `paramName` è il nome specificato nell'[associazione di input](#input). Nelle funzioni Node.js si accede ai dati del file di input usando `context.bindings.<name>`.

È possibile deserializzare il file in uno dei tipi seguenti:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx): utile per i dati dei file serializzati con JSON.
  Se si dichiara un tipo di input personalizzato, ad esempio `InputType`, Funzioni di Azure tenta di deserializzare i dati JSON nel tipo specificato.
* Stringa: utile per i dati del file di testo.

Nelle funzioni C# è anche possibile eseguire l'associazione a uno dei tipi seguenti e il runtime di Funzioni di Azure tenta di deserializzare i dati del file usando quel tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`


<a name="output"></a>

## <a name="external-file-output-binding"></a>Associazione di output di file esterni
L'associazione di input di file esterni di Azure consente di usare un file da una cartella esterna nella funzione.

L'output del file esterno per una funzione usa gli oggetti JSON seguenti nella matrice `bindings` di function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

Tenere presente quanto segue:

* `path` deve contenere il nome della cartella e il nome del file in cui scrivere. Se, ad esempio, nella funzione è presente un [trigger della coda](functions-bindings-storage-queue.md), è possibile usare `"path": "samples-workitems/{queueTrigger}"` in modo che punti a un file nella cartella `samples-workitems` con un nome che corrisponde al nome di file specificato nel messaggio di trigger.   

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso dell'output
Nelle funzioni C# è possibile eseguire l'associazione al file di output usando il parametro denominato `out` nella firma funzione, ad esempio `out <T> <name>`, dove `T` è il tipo di dati in cui si vuole serializzare i dati e `paramName` è il nome specificato nell'[associazione di output](#output). Nelle funzioni Node.js si accede al file di output usando `context.bindings.<name>`.

È possibile scrivere nel file di output usando uno dei tipi seguenti:

* Qualsiasi [oggetto](https://msdn.microsoft.com/library/system.object.aspx), utile per la serializzazione con JSON.
  Se si dichiara un tipo di output personalizzato (ad esempio `out OutputType paramName`), Funzioni di Azure tenta di serializzare l'oggetto in JSON. Se il parametro di output è null quando la funzione viene chiusa, il runtime di Funzioni crea un file come un oggetto null.
* Stringa: `out string paramName`utile per i dati del file di testo. Il runtime di Funzioni crea un file solo se il parametro di stringa è diverso da null quando la funzione viene chiusa.

Nelle funzioni C# è anche possibile eseguire l'output in uno dei tipi seguenti:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>Esempio di input e output
Si supponga di avere il function.json seguente, che definisce un [trigger della coda di archiviazione](functions-bindings-storage-queue.md), un input del file esterno e un output del file esterno:

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
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Vedere l'esempio specifico del linguaggio che copia il file di input nel file di output.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>Utilizzo in C# #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>Uso in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

