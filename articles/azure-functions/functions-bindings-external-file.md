---
title: Associazioni di File esterne per le funzioni di Azure (sperimentale)
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
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Associazioni di File esterno funzioni Azure (sperimentale)
In questo articolo viene illustrato come modificare i file dai diversi provider SaaS (ad esempio l'unità di Google o Dropbox) nelle funzioni di Azure. Azure supporta funzioni trigger, l'input e output associazioni di file esterni. Queste associazioni creano connessioni di API per i provider SaaS o utilizzano le connessioni esistenti di API dal gruppo di risorse dell'applicazione (funzione).

> [!IMPORTANT]
> Le associazioni di File esterno sono sperimentale e potrebbero non raggiungere mai lo stato in genere disponibili (GA). Sono inclusi solo in Azure non sono previste per aggiungerle a funzioni di Azure e le funzioni di 1. x 2. x. Per gli scenari che richiedono l'accesso ai dati in provider SaaS, è consigliabile usare [logica App che chiamano funzioni](functions-twitter-email.md). Vedere il [connettore File System di logica app](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Connessioni per file disponibili

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
> Le connessioni esterne File utilizzabili [Azure logica app](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Trigger

Il trigger di file esterno consente di monitorare una cartella remota ed eseguire il codice di funzione, quando vengono rilevate modifiche.

## <a name="trigger---example"></a>Trigger - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Trigger - esempio di script C#

Nell'esempio seguente viene illustrato un trigger di file esterno, l'associazione in un *function.json* file e un [la funzione di script c#](functions-reference-csharp.md) che utilizza l'associazione. La funzione Registra il contenuto di ogni file che viene aggiunto alla cartella monitorata.

Ecco i dati di associazione nel file *function.json*:

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

Ecco il codice script C#:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Trigger - esempio JavaScript

Nell'esempio seguente viene illustrato un trigger di file esterno, l'associazione in un *function.json* file e un [funzione JavaScript](functions-reference-node.md) che utilizza l'associazione. La funzione Registra il contenuto di ogni file che viene aggiunto alla cartella monitorata.

Ecco i dati di associazione nel file *function.json*:

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

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json*.

|Proprietà di function.json | DESCRIZIONE|
|---------|---------|----------------------|
|**type** | Il valore deve essere impostato su `apiHubFileTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | Nome della variabile che rappresenta l'elemento evento nel codice della funzione. | 
|**connessione**| Identifica l'impostazione dell'app che archivia la stringa di connessione. L'impostazione dell'app viene creata automaticamente quando si aggiunge una connessione nell'interfaccia utente di integrazione nel portale di Azure.|
|**path** | La cartella da monitorare e facoltativamente un modello di nome.|

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
Ad esempio: 

```json
"path": "images/{{20140101}}-{name}",
```

Questo percorso troverà un file denominato *{20140101}-soundfile.mp3* nella cartella *images* e il valore della variabile `name` nel codice della funzione sarà *soundfile.mp3*.

## <a name="trigger---usage"></a>Trigger - uso

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

<!--- ## Trigger - file receipts
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

## <a name="trigger---poison-files"></a>Trigger - file di messaggi non elaborabili

Quando una funzione di trigger del file esterno ha esito negativo, per impostazione predefinita Funzioni di Azure ritenta l'esecuzione fino a 5 volte (incluso il primo tentativo) per un dato messaggio.
Se tutti i 5 tentativi non riescono, Funzioni di Azure aggiunge un messaggio a una coda di archiviazione denominata *webjobs-apihubtrigger-poison*. Il messaggio di coda per i file non elaborabili è un oggetto JSON che contiene le seguenti proprietà:

* FunctionId (nel formato *&lt;nome dell'app per le funzioni>*.Functions.*&lt;nome della funzione>*)
* FileType
* FolderName
* FileName
* ETag (identificatore di versione del file, ad esempio: "0x8D1DC6E70A277EF")

## <a name="input"></a>Input

L'associazione di input di file esterni di Azure consente di usare un file da una cartella esterna nella funzione.

## <a name="input---example"></a>Input - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Input - esempio di script C#

L'esempio seguente Mostra file esterni associazioni di input e outpue in un *function.json* file e un [la funzione di script c#](functions-reference-csharp.md) che utilizza l'associazione. La funzione copia un file di input in un file di output.

Ecco i dati di associazione nel file *function.json*:

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

Ecco il codice script C#:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Input - esempio JavaScript

L'esempio seguente Mostra file esterni associazioni di input e outpue in un *function.json* file e un [funzione JavaScript](functions-reference-node.md) che utilizza l'associazione. La funzione copia un file di input in un file di output.

Ecco i dati di associazione nel file *function.json*:

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

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Input - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json*.

|Proprietà di function.json | DESCRIZIONE|
|---------|---------|----------------------|
|**type** | Il valore deve essere impostato su `apiHubFile`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | Nome della variabile che rappresenta l'elemento evento nel codice della funzione. | 
|**connessione**| Identifica l'impostazione dell'app che archivia la stringa di connessione. L'impostazione dell'app viene creata automaticamente quando si aggiunge una connessione nell'interfaccia utente di integrazione nel portale di Azure.|
|**path** | Deve contenere il nome della cartella e il nome del file. Se, ad esempio, nella funzione è presente un [trigger della coda](functions-bindings-storage-queue.md), è possibile usare `"path": "samples-workitems/{queueTrigger}"` in modo che punti a un file nella cartella `samples-workitems` con un nome che corrisponde al nome di file specificato nel messaggio di trigger.   

## <a name="input---usage"></a>Input - uso

Nelle funzioni C# l'associazione ai dati del file di input viene eseguita usando un parametro denominato nella firma funzione, ad esempio `<T> <name>`. `T`è il tipo di dati che si desidera deserializzare i dati in e `name` è il nome specificato nell'associazione di input. Nelle funzioni Node.js si accede ai dati del file di input usando `context.bindings.<name>`.

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

## <a name="output"></a>Output

L'associazione di input di file esterni di Azure consente di usare un file da una cartella esterna nella funzione.

## <a name="output---example"></a>Output - esempio

Vedere il [esempio di associazione di input](#input---example).

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json*.

|Proprietà di function.json | DESCRIZIONE|
|---------|---------|----------------------|
|**type** | Il valore deve essere impostato su `apiHubFile`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | Il valore deve essere impostato su `out`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | Nome della variabile che rappresenta l'elemento evento nel codice della funzione. | 
|**connessione**| Identifica l'impostazione dell'app che archivia la stringa di connessione. L'impostazione dell'app viene creata automaticamente quando si aggiunge una connessione nell'interfaccia utente di integrazione nel portale di Azure.|
|**path** | Deve contenere il nome della cartella e il nome del file. Se, ad esempio, nella funzione è presente un [trigger della coda](functions-bindings-storage-queue.md), è possibile usare `"path": "samples-workitems/{queueTrigger}"` in modo che punti a un file nella cartella `samples-workitems` con un nome che corrisponde al nome di file specificato nel messaggio di trigger.   

## <a name="output---usage"></a>Output - uso

In c# le funzioni, è associare al file di output utilizzando l'oggetto denominato `out` parametro nella firma di funzione, ad esempio `out <T> <name>`, dove `T` è il tipo di dati che si desidera serializzare i dati, e `name` è il nome specificato in l'associazione di output. Nelle funzioni Node.js si accede al file di output usando `context.bindings.<name>`.

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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
