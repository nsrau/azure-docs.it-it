---
title: Introduzione alle API Web .NET per Azure AD | Microsoft Docs
description: Come compilare un'API Web MVC per Node.js che si integra con Azure AD per l'autenticazione e l'autorizzazione.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: f44d75f45073a5d9aa9b1863ed227aba4efcf785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="help-protect-a-web-api-by-using-bearer-tokens-from-azure-ad"></a>Proteggere un'API Web usando token di connessione di Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Se si compila un'applicazione che fornisce l'accesso alle risorse protette, è necessario sapere come prevenire l'accesso non protetto a tali risorse.
Azure Active Directory (Azure AD) rende semplici e dirette le operazioni per la protezione di un'API Web usando i token di connessione dell'accesso di OAuth 2.0 con solo poche righe di codice.

Nelle app Web Asp.NET, a questo scopo si usa l'implementazione di Microsoft del middleware OWIN gestito dalla community e incluso in .NET Framework 4.5. Qui OWIN verrà usato per creare un'API Web "Elenco attività" in grado di:

* Designare le API protette.
* Verificare che le chiamate all'API Web contengano un token di accesso valido.

Per compilare l'API "To Do List", è innanzitutto necessario:

1. Registrare un'applicazione con Azure AD.
2. Configurare l'app per l'uso della pipeline di autenticazione OWIN.
3. Configurare un'applicazione client per chiamare l'API Web.

Per iniziare, [scaricare la struttura dell'app](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Ognuno è una soluzione di Visual Studio 2013. È necessario anche un tenant di Azure AD in cui registrare l'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Passaggio 1: Registrare un'applicazione con Azure AD
Per proteggere l'applicazione, si dovrà per prima cosa creare un'applicazione nel proprio tenant e fornire ad Azure AD alcune informazioni fondamentali.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nella barra superiore fare clic sull'account. Nell'elenco di **Directory** scegliere il tenant di Azure AD in cui si vuole registrare l'applicazione.

3. Fare clic su **More Services** (Altri servizi) nel riquadro sinistro, quindi selezionare **Azure Active Directory**.

4. Fare clic su **Registrazioni per l'app** e scegliere **Aggiungi**.

5. Seguire le istruzioni e creare una nuova **applicazione Web e/o API Web**.
  * **Nome** descrive l'applicazione agli utenti. Immettere **To Do List Service**.
  * **URI di reindirizzamento** è una combinazione dello schema e della stringa che Azure AD userà per restituire i token richiesti dall'app. Immettere `https://localhost:44321/` per questo valore.

6. Dalla pagina **Impostazioni** -> **Proprietà** dell'applicazione aggiornare l'URI dell'ID app. Immettere un identificatore specifico del tenant. Ad esempio, immettere `https://contoso.onmicrosoft.com/TodoListService`.

7. Salvare la configurazione. Lasciare aperto il portale, poiché tra poco si dovrà registrare anche l'applicazione client.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Passaggio 2: Configurare l'app per l'uso della pipeline di autenticazione OWIN
Per convalidare le richieste in ingresso e i token, è necessario configurare l'applicazione per la comunicazione con Azure AD.

1. Per iniziare, aprire la soluzione e aggiungere i pacchetti NuGet del middleware OWIN al progetto TodoListService usando la Console di Gestione pacchetti.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Aggiungere al progetto TodoListService una OWIN Startup Class denominata `Startup.cs`.  Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** > **Nuovo elemento** e cercare **OWIN**. Il middleware OWIN richiamerà il metodo `Configuration(…)` all'avvio dell'app.

3. Modificare la dichiarazione di classe in `public partial class Startup`. Parte di questa classe è stata già implementata in un altro file. Nel metodo `Configuration(…)` effettuare una chiamata a `ConfgureAuth(…)` per configurare l'autenticazione per l'app Web.

    ```C#
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(…)`. I parametri forniti in `WindowsAzureActiveDirectoryBearerAuthenticationOptions` fungeranno da coordinate per consentire all'app di comunicare con Azure AD.

    ```C#
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. A questo punto è possibile usare gli attributi `[Authorize]` per proteggere i controller e le azioni con l'autenticazione della connessione al token JSON Web (JWT). Decorare la classe `Controllers\TodoListController.cs` con un tag di autorizzazione. Ciò forzerà l'utente a eseguire l'accesso prima di accedere alla pagina.

    ```C#
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Quando un chiamante autorizzato riesce a chiamare una delle API `TodoListController` , l'azione potrebbe richiedere l'accesso alle informazioni relative al chiamante. OWIN fornisce l'accesso alle attestazioni all'interno del token di connessione tramite l'oggetto `ClaimsPrincpal` .  

6. Un requisito comune per l'API Web è la convalida degli "ambiti" presenti nel token. Ciò garantisce che l'utente goda delle autorizzazioni necessarie per accedere a To Do List Service.

    ```C#
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Aprire il file `web.config` nella radice del progetto TodoListService e immettere i valori di configurazione nella sezione `<appSettings>`.
  * `ida:Tenant` è il nome del tenant di Azure AD, ad esempio contoso.onmicrosoft.com.
  * `ida:Audience` è l'URI ID app dell'applicazione immesso nel portale di Azure.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Passaggio 3: Configurare un'applicazione client ed eseguire il servizio
Prima di poter vedere To Do List Service in azione, è necessario configurare To Do List Client, in modo che possa ricevere i token da Azure AD ed effettuare chiamate al servizio.

1. Tornare al [portale di Azure](https://portal.azure.com).

2. Creare una nuova applicazione nel tenant di Azure AD e selezionare **Applicazione client nativa** nella richiesta risultante.
  * **Nome** descrive l'applicazione agli utenti.
  * Immettere `http://TodoListClient/` per il valore di **URI di reindirizzamento** .

3. Dopo aver completato la registrazione, Azure AD assegna all'app un ID applicazione univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla pagina dell'applicazione.

4. Nella pagina **Impostazioni** selezionare **Autorizzazioni necessarie** e selezionare **Aggiungi**. Individuare e selezionare To Do List Service, aggiungere l'autorizzazione di **Access TodoListService** in **Autorizzazioni delegate** e quindi fare clic su **Operazione completata**.

5. In Visual Studio aprire `App.config` nel progetto TodoListClient e quindi immettere i valori di configurazione nella sezione `<appSettings>`.

  * `ida:Tenant` è il nome del tenant di Azure AD, ad esempio contoso.onmicrosoft.com.
  * `ida:ClientId` è l'ID app copiato dal portale di Azure.
  * `todo:TodoListResourceId` è l'URI ID app dell'applicazione To Do List Service immesso nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi
Infine pulire, compilare ed eseguire ogni progetto. Se non si è ancora creato un nuovo utente nel tenant con un dominio *.onmicrosoft.com, ora è possibile farlo. Accedere al client To Do List come l'utente creato e aggiungere alcune attività all'elenco delle attività dell'utente.

Come riferimento, l'esempio completo, senza valori di configurazione, è disponibile in [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). È ora possibile passare ad altri scenari relativi alle identità.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
