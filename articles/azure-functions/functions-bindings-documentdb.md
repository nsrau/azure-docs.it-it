---
title: Associazioni di DocumentDB in Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare le associazioni Azure DocumentDB n Funzioni di Azure.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/10/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2ac78606f851068fa0fb7dcab3bac1c629b9cdb3
ms.lasthandoff: 04/03/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Associazioni di DocumentDB in Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice di associazioni di Azure DocumentDB in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Per altre informazioni su DocumentDB, vedere [Introduzione a DocumentDB](../documentdb/documentdb-introduction.md) e [Compilare un'applicazione console di DocumentDB](../documentdb/documentdb-get-started.md).

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>Associazione di input di DocumentDB
L'associazione di input di DocumentDB recupera un documento di DocumentDB e lo passa al parametro di input denominato della funzione. L'ID documento può essere determinato in base al trigger che richiama la funzione. 

L'input di DocumentDB a una funzione usa l'oggetto JSON seguente nella matrice `bindings` di function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "id": "<Id of the DocumentDB document - see below>",
  "connection": "<Name of app setting with connection string - see below>",
  "direction": "in"
},
```

Tenere presente quanto segue:

* `id` supporta associazioni simili a `{queueTrigger}` che usa il valore stringa del messaggio della coda come ID documento.
* `connection` deve essere il nome di un'impostazione di app che punta all'endpoint per l'account di DocumentDB (con il valore `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). Se si crea un account di DocumentDB dall'interfaccia utente del portale Funzioni, il processo di creazione dell'account crea automaticamente un'impostazione dell'app. Per usare un account di DocumentDB esistente, è necessario [configurare manualmente questa impostazione dell'app](functions-how-to-use-azure-function-app-settings.md). 
* Se il documento specificato non viene trovato, il parametro di input denominato per la funzione viene impostato su `null`. 

## <a name="input-usage"></a>Uso dell'input
In questa sezione viene illustrato come usare l'associazione di input di DocumentDB nel codice di funzione.

Nelle funzioni C# e F# eventuali modifiche apportate al documento di input (parametro di input denominato) vengono automaticamente inviate alla raccolta quando la funzione termina correttamente. Nelle funzioni Node.js gli aggiornamenti del documento nell'associazione di input non vengono inviati alla raccolta. È tuttavia possibile usare `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` per eseguire aggiornamenti dei documenti di input. L'[esempio Node.js](#innodejs) illustra la procedura.

<a name="inputsample"></a>

## <a name="input-sample"></a>Esempio di input
Si supponga di avere la seguente associazione di input di DocumentDB nella matrice `bindings` di function.json:

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "in"
}
```

Vedere l'esempio specifico del linguaggio che usa questa associazione di input per aggiornare il valore di testo del documento.

* [C#](#incsharp)
* [F#](#infsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Esempio di input in C# #

```cs
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Esempio di input in F# #

```fsharp
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

È necessario aggiungere un file `project.json` che specifichi le dipendenze NuGet `FSharp.Interop.Dynamic` e `Dynamitey`:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Per aggiungere un file `project.json`, vedere l'argomento relativo alla [gestione dei pacchetti F #](functions-reference-fsharp.md#package).

<a name="innodejs"></a>

### <a name="input-sample-in-nodejs"></a>Esempio di input in Node.js

```javascript
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a id="docdboutput"></a>Associazione di output di DocumentDB
L'associazione di output di DocumentDB consente di scrivere un nuovo documento in un database di Azure DocumentDB. 

L'associazione di output usa l'oggetto JSON seguente nella matrice `bindings` di function.json: 

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "createIfNotExists": <true or false - see below>,
  "connection": "<Value of AccountEndpoint in Application Setting - see below>",
  "direction": "out"
}
```

Tenere presente quanto segue:

* Impostare `createIfNotExists` su `true` per creare il database e la raccolta se non esiste. Il valore predefinito è `false`. Le nuove raccolte vengono create con una velocità effettiva riservata, che ha implicazioni in termini di prezzi. Per altre informazioni, vedere [DocumentDB - Prezzi](https://azure.microsoft.com/pricing/details/documentdb/).
* `connection` deve essere il nome di un'impostazione di app che punta all'endpoint per l'account di DocumentDB (con il valore `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). Se si crea un account di DocumentDB dall'interfaccia utente del portale Funzioni, il processo di creazione dell'account crea automaticamente una nuova impostazione dell'app. Per usare un account di DocumentDB esistente, è necessario [configurare manualmente questa impostazione dell'app](functions-how-to-use-azure-function-app-settings.md). 

## <a name="output-usage"></a>Uso dell'output
In questa sezione viene illustrato come usare l'associazione di output di DocumentDB nel codice di funzione.

Quando si scrive per il parametro di output nella funzione, per impostazione predefinita viene generato un nuovo documento nel database con un GUID generato automaticamente come ID documento. È possibile specificare l'ID documento del documento di output specificando la proprietà JSON `id` nel parametro di output. 

>[!Note]  
>Quando si specifica l'ID di un documento esistente, questo viene sovrascritto dal nuovo documento di output. 

È possibile scrivere nell'output usando uno dei tipi seguenti:

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

Per visualizzare più documenti, è anche possibile definire l'associazione a `ICollector<T>` o `IAsyncCollector<T>` dove `T` è uno dei tipi supportati.


<a name="outputsample"></a>

## <a name="output-sample"></a>Esempio di output
Si supponga di avere la seguente associazione di output di DocumentDB nella matrice `bindings` di function.json:

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "out"
}
```

Ed è disponibile un'associazione di input di coda per una coda che riceve JSON nel formato seguente:

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Si vogliono creare documenti di DocumentDB nel formato seguente per ogni record:

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Vedere l'esempio specifico del linguaggio che usa questa associazione di output per aggiungere i documenti al database.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Esempio di output in C# #

```cs
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
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Esempio di output in F# #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

È necessario aggiungere un file `project.json` che specifichi le dipendenze NuGet `FSharp.Interop.Dynamic` e `Dynamitey`:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Per aggiungere un file `project.json`, vedere l'argomento relativo alla [gestione dei pacchetti F #](functions-reference-fsharp.md#package).

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Esempio di output in Node.js

```javascript
module.exports = function (context) {

  context.bindings.employeeDocument = JSON.stringify({ 
    id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
    name: context.bindings.myQueueItem.name,
    employeeId: context.bindings.myQueueItem.employeeId,
    address: context.bindings.myQueueItem.address
  });

  context.done();
};
```

