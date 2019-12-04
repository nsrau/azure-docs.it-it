---
title: Associazioni del servizio SignalR per Funzioni di Azure
description: Informazioni su come usare le associazioni del servizio SignalR con Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: a1de59ebb5ef0d7f5522a388aa9a2f5818495a9f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786334"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Associazioni del servizio SignalR per Funzioni di Azure

Questo articolo illustra come autenticare e inviare messaggi in tempo reale ai client connessi al [servizio Azure SignalR](https://azure.microsoft.com/services/signalr-service/) usando le associazioni del servizio SignalR in Funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per il servizio SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

Le associazioni del servizio SignalR sono incluse nel pacchetto NuGet [Microsoft. Azure. webjobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) , versione 1. *. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

Per informazioni dettagliate su come configurare e usare insieme il servizio SignalR e funzioni di Azure, vedere [sviluppo e configurazione di funzioni di Azure con il servizio Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Libreria Annotations (solo Java)

Per usare le annotazioni del servizio SignalR nelle funzioni Java, è necessario aggiungere una dipendenza all'artefatto di *Azure-Functions-Java-Library-SignalR* (versione 1,0 o successiva) al file POM. XML.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="input"></a>Input

Prima che un client possa connettersi al servizio Azure SignalR, deve recuperare l'URL dell'endpoint di servizio e un token di accesso valido. L'associazione di input *SignalRConnectionInfo*genera l'URL dell'endpoint del servizio SignalR e un token valido per la connessione al servizio. Poiché il token ha una durata limitata e può essere usato per autenticare un utente specifico per una connessione, non deve essere memorizzato nella cache né condiviso tra client. I client possono usare un trigger HTTP con questa associazione per recuperare le informazioni di connessione.

Per altre informazioni su come questa associazione viene usata per creare una funzione "Negotiate" che può essere usata da un SDK client SignalR, vedere l' [articolo sviluppo e configurazione di funzioni di Azure](../azure-signalr/signalr-concept-serverless-development-config.md) nella documentazione relativa ai concetti del servizio SignalR.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Nell'esempio seguente viene illustrata un'associazione di input di informazioni sulla connessione SignalR in un file *Function. JSON* e una [ C# funzione script](functions-reference-csharp.md) che usa l'associazione per restituire le informazioni di connessione.

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

Ecco il C# codice dello script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

L'esempio seguente illustra un'associazione di input di informazioni sulla connessione SignalR in un file *Function. JSON* e una [funzione Python](functions-reference-python.md) che usa l'associazione per restituire le informazioni di connessione.

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

Ecco il codice Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

Nell'esempio seguente viene illustrata una [funzione Java](functions-reference-java.md) che acquisisce le informazioni di connessione SignalR usando l'associazione di input e le restituisce tramite http.

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

---

### <a name="authenticated-tokens"></a>Token autenticati

Se la funzione viene attivata da un client autenticato, è possibile aggiungere un'attestazione ID utente al token generato. È possibile aggiungere facilmente l'autenticazione a un'app per le funzioni usando [l'autenticazione del servizio app](../app-service/overview-authentication-authorization.md).

Autenticazione servizio App imposta le intestazioni HTTP denominate `x-ms-client-principal-id` e `x-ms-client-principal-name` contenenti rispettivamente l'ID e il nome dell'entità client dell'utente autenticato.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

È possibile impostare la proprietà `UserId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`.

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

È possibile impostare la proprietà `userId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`.

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

Ecco il C# codice dello script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

È possibile impostare la proprietà `userId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

È possibile impostare la proprietà `userId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`.

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

Ecco il codice Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

È possibile impostare la proprietà `userId` dell'associazione sul valore ottenuto da una delle intestazioni con un'[espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`.

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

---

## <a name="output"></a>Output

Usare l'associazione di output *SignalR* per inviare uno o più messaggi con il servizio Azure SignalR. È possibile trasmettere un messaggio a tutti i client connessi o solo ai client connessi che sono stati autenticati per un determinato utente.

È anche possibile usarlo per gestire i gruppi a cui appartiene un utente.

### <a name="broadcast-to-all-clients"></a>Trasmettere a tutti i client

Nell'esempio seguente viene illustrata una funzione che invia un messaggio utilizzando l'associazione di output a tutti i client connessi. La *destinazione* è il nome del metodo da richiamare in ogni client. *Arguments* è una matrice di zero o più oggetti da passare al metodo client.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

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

Ecco il C# codice dello script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Ecco il codice Python:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="send-to-a-user"></a>Inviare a un utente

È possibile inviare un messaggio solo alle connessioni autenticate a un utente impostando l' *ID utente* nel messaggio SignalR.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

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

Ecco il C# codice dello script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Ecco il codice Python:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="send-to-a-group"></a>Invia a un gruppo

È possibile inviare un messaggio solo alle connessioni aggiunte a un gruppo impostando il *nome del gruppo* nel messaggio SignalR.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

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

Ecco il C# codice dello script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Ecco il codice Python:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="group-management"></a>Gestione dei gruppi

Il servizio SignalR consente agli utenti di essere aggiunti ai gruppi. I messaggi possono quindi essere inviati a un gruppo. È possibile usare l'associazione di output `SignalR` per gestire l'appartenenza a un gruppo di un utente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="add-user-to-a-group"></a>Aggiungere un utente a un gruppo

Nell'esempio seguente viene aggiunto un utente a un gruppo.

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

#### <a name="remove-user-from-a-group"></a>Rimuovere un utente da un gruppo

Nell'esempio seguente viene rimosso un utente da un gruppo.

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
> Per ottenere il `ClaimsPrincipal` associato correttamente, è necessario aver configurato le impostazioni di autenticazione in funzioni di Azure.

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

#### <a name="add-user-to-a-group"></a>Aggiungere un utente a un gruppo

Nell'esempio seguente viene aggiunto un utente a un gruppo.

Esempio di *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Esegui. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
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

#### <a name="remove-user-from-a-group"></a>Rimuovere un utente da un gruppo

Nell'esempio seguente viene rimosso un utente da un gruppo.

Esempio di *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Esegui. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
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
> Per ottenere il `ClaimsPrincipal` associato correttamente, è necessario aver configurato le impostazioni di autenticazione in funzioni di Azure.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="add-user-to-a-group"></a>Aggiungere un utente a un gruppo

Nell'esempio seguente viene aggiunto un utente a un gruppo.

Esempio di *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
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

#### <a name="remove-user-from-a-group"></a>Rimuovere un utente da un gruppo

Nell'esempio seguente viene rimosso un utente da un gruppo.

Esempio di *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
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

# <a name="pythontabpython"></a>[Python](#tab/python)

#### <a name="add-user-to-a-group"></a>Aggiungere un utente a un gruppo

Nell'esempio seguente viene aggiunto un utente a un gruppo.

Esempio di *Function. JSON*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

#### <a name="remove-user-from-a-group"></a>Rimuovere un utente da un gruppo

Nell'esempio seguente viene rimosso un utente da un gruppo.

Esempio di *Function. JSON*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

#### <a name="add-user-to-a-group"></a>Aggiungere un utente a un gruppo

Nell'esempio seguente viene aggiunto un utente a un gruppo.

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

#### <a name="remove-user-from-a-group"></a>Rimuovere un utente da un gruppo

Nell'esempio seguente viene rimosso un utente da un gruppo.

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

---

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
