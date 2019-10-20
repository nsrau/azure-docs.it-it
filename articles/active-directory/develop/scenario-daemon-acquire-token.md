---
title: App daemon che chiama API Web (acquisizione di token per l'app)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app daemon che chiama API Web (acquisizione di token)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 605614265d033647bfcf22bb99d45c89f275298b
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596389"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>App daemon che chiama le API Web-Acquisisci un token

Una volta creata l'applicazione client riservata, è possibile acquisire un token per l'app chiamando ``AcquireTokenForClient``, passando l'ambito e forzando o meno un aggiornamento del token.

## <a name="scopes-to-request"></a>Ambiti da richiedere

L'ambito da richiedere per un flusso di credenziali client è il nome della risorsa seguito da `/.default`. Questa notazione indica Azure AD di utilizzare le **autorizzazioni a livello di applicazione** dichiarate in modo statico durante la registrazione dell'applicazione. Inoltre, come illustrato in precedenza, queste autorizzazioni API devono essere concesse da un amministratore tenant

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

In MSAL. Python, il file di configurazione sarà simile al frammento di codice seguente:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Caso di risorse Azure AD (v 1.0)

L'ambito usato per le credenziali client deve essere sempre resourceId + "/.default"

> [!IMPORTANT]
> Per MSAL che richiede un token di accesso per una risorsa che accetta un token di accesso v 1.0, Azure AD analizza i destinatari desiderati dall'ambito richiesto, prendendo tutti gli elementi prima dell'ultima barra e usandola come identificatore di risorsa.
> Se, ad esempio, come Azure SQL ( **https://database.windows.net** ) la risorsa prevede un pubblico che termina con una barra (per Azure SQL: `https://database.windows.net/` ), è necessario richiedere un ambito di `https://database.windows.net//.default` (si noti la barra doppia). Vedere anche problema MSAL.NET [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): la barra finale dell'URL della risorsa viene omessa, causando un errore di autenticazione SQL.

## <a name="acquiretokenforclient-api"></a>API AcquireTokenForClient

Per acquisire un token per l'app, si userà `AcquireTokenForClient` o l'equivalente a seconda delle piattaforme.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
    print(result["expires_in"])  # You don't normally need to care about this.
                                 # It will be good for at least 5 minutes.
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protocol

Se non si dispone ancora di una libreria per la lingua scelta, è possibile usare direttamente il protocollo:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primo caso: richiesta del token di accesso con un segreto condiviso

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Secondo caso: richiesta del token di accesso con un certificato

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Per altre informazioni, vedere la documentazione del protocollo: [Microsoft Identity Platform e il flusso di credenziali client OAuth 2,0](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Cache del token dell'applicazione

In MSAL.NET `AcquireTokenForClient` utilizza la **cache del token dell'applicazione** (tutti gli altri metodi AcquireTokenXX utilizzano la cache dei token utente) non chiamare `AcquireTokenSilent` prima di chiamare `AcquireTokenForClient` come `AcquireTokenSilent` utilizza la cache dei token **utente** . `AcquireTokenForClient` controlla la cache del token **dell'applicazione** e la Aggiorna.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="did-you-use-the-resourcedefault-scope"></a>Si è usato l'ambito Resource/. default?

Se viene restituito un messaggio di errore che informa che è stato utilizzato un ambito non valido, è probabile che non sia stato utilizzato l'ambito `resource/.default`.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Si è omesso di fornire il consenso dell'amministratore? Le app daemon ti servono!

Se viene ricevuto un errore quando si chiama l'API **privilegi insufficienti per completare l'operazione**, l'amministratore tenant deve concedere le autorizzazioni all'applicazione. Vedere il passaggio 6 di registrare l'app client sopra.
In genere viene visualizzato un errore simile al seguente:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [App daemon: chiamata di un'API Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [App daemon: chiamata di un'API Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App daemon: chiamata di un'API Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
