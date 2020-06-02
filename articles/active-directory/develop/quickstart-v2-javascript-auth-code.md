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
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript
ms.openlocfilehash: 0ba4531ed15630a8887cb7be843a00ba23a439cc
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682047"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Guida introduttiva: Consentire l'accesso agli utenti e ottenere un token di accesso in un'applicazione a pagina singola JavaScript con il flusso del codice di autorizzazione

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità generale.

In questa guida introduttiva viene eseguito un esempio di codice che illustra in che modo un'applicazione a pagina singola JavaScript può consentire l'accesso agli utenti con account personali, account aziendali e account dell'istituto di istruzione tramite il flusso del codice di autorizzazione. L'esempio di codice illustra anche come ottenere un token di accesso per chiamare un'API Web, in questo caso l'API Microsoft Graph. Per un'illustrazione, vedere [Funzionamento dell'esempio](#how-the-sample-works).

In questa guida introduttiva viene usato MSAL.js 2.0 con il flusso del codice di autorizzazione. Per una guida introduttiva simile che usi MSAL.js 1.0 con il flusso implicito, vedere [Guida introduttiva: Consentire l'accesso degli utenti nelle applicazioni a pagina singola JavaScript](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure - [Creare una sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) o un altro editor di codice

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
> 1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e quindi impostare la sessione del portale sul tenant di Azure AD che si vuole usare.
> 1. Selezionare [Registrazioni per l'app](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Selezionare **Nuova registrazione**.
> 1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
> 1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
> 1. Selezionare **Registra**. Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
> 1. Nel riquadro sinistro dell'applicazione registrata selezionare **Autenticazione**.
> 1. In **Configurazioni della piattaforma** selezionare **Aggiungi una piattaforma**. Nel riquadro visualizzato selezionare **Applicazione a pagina singola**.
> 1. Impostare il valore del campo **URI di reindirizzamento** su `http://localhost:3000/`.
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
> Per eseguire il progetto con un server Web, ad esempio Node.js, [scaricare i file di progetto di base](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Eseguire il progetto con un server Web usando Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Passaggio 3: Configurare l'app JavaScript
>
> Nella cartella *app* aprire il file *authConfig.js* e aggiornare i valori `clientID`, `authority` e `redirectUri` nell'oggetto `msalConfig`.
>
> ```javascript
> // Config object to be passed to Msal on creation
> const msalConfig = {
>   auth: {
>     clientId: "Enter_the_Application_Id_Here",
>     authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>     redirectUri: "Enter_the_Redirect_Uri_Here",
>   },
>   cache: {
>     cacheLocation: "sessionStorage", // This configures where your cache will be stored
>     storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>   }
> };
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Modificare i valori nella sezione `msalConfig` come descritto di seguito:
>
> - `Enter_the_Application_Id_Here` è l'**ID applicazione (client)** per l'applicazione registrata.
> - `Enter_the_Cloud_Instance_Id_Here` è l'istanza del cloud di Azure. Per il cloud di Azure principale o globale immettere `https://login.microsoftonline.com/`. Per i cloud **nazionali** (ad esempio, Cina), vedere [Cloud nazionali](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` è impostato su uno degli elementi seguenti:
>   - Se l'applicazione supporta *account in questa directory organizzativa*, sostituire questo valore con l'**ID tenant** o il **nome del tenant**. Ad esempio: `contoso.microsoft.com`.
>   - Se l'applicazione supporta *account in qualsiasi directory organizzativa*, sostituire questo valore con `organizations`.
>   - Se l'applicazione supporta *account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con `common`. **Per questa guida introduttiva**, usare `common`.
>   - Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con `consumers`.
> - `Enter_the_Redirect_Uri_Here` è `http://localhost:3000/`.
>
> Se si usa il cloud principale (globale) di Azure, il valore `authority` nel file *authConfig.js* deve essere simile al seguente:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Per trovare i valori di **ID applicazione (client)** , **ID della directory (tenant)** e **Tipi di account supportati**, passare alla pagina di **panoramica** di registrazione dell'app nel portale di Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app.

> [!div renderon="docs"]
>
> Nella stessa cartella modificare il file *graphConfig.js* e aggiornare i valori `graphMeEndpoint` e `graphMailEndpoint` nell'oggetto `apiConfig`.
>
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
> `Enter_the_Graph_Endpoint_Here` è l'endpoint a cui verranno eseguite le chiamate API. Per il servizio API Microsoft Graph principale (globale), immettere `https://graph.microsoft.com/` (includere la barra finale). Per altre informazioni su Microsoft Graph nei cloud nazionali, vedere [Distribuzione di cloud nazionali](https://docs.microsoft.com/graph/deployments).
>
> Se si usa il servizio Microsoft Graph API principale (globale), i valori `graphMeEndpoint` e `graphMailEndpoint` nel file *graphConfig.js* deve essere simile al seguente:
>
> ```javascript
> graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
> graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
> ```
>
> #### <a name="step-4-run-the-project"></a>Passaggio 4: Eseguire il progetto

Eseguire il progetto con un server Web usandoNode.js:

1. Per avviare il server, eseguire i comandi seguenti nella directory del progetto:
    ```console
    npm install
    npm start
    ```
1. Passare a `http://localhost:3000/`.

1. Fare clic su **Accedi** per avviare il processo di accesso e quindi chiamare l'API Microsoft Graph.

    Al primo accesso viene chiesto di concedere il proprio consenso per consentire all'applicazione di accedere al profilo e completare la procedura di accesso. Dopo aver effettuato l'accesso, nella pagina devono essere visualizzate le informazioni del profilo utente.

## <a name="more-information"></a>Ulteriori informazioni

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio

:::image type="content" source="media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagramma che mostra il flusso del codice di autorizzazione per un'applicazione a pagina singola":::

### <a name="msaljs"></a>msal.js

Con la libreria MSAL.js è possibile concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform. Il file *index.html* dell'esempio contiene un riferimento alla libreria:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```

Se Node.js è già installato, è possibile scaricare l'ultima versione usando Node.js Package Manager (npm):

```console
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Passaggi successivi

Per una guida più dettagliata alla creazione dell'applicazione usata in questa guida introduttiva, vedere l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione per l'accesso e la chiamata a MS Graph](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
