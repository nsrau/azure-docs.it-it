---
title: Guida introduttiva alle app Web ASP.NET Core di Azure AD v2.0 | Microsoft Docs
description: Informazioni su come implementare l'accesso Microsoft in un'app ASP.NET Core tramite OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: f57dc1707a9e25e4ea308142e804cdbc80d4308a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984943"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Aggiungere l'accesso con Microsoft a un'app Web ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Questa guida introduttiva contiene un codice di esempio che dimostra come un'app Web ASP.NET Core possa accedere ad account personali (hotmail.com, live.com e altri), aziendali o dell'istituto di istruzione da qualsiasi istanza di Azure Active Directory.

![Funzionamento dell'app di esempio generata da questa guida introduttiva](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Registrare l'applicazione e scaricare l'app della guida introduttiva
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registrare e configurare l'applicazione e il codice di esempio
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> 
> 1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app).
> 1. Specificare un nome per l'applicazione, assicurarsi che l'opzione **Installazione guidata** sia deselezionata e fare clic su **Crea**.
> 1. Fare clic su `Add Platform` e quindi selezionare `Web`.
> 1. Assicurarsi che l'opzione **Consenti il flusso implicito** sia *selezionata*.
> 1. In **URL di reindirizzamento** immettere `https://localhost:3110/`.
> 1. Scorrere verso il basso fino alla fine della pagina e fare clic su **Salva**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per il funzionamento dell'esempio di codice di questa guida introduttiva è necessario aggiungere un URL di risposta come `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-core-webapp/green-check.png) L'applicazione è configurata con questo attributo

#### <a name="step-2-download-your-aspnet-core-project"></a>Passaggio 2: Scaricare il progetto ASP.NET Core

- [Scaricare il progetto ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Passaggio 3: Configurare il progetto

1. Estrarre il file con estensione zip in una cartella locale, ad esempio **C:\Azure-Samples**
1. Se si usa Visual Studio 2017, aprire il progetto in Visual Studio (facoltativo)
1. Modificare **appsettings.json** e sostituire il valore di `ClientId` con l'ID dell'applicazione appena registrata:

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Se l'applicazione è un'*applicazione a singolo tenant* (destinata solo agli account della directory corrente), nel file **appsettings.json** individuare il valore di `TenantId` e sostituire `common` con l'**ID tenant** o il **nome tenant** (ad esempio contoso.microsoft.com). È possibile ottenere il nome del tenant nella **pagina Panoramica**.

## <a name="more-information"></a>Altre informazioni

Questa sezione include una panoramica del codice necessario per consentire l'accesso degli utenti. La panoramica aiuta a capire il funzionamento del codice, gli argomenti principali e anche se si vuole aggiungere un sistema di accesso a un'applicazione ASP.NET esistente.

### <a name="startup-class"></a>Classe di avvio

Il middleware *Microsoft.AspNetCore.Authentication* usa una classe di avvio che viene eseguita quando si inizializza il processo di hosting:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

Il metodo `AddAuthentication` configura il servizio in modo che venga aggiunta l'autenticazione basata su cookie (usata in scenari del browser) e venga impostata la richiesta di verifica per OpenIdConnect. 

La riga contenente `.AddAzureAd` aggiunge l'autenticazione di Azure Active Directory all'applicazione.

Il file **AzureAdAuthenticationBuilderExtensions.cs** aggiunge invece alla pipeline di autenticazione di Azure AD un metodo di estensione che consente di configurare gli attributi necessari per l'autenticazione di Azure AD. Il metodo `Configure` nell'interfaccia `IConfigureNamedOptions` contiene quanto segue:

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Where  |  |
> |---------|---------|
> |ClientId     |ID dell'applicazione registrata nel portale di Azure|
> |Authority | Endpoint del servizio token di sicurezza per l'utente da autenticare. In genere https://login.microsoftonline.com/{tenant}/v2.0 per il cloud pubblico, dove {tenant} è il nome del tenant, l'ID del tenant, oppure *common* per un riferimento all'endpoint comune (usato per le applicazioni multi-tenant)|
> |UseTokenLifetime |Indica che il cookie di autenticazione deve corrispondere a quello del token di autenticazione|
> |RequireHttpsMetadata     |Indica la necessità del protocollo HTTPS per l'indirizzo dei metadati o l'autorità. È consigliabile modificare questo valore in `True`|
> |TokenValidationParameters     | Elenco di parametri per la convalida del token. In questo caso `ValidateIssuer` è impostato su `false` per indicare che può accettare accessi provenienti da qualsiasi account personale, aziendale o dell'istituto di istruzione|

### <a name="initiate-an-authentication-challenge"></a>Avviare una richiesta di autenticazione

È possibile indurre un utente a eseguire l'accesso inserendo una richiesta di autenticazione nel controller, proprio come in **AccountController.cs**:

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> L'aggiunta di una richiesta di autenticazione con questo metodo è facoltativa e viene usata in genere per consentire sia agli utenti autenticati sia a quelli non autenticati di accedere a una visualizzazione. È possibile proteggere i controller usando il metodo descritto nella sezione successiva.

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteggere un controller o un metodo del controller

È possibile proteggere un controller o un metodo del controller usando l'attributo `[Authorize]`. Questo attributo limita l'accesso al controller o ai metodi del controller ai soli utenti autenticati. Questo significa che, se un utente non è autenticato, è possibile avviare la richiesta di autenticazione per l'accesso al controller.

## <a name="next-steps"></a>Passaggi successivi

Consultare il repository GitHub relativo a questa guida introduttiva su ASP.NET Core per altre informazioni, incluse istruzioni su come aggiungere l'autenticazione a una nuova applicazione Web ASP.NET Core:

> [!div class="nextstepaction"]
> [Codice di esempio per l'app Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]