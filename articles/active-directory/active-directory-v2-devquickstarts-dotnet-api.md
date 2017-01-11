---
title: API Web .NET v2.0 di Azure AD| Microsoft Docs
description: Come creare un&quot;API Web .NET MVC che accetta token da account Microsoft personali, aziendali e dell&quot;istituto di istruzione.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e77bc4e0-d0c9-4075-a3f6-769e2c810206
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 87c73981c74fc763fd1aec6c283e934c77008441
ms.openlocfilehash: 005e443bc2bc5cc32a7c32400cec3b7b34120485


---
# <a name="secure-an-mvc-web-api"></a>Proteggere un'API Web MVC
Con l'endpoint v2.0 di Azure Active Directory è possibile proteggere un'API Web usando token di accesso [OAuth 2.0](active-directory-v2-protocols.md) in modo da consentire agli utenti di accedere all'API Web in modo sicuro con un account Microsoft personale, aziendale o dell'istituto di istruzione.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint 2.0.  Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
>
>

Nelle API Web ASP.NET, a questo scopo si usa il middleware OWIN di Microsoft incluso in .NET Framework 4.5.  Verrà usato OWIN per compilare un'API Web MVC "Elenco attività" che consente ai client di creare e leggere le attività dall'elenco di attività dell'utente.  L'API Web verifica che le richieste in ingresso contengano un token di accesso valido e rifiuta le richieste che non superano la convalida su una route protetta.  Questo esempio è stato creato con Visual Studio 2015.

## <a name="download"></a>Scaricare
Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

Lo scheletro di un'app include tutto il codice boilerplate per una semplice API, ma non tutte le parti relative all'identità. Se non si vuole proseguire, in alternativa è possibile clonare o [scaricare l'esempio completo](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registrare un'app
Creare una nuova app in [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o seguire questa [procedura dettagliata](active-directory-v2-app-registration.md).  Verificare di:

* Copiare l' **ID applicazione** assegnato all'app, perché verrà richiesto a breve.

Questa soluzione di visual studio contiene anche una "TodoListClient", che è una semplice applicazione WPF.  La TodoListClient viene utilizzata per illustrare come avviene l’accesso dell'utente e come un client può inviare le richieste all’API Web.  In questo caso, sia TodoListClient sia il TodoListService sono rappresentati dalla stessa app.  Per configurare il TodoListClient, è necessario inoltre:

* Aggiungere la piattaforma **Mobile** per l'app.

## <a name="install-owin"></a>Installare OWIN
Dopo aver registrato un'app, si dovrà configurarla in modo che comunichi con l'endpoint v 2.0 per convalidare le richieste in ingresso e i token.

* Per iniziare, aprire la soluzione e aggiungere i pacchetti NuGet del middleware OWIN al progetto TodoListService usando la Console di Gestione pacchetti.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurare l'autenticazione OAuth
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

* Aprire il file `App_Start\Startup.Auth.cs` e implementare il metodo `ConfigureAuth(…)`, che configura l'API Web per accettare token dall'endpoint 2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

* È ora possibile usare gli attributi `[Authorize]` per proteggere i controller e le azioni con l'autenticazione della connessione OAuth 2.0.  Decorare la classe `Controllers\TodoListController.cs` con un tag di autorizzazione.  Ciò forzerà l'utente a eseguire l'accesso prima di accedere alla pagina.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

* Quando un chiamante autorizzato riesce a chiamare una delle API `TodoListController` , l'azione potrebbe richiedere l'accesso alle informazioni relative al chiamante.  OWIN fornisce l'accesso alle attestazioni all'interno del token di connessione tramite l'oggetto `ClaimsPrincpal` .  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

* Infine aprire il file `web.config` nella radice del progetto TodoListService e immettere i valori di configurazione nella sezione `<appSettings>`.
  * `ida:Audience` rappresenta l' **ID applicazione** dell'app immesso nel portale.

## <a name="configure-the-client-app"></a>Configurare l'app client
Prima di poter vedere in azione il servizio To Do List, è necessario configurare il client To Do List in modo che possa ricevere i token dall'endpoint 2.0 ed eseguire chiamate al servizio.

* Nel progetto client To Do List aprire `App.config` e immettere i valori di configurazione nella sezione `<appSettings>`.
  * ID applicazione `ida:ClientId` copiato dal portale.

Infine pulire, compilare ed eseguire ogni progetto.  È ora disponibile un'API Web .NET MVC che accetta token da account Microsoft personali, aziendali e dell'istituto di istruzione.  Accedere al client To Do List e chiamare l'API Web in modo che aggiunga attività all'elenco di attività da svolgere dell'utente.

Come riferimento, l'esempio completato (senza i valori di configurazione) [è disponibile in un file con estensione .zip qui](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Passaggi successivi
È ora possibile passare ad altri argomenti.  È possibile:

[Chiamare un'API Web da un'app Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Per altre risorse, vedere:

* [Guida per sviluppatori v2.0 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow: tag "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Ottenere aggiornamenti della sicurezza per i prodotti
È consigliabile ricevere notifiche in caso di problemi di sicurezza. A tale scopo, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e sottoscrivere gli avvisi di sicurezza.



<!--HONumber=Nov16_HO3-->


