---
title: Introduzione alla chiamata di API da un'App Web Azure AD v2.0 .NET| Documentazione Microsoft
description: Come creare un'app Web .NET MVC che chiama servizi Web usando account Microsoft personali, aziendali e dell'istituto di istruzione per l'accesso.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 56be906e-71de-469d-9a5c-9fc08aae4223
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: dc3162ae8e6ce622139125c2e78fa45d2e90d534
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="calling-a-web-api-from-a-net-web-app"></a>Chiamare un'API Web da un'applicazione Web .NET
Con l'endpoint v2.0 è possibile aggiungere rapidamente l'autenticazione alle app Web e alle API Web con supporto per account Microsoft personali, aziendali o dell'istituto di istruzione.  Di seguito viene creata un'app Web MVC che consente agli utenti di accedere tramite OpenID Connect e con l'aiuto del middleware OWIN di Microsoft.  L'applicazione Web otterrà i token di accesso OAuth 2.0 per un'api Web protetta da OAuth 2.0 che consente di creare, leggere ed eliminare le voci dell'elenco attività di un determinato utente.

Questa esercitazione è incentrata principalmente sull'uso di MSAL per l'acquisizione e l'uso di token di accesso in un'app Web, la cui descrizione dettagliata è disponibile [qui](active-directory-v2-flows.md#web-apps).  Come prerequisiti, è consigliabile apprendere come [aggiungere l'accesso base a un'app Web](active-directory-v2-devquickstarts-dotnet-web.md) o come [proteggere correttamente un'API Web](active-directory-v2-devquickstarts-dotnet-api.md).

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint 2.0.  Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-sample-code"></a>Scaricare il codice di esempio
Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

In alternativa, è possibile [scaricare l'app completata come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) oppure clonare l'app completata:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Registrare un'app
Creare una nuova app in [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o seguire questa [procedura dettagliata](active-directory-v2-app-registration.md).  Verificare di:

* Copiare l' **ID applicazione** assegnato all'app, perché verrà richiesto a breve.
* Creare una **Chiave privata app** di tipo **Password** e copiare il relativo valore per usarlo in seguito.
* Aggiungere la piattaforma **Web** per l'app.
* Immettere l' **URI di reindirizzamento**corretto. L'URI di reindirizzamento indica ad Azure AD dove indirizzare le risposte di autenticazione. Il valore predefinito per questa esercitazione è `https://localhost:44326/`.

## <a name="install-owin"></a>Installare OWIN
Aggiungere i pacchetti NuGet del middleware OWIN al progetto `TodoList-WebApp` tramite la console di Gestione pacchetti.  Il middleware OWIN verrà usato, tra le altre cose, per inviare le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Connettere l'utente all'app
Configurare il middleware OWIN per l'uso del [protocollo di autenticazione OpenID Connect](active-directory-v2-protocols.md).  

* Aprire il file `web.config` nella radice del progetto `TodoList-WebApp` e immettere i valori di configurazione dell'app nella sezione `<appSettings>`.
  * `ida:ClientId` rappresenta l' **ID applicazione** assegnato all'app nel portale di registrazione.
  * `ida:ClientSecret` rappresenta la **chiave privata app** creata nel portale di registrazione.
  * `ida:RedirectUri` rappresenta l' **URI di reindirizzamento** immesso nel portale.
* Aprire il file `web.config` nella radice del progetto `TodoList-Service` e sostituire `ida:Audience` con lo stesso **ID applicazione** di cui sopra.
* Aprire il file `App_Start\Startup.Auth.cs` e aggiungere istruzioni `using` per le librerie indicate in precedenza.
* Nello stesso file, implementare il metodo `ConfigureAuth(...)` .  I parametri forniti in `OpenIDConnectAuthenticationOptions` fungeranno da coordinate per consentire all'app di comunicare con Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Usare MSAL per ottenere i token di accesso
Nella notifica `AuthorizationCodeReceived` si desidera usare [OAuth 2.0 in parallelo con OpenID Connect](active-directory-v2-protocols.md) per riscattare l'authorization_code per un token di accesso al servizio To Do List.  MSAL può semplificare questo processo.

* Per prima cosa installare la versione di anteprima di MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```

* Aggiungere quindi un'altra istruzione `using`al file `App_Start\Startup.Auth.cs` per MSAL.
* Aggiungere ora un nuovo metodo, il gestore eventi `OnAuthorizationCodeReceived`.  Questo gestore userà MSAL per acquisire un token di accesso per l'API To Do List e archivierà il token nella cache dei token di MSAL per usi successivi:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

* Nelle app Web, MSAL ha una cache di token estendibile che può essere usata per archiviare i token.  Questo esempio implementa `NaiveSessionCache` che usa lo spazio di archiviazione della sessione HTTP per la memorizzazione dei token nella cache.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Chiamare l'API Web
È ora possibile usare il token di accesso acquisito al passaggio 3.  Aprire il file `Controllers\TodoListController.cs` dell'app Web, che esegue tutte le richieste CRUD all'API To Do List.

* È possibile usare nuovamente MSAL per recuperare i token di accesso dalla cache MSAL.  Per prima cosa aggiungere un'istruzione `using` per MSAL a questo file.
  
    `using Microsoft.Identity.Client;`
* Nell'azione `Index` usare il metodo `AcquireTokenSilentAsync` di MSAL per ottenere un token di accesso da usare per leggere i dati dal servizio To Do List:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

* L'esempio aggiunge quindi il token risultante alla richiesta HTTP GET come intestazione `Authorization`, usata dal servizio To Do List per autenticare la richiesta.
* Se il servizio To Do List restituisce una risposta `401 Unauthorized`, significa che i token di accesso in MSAL per qualche motivo non sono più validi.  In questo caso, è consigliabile eliminare tutti i token di accesso dalla cache MSAL e visualizzare all'utente un messaggio che comunica che potrebbe essere necessario eseguire un nuovo accesso. In questo modo verrà riavviato il flusso di acquisizione dei token.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

* Analogamente, se per qualsiasi motivo MSAL non riesce a restituire un token di accesso, è consigliabile chiedere all'utente di eseguire di nuovo l'accesso.  Questo è semplice quanto individuare eventuali `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

* La stessa identica chiamata `AcquireTokenSilentAsync` viene implementata nelle azioni `Create` e `Delete`.  Nelle app Web è possibile usare questo metodo MSAL per ottenere i token di accesso ogni volta che sono necessari nell'app.  L'acquisizione, la memorizzazione nella cache e l'aggiornamento dei token vengono gestiti da MSAL.

Infine compilare ed eseguire l'app.  Accedere con un account Microsoft o con un account Azure AD e osservare che l'identità dell'utente sia visibile nella barra di spostamento superiore.  Aggiungere ed eliminare alcuni elementi dall'elenco di attività dell'utente per vedere le chiamate API protette OAuth 2.0 in azione.  Sono ora disponibili un'app e un'API Web protette che usano protocolli standard del settore in grado di autenticare gli utenti con account personali, aziendali e dell'istituto di istruzione.

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) [qui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Passaggi successivi
Per altre risorse, vedere:

* [Guida per sviluppatori v2.0 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow: tag "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Ottenere aggiornamenti della sicurezza per i prodotti
È consigliabile ricevere notifiche in caso di problemi di sicurezza. A tale scopo, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e sottoscrivere gli avvisi di sicurezza.

