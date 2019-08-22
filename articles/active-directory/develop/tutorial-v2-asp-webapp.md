---
title: Introduzione al server Web ASP.NET per Azure AD v2.0 | Microsoft Docs
description: Implementazione di accessi Microsoft in una soluzione ASP.NET con un'applicazione tradizionale basata su Web browser tramite lo standard OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce6ab2c5068d1628860356a9df0dd33c73948b3
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511961"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Aggiungere l'accesso con Microsoft a un'app Web ASP.NET

Questa guida illustra come implementare l'accesso con Microsoft usando una soluzione ASP.NET MVC con un'applicazione tradizionale basata su Web browser tramite OpenID Connect.

Al termine di questa guida, l'applicazione potrà accettare accessi di account personali, ad esempio outlook.com, live.com e di altro tipo. Tali account includono anche account aziendali o dell'istituto di istruzione di qualsiasi organizzazione o azienda che abbia eseguito l'integrazione con Azure Active Directory.

> Questa guida richiede Visual Studio 2019.  Se non lo si ha, è possibile  [scaricare Visual Studio 2019 gratuitamente](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funzionamento dell'app di esempio generata da questa guida

![Illustra come funziona l'app di esempio generata da questa esercitazione](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

L'applicazione di esempio creata è basata su uno scenario in cui si usa il browser per accedere a un sito Web ASP.NET che richiede all'utente di eseguire l'autenticazione tramite un pulsante di accesso. In questo scenario, la maggior parte delle operazioni necessarie per il rendering della pagina Web viene eseguita sul lato server.

## <a name="libraries"></a>Librerie

Questa guida usa le librerie seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware che consente a un'applicazione di usare OpenID Connect per l'autenticazione|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware che consente a un'applicazione di mantenere la sessione utente usando i cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Consente l'esecuzione in IIS di applicazioni basate su OWIN tramite la pipeline di richieste ASP.NET|

## <a name="set-up-your-project"></a>Configurare il progetto

Questa sezione illustra la procedura per l'installazione e la configurazione della pipeline di autenticazione tramite middleware OWIN in un progetto ASP.NET mediante OpenID Connect.

> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, [Scaricare un progetto](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) e passare direttamente al [passaggio di configurazione](#register-your-application) per configurare il codice di esempio prima di eseguirlo.

### <a name="create-your-aspnet-project"></a>Creare un progetto ASP.NET

1. In Visual Studio: `File` > `New` > `Project`
2. In *Visual C#\Web* selezionare `ASP.NET Web Application (.NET Framework)`
3. Assegnare un nome all'applicazione e fare clic su *OK*
4. Selezionare `Empty` e quindi selezionare la casella di controllo per aggiungere i riferimenti `MVC`

## <a name="add-authentication-components"></a>Aggiungere i componenti per l'autenticazione

1. In Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Aggiungere *pacchetti NuGet del middleware OWIN* digitando quanto segue nella finestra della Console di Gestione pacchetti:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Informazioni sulle librerie
> Le librerie precedenti abilitano l'accesso Single Sign-On (SSO) usando OpenID Connect tramite l'autenticazione basata su cookie. Al termine dell'autenticazione e dopo l'invio del token che rappresenta l'utente all'applicazione, il middleware OWIN crea un cookie di sessione. Il browser usa quindi questo cookie nelle richieste successive, in modo che l'utente non debba digitare di nuovo la password e non siano necessarie operazioni di verifica aggiuntive.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurare la pipeline di autenticazione

La procedura seguente consente di creare una classe di avvio del middleware OWIN per configurare l'autenticazione OpenID Connect. Questa classe verrà eseguita automaticamente all'avvio del processo di IIS.

> [!TIP]
> Se il progetto non include alcun file `Startup.cs` nella cartella radice:
> 1. Fare clic con il pulsante destro del mouse sulla cartella radice del progetto: > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Assegnare il nome `Startup.cs`
>
>> Assicurarsi che la classe selezionata sia una classe di avvio di OWIN e non una classe C# standard. Per confermarlo, verificare se `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` viene visualizzato sopra lo spazio dei nomi.

1. Aggiungere i riferimenti *OWIN* e *Microsoft.IdentityModel* a `Startup.cs`:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Sostituire la classe di avvio con il codice seguente:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> L'impostazione di `ValidateIssuer = false` è una semplificazione per questo avvio rapido. Nelle applicazioni reali è necessario convalidare l'autorità di certificazione. Vedere gli esempi per comprendere come eseguire questa operazione.

<!--start-collapse-->
> ### <a name="more-information"></a>Altre informazioni
> I parametri forniti in *OpenIDConnectAuthenticationOptions* fungeranno da coordinate per consentire all'applicazione di comunicare con Azure AD. Poiché il middleware OpenID Connect usa i cookie in background, è anche necessario configurare l'autenticazione dei cookie, come illustrato nel codice precedente. Il valore *ValidateIssuer* segnala a OpenIdConnect di non limitare l'accesso a un'organizzazione specifica.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Aggiungere un controller per gestire le richieste di accesso e disconnessione

Questo passaggio illustra come creare un nuovo controller per esporre i metodi di accesso e disconnessione.

1.  Fare clic con il pulsante destro del mouse sulla cartella `Controllers` e scegliere `Add` > `Controller`.
2.  Selezionare `MVC (.NET version) Controller – Empty`.
3.  Fare clic su *Aggiungi*.
4.  Assegnare il nome `HomeController` e fare clic su *Aggiungi*.
5.  Aggiungere i riferimenti *OWIN* alla classe:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Aggiungere al controller i due metodi seguenti per gestire l'accesso e la disconnessione avviando una richiesta di autenticazione tramite codice:
    
    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Creare la home page dell'app per l'accesso degli utenti tramite un pulsante di accesso

In Visual Studio creare una nuova visualizzazione per aggiungere il pulsante di accesso e mostrare le informazioni relative all'utente dopo l'autenticazione:

1.  Fare clic con il pulsante destro del mouse sulla cartella `Views\Home` e scegliere `Add View`.
2.  Denominarlo `Index`.
3.  Aggiungere al file il codice HTML seguente, che include il pulsante di accesso:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Altre informazioni
> Questa pagina aggiunge un pulsante di accesso in formato SVG con sfondo nero:<br/>![Accedi con Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Per altri pulsanti di accesso, vedere [questa pagina](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Linee guida sulla personalizzazione").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Aggiungere un controller per visualizzare le attestazioni dell'utente
Questo controller illustra gli usi dell'attributo `[Authorize]` per la protezione di un controller. Questo attributo limita l'accesso al controller ai soli utenti autenticati. Il codice seguente usa l'attributo per visualizzare le attestazioni utente recuperate durante l'accesso.

1.  Fare clic con il pulsante destro del mouse sulla cartella `Controllers` e scegliere `Add` > `Controller`.
2.  Selezionare `MVC {version} Controller – Empty`.
3.  Fare clic su *Aggiungi*.
4.  Assegnare il nome `ClaimsController`.
5.  Sostituire il codice della classe controller con il codice seguente, che aggiunge l'attributo `[Authorize]` alla classe:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Altre informazioni
> A causa dell'uso dell'attributo `[Authorize]`, tutti i metodi di questo controller possono essere eseguiti solo se l'utente è autenticato. Se un utente non autenticato prova ad accedere al controller, OWIN avvierà una richiesta di autenticazione e imporrà all'utente di autenticarsi. Il codice riportato sopra cerca nell'elenco di attestazioni attributi utente specifici inclusi nel token ID dell'utente. Tali attributi includono nome e cognome, nome utente e soggetto ID utente globale dell'utente. Contengono anche l'*ID tenant*, che rappresenta l'ID dell'organizzazione dell'utente. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Creare una visualizzazione per mostrare le attestazioni dell'utente

In Visual Studio creare una nuova visualizzazione per mostrare le attestazioni dell'utente in una pagina Web:

1.  Fare clic con il pulsante destro del mouse sulla cartella `Views\Claims` e scegliere `Add View`.
2.  Denominarlo `Index`.
3.  Aggiungere il codice HTML seguente al file:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Registrare l'applicazione

Per registrare l'applicazione e aggiungere le relative informazioni di registrazione alla soluzione, sono possibili due opzioni:

### <a name="option-1-express-mode"></a>Opzione 1: modalità Rapida

È possibile registrare rapidamente l'applicazione seguendo questa procedura:

1. Passare al nuovo riquadro [Portale di Azure - Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Immettere un nome per l'applicazione e fare clic su **Registra**.
1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.

### <a name="option-2-advanced-mode"></a>Opzione 2: modalità Avanzata

Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:

1. Passare a Visual Studio e:
   1. in Esplora soluzioni selezionare il progetto e controllare la finestra Proprietà (se non viene visualizzata la finestra Proprietà, premere F4).
   1. Impostare il valore di SSL abilitato su `True`.
   1. Fare clic con il pulsante destro del mouse sul progetto in Visual Studio e quindi scegliere **Proprietà** e la scheda **Web**. Nella sezione *Server* modificare *URL progetto* specificando l'URL SSL.
   1. Copiare l'URL SSL. Aggiungerlo all'elenco di URL di reindirizzamento nell'elenco corrispondente del portale di registrazione nel passaggio successivo:<br/><br/>![Proprietà del progetto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
   1. Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `ASPNET-Tutorial`.
   1. Aggiungere l'URL SSL copiato da Visual Studio nel passaggio 1 (ad esempio `https://localhost:44368/`) in **URL di risposta** e fare clic su **Registra**.
1. Selezionare il menu **Autenticazione**, impostare **Token ID** in **Concessione implicita** e quindi selezionare **Salva**.
1. Aggiungere il codice seguente in `web.config`, disponibile nella sezione `configuration\appSettings` della cartella radice:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Sostituire `ClientId` con l'ID applicazione appena registrato.
1. Sostituire `redirectUri` con l'URL SSL del progetto.

## <a name="test-your-code"></a>Testare il codice

Per eseguire test dell'applicazione in Visual Studio, premere **F5** per eseguire il progetto. Il browser viene aperto sulla posizione http://<span></span>localhost:{porta} e viene visualizzato il pulsante **Accedi con Microsoft**. Selezionare il pulsante per avviare il processo di accesso.

Quando si è pronti per eseguire il test, usare un account Microsoft Azure Active Directory (Azure AD) (aziendale o dell'istituto di istruzione) o un account Microsoft personale (<span>live.</span>com oppure <span>outlook.</span>com) per accedere.

![Accedi con Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Accedere all'account Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione

Dopo l'accesso, l'utente viene reindirizzato alla home page del sito Web. La home page è l'URL HTTPS specificato nelle informazioni di registrazione dell'applicazione nel portale di registrazione delle applicazioni Microsoft. La home page include un messaggio di benvenuto di tipo *"Hello \<Utente>"* , un collegamento per la disconnessione e un collegamento per la visualizzazione delle attestazioni dell'utente. Il collegamento per le attestazioni dell'utente porta al controller *Attestazioni* creato in precedenza.

### <a name="browse-to-see-the-users-claims"></a>Esplorare per visualizzare le attestazioni dell'utente

Per visualizzare le attestazioni dell'utente, selezionare il collegamento per passare alla visualizzazione dei controller disponibile solo per gli utenti autenticati.

#### <a name="view-the-claims-results"></a>Visualizzare i risultati delle attestazioni

Dopo il passaggio alla visualizzazione dei controller, dovrebbe essere visualizzata una tabella contenente le proprietà di base per l'utente:

|Proprietà |Valore |DESCRIZIONE |
|---|---|---|
|**Nome** |Nome e cognome dell'utente | Nome e cognome dell'utente.
|**Nome utente** |utente<span>@domain.com</span> | Nome utente usato per identificare l'utente.
|**Oggetto** |Oggetto |Stringa che identifica in modo univoco l'utente sul Web.|
|**ID tenant** |Guid | **GUID** che rappresenta in modo univoco l'organizzazione di Azure AD dell'utente.|

Dovrebbe essere visualizzata anche una tabella di tutte le attestazioni disponibili nella richiesta di autenticazione. Per altre informazioni, vedere l'[elenco di attestazioni disponibili in un token ID di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Eseguire test dell'accesso a un metodo con attributo Authorize (facoltativo)

Per testare l'accesso come utente anonimo a un controller protetto con l'attributo `Authorize`, eseguire la procedura seguente:

1. Selezionare il collegamento per la disconnessione dell'utente e completare il processo di disconnessione.
2. Nel browser digitare http://<span></span>localhost:{port}/claims per accedere al controller protetto con l'attributo `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Risultati previsti dopo l'accesso a un controller protetto

Viene richiesta l'autenticazione per usare la visualizzazione protetta dei controller.

## <a name="advanced-options"></a>Advanced Options

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteggere l'intero sito Web
Per proteggere l'intero sito Web, nel file **Global.asax** aggiungere l'attributo `AuthorizeAttribute` al filtro `GlobalFilters` nel metodo `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Limitare l'accesso all'applicazione

Per impostazione predefinita, quando viene compilata l'applicazione creata con la guida, questa sarà in grado di accettare accessi sia di account personali (ad esempio, outlook.com, live.com e altri) sia di account aziendali o di istituti di istruzione di proprietà di aziende od organizzazioni con Azure Active Directory integrato. Si tratta di un'opzione consigliata per le applicazioni SaaS.

Per limitare l'accesso degli utenti all'applicazione, sono disponibili diverse opzioni:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opzione 1: Limitare l'accesso all'applicazione solo agli utenti di una specifica istanza di Active Directory dell'organizzazione (tenant singolo)

Questa opzione è uno scenario comune per le *applicazioni line-of-business*: Se si vuole che l'applicazione accetti gli accessi solo da account appartenenti a una specifica istanza di Azure Active Directory (inclusi gli *account guest* dell'istanza), procedere come segue:

1. Nel file **web.config**, modificare il valore del parametro `Tenant` da `Common` con il nome del tenant dell'organizzazione, ad esempio `contoso.onmicrosoft.com`.
2. Nella [classe OWIN Startup](#configure-the-authentication-pipeline) impostare l'argomento `ValidateIssuer` su `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Opzione 2: limitare l'accesso all'applicazione ai soli utenti contenuti in un elenco specifico di organizzazioni

È possibile limitare l'accesso ai soli account utente inclusi in un'organizzazione di Azure AD che fa parte dell'elenco di organizzazioni consentite:
1. Nella [classe OWIN Startup](#configure-the-authentication-pipeline) impostare l'argomento `ValidateIssuer` su `true`.
2. Impostare il valore del parametro `ValidIssuers` sull'elenco di organizzazioni consentite.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opzione 3: Usare un metodo personalizzato per convalidare le autorità di certificazione

È possibile implementare un metodo personalizzato per convalidare le autorità di certificazione usando il parametro **IssuerValidator**. Per altre informazioni su come usare questo parametro, vedere la [classe TokenValidationParameters class](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su App Web che chiamano API Web:

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Informazioni sulla procedura per creare l'applicazione usata in questa guida introduttiva

> [!div class="nextstepaction"]
> [App Web che chiamano API Web]( https://aka.ms/msal-net-authorization-code)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Contribuire al miglioramento di Microsoft Identity Platform. Completare un breve sondaggio di due domande per condividere la propria opinione.

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)