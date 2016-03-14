<properties
   pageTitle="Autorizzazioni nelle applicazioni multi-tenant | Microsoft Azure"
   description="Come un'applicazione multi-tenant può eseguire l'autenticazione degli utenti da Azure AD"
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

# Autorizzazioni nelle applicazioni multi-tenant

Questo articolo fa [parte di una serie](guidance-multitenant-identity.md). Per questa serie è anche disponibile un'[applicazione di esempio] completa.

Questo articolo descrive in che modo un'applicazione multi-tenant può autenticare gli utenti da Azure Active Directory (Azure AD) usando OpenID Connect (OIDC) per l'autenticazione.

## Panoramica

L'[implementazione di riferimento](guidance-multitenant-identity-tailspin.md) è un'applicazione ASP.NET Core 1.0. L'applicazione usa il middleware OpenID Connect predefinito per eseguire il flusso di autenticazione di OIDC. Il diagramma seguente illustra a livello generale ciò che accade quando l'utente esegue l'accesso.

![Flusso di autenticazione](media/guidance-multitenant-identity/auth-flow.png)

1.	L'utente fa clic sul pulsante "Accedi" nell'app. L'azione viene gestita da un controller MVC.
2.	Il controller MVC restituisce un'azione **ChallengeResult**.
3.	Il middleware intercetta l'azione **ChallengeResult** e crea una risposta 302 che reindirizza l'utente alla pagina di accesso di Azure AD.
4.	L'utente viene autenticato con Azure AD.
5.	Azure AD invia un token ID all'applicazione.
6.	Il middleware convalida il token ID. A questo punto, l'utente viene autenticato all'interno dell'applicazione.
7.	Il middleware reindirizza di nuovo l'utente all'applicazione.

## Registrazione dell'applicazione con Azure AD

Per abilitare OpenID Connect, il provider di SaaS registra l'applicazione all'interno del tenant di Azure AD.

Per registrare l'applicazione, seguire la procedura descritta nella sezione "Per registrare un'applicazione nel portale di gestione di Azure" dell'articolo [Integrazione di applicazioni con Azure Active Directory](../active-directory/active-directory-integrating-applications.md).

Nella pagina **Configura**:

-	Prendere nota dell'ID client.
-	In **L'applicazione è multi-tenant** selezionare **Sì**.
-	Impostare **URL di risposta** su un URL a cui Azure AD invierà la risposta di autenticazione. È possibile usare l'URL di base dell'app.
  -	Nota: è possibile usare un percorso URL qualsiasi, purché il nome host corrisponda all'app distribuita.
  -	È possibile impostare più URL di risposta. Durante lo sviluppo, è possibile usare un indirizzo `localhost` per eseguire l'applicazione in locale.
-	Generare un segreto client: in **Chiavi** fare clic sull'elenco a discesa **Seleziona durata** e selezionare 1 o 2 anni. La chiave sarà visibile quando si fa clic su **Salva**. Assicurarsi di copiare il valore, perché non verrà visualizzato di nuovo quando si ricarica la pagina di configurazione.

## Configurazione del middleware di autenticazione

Questa sezione descrive come configurare il middleware di autenticazione in ASP.NET Core 1.0 per l'autenticazione multi-tenant con OpenID Connect.

Nella classe di avvio aggiungere il middleware OpenID Connect:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] Vedere [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs).

