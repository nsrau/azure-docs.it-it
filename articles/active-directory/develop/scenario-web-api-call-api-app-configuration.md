---
title: Configurare un'API Web che chiama API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web che chiama le API Web (configurazione del codice dell'app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38e319efb100d326d55f6f821e7c903306a7c7d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80991008"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>API Web che chiama API Web: configurazione del codice

Dopo aver registrato l'API Web, è possibile configurare il codice per l'applicazione.

Il codice usato per configurare l'API Web in modo che chiami le API Web downstream si basa sul codice usato per proteggere un'API Web. Per altre informazioni, vedere [API Web protetta: configurazione dell'app](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Codice sottoscritto a OnTokenValidated

Oltre alla configurazione del codice per qualsiasi API Web protetta, è necessario sottoscrivere la convalida della bearer token ricevuta quando viene chiamata l'API:

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

## <a name="on-behalf-of-flow"></a>Flusso On-Behalf-Of

Il metodo AddAccountToCacheFromJwt () deve:

- Creare un'istanza di un'applicazione client riservata Microsoft Authentication Library (MSAL).
- Chiamare il metodo `AcquireTokenOnBehalf` . Questa chiamata scambia la bearer token acquisita dal client per l'API Web rispetto a una bearer token per lo stesso utente, ma l'API chiama un'API downstream.

### <a name="instantiate-a-confidential-client-application"></a>Creare un'istanza di un'applicazione client riservata

Questo flusso è disponibile solo nel flusso client riservato, in modo che l'API Web protetta fornisca le credenziali client (segreto client o certificato) alla [classe ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) tramite il `WithClientSecret` metodo o `WithCertificate` .

![Elenco di metodi di IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

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

Infine, anziché dimostrare la propria identità tramite un segreto client o un certificato, le applicazioni client riservate possono dimostrare la propria identità usando le asserzioni client.
Per ulteriori informazioni su questo scenario avanzato, vedere [asserzioni client riservate](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Come chiamare per conto di

Per effettuare la chiamata a per conto di (OBO), chiamare il [Metodo AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) sull' `IConfidentialClientApplication` interfaccia.

La `UserAssertion` classe viene compilata dalla Bearer token ricevuta dall'API Web dai propri client. Sono disponibili [due costruttori](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Uno che accetta un token JSON Web (JWT) bearer token
* Uno che accetta qualsiasi tipo di asserzione utente, un altro tipo di token di sicurezza, il cui tipo viene quindi specificato in un parametro aggiuntivo denominato`assertionType`

![Proprietà e metodi di UserAssertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

In pratica, il flusso OBO viene spesso usato per acquisire un token per un'API downstream e archiviarlo nella cache dei token utente di MSAL.NET. Questa operazione viene eseguita in modo che altre parti dell'API Web possano chiamare in un secondo momento le [sostituzioni](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) di ``AcquireTokenOnSilent`` per chiamare le API downstream. Questa chiamata ha l'effetto di aggiornare i token, se necessario.

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

Il flusso per conto di (OBO) viene usato per ottenere un token per chiamare l'API Web downstream. In questo flusso, l'API Web riceve un bearer token con autorizzazioni delegate dall'utente dall'applicazione client e quindi scambia questo token per un altro token di accesso per chiamare l'API Web downstream.

Il codice seguente usa Spring Security Framework `SecurityContextHolder` nell'API Web per ottenere la Bearer token convalidata. USA quindi la libreria Java MSAL per ottenere un token per l'API downstream usando la `acquireToken` chiamata con `OnBehalfOfParameters` . MSAL memorizza nella cache il token in modo che le chiamate successive all'API possano usare `acquireTokenSilently` per ottenere il token memorizzato nella cache.

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

Il flusso per conto di (OBO) viene usato per ottenere un token per chiamare l'API Web downstream. In questo flusso, l'API Web riceve un bearer token con autorizzazioni delegate dall'utente dall'applicazione client e quindi scambia questo token per un altro token di accesso per chiamare l'API Web downstream.

Un'API Web Python dovrà usare un middleware per convalidare la bearer token ricevuta dal client. L'API Web può quindi ottenere il token di accesso per l'API downstream usando la libreria MSAL Python chiamando il [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metodo. Per un esempio dell'uso di questa API, vedere il [codice di test per Microsoft-Authentication-Library-for-Python su GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Vedere anche la discussione sul [problema 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) nello stesso repository per un approccio che ignora la necessità di un'applicazione di livello intermedio.

---

È anche possibile vedere un esempio di implementazione del flusso OBO in [Node.js e funzioni di Azure](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocollo

Per altre informazioni sul protocollo OBO, vedere [Microsoft Identity Platform e OAuth 2,0 on-conto-of Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API Web che chiama le API Web: acquisire un token per l'app](scenario-web-api-call-api-acquire-token.md)
