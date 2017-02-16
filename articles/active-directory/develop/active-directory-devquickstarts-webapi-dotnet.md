---
title: Introduzione a .NET per Azure AD | Documentazione Microsoft
description: Come compilare un&quot;API Web MVC per Node.js che si integra con Azure AD per l&quot;autenticazione e l&quot;autorizzazione.
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
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: c401d473d3951ed5853170e8761a09a915329164


---
# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Proteggere un'API Web usando token di connessione di Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Se si crea un'applicazione che fornisce l'accesso alle risorse protette, è necessario sapere come proteggere le risorse dall'accesso non autorizzato.
Azure AD rende semplici e dirette le operazioni per la protezione di un'API Web usando i token di accesso di OAuth Bearer 2.0 con solo poche righe di codice.

Nelle app Web Asp.NET, a questo scopo si usa l'implementazione di Microsoft del middleware OWIN gestito dalla community e incluso in .NET Framework 4.5.  Qui OWIN verrà usato per creare un'API Web "Elenco attività" in grado di:

* Impostare le API protette.
* Verificare che le chiamate all'API Web contengano un token di accesso valido.

A questo scopo è necessario:

1. Registrare un'applicazione con Azure AD.
2. Configurare l'app per l'uso della pipeline di autenticazione OWIN.
3. Configurare un'applicazione client per chiamare l'API Web To Do List.

Per iniziare, [scaricare la struttura dell'app](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Ognuno è una soluzione di Visual Studio 2013.  Sarà necessario anche un tenant di Azure AD in cui registrare l'applicazione.  Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="1----register-an-application-with-azure-ad"></a>1.    Registrare un'applicazione con Azure AD
Per proteggere l'applicazione, si dovrà per prima cosa creare un'applicazione nel proprio tenant e fornire ad Azure AD alcune informazioni fondamentali.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra in alto fare clic sull'account e nell'elenco **Directory** scegliere il tenant di Active Directory in cui si vuole registrare l'applicazione.
3. Fare clic su **Altri servizi** nella barra di spostamento a sinistra e scegliere **Azure Active Directory**.
4. Fare clic su **App registrations (Registrazioni app)** e scegliere **Aggiungi**.
5. Seguire le istruzioni e creare una nuova **Applicazione Web e/o API Web**.
  * Il **Nome** dell'applicazione deve essere una descrizione per gli utenti finali.  Immettere "To Do List Service".
  * L' **URI di reindirizzamento** è una combinazione dello schema e della stringa che Azure AD userà per restituire i token richiesti dall'app. Immettere `https://localhost:44321/` per questo valore.
  * Per il campo **URI ID app** immettere un identificatore specifico del tenant, quale `https://contoso.onmicrosoft.com/TodoListService`
6. Salvare la configurazione.  Lasciare aperto il portale, tra poco si dovrà registrare anche l'applicazione client.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>2. Configurare l'app per l'uso della pipeline di autenticazione OWIN.
Dopo aver registrato un'applicazione con Azure AD, si dovrà configurarla in modo che comunichi con Azure AD per convalidare le richieste in ingresso e i token.

* Per iniziare, aprire la soluzione e aggiungere i pacchetti NuGet del middleware OWIN al progetto TodoListService usando la Console di Gestione pacchetti.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

* Aggiungere al progetto TodoListService una OWIN Startup Class denominata `Startup.cs`.  Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** --> **Nuovo elemento** e quindi cercare"OWIN".  Il middleware OWIN richiamerà il metodo `Configuration(…)` all'avvio dell'app.
* Sostituire la dichiarazione della classe con `public partial class Startup` .  Nel metodo `Configuration(…)` effettuare una chiamata a ConfgureAuth(...) per configurare l'autenticazione per l'app Web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(…)`.  I parametri forniti in `WindowsAzureActiveDirectoryBearerAuthenticationOptions` fungeranno da coordinate per consentire all'app di comunicare con Azure AD.

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

* A questo punto è possibile usare gli attributi `[Authorize]` per proteggere i controller e le azioni con l'autenticazione della connessione JWT.  Decorare la classe `Controllers\TodoListController.cs` con un tag di autorizzazione.  Ciò forzerà l'utente a eseguire l'accesso prima di accedere alla pagina.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

* Quando un chiamante autorizzato riesce a chiamare una delle API `TodoListController` , l'azione potrebbe richiedere l'accesso alle informazioni relative al chiamante.  OWIN fornisce l'accesso alle attestazioni all'interno del token di connessione tramite l'oggetto `ClaimsPrincpal` .  
* Un requisito comune per le API Web riguarda la convalida degli "ambiti" presenti nel token. In questo modo si assicura che l'utente finale abbia acconsentito alle autorizzazioni richieste per accedere a Todo List Service:

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
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

* Infine aprire il file `web.config` nella radice del progetto TodoListService e immettere i valori di configurazione nella sezione `<appSettings>`.
  * `ida:Tenant` è il nome del tenant di Azure AD, ad esempio, "contoso.onmicrosoft.com".
  * `ida:Audience` è l'URI ID app dell'applicazione immesso nel portale di Azure.

## <a name="3-configure-a-client-application--run-the-service"></a>3. Configurare un'applicazione client ed eseguire il servizio
Prima di poter vedere Todo List Service in azione, è necessario configurare Todo List Client, in modo che possa ricevere i token da AAD ed effettuare chiamate al servizio.

* Ritornare al [portale di Azure](https://portal.azure.com)
* Creare una nuova applicazione nel tenant di Azure AD e selezionare **Applicazione client nativa** nella richiesta risultante.
  * Il **Nome** dell'applicazione deve essere una descrizione per gli utenti finali.
  * Immettere `http://TodoListClient/` per il valore di **URI di reindirizzamento** .
* Dopo avere completato la registrazione, AAD assegnerà all'app un **ID app** univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla pagina dell'applicazione.
* Nella pagina **Impostazioni** scegliere **Autorizzazioni necessarie** e quindi scegliere **Aggiungi**.  Individuare e selezionare TodoListService e aggiungere l'autorizzazione di **accesso a TodoListService** in **Autorizzazioni delegate** e scegliere **Fatto**.

* In Visual Studio aprire `App.config` nel progetto TodoListClient e immettere i valori di configurazione nella sezione `<appSettings>`.
  
  * `ida:Tenant` è il nome del tenant di Azure AD, ad esempio, "contoso.onmicrosoft.com".
  * L'ID app `ida:ClientId` copiato dal portale di Azure.
  * `todo:TodoListResourceId` è l'URI ID app dell'applicazione To Do List Service immesso nel portale di Azure.

Infine pulire, compilare ed eseguire ogni progetto.  Se non si è ancora creato un nuovo utente nel tenant con un dominio *.onmicrosoft.com, ora è possibile farlo.  Accedere al client To Do List come l'utente creato e aggiungere alcune attività all'elenco azioni dell'utente.

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) [qui](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  È ora possibile passare ad altri scenari relativi alle identità.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