Per altre informazioni sulla classe di avvio, vedere l'articolo relativo all'[avvio dell'applicazione](https://docs.asp.net/en/latest/fundamentals/startup.html) nella documentazione di ASP.NET Core 1.0.

Impostare le opzioni del middleware seguenti:

- **ClientId**. ID client dell'applicazione ottenuto quando è stata registrata l'applicazione in Azure AD.
- **Authority**. Per un'applicazione multi-tenant impostarla su `https://login.microsoftonline.com/common/`. Si tratta dell'URL per l'endpoint comune di AD Azure, che consente agli utenti di qualsiasi tenant di Azure AD di accedere. Per ulteriori altre sull'endpoint comune, vedere [questo post di blog](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/).
- In **TokenValidationParameters** impostare **ValidateIssuer** su false. Significa che l'applicazione sarà responsabile per la convalida del valore dell'autorità emittente nel token ID. Il middleware convalida comunque il token stesso. Per altre informazioni sulla convalida dell'autorità emittente, vedere la sezione relativa alla[convalida dell'autorità emittente](guidance-multitenant-identity-claims.md#issuer-validation).
- **CallbackPath**. Impostare questa opzione sullo stesso percorso dell'URL di risposta registrato in Azure AD. Ad esempio, se l'URL di risposta è `http://contoso.com/aadsignin`, **CallbackPath** deve essere `aadsignin`. Se non si imposta questa opzione, il valore predefinito è `signin-oidc`.
- **PostLogoutRedirectUri**. Specificare un URL per reindirizzare gli utenti dopo la disconnessione. Deve trattarsi di una pagina che consente le richieste anonime, in genere la home page.
- **SignInScheme**. Impostare questa opzione su `CookieAuthenticationDefaults.AuthenticationScheme`. Questa impostazione indica che dopo l'autenticazione dell'utente, le attestazioni utente vengono archiviate in un cookie in locale. Questo cookie indica in che modo l'utente resta connesso durante la sessione del browser.
- **Eventi.** Callback degli eventi, vedere [Eventi di autenticazione](#authentication-events).

Aggiungere alla pipeline anche il middleware di autenticazione dei cookie. Il middleware è responsabile della scrittura delle attestazioni utente a un cookie e quindi della lettura del cookie durante i successivi caricamenti della pagina.

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## Avvio del flusso di autenticazione

Per avviare il flusso di autenticazione in ASP.NET MVC, restituire un'azione **ChallengeResult** dal controller:

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

In questo modo il middleware restituire una risposta 302 (contenuto trovato) che reindirizza all'endpoint di autenticazione.

## Sessione di accesso utente

Come accennato, quando l'utente accede per la prima volta, il middleware di autenticazione dei cookie scrive le attestazioni utente in un cookie. Successivamente, le richieste HTTP vengono autenticate leggendo il cookie.

Per impostazione predefinita, il middleware del cookie scrive un [cookie di sessione][session-cookie] che viene eliminato dopo la chiusura del browser da parte dell'utente. La prossima volta che l'utente visita il sito, dovrà ripetere l'accesso. Se tuttavia si imposta **IsPersistent** su true in **ChallengeResult**, il middleware scrive un cookie permanente, che manterrà l'utente connesso dopo la chiusura del browser. È possibile configurare la scadenza del cookie. Vedere l'articolo relativo al [controllo delle opzioni dei cookie][cookie-options]. L'uso dei cookie permanenti è più utile per l'utente, ma potrebbe non essere appropriato per alcune applicazioni, ad esempio, un'applicazione bancaria, in cui si vuole che l'utente esegua l'accesso ogni volta.

## Informazioni sul middleware per OpenID Connect

Il middleware per OpenID Connect in ASP.NET nasconde la maggior parte dei dettagli del protocollo. Questa sezione include alcune note sull'implementazione, che possono essere utili per comprendere il flusso del protocollo.

Prima di tutto si esaminerà flusso di autenticazione in termini di ASP.NET, ignorando i dettagli del flusso del protocollo OIDC tra l'applicazione e Azure AD. Il diagramma seguente mostra il processo.

![Flusso di accesso](media/guidance-multitenant-identity/sign-in-flow.png)

In questo diagramma sono presenti due controller MVC. Il controller Account gestisce le richieste di accesso e il controller Home visualizza la home page.

Ecco il processo di autenticazione:

1. L'utente fa clic sul pulsante "Accedi" e il browser invia una richiesta GET. Ad esempio: `GET /Account/SignIn/`.
2. Il controller Account restituisce un'azione `ChallengeResult`.
3. Il middleware OIDC restituisce una risposta HTTP 302 con reindirizzamento ad Azure AD.
4. Il browser invia la richiesta di autenticazione ad Azure AD.
5. L'utente accede ad Azure e Azure AD invia una risposta di autenticazione.
6. Il middleware OIDC crea un'entità attestazioni e la passa al middleware di autenticazione dei cookie.
7. Il middleware dei cookie serializza l'entità attestazioni e imposta un cookie.
8. Il middleware OIDC reindirizza all'URL di callback dell'applicazione.
10. Il browser segue il reindirizzamento e invia il cookie nella richiesta.
11. Il middleware dei cookie deserializza il cookie in un'entità di attestazioni e imposta `HttpContext.User` come per l'entità attestazioni. La richiesta viene indirizzata a un controller MVC.

### Ticket di autenticazione

Se l'autenticazione riesce, il middleware OIDC crea un ticket di autenticazione che contiene un'entità attestazioni con le attestazioni dell'utente. È possibile accedere al ticket all'interno dell'evento **AuthenticationValidated** o **TicketReceived**.

> [AZURE.NOTE] Fino a quando non viene completato l'intero flusso di autenticazione, `HttpContext.User` include ancora un'identità anonima, _non_ l'utente autenticato. L'entità anonima include una raccolta di attestazioni vuota. Dopo il completamento dell'autenticazione e il reindirizzamento dell'app, il middleware dei cookie deserializza i cookie di autenticazione e imposta `HttpContext.User` su un'entità attestazioni che rappresenta l'utente autenticato.

### Eventi di autenticazione

Durante il processo di autenticazione il middleware OpenID Connect genera una serie di eventi:

- **RedirectToAuthenticationEndpoint**. Chiamato subito prima che il middleware esegua il reindirizzamento all'endpoint di autenticazione. È possibile usare questo evento per modificare l'URL di reindirizzamento. Ad esempio, per aggiungere parametri della richiesta. Per un esempio, vedere [Aggiunta della richiesta di consenso dell'amministratore](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt).

- **AuthorizationResponseReceived**. Chiamato dopo che il middleware riceve la risposta di autenticazione dal provider di identità (IDP), ma prima che il middleware convalidi la risposta.

- **AuthorizationCodeReceived**. Chiamato con il codice di autorizzazione.

- **TokenResponseReceived**. Chiamato dopo che il middleware ha ottenuto un token di accesso dall'IDP. Si applica solo al flusso del codice di autorizzazione.

- **AuthenticationValidated**. Chiamato dopo che il middleware ha convalidato il token ID. A questo punto, l'applicazione ha un set di attestazioni convalidate relative all'utente. È possibile usare questo evento per eseguire una convalida aggiuntiva alle attestazioni o per trasformare le attestazioni. Vedere l'articolo relativo all'[utilizzo delle attestazioni](guidance-multitenant-identity-claims.md).

- **UserInformationReceived**. Chiamato se il middleware ottiene il profilo utente dall'endpoint di informazioni sull'utente. Si applica solo al flusso del codice di autorizzazione e solo quando nelle opzioni del middleware è impostata `GetClaimsFromUserInfoEndpoint = true`.

- **TicketReceived**. Chiamato quando viene completata l'autenticazione. Questo è l'ultimo evento, presupponendo che l'autenticazione riesca. Dopo la gestione di questo evento, l'utente viene connesso all'app.

- **AuthenticationFailed**. Chiamato se l'autenticazione non riesce. Usare questo evento per gestire gli errori di autenticazione, ad esempio, per il reindirizzamento a una pagina di errore.

Per fornire i callback per questi eventi, impostare l'opzione **Events** nel middleware. Esistono due modi diversi per dichiarare i gestori eventi: inline con espressioni lambda o in una classe che deriva da **OpenIdConnectEvents**.

Inline con espressioni lambda:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

Con derivazione da **OpenIdConnectEvents**:

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

Il secondo approccio è consigliabile se i callback degli eventi hanno una logica sostanziale, in modo da mantenere semplice la classe di avvio. L'implementazione di riferimento usa questo approccio. Vedere [SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs).

### Endpoint di OpenId Connect

Azure AD supporta l'[individuazione OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html), in cui il provider di identità (IDP) restituisce un documento di metadati JSON da un [endpoint well-known](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). Il documento di metadati contiene informazioni quali:

-	URL dell'endpoint di autorizzazione. Si tratta della destinazione di reindirizzamento dell'app per autenticare l'utente.
-	URL dell'endpoint di "fine sessione", a cui l'app passa per disconnettere l'utente.
-	URL per ottenere le chiavi di firma, che il client usa per convalidare i token OIDC ottenuto dall'IDP.

Per impostazione predefinita, il middleware OIDC riconosce la modalità di recupero dei metadati. Impostare l'opzione **Authority** nel middleware per consentire al middleware di costruire l'URL per i metadati. È possibile eseguire l'override dell'URL dei metadati impostando l'opzione **MetadataAddress**.

### Flussi di OpenId Connect

Per impostazione predefinita, il middleware OIDC usa un flusso ibrido con una modalità di risposta che prevede un Post per i form.

-	_Flusso ibrido_ significa che il client può ottenere un token ID e un codice di autorizzazione nello stesso round trip al server di autorizzazione.
-	_Modalità di risposta con Post per i form_ significa che il server di autorizzazione usa una richiesta HTTP POST per inviare il token ID e il codice di autorizzazione all'applicazione. I valori sono nel formato form-urlencoded (tipo di contenuto = "application/x-www-form-urlencoded").

Quando il middleware OIDC reindirizza all'endpoint di autorizzazione, l'URL di reindirizzamento include tutti i parametri della stringa di query necessari per OIDC. Per il flusso ibrido:

-	client\_id Questo valore viene impostato nell'opzione **ClientId**.
-	scope = "openid profile", si tratta di una richiesta OIDC e si vuole recuperare il profilo dell'utente.
-	response\_type = "code id\_token". Specifica il flusso ibrido.
-	response\_mode = "form\_post". Specifica la risposta del Post per i form.

Per specificare un flusso diverso, impostare la proprietà **ResponseType** nelle opzioni. Ad esempio:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->