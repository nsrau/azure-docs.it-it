---
title: Acquisire token per chiamare un'API Web (app daemon) - Piattaforma di identità Microsoft . Azure
description: Informazioni su come creare un'app daemon che chiama le API Web (acquisizione di token)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7f1010949a72f95ef2836c43666e6cea9281e04d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262646"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>App Daemon che chiama le API Web- acquisisci un token

Dopo aver costruito un'applicazione client riservata, è possibile `AcquireTokenForClient`acquisire un token per l'app chiamando , passando l'ambito e facoltativamente forzando un aggiornamento del token.

## <a name="scopes-to-request"></a>Ambiti da richiedere

L'ambito da richiedere per un flusso di credenziali `/.default`client è il nome della risorsa seguito da . Questa notazione indica ad Azure Active Directory (Azure AD) di usare *le autorizzazioni* a livello di applicazione dichiarate staticamente durante la registrazione dell'applicazione. Inoltre, queste autorizzazioni API devono essere concesse da un amministratore tenant.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

In MSAL Python, il file di configurazione è simile a questo frammento di codice:In MSAL Python, the configuration file looks like this code snippet:

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

### <a name="azure-ad-v10-resources"></a>Risorse di Azure AD (v1.0)Azure AD (v1.0) resources

L'ambito utilizzato per le credenziali client deve `/.default`essere sempre l'ID risorsa seguito da .

> [!IMPORTANT]
> Quando MSAL richiede un token di accesso per una risorsa che accetta un token di accesso versione 1.0, Azure AD analizza il pubblico desiderato dall'ambito richiesto prendendo tutto prima dell'ultima barra e usandolo come identificatore di risorsa.
> Pertanto, se, come il database SQL di Azure (**https:\//database.windows.net**), la `https://database.windows.net/`risorsa prevede un gruppo di `https://database.windows.net//.default`destinatari che termina con una barra (per il database SQL di Azure, ), sarà necessario richiedere un ambito di . (Notare la doppia barra.) Vedere anche MSAL.NET problema [#747: barra finale dell'URL della risorsa viene omessa, che ha causato](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)un errore di autenticazione SQL .

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Per acquisire un token per l'app, userai `AcquireTokenForClient` o il suo equivalente, a seconda della piattaforma.

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

Questo codice viene estratto dagli esempi di [sviluppo Java MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

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

Se non si dispone ancora di una libreria per la lingua scelta, è possibile utilizzare direttamente il protocollo:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Primo caso: accedere alla richiesta di token utilizzando un segreto condiviso

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Secondo caso: accedere alla richiesta di token usando un certificatoSecond case: Access the token request by using a certificate

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Per ulteriori informazioni, vedere la documentazione del protocollo: [Microsoft identity platform and the OAuth 2.0 client credentials flow](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Cache dei token dell'applicazione

In MSAL.NET, `AcquireTokenForClient` utilizza la cache dei token dell'applicazione. Tutti gli altri metodi AcquireToken*XX* utilizzano la cache dei token utente. Non `AcquireTokenSilent` chiamare prima di `AcquireTokenForClient`chiamare `AcquireTokenSilent` , perché utilizza la cache dei token *utente.* `AcquireTokenForClient`controlla la cache dei token *dell'applicazione* e la aggiorna.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="did-you-use-the-resourcedefault-scope"></a>È stato utilizzato l'ambito resource/.default?

Se viene visualizzato un messaggio di errore che indica che è `resource/.default` stato utilizzato un ambito non valido, probabilmente non è stato utilizzato l'ambito.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Ti sei dimenticato di fornire il consenso dell'amministratore? Le app Daemon ne hanno bisogno!

Se si ottiene un **privilegio insufficiente per completare l'errore di operazione** quando si chiama l'API, l'amministratore tenant deve concedere le autorizzazioni per l'applicazione. Vedere il passaggio 6 di Registrare l'app client precedente.
In genere viene visualizzato un errore simile a questo errore:You'll typically see an error that looks like this error:

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

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [App Daemon - chiamata di un'API Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [App Daemon - chiamata di un'API Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App Daemon - chiamata di un'API Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
