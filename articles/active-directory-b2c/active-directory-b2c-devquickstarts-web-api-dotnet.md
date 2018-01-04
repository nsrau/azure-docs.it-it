---
title: Azure Active Directory B2C | Documentazione Microsoft
description: Come compilare un'app Web .NET e chiamare un API Web usando Azure Active Directory B2C i token di accesso OAuth 2.0.
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: mtillman
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 42b2b698493408f11ee23f06f99d9ba22860746a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C: Chiamare un'API Web .NET da un'app Web .NET

Tramite Azure AD B2C, è possibile aggiungere potenti funzionalità di gestione di identità per l'app Web e le API. In questo articolo viene illustrato come richiedere i token di accesso ed effettuare chiamate da un'app Web "elenco attività" .NET per API Web .NET.

Questo articolo non descrive come implementare le esperienze di accesso, iscrizione e gestione del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha già effettuato l'autenticazione. Se lo si è già fatto, è necessario fare:

* un'introduzione alle [app Web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* un'introduzione alle [API Web .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Prerequisito

Per compilare un'app Web che chiama un'API Web è necessario:

1. [Creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md).
2. [Registrare un'API Web](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Registrare un'app Web](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Configurare i criteri](active-directory-b2c-reference-policies.md).
5. [Concedere le autorizzazioni all'app Web per usare le API Web](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> L'applicazione client e l'API Web devono usare la stessa directory di Azure AD B2C.
>

## <a name="download-the-code"></a>Scaricare il codice

Il codice per questa esercitazione è salvato su [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). È possibile clonare l'esempio eseguendo:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare. Il file della soluzione contiene due progetti: `TaskWebApp` e `TaskService`. `TaskWebApp` è un'applicazione Web MVC con cui l'utente interagisce. `TaskService` è l'API Web back-end dell'app in cui viene archiviato l'elenco attività di ogni utente. Questo articolo non descrive la compilazione dell'app Web `TaskWebApp` o delle API Web `TaskService`. Per informazioni su come compilare un'app Web .NET usando Azure AD B2C, vedere l'[esercitazione sulle app Web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Per informazioni su come compilare l'API Web .NET usando Azure AD B2C, vedere l'[esercitazione sulle API Web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Aggiornare la configurazione di Azure AD B2C

L'esempio è configurato per l'uso dei criteri e dell'ID client del tenant demo. Se si desidera usare il proprio tenant:

1. Aprire `web.config` nel progetto `TaskService` e sostituire i valori di

    * `ida:Tenant` con il nome del tenant
    * `ida:ClientId` con l'ID dell'applicazione API Web
    * `ida:SignUpSignInPolicyId` con il nome del criterio "Iscrizione o accesso"

2. Aprire `web.config` nel progetto `TaskWebApp` e sostituire i valori di

    * `ida:Tenant` con il nome del tenant
    * `ida:ClientId` con l'ID dell'applicazione di tipo app Web
    * `ida:ClientSecret` con la chiave privata dell'app Web
    * `ida:SignUpSignInPolicyId` con il nome del criterio "Iscrizione o accesso"
    * `ida:EditProfilePolicyId` con il nome del criterio "Modifica profilo"
    * `ida:ResetPasswordPolicyId` con il nome del criterio "Reimposta password"



## <a name="requesting-and-saving-an-access-token"></a>Richiesta e salvataggio di un token di accesso

### <a name="specify-the-permissions"></a>Specificare le autorizzazioni

Per poter effettuare la chiamata all'API Web, è necessario autenticare l'utente (tramite i criteri di registrazione o accesso) e [ricevere un token di accesso](active-directory-b2c-access-tokens.md) da Azure Active Directory B2C. Per ricevere un token di accesso, è necessario specificare le autorizzazioni che si desidera concedere al token di accesso. Le autorizzazioni vengono specificate nel parametro `scope` quando si effettua la richiesta per l'endpoint `/authorize`. Ad esempio, per acquisire un token di accesso con l'autorizzazione "lettura" per l'applicazione della risorsa con l'URI ID App di `https://contoso.onmicrosoft.com/tasks`, l'ambito sarà `https://contoso.onmicrosoft.com/tasks/read`.

Per specificare l'ambito nell'esempio, aprire il file `App_Start\Startup.Auth.cs` e definire la variabile `Scope` in OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Scambiare il codice di autorizzazione con un token di accesso

Al termine dell'esperienza di iscrizione o accesso dell'utente, l'applicazione riceverà un codice di autorizzazione da Azure AD B2C. Il middleware OWIN OpenID Connect archivierà il codice, ma non lo scambierà per un token di accesso. È possibile usare la [libreria MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) per fare lo scambio. Nell'esempio, è stato configurato un callback di notifica nel middleware OpenID Connect ogni volta che viene ricevuto un codice di autorizzazione. Nel callback viene usato MSAL per scambiare il codice con un token e salvare il token nella cache.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Chiamata dell'API Web

Questa sezione illustra come usare il token ricevuto durante la registrazione o l'accesso con Azure AD B2C per accedere a un'API Web.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Recuperare il token salvato nei controller

`TasksController` è responsabile della comunicazione con l'API Web e dell'invio di richieste HTTP all'API per la lettura, la creazione e l'eliminazione di attività. Poiché che l'API è protetta da Azure AD B2C, per prima cosa è necessario recuperare il token salvato nel passaggio precedente.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Leggere le attività dall'API Web

Dopo aver ottenuto un token, collegarlo alla richiesta HTTP `GET` nell'intestazione `Authorization` per chiamare `TaskService` in modo sicuro:

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Creare ed eliminare attività nell'API Web

Seguire lo stesso modello quando si inviano le richieste `POST` e `DELETE` all'API Web, usando MSAL per recuperare il token di accesso dalla cache.

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Infine, compilare ed eseguire entrambe le app. Eseguire l'iscrizione e l'accesso e creare attività per l'utente connesso. Disconnettersi ed eseguire l'accesso usando un account utente diverso. Creare le attività per l'utente. Si noti che le attività sono archiviate per ogni utente nell'API, perché l'API estrae l'identità dell'utente dai token che riceve. Provare inoltre a usare gli ambiti. Rimuovere l'autorizzazione "scrivere" e quindi provare ad aggiungere un'attività. Basta assicurarsi di disconnettersi ogni volta che si modifica l'ambito.

