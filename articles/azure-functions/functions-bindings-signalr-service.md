---
title: Associazioni del servizio SignalR per Funzioni di Azure
description: Informazioni su come usare le associazioni del servizio SignalR con Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 4c7d5d4d8777fee445585b43b58ceb261176b7f4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231028"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Associazioni del servizio SignalR per Funzioni di Azure

Questo articolo illustra come autenticare e inviare messaggi in tempo reale ai client connessi al [servizio Azure SignalR](https://azure.microsoft.com/services/signalr-service/) usando le associazioni del servizio SignalR in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per il servizio SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

The SignalR Service bindings are provided in the [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet package, version 1.*. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java annotations

To use the SignalR Service annotations in Java functions, you need to add a dependency to the *azure-functions-java-library-signalr* artifact (version 1.0 or higher) to your pom.xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Per usare le associazioni del servizio SignalR in Java, verificare di usare la versione 2.4.419 o versione successiva di Azure Functions Core Tools (versione host 2.0.12332).

## <a name="using-signalr-service-with-azure-functions"></a>Using SignalR Service with Azure Functions

For details on how to configure and use SignalR Service and Azure Functions together, refer to [Azure Functions development and configuration with Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Associazione di input delle informazioni di connessione a SignalR

Prima che un client possa connettersi al servizio Azure SignalR, deve recuperare l'URL dell'endpoint di servizio e un token di accesso valido. L'associazione di input *SignalRConnectionInfo*genera l'URL dell'endpoint del servizio SignalR e un token valido per la connessione al servizio. Poiché il token ha una durata limitata e può essere usato per autenticare un utente specifico per una connessione, non deve essere memorizzato nella cache né condiviso tra client. I client possono usare un trigger HTTP con questa associazione per recuperare le informazioni di connessione.

Vedere l'esempio specifico per ciascun linguaggio:

* [C# 2.x](#2x-c-input-examples)
* [JavaScript 2.x](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

For more information on how this binding is used to create a "negotiate" function that can be consumed by a SignalR client SDK, see the [Azure Functions development and configuration article](../azure-signalr/signalr-concept-serverless-development-config.md) in the SignalR Service concepts documentation.

### <a name="2x-c-input-examples"></a>2.x C# input examples

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che acquisisce le informazioni di connessione a SignalR usando l'associazione di input e le restituisce tramite HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Token autenticati

Se la funzione viene attivata da un client autenticato, è possibile aggiungere un'attestazione ID utente al token generato. You can easily add authentication to a function app using [App Service Authentication](../app-service/overview-authentication-authorization.md).

Autenticazione servizio App imposta le intestazioni HTTP denominate `x-ms-client-principal-id` e `x-ms-client-principal-name` contenenti rispettivamente l'ID e il nome dell'entità client dell'utente autenticato. È possibile impostare la proprietà `UserId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-examples"></a>2.x JavaScript input examples

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
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Token autenticati

Se la funzione viene attivata da un client autenticato, è possibile aggiungere un'attestazione ID utente al token generato. You can easily add authentication to a function app using [App Service Authentication](../app-service/overview-authentication-authorization.md).

Autenticazione servizio App imposta le intestazioni HTTP denominate `x-ms-client-principal-id` e `x-ms-client-principal-name` contenenti rispettivamente l'ID e il nome dell'entità client dell'utente autenticato. È possibile impostare la proprietà `userId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`. 

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
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>2.x Java input examples

The following example shows a [Java function](functions-reference-java.md) that acquires SignalR connection information using the input binding and returns it over HTTP.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Token autenticati

Se la funzione viene attivata da un client autenticato, è possibile aggiungere un'attestazione ID utente al token generato. You can easily add authentication to a function app using [App Service Authentication](../app-service/overview-authentication-authorization.md).

Autenticazione servizio App imposta le intestazioni HTTP denominate `x-ms-client-principal-id` e `x-ms-client-principal-name` contenenti rispettivamente l'ID e il nome dell'entità client dell'utente autenticato. È possibile impostare la proprietà `UserId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

## <a name="signalr-output-binding"></a>Associazione di output SignalR

Usare l'associazione di output *SignalR* per inviare uno o più messaggi con il servizio Azure SignalR. È possibile trasmettere un messaggio a tutti i client connessi o solo ai client connessi che sono stati autenticati per un determinato utente.

You can also use it to manage the groups that a user belongs to.

Vedere l'esempio specifico per ciascun linguaggio:

* [C# 2.x](#2x-c-send-message-output-examples)
* [JavaScript 2.x](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.x C# send message output examples

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
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>Send to a group

You can send a message only to connections that have been added to a group by setting the `GroupName` property of the SignalR message.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>2.x C# group management output examples

SignalR Service allows users to be added to groups. Messages can then be sent to a group. You can use the `SignalRGroupAction` class with the `SignalR` output binding to manage a user's group membership.

#### <a name="add-user-to-a-group"></a>Add user to a group

The following example adds a user to a group.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Remove user from a group

The following example removes a user from a group.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> In order to get the `ClaimsPrincipal` correctly bound, you must have configured the authentication settings in Azure Functions.

### <a name="2x-javascript-send-message-output-examples"></a>2.x JavaScript send message output examples

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
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>Inviare a un utente

È possibile inviare un messaggio solo alle connessioni autenticate per un utente impostando la proprietà `userId` del messaggio SignalR.

*function.json* resta uguale. Ecco il codice JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>Send to a group

You can send a message only to connections that have been added to a group by setting the `groupName` property of the SignalR message.

*function.json* resta uguale. Ecco il codice JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>2.x JavaScript group management output examples

SignalR Service allows users to be added to groups. Messages can then be sent to a group. You can use the `SignalR` output binding to manage a user's group membership.

#### <a name="add-user-to-a-group"></a>Add user to a group

The following example adds a user to a group.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Remove user from a group

The following example removes a user from a group.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2.x Java send message output examples

#### <a name="broadcast-to-all-clients"></a>Trasmettere a tutti i client

The following example shows a [Java function](functions-reference-java.md) that sends a message using the output binding to all connected clients. `target` è il nome del metodo da richiamare su ogni client. La proprietà `arguments` è una matrice di zero o più oggetti da passare al metodo client.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>Inviare a un utente

È possibile inviare un messaggio solo alle connessioni autenticate per un utente impostando la proprietà `userId` del messaggio SignalR.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>Send to a group

You can send a message only to connections that have been added to a group by setting the `groupName` property of the SignalR message.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>2.x Java group management output examples

SignalR Service allows users to be added to groups. Messages can then be sent to a group. You can use the `SignalRGroupAction` class with the `SignalROutput` output binding to manage a user's group membership.

#### <a name="add-user-to-a-group"></a>Add user to a group

The following example adds a user to a group.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Remove user from a group

The following example removes a user from a group.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

## <a name="configuration"></a>Configurazione

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `SignalRConnectionInfo`.

|Proprietà di function.json | Proprietà dell'attributo |Description|
|---------|---------|----------------------|
|**type**|| Il valore deve essere impostato su `signalRConnectionInfo`.|
|**direction**|| Il valore deve essere impostato su `in`.|
|**nome**|| Nome della variabile usato nel codice della funzione per l'oggetto informazioni di connessione. |
|**hubName**|**HubName**| Questo valore deve essere impostato sul nome dell'hub SignalR per il quale vengono generate le informazioni di connessione.|
|**userId**|**UserId**| Facoltativo: valore dell'attestazione dell'identificatore utente da impostare nel token della chiave di accesso. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nome dell'impostazione app contenente la stringa di connessione al servizio SignalR (valore predefinito:"AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `SignalR`.

|Proprietà di function.json | Proprietà dell'attributo |Description|
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

> [!div class="nextstepaction"]
> [Sviluppo e configurazione di Funzioni di Azure e con il Servizio Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md)
