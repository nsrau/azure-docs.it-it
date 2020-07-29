---
title: Binding di input del servizio SignalR di funzioni di Azure
description: Informazioni su come restituire un URL dell'endpoint del servizio SignalR e un token di accesso in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77530263"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Binding di input del servizio SignalR per funzioni di Azure

Prima che un client possa connettersi al servizio Azure SignalR, deve recuperare l'URL dell'endpoint di servizio e un token di accesso valido. L'associazione di input *SignalRConnectionInfo*genera l'URL dell'endpoint del servizio SignalR e un token valido per la connessione al servizio. Poiché il token ha una durata limitata e può essere usato per autenticare un utente specifico per una connessione, non deve essere memorizzato nella cache né condiviso tra client. I client possono usare un trigger HTTP con questa associazione per recuperare le informazioni di connessione.

Per altre informazioni su come questa associazione viene usata per creare una funzione "Negotiate" che può essere usata da un SDK client SignalR, vedere l' [articolo sviluppo e configurazione di funzioni di Azure](../azure-signalr/signalr-concept-serverless-development-config.md) nella documentazione relativa ai concetti del servizio SignalR.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-signalr-service.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Nell'esempio seguente viene illustrata un'associazione di input di informazioni sulla connessione di SignalR in una *function.jssu* file e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione per restituire le informazioni di connessione.

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

Ecco il codice script C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrata un'associazione di input di informazioni sulla connessione di SignalR in una *function.jssu* file e una [funzione Python](functions-reference-python.md) che usa l'associazione per restituire le informazioni di connessione.

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="authenticated-tokens"></a>Token autenticati

Se la funzione viene attivata da un client autenticato, è possibile aggiungere un'attestazione ID utente al token generato. È possibile aggiungere facilmente l'autenticazione a un'app per le funzioni usando [l'autenticazione del servizio app](../app-service/overview-authentication-authorization.md).

Autenticazione servizio App imposta le intestazioni HTTP denominate `x-ms-client-principal-id` e `x-ms-client-principal-name` contenenti rispettivamente l'ID e il nome dell'entità client dell'utente autenticato.

# <a name="c"></a>[C#](#tab/csharp)

È possibile impostare la `UserId` proprietà dell'associazione sul valore di una delle intestazioni usando un' [espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}` .

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

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

È possibile impostare la `userId` proprietà dell'associazione sul valore di una delle intestazioni usando un' [espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}` .

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

Ecco il codice script C#:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

È possibile impostare la `userId` proprietà dell'associazione sul valore di una delle intestazioni usando un' [espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}` .

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

# <a name="python"></a>[Python](#tab/python)

È possibile impostare la `userId` proprietà dell'associazione sul valore di una delle intestazioni usando un' [espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}` .

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

# <a name="java"></a>[Java](#tab/java)

È possibile impostare la `userId` proprietà dell'associazione sul valore di una delle intestazioni usando un' [espressione di associazione](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}` .

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

## <a name="next-steps"></a>Passaggi successivi

- [Inviare messaggi del servizio SignalR (associazione di output)](./functions-bindings-signalr-service-output.md) 
