<properties
   pageTitle="Proteggere un'API Web back-end in un'applicazione multi-tenant | Microsoft Azure"
   description="Come proteggere un'API Web back-end"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Proteggere un'API Web back-end in un'applicazione multi-tenant

Questo articolo fa [parte di una serie]. Esiste anche un'[applicazione di esempio] che accompagna questa serie.

L'applicazione [Tailspin Surveys] usa un'API Web back-end per gestire le operazioni CRUD nei sondaggi. Ad esempio, quando un utente fa clic su "My Surveys" l'applicazione Web invia una richiesta HTTP all'API Web:

```
GET /users/{userId}/surveys
```

L'API Web restituisce un oggetto JSON:

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

L'API Web non consente le richieste anonime, quindi l'app Web deve autenticarsi con i token di connessione OAuth 2.

> [AZURE.NOTE] Si tratta di uno scenario di connessioni tra server specifici. L'applicazione non esegue chiamate AJAX all'API dal browser client.

Esistono due principali approcci possibili:

- Identità utente delegato. L'applicazione Web si autentica con l'identità utente.
- Identità dell'applicazione. L'applicazione Web si autentica con l'ID client tramite il flusso di credenziali client OAuth2.

L'applicazione Tailspin implementa l'identità utente delegato. Le principali differenze sono le seguenti:

**Identità utente delegato**

- Il token di connessione inviato all'API Web contiene l'identità utente.
- L'API Web prende decisioni di autorizzazione in base all'identità utente.
- L'applicazione Web deve gestire gli errori 403 (accesso negato) dall'API Web se l'utente non è autorizzato a eseguire un'azione.
- In genere, l'applicazione Web continua a prendere alcune decisioni di autorizzazione che influiscono sull'interfaccia utente, ad esempio mostrare o nascondere elementi dell'interfaccia utente.
- Con questo approccio, l'API Web può essere usata da client non attendibili, ad esempio un'applicazione JavaScript o un'applicazione client nativa.

**Identità dell'applicazione**

- L'API Web non ottiene informazioni sull'utente.
- L'API Web non può eseguire alcuna autorizzazione basata sull'identità utente. Tutte le decisioni di autorizzazione vengono prese dall'applicazione Web.  
- L'API Web non può essere usata da un client non attendibile, ad esempio JavaScript o un'applicazione client nativa.
- Questo approccio è in qualche modo più semplice da implementare perché non sussiste alcuna logica di autorizzazione nell'API Web.

In entrambi gli approcci, l'applicazione Web deve ottenere un token di accesso che corrisponde alle credenziali necessarie per chiamare l'API Web.

- Per l'identità utente delegato il token deve provenire da IDP, in grado di rilasciare un token per conto dell'utente.

- Per le credenziali client, un'applicazione può ottenere il token da IDP oppure ospitare il proprio token server. Non scrivere un token server da zero, ma usare un framework testato come [IdentityServer3]. In caso di autenticazione con Azure AD è consigliabile ottenere il token di accesso da Azure AD, anche con il flusso di credenziali client.

Il resto di questo articolo presuppone l'autenticazione dell'applicazione con Azure AD.

![Ottenere il token di accesso](media/guidance-multitenant-identity/access-token.png)

## Registrare l'API Web in Azure AD

Per consentire ad Azure AD di emettere un token di connessione per l'API Web, è necessario configurare alcuni aspetti in Azure AD.

