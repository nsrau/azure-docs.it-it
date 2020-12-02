---
title: "Esercitazione: Creare un'app a pagina singola JavaScript che usa il flusso del codice di autenticazione | Azure"
titleSuffix: Microsoft identity platform
description: In questa esercitazione si creerà un'app a pagina singola JavaScript che consente agli utenti di accedere e usa il flusso del codice di autenticazione per ottenere un token di accesso da Microsoft Identity Platform e chiamare l'API Microsoft Graph.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 6b8a9cbfd3e7057f0d85d5f4e19fea3aa4fbe90b
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980219"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Esercitazione: Eseguire l'accesso degli utenti e chiamare l'API Microsoft Graph da un'applicazione a pagina singola JavaScript usando il flusso del codice di autenticazione

In questa esercitazione verrà creata un'applicazione a pagina singola JavaScript che concede l'accesso agli utenti e chiama Microsoft Graph usando il flusso del codice di autorizzazione con PKCE. L'applicazione a pagina singola creata usa Microsoft Authentication Library (MSAL) per JavaScript v2.0.

In questa esercitazione:
> [!div class="checklist"]
> * Eseguire il flusso del codice di autorizzazione OAuth 2.0 con PKCE
> * Accedere agli account Microsoft personali, oltre che agli account aziendali e dell'istituto di istruzione
> * Acquisire un token di accesso
> * Chiamare l'API Microsoft Graph o le proprie API che richiedono token di accesso ottenuti dall'endpoint di Microsoft Identity Platform

MSAL.js 2.0 rappresenta un miglioramento rispetto a MSAL.js 1.0 in quanto supporta il flusso del codice di autorizzazione nel browser invece del flusso di concessione implicita. MSAL.js 2.0 **NON** supporta il flusso implicito.

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/en/download/) per l'esecuzione di un server Web locale
* [Visual Studio Code](https://code.visualstudio.com/download) o un altro editor di codice

## <a name="how-the-tutorial-app-works"></a>Funzionamento dell'app dell'esercitazione

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagramma che mostra il flusso del codice di autorizzazione in un'applicazione a pagina singola":::

In questa esercitazione viene creata a un'applicazione a pagina singola JavaScript che esegue query sull'API Microsoft Graph acquisendo token di sicurezza dall'endpoint di Microsoft Identity Platform. Per questo scenario, dopo l'accesso di un utente, viene richiesto e aggiunto un token di accesso alle richieste HTTP nell'intestazione dell'autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL.js) per JavaScript.

Questa esercitazione la libreria seguente:

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser): pacchetto del browser di Microsoft Authentication Library per JavaScript v2.0

## <a name="get-the-completed-code-sample"></a>Ottenere l'esempio di codice completo

Si preferisce invece scaricare il progetto di esempio completo di questa esercitazione? Per eseguire il progetto tramite un server Web locale, come Node.js, clonare il repository [ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2):

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Quindi, per configurare l'esempio di codice prima di eseguirlo, procedere con il [passaggio di configurazione](#register-your-application).

