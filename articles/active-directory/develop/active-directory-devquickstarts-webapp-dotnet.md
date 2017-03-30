---
title: Introduzione alle app Web .NET per Azure AD | Microsoft Docs
description: Compilare un&quot;app Web .NET MVC che si integra con Azure AD per l&quot;accesso.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 43ba592b6294a9a75a20dacd81953a77c241b89f
ms.lasthandoff: 03/18/2017


---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>Accesso e disconnessione all'app Web ASP.NET con Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Con poche righe di codice uniche sia per l'accesso che per la disconnessione, Azure Active Directory (Azure AD) facilita e semplifica l'outsourcing della gestione delle identità delle app Web. È possibile eseguire l'accesso e la disconnessione degli utenti nelle app Web ASP.NET tramite l'implementazione Microsoft di Open Web Interface per middleware .NET (OWIN). Il middleware OWIN gestito dalla community è incluso in .NET Framework 4.5. Questo articolo mostra come usare OWIN per:

* Far accedere gli utenti alle app Web usando Azure AD come provider di identità.
* Visualizzare alcune informazioni sugli utenti.
* Far disconnettere gli utenti dalle app.

## <a name="before-you-get-started"></a>Prima di iniziare
* Scaricare la [struttura dell'app](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) oppure l'[esempio completato](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).
* È necessario disporre anche di un tenant di Azure AD in cui registrare l'app. Se non si ha già un tenant di Azure AD, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

Quando si è pronti, attenersi alle procedure descritte nelle quattro sezioni seguenti.

## <a name="step-1-register-the-new-app-with-azure-ad"></a>Passaggio 1: registrare la nuova app con Azure AD
Per configurare l'app per l'autenticazione degli utenti, registrarla innanzitutto nel tenant eseguendo queste operazioni:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra superiore fare clic sul nome dell'account. Nell'elenco **Directory** selezionare il tenant di Active Directory in cui si vuole registrare l'app.
3. Fare clic su **More Services** (Altri servizi) nel riquadro a sinistra e scegliere **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e scegliere **Aggiungi**.
5. Seguire le istruzioni e creare un'**applicazione Web e/o API Web**.
  * Il **nome** descrive l'app agli utenti.
  * L'**URL accesso** è l'URL di base dell'app. Il valore predefinito della struttura è https://localhost:44320/.
6. Dopo aver completato la registrazione, Azure AD assegna automaticamente all'app un ID applicazione univoco. Copiare il valore dalla pagina dell'app per usarlo nelle sezioni successive.
7. Dalla pagina **Impostazioni** -> **Proprietà** dell'applicazione aggiornare l'URI dell'ID app. L'**URI ID app** è un identificatore univoco dell'app. La convenzione di denominazione è `https://<tenant-domain>/<app-name>`, ad esempio `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Passaggio 2: configurare l'app per l'uso della pipeline di autenticazione OWIN
In questo passaggio, verrà configurato il middleware OWIN per l'uso del protocollo di autenticazione OpenID Connect. OWIN verrà usato, tra le altre cose, per inviare le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente.

1. Per iniziare, aggiungere i pacchetti NuGet del middleware OWIN al progetto usando la Console di Gestione pacchetti.

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. Per aggiungere al progetto una OWIN Startup Class denominata `Startup.cs`, fare clic con il pulsante destro del mouse sul progetto, selezionare **Aggiungi** --> **Nuovo elemento** e quindi cercare **OWIN**. OWIN richiama il metodo **Configuration(...)** all'avvio dell'app.
3. Modificare la dichiarazione di classe in `public partial class Startup`. Parte di questa classe è stata già implementata in un altro file. Nel metodo **Configuration(...)** effettuare una chiamata a **ConfgureAuth(...)** per configurare l'autenticazione per l'app.  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. Aprire il file App_Start\Startup.Auth.cs e quindi implementare il metodo **ConfigureAuth(...)**. I parametri forniti in *OpenIDConnectAuthenticationOptions* fungeranno da coordinate per consentire all'app di comunicare con Azure AD. È inoltre necessario impostare l'autenticazione tramite cookie poiché il middleware OpenID Connect usa i cookie in background.

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
             });
     }
     ```

5. Infine aprire il file web.config nella radice del progetto e immettere i valori di configurazione nella sezione `<appSettings>`.
  * `ida:ClientId`: il GUID copiato dal Portale di Azure in "Passaggio 1: registrare la nuova applicazione con Azure AD."
  * `ida:Tenant`: il nome del tenant di Azure AD, ad esempio contoso.onmicrosoft.com.
  * `ida:PostLogoutRedirectUri`: indica ad Azure AD dove reindirizzare un utente dopo il completamento di una richiesta di disconnessione.

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Passaggio 3: usare OWIN per inviare le richieste di accesso e disconnessione ad Azure AD
L'app ora è configurata correttamente per comunicare con Azure AD mediante il protocollo di autenticazione OpenID Connect. OWIN ha gestito tutti i dettagli relativi alla creazione dei messaggi di autenticazione, alla convalida dei token da Azure AD e alla gestione delle sessioni utente. Non resta che dare agli utenti un modo per accedere e disconnettersi.

1. È possibile usare tag di autorizzazione nei controller per obbligare l'utente ad accedere prima di aprire determinate pagine. A tale scopo, aprire Controllers\HomeController.cs e quindi aggiungere il tag `[Authorize]` al controller About.

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. È possibile usare OWIN anche per inviare le richieste di autenticazione direttamente dal codice. A questo fine, aprire Controllers\AccountController.cs. Poi, nelle azioni SignIn() e SignOut() inoltrare le richieste di verifica e di disconnessione di OpenID Connect.

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. Aprire Views\Shared\_LoginPartial.cshtml per indicare all'utente i collegamenti di accesso e disconnessione dall'app e per stampare il nome dell'utente in una vista.

    ```HTML
    @if (Request.IsAuthenticated)
    {
     <text>
         <ul class="nav navbar-nav navbar-right">
             <li class="navbar-text">
                 Hello, @User.Identity.Name!
             </li>
             <li>
                 @Html.ActionLink("Sign out", "SignOut", "Account")
             </li>
         </ul>
     </text>
    }
    else
    {
     <ul class="nav navbar-nav navbar-right">
         <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
     </ul>
    }
    ```

## <a name="step-4-display-user-information"></a>Passaggio 4: visualizzare le informazioni utente
Quando si autenticano gli utenti con OpenID Connect, Azure AD restituisce un id_token all'app contenente attestazioni, ovvero asserzioni sull'utente. È possibile usare tali attestazioni per personalizzare l'app eseguendo le operazioni seguenti:

1. Aprire il file Controllers\HomeController.cs. È possibile accedere alle attestazioni dell'utente nei controller tramite l'oggetto di entità di sicurezza `ClaimsPrincipal.Current` .

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. Compilare ed eseguire l'app. Se non si è ancora creato un nuovo utente nel tenant con un dominio .onmicrosoft.com, ora è possibile farlo. Ecco come:

  a. Accedere con tale utente e notare che l'identità dell'utente è visualizzata nella barra di superiore.

  b. Disconnettersi e accedere nuovamente come un altro utente nel tenant.

  c. Se lo si desidera, per vedere Single Sign-On in azione, registrare ed eseguire un'altra istanza di questa app (con il suo clientId).

## <a name="next-steps"></a>Passaggi successivi
Per riferimento, consultare l'[esempio completato](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip), che non contiene i valori di configurazione immessi durante la procedura sopra indicata.

Ora è possibile passare ad argomenti più avanzati. Provare ad esempio [Proteggere un'API Web con Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

