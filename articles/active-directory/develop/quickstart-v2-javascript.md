---
title: Consentire l'accesso degli utenti nelle applicazioni a pagina singola JavaScript | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come un'app JavaScript possa chiamare un'API che richiede token di accesso tramite Microsoft Identity Platform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.openlocfilehash: ed94b44972ffc8c53ce96c0240f11ad44535531b
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434606"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Avvio rapido: Accedere agli utenti e ottenere un token di accesso in un'applicazione a pagina singola JavaScript

In questa guida di avvio rapido si usa codice di esempio per dimostrare in che modo un'applicazione a pagina singola JavaScript possa concedere l'accesso ad account personali, aziendali o dell'istituto di istruzione. Un'applicazione a pagina singola può anche ottenere un token di accesso per chiamare l'API Microsoft Graph o un'API Web. Per un'illustrazione, vedere [Funzionamento dell'esempio](#how-the-sample-works).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure - [Creare una sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (per modificare i file di progetto)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrare e scaricare l'app dell'avvio rapido
> Per avviare l'applicazione di avvio rapido, usare una delle opzioni seguenti.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1 (Modalità rapida): Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
> 1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e quindi impostare la sessione del portale sul tenant di Azure Active Directory (Azure AD) che si vuole usare.
> 1. Passare al nuovo riquadro [Portale di Azure - Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Immettere un nome per l'applicazione.
> 1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
> 1. Selezionare **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2 (Manuale): Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
>
> 1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
>
> 1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e quindi impostare la sessione del portale sul tenant di Azure AD che si vuole usare.
> 1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
> 1. Selezionare **Nuova registrazione**.
> 1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
> 1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
> 1. Selezionare **Registra**. Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
> 1. Per questo avvio rapido è necessario abilitare il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md). Nel riquadro sinistro dell'applicazione registrata selezionare **Autenticazione**.
> 1. In **Configurazioni della piattaforma** selezionare **Aggiungi una piattaforma**. Viene visualizzato un pannello sulla sinistra. Selezionare l'area **Applicazioni Web**.
> 1. Sempre a sinistra, impostare il valore di **URI di reindirizzamento** su `http://localhost:3000/`. Selezionare quindi **Token di accesso** e **Token ID**.
> 1. Selezionare **Configura**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per il funzionamento dell'esempio di codice di questa guida di avvio rapido, è necessario aggiungere un oggetto `redirectUri` come `http://localhost:3000/` e abilitare l'opzione **Concessione implicita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-javascript/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-project"></a>Passaggio 2: Scaricare il progetto

> [!div renderon="docs"]
> Per eseguire il progetto con un server Web, ad esempio Node.js, [scaricare i file di progetto di base](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal"]
> Eseguire il progetto con un server Web usando Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Passaggio 3: Configurare l'app JavaScript
>
> Nella cartella *JavaScriptSPA* modificare *authConfig.js* e impostare i valori `clientID`, `authority` e `redirectUri` in `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Dove:
> - *\<Enter_the_Application_Id_Here>* è l'**ID applicazione (client)** per l'applicazione registrata.
> - *\<Enter_the_Cloud_Instance_Id_Here>* è l'istanza del cloud di Azure. Per il cloud di Azure principale o globale è sufficiente immettere *https://login.microsoftonline.com* . Per i cloud **nazionali** (ad esempio, Cina), vedere [Cloud nazionali](./authentication-national-cloud.md).
> - *\<Enter_the_Tenant_info_here>* è impostato su una delle opzioni seguenti:
>    - Se l'applicazione supporta *Account solo in questa directory organizzativa*, sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, *contoso.microsoft.com*).
>    - Se l'applicazione supporta *Account in qualsiasi directory organizzativa*, sostituire questo valore con **organizations**.
>    - Se l'applicazione supporta *Account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con **common**. Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con **consumers**.
>
> > [!TIP]
> > Per trovare i valori di **ID applicazione (client)** , **ID della directory (tenant)** e **Tipi di account supportati**, passare alla pagina di **panoramica** dell'app nel portale di Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app.

> [!div renderon="docs"]
>
> Quindi, sempre nella stessa cartella modificare il file *graphConfig.js* per impostare `graphMeEndpoint` e `graphMeEndpoint` per l'oggetto `apiConfig`.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Dove:
> - *\<Enter_the_Graph_Endpoint_Here>* è l'endpoint a cui verranno eseguite le chiamate API. Per il servizio API Microsoft Graph principale o globale, è sufficiente immettere `https://graph.microsoft.com`. Per altre informazioni, vedere [Distribuzione di un cloud nazionale](/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Passaggio 4: Eseguire il progetto

Eseguire il progetto con un server Web usando [Node.js](https://nodejs.org/en/download/):

1. Per avviare il server, eseguire il comando seguente nella directory del progetto:
    ```cmd
    npm install
    npm start
    ```
1. Aprire un Web browser e passare a `http://localhost:3000/`.

1. Fare clic su **Accedi** per effettuare l'accesso e chiamare quindi l'API Microsoft Graph.

Dopo che il browser ha caricato l'applicazione, fare clic su **Accedi**. Al primo accesso viene chiesto di concedere il proprio consenso per consentire all'applicazione di accedere al profilo e completare la procedura di accesso. Dopo aver effettuato l'accesso, nella pagina devono essere visualizzate le informazioni del profilo utente.

## <a name="more-information"></a>Ulteriori informazioni

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio

![Funzionamento dell'applicazione a pagina singola JavaScript di esempio: 1. L'applicazione a pagina singola avvia l'accesso. 2. L'applicazione a pagina singola acquisisce un token ID da Microsoft Identity Platform. 3. L'applicazione a pagina singola chiama il token di acquisizione. 4. Microsoft Identity Platform restituisce un token di accesso per l'applicazione a pagina singola. 5. L'applicazione a pagina singola crea una richiesta HTTP GET con il token di accesso per l'API Microsoft Graph. 6. L'API Graph restituisce una risposta HTTP all'applicazione a pagina singola.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

Con la libreria MSAL è possibile concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform. Il file *index.html* dell'argomento di avvio rapido contiene un riferimento alla libreria:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> È possibile sostituire la versione precedente con l'ultima versione rilasciata disponibile nella pagina delle [versioni di MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

In alternativa, se Node.js è già installato, è possibile scaricare l'ultima versione tramite Node.js Package Manager (npm):

```cmd
npm install msal
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

Il codice di avvio rapido illustra anche come inizializzare la libreria MSAL:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Where  | Descrizione |
> |---------|---------|
> |`clientId`     | ID dell'applicazione registrata nel portale di Azure.|
> |`authority`    | (Facoltativo) URL dell'autorità che supporta i tipi di account, come descritto nella sezione di configurazione precedente. L'autorità predefinita è `https://login.microsoftonline.com/common`. |
> |`redirectUri`     | L'oggetto reply/redirectUri della registrazione dell'applicazione configurato. In questo caso, `http://localhost:3000/`. |
> |`cacheLocation`  | (Facoltativo) Valore che consente di impostare l'archiviazione del browser per lo stato di autenticazione. L'impostazione predefinita è sessionStorage.   |
> |`storeAuthStateInCookie`  | (Facoltativo) Libreria che archivia lo stato della richiesta di autenticazione necessario per la convalida dei flussi di autenticazione nei cookie del browser. Questo cookie viene impostato per i browser Internet Explorer ed Edge per attenuare alcuni [problemi noti](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Per altre informazioni sulle opzioni configurabili disponibili, vedere [Inizializzare le applicazioni client](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Consentire l'accesso degli utenti

Il frammento di codice seguente illustra come consentire l'accesso degli utenti:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Where  | Descrizione |
> |---------|---------|
> | `scopes`   | (Facoltativo) Contiene gli ambiti richiesti per il consenso dell'utente al momento dell'accesso, ad esempio: `[ "user.read" ]` per Microsoft Graph o `[ "<Application ID URL>/scope" ]` per le API Web personalizzate, ovvero `api://<Application ID>/access_as_user`. |

> [!TIP]
> In alternativa, è possibile usare il metodo `loginRedirect` per reindirizzare alla pagina corrente alla pagina di accesso anziché a una finestra popup.

### <a name="request-tokens"></a>Richiedere token

In MSAL sono disponibili tre metodi per acquisire i token: `acquireTokenRedirect`, `acquireTokenPopup` e `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Il metodo `acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo aver eseguito il metodo `loginRedirect` o `loginPopup` la prima volta, per le chiamate successive il metodo comunemente usato per ottenere i token usati per accedere a risorse protette è `acquireTokenSilent`. Le chiamate per richiedere o rinnovare i token vengono eseguite in modo invisibile per l'utente.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Where  | Descrizione |
> |---------|---------|
> | `scopes`   | Contiene gli ambiti da restituire nel token di accesso per l'API, ad esempio: `[ "mail.read" ]` per Microsoft Graph o `[ "<Application ID URL>/scope" ]` per le API Web personalizzate, ovvero `api://<Application ID>/access_as_user`.|

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

In alcune situazioni è necessario forzare gli utenti a interagire con l'endpoint di Microsoft Identity Platform. Ad esempio:
* Un utente deve immettere nuovamente le credenziali perché la password è scaduta.
* L'applicazione richiede l'accesso ad ambiti di risorse aggiuntivi per cui è necessario il consenso dell'utente.
* È necessaria l'autenticazione a due fattori.

Per la maggior parte delle applicazioni, l'approccio consigliato è quello di chiamare prima `acquireTokenSilent`, quindi individuare l'eccezione e infine chiamare `acquireTokenPopup` (o `acquireTokenRedirect`) per avviare una richiesta interattiva.

Se si chiama `acquireTokenPopup`, viene visualizzata una finestra popup di accesso, mentre con `acquireTokenRedirect` gli utenti vengono reindirizzati all'endpoint di Microsoft Identity Platform. Nella finestra popup gli utenti devono interagire confermando le proprie credenziali, concedendo il consenso per la risorsa necessaria o completando l'autenticazione a due fattori.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Questo argomento di avvio rapido usa i metodi `loginRedirect` e `acquireTokenRedirect` con Microsoft Internet Explorer a causa di un [problema noto](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) correlato alla gestione delle finestre popup da questo browser.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sulla creazione dell'applicazione per questo argomento di avvio rapido, vedere:

> [!div class="nextstepaction"]
> [Esercitazione per l'accesso e la chiamata a MS Graph](./tutorial-v2-javascript-spa.md)

Per consultare il repository MSAL per documentazione, domande frequenti, problemi e altro ancora:

> [!div class="nextstepaction"]
> [Repository MSAL.js di GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js)
