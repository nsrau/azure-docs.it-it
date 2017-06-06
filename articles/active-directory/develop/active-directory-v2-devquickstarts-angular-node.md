---
title: Introduzione all&quot;app a pagina singola Azure AD v2.0 NodeJS AngularJS | Documentazione Microsoft
description: Come creare un&quot;app a pagina singola AngularJS che consente agli utenti di accedere con un account Microsoft personale, aziendale e dell&quot;istituto di istruzione.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: d286aa33-8a94-452f-beb7-ddc6c6daa5c8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 9cd676554542e4effef54790bf9095c5b7a8f75b
ms.openlocfilehash: 3f2e856b71a42fe677d92c9c020236b8f0da9c1e
ms.contentlocale: it-it
ms.lasthandoff: 02/03/2017


---
# <a name="add-sign-in-to-an-angularjs-single-page-app---nodejs"></a>Aggiungere l'accesso a un'app a pagina singola AngularJS - NodeJS
In questo articolo si aggiungerà l'accesso con account Microsoft a un'app AngularJS usando l'endpoint v2.0 di Azure Active Directory. L'endpoint v2.0 consente di eseguire un'unica integrazione nell'app e autenticare gli utenti con account personali e aziendali o dell'istituto di istruzione.

Questo esempio è una semplice app a pagina singola To-Do List che archivia le attività in un'API REST back-end, scritta in NodeJS e protetta con i token di connessione OAuth di Azure AD.  L'app AngularJS userà la libreria di autenticazione JavaScript open source [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) per gestire l'intero processo di accesso e acquisire i token per chiamare l'API REST.  Lo stesso modello può essere applicato per l'autenticazione in altre API REST, ad esempio [Microsoft Graph](https://graph.microsoft.com) o le API di Gestione risorse di Azure.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint 2.0.  Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download"></a>Scaricare
Per iniziare, sarà necessario scaricare e installare [node.js](https://nodejs.org).  Sarà quindi possibile clonare o [scaricare](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) lo scheletro di un'app:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

Lo scheletro di un'app include tutto il codice boilerplate per una semplice app AngularJS, ma non tutte le parti relative all'identità.  Se non si vuole proseguire, in alternativa è possibile clonare o [scaricare](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) l'esempio completo.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## <a name="register-an-app"></a>Registrare un'app
Per prima cosa, creare un'app nel [portale di registrazione delle app](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) oppure seguire questa [procedura dettagliata](active-directory-v2-app-registration.md).  Verificare di:

* Aggiungere la piattaforma **Web** per l'app.
* Immettere l' **URI di reindirizzamento**corretto. Il valore predefinito per questo esempio è `http://localhost:8080`.
* Lasciare abilitata la casella di controllo **Consenti flusso implicito** . 

Copiare l' **ID applicazione** assegnato all'app, perché verrà richiesto a breve. 

## <a name="install-adaljs"></a>Installare adal.js
Per iniziare, andare al progetto scaricato e installare adal.js.  Se [bower](http://bower.io/) è installato, è sufficiente eseguire questo comando.  In caso di mancata corrispondenza delle versioni delle dipendenze, scegliere la versione superiore.

```
bower install adal-angular#experimental
```

In alternativa, è possibile scaricare manualmente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) e [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Aggiungere entrambi i file alla directory `app/lib/adal-angular-experimental/dist` .

Aprire ora il progetto nell'editor di testo preferito e caricare adal.js alla fine del corpo della pagina:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Configurare l'API REST
Mentre si configurano altre impostazioni, verrà resa operativa l'API REST back-end.  In un prompt dei comandi installare tutti i pacchetti necessari eseguendo (assicurarsi di essere nella directory di primo livello del progetto):

```
npm install
```

Aprire ora `config.js` e sostituire il valore `audience`:

```js
exports.creds = {

     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',

     ...
}
```

L'API REST userà questo valore per convalidare i token ricevuti dall'app Angular nelle richieste AJAX.  Si noti che questa semplice API REST archivia i dati in memoria, quindi, ogni volta che si arresta il server, si perderanno tutte le attività create in precedenza.

Da ora in avanti non si parlerà più del funzionamento dell'API REST.  È possibile scrivere nel codice, ma, per altre informazioni sulla protezione delle API Web con Azure AD, vedere [questo articolo](active-directory-v2-devquickstarts-node-api.md). 

## <a name="sign-users-in"></a>Concedere l'accesso agli utenti
Ora verrà scritto un codice di identità.  Come è possibile osservare, adal.js contiene un provider AngularJS, che funziona bene con il meccanismo di routing Angular.  Iniziare aggiungendo il modulo adal all'app:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Ora è possibile inizializzare `adalProvider` con l'ID applicazione:

```js
// app/scripts/app.js

...

adalProvider.init({

        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 

        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',

        // Your application id from the registration portal
        clientId: '<Your-application-id>',

        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',

    }, $httpProvider);
```

Ora adal.js ha tutte le informazioni necessarie per proteggere l'app e far accedere gli utenti.  Per forzare l'accesso per una particolare route nell'app, è sufficiente una riga di codice:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Ora, quando un utente fa clic sul collegamento `TodoList` , viene reindirizzato automaticamente da adal.js ad Azure AD per l'accesso, se necessario.  È anche possibile inviare in modo esplicito richieste di accesso e di disconnessione richiamando adal.js nei controller:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {

        // Redirect the user to sign in
        adalService.login();

    };
    $scope.logout = function () {

        // Redirect the user to log out    
        adalService.logOut();

    };
...
```

## <a name="display-user-info"></a>Visualizzare le info utente
Ora che l'utente è connesso, sarà probabilmente necessario accedere ai dati di autenticazione dell'utente connesso contenuti nell'applicazione.  Adal.js espone queste informazioni nell'oggetto `userInfo` .  Per accedere a questo oggetto in una visualizzazione, aggiungere prima adal.js all'ambito radice del controller corrispondente:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Sarà quindi possibile indirizzare direttamente l'oggetto `userInfo` nella visualizzazione: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

È anche possibile usare l'oggetto `userInfo` per determinare se l'utente è connesso.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Chiamare l'API REST
Ora verranno recuperati alcuni token e verrà chiamata l'API REST per creare, leggere, aggiornare ed eliminare le attività.  La novità è che  non è necessario eseguire *alcuna operazione*.  Adal.js eseguirà automaticamente il recupero, la memorizzazione nella cache e l'aggiornamento dei token.  Allegherà anche questi token alle richieste AJAX in uscita inviate all'API REST.  

Come funziona esattamente tutto questo? Sta tutto negli [intercettori AngularJS](https://docs.angularjs.org/api/ng/service/$http), che consentono ad adal.js di trasformare i messaggi http in uscita e in ingresso.  Adal.js presume inoltre che le richieste inviate allo stesso dominio della finestra usino i token destinati allo stesso ID applicazione dell'app AngularJS.  Infatti lo stesso ID applicazione è stato usato sia nell'app Angular che nell'API REST NodeJS.  È possibile, ovviamente, ignorare questo comportamento e comunicare ad adal.js di ottenere i token per le altre API REST, se necessario, ma per questo semplice scenario verranno usate le impostazioni predefinite.

Ecco un frammento che mostra come sia semplice inviare richieste con i token di connessione da Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Congratulazioni.  A questo punto l'app a singola pagina integrata in Azure AD è completata.  Come è evidente,  può autenticare gli utenti, chiamare in modo sicuro l'API REST back-end con OpenID Connect e ottenere informazioni di base sull'utente.  Per impostazione predefinita, supporta tutti gli utenti con un account Microsoft personale o un account aziendale o dell'istituto di istruzione di Azure AD.  Provare l'app eseguendo:

```
node server.js
```

In un browser passare a `http://localhost:8080`.  Accedere con un account Microsoft personale o un account aziendale o dell'istituto di istruzione.  Aggiungere attività all'elenco attività dell'utente e disconnettersi.  Provare ad accedere con l'altro tipo di account. Se è necessario un tenant di Azure AD per creare utenti aziendali o dell'istituto di istruzione, [qui sono disponibili informazioni per ottenerne uno](active-directory-howto-tenant.md) (è gratuito).

Per altre informazioni sull'endpoint v2.0, tornare alla [guida per sviluppatori versione 2.0](active-directory-appmodel-v2-overview.md).  Per altre risorse, vedere:

* [Esempi di Azure in GitHub >>](https://github.com/Azure-Samples)
* [Azure AD in Stack Overflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
* Documentazione di Azure AD in [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Ottenere aggiornamenti della sicurezza per i prodotti
È consigliabile ricevere notifiche in caso di problemi di sicurezza. A tale scopo, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e sottoscrivere gli avvisi di sicurezza.


