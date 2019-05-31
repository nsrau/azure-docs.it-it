---
title: Evitare di Ricarica pagina (Microsoft Authentication Library per JavaScript) | Azure
description: Informazioni su come evitare ricaricamenti pagina durante l'acquisizione e token rinnovati automaticamente utilizzando Microsoft Authentication Library per JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420469"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Evitare ricaricamenti pagina durante l'acquisizione e token rinnovati automaticamente usando msal. js
Microsoft Authentication Library per JavaScript (msal) utilizza nascosto `iframe` elementi per acquisire e rinnovare i token in modo invisibile in background. Azure AD restituisce il token all'URI di reindirizzamento registrato specificato nella richiesta del token (per impostazione predefinita si tratta di pagina radice dell'app). Poiché la risposta è 302, viene generato il codice HTML corrispondente di `redirect_uri` caricati nel `iframe`. In genere dell'app `redirect_uri` è la pagina radice e questo ne determina il ricaricamento.

In altri casi, se passando alla pagina principale dell'app richiede l'autenticazione, può portare a annidato `iframe` elementi o `X-Frame-Options: deny` errore.

Poiché msal. js non è possibile chiudere il 302 rilasciato da Azure AD ed è necessario per elaborare il token restituito, non può impedire che il `redirect_uri` da caricati nel `iframe`.

Per evitare l'intera app ricaricamento nuovamente o altri errori causati per questo motivo, seguire queste soluzioni alternative.

## <a name="specify-different-html-for-the-iframe"></a>Specificare diverse HTML di iframe

Impostare il `redirect_uri` proprietà sul file di configurazione a una pagina semplice, che non richiede l'autenticazione. È necessario assicurarsi che la corrispondenza con il `redirect_uri` registrati nel portale di Azure. Ciò non influirà esperienza di accesso dell'utente come MSAL consente di salvare la pagina iniziale quando l'utente inizia il processo di accesso e al termine dell'account di accesso viene reindirizzato alla posizione esatta.

## <a name="initialization-in-your-main-app-file"></a>Inizializzazione di file dell'app principale

Se l'app è strutturato in modo che ci sia un file Javascript centrale che definisce l'inizializzazione dell'app, il routing e altro software, è possibile caricare in modo condizionale i moduli di app basati sul fatto che l'app viene caricata un' `iframe` oppure No. Ad esempio:

In AngularJS: app. js

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

In Angular: TS

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

MsalComponent:

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
Altre informazioni sulle [compilazione di un'applicazione a pagina singola (SPA)](scenario-spa-overview.md) usando msal. js.