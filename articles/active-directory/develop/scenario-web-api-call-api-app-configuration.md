---
title: Web API che le chiamate a valle web API (configurazione del codice dell'app) - piattaforma delle identità Microsoft
description: Informazioni su come creare una web API che chiama web API (configurazione del codice dell'app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
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
ms.openlocfilehash: 3dedef2d22df9c8c81410296bdb0c4814bd98b80
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507122"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Web API che chiama l'API - configurazione del codice web

Dopo aver registrato l'API web, è possibile configurare il codice per l'applicazione.

Il codice per configurare l'API web in modo che chiami l'API web downstream si basa il codice usato per proiettare un'API web. Per altre informazioni, vedi [API web - configurazione delle app protetta](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Codice sottoscritto OnTokenValidated

Nella parte superiore della configurazione di codice per tutte le API web protetta, è necessario effettuare la sottoscrizione per la convalida del token di connessione che viene ricevuta quando viene chiamata l'API:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Flusso on-behalf-of

Il metodo AddAccountToCacheFromJwt() deve:

- Creare un'istanza di un'applicazione client riservata MSAL.
- Chiamare `AcquireTokenOnBehalf` per scambiare il token di connessione che è stato acquisito dal client per l'API web, con un token di connessione per lo stesso utente, ma per la nostra API per chiamare un'API downstream.

### <a name="instantiate-a-confidential-client-application"></a>Creare un'istanza di un'applicazione client riservata

Questo flusso è disponibile solo nel flusso di client riservato in modo che l'API web protetta fornisce le credenziali del client (certificato o segreto client) per il [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.confidentialclientapplicationbuilder?view=azure-dotnet-preview) tramite il `WithClientSecret` o`WithCertificate`metodi di, rispettivamente.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

### <a name="how-to-call-on-behalf-of"></a>Come chiamare on-behalf-of

La chiamata di on-behalf-of (OBO) viene eseguita chiamando il [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenonbehalfofparameterbuilder?view=azure-dotnet-preview) metodo su di `IConfidentialClientApplication` interfaccia.

Il `ClientAssertion` viene compilata dal token di connessione ricevuto dall'API web dai propri client. Esistono [due costruttori](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), uno che accetta token un bearer token JWT e uno che accetta qualsiasi tipo di asserzione utente (un altro tipo di token di sicurezza, quale tipo viene specificato in un parametro aggiuntivo denominato `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

In pratica, il flusso OBO viene spesso usato per acquisire un token per un'API downstream e memorizzarli nella cache dei token utente MSAL.NET in modo da altre parti dell'API web in un secondo momento è possono chiamare sul [esegue l'override](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) di ``AcquireTokenOnSilent`` per chiamare le API downstream. Questo ha l'effetto di aggiornamento dei token, se necessario.

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
 try
 {
  UserAssertion userAssertion;
  IEnumerable<string> requestedScopes;
  if (jwtToken != null)
  {
   userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
   requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
  }
  else
  {
   throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
  }

  // Create the application
  var application = BuildConfidentialClientApplication(httpContext, principal);

  // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
  var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                  userAssertion)
                                        .ExecuteAsync()
                                        .GetAwaiter().GetResult();
 }
 catch (MsalException ex)
 {
  Debug.WriteLine(ex.Message);
  throw;
 }
}
```

## <a name="protocol"></a>Protocol

Per altre informazioni sul protocollo on-behalf-of, vedere [piattaforma delle identità Microsoft e il flusso di OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisire un token per l'app](scenario-web-api-call-api-acquire-token.md)
