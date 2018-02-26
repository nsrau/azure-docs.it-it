---
title: Associazioni di app per dispositivi mobili per Funzioni di Azure
description: Informazioni su come usare le associazioni di app per dispositivi mobili in Funzioni di Azure
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: a1e4f15747031ba75ba5ae589557750919a71853
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Associazioni di app per dispositivi mobili per Funzioni di Azure 

Questo articolo descrive come usare le associazioni di [app per dispositivi mobili](../app-service-mobile/app-service-mobile-value-prop.md) in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per App per dispositivi mobili.

Le associazioni di app per dispositivi mobili consentono di leggere e aggiornare le tabelle di dati nelle app per dispositivi mobili.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="input"></a>Input

L'associazione di input di App per dispositivi mobili carica un record da un endpoint tabella per dispositivi mobili e lo passa alla propria funzione. Nelle funzioni C# e F# eventuali modifiche apportate al record vengono automaticamente inviate alla tabella se la funzione termina correttamente.

## <a name="input---example"></a>Input - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [Script C# (file con estensione csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Input - esempio di script C#

L'esempio seguente mostra un'associazione di input dell'app per dispositivi mobili in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione viene attivata da un messaggio in coda contenente un identificatore di record. La funzione legge il record specificato e modifica la proprietà `Text`.

