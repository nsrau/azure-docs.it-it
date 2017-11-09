---
title: 'Configurazione guidata di app a singola pagina JavaScript con Azure AD v2: uso | Microsoft Docs'
description: Come le applicazioni SPA di Javascript possono chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: f52157df298ddfc1c1b29a18dc9a54aae59b52a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Usare Microsoft Authentication Library (MSAL) per l'accesso degli utenti

1.  Creare un file denominato `app.js`. Se si usa Visual Studio, selezionare il progetto (ossia la cartella radice del progetto), fare clic con il pulsante destro del mouse e scegliere `Add` > `New Item` > `JavaScript File`.
2.  Aggiungere il codice seguente al file `app.js`:

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show Sign-Out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });

    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from login
 * @param {object} error - The error string
 * @param {string} tokenType - the token type: usually id_token
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

Quando un utente fa clic sul pulsante *Call Microsoft Graph API* (Chiama API Microsoft Graph) per la prima volta, il metodo `callGraphApi` chiama `loginRedirect` per l'accesso dell'utente. Questo metodo determina il reindirizzamento dell'utente all'*endpoint di Microsoft Azure Active Directory v2* per la richiesta e la convalida delle credenziali utente. Dopo che è stato completato l'accesso, l'utente viene reindirizzato di nuovo alla pagina *index.html* originale e viene ricevuto un token, elaborato da `msal.js`, le cui informazioni vengono memorizzate nella cache. Questo token è noto come *token ID* e contiene informazioni di base sull'utente, ad esempio il nome visualizzato. Se si prevede di usare per qualsiasi scopo i dati forniti da questo token, è necessario verificare che il token venga convalidato dal server back-end per garantire che il token sia stato rilasciato a un utente valido per l'applicazione.

L'app a singola pagina generata da questa guida non usa direttamente il token ID, ma chiama `acquireTokenSilent` e/o `acquireTokenRedirect` per acquisire un *token di accesso* usato per eseguire query sull'API Microsoft Graph. Se è necessario un esempio che convalidi il token ID, vedere [questa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Esempio active-directory-javascript-singlepageapp-dotnet-webapi-v2 in GitHub") applicazione di esempio in GitHub. L'esempio usa un'API Web ASP.NET per la convalida dei token.

#### <a name="getting-a-user-token-interactively"></a>Acquisizione di un token utente in modo interattivo

Dopo l'accesso iniziale, per non chiedere agli utenti di ripetere l'autenticazione ogni volta che devono richiedere un token per accedere a una risorsa, si dovrà usare *acquireTokenSilent* per acquisire i token nella maggior parte dei casi. In alcune situazioni, tuttavia, è necessario imporre agli utenti di interagire con l'endpoint di Azure Active Directory v2, ad esempio:
-   Potrebbe essere necessario che gli utenti reimmettano le proprie credenziali perché la password è scaduta
-   L'applicazione richiede l'accesso a una risorsa per cui è necessario il consenso dell'utente
-   È necessaria l'autenticazione a due fattori

Chiamando *acquireTokenRedirect(scope)*, gli utenti vengono reindirizzati all'endpoint di Azure Active Directory v2 (mentre con *acquireTokenPopup(scope)* viene visualizzata una finestra popup) e gli utenti devono interagire confermando le proprie credenziali, dando il consenso per la risorsa necessaria o completando l'autenticazione a due fattori.

#### <a name="getting-a-user-token-silently"></a>Acquisizione di un token utente in modo invisibile
Il metodo ` acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo l'esecuzione iniziale di `loginRedirect` o `loginPopup`, il metodo in genere usato per le chiamate successive per ottenere i token usati per l'accesso alle risorse protette è `acquireTokenSilent` e le chiamate per richiedere o rinnovare token vengono eseguite in modo invisibile all'utente.
In alcuni casi, `acquireTokenSilent` può avere esito negativo, ad esempio se la password dell'utente è scaduta. L'applicazione può gestire questa eccezione in due modi:

1.  Eseguire immediatamente una chiamata ad `acquireTokenRedirect` in modo da chiedere all'utente di eseguire l'accesso. Questo modello viene in genere usato nelle applicazioni online in cui non è disponibile per l'utente contenuto non autenticato. L'esempio generato da questa configurazione guidata usa questo modello.

2. Le applicazioni possono anche generare un'indicazione visiva per informare l'utente che è necessario un accesso interattivo, in modo da consentire di scegliere il momento più opportuno per accedere. In alternativa, l'applicazione riproverà a eseguire `acquireTokenSilent` in un secondo momento. Questo modello viene in genere usato quando l'utente può accedere ad altre funzionalità dell'applicazione senza interruzioni, ad esempio quando nell'applicazione è disponibile contenuto non autenticato. In questo caso, l'utente può decidere se vuole eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

Aggiungere il codice seguente al file `app.js`:

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Altre informazioni sull'esecuzione di una chiamata REST a un'API protetta

Nell'applicazione di esempio creata da questa guida viene usato il metodo `callWebApiWithToken()` per eseguire una richiesta HTTP `GET` a una risorsa protetta che richiede un token e restituisce il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. Per l'applicazione di esempio creata da questa guida, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che consente di visualizzare informazioni sul profilo dell'utente.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Aggiungere un metodo per disconnettere l'utente

Aggiungere il codice seguente al file `app.js`:

```javascript
/**
 * Sign-out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
