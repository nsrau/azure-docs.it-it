---
title: Azure Active Directory B2C | Documentazione Microsoft
description: "Come compilare un&quot;applicazione Web con funzionalità di registrazione, accesso, modifica del profilo e reimpostazione della password usando Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 30261336-d7a5-4a6d-8c1a-7943ad76ed25
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 87b8b91fc5970bd127dfdc47e24d99a19471aa8c
ms.lasthandoff: 05/03/2017


---
# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>Azure AD B2C: eseguire l'iscrizione e l'accesso in un'app Web ASP.NET

Tramite Azure AD B2C, è possibile aggiungere potenti funzionalità di gestione di identità per l'app Web. Questo articolo illustra come creare un'app Web ASP.NET con funzionalità di registrazione, accesso, modifica del profilo e reimpostazione della password per gli utenti.

## <a name="create-an-azure-ad-b2c-directory"></a>Creare una directory Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire con questa guida.

## <a name="create-an-application"></a>Creare un'applicazione

Successivamente, è necessario creare un'app Web nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

* Includere un' **app Web o un'API Web** nell'applicazione.
* Immettere `https://localhost:44316/` come **URI di reindirizzamento**. Si tratta dell'URL predefinito per questo esempio di codice.
* Copiare l'**ID applicazione** assegnato all'app,  Sarà necessario più avanti.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md)specifici. Questo esempio di codice contiene tre esperienze di identità: **registrazione e accesso**, **modifica del profilo** e **reimpostazione della password**.  È necessario creare i criteri per ogni tipo, come descritto nell' [articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md). Durante la creazione dei criteri, assicurarsi di:

* Scegliere **Iscrizione ID utente** o **Iscrizione posta elettronica** nel pannello dei provider di identità.
* Scegliere **Nome visualizzato** e altri attributi per l'iscrizione nei criteri di iscrizione e accesso.
* Scegliere l'attestazione **Nome visualizzato** come attestazione dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
* Copiare il **Nome** di ogni criterio dopo averlo creato. I nomi dei criteri saranno necessari in un secondo momento.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i criteri è possibile passare alla compilazione dell'app.

## <a name="download-the-code"></a>Scaricare il codice

