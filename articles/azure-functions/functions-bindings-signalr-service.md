---
title: Associazioni del servizio SignalR per Funzioni di Azure
description: Informazioni su come usare le associazioni del servizio SignalR con Funzioni di Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/23/2018
ms.author: cshoe
ms.openlocfilehash: 7a7063b9177774c5207746283dc7cd25e3dd5793
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721887"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Associazioni del servizio SignalR per Funzioni di Azure

Questo articolo illustra come autenticare e inviare messaggi in tempo reale ai client connessi al [servizio Azure SignalR](https://azure.microsoft.com/services/signalr-service/) usando le associazioni del servizio SignalR in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per il servizio SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

Le associazioni del servizio SignalR sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) versione 1.0.0-preview1-*. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

> [!NOTE]
> Il servizio Azure SignalR è disponibile a livello generale. Tuttavia, le associazioni del servizio SignalR per Funzioni di Azure sono attualmente in anteprima.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>Associazione di input delle informazioni di connessione a SignalR

Prima che un client possa connettersi al servizio Azure SignalR, deve recuperare l'URL dell'endpoint di servizio e un token di accesso valido. L'associazione di input *SignalRConnectionInfo*genera l'URL dell'endpoint del servizio SignalR e un token valido per la connessione al servizio. Poiché il token ha una durata limitata e può essere usato per autenticare un utente specifico per una connessione, non deve essere memorizzato nella cache né condiviso tra client. I client possono usare un trigger HTTP con questa associazione per recuperare le informazioni di connessione.

Vedere l'esempio specifico per ciascun linguaggio:

* [C# 2.x](#2x-c-input-example)
* [JavaScript 2.x](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>Esempio di input C# 2.x

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che acquisisce le informazioni di connessione a SignalR usando l'associazione di input e le restituisce tramite HTTP.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Token autenticati

Se la funzione viene attivata da un client autenticato, è possibile aggiungere un'attestazione ID utente al token generato. Per aggiungere l'autenticazione a un'app per le funzioni, usare [Autenticazione servizio app] (../App-Service/Overview-Authentication-Authorization.MD).

Autenticazione servizio App imposta le intestazioni HTTP denominate `x-ms-client-principal-id` e `x-ms-client-principal-name` contenenti rispettivamente l'ID e il nome dell'entità client dell'utente autenticato. È possibile impostare la proprietà `UserId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>Esempio di input JavaScript 2.x

L'esempio seguente mostra un'associazione di input delle informazioni di connessione a SignalR in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione per restituire le informazioni di connessione.

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>Token autenticati

Se la funzione viene attivata da un client autenticato, è possibile aggiungere un'attestazione ID utente al token generato. Per aggiungere l'autenticazione a un'app per le funzioni, usare [Autenticazione servizio app] (../App-Service/Overview-Authentication-Authorization.MD).

Autenticazione servizio App imposta le intestazioni HTTP denominate `x-ms-client-principal-id` e `x-ms-client-principal-name` contenenti rispettivamente l'ID e il nome dell'entità client dell'utente autenticato. È possibile impostare la proprietà `userId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`. 

Function.json di esempio:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>Associazione di output SignalR

Usare l'associazione di output *SignalR* per inviare uno o più messaggi con il servizio Azure SignalR. È possibile trasmettere un messaggio a tutti i client connessi o solo ai client connessi che sono stati autenticati per un determinato utente.

Vedere l'esempio specifico per ciascun linguaggio:

* [C# 2.x](#2x-c-output-example)
* [JavaScript 2.x](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>Esempio di output C# 2.x

#### <a name="broadcast-to-all-clients"></a>Trasmettere a tutti i client

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che invia un messaggio con l'associazione di output a tutti i client connessi. `Target` è il nome del metodo da richiamare su ogni client. La proprietà `Arguments` è una matrice di zero o più oggetti da passare al metodo client.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>Inviare a un utente

È possibile inviare un messaggio solo alle connessioni autenticate per un utente impostando la proprietà `UserId` del messaggio SignalR.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>Esempio di output JavaScript 2.x

#### <a name="broadcast-to-all-clients"></a>Trasmettere a tutti i client

L'esempio seguente mostra un'associazione di output SignalR in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione per inviare un messaggio con il servizio Azure SignalR. Impostare l'associazione di output su una matrice di uno o più messaggi SignalR. Un messaggio SignalR è costituito da una proprietà `target` che specifica il nome del metodo da richiamare su ogni client e da una proprietà `arguments` che è una matrice di oggetti da passare al metodo client come argomenti.

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>Inviare a un utente

È possibile inviare un messaggio solo alle connessioni autenticate per un utente impostando la proprietà `userId` del messaggio SignalR.

*function.json* resta uguale. Ecco il codice JavaScript:

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>Configurazione

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `SignalRConnectionInfo`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type**|| Il valore deve essere impostato su `signalRConnectionInfo`.|
|**direction**|| Il valore deve essere impostato su `in`.|
|**nome**|| Nome della variabile usato nel codice della funzione per l'oggetto informazioni di connessione. |
|**hubName**|**HubName**| Questo valore deve essere impostato sul nome dell'hub SignalR per il quale vengono generate le informazioni di connessione.|
|**userId**|**UserId**| Facoltativo: valore dell'attestazione dell'ID utente da impostare nel token della chiave di accesso. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nome dell'impostazione app contenente la stringa di connessione al servizio SignalR (valore predefinito:"AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `SignalR`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type**|| Il valore deve essere impostato su `signalR`.|
|**direction**|| Il valore deve essere impostato su `out`.|
|**nome**|| Nome della variabile usato nel codice della funzione per l'oggetto informazioni di connessione. |
|**hubName**|**HubName**| Questo valore deve essere impostato sul nome dell'hub SignalR per il quale vengono generate le informazioni di connessione.|
|**connectionStringSetting**|**ConnectionStringSetting**| Nome dell'impostazione app contenente la stringa di connessione al servizio SignalR (valore predefinito:"AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

