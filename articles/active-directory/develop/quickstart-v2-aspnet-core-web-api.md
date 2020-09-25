---
title: "Avvio rapido: Proteggere un'API Web ASP.NET Core con Microsoft Identity Platform | Azure"
titleSuffix: Microsoft identity platform
description: In questa guida di avvio rapido verrà scaricato e modificato un esempio di codice che illustra come proteggere un'API Web ASP.NET Core usando Microsoft Identity Platform per l'autorizzazione.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: dc0cdca2355403bc8f5409d9a6ca7f4ae89caf25
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944453"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-microsoft-identity-platform"></a>Avvio rapido: Proteggere un'API Web ASP.NET Core con Microsoft Identity Platform

In questa guida di avvio rapido viene usato un esempio di codice per apprendere come proteggere un'API Web ASP.NET Core in modo che sia accessibile solo da account autorizzati. Gli account possono essere account personali (hotmail.com, outlook.com e altri) e aziendali o dell'istituto di istruzione in una qualsiasi istanza di Azure Active Directory (Azure AD).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Prerequisiti
>
> - Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Tenant di Azure Active Directory](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) o [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Passaggio 1: Registrare l'applicazione
>
> Registrare prima di tutto l'API Web nel tenant di Azure AD e aggiungere un ambito seguendo questa procedura:
>
> 1. Accedere al [portale di Azure](https://portal.azure.com).
> 1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** e quindi **Nuova registrazione**.
> 1. Immettere un **Nome** per l'applicazione, ad esempio `AspNetCoreWebApi-Quickstart`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
> 1. Selezionare **Registra**.
> 1. In **Gestisci** selezionare **Esponi un'API**.
> 1. Selezionare **Aggiungi un ambito** e quindi selezionare **Salva e continua** per accettare l'**URI dell'ID applicazione** predefinito.
> 1. Nel riquadro **Aggiungi un ambito** immettere i valori seguenti:
>    - **Nome ambito**: `access_as_user`
>    - **Utenti che possono fornire il consenso**: **Amministratori e utenti**
>    - **Nome visualizzato per il consenso amministratore**: `Access AspNetCoreWebApi-Quickstart`
>    - **Descrizione del consenso amministratore**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Nome visualizzato per il consenso utente**: `Access AspNetCoreWebApi-Quickstart`
>    - **Descrizione del consenso utente**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Stato**: **Enabled**
> 1. Selezionare **Aggiungi ambito** per completare l'aggiunta dell'ambito.

## <a name="step-2-download-the-aspnet-core-project"></a>Passaggio 2: Scaricare il progetto ASP.NET Core

> [!div renderon="docs"]
> [Scaricare la soluzione ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) da GitHub.

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Passaggio 3: Configurare il progetto ASP.NET Core
>
> In questo passaggio, configurare il codice di esempio per l'uso della registrazione dell'app creata in precedenza.
>
> 1. Estrarre l'archivio ZIP in una cartella vicina alla radice dell'unità. Ad esempio, in *C:\Azure-Samples*.
> 1. Aprire la soluzione nella cartella *webapi* nell'editor del codice.
> 1. Aprire il file *appsettings.json* e modificare quanto segue:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Sostituire `Enter_the_Application_Id_here` con il valore di **ID applicazione (client)** dell'applicazione registrata nel portale di Azure. **ID applicazione (client)** si trova nella pagina **Panoramica** dell'app.
>    - Sostituire `Enter_the_Tenant_Info_Here` con uno dei seguenti elementi:
>       - Se l'applicazione supporta **Account solo in questa directory dell'organizzazione** sostituire questo valore con l'**ID della directory (tenant)** (un GUID) o il **nome del tenant** (ad esempio, `contoso.onmicrosoft.com`). È possibile trovare l'**ID della directory (tenant)** nella pagina **Panoramica** dell'app.
>       - Se l'applicazione supporta **Account in qualsiasi directory organizzativa**, sostituire questo valore con `organizations`
>       - Se l'applicazione supporta **Tutti gli utenti di account Microsoft**, lasciare il valore `common`
>
> Per questa guida di avvio rapido, non modificare altri valori nel file *appsettings.json*.

## <a name="how-the-sample-works"></a>Funzionamento dell'esempio

L'API Web riceve un token da un'applicazione client e il codice nell'API Web convalida il token. Questo scenario viene illustrato più dettagliatamente in [Scenario: API Web protetta](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Classe di avvio

Il middleware *Microsoft.AspNetCore.Authentication* usa una classe `Startup` che viene eseguita quando si inizializza il processo di hosting. Nel metodo `ConfigureServices` viene chiamato il metodo di estensione `AddMicrosoftIdentityWebApi` fornito da *Microsoft.Identity.Web*.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

Il metodo `AddAuthentication()` configura il servizio per l'aggiunta dell'autenticazione basata su JwtBearer.

La riga contenente `.AddMicrosoftIdentityWebApi` aggiunge l'autorizzazione di Microsoft Identity Platform all'API Web. Viene quindi configurato per convalidare i token di accesso emessi dall'endpoint di Microsoft Identity Platform, in base alle informazioni contenute nella sezione `AzureAD` del file di configurazione *appsettings.json*:

| Chiave *appsettings.json* | Descrizione                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **ID applicazione (client)** dell'applicazione registrata nel portale di Azure.                                                                                       |
| `Instance`             | Endpoint del servizio token di sicurezza (STS) per l'autenticazione dell'utente. Questo valore è in genere `https://login.microsoftonline.com/`, che indica il cloud pubblico di Azure. |
| `TenantId`             | Nome del tenant o dell'ID tenant (GUID) oppure *common* per l'accesso agli utenti con account aziendali o dell'istituto di istruzione o account personali Microsoft.                             |

Il metodo `Configure()` contiene due metodi importanti, `app.UseAuthentication()` e `app.UseAuthorization()`, che abilitano la funzionalità denominata:

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-a-controllers-method-or-a-razor-page"></a>Proteggere un controller, un metodo del controller o una pagina Razor

È possibile proteggere un controller o i metodi del controller usando l'attributo `[Authorize]`. Questo attributo limita l'accesso al controller o ai metodi ai soli utenti autenticati. La richiesta di autenticazione per l'accesso al controller può quindi essere avviata se l'utente non è autenticato.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validate-the-scope-in-the-controller"></a>Convalidare l'ambito nel controller

Il codice nell'API verifica quindi che gli ambiti necessari siano presenti nel token usando `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);`

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The Web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Il repository GitHub che contiene questo esempio di codice dell'API Web ASP.NET Core include istruzioni e altri esempi di codice che illustrano come:

- Aggiungere l'autenticazione a una nuova API Web ASP.NET Core
- Chiamare l'API Web da un'applicazione desktop
- Chiamare API downstream come Microsoft Graph e altre API Microsoft

> [!div class="nextstepaction"]
> [Esercitazioni sulle API Web ASP.NET Core su GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
