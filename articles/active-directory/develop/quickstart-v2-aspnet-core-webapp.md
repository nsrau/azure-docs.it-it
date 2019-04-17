---
title: Guida introduttiva alle app Web ASP.NET Core di Azure AD v2.0 | Microsoft Docs
description: Informazioni su come implementare l'accesso Microsoft in un'app ASP.NET Core tramite OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/03/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dfa78177974499badc29b7e83556b6a91db7979
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005663"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Questa guida introduttiva illustra come un'app Web ASP.NET Core può consentire l'accesso ad account personali (hotmail.com, outlook.com e di altro tipo) e account aziendali e dell'istituto di istruzione da qualsiasi istanza di Azure Active Directory (Azure AD).

![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro-updated.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
> Per avviare l'applicazione della guida introduttiva sono disponibili due opzioni:
> * [Rapida] [Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuale] [Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare a [Registrazioni app nel portale di Azure](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
> 1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
> 1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
> 1. Selezionare **Nuova registrazione**.
> 1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
>    - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `AspNetCore-Quickstart`.
>    - In **URL di risposta** aggiungere `https://localhost:44321/` e selezionare **Registra**.
> 1. Selezionare il menu **Autenticazione** e quindi aggiungere le informazioni seguenti:
>    - In **URL di risposta** aggiungere `https://localhost:44321/signin-oidc` e selezionare **Registra**.
>    - Nella sezione **Impostazioni avanzate** impostare **URL disconnessione** su `https://localhost:44321/signout-oidc`.
>    - In **Concessione implicita** selezionare **Token ID**.
>    - Selezionare **Salva**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per il funzionamento dell'esempio di codice per questa guida introduttiva, è necessario aggiungere `https://localhost:44321/` e `https://localhost:44321/signin-oidc` come URL di risposta e `https://localhost:44321/signout-oidc` come URL di disconnessione e richiedere il rilascio di token ID da parte dell'endpoint di autorizzazione.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-webapp/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-your-aspnet-core-project"></a>Passaggio 2: Scaricare il progetto ASP.NET Core

- [Scaricare la soluzione di Visual Studio 2017](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Passaggio 3: Configurare il progetto di Visual Studio

1. Estrarre il file ZIP in una cartella locale all'interno della cartella radice, ad esempio **C:\Azure-Samples**.
1. Se si usa Visual Studio 2017, aprire la soluzione in Visual Studio (facoltativo).
1. Modificare il file **appsettings.json**. Trovare `ClientId` e aggiornare il valore di `ClientId` con il valore dell'**ID applicazione (client)** dell'applicazione appena registrata. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div renderon="docs"]
> Dove:
> - `Enter_the_Application_Id_here` è il valore di **ID applicazione (client)** dell'applicazione registrata nel portale di Azure. **ID applicazione (client)** si trova nella pagina **Panoramica** dell'app.
> - `Enter_the_Tenant_Info_Here` - è una delle opzioni seguenti:
>   - Se l'applicazione supporta **Account solo in questa directory organizzativa**, sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, contoso.microsoft.com)
>   - Se l'applicazione supporta **Account in qualsiasi directory organizzativa**, sostituire questo valore con `organizations`
>   - Se l'applicazione supporta **Tutti gli utenti di account Microsoft**, sostituire questo valore con `common`
>
> > [!TIP]
> > Per trovare i valori di **ID applicazione (client)**, **ID della directory (tenant)** e **Tipi di account supportati**, passare alla pagina di **panoramica** dell'app nel portale di Azure.

## <a name="more-information"></a>Altre informazioni

Questa sezione include una panoramica del codice necessario per consentire l'accesso degli utenti. La panoramica aiuta a capire il funzionamento del codice, gli argomenti principali e anche se si vuole aggiungere un sistema di accesso a un'applicazione ASP.NET esistente.

### <a name="startup-class"></a>Classe di avvio

Il middleware *Microsoft.AspNetCore.Authentication* usa una classe di avvio che viene eseguita quando si inizializza il processo di hosting:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Azure AD v2.0

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

Il metodo `AddAuthentication` configura il servizio in modo da aggiungere l'autenticazione basata su cookie (usata in scenari con browser) e impostare la richiesta su OpenID Connect. 

La riga contenente `.AddAzureAd` aggiunge l'autenticazione di Azure AD all'applicazione. Viene quindi eseguita la configurazione per l'accesso con l'endpoint di Azure AD v2.0.

> |Where  |  |
> |---------|---------|
> | ClientId  | ID applicazione (client) dell'applicazione registrata nel portale di Azure. |
> | Authority | Endpoint del servizio token di sicurezza per l'autenticazione dell'utente. In genere è <https://login.microsoftonline.com/{tenant}/v2.0> per il cloud pubblico, dove {tenant} è il nome del tenant o l'ID tenant, oppure *common* per un riferimento all'endpoint comune (usato per le applicazioni multi-tenant). |
> | TokenValidationParameters | Elenco di parametri per la convalida del token. In questo caso, `ValidateIssuer` è impostato su `false` per indicare che possono essere accettati accessi da qualsiasi account personale, aziendale o dell'istituto di istruzione. |

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteggere un controller o un metodo del controller

È possibile proteggere un controller o i metodi del controller usando l'attributo `[Authorize]`. Questo attributo limita l'accesso al controller o ai metodi ai soli utenti autenticati. La richiesta di autenticazione per l'accesso al controller può quindi essere avviata se l'utente non è autenticato.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, tra cui istruzioni su come aggiungere l'autenticazione a una nuova applicazione Web ASP.NET Core, vedere il repository GitHub per questa guida introduttiva su ASP.NET Core:

> [!div class="nextstepaction"]
> [Codice di esempio per l'app Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

