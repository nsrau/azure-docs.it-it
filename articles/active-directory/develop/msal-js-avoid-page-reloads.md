---
title: Evitare i ricaricamento delle pagine (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come evitare il ricaricamento delle pagine durante l'acquisizione e il rinnovo dei token in modo invisibile all'utente utilizzando Microsoft Authentication Library per JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084949"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Evitare i ricaricamenti delle pagine quando si acquisiscono e si rinnovano i token in modo invisibile all'utente utilizzando MSAL.js
Microsoft Authentication Library per JavaScript (MSAL.js) utilizza elementi nascosti `iframe` per acquisire e rinnovare i token in background in modo invisibile all'utente. Azure AD restituisce il token al redirect_uri registrato specificato nella richiesta di token (per impostazione predefinita si tratta della pagina radice dell'app). Poiché la risposta è un 302, risulta `redirect_uri` nel codice `iframe`HTML corrispondente al caricamento nel file . Di solito l'app `redirect_uri` è la pagina radice e questo fa sì che venga ricaricata.

In altri casi, se la navigazione alla pagina radice dell'app `iframe` richiede `X-Frame-Options: deny` l'autenticazione, potrebbe verificarsi elementi annidati o errori.

Poiché MSAL.js non è in grado di chiudere il 302 rilasciato da `redirect_uri` Azure AD `iframe`ed è necessario per elaborare il token restituito, non può impedire il caricamento nel file .

Per evitare il ricaricamento dell'intera app o altri errori causati da ciò, segui queste soluzioni alternative.

## <a name="specify-different-html-for-the-iframe"></a>Specificare codice HTML diverso per l'iframe

Impostare `redirect_uri` la proprietà in config su una pagina semplice, che non richiede l'autenticazione. È necessario assicurarsi che corrisponda al registrato nel portale di Azure.You have to make sure that it matches with the `redirect_uri` registered in Azure portal. Ciò non influirà sull'esperienza di accesso dell'utente in quanto MSAL salva la pagina iniziale quando l'utente inizia il processo di accesso e reindirizza alla posizione esatta al termine dell'accesso.

## <a name="initialization-in-your-main-app-file"></a>Inizializzazione nel file principale dell'app

Se l'app è strutturata in modo che sia presente un file Javascript centrale che definisce l'inizializzazione, il routing `iframe` e altri elementi dell'app, puoi caricare in modo condizionale i moduli dell'app in base al fatto che l'app venga caricata o meno. Ad esempio:

In AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

In angolare: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

Componente Msal:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla creazione di [un'applicazione a pagina singola (SPA)](scenario-spa-overview.md) utilizzando MSAL.js.