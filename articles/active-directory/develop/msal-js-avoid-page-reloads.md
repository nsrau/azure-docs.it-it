---
title: Evitare ricaricamenti di pagina (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come evitare il ricaricamento delle pagine quando si acquisisce e si rinnovano i token in modo invisibile all'utente usando Microsoft Authentication Library per JavaScript (MSAL. js).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d1ef1f76b1b1a807f48f7c79e41ada68b561fc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916436"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Evitare il ricaricamento delle pagine quando si acquisisce e si rinnovano i token in modo invisibile all'utente con MSAL. js
Microsoft Authentication Library per JavaScript (MSAL. js) usa elementi `iframe` nascosti per acquisire e rinnovare i token in modo invisibile all'utente in background. Azure AD restituisce il token al redirect_uri registrato specificato nella richiesta di token (per impostazione predefinita, si tratta della pagina radice dell'app). Poiché la risposta è 302, il codice HTML corrispondente al `redirect_uri` viene caricato nel `iframe`. In genere, il `redirect_uri` dell'app è la pagina radice e questo ne comporta il ricaricamento.

In altri casi, se si passa alla pagina radice dell'app è necessaria l'autenticazione, potrebbero verificarsi elementi annidati `iframe` o `X-Frame-Options: deny` errore.

Poiché MSAL. js non è in grado di eliminare il 302 emesso dal Azure AD ed è necessario per elaborare il token restituito, non può impedire il caricamento del `redirect_uri` nel `iframe`.

Per evitare di ricaricare l'intera app o altri errori causati da questo problema, seguire queste soluzioni alternative.

## <a name="specify-different-html-for-the-iframe"></a>Specificare codice HTML diverso per l'iframe

Impostare la proprietà `redirect_uri` in config su una pagina semplice che non richiede l'autenticazione. È necessario assicurarsi che corrisponda al `redirect_uri` registrato in portale di Azure. Questa operazione non influirà sull'esperienza di accesso dell'utente, perché MSAL salva la pagina iniziale quando l'utente inizia il processo di accesso e reindirizza al percorso esatto dopo il completamento dell'accesso.

## <a name="initialization-in-your-main-app-file"></a>Inizializzazione nel file dell'app principale

Se l'app è strutturata in modo che sia presente un file JavaScript centrale che definisce l'inizializzazione dell'app, il routing e altri elementi, è possibile caricare in modo condizionale i moduli dell'app a seconda che l'app venga caricata o meno in un `iframe`. ad esempio:

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

In angolare: app. Module. TS

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
Altre informazioni sulla [creazione di un'applicazione a singola pagina (Spa)](scenario-spa-overview.md) con MSAL. js.