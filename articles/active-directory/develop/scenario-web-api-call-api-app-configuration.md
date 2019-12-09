---
title: Configurare un'API Web che chiama API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web che chiama le API Web (configurazione del codice dell'app)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 219724186e3fa69fec35e89435af495b662c871d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919750"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>API Web che chiama API Web-configurazione del codice

Dopo aver registrato l'API Web, è possibile configurare il codice per l'applicazione.

Il codice per configurare l'API Web in modo che chiami le API Web downstream si basa sul codice usato per proteggere un'API Web. Per altre informazioni, vedere [API Web protetta-configurazione dell'app](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Codice sottoscritto a OnTokenValidated

Oltre alla configurazione del codice per qualsiasi API Web protetta, è necessario sottoscrivere la convalida della bearer token ricevuta quando viene chiamata l'API:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
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

Il metodo AddAccountToCacheFromJwt () deve:

- Creare un'istanza di un'applicazione client riservata MSAL.
- Chiamare `AcquireTokenOnBehalf` per scambiare la bearer token acquisita dal client per l'API Web, rispetto a un bearer token per lo stesso utente, ma affinché l'API chiami un'API downstream.

### <a name="instantiate-a-confidential-client-application"></a>Creare un'istanza di un'applicazione client riservata

Questo flusso è disponibile solo nel flusso client riservato, in modo che l'API Web protetta fornisca le credenziali client (segreto client o certificato) al [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) tramite i metodi `WithClientSecret` o `WithCertificate`, rispettivamente.

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

Infine, anziché un segreto client o un certificato, le applicazioni client riservate possono anche dimostrare la propria identità usando le asserzioni client.
Questo scenario avanzato è dettagliato in [asserzioni client](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>Come chiamare per conto di

La chiamata a per conto di (OBO) viene eseguita chiamando il metodo [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) sull'interfaccia `IConfidentialClientApplication`.

Il `UserAssertion` viene creato dalla bearer token ricevuta dall'API Web dai propri client. Sono disponibili [due costruttori](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), uno che accetta un Bearer token JWT, e uno che accetta qualsiasi tipo di asserzione utente (un altro tipo di token di sicurezza, il quale viene quindi specificato in un parametro aggiuntivo denominato `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

In pratica, il flusso OBO viene spesso usato per acquisire un token per un'API downstream e archiviarlo nella cache dei token utente di MSAL.NET in modo che altre parti dell'API Web possano chiamare in un secondo momento le [sostituzioni](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) di ``AcquireTokenOnSilent`` per chiamare le API downstream. Questa chiamata ha l'effetto di aggiornare i token, se necessario.

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

È anche possibile vedere un esempio di per conto dell'implementazione di Flow in [NodeJS e funzioni di Azure](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol

Per ulteriori informazioni sul protocollo per conto di, vedere [Microsoft Identity Platform e OAuth 2,0 on-conto-of Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisizione di un token per l'app](scenario-web-api-call-api-acquire-token.md)
