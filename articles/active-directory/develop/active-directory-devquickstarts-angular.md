---
title: Introduzione ad AngularJS per Azure AD | Microsoft Docs
description: Come compilare un'applicazione Angular JS a singola pagina che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth.
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4153910bc03f112f84c26cda6f9c78f11028b934
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="help-secure-angularjs-single-page-apps-by-using-azure-ad"></a>Proteggere le app AngularJS a singola pagina con Azure AD

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) rende semplice e pratico aggiungere accesso, disconnessione e protezione delle chiamate API OAuth alle app a singola pagina.  Consente alle app di autenticare gli utenti con gli account Windows Server Active Directory e di utilizzare qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per le applicazioni JavaScript in esecuzione in un browser, Azure AD fornisce Active Directory Authentication Library (ADAL) o adal.js. La funzione esclusiva di adal.js è permettere all'app di ottenere facilmente i token di accesso. Per far capire quanto è semplice, verrà compilata un'applicazione AngularJS To-Do List che:

* Fa accedere gli utenti all'app usando Azure AD come provider di identità.

* Visualizza alcune informazioni relative all'utente.
* Chiama in modo sicuro l'API To Do List dell'app usando token di connessione di Azure AD.
* Disconnette l'utente dall'app.

Per compilare l'applicazione funzionante completa, è necessario:

1. Registrare l'app con Azure AD.
2. Installare ADAL e configurare l'app a singola pagina.
3. Usare ADAL per proteggere le pagine nell'app a singola pagina.

Per iniziare, [scaricare la struttura dell'app](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). È necessario anche un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Passaggio 1: Registrare l'applicazione DirectorySearcher
Per consentire all'app di autenticare gli utenti e ottenere i token, è innanzitutto necessario registrarla nel tenant di Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se è stato eseguito l'accesso a più directory, potrebbe essere necessario assicurarsi di visualizzare la directory corretta. A tale scopo, nella barra superiore fare clic sull'account. Nell'elenco **Directory** scegliere il tenant di Azure AD in cui si vuole registrare l'applicazione.
3. Fare clic su **More Services** (Altri servizi) nel riquadro a sinistra e scegliere **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e scegliere **Aggiungi**.
5. Seguire le istruzioni e creare una nuova applicazione Web e/o API Web:
  * Il **nome** descrive l'applicazione agli utenti.
  * L'**URI di reindirizzamento** è il percorso in cui Azure AD restituirà i token. Il percorso predefinito per questo esempio è `https://localhost:44326/`.
6. Dopo aver completato la registrazione, Azure AD assegna all'app un ID applicazione univoco.  Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla scheda dell'applicazione.
7. Adal.js usa il flusso implicito di OAuth per comunicare con Azure AD. È necessario abilitare il flusso implicito per l'applicazione eseguendo le operazioni seguenti:
  1. Fare clic sull'applicazione e scegliere **Manifesto** per aprire l'editor manifesto incorporato.
  2. Individuare la proprietà `oauth2AllowImplicitFlow`. Impostare il relativo valore su `true`.
  3. Fare clic su **Salva** per salvare il manifesto.
8. Concedere le autorizzazioni nel tenant per l'applicazione. Andare in **Impostazioni** > **Proprietà** > **Autorizzazioni necessarie** e fare clic sul pulsante **Concedi autorizzazioni** nella barra superiore. Fare clic su **Yes** per confermare.

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

## <a name="summary"></a>Riepilogo
A questo punto è disponibile un'app a singola pagina sicura, in grado di concedere l'accesso agli utenti e rilasciare richieste protette di token di connessione all'API back-end. Quando un utente fa clic sul collegamento **TodoList**, se necessario viene reindirizzato automaticamente da adal.js ad Azure AD per l'accesso. Inoltre, adal.js collegherà automaticamente un token di accesso a tutte le richieste Ajax inviate al back-end dell'app.  

I passaggi illustrati sopra sono i requisiti minimi necessari per compilare un'app a singola pagina con adal.js. Tuttavia sono disponibili altre funzionalità utili nelle app a singola pagina:

* Per generare in modo esplicito richieste di accesso e disconnessione, è possibile definire funzioni nei controller per richiamare adal.js.  In `App/Scripts/homeCtrl.js`:

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

* Esistono anche molti scenari in cui è necessario sapere se l'utente è connesso oppure no. Per raccogliere queste informazioni, è anche possibile usare l'oggetto `userInfo` .  Ad esempio, in `index.html` è possibile visualizzare il pulsante **Accesso** o **Disconnessione** a seconda dello stato di autenticazione:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

A questo punto l'app a singola pagina integrata in Azure AD può autenticare gli utenti, chiamare in modo sicuro il relativo back-end tramite OAuth 2.0 e ottenere informazioni di base sull'utente. Se non si è ancora popolato il tenant con alcuni utenti, ora è possibile farlo. Eseguire l'app a singola pagina To Do List e accedere come uno di questi utenti. Aggiungere attività all'elenco azioni dell'utente, disconnettersi e accedere di nuovo.

Adal.js consente di incorporare facilmente nell'applicazione funzionalità comuni relative alle identità. Esegue automaticamente le attività più complesse: gestione della cache, supporto del protocollo OAuth, presentazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti e altro.

Come riferimento, l'esempio completo (senza i valori di configurazione) è disponibile in [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Passaggi successivi
Ora è possibile passare ad altri scenari. Ad esempio è possibile provare a [chiamare un'API Web CORS da un'app a singola pagina](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
