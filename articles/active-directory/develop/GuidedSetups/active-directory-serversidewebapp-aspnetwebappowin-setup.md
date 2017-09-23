---
title: Introduzione al server Web ASP.NET per Azure AD v2 - Installazione | Microsoft Docs
description: Implementazione di accessi Microsoft in una soluzione ASP.NET con un'applicazione tradizionale basata su Web browser tramite lo standard OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ebf54f5a203adb7f0e5b0c47dcc07595e269e218
ms.contentlocale: it-it

---

## <a name="set-up-your-project"></a>Configurare il progetto

Questa sezione illustra la procedura per l'installazione e la configurazione della pipeline di autenticazione tramite middleware OWIN in un progetto ASP.NET mediante OpenID Connect. 

> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, [Scaricare un progetto](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) e passare direttamente al [passaggio di configurazione](#create-an-application-express) per configurare il codice di esempio prima di eseguirlo.

<!--start-collapse-->
> ### <a name="create-your-aspnet-project"></a>Creare un progetto ASP.NET

> 1. In Visual Studio: `File` > `New` > `Project`<br/>
> 2. In *Visual C#\Web* selezionare `ASP.NET Web Application (.NET Framework)`
> 3. Assegnare un nome all'applicazione e fare clic su *OK*
> 4. Selezionare `Empty` e quindi selezionare la casella di controllo per aggiungere i riferimenti `MVC`
<!--end-collapse-->

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

>Le librerie precedenti abilitano l'accesso Single Sign-On (SSO) usando OpenID Connect tramite l'autenticazione basata su cookie. Al termine dell'autenticazione e dopo l'invio del token che rappresenta l'utente all'applicazione, il middleware OWIN crea un cookie di sessione. Il browser usa quindi questo cookie nelle richieste successive, in modo che l'utente non debba digitare di nuovo la password, e non sono necessarie operazioni di verifica aggiuntive.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurare la pipeline di autenticazione
La procedura seguente consente di creare una classe di avvio del middleware OWIN per configurare l'autenticazione OpenID Connect. Questa classe verrà eseguita automaticamente all'avvio del processo di IIS.

> Se il progetto non include alcun file `Startup.cs` nella cartella radice:<br/>
> 1. Fare clic con il pulsante destro del mouse sulla cartella radice del progetto: > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Assegnare il nome `Startup.cs`

> Assicurarsi che la classe selezionata sia una classe di avvio di OWIN e non una classe C# standard. Per confermarlo, verificare se `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` viene visualizzato sopra lo spazio dei nomi.


1. Aggiungere i riferimenti *OWIN* e *Microsoft.IdentityModel* a `Startup.cs`:

```csharp
using Microsoft.Owin;
using Owin;
using Microsoft.IdentityModel.Protocols;
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
using Microsoft.Owin.Security.Notifications;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Sostituire la classe di avvio con il codice seguente:
</li>
</ol>

```csharp
public class Startup
{        
    // The Client ID is used by the application to uniquely identify itself to Azure AD.
    string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

    // RedirectUri is the URL where the user will be redirected to after they sign in.
    string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

    // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
    static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

    // Authority is the URL for authority, composed by Azure Active Directory v2 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
                Scope = OpenIdConnectScopes.OpenIdProfile,
                // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                ResponseType = OpenIdConnectResponseTypes.IdToken,
                // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters() { ValidateIssuer = false },
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
<!--start-collapse-->
> ### <a name="more-information"></a>Altre informazioni

> I parametri forniti in *OpenIDConnectAuthenticationOptions* fungeranno da coordinate per consentire all'applicazione di comunicare con Azure AD. Poiché il middleware OpenID Connect usa i cookie in background, è anche necessario configurare l'autenticazione dei cookie, come illustrato nel codice precedente. Il valore *ValidateIssuer* segnala a OpenIdConnect di non limitare l'accesso a un'organizzazione specifica.
<!--end-collapse-->


