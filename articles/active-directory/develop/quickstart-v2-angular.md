---
title: Consentire l'accesso degli utenti nelle applicazioni a pagina singola Angular - Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come un'app Angular può chiamare un'API che richiede token di accesso con Microsoft Identity Platform.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: a7553eea8941b93e589f38f8229ef35b70361f8e
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87129186"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Guida introduttiva: Consentire l'accesso agli utenti e ottenere un token di accesso in un'applicazione a pagina singola Angular

In questa guida di avvio rapido si usa codice di esempio per illustrare come un'applicazione a pagina singola Angular può consentire l'accesso agli utenti con account Microsoft personali, account aziendali o account dell'istituto di istruzione. Un'applicazione a pagina singola Angular può anche ottenere un token di accesso per chiamare l'API Microsoft Graph o un'API Web.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) per modificare i file di progetto oppure [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) per eseguire il progetto.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registrare e scaricare l'app di avvio rapido
> Per avviare l'app di avvio rapido, usare una delle opzioni descritte di seguito.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Opzione 1 (rapida): Registrare e configurare automaticamente l'app e quindi scaricare il codice di esempio
>
> 1. Accedere al [portale di Azure](https://portal.azure.com).
> 1. Se l'account ha accesso a più tenant, selezionare l'account in alto a destra e quindi impostare la sessione del portale sul tenant di Azure Active Directory (Azure AD) desiderato.
> 1. Aprire il nuovo riquadro [Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) nel portale di Azure.
> 1. Immettere un nome per l'applicazione e quindi fare clic su **Registra**.
> 1. Passare al riquadro Avvio rapido e visualizzare la guida di avvio rapido per Angular. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Opzione 2 (manuale): Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-the-application"></a>Passaggio 1: Registrare l'applicazione
>
> 1. Accedere al [portale di Azure](https://portal.azure.com).
> 1. Se l'account ha accesso a più tenant, selezionare l'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
> 1. Seguire le istruzioni per [registrare un'applicazione a pagina singola](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) nel portale di Azure.
> 1. Aggiungere una nuova piattaforma nel riquadro **Autenticazione** della registrazione dell'app e registrare l'URI di reindirizzamento: `http://localhost:4200/`.
> 1. Questo avvio rapido usa il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md). Selezionare le impostazioni di **Concessione implicita** per **Token ID** e **Token di accesso**. I token ID e i token di accesso sono obbligatori perché l'app consente l'accesso agli utenti e chiama un'API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per il funzionamento del codice di esempio di questa guida di avvio rapido è necessario aggiungere un URI di reindirizzamento come **http://localhost:4200/** e abilitare **Concessione implicita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-javascript/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-code-sample"></a>Passaggio 2: Scaricare il codice di esempio
>[!div renderon="docs"]
>Per eseguire il progetto con un server Web usando Node.js, [clonare il repository di esempio](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) oppure [scaricare i file di progetto di base](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Aprire i file con un editor come Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Passaggio 3: Configurare l'app JavaScript
>
> Nella cartella *src/app* modificare *app.module.ts* e impostare i valori di `clientId` e `authority` in `MsalModule.forRoot`.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> Sostituire questi valori:
>
>|Nome del valore|Descrizione|
>|---------|---------|
>|Enter_the_Application_Id_Here|Nella pagina **Panoramica** della registrazione dell'applicazione, è il valore di **ID applicazione (client)** . |
>|Enter_the_Cloud_Instance_Id_Here|Si tratta dell'istanza del cloud di Azure. Per il cloud globale o principale di Azure, immettere **https://login.microsoftonline.com** . Per i cloud nazionali (ad esempio, Cina), vedere [Cloud nazionali](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Impostare il valore su una delle opzioni seguenti: Se l'applicazione supporta *account in questa directory dell'organizzazione*, sostituire questo valore con l'ID della directory (tenant) o il nome del tenant (ad esempio, **contoso.microsoft.com**). Se l'applicazione supporta *Account in qualsiasi directory organizzativa*, sostituire questo valore con **organizations**. Se l'applicazione supporta *Account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con **common**. Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con **consumers**. |
>|Enter_the_Redirect_Uri_Here|Sostituire con **http://localhost:4200** .|
>|cacheLocation  | (Facoltativo) Impostare l'archiviazione browser per lo stato di autenticazione. L'impostazione predefinita è **sessionStorage**.   |
>|storeAuthStateInCookie  | (Facoltativo) Identificare la libreria in cui è archiviato lo stato della richiesta di autenticazione. Questo stato è necessario per convalidare i flussi di autenticazione nei cookie del browser. Questo cookie è impostato per Internet Explorer e Microsoft Edge allo scopo di supportare questi due browser. Per altri dettagli, vedere i [problemi noti](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > Per trovare i valori di **ID applicazione (client)** , **ID della directory (tenant)** e **Tipi di account supportati**, passare alla pagina di **panoramica** dell'app nel portale di Azure.

Per altre informazioni sulle opzioni configurabili disponibili, vedere [Inizializzare le applicazioni client](msal-js-initializing-client-applications.md).

Il codice sorgente per la libreria MSAL.js è disponibile nel repository [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) in GitHub.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Passaggio 3: Eseguire il progetto

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Passaggio 4: Eseguire il progetto

Se si usa Node.js:

1. Avviare il server eseguendo i comandi seguenti dalla directory del progetto:

   ```console
   npm install
   npm start
   ```

1. Passare a **http://localhost:4200/** .
1. Selezionare **Accedi**.
1. Selezionare **Profilo** per chiamare Microsoft Graph.

Dopo che il browser ha caricato l'applicazione, selezionare **Accedi**. Quando si avvia l'accesso per la prima volta, viene richiesto di fornire il consenso per consentire all'applicazione di accedere al profilo e completare la procedura di accesso. Dopo aver effettuato l'accesso, selezionare **Profilo** e le informazioni del profilo utente verranno visualizzate nella pagina.

## <a name="how-the-sample-works"></a>Funzionamento dell'esempio

![Diagramma che illustra il funzionamento dell'app di esempio di questo argomento di avvio rapido](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>Passaggi successivi

L'esercitazione su Angular illustra come consentire l'accesso a un utente e acquisire i token:

> [!div class="nextstepaction"]
> [Esercitazioni su Angular](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)

