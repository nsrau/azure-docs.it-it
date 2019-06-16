---
title: API web - configurazione del codice app protetta | Azure
description: Informazioni su come compilare un'API Web protetta e configurare il codice dell'applicazione.
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
ms.openlocfilehash: bdd68d9ec5d0dd83df4628f39785ce255482245d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111159"
---
# <a name="protected-web-api---code-configuration"></a>API - configurazione del codice web protetta

Per configurare correttamente il codice per l'API web protetta, è necessario comprendere ciò che rende le API protette, è necessario configurare il token di connessione e come convalidare il token.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>Che cosa rende le API Core ASP.NET/ASP.NET protetto?

Ad esempio nell'App web, il ASP.NET/ASP.NET core web API sono "protetto" perché le azioni del controller sono precedute il `[Authorize]` attributo. Di conseguenza, le azioni del controller possono essere chiamate solo se viene chiamata l'API con un'identità che è autorizzata.

Prendere in considerazione le domande seguenti:

- In che modo l'API web riconosce l'identità dell'applicazione che lo chiama (solo un'applicazione può chiamare un'API web)?
- Se l'applicazione ha chiamato l'API web per conto di un utente, qual è l'identità dell'utente?

## <a name="bearer-token"></a>Bearer token

Le informazioni relative all'identità dell'applicazione e sull'utente (a meno che l'app web accetta chiamate da servizio a da un'applicazione daemon), viene mantenuto nel token di connessione che viene impostato nell'intestazione quando l'applicazione chiamante.

Di seguito è riportato un C# esempio di codice che mostra un client chiama l'API dopo aver acquisito un token con MSAL.NET.

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
> Il token di connessione è stata richiesta da un'applicazione client all'endpoint di Microsoft identity platform **per l'API web**. L'API web è l'unica applicazione che deve verificare il token ed esaminare le attestazioni che contiene. Le applicazioni client non è opportuno esaminare le attestazioni nei token (l'API web potrebbe decidere, in futuro, che richiede che il token crittografati e si interromperà l'applicazione client che è in grado di decifrare aprire i token di accesso).

## <a name="jwtbearer-configuration"></a>Configurazione JwtBearer

In questa sezione descrive cosa è necessario configurare il token di connessione.

### <a name="config-file"></a>File di configurazione

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

Quando l'applicazione viene chiamata nell'azienda di azione del controller un `[Authorize]` attributo, core ASP.NET/ASP.NET esamina il token di connessione nell'intestazione dell'autorizzazione della richiesta di chiamata ed estrae il token di accesso che viene quindi inoltrato al JwtBearer middleware, che chiama le estensioni del modello di identità Microsoft per .NET.

In ASP.NET Core, il middleware viene inizializzato nel `Startup.cs` file.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Il middleware viene aggiunto all'API web per l'istruzione seguente:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Attualmente, i modelli ASP.NET Core creano API web v1.0 di Azure AD. Tuttavia, è possibile modificare in modo che usino l'endpoint di Microsoft identity platform aggiungendo il codice seguente nel facilmente il `Startup.cs` file.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Convalida dei token

Il middleware JwtBearer, ad esempio il middleware OpenID Connect nelle App web, viene indirizzato per il `TokenValidationParameters` per convalidare il token. Il token viene decrittografato (se necessario), le attestazioni vengono estratte e la firma viene verificata. Quindi, il token viene convalidato tramite il controllo dei dati seguenti:

- È destinata a web API (destinatario)
- È stato rilasciato per un'applicazione che è possibile chiamare l'API (sub) web
- È stato rilasciato da un attendibile Server (token di sicurezza) (autorità di certificazione)
- Durata del token è compreso nell'intervallo (scadenza)
- È non è stato manomesso (firma)

È anche possibile convalide particolari. Ad esempio, è possibile convalidare che le chiavi di firma (se incorporato in un token) attendibili e che il token non è in corso di riesecuzione. Infine, alcuni protocolli richiedono le convalide specifiche.

### <a name="validators"></a>Validator

I passaggi di convalida vengono acquisiti in validator, che fanno tutti i [estensione di modello di identità di Microsoft per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) libreria open source, in un file di origine: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

Le convalide sono descritte nella tabella seguente:

| Validator | Descrizione |
|---------|---------|
| `ValidateAudience` | Assicura che il token sia per l'applicazione che convalida il token (per me). |
| `ValidateIssuer` | Assicura che il token emesso da un servizio token di sicurezza (da qualcuno che mi fido). |
| `ValidateIssuerSigningKey` | Garantisce l'applicazione convalida il token trust tra la chiave che è stata usata per firmare il token (caso speciale in cui la chiave viene incorporata nel token, in genere non necessari). |
| `ValidateLifetime` | Assicura che il token è valido ancora (o già). Operazione eseguita controllando che la durata del token (`notbefore`, `expires` attestazioni) è compreso nell'intervallo. |
| `ValidateSignature` | Assicura che il token non sia stato manomesso. |
| `ValidateTokenReplay` | Garantisce il token non viene riprodotta (caso speciale per alcuni protocolli onetime uso). |

I validator associati a proprietà del `TokenValidationParameters` classe stessi inizializzato dalla configurazione ASP.NET/ASP.NET Core. Nella maggior parte dei casi, non sarà necessario modificare i parametri. Vi è un'eccezione per le applicazioni che non sono a tenant singolo (l'App web che supportano gli utenti da qualsiasi organizzazione o un account Microsoft personali): in questo caso, l'autorità di certificazione deve essere convalidato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Verificare gli ambiti e ruoli dell'app nel codice](scenario-protected-web-api-verification-scope-app-roles.md)
