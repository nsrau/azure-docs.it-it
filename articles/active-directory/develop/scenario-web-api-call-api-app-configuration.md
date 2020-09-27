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
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88f0891e9bd67da094240b059750226402da0244
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396232"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>API Web che chiama API Web: configurazione del codice

Dopo aver registrato l'API Web, è possibile configurare il codice per l'applicazione.

Il codice usato per configurare l'API Web in modo che chiami le API Web downstream si basa sul codice usato per proteggere un'API Web. Per altre informazioni, vedere [API Web protetta: configurazione dell'app](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

Microsoft consiglia di usare il pacchetto NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) per lo sviluppo di un'API ASP.NET Core protetta che chiama API Web downstream. Vedere [API Web protetta: configurazione del codice | Microsoft. Identity. Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) per una rapida presentazione di tale libreria nel contesto di un'API Web.

## <a name="client-secrets-or-client-certificates"></a>Segreti client o certificati client

Dato che l'API Web ora chiama un'API Web downstream, è necessario fornire un segreto client o un certificato client nell' *appsettings.jssu* file. È anche possibile aggiungere una sezione che specifichi:

- URL dell'API Web downstream
- Ambiti necessari per chiamare l'API

Nell'esempio seguente, la `GraphBeta` sezione specifica queste impostazioni.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Anziché un segreto client, è possibile fornire un certificato client. Il frammento di codice seguente illustra l'uso di un certificato archiviato in Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft. Identity. Web offre diversi modi per descrivere i certificati, sia per configurazione sia per codice. Per informazioni dettagliate, vedere [Microsoft. Identity. Web wiki-uso di certificati](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) su GitHub.

## <a name="startupcs"></a>Startup.cs

L'API Web deve acquisire un token per l'API downstream. Per specificarlo, aggiungere la `.EnableTokenAcquisitionToCallDownstreamApi()` riga dopo `.AddMicrosoftIdentityWebApi(Configuration)` . Questa riga espone il `ITokenAcquisition` servizio, che è possibile usare nelle azioni del controller o delle pagine. Tuttavia, come si vedrà nei prossimi due punti elenco, è possibile eseguire ancora più facilmente. È anche necessario scegliere un'implementazione della cache dei token, ad esempio `.AddInMemoryTokenCaches()` in *Startup.cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Se non si vuole acquisire il token, *Microsoft. Identity. Web* offre due meccanismi per chiamare un'API Web downstream da un'altra API. L'opzione scelta dipende dal fatto che si voglia chiamare Microsoft Graph o un'altra API.

### <a name="option-1-call-microsoft-graph"></a>Opzione 1: chiamare Microsoft Graph

Se si vuole chiamare Microsoft Graph, Microsoft. Identity. Web consente di usare direttamente `GraphServiceClient` (esposto da SDK Microsoft Graph) nelle azioni dell'API. Per esporre Microsoft Graph:

1. Aggiungere il pacchetto NuGet [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) al progetto.
1. Aggiungere `.AddMicrosoftGraph()` after `.EnableTokenAcquisitionToCallDownstreamApi()` nel file *Startup.cs* . `.AddMicrosoftGraph()` include diverse sostituzioni. Utilizzando la sostituzione che accetta una sezione di configurazione come parametro, il codice diventa:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Opzione 2: chiamare un'API Web downstream diversa da Microsoft Graph

Per chiamare un'API downstream diversa da Microsoft Graph, *Microsoft. Identity. Web* fornisce `.AddDownstreamWebApi()` , che richiede token e chiama l'API Web downstream.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Come per le app Web, è possibile scegliere diverse implementazioni della cache dei token. Per informazioni dettagliate, vedere [serializzazione della cache del token Web di Microsoft Identity](https://aka.ms/ms-id-web/token-cache-serialization) su GitHub.

Nell'immagine seguente vengono illustrate le diverse possibilità di *Microsoft. Identity. Web* e il relativo effetto sul file *Startup.cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.png" alt-text="Quando si crea un'API Web, è possibile scegliere di chiamare un'API downstream e implementazioni della cache di token.":::

> [!NOTE]
> Per comprendere gli esempi di codice, è necessario conoscere le [Nozioni fondamentali su ASP.NET Core](/aspnet/core/fundamentals), in particolare l'[inserimento delle dipendenze](/aspnet/core/fundamentals/dependency-injection) e le [opzioni](/aspnet/core/fundamentals/configuration/options).

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

Per altre informazioni sul protocollo OBO, vedere [Microsoft Identity Platform e OAuth 2,0 on-conto-of Flow](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API Web che chiama le API Web: acquisire un token per l'app](scenario-web-api-call-api-acquire-token.md)
