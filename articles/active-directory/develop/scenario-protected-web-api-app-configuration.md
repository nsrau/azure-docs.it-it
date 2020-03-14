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
ms.openlocfilehash: 3f07105c14d4dafeb689eaaf7d679f93e5f235fe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262516"
---
# <a name="protected-web-api-code-configuration"></a>API Web protetta: configurazione del codice

Per configurare il codice per l'API Web protetta, è necessario comprendere quanto segue:

- Che cosa definisce le API come protette.
- Come configurare un bearer token.
- Come convalidare il token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Che cosa definisce le API ASP.NET e ASP.NET Core come protette?

Analogamente alle app Web, le API Web ASP.NET e ASP.NET Core sono protette perché le azioni del controller sono precedute dall'attributo **[autoautorizzate]** . Le azioni del controller possono essere chiamate solo se l'API viene chiamata con un'identità autorizzata.

Prendere in considerazione le domande seguenti:

- Solo un'app può chiamare un'API Web. In che modo l'API conosce l'identità dell'app che la chiama?
- Se l'app chiama l'API per conto di un utente, qual è l'identità dell'utente?

## <a name="bearer-token"></a>Bearer token

Il bearer token impostato nell'intestazione quando l'app viene chiamata include informazioni sull'identità dell'app. Include anche informazioni sull'utente, a meno che l'app Web non accetti le chiamate da servizio a servizio da un'app daemon.

Di seguito è C# riportato un esempio di codice che mostra un client che chiama l'API dopo l'acquisizione di un token con Microsoft Authentication Library per .net (MSAL.NET):

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
> Un'applicazione client richiede l'bearer token all'endpoint della piattaforma di identità Microsoft *per l'API Web*. L'API Web è l'unica applicazione che deve verificare il token e visualizzare le attestazioni in esso contenute. Le app client non devono mai provare a esaminare le attestazioni nei token.
>
> In futuro, l'API Web potrebbe richiedere la crittografia del token. Questo requisito impedisce l'accesso per le app client in grado di visualizzare i token di accesso.

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

Quando un'app viene chiamata su un'azione del controller che include un attributo **[autorizzate]** , ASP.NET e ASP.NET Core estrarre il token di accesso dall'Bearer token dell'intestazione di autorizzazione. Il token di accesso viene quindi inviato al middleware JwtBearer, che chiama Microsoft IdentityModel Extensions for .NET.

In ASP.NET Core, questo middleware viene inizializzato nel file Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Il middleware viene aggiunto all'API Web da questa istruzione:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Attualmente, i modelli di ASP.NET Core creano API Web di Azure Active Directory (Azure AD) che confirmano gli utenti all'interno dell'organizzazione o di qualsiasi organizzazione. Non eseguono l'accesso agli utenti con account personali. Tuttavia, è possibile modificare i modelli per usare l'endpoint della piattaforma di identità Microsoft aggiungendo questo codice a Startup.cs:

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

Il frammento di codice precedente viene estratto dall'esercitazione incrementale ASP.NET Core API Web in [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Il metodo **AddProtectedWebApi** , che viene visualizzato in modo più che il frammento di codice, viene chiamato da startup.cs.

## <a name="token-validation"></a>Convalida dei token

Nel frammento di codice precedente, il middleware JwtBearer, come il middleware OpenID Connect nelle app Web, convalida il token in base al valore di `TokenValidationParameters`. Il token viene decrittografato in base alle esigenze, le attestazioni vengono estratte e la firma viene verificata. Il middleware convalida quindi il token controllando la presenza di questi dati:

- Destinatari: il token è destinato all'API Web.
- Sub: è stato emesso per un'app che può chiamare l'API Web.
- Emittente: è stato emesso da un servizio token di sicurezza (STS) attendibile.
- Scadenza: la sua durata è compresa nell'intervallo.
- Firma: non è stata manomessa.

Possono inoltre essere presenti convalide speciali. È ad esempio possibile convalidare che le chiavi di firma, quando sono incorporate in un token, siano considerate attendibili e che il token non venga riprodotto. Infine, alcuni protocolli richiedono convalide specifiche.

### <a name="validators"></a>Validator

I passaggi di convalida vengono acquisiti nei validator, forniti dalla libreria open source [Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) . I validator vengono definiti nel file di origine della libreria [Microsoft. IdentityModel. Tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Questa tabella descrive i validator:

| Componente di convalida | Descrizione |
|---------|---------|
| **ValidateAudience** | Garantisce che il token sia per l'applicazione che convalida il token per l'utente. |
| **ValidateIssuer** | Garantisce che il token sia stato emesso da un servizio token di sicurezza attendibile, vale a dire che è un utente attendibile. |
| **ValidateIssuerSigningKey** | Garantisce che l'applicazione che convalida il token consideri attendibile la chiave usata per firmare il token. C'è un caso speciale in cui la chiave è incorporata nel token. Questo caso, tuttavia, non si verifica in genere. |
| **ValidateLifetime** | Garantisce che il token sia ancora o già valido. Il validator controlla se la durata del token è compresa nell'intervallo specificato da **NotBefore** e **scade** le attestazioni. |
| **ValidateSignature** | Assicura che il token non sia stato alterato. |
| **ValidateTokenReplay** | Assicura che il token non venga riprodotto. C'è un caso speciale per alcuni protocolli monouso. |

I validator sono associati alle proprietà della classe **TokenValidationParameters** . Le proprietà vengono inizializzate dalla configurazione ASP.NET e ASP.NET Core.

Nella maggior parte dei casi non è necessario modificare i parametri. Le app che non sono tenant singoli sono eccezioni. Queste app Web accettano utenti da qualsiasi organizzazione o da account Microsoft personali. In questo caso, è necessario convalidare le autorità emittenti.

## <a name="token-validation-in-azure-functions"></a>Convalida di token in funzioni di Azure

È anche possibile convalidare i token di accesso in ingresso in funzioni di Azure. È possibile trovare esempi di tale convalida in [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)e [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Verificare gli ambiti e i ruoli dell'app nel codice](scenario-protected-web-api-verification-scope-app-roles.md)
