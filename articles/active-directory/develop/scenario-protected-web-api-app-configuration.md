---
title: Configurare app per le API Web protette | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web protetta e configurare il codice dell'applicazione.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 992c29cb8380cf6acbe970b2fd5e958b6b2b33dc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026714"
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

Di seguito è riportato un esempio di codice C# che mostra un client che chiama l'API dopo l'acquisizione di un token con Microsoft Authentication Library per .NET (MSAL.NET):

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

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Caso in cui è stato usato un URI ID app personalizzato per l'API Web

Se è stato accettato l'URI ID app proposto dal portale di registrazione delle app, non è necessario specificare il gruppo di destinatari (vedere [URI ID applicazione e ambiti](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)). In caso contrario, è necessario aggiungere una `Audience` proprietà il cui valore è l'URI ID app per l'API Web.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>Inizializzazione codice

Quando un'app viene chiamata su un'azione del controller che include un attributo **[autorizzate]** , ASP.NET e ASP.NET Core estrarre il token di accesso dall'Bearer token dell'intestazione di autorizzazione. Il token di accesso viene quindi inviato al middleware JwtBearer, che chiama Microsoft IdentityModel Extensions for .NET.

#### <a name="using-microsoftidentityweb-templates"></a>Utilizzo di Microsoft. Identity. Web Templates

È possibile creare un'API Web da zero usando i modelli di progetto Microsoft. Identity. Web. Per informazioni dettagliate, vedere [modello di progetto API Web Microsoft. Identity. Web](https://aka.ms/ms-id-web/webapi-project-templates)

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>A partire da un'applicazione ASP.NET Core 3,1 esistente

Attualmente, ASP.NET Core 3,1 USA la libreria Microsoft. AspNetCore. AzureAD. UI. Il middleware viene inizializzato nel file Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Il middleware viene aggiunto all'API Web da questa istruzione:

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 Attualmente, i modelli di ASP.NET Core creano API Web di Azure Active Directory (Azure AD) che confirmano gli utenti all'interno dell'organizzazione o di qualsiasi organizzazione. Non eseguono l'accesso agli utenti con account personali. Tuttavia, è possibile modificare i modelli per usare l'endpoint della piattaforma Microsoft Identity usando [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web), disponibile come pacchetto NuGet, sostituendo il codice in *Startup.cs*:

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

> [!NOTE]
> Se si usa Microsoft. Identity. Web e non si imposta `Audience` in *appsettings.json*, viene usato quanto segue:
> -  `$"{ClientId}"`Se è stata impostata la [versione accettata del token di accesso](scenario-protected-web-api-app-registration.md#accepted-token-version) su `2` o per Azure ad B2C API Web.
> - `$"api://{ClientId}`in tutti gli altri casi (per i [token di accesso](access-tokens.md)v 1.0).
> Per informazioni dettagliate, vedere [codice sorgente](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83)Microsoft. Identity. Web.

Il frammento di codice precedente viene estratto dall' [esercitazione incrementale ASP.NET Core API Web](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28). Il dettaglio di **AddMicrosoftWebApiAuthentication** è disponibile in [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/WebApiExtensions/WebApiServiceCollectionExtensions.cs#L27). Questo metodo chiama [AddMicrosoftWebAPI](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/WebApiExtensions/WebApiAuthenticationBuilderExtensions.cs#L58), che a sua volta indica al middleware la modalità di convalida del token. Per informazioni dettagliate, vedere il [codice sorgente](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/WebApiExtensions/WebApiAuthenticationBuilderExtensions.cs#L104-L122).

## <a name="token-validation"></a>Convalida dei token

Nel frammento di codice precedente, il middleware JwtBearer, come il middleware OpenID Connect nelle app Web, convalida il token in base al valore di `TokenValidationParameters` . Il token viene decrittografato in base alle esigenze, le attestazioni vengono estratte e la firma viene verificata. Il middleware convalida quindi il token controllando la presenza di questi dati:

- Destinatari: il token è destinato all'API Web.
- Sub: è stato emesso per un'app che può chiamare l'API Web.
- Emittente: è stato emesso da un servizio token di sicurezza (STS) attendibile.
- Scadenza: la sua durata è compresa nell'intervallo.
- Firma: non è stata manomessa.

Possono inoltre essere presenti convalide speciali. È ad esempio possibile convalidare che le chiavi di firma, quando sono incorporate in un token, siano considerate attendibili e che il token non venga riprodotto. Infine, alcuni protocolli richiedono convalide specifiche.

### <a name="validators"></a>Validator

I passaggi di convalida vengono acquisiti nei validator, forniti dalla libreria open source [Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) . I validator vengono definiti nel file di origine della libreria [Microsoft. IdentityModel. Tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Questa tabella descrive i validator:

| Validator | Descrizione |
|---------|---------|
| **ValidateAudience** | Garantisce che il token sia per l'applicazione che convalida il token per l'utente. |
| **ValidateIssuer** | Garantisce che il token sia stato emesso da un servizio token di sicurezza attendibile, vale a dire che è un utente attendibile. |
| **ValidateIssuerSigningKey** | Garantisce che l'applicazione che convalida il token consideri attendibile la chiave usata per firmare il token. C'è un caso speciale in cui la chiave è incorporata nel token. Questo caso, tuttavia, non si verifica in genere. |
| **ValidateLifetime** | Garantisce che il token sia ancora o già valido. Il validator controlla se la durata del token è compresa nell'intervallo specificato da **NotBefore** e **scade** le attestazioni. |
| **ValidateSignature** | Assicura che il token non sia stato alterato. |
| **ValidateTokenReplay** | Assicura che il token non venga riprodotto. C'è un caso speciale per alcuni protocolli monouso. |

#### <a name="customizing-token-validation"></a>Personalizzazione della convalida dei token

I validator sono associati alle proprietà della classe **TokenValidationParameters** . Le proprietà vengono inizializzate dalla configurazione ASP.NET e ASP.NET Core.

Nella maggior parte dei casi non è necessario modificare i parametri. Le app che non sono tenant singoli sono eccezioni. Queste app Web accettano utenti da qualsiasi organizzazione o da account Microsoft personali. In questo caso, è necessario convalidare le autorità emittenti. Microsoft. Identity. Web si occupa anche della convalida dell'autorità di certificazione. Per informazioni dettagliate, vedere Microsoft. Identity. Web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

In ASP.NET Core, se si desidera personalizzare i parametri di convalida del token, usare il frammento di codice seguente in *Startup.cs*:

```c#
services.AddMicrosoftWebApiAuthentication(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  }
});
```

Per ASP.NET MVC, l'esempio di codice seguente illustra come eseguire la convalida del token personalizzata:

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Convalida di token in funzioni di Azure

È anche possibile convalidare i token di accesso in ingresso in funzioni di Azure. È possibile trovare esempi di tale convalida negli esempi di codice seguenti su GitHub:

- .NET: [Azure-Samples/MS-Identity-DotNet-WebAPI-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-Samples/MS-Identity-NodeJS-WebAPI-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-Samples/MS-Identity-Python-WebAPI-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Verificare gli ambiti e i ruoli dell'app nel codice](scenario-protected-web-api-verification-scope-app-roles.md)
