---
title: "Chiamare il metodo app daemon web API (l'acquisizione dei token per l'app): piattaforma delle identità Microsoft"
description: Informazioni su come compilare un'applicazione daemon che chiama web API (l'acquisizione dei token)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075371"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>App daemon che chiama le API - web acquisire un token

Una volta costruito l'applicazione client riservata, è possibile acquisire un token per l'app tramite la chiamata ``AcquireTokenForClient``, passando l'ambito e forzando o non un aggiornamento del token.

## <a name="scopes-to-request"></a>Ambiti per richiedere

L'ambito per la richiesta di un flusso di credenziali client è il nome della risorsa seguito dal `/.default`. Questa notazione indica ad Azure AD da usare la **le autorizzazioni a livello di applicazione** dichiarato in modo statico durante la registrazione dell'applicazione. Come visto in precedenza, queste autorizzazioni API devono essere concesso anche da un amministratore del tenant

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

In MSAL. Python, file di configurazione sarà simile al frammento di codice seguente:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Tutti

L'ambito utilizzato per le credenziali del client deve essere sempre resourceId + "/. Default"

### <a name="case-of-v10-resources"></a>Caso di risorse v1.0

> [!IMPORTANT]
> Per MSAL (endpoint v2.0) che richiede un token di accesso per una risorsa di accettare un token di accesso di v1.0, Azure AD analizza i destinatari desiderati dall'ambito richiesto prendendo tutto ciò che precede l'ultima barra e usato come identificatore di risorsa.
> Pertanto se, ad esempio SQL di Azure (**https://database.windows.net**) la risorsa si aspetta che un gruppo di destinatari che terminano con una barra (per SQL di Azure: `https://database.windows.net/`), è necessario richiedere un ambito di `https://database.windows.net//.default` (si noti la barra doppia). Vedere anche MSAL.NET problema [747 #](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Barra finale dell'url della risorsa viene omesso, che ha causato un errore di autenticazione sql.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

### <a name="net"></a>.NET

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

#### <a name="application-token-cache"></a>Cache dei token dell'applicazione

In MSAL.NET, `AcquireTokenForClient` Usa il **cache dei token dell'applicazione** (tutti gli altri metodi AcquireTokenXX usano la cache dei token utente) non chiamano `AcquireTokenSilent` prima di chiamare `AcquireTokenForClient` come `AcquireTokenSilent` utilizza il **utente** cache dei token. `AcquireTokenForClient` Controlla il **applicazione** token nella cache se stesso e lo aggiorna.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

Se non hai ancora una libreria per il linguaggio scelto, è possibile usare direttamente il protocollo:

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

### <a name="learn-more-about-the-protocol"></a>Altre informazioni sul protocollo

Per altre informazioni, vedere la documentazione del protocollo: [Flusso di credenziali di Azure Active Directory v2.0 e il client di OAuth 2.0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="did-you-use-the-resourcedefault-scope"></a>È stata usata l'ambito di risorse /. default?

Se viene visualizzato un messaggio di errore che informa che è stato utilizzato un ambito non valido, probabilmente, non è stato usato il `resource/.default` ambito.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Si è omesso di fornire il consenso dell'amministratore? Le app daemon serve!

Se si verifica un errore quando si chiama l'API **dispone di privilegi sufficienti per completare l'operazione**, l'amministratore del tenant deve concedere le autorizzazioni per l'applicazione. Vedere il passaggio 6 di registrare l'app client precedente.
In genere si noterà ed errore, ad esempio la descrizione dell'errore seguente:

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

> [!div class="nextstepaction"]
> [App daemon: chiamare un'API web](scenario-daemon-call-api.md)