---
title: API web - configurazione del codice app protetta | Azure
description: Informazioni su come compilare un'API web protetta e configurare il codice dell'applicazione.
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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536893"
---
# <a name="protected-web-api-code-configuration"></a>API web protetta: Configurazione del codice

Per configurare il codice per l'API web protetta, è necessario comprendere ciò che definisce le API come protetto, come configurare un token di connessione e come convalidare il token.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Definisce le API Core ASP.NET/ASP.NET come protetto?

Come App web e le API web ASP.NET/ASP.NET Core sono "protette" perché le azioni del controller sono precedute il `[Authorize]` attributo. Pertanto, le azioni del controller possono essere chiamate solo se viene chiamata l'API con un'identità che è autorizzata.

Prendere in considerazione le domande seguenti:

- In che modo l'API web riconosce l'identità dell'app che lo chiama? (Solo un'app può chiamare un'API web).
- Se l'app chiamato l'API web per conto di un utente, che cos'è l'identità dell'utente?

## <a name="bearer-token"></a>Bearer token

Le informazioni relative all'identità dell'app e sull'utente (a meno che l'app web accetta chiamate da servizio a da un'app daemon), viene mantenuto nel token di connessione che viene impostato nell'intestazione quando viene chiamato l'app.

Di seguito è riportato un C# esempio di codice che mostra un client chiama l'API dopo che acquisisce un token con Microsoft Authentication Library per .NET (MSAL.NET):

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
> Il token di connessione è stata richiesta da un'applicazione client all'endpoint di Microsoft identity platform *per l'API web*. L'API web è l'unica applicazione che deve verificare il token e visualizzare le attestazioni che contiene. Le app client non dovrebbero mai tentare di verificare le attestazioni nel token. (L'API web potrebbe richiedere, in futuro, che il token crittografati. Questo requisito potrebbe impedire l'accesso per le app client che possono visualizzare i token di accesso.)

## <a name="jwtbearer-configuration"></a>Configurazione JwtBearer

Questa sezione descrive come configurare un token di connessione.

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

Quando un'app viene chiamata su un'azione del controller che contiene un `[Authorize]` attributo, Core ASP.NET/ASP.NET esamina il token di connessione nell'intestazione dell'autorizzazione della richiesta di chiamata ed estrae il token di accesso. Il token viene quindi inoltrato al middleware JwtBearer, che chiama Microsoft IdentityModel Extensions per .NET.

In ASP.NET Core, il middleware viene inizializzato nel file Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Il middleware viene aggiunto all'API web da questa istruzione:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Attualmente, i modelli ASP.NET Core creano API che consentono l'accesso web di Azure Active Directory (Azure AD) degli utenti all'interno dell'organizzazione o da qualsiasi organizzazione, non con account personali. Ma è possibile modificare facilmente in modo che usino l'endpoint di Microsoft identity platform aggiungendo il codice nel file Startup.cs:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
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
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Convalida dei token

Il middleware JwtBearer, ad esempio il middleware OpenID Connect nelle App web, viene indirizzato da `TokenValidationParameters` per convalidare il token. Il token viene decrittografato (se necessario), le attestazioni vengono estratte e la firma viene verificata. Il middleware convalida quindi il token tramite il controllo dei dati:

- Si è destinato a web API (audience).
- È stato rilasciato per un'app che è consentito per chiamare l'API (sub) web.
- Sia stato emesso da un servizio token di sicurezza attendibile (STS) (autorità di certificazione).
- La sua durata è compreso nell'intervallo (scadenza).
- È non è stato manomesso (firma).

È anche possibile convalide particolari. Ad esempio, è possibile convalidare che le chiavi di firma (se incorporato in un token) attendibili e che il token non è in corso di riesecuzione. Infine, alcuni protocolli richiedono le convalide specifiche.

### <a name="validators"></a>Validator

I passaggi di convalida vengono acquisiti nei validator, che fanno tutti i [IdentityModel in estensioni Microsoft per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) libreria open source, in un file di origine: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Le convalide sono descritte in questa tabella:

| Validator | Descrizione |
|---------|---------|
| `ValidateAudience` | Verifica il token per l'applicazione che convalida il token (per me). |
| `ValidateIssuer` | Garantisce che il token emesso da un servizio token di sicurezza (da qualcuno che mi fido). |
| `ValidateIssuerSigningKey` | Garantisce l'applicazione convalida il token trust tra la chiave usata per firmare il token. (Caso speciale in cui la chiave viene incorporata nel token. In genere non obbligatorio). |
| `ValidateLifetime` | Garantisce che il token è valido ancora (o già). Il validator controlla se la durata del token (`notbefore` e `expires` attestazioni) è compreso nell'intervallo. |
| `ValidateSignature` | Garantisce che il token non sia stato manomesso. |
| `ValidateTokenReplay` | Garantisce che il token non viene riprodotta. (Caso speciale per alcuni protocolli onetime uso). |

I validator associati a proprietà del `TokenValidationParameters` classe stessi inizializzato dalla configurazione ASP.NET/ASP.NET Core. Nella maggior parte dei casi, non sarà necessario modificare i parametri. Vi è un'eccezione, per le app che non sono a tenant singolo. (Web, App che supportano gli utenti da qualsiasi organizzazione o da account Microsoft personali). In questo caso, l'autorità di certificazione devono essere convalidati.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Verificare gli ambiti e ruoli dell'app nel codice](scenario-protected-web-api-verification-scope-app-roles.md)
