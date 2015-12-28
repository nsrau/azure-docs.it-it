<properties
	pageTitle="Introduzione ad AngularJS per Azure AD | Microsoft Azure"
	description="Come creare un'app a pagina singola AngularJS che consente agli utenti di accedere con un account Microsoft personale, aziendale e dell'istituto di istruzione."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="dastrock"/>


# Anteprima di Modello app 2.0: Aggiungere l'accesso a un'app a pagina singola AngularJS - .NET

In questo articolo verrà aggiunto l'accesso con account Microsoft a un'app AngularJS usando Modello app 2.0 di Azure Active Directory. Modello app 2.0 consente di eseguire una singola integrazione nell'app e di autenticare gli utenti con account sia personali che aziendali o dell'istituto di istruzione.

Questo esempio è una semplice app a pagina singola To-Do List che archivia le attività in un'API REST back-end, scritta con il framework MVC .NET 4.5 e protetta con i token di connessione OAuth di Azure AD. L'app AngularJS userà la libreria di autenticazione JavaScript open source [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) per gestire l'intero processo di accesso e acquisire i token per chiamare l'API REST. Lo stesso modello può essere applicato per l'autenticazione in altre API REST, ad esempio [Microsoft Graph](https://graph.microsoft.com) o le API di Gestione risorse di Azure.

## Scaricare

Per iniziare, sarà necessario scaricare e installare Visual Studio. Sarà quindi possibile clonare o [scaricare](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) lo scheletro di un'app:

```
git clone --branch skeleton https://github.com/AzureADQuickStarst/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

Lo scheletro di un'app include tutto il codice boilerplate per una semplice app AngularJS, ma non tutte le parti relative all'identità. Se non si vuole proseguire, in alternativa è possibile clonare o [scaricare](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) l'esempio completo.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## Registrare un'app

Creare prima di tutto un'app nel [portale di registrazione delle app](https://apps.dev.microsoft.com) o seguire questi [passaggi dettagliati](active-directory-v2-app-registration.md). Verificare di:

- Aggiungere la piattaforma **Web** per l'app.
- Immettere l'**URI di reindirizzamento** corretto. Il valore predefinito per questo esempio è `https://localhost:44326/`.
- Lasciare abilitata la casella di controllo **Consenti flusso implicito**. 

Copiare l'**ID applicazione** assegnato all'app, perché verrà richiesto a breve.

## Installare adal.js
Per iniziare, andare al progetto scaricato e installare adal.js. Se [bower](http://bower.io/) è installato, è sufficiente eseguire questo comando. In caso di versioni delle dipendenze non corrispondenti, scegliere la versione superiore. ```
bower install adal-angular#experimental
```

In alternativa, è possibile scaricare manualmente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) e [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js). Aggiungere entrambi i file alla directory `app/lib/adal-angular-experimental/dist` del progetto `TodoSPA`.

Ora aprire il progetto in Visual Studio e caricare adal.js alla fine del corpo della pagina principale:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## Configurare l'API REST

Mentre si configurano altre impostazioni, verrà resa operativa l'API REST back-end. Nella radice del progetto aprire `web.config` e sostituire il valore `audience`. L'API REST userà questo valore per convalidare i token ricevuti dall'app Angular nelle richieste AJAX.

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>
    
...
```

Da ora in avanti non si parlerà più del funzionamento dell'API REST. È possibile scrivere nel codice, ma, per altre informazioni sulla protezione delle API Web con Azure AD, vedere [questo articolo](active-directory-v2-devquickstarts-dotnet-api.md).

## Concedere l'accesso agli utenti
Ora verrà scritto un codice di identità. Come è possibile osservare, adal.js contiene un provider AngularJS, che funziona bene con il meccanismo di routing Angular. Iniziare aggiungendo il modulo adal all'app:

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

Ora adal.js ha tutte le informazioni necessarie per proteggere l'app e far accedere gli utenti. Per forzare l'accesso per una particolare route nell'app, è sufficiente una riga di codice:

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

Ora, quando un utente fa clic sul collegamento `TodoList`, viene reindirizzato automaticamente da adal.js ad Azure AD per l'accesso, se necessario. È anche possibile inviare in modo esplicito richieste di accesso e di disconnessione richiamando adal.js nei controller:

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

## Visualizzare le info utente
Ora che l'utente è connesso, sarà probabilmente necessario accedere ai dati di autenticazione dell'utente connesso contenuti nell'applicazione. Adal.js espone queste informazioni nell'oggetto `userInfo`. Per accedere a questo oggetto in una visualizzazione, aggiungere prima adal.js all'ambito radice del controller corrispondente:

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

## Chiamare l'API REST
Ora verranno recuperati alcuni token e verrà chiamata l'API REST per creare, leggere, aggiornare ed eliminare le attività. La novità è che non è necessario fare *nulla*. Adal.js eseguirà automaticamente il recupero, la memorizzazione nella cache e l'aggiornamento dei token. Allegherà anche questi token alle richieste AJAX in uscita inviate all'API REST.

Come funziona esattamente tutto questo? Sta tutto negli [intercettori AngularJS](https://docs.angularjs.org/api/ng/service/$http), che consentono ad adal.js di trasformare i messaggi http in uscita e in ingresso. Adal.js presume inoltre che le richieste inviate allo stesso dominio della finestra usino i token destinati allo stesso ID applicazione dell'app AngularJS. Infatti lo stesso ID applicazione è stato usato sia nell'app Angular che nell'API REST NodeJS. È possibile, ovviamente, ignorare questo comportamento e comunicare ad adal.js di ottenere i token per le altre API REST, se necessario, ma per questo semplice scenario verranno usate le impostazioni predefinite.

Ecco un frammento che mostra come sia semplice inviare richieste con i token di connessione da Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Congratulazioni. A questo punto l'app a singola pagina integrata in Azure AD è completata. Come è evidente, può autenticare gli utenti, chiamare in modo sicuro l'API REST back-end con OpenID Connect e ottenere informazioni di base sull'utente. Per impostazione predefinita, supporta tutti gli utenti con un account Microsoft personale o un account aziendale o dell'istituto di istruzione di Azure AD. Eseguire l'app e in un browser andare a `https://localhost:44326/`. Accedere con un account Microsoft personale o un account aziendale o dell'istituto di istruzione. Aggiungere attività all'elenco attività dell'utente e disconnettersi. Provare ad accedere con l'altro tipo di account. Se è necessario un tenant di Azure AD per creare utenti aziendali o dell'istituto di istruzione, [qui sono disponibili informazioni per ottenerne uno](active-directory-howto-tenant.md) (è gratuito).

Per altre informazioni sull'anteprima di Modello app 2.0, tornare alla [guida per sviluppatori versione 2.0](active-directory-appmodel-v2-overview.md). Per altre risorse, vedere:

- [Esempi di Azure in GitHub >>](https://github.com/Azure-Samples)
- [Azure AD in Stack Overflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Documentazione di Azure AD su [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=AcomDC_1217_2015-->