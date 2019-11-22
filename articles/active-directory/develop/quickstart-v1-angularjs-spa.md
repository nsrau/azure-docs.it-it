---
title: Creare un'app a pagina singola AngularJS per l'accesso e la disconnessione con Azure Active Directory | Microsoft Docs
description: Informazioni su come compilare un'applicazione AngularJS a singola pagina che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c239b88a67e1be19e3a95130839ab6fd4598fe77
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150055"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Guida introduttiva: Compilazione di un'app a singola pagina AngularJS per l'accesso e la disconnessione con Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

> [!IMPORTANT]
> [Microsoft Identity Platform](v2-overview.md) è un'evoluzione della piattaforma per sviluppatori di Azure Active Directory (Azure AD). Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare API Microsoft, come Microsoft Graph o API create dagli sviluppatori.
> Se è necessario abilitare l'accesso per gli account personali, oltre che per gli account aziendali e dell'istituto di istruzione, è possibile usare l' *[endpoint di Microsoft Identity Platform](azure-ad-endpoint-comparison.md)* .
> Questo avvio rapido è relativo all'endpoint Azure AD v1.0. Per i nuovi progetti è consigliabile usare l'endpoint v2.0. Per altre informazioni, vedere [questa esercitazione su JavaScript SPA](tutorial-v2-javascript-spa.md) e [questo articolo](active-directory-v2-limitations.md) che illustra le caratteristiche dell'*endpoint di Microsoft Identity Platform*.

Azure Active Directory (Azure AD) rende semplice e pratico aggiungere accesso, disconnessione e protezione delle chiamate API OAuth alle app a singola pagina. Consente alle app di autenticare gli utenti con gli account Windows Server Active Directory e di utilizzare qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per le applicazioni JavaScript in esecuzione in un browser, Azure AD fornisce Active Directory Authentication Library (ADAL) o adal.js. La funzione esclusiva di adal.js è permettere all'app di ottenere facilmente i token di accesso.

In questa guida introduttiva si apprenderà come creare un'applicazione AngularJS to-Do List che:

* Fa accedere gli utenti all'app usando Azure AD come provider di identità.
* Visualizza alcune informazioni relative all'utente.
* Chiama in modo sicuro l'API To Do List dell'app usando token di connessione di Azure AD.
* Disconnette l'utente dall'app.

Per compilare l'applicazione funzionante completa, sarà necessario:

1. Registrare l'app con Azure AD.
2. Installare ADAL e configurare l'app a singola pagina.
3. Usare ADAL per proteggere le pagine nell'app a singola pagina.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, completare questi prerequisiti:

