---
title: Configurare le app per le API Web protette Azure
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
ms.openlocfilehash: 3f07105c14d4dafeb689eaaf7d679f93e5f235fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262516"
---
# <a name="protected-web-api-code-configuration"></a>API Web protetta: configurazione del codiceProtected web API: Code configuration

Per configurare il codice per l'API Web protetta, è necessario comprendere:To configure the code for your protected web API, you need to understand:

- Cosa definisce le API come protette.
- Come configurare un token di connessione.
- Come convalidare il token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Che cosa definisce le API ASP.NET e ASP.NET Core come protette?

Come le app Web, le API Web ASP.NET e ASP.NET Core sono protette perché le azioni del controller sono precedute dall'attributo **[Authorize].** Le azioni del controller possono essere chiamate solo se l'API viene chiamata con un'identità autorizzata.

Prendere in considerazione le domande seguenti:

- Solo un'app può chiamare un'API Web. In che modo l'API conosce l'identità dell'app che la chiama?
- Se l'app chiama l'API per conto di un utente, qual è l'identità dell'utente?

## <a name="bearer-token"></a>Bearer token

Il token di connessione impostato nell'intestazione quando viene chiamata l'app contiene informazioni sull'identità dell'app. Contiene inoltre informazioni sull'utente, a meno che l'app Web non accetti chiamate da servizio a servizio da un'app daemon.

Di seguito è riportato un esempio di codice in C' che mostra un client che chiama l'API dopo l'acquisizione di un token con microsoft Authentication Library per .NET (MSAL.NET):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Un'applicazione client richiede il token di connessione all'endpoint della piattaforma di identità Microsoft *per l'API Web.* L'API Web è l'unica applicazione che deve verificare il token e visualizzare le attestazioni in esso contenute. Le app client non devono mai tentare di controllare le attestazioni nei token.
>
> In futuro, l'API Web potrebbe richiedere la crittografia del token. Questo requisito impedirebbe l'accesso per le app client in grado di visualizzare i token di accesso.

## <a name="jwtbearer-configuration"></a>Configurazione JwtBearer

In questa sezione viene descritto come configurare un token di connessione.

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

### <a name="code-initialization"></a>Inizializzazione del codice

Quando un'app viene chiamata su un'azione del controller che contiene un attributo **[Authorize],** ASP.NET e ASP.NET Core estraggono il token di accesso dal token di connessione dell'intestazione Authorization. Il token di accesso viene quindi inoltrato al middleware JwtBearer, che chiama Microsoft IdentityModel Extensions per .NET.

In ASP.NET Core, questo middleware viene inizializzato nel file Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Il middleware viene aggiunto all'API Web da questa istruzione:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Attualmente, i modelli ASP.NET Core creano API Web di Azure Active Directory (Azure AD) che accedono agli utenti all'interno dell'organizzazione o di qualsiasi organizzazione. Non accedono agli utenti con account personali. Tuttavia, è possibile modificare i modelli per usare l'endpoint della piattaforma di identità Microsoft aggiungendo questo codice a Startup.cs:

```csharp
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

Il frammento di codice precedente viene estratto dall'esercitazione incrementale dell'API Web ASP.NET Core in [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs.L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Il **Metodo AddProtectedWebApi,** che esegue più di quanto illustrato nel frammento di codice, viene chiamato da Startup.cs.

## <a name="token-validation"></a>Convalida dei token

Nel frammento precedente, il middleware di JwtBearer, come il middleware OpenID Connect nelle `TokenValidationParameters`app Web, convalida il token in base al valore di . Il token viene decrittografato in base alle esigenze, le attestazioni vengono estratte e la firma viene verificata. Il middleware convalida quindi il token controllando questi dati:

- Pubblico: il token è destinato all'API Web.Audience: The token is targeted for the web API.
- Sub: It was issued for an app that's allowed to call the web API.
- Autorità di certificazione: è stata rilasciata da un servizio token di sicurezza attendibile.
- Scadenza: la sua durata è compresa nell'intervallo.
- Firma: Non è stato manomesso.

Ci possono essere anche convalide speciali. Ad esempio, è possibile convalidare che le chiavi di firma, quando incorporate in un token, sono attendibili e che il token non viene riprodotto. Infine, alcuni protocolli richiedono convalide specifiche.

### <a name="validators"></a>Validator

I passaggi di convalida vengono acquisiti nei validator, forniti dalle [estensioni Microsoft IdentityModel per](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) la libreria open source .NET. I validator sono definiti nel file di origine della libreria [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Questa tabella descrive i validatori:

| Componente di convalida | Descrizione |
|---------|---------|
| **Convalidagruppo** | Garantisce che il token sia per l'applicazione che convalida il token automaticamente. |
| **ValidateIssuer** | Garantisce che il token sia stato emesso da un servizio token di sicurezza attendibile, ovvero proviene da qualcuno di cui ti fidi. |
| **ValidateIssuerSigningKey** | Garantisce che l'applicazione che convalida il token consideri attendibile la chiave utilizzata per firmare il token. C'è un caso speciale in cui la chiave è incorporata nel token. Ma questo caso di solito non sorgo. |
| **ValidateLifetime** | Garantisce che il token sia ancora o già valido. Il validator controlla se la durata del token è compresa nell'intervallo specificato dalle attestazioni **notbefore** e **scade.** |
| **ConvalidaFirma** | Garantisce che il token non sia stato manomesso. |
| **ValidateTokenReplay** | Garantisce che il token non venga riprodotto. C'è un caso speciale per alcuni protocolli di una volta-uso. |

I validator sono associati alle proprietà della classe **TokenValidationParameters.** Le proprietà vengono inizializzate dalla configurazione ASP.NET e ASP.NET Core.

Nella maggior parte dei casi, non è necessario modificare i parametri. Le app che non sono tenant singoli sono eccezioni. Queste app Web accettano utenti da qualsiasi organizzazione o da account Microsoft personali. Gli emittenti in questo caso devono essere convalidati.

## <a name="token-validation-in-azure-functions"></a>Token validation in Azure Functions

È anche possibile convalidare i token di accesso in ingresso in Funzioni di Azure.You can also validate incoming access tokens in Azure Functions. È possibile trovare esempi di tale convalida in [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)e [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Verificare gli ambiti e i ruoli dell'app nel codiceVerify scopes and app roles in your code](scenario-protected-web-api-verification-scope-app-roles.md)
