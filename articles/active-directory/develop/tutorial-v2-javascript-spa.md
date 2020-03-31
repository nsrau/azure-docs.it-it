---
title: Esercitazione sulle app a pagina singola JavaScript - Microsoft Identity Platform | Azure
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
ms.openlocfilehash: 6eb144e648e8f5fa1682c353f14686d6f82c7328
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79530445"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Eseguire l'accesso degli utenti e chiamare l'API Microsoft Graph da un'applicazione a singola pagina (SPA) di JavaScript

Questa guida illustra come un'applicazione a pagina singola JavaScript consente di:
- Accedere agli account personali, nonché agli account aziendali e dell'istituto di istruzione 
- Acquisire un token di accesso
- Chiamare l'API Microsoft Graph o altre API che richiedono token di accesso generati dall'*endpoint di Microsoft Identity Platform*

>[!NOTE]
> Se non si ha familiarità con Microsoft Identity Platform, è consigliabile iniziare con l'argomento di avvio rapido [Accedere agli utenti e ottenere un token di accesso in un'applicazione a pagina singola JavaScript](quickstart-v2-javascript.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funzionamento dell'app di esempio generata da questa guida

![Illustra come funziona l'app di esempio generata da questa esercitazione](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Ulteriori informazioni

L'applicazione di esempio creata in questa guida consente a un'applicazione a pagina singola JavaScript di eseguire query sull'API Microsoft Graph o su un'API Web che accetta token dall'endpoint di Microsoft Identity Platform. Per questo scenario, dopo l'accesso di un utente, viene richiesto un token di accesso che viene aggiunto a richieste HTTP tramite l'intestazione dell'autorizzazione. Questo token verrà usato per acquisire il profilo dell'utente e gli indirizzi di posta elettronica tramite l'**API Microsoft Graph**. L'acquisizione e il rinnovo del token vengono gestiti da **Microsoft Authentication Library (MSAL) per JavaScript**.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Librerie

Questa guida usa la libreria seguente:

|Libreria|Descrizione|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Authentication Library di Microsoft per JavaScript|

> [!NOTE]
> *Msal.js* specifica come destinazione l'endpoint di Microsoft Identity Platform, che consente agli account personali, aziendali e dell'istituto di istruzione di eseguire l'accesso e di acquisire i token. L'endpoint di Microsoft Identity Platform presenta [alcune limitazioni](../azuread-dev/azure-ad-endpoint-comparison.md#limitations).
> Per capire le differenze tra gli endpoint v1.0 e v2.0, vedere la [guida al confronto tra gli endpoint](../azuread-dev/azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Impostare il server Web o il progetto

> Si preferisce scaricare questo progetto di esempio? [Scaricare i file di progetto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Per configurare l'esempio di codice prima di eseguirlo, procedere con il [passaggio di configurazione](#register-your-application).

## <a name="prerequisites"></a>Prerequisiti

* Per eseguire l'esercitazione, è necessario un server Web locale, ad esempio [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) o l'integrazione di IIS Express con [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Le istruzioni riportate in questa guida sono basate su un server Web compilato in Node.js. È consigliabile usare [Visual Studio Code](https://code.visualstudio.com/download) come IDE (Integrated Development Environment).

## <a name="create-your-project"></a>Creare il progetto

Assicurarsi che [Node.js](https://nodejs.org/en/download/) sia installato e quindi creare una cartella in cui ospitare l'applicazione. Qui viene implementato un semplice server Web [Express](https://expressjs.com/) per gestire il file `index.html`. 

1. Per prima cosa, usando il terminale integrato di Visual Studio Code, individuare la cartella del progetto e quindi installare Express con NPM.

1. Successivamente, creare un file js denominato `server.js`, quindi aggiungere il codice seguente:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

A questo punto si dispone di un server semplice per gestire l'applicazione a pagina singola. La struttura di cartelle prevista alla fine di questa esercitazione è la seguente:

![rappresentazione testuale della struttura di cartelle dell'applicazione a pagina singola desiderata](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Creare l'interfaccia utente SPA

1. Creare un file `index.html` per l'applicazione a pagina singola JavaScript. Questo file implementa un'interfaccia utente compilata con **Bootstrap 4 Framework** e importa i file script per la configurazione, l'autenticazione e la chiamata API.

   Nel file `index.html` aggiungere il codice seguente:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
         </div>
       </div>
       </div>
       <br>
       <br>
         <div class="col-md-4">
           <div class="list-group" id="list-tab" role="tablist">
           </div>
         </div>
         <div class="col-md-5">
           <div class="tab-content" id="nav-tabContent">
           </div>
         </div>
       </div>
       <br>
       <br>

       <!-- importing bootstrap.js and supporting js libraries -->
       <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
       <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>  
       <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

       <!-- importing app scripts (load order is important) -->
       <script type="text/javascript" src="./authConfig.js"></script>
       <script type="text/javascript" src="./graphConfig.js"></script>
       <script type="text/javascript" src="./ui.js"></script>

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > È possibile sostituire la versione di MSAL.js nello script precedente con l'ultima versione rilasciata disponibile nella pagina delle [versioni di MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
   
2. A questo punto, creare un file js denominato `ui.js`, che consente di accedere e aggiornare gli elementi DOM e aggiungere il codice seguente:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>Registrare l'applicazione

Prima di procedere con l'autenticazione, registrare l'applicazione in **Azure Active Directory**.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e quindi impostare la sessione del portale sul tenant di Azure AD che si vuole usare.
1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
1. Nell'elenco a discesa nella sezione **URI di reindirizzamento** selezionare la piattaforma **Web** e quindi impostare il valore sull'URL dell'applicazione basato sul server Web.
1. Selezionare **Registra**.
1. Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
1. Per questo avvio rapido è necessario abilitare il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md). Nel riquadro sinistro dell'applicazione registrata selezionare **Autenticazione**.
1. In **Impostazioni avanzate**, in **Concessione implicita**, selezionare entrambe le caselle di controllo **Token ID** e **Token di accesso**. I token ID e di accesso sono obbligatori perché l'app deve consentire l'accesso agli utenti e chiamare un'API.
1. Selezionare **Salva**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Impostare l'URL di reindirizzamento per Node.js
>
> Per Node.js, è possibile impostare la porta del server Web nel file *server.js*. Questa esercitazione usa la porta 3000, ma è possibile usarne un'altra qualsiasi disponibile.
>
> Per configurare un URL di reindirizzamento nelle informazioni di registrazione dell'applicazione, tornare al riquadro **Registrazione dell'applicazione** ed eseguire una delle operazioni seguenti:
>
> - Impostare *`http://localhost:3000/`* come **URL di reindirizzamento**.
> - Se si usa una porta TCP personalizzata, usare *`http://localhost:<port>/`* (dove *\<porta>* è il numero della porta TCP personalizzata).
>   1. Copiare il valore del campo **URL**.
>   1. Tornare al riquadro **Registrazione dell'applicazione** e incollare il valore copiato come **URL di reindirizzamento**.
>

### <a name="configure-your-javascript-spa"></a>Configurare JavaScript SPA

Creare un nuovo file js denominato `authConfig.js`, che conterrà i parametri di configurazione per l'autenticazione e aggiungere il codice seguente:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };  

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
    scopes: ["openid", "profile", "User.Read"]
  };

  // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
    scopes: ["Mail.Read"]
  };
```

 Dove:
 - *\<Enter_the_Application_Id_Here>* è il valore di **ID applicazione (client)** dell'applicazione registrata.
 - *\<Enter_the_Cloud_Instance_Id_Here>* è l'istanza del cloud di Azure. Per il cloud di Azure principale o globale è sufficiente immettere *https://login.microsoftonline.com* . Per i cloud **nazionali** (ad esempio, Cina), vedere [Cloud nazionali](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - Il valore *\<Enter_the_Tenant_info_here>* deve essere impostato su una delle opzioni seguenti:
   - Se l'applicazione supporta *Account solo in questa directory organizzativa*, sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, *contoso.microsoft.com*).
   - Se l'applicazione supporta *Account in qualsiasi directory organizzativa*, sostituire questo valore con **organizations**.
   - Se l'applicazione supporta *Account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con **common**. Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con **consumers**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Usare Microsoft Authentication Library (MSAL) per l'accesso degli utenti

Creare un nuovo file js denominato `authPopup.js`, che conterrà la logica di autenticazione e acquisizione dei token, e aggiungere il codice seguente:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
             });
       });
   }

   function seeProfile() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(loginRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
           profileButton.classList.add('d-none');
           mailButton.classList.remove('d-none');
         }).catch(error => {
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>Ulteriori informazioni

Quando un utente seleziona il pulsante **Accedi** per la prima volta, il metodo `signIn` chiama `loginPopup` per farlo accedere. Questo metodo apre una finestra popup con l'*endpoint di Microsoft Identity Platform* per la richiesta e la convalida delle credenziali dell'utente. Dopo aver eseguito l'accesso, l'utente viene reindirizzato alla pagina *index.html* originale. Un token viene ricevuto ed elaborato da `msal.js`, quindi le informazioni contenute nel token vengono memorizzate nella cache. Questo token è noto come *token ID* e contiene informazioni di base sull'utente, ad esempio il nome visualizzato. Se si prevede di usare per qualsiasi scopo i dati forniti da questo token, è necessario verificare che il token venga convalidato dal server back-end per garantire che il token sia stato rilasciato a un utente valido per l'applicazione.

L'applicazione a pagina singola generata da questa guida chiama `acquireTokenSilent` e/o `acquireTokenPopup` per acquisire un *token di accesso* usato per cercare nell'API Microsoft Graph le informazioni sul profilo utente. Se è necessario un esempio che convalida il token ID, vedere [questa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Esempio di GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2") applicazione di esempio in GitHub. L'esempio usa un'API Web ASP.NET per la convalida dei token.

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

Dopo l'accesso iniziale, per non chiedere agli utenti di ripetere l'autenticazione ogni volta che devono richiedere un token per accedere a una risorsa, si dovrà usare *acquireTokenSilent* per acquisire i token nella maggior parte dei casi. In alcune situazioni, tuttavia, è necessario forzare gli utenti a interagire con l'endpoint di Microsoft Identity Platform. Tra gli esempi sono inclusi:

- Gli utenti devono reimmettere le proprie credenziali perché la password è scaduta.
- L'applicazione richiede l'accesso a una risorsa per cui è necessario il consenso dell'utente.
- È necessaria l'autenticazione a due fattori.

La chiamata ad *acquireTokenPopup* apre una finestra popup (oppure *acquireTokenRedirect* reindirizza gli utenti all'endpoint di Microsoft Identity Platform). In questa finestra gli utenti devono interagire confermando le proprie credenziali, fornendo il consenso per la risorsa necessaria o completando l'autenticazione a due fattori.

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Il metodo `acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo aver eseguito il metodo `loginPopup` o `loginRedirect` la prima volta, per le chiamate successive il metodo comunemente usato per ottenere i token usati per accedere a risorse protette è `acquireTokenSilent`. Le chiamate per richiedere o rinnovare i token vengono eseguite in modo invisibile per l'utente. `acquireTokenSilent` potrebbe non riuscire in alcuni casi. È ad esempio possibile che la password dell'utente sia scaduta. L'applicazione può gestire questa eccezione in due modi:

1. Effettuare una chiamata a `acquireTokenPopup` immediatamente, che attiva una richiesta di accesso utente. Questo modello viene in genere usato nelle applicazioni online in cui non è disponibile per l'utente contenuto non autenticato. L'esempio generato da questa configurazione guidata usa questo modello.

1. Le applicazioni possono anche generare un'indicazione visiva per informare l'utente che è necessario un accesso interattivo, in modo da consentire di scegliere il momento più opportuno per accedere. In alternativa, l'applicazione riproverà a eseguire `acquireTokenSilent` in un secondo momento. Questa opzione viene in genere usata quando l'utente può usare altre funzionalità dell'applicazione senza subire interruzioni. Ad esempio, nell'applicazione potrebbe essere disponibile contenuto non autenticato. In questa situazione, l'utente può decidere se vuole eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete.

> [!NOTE]
> Questo avvio rapido usa i metodi `loginPopup` e `acquireTokenPopup` per impostazione predefinita. Se si usa Internet Explorer come browser, è consigliabile usare i metodi `loginRedirect` e `acquireTokenRedirect` a causa di un [problema noto](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) correlato al modo in cui Internet Explorer gestisce le finestre popup. Per informazioni su come ottenere lo stesso risultato usando `Redirect methods`, vedere [qui](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js). 
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Chiamare l'API Microsoft Graph usando il token appena acquisito

1. Per prima cosa, creare un file js denominato `graphConfig.js`, in cui archiviare gli endpoint REST. Aggiungere il codice seguente:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Dove:
   - *\<Enter_the_Graph_Endpoint_Here>* è l'istanza dell'API Microsoft Graph. Per l'endpoint globale dell'API Microsoft Graph, è sufficiente sostituire questa stringa con `https://graph.microsoft.com`. Per le distribuzioni cloud nazionali, fare riferimento alla [documentazione dell'API Graph](https://docs.microsoft.com/graph/deployments).

1. Successivamente, creare un file js denominato `graph.js`, che effettuerà una chiamata REST all'API Microsoft Graph,e aggiungere il codice seguente:

   ```javascript
   function callMSGraph(endpoint, token, callback) {
     const headers = new Headers();
     const bearer = `Bearer ${token}`;

     headers.append("Authorization", bearer);

     const options = {
         method: "GET",
         headers: headers
     };

     console.log('request made to Graph API at: ' + new Date().toString());
  
     fetch(endpoint, options)
       .then(response => response.json())
       .then(response => callback(response, endpoint))
       .catch(error => console.log(error))
   }
   ```

<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Altre informazioni sull'esecuzione di una chiamata REST a un'API protetta

Nell'applicazione di esempio creata in questa guida, viene usato il metodo `callMSGraph()` per eseguire una richiesta HTTP `GET` per una risorsa protetta che richiede un token. La richiesta restituisce quindi il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. Per l'applicazione di esempio creata da questa guida, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che visualizza informazioni sul profilo dell'utente.

<!--end-collapse-->

## <a name="test-your-code"></a>Testare il codice

1. Configurare il server per rimanere in ascolto di una porta TCP basata sulla posizione del file *index.html*. Per Node.js, avviare il server Web in modo che rimanga in ascolto sulla porta eseguendo questi comandi da un prompt della riga di comando nella cartella dell'applicazione:

   ```bash
   npm install
   npm start
   ```
1. Nel browser immettere **http://localhost:3000** o **http://localhost:{port}** , dove *porta* è la porta su cui il server Web è in ascolto. Dovrebbero essere visibili il contenuto del file *index.html* e il pulsante **Accedi**.

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

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. Ad esempio, l'API Microsoft Graph richiede l'ambito *Mail.Read* per generare l'elenco degli indirizzi di posta elettronica dell'utente.

> [!NOTE]
> Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

Se un'API back-end non richiede alcun ambito (scelta non consigliata), è possibile usare *clientId* come ambito nelle chiamate per acquisire i token.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Contribuire al miglioramento di Microsoft Identity Platform. Completare un breve sondaggio di due domande per condividere la propria opinione.

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