Per continuare con l'esercitazione e compilare l'applicazione manualmente, passare alla sezione successiva, [Prerequisiti](#prerequisites).

## <a name="create-your-project"></a>Creare il progetto

Dopo aver installato [Node.js](https://nodejs.org/en/download/), creare una cartella in cui ospitare l'applicazione, ad esempio *msal-spa-tutorial*.

Implementare quindi un piccolo server Web [Express](https://expressjs.com/) per gestire il file *index.html*.

1. Per prima cosa, passare alla cartella del progetto nel terminale e quindi eseguire i comandi `npm` seguenti:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Successivamente, creare un file denominato *server.js* e aggiungere il codice seguente:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

A questo punto è disponibile un piccolo server Web per gestire l'applicazione a pagina singola. Dopo aver completato il resto dell'esercitazione, la struttura di file e cartelle del progetto sarà simile alla seguente:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>Creare l'interfaccia utente SPA

1. Creare una cartella *app* nella directory del progetto e al suo interno creare un file *index.html* per l'applicazione a pagina singola JavaScript. Questo file implementa un'interfaccia utente creata con **Bootstrap 4 Framework** e importa i file di script per la configurazione, l'autenticazione e le chiamate API.

    Nel file *index.html* aggiungere il codice seguente:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
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

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. Quindi, sempre nella cartella *app*, creare un file denominato *ui.js* e aggiungere il codice seguente. Questo file accede agli elementi DOM e li aggiorna.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
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

Seguire la procedura descritta in [Applicazione a singola pagina: registrazione dell'app](scenario-spa-app-registration.md) per creare una registrazione per l'applicazione a pagina singola.

Nel passaggio [URI di reindirizzamento: MSAL. JS 2.0 con flusso del codice di autenticazione](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) immettere `http://localhost:3000`, la posizione predefinita in cui viene eseguita l'applicazione di questa esercitazione.

Se si vuole usare una porta diversa, immettere `http://localhost:<port>`, dove `<port>` è il numero di porta TCP preferito. Se si specifica un numero di porta diverso da `3000`, aggiornare anche *server.js* con tale numero.

### <a name="configure-your-javascript-spa"></a>Configurare JavaScript SPA

Creare un file denominato *authConfig.js* nella cartella *app*, che conterrà i parametri di configurazione per l'autenticazione, e quindi aggiungere il codice seguente:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

Modificare i valori nella sezione `msalConfig` come descritto di seguito:

- `Enter_the_Application_Id_Here`: l'**ID applicazione (client)** per l'applicazione registrata.
- `Enter_the_Cloud_Instance_Id_Here`: l'istanza del cloud di Azure in cui è registrata l'applicazione.
  - Per il cloud principale (o *globale*) di Azure, immettere `https://login.microsoftonline.com`.
  - Peri cloud **nazionali**, ad esempio Cina, è possibile trovare i valori appropriati nella pagina [Cloud nazionali](authentication-national-cloud.md).
- `Enter_the_Tenant_info_here` deve essere uno dei seguenti:
  - Se l'applicazione supporta *account nella directory organizzativa corrente*, sostituire questo valore con l'**ID tenant** o il **nome del tenant**. Ad esempio: `contoso.microsoft.com`.
  - Se l'applicazione supporta *account in qualsiasi directory organizzativa*, sostituire questo valore con `organizations`.
  - Se l'applicazione supporta *account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con `common`.
  - Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con `consumers`.
- `Enter_the_Redirect_Uri_Here` è `http://localhost:3000`.

Se si usa il cloud globale di Azure, il valore `authority` nel file *authConfig.js* dovrà essere simile al seguente:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Sempre nella cartella *app* creare un file denominato *graphConfig.js*. Aggiungere il codice seguente per fornire all'applicazione i parametri di configurazione per chiamare l'API Microsoft Graph:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Modificare i valori nella sezione `graphConfig` come descritto di seguito:

- `Enter_the_Graph_Endpoint_Here` è l'istanza dell'API Microsoft Graph con cui l'applicazione dovrà comunicare.
  - Per l'endpoint API Microsoft Graph **globale**, sostituire entrambe le istanze di questa stringa con `https://graph.microsoft.com`.
  - Per gli endpoint delle distribuzioni di cloud **nazionali**, vedere [Distribuzioni di cloud nazionali](https://docs.microsoft.com/graph/deployments) nella documentazione di Microsoft Graph.

Se si usa l'endpoint globale, i valori `graphMeEndpoint` e `graphMailEndpoint` nel file *graphConfig.js* dovranno essere simili ai seguenti:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-microsoft-authentication-library-msal-to-sign-in-user"></a>Usare Microsoft Authentication Library (MSAL) per l'accesso dell'utente

### <a name="pop-up"></a>Popup

Nella cartella *app* creare un file denominato *authPopup.js* e aggiungere il codice di autenticazione e di acquisizione di token seguente per la finestra popup di accesso:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>reindirizzamento

Creare un file denominato *authRedirect.js* nella cartella *app* e aggiungere il codice di autenticazione e di acquisizione di token seguente per il reindirizzamento dell'account di accesso:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>Funzionamento del codice

Quando un utente seleziona il pulsante **Accedi** per la prima volta, il metodo `signIn` chiama `loginPopup` per farlo accedere. Il metodo `loginPopup` apre una finestra popup con l'*endpoint di Microsoft Identity Platform* per la richiesta e la convalida delle credenziali dell'utente. Dopo aver eseguito l'accesso, *msal.js* avvia il [flusso di Codice di autorizzazione](v2-oauth2-auth-code-flow.md).

A questo punto, un codice di autorizzazione protetto da PKCE viene inviato all'endpoint token protetto da CORS e viene sostituito con i token. L'applicazione riceve un token ID, un token di accesso e un token di aggiornamento, che vengono quindi elaborati da *msal.js*, quindi le informazioni contenute nei token vengono memorizzate nella cache.

Il token ID contiene informazioni di base sull'utente, ad esempio il nome visualizzato. Se si prevede di usare i dati forniti dal token ID, il server back-end *deve* convalidarlo per garantire che sia stato emesso a un utente valido per l'applicazione.

Il token di accesso ha una durata limitata e scade dopo 24 ore. Il token di aggiornamento può essere usato per acquisire automaticamente nuovi token di accesso.

L'applicazione a pagina singola creata in questa esercitazione chiama `acquireTokenSilent` e/o `acquireTokenPopup` per acquisire un *token di accesso* usato per eseguire una query sull'API Microsoft Graph e trovare le informazioni del profilo utente. Se è necessario un esempio che convalidi il token ID, vedere l'applicazione di esempio [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) in GitHub. Nell'esempio viene usata un'API Web ASP.NET per la convalida dei token.

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

Dopo l'accesso iniziale, l'app non dovrà chiedere agli utenti di ripetere l'autenticazione (ossia, non dovrà richiedere un token) ogni volta che vogliono accedere a una risorsa protetta. Per impedire tali richieste di riautenticazione, chiamare `acquireTokenSilent`. In alcune situazioni, tuttavia, può essere necessario forzare gli utenti a interagire con l'endpoint di Microsoft Identity Platform. Ad esempio:

- Gli utenti devono reimmettere le proprie credenziali perché la password è scaduta.
- L'applicazione richiede l'accesso a una risorsa per cui è necessario il consenso dell'utente.
- È necessaria l'autenticazione a due fattori.

Se si chiama `acquireTokenPopup`, viene aperta una finestra popup oppure `acquireTokenRedirect` reindirizza gli utenti all'endpoint di Microsoft Identity Platform. In questa finestra gli utenti devono interagire confermando le proprie credenziali, fornendo il consenso per la risorsa necessaria o completando l'autenticazione a due fattori.

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Il metodo `acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo aver eseguito il metodo `loginPopup` o `loginRedirect` la prima volta, per le chiamate successive il metodo comunemente usato per ottenere i token usati per accedere a risorse protette è `acquireTokenSilent`. Le chiamate per richiedere o rinnovare i token vengono eseguite in modo invisibile per l'utente. `acquireTokenSilent` potrebbe non riuscire in alcuni casi. È ad esempio possibile che la password dell'utente sia scaduta. L'applicazione può gestire questa eccezione in due modi:

1. Effettuare immediatamente una chiamata a `acquireTokenPopup` per attivare una richiesta di accesso utente. Questo modello viene in genere usato nelle applicazioni online in cui non è disponibile per l'utente contenuto non autenticato. L'esempio generato da questa configurazione guidata usa questo modello.
1. Usare un'indicazione visiva per informare l'utente che è necessario un accesso interattivo, in modo da consentire di scegliere il momento più opportuno per accedere. In alternativa, l'applicazione riproverà a eseguire `acquireTokenSilent` in un secondo momento. Questa tecnica viene in genere usata quando l'utente può usare altre funzionalità dell'applicazione senza subire interruzioni. Ad esempio, nell'applicazione potrebbe essere disponibile contenuto non autenticato. In questa situazione, l'utente può decidere se vuole eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete.

> [!NOTE]
> Questa esercitazione usa i metodi `loginPopup` e `acquireTokenPopup` per impostazione predefinita. Se si usa Internet Explorer, è consigliabile usare i metodi `loginRedirect` e `acquireTokenRedirect` a causa di un [problema noto](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) di Internet Explorer con le finestre popup. Ad esempio per ottenere lo stesso risultato con metodi di reindirizzamento diversi, vedere [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) in GitHub.

## <a name="call-the-microsoft-graph-api"></a>Chiamare l'API Microsoft Graph

Creare un file denominato *graph.js* nella cartella *app* e aggiungere il codice seguente per eseguire chiamate REST all'API Microsoft Graph:

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
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
        .catch(error => console.log(error));
}
```

Nell'applicazione di esempio creata in questa esercitazione viene usato il metodo `callMSGraph()` per eseguire una richiesta HTTP `GET` di una risorsa protetta che richiede un token. La richiesta restituisce quindi il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. Nell'applicazione di esempio creata in questa esercitazione la risorsa protetta è l'endpoint *me* dell'API Microsoft Graph, che visualizza informazioni sul profilo dell'utente connesso.

## <a name="test-your-application"></a>Testare l'applicazione

La creazione dell'applicazione è stata completata ed è ora possibile avviare il server Web Node.js e testarne la funzionalità.

1. Avviare il server Web Node.js eseguendo i comandi seguenti all'interno della radice della cartella del progetto:

   ```console
   npm start
   ```
1. Nel browser passare a `http://localhost:3000` o `http://localhost:<port>`, dove `<port>` è la porta su cui il server Web è in ascolto. Dovrebbero essere visibili il contenuto del file *index.html* e il pulsante **Accedi**.

### <a name="sign-in-to-the-application"></a>Accedere all'applicazione

Dopo che il browser ha caricato il file *index.html*, fare clic su **Accedi**. Verrà chiesto di accedere con l'endpoint Microsoft Identity Platform:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Web browser che visualizza la finestra di dialogo di accesso":::

### <a name="provide-consent-for-application-access"></a>Specificare il consenso per l'accesso all'applicazione

La prima volta che si accede all'applicazione, viene chiesto di concedere l'accesso al proprio profilo e viene eseguito l'accesso:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Finestra di dialogo di contenuto visualizzata nel Web browser":::

Se si acconsente alle autorizzazioni richieste, nelle applicazioni Web viene visualizzato il nome utente, a indicare che l'accesso è riuscito:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Risultati di un accesso riuscito nel Web browser":::

### <a name="call-the-graph-api"></a>Chiamare l'API Graph

Dopo aver eseguito l'accesso, selezionare **See Profile** (Vedi profilo) per visualizzare le informazioni del profilo utente restituite in una risposta dalla chiamata all'API Microsoft Graph:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Informazioni del profilo di Microsoft Graph visualizzate nel browser":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di Azure. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. Ad esempio, l'API Microsoft Graph richiede l'ambito *Mail.Read* per visualizzare la posta elettronica dell'utente.

Quando si aggiungono ambiti, agli utenti potrebbe essere richiesto di fornire un consenso aggiuntivo.

Se per un'API back-end non è richiesto alcun ambito (non consigliabile), è possibile usare `clientId` come ambito nelle chiamate per acquisire i token.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Se si vuole approfondire lo sviluppo di applicazioni a pagina singola JavaScript con Microsoft Identity Platform, vedere la serie di scenari in più parti:

> [!div class="nextstepaction"]
> [Scenario: Applicazione a pagina singola](scenario-spa-overview.md)
