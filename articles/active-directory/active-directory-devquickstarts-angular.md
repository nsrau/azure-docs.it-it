---
title: Introduzione ad AngularJS per Azure AD | Microsoft Docs
description: Come compilare un&quot;applicazione Angular JS a singola pagina che si integra con Azure AD per l&quot;accesso e chiama le API protette di Azure AD usando OAuth.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 1865043ca9c9019b9813f11eb4a55f7f16d79287
ms.openlocfilehash: f2ee67fa55bf71eefb6c1b156b43fa3482f5dbe0


---
# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>Protezione di app AngularJS a singola pagina con Azure AD
[!INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD rende semplici e dirette le operazioni di accesso, disconnessione e protezione delle chiamate API OAuth alle app a singola pagina.  Consente all'app di autenticare gli utenti con gli account Active Directory e di utilizzare qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per le applicazioni JavaScript in esecuzione in un browser, Azure AD fornisce Active Directory Authentication Library o adal.js.  La funzione esclusiva di Adal.js è di permettere all'app di ottenere facilmente i token di accesso.  Per far capire quanto è semplice, verrà compilata un'applicazione AngularJS To-Do List che:

* Connette gli utenti all'app usando Azure AD come provider di identità.
* Visualizza alcune informazioni relative all'utente.
* Chiama in modo sicuro l'API To Do List dell'app usando token di connessione di AAD.
* Disconnette l'utente dall'app.

Per compilare l'applicazione funzionante completa, sarà necessario:

1. Registrare l'applicazione con Azure AD.
2. Installare ADAL e configurare l'applicazione a singola pagina (SPA).
3. Usare ADAL per proteggere le pagine nell'applicazione a singola pagina.

Per iniziare, [scaricare la struttura dell'app](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Sarà necessario anche un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione.  Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. Registrare l'applicazione DirectorySearcher*
Per consentire all'app di autenticare gli utenti e ottenere i token, sarà innanzitutto necessario registrarla nel tenant di Azure AD.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD facendo clic sull'account nell'angolo superiore destro della pagina.
3. Nel riquadro di spostamento a sinistra fare clic su **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e quindi su **Aggiungi**.
5. Immettere un nome descrittivo per l'applicazione, ad esempio "TodoListService", selezionare "Applicazione Web e/o API Web" come tipo di applicazione.
6. Come URL di accesso immettere l'URL di base per l'esempio, come `https://localhost:8080`.
7. Dopo avere completato la registrazione, Azure AD assegnerà all'applicazione un identificatore client univoco, l'ID applicazione. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla pagina dell'applicazione.
8. Dalla pagina delle impostazioni configurare l'**URL di risposta**, ovvero il percorso a cui AAD restituirà i token. Il percorso predefinito per questo esempio è `https://localhost:44326/`

* Adal.js usa il flusso implicito di OAuth per comunicare con Azure AD.  È necessario abilitare il flusso implicito per l'applicazione eseguendo le operazioni seguenti:
  * Fare clic su **Manifesto** dalla pagina dell'applicazione per aprire l'editor del manifesto e individuare la `oauth2AllowImplicitFlow` proprietà. Impostare il relativo valore su `true`.
  * Salvare il manifesto.

## <a name="2-install-adal--configure-the-spa"></a>*2. Installare ADAL e configurare l'applicazione a singola pagina (SPA)*
Ora che si dispone di un'applicazione in Azure AD, è possibile installare adal.js e scrivere il codice relativo all'identità.

* Iniziare aggiungendo adal.js al progetto TodoSPA tramite Console di Gestione pacchetti:
  * Scaricare [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) e aggiungerlo alla `App/Scripts/`directory del progetto.
  * Scaricare [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) e aggiungerlo alla `App/Scripts/` directory del progetto.
  * Caricare ogni script prima della fine di `</body>` in `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

* Per fare in modo che l'API To Do List del back-end dell'applicazione a singola pagina accetti i token dal browser, il back-end deve avere le informazioni di configurazione relative alla registrazione dell'app. Nel progetto TodoSPA aprire il file `web.config`.  Sostituire i valori degli elementi nella sezione `<appSettings>` in modo che corrispondano ai valori inseriti nel portale di Azure.  Il codice farà riferimento a questi valori ogni volta che userà ADAL.
  * `ida:Tenant` è il dominio del tenant di Azure AD, ad esempio, contoso.onmicrosoft.com.
  * `ida:Audience` deve essere l' **ID client** dell'applicazione copiato dal portale.

## <a name="3----use-adal-to-secure-pages-in-the-spa"></a>*3.    Usare ADAL per proteggere le pagine nell'applicazione a singola pagina*
Adal.js è stato creato per l'integrazione con i provider di route e HTTP AngularJS e consente di proteggere visualizzazioni individuali nell'applicazione a singola pagina.

* Importare il modulo adal.js in `App/Scripts/app.js`:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
* A questo punto è possibile inizializzare `adalProvider` con i valori di configurazione relativi alla registrazione dell'applicazione anche in `App/Scripts/app.js`:

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
* Per proteggere la visualizzazione `TodoList` nell'app, ora è necessaria una sola riga di codice: `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

A questo punto è disponibile un'applicazione a singola pagina sicura, in grado di concedere l'accesso agli utenti e rilasciare richieste di token di connessione protette all'API back-end.  Quando un utente fa clic sul collegamento `TodoList`, viene reindirizzato automaticamente da adal.js ad Azure AD per l'accesso, se necessario.  Inoltre, adal.js collegherà automaticamente un access_token a tutte le richieste AJAX inviate al back-end dell'applicazione.  Quanto illustrato sopra è il minimo necessario per compilare un'applicazione a singola pagina con adal.js, ma sono disponibili molte altre funzionalità utili nelle applicazioni a singola pagina:

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
* È anche possibile presentare informazioni sull'utente nell'interfaccia utente dell'app.  Il servizio ADAL è già stato aggiunto al controller `userDataCtrl`, quindi è possibile accedere all'oggetto `userInfo` nella visualizzazione associata, `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

* Esistono anche molti scenari in cui è consigliabile sapere se l'utente è o meno connesso.  Per raccogliere queste informazioni, è anche possibile usare l'oggetto `userInfo` .  Ad esempio, in `index.html` è possibile visualizzare il pulsante di "accesso" o "disconnessione" a seconda dello stato di autenticazione:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Congratulazioni. A questo punto l'app a singola pagina integrata in Azure AD è completata.  Può autenticare gli utenti, chiamare in modo sicuro il relativo back-end tramite OAuth 2.0 e ottenere informazioni di base sull'utente.  Se non si è ancora popolato il tenant con alcuni utenti, ora è possibile farlo.  Eseguire l'applicazione a singola pagina To Do List SPA e accedere come uno di questi utenti.  Aggiungere attività all'elenco azioni dell'utente, disconnettersi e accedere di nuovo.

Adal.js consente di incorporare facilmente nell'applicazione tutte queste funzionalità comuni relative alle identità.  Esegue automaticamente le attività più complesse: gestione della cache, supporto del protocollo OAuth, presentazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti e altro.

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) [qui](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Ora è possibile passare ad altri scenari.  È possibile:

[Chiamare un'API Web CORS da una SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]



<!--HONumber=Dec16_HO4-->