Il codice per questa esercitazione è salvato su [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). È possibile clonare l'esempio eseguendo:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare. Il file della soluzione contiene due progetti: `TaskWebApp` e `TaskService`. `TaskWebApp` è l'applicazione Web MVC con cui l'utente interagisce. `TaskService` è l'API Web back-end dell'app in cui viene archiviato l'elenco attività di ogni utente. Questo articolo illustra solo l'applicazione `TaskWebApp`. Per informazioni su come compilare `TaskService` usando Azure AD B2C, vedere l'[esercitazione sulle API Web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Aggiornare la configurazione di Azure AD B2C

L'esempio è configurato per l'uso dei criteri e dell'ID client del tenant demo. Se si desidera usare il proprio tenant, sarà necessario aprire `web.config` nel progetto `TaskWebApp` e sostituire i valori per

* `ida:Tenant` con il nome del tenant
* `ida:ClientId` con l'ID dell'applicazione di tipo app Web
* `ida:ClientSecret` con la chiave privata dell'app Web
* `ida:SignUpSignInPolicyId` con il nome del criterio "Iscrizione o accesso"
* `ida:EditProfilePolicyId` con il nome del criterio "Modifica profilo"
* `ida:ResetPasswordPolicyId` con il nome del criterio "Reimposta password"

## <a name="add-authentication-support"></a>Aggiungere il supporto per l'autenticazione

È ora possibile configurare l'app per l'uso di Azure AD B2C. L'app comunica con Azure AD B2C inviando richieste di autenticazione OpenID Connect. Le richieste determinano l'esperienza dell'utente che l'app desidera eseguire specificando i criteri. È possibile usare la libreria OWIN di Microsoft per inviare queste richieste, eseguire criteri, gestire le sessioni utente e così via.

### <a name="install-owin"></a>Installare OWIN

Per iniziare, aggiungere i pacchetti NuGet del middleware OWIN al progetto usando la Console di Gestione pacchetti di Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="add-an-owin-startup-class"></a>Aggiungere una classe di avvio OWIN

Aggiungere una classe di avvio OWIN all'API denominata `Startup.cs`.  Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi **Nuovo elemento** e cercare "OWIN". Il middleware OWIN richiamerà il metodo `Configuration(…)` all'avvio dell'app.

In questo esempio la dichiarazione di classe è stata modificata in `public partial class Startup` ed è stata implementata l'altra parte della classe in `App_Start\Startup.Auth.cs`. Nel metodo `Configuration` è stata aggiunta una chiamata a `ConfigureAuth`, definita in `Startup.Auth.cs`. Dopo le modifiche, `Startup.cs` ha un aspetto analogo al seguente:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-the-authentication-middleware"></a>Configurazione del middleware di autenticazione

Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(...)`. I parametri indicati in `OpenIdConnectAuthenticationOptions` fungono da coordinate per consentire all'app di comunicare con Azure AD B2C. Se non si specificano determinati parametri, verrà usato il valore predefinito. Ad esempio, nell'esercitazione non viene specificato `ResponseType`, pertanto il valore predefinito `code id_token` verrà usato in ciascuna richiesta in uscita per Azure AD B2C.

È necessario configurare anche l'autenticazione tramite cookie. Il middleware OpenID Connect usa i cookie per gestire, tra l'altro, le sessioni utente.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

In `OpenIdConnectAuthenticationOptions` sopra, si definisce un set di funzioni di callback per le notifiche specifiche che vengono ricevute dal middleware OpenID Connect. Questi comportamenti vengono definiti usando un oggetto `OpenIdConnectAuthenticationNotifications` memorizzato nella variabile `Notifications`. Nell'esempio vengono definiti tre diversi callback a seconda dell'evento.

#### <a name="using-different-policies"></a>Uso di criteri diversi

La notifica `RedirectToIdentityProvider` viene attivata ogni volta che viene effettuata una richiesta per Azure AD B2C. Nella funzione di callback `OnRedirectToIdentityProvider`, se si desidera usare criteri diversi è necessario controllare la chiamata in uscita. Per reimpostare la password o modificare un profilo, è necessario usare i criteri corrispondenti, ad esempio i criteri di reimpostazione della password anziché i criteri predefiniti di "registrazione o accesso".

Nell'esempio quando un utente desidera reimpostare la password o modificare il profilo, vengono aggiunti i criteri che si desidera usare nel contesto di OWIN. È possibile fare tutto questo effettuando le operazioni seguenti:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

È inoltre possibile implementare la funzione di callback `OnRedirectToIdentityProvider` effettuando le operazioni seguenti:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

#### <a name="handling-authorization-codes"></a>Gestione dei codici di autorizzazione

La notifica `AuthorizationCodeReceived` viene attivata quando si riceve un codice di autorizzazione. Il middleware OpenID Connect non supporta lo scambio di codici per i token di accesso. È possibile scambiare manualmente il codice per il token in una funzione di callback. Per altre informazioni, consultare la [documentazione](active-directory-b2c-devquickstarts-web-api-dotnet.md) che spiega la procedura.

#### <a name="handling-errors"></a>Gestione degli errori

La notifica `AuthenticationFailed` viene attivata quando l'autenticazione non ha esito positivo. Nel metodo di callback è possibile gestire gli errori in base alle necessità. È tuttavia necessario aggiungere un controllo del codice di errore `AADB2C90118`. Durante l'esecuzione dei criteri di "registrazione o accesso", l'utente può di fare clic sul collegamento **Password dimenticata?**. In questo caso, Azure AD B2C invierà all'app il codice di errore che indica che l'app deve effettuare una richiesta usando i criteri di reimpostazione della password.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Inviare richieste di autenticazione ad Azure AD

L'app è ora configurata correttamente per comunicare con Azure AD B2C usando il protocollo di autenticazione OpenID Connect. OWIN ha gestito tutti dettagli relativi alla creazione dei messaggi di autenticazione, alla convalida dei token da Azure AD B2C e alla gestione della sessione utente. A questo punto, non resta che avviare ogni flusso utente.

Quando un utente seleziona **Accedi/Iscriviti**, **Modifica profilo** o **Reimposta password** nell'app Web, l'azione associata viene richiamata in `Controllers\AccountController.cs`:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

È anche possibile usare OWIN per disconnettere l'utente dell'app. In `Controllers\AccountController.cs` c'è:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Oltre a richiamare in modo esplicito i criteri, è possibile usare un tag `[Authorize]` nei controller, che attiverà l'esecuzione dei criteri se l'utente non è connesso. Aprire `Controllers\HomeController.cs` e aggiungere il tag `[Authorize]` al controller delle attestazioni.  Quando viene raggiunto il tag `[Authorize]` , in OWIN verrà selezionato l'ultimo criterio configurato.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

## <a name="display-user-information"></a>Visualizzare le informazioni utente

Quando si autenticano gli utenti usando OpenID Connect, Azure AD B2C restituisce un token ID all'app che contiene le **attestazioni**. Si tratta di asserzioni sull'utente. È possibile usare le attestazioni per personalizzare l'app.

Aprire il file `Controllers\HomeController.cs` . È possibile accedere alle attestazioni utente nei controller tramite l'oggetto entità di sicurezza `ClaimsPrincipal.Current` .

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

È possibile accedere a qualsiasi attestazione ricevuta dall'applicazione nello stesso modo.  Nella pagina **Attestazioni** è disponibile un elenco di tutte le attestazioni ricevute dall'app.

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto e possibile compilare ed eseguire l'app. Effettuare l'iscrizione all'app usando un indirizzo di posta elettronica o un nome utente. Disconnettersi e accedere nuovamente con lo stesso account utente. Modificare il profilo o reimpostare la password. Disconnettersi ed effettuare l'iscrizione usando un account utente diverso. Si noti che le informazioni visualizzate nella scheda **Attestazioni** corrispondono alle informazioni configurate nei criteri.

## <a name="add-social-idps"></a>Aggiungere i provider di identità per i social network

L'app supporta attualmente solo l'iscrizione e l'accesso dell'utente con **account locali**. Si tratta di account archiviati nella directory B2C che usano un nome utente e una password. Tramite Azure AD B2C è possibile aggiungere il supporto per altri **provider di identità** (IdP) senza modificare il codice.

Per aggiungere provider di identità per i social media all'applicazione, seguire le istruzioni dettagliate fornite in questi articoli. Per ogni provider di identità che si vuole supportare, è necessario registrare un'applicazione nel relativo sistema e ottenere un ID client.

* [Configurare Facebook come provider di identità](active-directory-b2c-setup-fb-app.md)
* [Configurare Google come provider di identità](active-directory-b2c-setup-goog-app.md)
* [Configurare Amazon come provider di identità](active-directory-b2c-setup-amzn-app.md)
* [Configurare LinkedIn come provider di identità](active-directory-b2c-setup-li-app.md)

Dopo aver aggiunto i provider di identità alla directory B2C, è necessario modificare ognuno dei tre criteri per includere i nuovi provider di identità, come descritto nell' [articolo di riferimento sui criteri](active-directory-b2c-reference-policies.md). Dopo aver salvato i criteri, eseguire nuovamente l'app.  I nuovi provider di identità dovrebbero essere stati aggiunti tra le opzioni di accesso e iscrizione in ognuna delle esperienze per l'identità.

Provare a usare i criteri e osservare gli effetti sull'app di esempio. Aggiungere o rimuovere provider di identità, manipolare le attestazioni dell'applicazione o modificare gli attributi per l'iscrizione. Fare delle prove fino a quando non è chiaro il modo in cui criteri, richieste di autenticazione e OWIN sono collegati tra loro.