1. [Registrare l'API Web in Azure AD].

2. Aggiungere l'ID client dell'app Web nel manifesto dell'applicazione API Web nella proprietà `knownClientApplications`. Vedere l'articolo su come [aggiornare i manifesti delle applicazioni].

3. [Autorizzare l'applicazione Web a chiamare l'API Web].

  Nel portale di gestione di Microsoft Azure è possibile impostare due tipi di autorizzazioni: "Autorizzazioni applicazione" per l'identità dell'applicazione (flusso di credenziali client) o "Autorizzazioni delegate" per l'identità utente delegato.

  ![Autorizzazioni delegate](media/guidance-multitenant-identity/delegated-permissions.png)

## Recupero di un token di accesso

Prima di chiamare l'API Web, l'applicazione Web ottiene un token di accesso da Azure AD. In un'applicazione .NET usare [Azure AD Authentication Library (ADAL) per .NET][ADAL].

Nel flusso di codici di autorizzazione OAuth 2 l'applicazione scambia un codice di autorizzazione per un token di accesso. Il codice seguente usa ADAL per ottenere il token di accesso. Questo codice viene chiamato durante l'evento `AuthorizationCodeReceived`.

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

Ecco i diversi parametri necessari:

- `authority`. Derivato dall'ID tenant dell'utente connesso. Non l'ID tenant del provider SaaS.  
- `authorizationCode`. Il codice di autorizzazione restituito da IDP.
- `clientId`. L'ID client dell'applicazione Web.
- `clientSecret`. Chiave privata client dell'applicazione Web.
- `redirectUri`. L'URI di reindirizzamento impostato per OpenID Connect. L'IDP richiama qui con il token.
- `resourceID`. L'URI ID app dell'API Web creato al momento della registrazione dell'API Web in Azure AD
- `tokenCache`. Un oggetto che memorizza nella cache i token di accesso. Vedere l'articolo sulla [memorizzazione nella cache dei token].

Se `AcquireTokenByAuthorizationCodeAsync` viene eseguito correttamente, ADAL memorizza nella cache il token. In seguito è possibile ottenere il token dalla cache chiamando AcquireTokenSilentAsync:

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

dove `userId` è l'ID oggetto dell'utente trovato nell'attestazione `http://schemas.microsoft.com/identity/claims/objectidentifier`.

## Usare il token di accesso per chiamare l'API Web

Dopo aver creato il token, inviarlo all'API Web nell'intestazione di autorizzazione delle richieste HTTP.

```
Authorization: Bearer xxxxxxxxxx
```

Il seguente metodo di estensione dall'applicazione Surveys imposta l'intestazione di autorizzazione su una richiesta HTTP con la classe **HttpClient**.

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] Vedere [HttpClientExtensions.cs].

## Autenticarsi nell'API Web

L'API Web deve autenticare il token di connessione. In ASP.NET Core 1.0 è possibile usare il pacchetto [Microsoft.AspNet.Authentication.JwtBearer][JwtBearer]. Questo pacchetto offre un middleware che consente all'applicazione di ricevere i token di connessione OpenID Connect.

Registrare il middleware nella classe `Startup` dell'API Web.

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] Vedere [Startup.cs].

- **Destinatari**. Impostare questa opzione per l'URL dell'ID app per l'API Web creato al momento della registrazione dell'API Web con Azure AD.
- **Autorità**. Per un'applicazione multi-tenant, impostarlo su https://login.microsoftonline.com/common/``.
- **TokenValidationParameters**. Per un'applicazione multi-tenant, impostare **ValidateIssuer** su falso. Ciò significa che l'applicazione convaliderà l'autorità di certificazione.
- **Events** è una classe che deriva da **JwtBearerEvents**.

### Convalida dell'autorità di certificazione

Convalidare l'autorità di certificazione del token nell'evento **JwtBearerEvents.ValidatedToken**. L'autorità di certificazione viene inviata nell'attestazione "iss".

Nell'applicazione Surveys l'API Web non gestisce l'[iscrizione del tenant]. Per questo motivo, controlla solo se l'autorità di certificazione si trova già nel database dell'applicazione. In caso contrario, viene generata un'eccezione che determina la mancata autenticazione.

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] Vedere [SurveysJwtBearerEvents.cs].

È anche possibile usare l'evento **ValidatedToken** per eseguire la [trasformazione delle attestazioni]. Tenere presente che le attestazioni provengono direttamente da Azure AD e, se l'applicazione Web ha eseguito trasformazioni delle attestazioni, queste non si riflettono nel token di connessione ricevuto dall'API Web.

## Authorization

Per una discussione generale sulle autorizzazioni, vedere l'articolo sulle [autorizzazioni]. Per un'API Web, la differenza principale è che il middleware JwtBearer gestisce le risposte di autorizzazione.

Ad esempio, per limitare un'azione del controller per gli utenti autenticati:

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```


Questo restituisce un codice di stato 401 se l'utente non è autenticato.

Per limitare un'azione del controller per criteri di autorizzazione:

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

Questo restituisce un codice di stato 401 se l'utente non è autenticato e 403 se l'utente è autenticato ma non autorizzato. Registrare i criteri all'avvio:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```


## Risorse aggiuntive

- [Scenari di autenticazione per Azure AD][auth-scenarios]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[auth-scenarios]: ../active-directory/active-directory-authentication-scenarios.md/#web-application-to-web-api
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[parte di una serie]: guidance-multitenant-identity.md
[Tailspin Surveys]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Registrare l'API Web in Azure AD]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[aggiornare i manifesti delle applicazioni]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[Autorizzare l'applicazione Web a chiamare l'API Web]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[memorizzazione nella cache dei token]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[iscrizione del tenant]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[trasformazione delle attestazioni]: guidance-multitenant-identity-claims.md#claims-transformations
[autorizzazioni]: guidance-multitenant-identity-authorize.md
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->