Ecco i dati di associazione nel file *function.json*:

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
Queste proprietà sono descritte nella sezione [configuration](#input---configuration).

Ecco il codice script C#:

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

### <a name="input---javascript"></a>Input - JavaScript

L'esempio seguente mostra un'associazione di input dell'app per dispositivi mobili in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione viene attivata da un messaggio in coda contenente un identificatore di record. La funzione legge il record specificato e modifica la proprietà `Text`.

Ecco i dati di associazione nel file *function.json*:

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
Queste proprietà sono descritte nella sezione [configuration](#input---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Input - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs), definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps).

Per informazioni sulle proprietà degli attributi che è possibile configurare, vedere la [sezione seguente relativa alla configurazione](#input---configuration).

## <a name="input---configuration"></a>Input - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `MobileTable`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
| **type**|| Deve essere impostato su "mobileTable"|
| **direction**||Deve essere impostato su "in"|
| **nome**|| Nome del parametro di input nella firma della funzione.|
|**tableName** |**TableName**|Nome della tabella di dati dell'app per dispositivi mobili|
| **id**| **Id** | Identificatore del record da recuperare. Può essere statico o basato sul trigger che richiama la funzione. Se, ad esempio, si usa un trigger della coda per la funzione, `"id": "{queueTrigger}"` userà il valore di stringa del messaggio della coda come ID del record da recuperare.|
|**connessione**|**Connection**|Il nome di un'impostazione dell'app con l'URL dell'app per dispositivi mobili. La funzione usa questo URL per creare le operazioni REST da eseguire sull'app per dispositivi mobili. Creare un'impostazione nell'app per le funzioni che contenga l'URL dell'app per dispositivi mobili e quindi specificare il nome dell'impostazione dell'app nella proprietà `connection` dell'associazione di input. L'URL ha l'aspetto seguente: `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|Il nome di un'impostazione dell'app con la chiave API dell'app per dispositivi mobili. Specificare la chiave API se si [implementa una chiave API nell'app per dispositivi mobili Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) oppure se si [implementa una chiave API nell'app per dispositivi mobili .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Per specificare la chiave, creare un'impostazione nell'app per le funzioni che contenga la chiave API e quindi aggiungere la proprietà `apiKey` nell'associazione di input con il nome dell'impostazione dell'app. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Non condividere la chiave API con i client dell'app per dispositivi mobili. Può essere distribuita in modo sicuro solo ai client sul lato servizio, come Funzioni di Azure. Funzioni di Azure archivia le informazioni di connessione e le chiavi API come impostazioni dell'app in modo che non vengano controllate nel repository di controllo del codice sorgente. In questo modo viene garantita la protezione delle informazioni riservate.

## <a name="input---usage"></a>Input - uso

Quando nelle funzioni C# viene trovato, il record con l'ID specificato viene passato nel parametro denominato [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm). Se il record non viene trovato, il valore del parametro è `null`. 

Nelle funzioni JavaScript il record viene passato all'oggetto `context.bindings.<name>`. Se il record non viene trovato, il valore del parametro è `null`. 

Nelle funzioni C# e F# eventuali modifiche apportate al record di input (parametro di input) vengono automaticamente inviate alla tabella quando la funzione termina correttamente. Non è possibile modificare un record nelle funzioni JavaScript.

## <a name="output"></a>Output

L'associazione di output di app per dispositivi mobili consente di scrivere un nuovo record in una tabella di app per dispositivi mobili.  

## <a name="output---example"></a>Output - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#output---c-example)
* [Script C# (file con estensione csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Output - esempio in C#

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che viene attivata da un messaggio in coda e che crea un record in una tabella di app per dispositivi mobili.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Output - esempio di script C#

L'esempio seguente mostra un'associazione di output dell'app per dispositivi mobili in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione viene attivata da un messaggio in coda e crea un nuovo record con valore hardcoded per la proprietà `Text`.

Ecco i dati di associazione nel file *function.json*:

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

Queste proprietà sono descritte nella sezione [configuration](#output---configuration).

Ecco il codice script C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Output - esempio JavaScript

L'esempio seguente mostra un'associazione di output dell'app per dispositivi mobili in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione viene attivata da un messaggio in coda e crea un nuovo record con valore hardcoded per la proprietà `Text`.

Ecco i dati di associazione nel file *function.json*:

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

Queste proprietà sono descritte nella sezione [configuration](#output---configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Output - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs), definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps).

Per informazioni sulle proprietà degli attributi che è possibile configurare, vedere la sezione [Output - configurazione](#output---configuration). Di seguito è riportato un esempio dell'attributo `MobileTable` in una firma del metodo:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Per un esempio completo, vedere [Output - esempio in C#](#output---c-example).

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `MobileTable`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
| **type**|| Deve essere impostato su "mobileTable"|
| **direction**||Deve essere impostato su "out"|
| **nome**|| Nome del parametro di output nella firma della funzione.|
|**tableName** |**TableName**|Nome della tabella di dati dell'app per dispositivi mobili|
|**connessione**|**MobileAppUriSetting**|Il nome di un'impostazione dell'app con l'URL dell'app per dispositivi mobili. La funzione usa questo URL per creare le operazioni REST da eseguire sull'app per dispositivi mobili. Creare un'impostazione nell'app per le funzioni che contenga l'URL dell'app per dispositivi mobili e quindi specificare il nome dell'impostazione dell'app nella proprietà `connection` dell'associazione di input. L'URL ha l'aspetto seguente: `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|Il nome di un'impostazione dell'app con la chiave API dell'app per dispositivi mobili. Specificare la chiave API se si [implementa una chiave API nel back-end dell'app per dispositivi mobili Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) o se si [implementa una chiave API nel back-end dell'app per dispositivi mobili .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Per specificare la chiave, creare un'impostazione nell'app per le funzioni che contenga la chiave API e quindi aggiungere la proprietà `apiKey` nell'associazione di input con il nome dell'impostazione dell'app. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Non condividere la chiave API con i client dell'app per dispositivi mobili. Può essere distribuita in modo sicuro solo ai client sul lato servizio, come Funzioni di Azure. Funzioni di Azure archivia le informazioni di connessione e le chiavi API come impostazioni dell'app in modo che non vengano controllate nel repository di controllo del codice sorgente. In questo modo viene garantita la protezione delle informazioni riservate.

## <a name="output---usage"></a>Output - uso

Nelle funzioni script C# è necessario usare un parametro di output denominato di tipo `out object` per accedere al record di output. Nelle librerie di classi C#, l'attributo `MobileTable` può essere usato con uno qualsiasi dei tipi seguenti:

* `ICollector<T>` o `IAsyncCollector<T>`, dove `T` è `JObject` o qualsiasi tipo con una proprietà `public string Id`.
* `out JObject`
* `out T` o `out T[]`, dove `T` è un tipo qualsiasi con una proprietà `public string Id`.

Nelle funzioni Node.js si accede al record di output usando `context.bindings.<name>`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
