---
title: Configurare un'API Web che chiama le API Web Azure
titleSuffix: Microsoft identity platform
description: Scopri come creare un'API Web che chiama le API Web (configurazione del codice dell'app)
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
ms.openlocfilehash: 82b5e1d9753fbb65fd81f24b06016d302457144e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834094"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Un'API Web che chiama le API Web: Configurazione del codice

Dopo aver registrato l'API Web, è possibile configurare il codice per l'applicazione.

Il codice usato per configurare l'API Web in modo che chiami le API Web downstream si basa sul codice utilizzato per proteggere un'API Web. Per ulteriori informazioni, vedere [API Web protetta: configurazione dell'app](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Codice sottoscritto a OnTokenValidatedCode subscribed to OnTokenValidated

Oltre alla configurazione del codice per tutte le API Web protette, è necessario sottoscrivere la convalida del token di connessione che si riceve quando viene chiamata l'API:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
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
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Flusso per conto di

Il metodo AddAccountToCacheFromJwt() deve:

- Creare un'istanza di un'applicazione client riservata Microsoft Authentication Library (MSAL).
- Chiamare il metodo `AcquireTokenOnBehalf` . Questa chiamata scambia il token di connessione che è stato acquisito dal client per l'API Web con un token di connessione per lo stesso utente, ma ha l'API chiamare un'API downstream.

### <a name="instantiate-a-confidential-client-application"></a>Creare un'istanza di un'applicazione client riservata

Questo flusso è disponibile solo nel flusso client riservato, in modo che l'API Web protetta fornisca `WithCertificate` le credenziali client (segreto client o certificato) alla [classe ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) tramite il `WithClientSecret` metodo o .

![Elenco dei metodi IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
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

Infine, invece di dimostrare la propria identità tramite un segreto client o un certificato, le applicazioni client riservate possono dimostrare la propria identità utilizzando asserzioni client.
Per ulteriori informazioni su questo scenario avanzato, vedere [Asserzioni client riservate](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Come chiamare Per conto di

Per effettuare la chiamata per conto di (OBO) chiamando `IConfidentialClientApplication` il [AcquireTokenOnBehalf metodo](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) sull'interfaccia.

La `UserAssertion` classe viene compilata dal token di connessione ricevuto dall'API Web dai propri client. Esistono [due costruttori](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Uno che accetta un token di connessione JSON Web Token (JWT)
* Uno che accetta qualsiasi tipo di asserzione utente, un altro tipo di token di sicurezza, il cui tipo viene quindi specificato in un parametro aggiuntivo denominato`assertionType`

![Proprietà e metodi UserAssertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

In pratica, il flusso OBO viene spesso utilizzato per acquisire un token per un'API downstream e archiviarlo nella cache dei token utente MSAL.NET. Questa operazione consente ad altre parti dell'API Web di ``AcquireTokenOnSilent`` chiamare in un secondo momento gli [override](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) di per chiamare le API downstream. Questa chiamata ha l'effetto di aggiornare i token, se necessario.

```csharp
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

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
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
# <a name="java"></a>[Java](#tab/java)

Il flusso Per conto di (OBO) viene utilizzato per ottenere un token per chiamare l'API Web downstream. In questo flusso, l'API Web riceve un token di connessione con autorizzazioni delegate dall'utente dall'applicazione client e quindi scambia questo token per un altro token di accesso per chiamare l'API Web downstream.

Il codice seguente usa il `SecurityContextHolder` framework di sicurezza di Spring nell'API Web per ottenere il token di connessione convalidato. Viene quindi utilizzata la libreria Java MSAL per ottenere `acquireToken` un `OnBehalfOfParameters`token per l'API downstream utilizzando la chiamata con . MSAL memorizza nella cache il token in `acquireTokenSilently` modo che le chiamate successive all'API possano essere utilizzate per ottenere il token memorizzato nella cache.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Il flusso Per conto di (OBO) viene utilizzato per ottenere un token per chiamare l'API Web downstream. In questo flusso, l'API Web riceve un token di connessione con autorizzazioni delegate dall'utente dall'applicazione client e quindi scambia questo token per un altro token di accesso per chiamare l'API Web downstream.

Un'API Web Python dovrà usare un middleware per convalidare il token di connessione ricevuto dal client. L'API Web può quindi ottenere il token di accesso per [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) l'API downstream usando la libreria MSAL Python chiamando il metodo . Un esempio che dimostra questo flusso con MSAL Python non è ancora disponibile.

---

È inoltre possibile visualizzare un esempio di implementazione del flusso OBO in [Node.js e Funzioni](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)di Azure .

## <a name="protocol"></a>Protocollo

Per ulteriori informazioni sul protocollo OBO, vedere Piattaforma di [identità Microsoft e flusso di oAuth 2.0 per conto di](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Un'API Web che chiama le API Web: acquisire un token per l'appA web API that calls web APIs: Acquire a token for the app](scenario-web-api-call-api-acquire-token.md)
