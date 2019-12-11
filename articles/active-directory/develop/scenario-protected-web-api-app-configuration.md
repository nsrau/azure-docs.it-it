---
title: Configurare app per le API Web protette | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web protetta e configurare il codice dell'applicazione.
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
ms.openlocfilehash: 7f78fa35096b7e17d3736190bfa49619c2c81520
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965399"
---
# <a name="protected-web-api-code-configuration"></a>API Web protetta: configurazione del codice

Per configurare il codice per l'API Web protetta, è necessario comprendere cosa definisce le API come protette, come configurare un bearer token e come convalidare il token.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Che cosa definisce le API ASP.NET/ASP.NET Core come protette?

Analogamente alle app Web, le API Web di ASP.NET/ASP.NET Core sono "protette" perché le azioni del controller sono precedute dall'attributo `[Authorize]`. Quindi, le azioni del controller possono essere chiamate solo se l'API viene chiamata con un'identità autorizzata.

Prendere in considerazione le domande seguenti:

- In che modo l'API Web conosce l'identità dell'app che la chiama? (Solo un'app può chiamare un'API Web).
- Se l'app ha chiamato l'API Web per conto di un utente, qual è l'identità dell'utente?

## <a name="bearer-token"></a>Bearer token

Le informazioni sull'identità dell'app e sull'utente (a meno che l'app Web non accetti le chiamate da servizio a servizio da un'app daemon) viene mantenuta nel bearer token impostato nell'intestazione quando viene chiamata l'app.

Di seguito è C# riportato un esempio di codice che mostra un client che chiama l'API dopo l'acquisizione di un token con Microsoft Authentication Library per .net (MSAL.NET):

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Il bearer token è stato richiesto da un'applicazione client all'endpoint della piattaforma di identità Microsoft *per l'API Web*. L'API Web è l'unica applicazione che deve verificare il token e visualizzare le attestazioni in esso contenute. Le app client non devono mai provare a esaminare le attestazioni nei token. L'API Web potrebbe richiedere, in futuro, che il token venga crittografato. Questo requisito impedisce l'accesso per le app client in grado di visualizzare i token di accesso.

## <a name="jwtbearer-configuration"></a>Configurazione di JwtBearer

In questa sezione viene descritto come configurare un bearer token.

### <a name="config-file"></a>File di configurazione

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Inizializzazione codice

Quando un'app viene chiamata su un'azione del controller che include un attributo `[Authorize]`, ASP.NET/ASP.NET Core esamina l'bearer token nell'intestazione dell'autorizzazione della richiesta chiamante ed estrae il token di accesso. Il token viene quindi inviato al middleware JwtBearer, che chiama Microsoft IdentityModel Extensions for .NET.

In ASP.NET Core, questo middleware viene inizializzato nel file Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Il middleware viene aggiunto all'API Web da questa istruzione:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Attualmente, i modelli di ASP.NET Core creano API Web di Azure Active Directory (Azure AD) che confirmano gli utenti all'interno dell'organizzazione o di qualsiasi organizzazione, non con gli account personali. Tuttavia è possibile modificarli facilmente per usare l'endpoint della piattaforma di identità Microsoft aggiungendo questo codice al file Startup.cs:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Questo frammento di codice viene estratto dall'esercitazione incrementale ASP.NET Core API Web in [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Il metodo `AddProtectedWebApi`, che fa molto altro, viene chiamato da Startup.cs

## <a name="token-validation"></a>Convalida dei token

Il middleware JwtBearer, come il middleware OpenID Connect nelle app Web, viene indirizzato da `TokenValidationParameters` per convalidare il token. Il token viene decrittografato (se necessario), le attestazioni vengono estratte e la firma viene verificata. Il middleware convalida quindi il token controllando la presenza di questi dati:

- È destinata all'API Web (audience).
- È stato emesso per un'app che può chiamare l'API Web (Sub).
- È stato emesso da un servizio token di sicurezza attendibile (STS) (emittente).
- La sua durata è compresa nell'intervallo (scadenza).
- Non è stata manomessa (firma).

Possono inoltre essere presenti convalide speciali. È ad esempio possibile convalidare che le chiavi di firma (quando sono incorporate in un token) sono attendibili e che il token non viene riprodotto. Infine, alcuni protocolli richiedono convalide specifiche.

### <a name="validators"></a>Validator

I passaggi di convalida vengono acquisiti nei validator, che si trovano tutti nella libreria open source [di Microsoft IdentityModel per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) , in un file di origine: [Microsoft. IdentityModel. Tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

I validator sono descritti in questa tabella:

| Componente di convalida | Description |
|---------|---------|
| `ValidateAudience` | Garantisce che il token sia per l'applicazione che convalida il token (per me). |
| `ValidateIssuer` | Assicura che il token sia stato emesso da un servizio token di sicurezza attendibile (da un utente attendibile). |
| `ValidateIssuerSigningKey` | Garantisce che l'applicazione che convalida il token consideri attendibile la chiave usata per firmare il token. (Caso speciale in cui la chiave è incorporata nel token. In genere non è obbligatorio.) |
| `ValidateLifetime` | Garantisce che il token sia ancora (o già) valido. Il validator controlla se la durata del token (`notbefore` e `expires` attestazioni) è compresa nell'intervallo. |
| `ValidateSignature` | Assicura che il token non sia stato alterato. |
| `ValidateTokenReplay` | Assicura che il token non venga riprodotto. (Caso speciale per alcuni protocolli monouso). |

Tutti i validator sono associati alle proprietà della classe `TokenValidationParameters`, che vengono inizializzate dalla configurazione ASP.NET/ASP.NET Core. Nella maggior parte dei casi, non è necessario modificare i parametri. Esiste un'eccezione per le app che non sono singoli tenant. Ovvero app Web che accettano utenti da qualsiasi organizzazione o da account Microsoft personali. In questo caso, l'emittente deve essere convalidato.

## <a name="token-validation-in-azure-functions"></a>Convalida di token in funzioni di Azure

È anche possibile convalidare i token di accesso in ingresso in funzioni di Azure. È possibile trovare esempi di convalida dei token in funzioni di Azure in [DotNet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)e [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Verificare gli ambiti e i ruoli dell'app nel codice](scenario-protected-web-api-verification-scope-app-roles.md)
