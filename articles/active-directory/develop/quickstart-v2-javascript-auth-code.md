---
title: Consentire l'accesso degli utenti nelle applicazioni a pagina singola JavaScript con il codice di autorizzazione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come un'app JavaScript possa chiamare un'API che richiede token di accesso tramite Microsoft Identity Platform.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: 9663c11508b0478a67f528cb301d705a3125e4f6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871516"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Avvio rapido: Accedere agli utenti e ottenere un token di accesso in un'applicazione a pagina singola JavaScript con il flusso di Codice di autorizzazione 

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità generale.


In questa guida di avvio rapido viene usato MSAL.js 2.0 con il flusso di Codice di autorizzazione. Per usare MSAL.js 1.0 con il flusso implicito, vedere [questo avvio rapido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

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
> 1. Accedere al [portale di Azure](https://portal.azure.com).
> 1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e quindi impostare la sessione del portale sul tenant di Azure Active Directory (Azure AD) che si vuole usare.
> 1. Selezionare [Registrazioni per l'app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Immettere un nome per l'applicazione.
> 1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
> 1. Selezionare **Registra**.
> 1. Passare al riquadro dell'avvio rapido e seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2 (Manuale): Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
>
> 1. Accedere al [portale di Azure](https://portal.azure.com).
>
> 1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e quindi impostare la sessione del portale sul tenant di Azure AD che si vuole usare.
> 1. Selezionare [Registrazioni per l'app](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Selezionare **Nuova registrazione**.
> 1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
> 1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
> 1. Selezionare **Registra**. Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
> 1. Nel riquadro sinistro dell'applicazione registrata selezionare **Autenticazione**.
> 1. In **Configurazioni della piattaforma** selezionare **Aggiungi una piattaforma**. Viene visualizzato un pannello sulla sinistra. Selezionare quindi l'area **Applicazioni a pagina singola**.
> 1. Sempre a sinistra, impostare il valore di **URI di reindirizzamento** su `http://localhost:3000/`. 
> 1. Selezionare **Configura**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per consentire il funzionamento dell'esempio di codice di questa guida di avvio rapido, è necessario aggiungere un oggetto `redirectUri` impostato su `http://localhost:3000/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-javascript/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-project"></a>Passaggio 2: Scaricare il progetto

> [!div renderon="docs"]
> Per eseguire il progetto con un server Web, ad esempio Node.js, [scaricare i file di progetto di base](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Eseguire il progetto con un server Web usando Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Passaggio 3: Configurare l'app JavaScript
>
> Nella cartella *app* modificare *authConfig.js* e impostare i valori `clientID`, `authority` e `redirectUri` in `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > :::no-loc text="Enter_the_Supported_Account_Info_Here":::

> [!div renderon="docs"]
>
> Dove:
> - *\<Enter_the_Application_Id_Here>* è il valore di **ID applicazione (client)** dell'applicazione registrata.
> - *\<Enter_the_Cloud_Instance_Id_Here>* è l'istanza del cloud di Azure. Per il cloud di Azure principale o globale è sufficiente immettere *https://login.microsoftonline.com/* . Per i cloud **nazionali** (ad esempio, Cina), vedere [Cloud nazionali](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - Il valore *\<Enter_the_Tenant_info_here>* deve essere impostato su una delle opzioni seguenti:
>    - Se l'applicazione supporta *Account solo in questa directory organizzativa*, sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, *contoso.microsoft.com*).
>    - Se l'applicazione supporta *Account in qualsiasi directory organizzativa*, sostituire questo valore con **organizations**.
>    - Se l'applicazione supporta *Account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con **common**. Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con **consumers**.
> - Sostituire *\<Enter_the_Redirect_Uri_Here>* con `http://localhost:3000`
> > [!TIP]
> > Per trovare i valori di **ID applicazione (client)** , **ID della directory (tenant)** e **Tipi di account supportati**, passare alla pagina di **panoramica** di registrazione dell'app nel portale di Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app.

> [!div renderon="docs"]
>
> Quindi, sempre nella stessa cartella modificare il file *graphConfig.js* per impostare `graphMeEndpoint` e `graphMailEndpoint` per l'oggetto `apiConfig`.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
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
> *\<Enter_the_Graph_Endpoint_Here >* è l'endpoint in cui verranno eseguire le chiamate API. Per il servizio API Microsoft Graph principale o globale, immettere `https://graph.microsoft.com`. Per altre informazioni, vedere [Distribuzione di un cloud nazionale](https://docs.microsoft.com/graph/deployments).
>
> #### <a name="step-4-run-the-project"></a>Passaggio 4: Eseguire il progetto

Eseguire il progetto con un server Web usando [Node.js](https://nodejs.org/en/download/):

1. Per avviare il server, eseguire i comandi seguenti nella directory del progetto:
    ```bash
    npm install
    npm start
    ```
1. Passare a `http://localhost:3000/`.

1. Fare clic su **Accedi** per avviare il processo di accesso e quindi chiamare l'API Microsoft Graph.

    Al primo accesso viene chiesto di concedere il proprio consenso per consentire all'applicazione di accedere al profilo e completare la procedura di accesso. Dopo aver effettuato l'accesso, nella pagina devono essere visualizzate le informazioni del profilo utente.

## <a name="more-information"></a>Ulteriori informazioni

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio

![Funzionamento dell'applicazione a pagina singola JavaScript di esempio: 1. L'applicazione a pagina singola avvia l'accesso. 2. L'applicazione a pagina singola acquisisce un token ID da Microsoft Identity Platform. 3. L'applicazione a pagina singola chiama il token di acquisizione. 4. Microsoft Identity Platform restituisce un token di accesso per l'applicazione a pagina singola. 5. L'applicazione a pagina singola crea una richiesta HTTP GET con il token di accesso per l'API Microsoft Graph. 6. L'API Graph restituisce una risposta HTTP all'applicazione a pagina singola.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

Con la libreria MSAL.js è possibile concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform. Il file *index.html* dell'esempio contiene un riferimento alla libreria:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```
> [!TIP]
> È possibile sostituire la versione precedente con l'ultima versione rilasciata disponibile nella pagina delle [versioni di MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

In alternativa, se Node.js è già installato, è possibile scaricare l'ultima versione usando Node.js Package Manager (npm):

```batch
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Passaggi successivi

Il [repository GitHub MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) contiene documentazione aggiuntiva, domande frequenti e offre assistenza per la risoluzione dei problemi.

Per informazioni dettagliate sulla creazione dell'applicazione per questo argomento di avvio rapido, vedere:

> [!div class="nextstepaction"]
> [Esercitazione per l'accesso e la chiamata a MS Graph](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
