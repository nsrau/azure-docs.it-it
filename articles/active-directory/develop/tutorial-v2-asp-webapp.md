---
title: Introduzione al server Web ASP.NET per Azure AD v2.0 | Microsoft Docs
description: Implementazione delle informazioni di accesso Microsoft in una soluzione ASP.NET con un'applicazione tradizionale basata su Web browser e lo standard OpenID Connect
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
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1ce7197a195b37787d1a43a44efd8438154e9f9
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113639"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Aggiungere le informazioni di accesso a Microsoft in un'app Web ASP.NET

Questa guida illustra come implementare le informazioni di accesso a Microsoft usando una soluzione MVC ASP.NET con un'applicazione tradizionale basata su Web browser e OpenID Connect.

Al termine di questa guida, l'applicazione potrà accettare accessi di account personali, come quelli di outlook.com e live.com. L'accesso all'app sarà consentito anche ad account aziendali o dell'istituto di istruzione di qualsiasi organizzazione o azienda che abbia eseguito l'integrazione con Azure Active Directory (Azure AD).

> Questa guida richiede Microsoft Visual Studio 2019.  Se non lo si ha, è possibile  [Scaricare gratuitamente Visual Studio 2019](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funzionamento dell'app di esempio generata da questa guida

![Illustra come funziona l'app di esempio generata da questa esercitazione](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

L'applicazione di esempio creata è basata su uno scenario in cui si usa il browser per accedere a un sito Web ASP.NET che richiede all'utente di eseguire l'autenticazione tramite un pulsante di accesso. In questo scenario, la maggior parte delle operazioni necessarie per il rendering della pagina Web viene eseguita sul lato server.

## <a name="libraries"></a>Librerie

Questa guida usa le librerie seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware che consente a un'applicazione di usare OpenID Connect per l'autenticazione|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware che consente a un'applicazione di mantenere una sessione utente usando i cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware che consente di eseguire applicazioni basate su OWIN in Internet Information Services (IIS) tramite la pipeline delle richieste ASP.NET|

## <a name="set-up-your-project"></a>Configurare il progetto

Questa sezione illustra l'installazione e la configurazione della pipeline di autenticazione tramite middleware OWIN in un progetto ASP.NET con OpenID Connect.

> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, [Scaricare un progetto](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) e passare a [Registrare l'applicazione](#register-your-application) per configurare il codice di esempio prima di eseguirlo.

### <a name="create-your-aspnet-project"></a>Creare un progetto ASP.NET

1. In Visual Studio: Passare a **File** > **Nuovo** > **Progetto**.
2. In **Visual C#\Web** selezionare **Applicazione Web ASP.NET (.NET Framework)** .
3. Assegnare un nome all'applicazione e selezionare **OK**.
4. Selezionare **Vuoto** e quindi la casella di controllo per aggiungere riferimenti **MVC**.

## <a name="add-authentication-components"></a>Aggiungere i componenti per l'autenticazione

1. In Visual Studio: Passare a **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.
2. Aggiungere *pacchetti NuGet del middleware OWIN* digitando quanto segue nella finestra della Console di Gestione pacchetti:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Informazioni sulle librerie
> Queste librerie abilitano l'accesso Single Sign-On (SSO) usando OpenID Connect tramite l'autenticazione basata su cookie. Al termine dell'autenticazione e dopo l'invio del token che rappresenta l'utente all'applicazione, il middleware OWIN crea un cookie di sessione. Il browser usa quindi questo cookie nelle richieste successive, in modo che l'utente non debba digitare di nuovo la password e non siano necessarie operazioni di verifica aggiuntive.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurare la pipeline di autenticazione

La procedura seguente consente di creare una classe di avvio del middleware OWIN per configurare l'autenticazione OpenID Connect. Questa classe viene eseguita automaticamente all'avvio del processo di IIS.

> [!TIP]
> Se il progetto non include alcun file `Startup.cs` nella cartella radice:
> 1. Fare clic con il pulsante destro del mouse sulla cartella radice del progetto e selezionare **Aggiungi** > **Nuovo elemento** > **Classe di avvio di OWIN**.<br/>
> 2. Assegnare alla classe il nome **Startup.cs**.
>
>> Assicurarsi che la classe selezionata sia una classe di avvio di OWIN e non una classe C# standard. Per confermarlo, verificare che venga visualizzato [assembly: OwinStartup(typeof({NameSpace}.Startup))] sopra lo spazio dei nomi.

1. Aggiungere i riferimenti *OWIN* e *Microsoft.IdentityModel* a Startup.cs:

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
> L'impostazione di `ValidateIssuer = false` è una semplificazione per questo avvio rapido. Nelle applicazioni reali è necessario convalidare l'autorità di certificazione.
> Per informazioni su come eseguire questa operazione, vedere gli esempi.

<!--start-collapse-->
> ### <a name="more-information"></a>Altre informazioni
> I parametri forniti in *OpenIDConnectAuthenticationOptions* fungeranno da coordinate per consentire all'applicazione di comunicare con Azure AD. Dal momento che il middleware OpenID Connect usa i cookie in background, è anche necessario configurare l'autenticazione dei cookie, come illustrato nel codice precedente. Il valore *ValidateIssuer* indica a OpenIdConnect di non limitare l'accesso a un'organizzazione specifica.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Aggiungere un controller per gestire le richieste di accesso e disconnessione

Per creare un nuovo controller per esporre i metodi di accesso e disconnessione, seguire questa procedura:

1.  Fare clic con il pulsante destro del mouse sulla cartella **Controller** e quindi selezionare **Aggiungi** > **Controller**.
2.  Selezionare **Controller MVC (versione .NET) - Vuoto**.
3.  Selezionare **Aggiungi**.
4.  Assegnare al controller il nome **HomeController** e quindi selezionare **Aggiungi**.
5.  Aggiungere i riferimenti OWIN alla classe:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Aggiungere al controller i due metodi seguenti per gestire l'accesso e la disconnessione avviando una richiesta di autenticazione:

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Creare la home page dell'app per l'accesso degli utenti

In Visual Studio creare una nuova visualizzazione per aggiungere il pulsante di accesso e visualizzare le informazioni relative all'utente dopo l'autenticazione:

1.  Fare clic con il pulsante destro del mouse sulla cartella **Views\Home** e scegliere **Aggiungi visualizzazione**.
2.  Assegnare alla nuova visualizzazione il nome **Indice**.
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
> Questa pagina aggiunge un pulsante di accesso in formato SVG con sfondo nero:<br/>![Accedi con Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Per altri pulsanti di accesso, vedere le [Linee guida sulla personalizzazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Linee guida sulla personalizzazione").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Aggiungere un controller per visualizzare le attestazioni dell'utente
Questo controller illustra gli usi dell'attributo `[Authorize]` per la protezione di un controller. Questo attributo limita l'accesso al controller ai soli utenti autenticati. Il codice seguente usa l'attributo per visualizzare le attestazioni utente recuperate durante l'accesso:

1.  Fare clic con il pulsante destro del mouse sulla cartella **Controller** e quindi selezionare **Aggiungi** > **Controller**.
2.  Selezionare **Controller MVC {versione} - Vuoto**.
3.  Selezionare **Aggiungi**.
4.  Denominarlo **ClaimsController**.
5.  Sostituire il codice della classe controller con il codice seguente. L'attributo `[Authorize]` verrà aggiunto alla classe:

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
> A causa dell'uso dell'attributo `[Authorize]`, tutti i metodi di questo controller possono essere eseguiti solo se l'utente è autenticato. Se un utente non autenticato prova ad accedere al controller, OWIN avvia una richiesta di autenticazione e impone all'utente di eseguire l'autenticazione. Il codice precedente cerca nell'elenco di attestazioni attributi utente specifici inclusi nel token ID dell'utente. Tali attributi includono nome e cognome, nome utente e soggetto ID utente globale dell'utente. Contengono anche l'*ID tenant*, che rappresenta l'ID dell'organizzazione dell'utente. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Creare una visualizzazione per mostrare le attestazioni dell'utente

In Visual Studio creare una nuova visualizzazione per mostrare le attestazioni dell'utente in una pagina Web:

1.  Fare clic con il pulsante destro del mouse sulla cartella **Views\Claims** e quindi selezionare **Aggiungi visualizzazione**.
2.  Assegnare alla nuova visualizzazione il nome **Indice**.
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

Per registrare rapidamente l'applicazione, seguire questa procedura:

1. Passare al nuovo riquadro [Portale di Azure - Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Immettere un nome per l'applicazione e fare clic su **Registra**.
1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un solo clic.

### <a name="option-2-advanced-mode"></a>Opzione 2: modalità Avanzata

Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:

1. Aprire Visual Studio e quindi eseguire le operazioni seguenti:
   1. In Esplora soluzioni selezionare il progetto e controllare la finestra Proprietà (se la finestra Proprietà non è visualizzata, premere F4).
   1. Impostare il valore di SSL abilitato su `True`.
   1. Fare clic con il pulsante destro del mouse sul progetto in Visual Studio, selezionare **Proprietà** e quindi la scheda **Web**. Nella sezione **Server** modificare il valore dell'impostazione **URL progetto** specificando quello dell'**URL SSL**.
   1. Copiare l'URL SSL. Aggiungerlo all'elenco di URL di reindirizzamento nell'elenco corrispondente del portale di registrazione nel passaggio successivo.<br/><br/>![Proprietà del progetto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
   1. Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio **ASPNET-Tutorial**.
   1. Aggiungere l'URL SSL copiato da Visual Studio nel passaggio 1 (ad esempio `https://localhost:44368/`) in **URL di risposta** e selezionare **Registra**.
1. Selezionare il menu **Autenticazione**, quindi **Token ID** in **Concessione implicita** e infine **Salva**.
1. Aggiungere il codice seguente nella sezione `configuration\appSettings` del file web.config, disponibile nella cartella radice:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Sostituire `ClientId` con l'ID applicazione appena registrato.
1. Sostituire `redirectUri` con l'URL SSL del progetto.

## <a name="test-your-code"></a>Testare il codice

Per testare l'applicazione in Visual Studio, premere F5 per eseguire il progetto. Il browser viene aperto alla pagina http://<span></span>localhost:{porta} e viene visualizzato il pulsante **Accedi con Microsoft**. Selezionare il pulsante per avviare il processo di accesso.

Quando si è pronti per eseguire il test, usare un account Azure AD (aziendale o dell'istituto di istruzione) o un account Microsoft personale (<span>live.</span>com oppure <span>outlook.</span>com) per accedere.

![Accedi con Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Accedere all'account Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Autorizzazioni e consenso nell'endpoint di Microsoft Identity Platform
>  Le applicazioni che si integrano con Microsoft Identity Platform seguono un modello di autorizzazione che offre a utenti e amministratori il controllo sulle modalità di accesso ai dati. Quando un utente esegue l'autenticazione con Azure AD per accedere a questa applicazione, viene richiesto di fornire il consenso alle autorizzazioni richieste dall'applicazione ("Visualizza il profilo di base personale" e "Conservazione dell'accesso ai dati per cui è stato autorizzato l'accesso"). Dopo aver accettato queste autorizzazioni, l'utente potrà passare ai risultati dell'applicazione. È tuttavia possibile che all'utente venga visualizzata la pagina **Need admin consent** (Consenso amministratore richiesto) se si verifica una delle condizioni seguenti:
>  > - Lo sviluppatore dell'applicazione inserisce autorizzazioni aggiuntive che richiedono il **consenso dell'amministratore**.
>  > - La configurazione del tenant (accessibile in **Applicazioni aziendali -> Impostazioni utente**) è tale che gli utenti non possono concedere alle app il consenso per accedere ai dati aziendali per proprio conto.
>
> Per altre informazioni, vedere [Autorizzazioni e consenso nell'endpoint di Microsoft Identity Platform](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent).
<!--end-collapse-->

#### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione

Dopo l'accesso, l'utente viene reindirizzato alla home page del sito Web. La home page è l'URL HTTPS specificato nelle informazioni di registrazione dell'applicazione nel portale di registrazione delle applicazioni Microsoft. La home page include un messaggio di benvenuto di tipo *"Salve \<utente>"* , un collegamento per la disconnessione e un collegamento per la visualizzazione delle attestazioni dell'utente. Il collegamento per le attestazioni dell'utente consente di connettersi al controller Attestazioni creato in precedenza.

### <a name="view-the-users-claims"></a>Visualizzare le attestazioni dell'utente

Per visualizzare le attestazioni dell'utente, selezionare il collegamento per passare alla visualizzazione dei controller disponibile solo per gli utenti autenticati.

#### <a name="view-the-claims-results"></a>Visualizzare i risultati delle attestazioni

Dopo il passaggio alla visualizzazione dei controller, dovrebbe essere visualizzata una tabella contenente le proprietà di base per l'utente:

|Proprietà |Valore |DESCRIZIONE |
|---|---|---|
|**Nome** |Nome e cognome dell'utente | Nome e cognome dell'utente
|**Nome utente** |utente<span>@domain.com</span> | Nome utente usato per identificare l'utente|
|**Oggetto** |Oggetto |Stringa che identifica in modo univoco l'utente nel Web|
|**ID tenant** |Guid | **GUID** che rappresenta in modo univoco l'organizzazione di Azure AD dell'utente.|

Dovrebbe anche essere visualizzata anche una tabella di tutte le attestazioni disponibili nella richiesta di autenticazione. Per altre informazioni, vedere l'[elenco di attestazioni disponibili in un token ID di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

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

Per impostazione predefinita, quando viene compilata l'applicazione creata con la guida, questa sarà in grado di accettare accessi sia di account personali (ad esempio, outlook.com, live.com e altri) sia di account aziendali o di istituti di istruzione di proprietà di aziende o di organizzazioni con Azure AD integrato. Si tratta di un'opzione consigliata per le applicazioni SaaS.

Per limitare l'accesso degli utenti all'applicazione, sono disponibili diverse opzioni.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opzione 1: Limitare l'accesso all'applicazione solo agli utenti di una specifica istanza di Active Directory dell'organizzazione (tenant singolo)

Questa opzione viene usata di frequente per le *applicazioni line-of-business*: Se si vuole che l'applicazione accetti gli accessi solo da account appartenenti a una specifica istanza di Azure AD (inclusi gli *account Guest*dell'istanza), procedere come segue:

1. Nel file web.config modificare il valore del parametro `Tenant` sostituendo `Common` con il nome del tenant dell'organizzazione, ad esempio `contoso.onmicrosoft.com`.
2. Nella [classe OWIN Startup](#configure-the-authentication-pipeline) impostare l'argomento `ValidateIssuer` su `true`.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Opzione 2: Limitare l'accesso ai soli utenti inclusi in un elenco specifico di organizzazioni

È possibile limitare l'accesso solo agli account utente inclusi in un'organizzazione di Azure AD che fa parte dell'elenco di organizzazioni consentite:
1. Nella [classe OWIN Startup](#configure-the-authentication-pipeline) impostare l'argomento `ValidateIssuer` su `true`.
2. Impostare il valore del parametro `ValidIssuers` sull'elenco di organizzazioni consentite.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opzione 3: Usare un metodo personalizzato per convalidare le autorità di certificazione

È possibile implementare un metodo personalizzato per convalidare le autorità di certificazione usando il parametro **IssuerValidator**. Per altre informazioni su come usare questo parametro, vedere la [classe TokenValidationParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla modalità di chiamata delle API Web nelle app Web.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Informazioni su come creare l'applicazione usata in questa guida di avvio rapido

Altre informazioni su app Web che chiamano API Web con Microsoft Identity Platform:

> [!div class="nextstepaction"]
> [App Web che chiamano API Web](scenario-web-app-sign-user-overview.md)

Informazioni su come creare API Web che chiamano Microsoft Graph:

> [!div class="nextstepaction"]
> [Esercitazione su ASP.NET con Microsoft Graph](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Contribuire al miglioramento di Microsoft Identity Platform. Completare un sondaggio di due domande per condividere la propria opinione:

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
