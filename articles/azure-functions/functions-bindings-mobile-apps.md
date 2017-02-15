---
title: Associazioni di app per dispositivi mobili in Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare le associazioni di app per dispositivi mobili in Funzioni di Azure
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: c5e1c02984f9773b263c0bee7685c7d5ff62e658


---
# <a name="azure-functions-mobile-apps-bindings"></a>Associazioni di app per dispositivi mobili in Funzioni di Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e scrivere il codice delle [associazioni di App per dispositivi mobili di Azure](../app-service-mobile/app-service-mobile-value-prop.md) in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per App per dispositivi mobili.

Le associazioni di input e output per App per dispositivi mobili consentono di [leggere e scrivere tabelle di dati](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) nella propria app per dispositivi mobili.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Associazione di input di App per dispositivi mobili
L'associazione di input di App per dispositivi mobili carica un record da un endpoint tabella per dispositivi mobili e lo passa alla propria funzione. Nelle funzioni C# e F# eventuali modifiche apportate al record vengono automaticamente inviate alla tabella se la funzione termina correttamente.

L'input di App per dispositivi mobili in una funzione usa l'oggetto JSON seguente nella matrice `bindings` di function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

Tenere presente quanto segue:

* `id` può essere statico oppure può essere basato sul trigger che richiama la funzione. Se ad esempio si usa un [trigger della coda]() per la propria funzione, `"id": "{queueTrigger}"` userà il valore di stringa del messaggio della coda come ID del record da recuperare.
* `connection` deve contenere il nome di un'impostazione dell'app per le funzioni, in cui è contenuto l'URL dell'app per dispositivi mobili. La funzione usa questo URL per creare le operazioni REST da eseguire sull'app per dispositivi mobili. A questo scopo, è necessario [creare un'impostazione nell'app per le funzioni]() che contenga l'URL dell'app per dispositivi mobili (simile a `http://<appname>.azurewebsites.net`) e quindi specificare il nome dell'impostazione dell'app nella proprietà `connection` dell'associazione di input. 
* È necessario specificare `apiKey` se si [implementa una chiave API nel back-end dell'app per dispositivi mobili Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) o se si [implementa una chiave API nel back-end dell'app per dispositivi mobili .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). A questo scopo, è necessario [creare un'impostazione nell'app per le funzioni]() che contenga la chiave API e quindi aggiungere la proprietà `apiKey` nell'associazione di input con il nome dell'impostazione dell'app. 
  
  > [!IMPORTANT]
  > Questa chiave API non deve essere condivisa con i client dell'app per dispositivi mobili. Può essere distribuita in modo sicuro solo ai client sul lato servizio, come Funzioni di Azure. 
  > 
  > [!NOTE]
  > Funzioni di Azure archivia le informazioni di connessione e le chiavi API come impostazioni dell'app in modo che non vengano controllate nel repository di controllo del codice sorgente. In questo modo viene garantita la protezione delle informazioni riservate.
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso dell'input
In questa sezione viene illustrato come usare l'associazione di input di App per dispositivi mobili nel codice di funzione. 

Se il record corrispondente alla tabella e all'ID di record specificati viene trovato, il record viene passato al parametro denominato [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) (in Node.js viene passato all'oggetto `context.bindings.<name>`). Se il record non viene trovato, il parametro è `null`. 

Nelle funzioni C# e F# eventuali modifiche apportate al record di input (parametro di input) vengono automaticamente inviate alla tabella di App per dispositivi mobili quando la funzione termina correttamente. Nelle funzioni Node.js si accede al record di input usando `context.bindings.<name>`. In Node.js, inoltre, non è possibile modificare i record.

<a name="inputsample"></a>

## <a name="input-sample"></a>Esempio di input
Si supponga di avere il seguente function.json, che recupera un record della tabella di App per dispositivi mobili tramite l'ID del messaggio di attivazione della coda:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```

Vedere l'esempio specifico del linguaggio che usa il record di input dall'associazione. Gli esempi in C# e F# modificano anche la proprietà `text` del record.

* [C#](#inputcsharp)
* [Node.JS](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Esempio di input in C# #

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Esempio di input in Node.js

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Associazione di output di App per dispositivi mobili
L'associazione di output di App per dispositivi mobili consente di scrivere un nuovo record in un endpoint tabella di App per dispositivi mobili.  

L'output di App per dispositivi mobili per una funzione usa l'oggetto JSON seguente nella matrice `bindings` di function.json:

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

Tenere presente quanto segue:

* `connection` deve contenere il nome di un'impostazione dell'app per le funzioni, in cui è contenuto l'URL dell'app per dispositivi mobili. La funzione usa questo URL per creare le operazioni REST da eseguire sull'app per dispositivi mobili. A questo scopo, è necessario [creare un'impostazione nell'app per le funzioni]() che contenga l'URL dell'app per dispositivi mobili (simile a `http://<appname>.azurewebsites.net`) e quindi specificare il nome dell'impostazione dell'app nella proprietà `connection` dell'associazione di input. 
* È necessario specificare `apiKey` se si [implementa una chiave API nel back-end dell'app per dispositivi mobili Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) o se si [implementa una chiave API nel back-end dell'app per dispositivi mobili .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). A questo scopo, è necessario [creare un'impostazione nell'app per le funzioni]() che contenga la chiave API e quindi aggiungere la proprietà `apiKey` nell'associazione di input con il nome dell'impostazione dell'app. 
  
  > [!IMPORTANT]
  > Questa chiave API non deve essere condivisa con i client dell'app per dispositivi mobili. Può essere distribuita in modo sicuro solo ai client sul lato servizio, come Funzioni di Azure. 
  > 
  > [!NOTE]
  > Funzioni di Azure archivia le informazioni di connessione e le chiavi API come impostazioni dell'app in modo che non vengano controllate nel repository di controllo del codice sorgente. In questo modo viene garantita la protezione delle informazioni riservate.
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso dell'output
In questa sezione viene illustrato come usare l'associazione di output di App per dispositivi mobili nel codice di funzione. 

Nelle funzioni C# è necessario usare un parametro di output denominato di tipo `out object` per accedere al record di output. Nelle funzioni Node.js si accede al record di output usando `context.bindings.<name>`.

<a name="outputsample"></a>

## <a name="output-sample"></a>Esempio di output
Si supponga di avere il seguente function.json, che definisce un trigger della coda e un output di App per dispositivi mobili:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

Vedere l'esempio specifico del linguaggio che crea un record nell'endpoint tabella di App per dispositivi mobili con il contenuto del messaggio della coda.

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Esempio di output in C# #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Esempio di output in Node.js

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