* Scaricare la [struttura dell'app](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) oppure l'[esempio completato](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).
* Predisporre un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Passaggio 1: Registrare l'applicazione DirectorySearcher

Per consentire all'app di autenticare gli utenti e ottenere i token, è innanzitutto necessario registrarla nel tenant di Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se è stato eseguito l'accesso a più directory, potrebbe essere necessario assicurarsi di visualizzare la directory corretta. A tale scopo, nella barra superiore fare clic sull'account. Nell'elenco **Directory** scegliere il tenant di Azure AD in cui si vuole registrare l'applicazione.
1. Fare clic su **Tutti i servizi** nel riquadro sinistro e quindi selezionare **Azure Active Directory**.
1. Fare clic su **Registrazioni app** e quindi selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere il nome dell'applicazione.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
1. Selezionare la piattaforma **Web** nella sezione **URI di reindirizzamento** e impostare il valore su `https://localhost:44326/`, corrispondente alla posizione in cui Azure AD restituirà i token.
1. Al termine, selezionare **Registra**. Nella pagina **Panoramica**  dell'app prendere nota del valore del campo **ID applicazione (client)** .
1. Adal.js usa il flusso implicito di OAuth per comunicare con Azure AD. È necessario abilitare il flusso implicito per l'applicazione eseguendo le operazioni seguenti. Nel riquadro di spostamento a sinistra dell'applicazione registrata selezionare **Autenticazione**.
1. Nelle **Impostazioni avanzate**, in **Concessione implicita**, selezionare entrambe le caselle di controllo **Token ID** e **Token di accesso**. I token ID e di accesso sono obbligatori perché l'app deve consentire l'accesso agli utenti e chiamare un'API.
1. Selezionare **Salva**.
1. Concedere le autorizzazioni nel tenant per l'applicazione. Selezionare **Autorizzazioni API** e fare clic sul pulsante **Concedi consenso amministratore** in **Fornisci il consenso**.
1. Selezionare **Sì** per confermare.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Passaggio 2: Installare ADAL e configurare l'app a singola pagina

Ora che si dispone di un'applicazione in Azure AD, è possibile installare adal.js e scrivere il codice relativo all'identità.

### <a name="configure-the-javascript-client"></a>Configurare il client JavaScript

Iniziare aggiungendo adal.js al progetto TodoSPA tramite la console di Gestione pacchetti:

1. Scaricare [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) e aggiungerlo alla `App/Scripts/`directory del progetto.
2. Scaricare [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) e aggiungerlo alla `App/Scripts/` directory del progetto.
3. Caricare ogni script prima della fine di `</body>` in `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Configurare il server back-end

Per fare in modo che l'API To Do List del back-end dell'app a singola pagina accetti i token dal browser, il back-end ha bisogno delle informazioni di configurazione relative alla registrazione dell'app. Nel progetto TodoSPA aprire il file `web.config`. Sostituire i valori degli elementi nella sezione `<appSettings>` in modo che corrispondano ai valori usati nel Portale di Azure. Il codice farà riferimento a questi valori ogni volta che userà ADAL.

   * `ida:Tenant` è il dominio del tenant di Azure AD, ad esempio contoso.onmicrosoft.com.
   * `ida:Audience` è l'ID client dell'applicazione copiato dal portale.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Passaggio 3: Usare ADAL per proteggere le pagine nell'app a singola pagina

Adal.js si integra con i provider di route e HTTP AngularJS e permette di proteggere visualizzazioni individuali nell'app a singola pagina.

1. Importare il modulo adal.js in `App/Scripts/app.js`:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inizializzare `adalProvider` con i valori di configurazione relativi alla registrazione dell'applicazione anche in `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Per proteggere la visualizzazione `TodoList` nell'app, usare una sola riga di codice: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Summary

A questo punto è disponibile un'app a singola pagina sicura, in grado di concedere l'accesso agli utenti e rilasciare richieste protette di token di connessione all'API back-end. Quando un utente fa clic sul collegamento **TodoList**, se necessario viene reindirizzato automaticamente da adal.js ad Azure AD per l'accesso. Inoltre, adal.js collegherà automaticamente un token di accesso a tutte le richieste Ajax inviate al back-end dell'app.

I passaggi illustrati sopra sono i requisiti minimi necessari per compilare un'app a singola pagina con adal.js. Tuttavia sono disponibili altre funzionalità utili nelle app a singola pagina:

* Per generare in modo esplicito richieste di accesso e disconnessione, è possibile definire funzioni nei controller per richiamare adal.js. In `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* È anche possibile presentare informazioni sull'utente nell'interfaccia utente dell'app. Il servizio ADAL è già stato aggiunto al controller `userDataCtrl`, quindi è possibile accedere all'oggetto `userInfo` nella visualizzazione associata, `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Esistono anche molti scenari in cui è necessario sapere se l'utente è connesso oppure no. Per raccogliere queste informazioni, è anche possibile usare l'oggetto `userInfo` . Ad esempio, in `index.html` è possibile visualizzare il pulsante **Accesso** o **Disconnessione** a seconda dello stato di autenticazione:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

A questo punto l'app a singola pagina integrata in Azure AD può autenticare gli utenti, chiamare in modo sicuro il relativo back-end tramite OAuth 2.0 e ottenere informazioni di base sull'utente. Se non si è ancora popolato il tenant con alcuni utenti, ora è possibile farlo. Eseguire l'app a singola pagina To Do List e accedere come uno di questi utenti. Aggiungere attività all'elenco azioni dell'utente, disconnettersi e accedere di nuovo.

Adal.js consente di incorporare facilmente nell'applicazione funzionalità comuni relative alle identità. Esegue automaticamente le attività più complesse: gestione della cache, supporto del protocollo OAuth, presentazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti e altro.

Come riferimento, l'esempio completo (senza i valori di configurazione) è disponibile in [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Passaggi successivi

Ora è possibile passare ad altri scenari.

> [!div class="nextstepaction"]
> [Chiamare un'API Web CORS da un'app a singola pagina](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).
