---
title: Configurazione guidata di un'applicazione a pagina singola Java Script di Azure AD v2.0 | Microsoft Docs
description: Informazioni su come le applicazioni a pagina singola JavaScript possono chiamare un'API che richiede token di accesso generati dall'endpoint di Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61790954393923bbf330ad3a534d1d33d1a44bbc
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983487"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Eseguire l'accesso degli utenti e chiamare l'API Microsoft Graph da un'applicazione a singola pagina (SPA) di JavaScript

Questa guida illustra come un'applicazione a pagina singola JavaScript consente di:
- Accedere agli account personali, nonché agli account aziendali e dell'istituto di istruzione 
- Acquisire un token di accesso
- Chiamare l'API Microsoft Graph o altre API che richiedono token di accesso generati dall'*endpoint di Microsoft Identity Platform*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funzionamento dell'app di esempio generata da questa guida

![Illustra come funziona l'app di esempio generata da questa esercitazione](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

L'applicazione di esempio creata in questa guida consente a un'applicazione a pagina singola JavaScript di eseguire query sull'API Microsoft Graph o su un'API Web che accetta token dall'endpoint di Microsoft Identity Platform. Per questo scenario, dopo l'accesso di un utente, viene richiesto un token di accesso che viene aggiunto a richieste HTTP tramite l'intestazione dell'autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Librerie

Questa guida usa la libreria seguente:

|Libreria|DESCRIZIONE|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Authentication Library di Microsoft per l'anteprima di JavaScript|

> [!NOTE]
> *Msal.js* specifica come destinazione l'endpoint di Microsoft Identity Platform, che consente agli account personali, aziendali e dell'istituto di istruzione di eseguire l'accesso e di acquisire i token. L'endpoint di Microsoft Identity Platform presenta [alcune limitazioni](azure-ad-endpoint-comparison.md#limitations).
> Per capire le differenze tra gli endpoint v1.0 e v2.0, vedere la [guida al confronto tra gli endpoint](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Impostare il server Web o il progetto

> Si preferisce scaricare questo progetto di esempio? Eseguire una di queste operazioni:
> 
> - Per eseguire il progetto con un server Web locale, ad esempio Node.js, [scaricare i file di progetto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Facoltativo) Per eseguire il progetto usando il server Microsoft IIS (Internet Information Services), [scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Per configurare l'esempio di codice prima di eseguirlo, procedere con il [passaggio di configurazione](#register-your-application).

## <a name="prerequisites"></a>Prerequisiti

* Per eseguire l'esercitazione, è necessario un server Web locale, ad esempio [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) o l'integrazione di IIS Express con [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Se si usa Node.js per eseguire il progetto, installare un ambiente di sviluppo integrato (IDE), ad esempio [Visual Studio Code](https://code.visualstudio.com/download), per modificare i file di progetto.

* Le istruzioni di questa guida sono basate su Node.js e Visual Studio 2017, ma è possibile usare qualsiasi altro ambiente di sviluppo o server Web.

## <a name="create-your-project"></a>Creare il progetto

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Opzione 1: Node.js o altri server Web
> Assicurarsi che [Node.js](https://nodejs.org/en/download/) sia installato e quindi creare una cartella in cui ospitare l'applicazione.
>
> ### <a name="option-2-visual-studio"></a>Opzione 2: Visual Studio
> Se si usa Visual Studio e si sta creando un nuovo progetto, procedere come segue:
> 1. In Visual Studio selezionare **File** > **Nuovo** > **Progetto**.
> 1. In **Visual C#\Web** selezionare **Applicazione Web ASP.NET (.NET Framework)** .
> 1. Immettere un nome per l'applicazione e quindi fare clic su **OK**.
> 1. In **Nuova applicazione Web ASP.NET** selezionare **Vuota**.

## <a name="create-the-spa-ui"></a>Creare l'interfaccia utente SPA
1. Creare un file *index.html* per la SPA di JavaScript. Se si usa Visual Studio, selezionare il progetto (ossia la cartella radice del progetto). Fare clic con il pulsante destro del mouse e scegliere **Aggiungi** > **Nuovo elemento** > **Pagina HTML**, quindi assegnare al file il nome *index.html*.

1. Nel file *index.html* aggiungere il codice seguente:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > È possibile sostituire la versione di MSAL.js nello script precedente con l'ultima versione rilasciata disponibile nella pagina delle [versioni di MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Usare Microsoft Authentication Library (MSAL) per l'accesso degli utenti

Aggiungere il codice seguente al file `index.html` tra i tag `<script></script>`:

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
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

Quando un utente seleziona il pulsante **Accedi** per la prima volta, il metodo `signIn` chiama `loginPopup` per farlo accedere. Questo metodo apre una finestra popup con l'*endpoint di Microsoft Identity Platform* per la richiesta e la convalida delle credenziali dell'utente. Dopo aver eseguito l'accesso, l'utente viene reindirizzato alla pagina *index.html* originale. Un token viene ricevuto ed elaborato da `msal.js`, quindi le informazioni contenute nel token vengono memorizzate nella cache. Questo token è noto come *token ID* e contiene informazioni di base sull'utente, ad esempio il nome visualizzato. Se si prevede di usare per qualsiasi scopo i dati forniti da questo token, è necessario verificare che il token venga convalidato dal server back-end per garantire che il token sia stato rilasciato a un utente valido per l'applicazione.

L'applicazione a pagina singola generata da questa guida chiama `acquireTokenSilent` e/o `acquireTokenPopup` per acquisire un *token di accesso* usato per cercare nell'API Microsoft Graph le informazioni sul profilo utente. Se è necessario un esempio che convalidi il token ID, vedere [questa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "active-directory-javascript-singlepageapp-dotnet-webapi-v2 di esempio in GitHub") applicazione di esempio in GitHub. L'esempio usa un'API Web ASP.NET per la convalida dei token.

#### <a name="getting-a-user-token-interactively"></a>Acquisizione di un token utente in modo interattivo

Dopo l'accesso iniziale, per non chiedere agli utenti di ripetere l'autenticazione ogni volta che devono richiedere un token per accedere a una risorsa, si dovrà usare *acquireTokenSilent* per acquisire i token nella maggior parte dei casi. In alcune situazioni, tuttavia, è necessario forzare gli utenti a interagire con l'endpoint di Microsoft Identity Platform. Tra gli esempi sono inclusi:

- Gli utenti devono reimmettere le proprie credenziali perché la password è scaduta.
- L'applicazione richiede l'accesso a una risorsa per cui è necessario il consenso dell'utente.
- È necessaria l'autenticazione a due fattori.

La chiamata ad *acquireTokenPopup* apre una finestra popup (oppure *acquireTokenRedirect* reindirizza gli utenti all'endpoint di Microsoft Identity Platform). In questa finestra gli utenti devono interagire confermando le proprie credenziali, fornendo il consenso per la risorsa necessaria o completando l'autenticazione a due fattori.

#### <a name="getting-a-user-token-silently"></a>Acquisizione di un token utente in modo invisibile

Il metodo `acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo aver eseguito il metodo `loginPopup` o `loginRedirect` la prima volta, per le chiamate successive il metodo comunemente usato per ottenere i token usati per accedere a risorse protette è `acquireTokenSilent`. Le chiamate per richiedere o rinnovare i token vengono eseguite in modo invisibile per l'utente. `acquireTokenSilent` potrebbe non riuscire in alcuni casi. È ad esempio possibile che la password dell'utente sia scaduta. L'applicazione può gestire questa eccezione in due modi:

1. Effettuare una chiamata a `acquireTokenPopup` immediatamente, che attiva una richiesta di accesso utente. Questo modello viene in genere usato nelle applicazioni online in cui non è disponibile per l'utente contenuto non autenticato. L'esempio generato da questa configurazione guidata usa questo modello.

2. Le applicazioni possono anche generare un'indicazione visiva per informare l'utente che è necessario un accesso interattivo, in modo da consentire di scegliere il momento più opportuno per accedere. In alternativa, l'applicazione riproverà a eseguire `acquireTokenSilent` in un secondo momento. Questa opzione viene in genere usata quando l'utente può usare altre funzionalità dell'applicazione senza subire interruzioni. Ad esempio, nell'applicazione potrebbe essere disponibile contenuto non autenticato. In questa situazione, l'utente può decidere se vuole eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete.

> [!NOTE]
> Questo argomento di avvio rapido usa i metodi `loginRedirect` e `acquireTokenRedirect` quando il browser in uso è Internet Explorer. Il motivo è che esiste un [problema noto](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relativo al modo in cui Internet Explorer gestisce le finestre popup.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Chiamare l'API Microsoft Graph usando il token appena acquisito

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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Altre informazioni sull'esecuzione di una chiamata REST a un'API protetta

Nell'applicazione di esempio creata in questa guida, viene usato il metodo `callMSGraph()` per eseguire una richiesta HTTP `GET` per una risorsa protetta che richiede un token. La richiesta restituisce quindi il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. Per l'applicazione di esempio creata da questa guida, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che visualizza informazioni sul profilo dell'utente.

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

## <a name="register-your-application"></a>Registrare l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e quindi impostare la sessione del portale sul tenant di Azure AD che si vuole usare.
1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
1. Nell'elenco a discesa nella sezione **URI di reindirizzamento** selezionare la piattaforma **Web** e quindi impostare il valore sull'URL dell'applicazione basato sul server Web.

   Per informazioni su come impostare e ottenere l'URL di reindirizzamento per Node.js e Visual Studio, vedere la sezione seguente, "Impostare un URL di reindirizzamento per Node.js", e l'argomento [Impostare un URL di reindirizzamento per Visual Studio](#set-a-redirect-url-for-visual-studio).

1. Selezionare **Registra**.
1. Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
1. Per questo avvio rapido è necessario abilitare il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md). Nel riquadro sinistro dell'applicazione registrata selezionare **Autenticazione**.
1. In **Impostazioni avanzate**, in **Concessione implicita**, selezionare entrambe le caselle di controllo **Token ID** e **Token di accesso**. I token ID e di accesso sono obbligatori perché l'app deve consentire l'accesso agli utenti e chiamare un'API.
1. Selezionare **Salva**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Impostare l'URL di reindirizzamento per Node.js
> Per Node.js, è possibile impostare la porta del server Web nel file *server.js*. Questa esercitazione usa la porta 30662, ma è possibile usarne un'altra qualsiasi disponibile.
>
> Per configurare un URL di reindirizzamento nelle informazioni di registrazione dell'applicazione, tornare al riquadro **Registrazione dell'applicazione** ed eseguire una delle operazioni seguenti:
>
> - Impostare *`http://localhost:30662/`* come **URL di reindirizzamento**.
> - Se si usa una porta TCP personalizzata, usare *`http://localhost:<port>/`* (dove *\<porta>* è il numero della porta TCP personalizzata).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Impostare l'URL di reindirizzamento per Visual Studio
> Per ottenere l'URL di reindirizzamento per Visual Studio, procedere come segue:
> 1. In Esplora soluzioni selezionare il progetto.
>
>    Si aprirà la finestra **Proprietà**. In caso contrario, premere F4.
>
>    ![Finestra Proprietà del progetto JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copiare il valore del campo **URL**.
> 1. Tornare al riquadro **Registrazione dell'applicazione** e incollare il valore copiato come **URL di reindirizzamento**.

#### <a name="configure-your-javascript-spa"></a>Configurare JavaScript SPA

1. Nel file *index.html* creato durante la configurazione del progetto aggiungere le informazioni di registrazione dell'applicazione. Nella parte iniziale del file, tra i tag `<script></script>`, aggiungere il codice seguente:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Dove:
    - *\<Enter_the_Application_Id_here>* è il valore di **ID applicazione (client)** dell'applicazione registrata.
    - Il valore *\<Enter_the_Tenant_info_here>* deve essere impostato su una delle opzioni seguenti:
       - Se l'applicazione supporta *Account solo in questa directory organizzativa*, sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, *contoso.microsoft.com*).
       - Se l'applicazione supporta *Account in qualsiasi directory organizzativa*, sostituire questo valore con **organizations**.
       - Se l'applicazione supporta *Account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con **common**. Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con **consumers**.

## <a name="test-your-code"></a>Testare il codice

Testare il codice usando uno degli ambienti seguenti.

### <a name="test-with-nodejs"></a>Eseguire test con Node.js

Se non si usa Visual Studio, assicurarsi che il server Web sia stato avviato.

1. Configurare il server per rimanere in ascolto di una porta TCP basata sulla posizione del file *index.html*. Per Node.js, avviare il server Web in modo che rimanga in ascolto sulla porta eseguendo questi comandi da un prompt della riga di comando nella cartella dell'applicazione:

    ```bash
    npm install
    node server.js
    ```
1. Nel browser immettere **http://\<span>\</span>localhost:30662** o **http://\<span>\</span>localhost:{porta}** , dove *porta* è la porta su cui rimane in ascolto il server Web. Dovrebbero essere visibili il contenuto del file *index.html* e il pulsante **Accedi**.

### <a name="test-with-visual-studio"></a>Eseguire test con Visual Studio

Se si usa Visual Studio, selezionare la soluzione del progetto e premere F5 per eseguire il progetto. Il browser si aprirà all'indirizzo http://<span></span>localhost:{porta} e dovrebbe essere visibile il pulsante **Accedi**.

## <a name="test-your-application"></a>Testare l'applicazione

Dopo che il browser ha caricato il file *index.html*, fare clic su **Accedi**. Verrà chiesto di accedere con l'endpoint Microsoft Identity Platform:

![Finestra di accesso all'account JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Specificare il consenso per l'accesso all'applicazione

La prima volta che si accede all'applicazione, viene chiesto di concedere l'accesso al proprio profilo e viene eseguito l'accesso:

![Finestra "Autorizzazioni richieste"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione

Dopo l'accesso, le informazioni del profilo utente vengono restituite nella risposta visualizzata dell'API Microsoft Graph:

![Risultati della chiamata all'API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. Ad esempio, l'API Microsoft Graph richiede l'ambito *Calendars.Read* per generare l'elenco dei calendari dell'utente.

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata *Calendars.Read* alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito *Calendars.Read* alla chiamata `acquireTokenSilent`.

>[!NOTE]
>Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

Se un'API back-end non richiede alcun ambito (scelta non consigliata), è possibile usare *clientId* come ambito nelle chiamate per acquisire i token.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Contribuire al miglioramento di Microsoft Identity Platform. Completare un breve sondaggio di due domande per condividere la propria opinione.

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
