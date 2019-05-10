---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8785f89a335f0ae7d983f267176da1656aee57a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199175"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Usare Microsoft Authentication Library (MSAL) per l'accesso degli utenti

1. Aggiungere il codice seguente al file `index.html` tra i tag `<script></script>`:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };

    var graphConfig = {
        graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
    };

    // this can be used for login or token request, however in more complex situations
    // this can have diverging options
    var requestObj = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallback(authRedirectCallBack);


    function signIn() {

        myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }

    function acquireTokenPopupAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
        });
    }


    function graphAPICallback(data) {
        document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
    }


    function showWelcomeMessage() {
        var divWelcome = document.getElementById('WelcomeMessage');
        divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
        var loginbutton = document.getElementById('SignIn');
        loginbutton.innerHTML = 'Sign Out';
        loginbutton.setAttribute('onclick', 'signOut();');
    }


    //This function can be removed if you do not need to support IE
    function acquireTokenRedirectAndCallMSGraph() {
         //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
         myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
             callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
         }).catch(function (error) {
             console.log(error);
             // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
             // Call acquireTokenRedirect
             if (requiresInteraction(error.errorCode)) {
                 myMSALObj.acquireTokenRedirect(requestObj);
             }
         });
     }


    function authRedirectCallBack(error, response) {
        if (error) {
            console.log(error);
        }
        else {
            if (response.tokenType === "access_token") {
                callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
            } else {
                console.log("token type is:" + response.tokenType);
            }
        }
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }

    // Browser check variables
    var ua = window.navigator.userAgent;
    var msie = ua.indexOf('MSIE ');
    var msie11 = ua.indexOf('Trident/');
    var msedge = ua.indexOf('Edge/');
    var isIE = msie > 0 || msie11 > 0;
    var isEdge = msedge > 0;
    //If you support IE, our recommendation is that you sign-in using Redirect APIs
    //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
    // can change this to default an experience outside browser use
    var loginType = isIE ? "REDIRECT" : "POPUP";

    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
        };
        if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
    } else {
        console.error('Please set a valid login type');
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

Quando un utente fa clic sul pulsante **Accedi** per la prima volta, il metodo `signIn` chiama `loginPopup` per l'accesso dell'utente. Questo metodo determina l'apertura di una finestra popup con l'*endpoint Microsoft Identity Platform* per la richiesta e la convalida delle credenziali utente. Dopo che è stato completato l'accesso, l'utente viene reindirizzato di nuovo alla pagina *index.html* originale e viene ricevuto un token, elaborato da `msal.js`, le cui informazioni vengono memorizzate nella cache. Questo token è noto come *token ID* e contiene informazioni di base sull'utente, ad esempio il nome visualizzato. Se si prevede di usare per qualsiasi scopo i dati forniti da questo token, è necessario verificare che il token venga convalidato dal server back-end per garantire che il token sia stato rilasciato a un utente valido per l'applicazione.

L'applicazione a pagina singola generata da questa guida chiama `acquireTokenSilent` e/o `acquireTokenPopup` per acquisire un *token di accesso* usato per cercare nell'API Microsoft Graph le informazioni sul profilo utente. Se è necessario un esempio che convalidi il token ID, vedere [questa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Esempio active-directory-javascript-singlepageapp-dotnet-webapi-v2 in GitHub") applicazione di esempio in GitHub. L'esempio usa un'API Web ASP.NET per la convalida dei token.

#### <a name="getting-a-user-token-interactively"></a>Acquisizione di un token utente in modo interattivo

Dopo l'accesso iniziale, per non chiedere agli utenti di ripetere l'autenticazione ogni volta che devono richiedere un token per accedere a una risorsa, si dovrà usare *acquireTokenSilent* per acquisire i token nella maggior parte dei casi. In alcune situazioni, tuttavia, è necessario imporre agli utenti di interagire con l'endpoint Microsoft Identity Platform, ad esempio:

- Potrebbe essere necessario che gli utenti reimmettano le proprie credenziali perché la password è scaduta
- L'applicazione richiede l'accesso a una risorsa per cui è necessario il consenso dell'utente
- È necessaria l'autenticazione a due fattori

Chiamando *acquireTokenPopup* viene visualizzata una finestra popup (mentre con *acquireTokenRedirect* gli utenti vengono reindirizzati all'endpoint Microsoft Identity Platform) in cui gli utenti devono interagire confermando le proprie credenziali, dando il consenso per la risorsa necessaria o completando l'autenticazione a due fattori.

#### <a name="getting-a-user-token-silently"></a>Acquisizione di un token utente in modo invisibile

Il metodo `acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo l'esecuzione iniziale di `loginPopup` o `loginRedirect`, il metodo in genere usato per le chiamate successive per ottenere i token usati per l'accesso alle risorse protette è `acquireTokenSilent` e le chiamate per richiedere o rinnovare token vengono eseguite in modo invisibile all'utente.
In alcuni casi, `acquireTokenSilent` può avere esito negativo, ad esempio se la password dell'utente è scaduta. L'applicazione può gestire questa eccezione in due modi:

1. Eseguire immediatamente una chiamata ad `acquireTokenPopup` in modo da chiedere all'utente di eseguire l'accesso. Questo modello viene in genere usato nelle applicazioni online in cui non è disponibile per l'utente contenuto non autenticato. L'esempio generato da questa configurazione guidata usa questo modello.

2. Le applicazioni possono anche generare un'indicazione visiva per informare l'utente che è necessario un accesso interattivo, in modo da consentire di scegliere il momento più opportuno per accedere. In alternativa, l'applicazione riproverà a eseguire `acquireTokenSilent` in un secondo momento. Questo modello viene in genere usato quando l'utente può accedere ad altre funzionalità dell'applicazione senza interruzioni, ad esempio quando nell'applicazione è disponibile contenuto non autenticato. In questo caso, l'utente può decidere se vuole eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete.

> [!NOTE]
> Questa guida di avvio rapido usa i metodi `loginRedirect` e `acquireTokenRedirect` quando il browser è Internet Explorer a causa di un [problema noto](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) correlato alla gestione delle finestre popup da questo browser.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

Aggiungere il codice seguente al file `index.html` tra i tag `<script></script>`:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Altre informazioni sull'esecuzione di una chiamata REST a un'API protetta

Nell'applicazione di esempio creata da questa guida viene usato il metodo `callMSGraph()` per eseguire una richiesta HTTP `GET` a una risorsa protetta che richiede un token e restituisce il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. Per l'applicazione di esempio creata da questa guida, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che consente di visualizzare informazioni sul profilo dell'utente.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Aggiungere un metodo per disconnettere l'utente

Aggiungere il codice seguente al file `index.html` tra i tag `<script></script>`:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
