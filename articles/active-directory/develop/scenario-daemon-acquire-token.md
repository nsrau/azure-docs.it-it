---
title: Acquisire i token per chiamare un'API Web (app daemon)-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'app daemon che chiama API Web (acquisizione di token)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d755573b53eb63d85165fb73fe4b97298dbeff09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868989"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>App daemon che chiama le API Web-Acquisisci un token

Dopo aver creato un'applicazione client riservata, è possibile acquisire un token per l'app chiamando `AcquireTokenForClient`, passando l'ambito ed eventualmente forzando un aggiornamento del token.

## <a name="scopes-to-request"></a>Ambiti da richiedere

L'ambito da richiedere per un flusso di credenziali client è il nome della risorsa seguita da `/.default`. Questa notazione indica Azure Active Directory (Azure AD) di utilizzare le *autorizzazioni a livello di applicazione* dichiarate in modo statico durante la registrazione dell'applicazione. Inoltre, queste autorizzazioni API devono essere concesse da un amministratore tenant.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

In MSAL Python il file di configurazione ha un aspetto simile al frammento di codice seguente:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Risorse Azure AD (v 1.0)

L'ambito usato per le credenziali client deve essere sempre l'ID della risorsa `/.default`seguito da.

> [!IMPORTANT]
> Quando MSAL richiede un token di accesso per una risorsa che accetta un token di accesso versione 1,0, Azure AD analizza i destinatari desiderati dall'ambito richiesto, prendendo tutti gli elementi prima dell'ultima barra e usandola come identificatore di risorsa.
> Se, ad esempio, il database SQL di Azure (**https:\//database.Windows.NET**), la risorsa si aspetta un pubblico che termina con una barra (per il `https://database.windows.net/`database SQL di Azure), è necessario richiedere un `https://database.windows.net//.default`ambito. Prendere nota della barra doppia. Vedere anche problema MSAL.NET [#747: la barra finale dell'URL della risorsa viene omessa, causando un errore di autenticazione SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>API AcquireTokenForClient

Per acquisire un token per l'app, si userà `AcquireTokenForClient` o il relativo equivalente, a seconda della piattaforma.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

Questo codice viene estratto dagli [esempi di MSAL Java dev](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protocollo

Se non si dispone ancora di una libreria per la lingua scelta, è possibile usare direttamente il protocollo:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Primo caso: accedere alla richiesta di token usando un segreto condiviso

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Secondo caso: accedere alla richiesta di token usando un certificato

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
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

In MSAL.NET `AcquireTokenForClient` utilizza la cache del token dell'applicazione. Tutti gli altri metodi AcquireToken*XX* utilizzano la cache dei token utente. Non chiamare `AcquireTokenSilent` prima di chiamare `AcquireTokenForClient`, perché `AcquireTokenSilent` usa la cache del token *utente* . `AcquireTokenForClient`Controlla la cache del token *dell'applicazione* e la Aggiorna.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="did-you-use-the-resourcedefault-scope"></a>Si è usato l'ambito Resource/. default?

Se viene restituito un messaggio di errore che informa che è stato utilizzato un ambito non valido, probabilmente non `resource/.default` è stato utilizzato l'ambito.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Si è omesso di fornire il consenso dell'amministratore? Le app daemon ti servono!

Se si ottengono **privilegi insufficienti per completare l'errore dell'operazione** quando si chiama l'API, l'amministratore tenant deve concedere le autorizzazioni all'applicazione. Vedere il passaggio 6 di registrare l'app client sopra.
In genere viene visualizzato un errore simile a questo errore:

```json
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

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [App daemon: chiamata di un'API Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [App daemon: chiamata di un'API Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App daemon: chiamata di un'API Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
