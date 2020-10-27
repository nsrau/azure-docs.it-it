---
title: "Esercitazione: Creare un'app Angular che usa Microsoft Identity Platform per l'autenticazione | Azure"
titleSuffix: Microsoft identity platform
description: In questa esercitazione si creerà un'app a pagina singola Angular che usa Microsoft Identity Platform per consentire l'accesso degli utenti e ottenere un token di accesso per chiamare l'API Microsoft Graph per loro conto.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: c93704130e150a7ca26144d4895e82756657fae2
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096250"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Esercitazione: Accesso utenti e chiamata dell'API Microsoft Graph da un'applicazione a pagina singola Angular

Questa esercitazione illustra come creare un'applicazione a pagina singola Angular che consente agli utenti di accedere con gli account Microsoft personali e con gli account aziendali e dell'istituto di istruzione e chiamare l'API Microsoft Graph per loro conto.

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un progetto Angular con `npm`
> * Registrare l'applicazione nel portale di Azure
> * Aggiungere il codice per supportare l'accesso e la disconnessione
> * Aggiungere il codice per chiamare l'API Microsoft Graph
> * Testare l'app

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/en/download/) per l'esecuzione di un server Web locale.
* [Visual Studio Code](https://code.visualstudio.com/download) o un altro editor per la modifica dei file di progetto.

## <a name="how-the-sample-app-works"></a>Funzionamento dell'app di esempio

![Diagramma che mostra come funziona l'app di esempio generata in questa esercitazione](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

L'applicazione di esempio creata in questa esercitazione consente a un'applicazione a pagina singola Angular di eseguire query sull'API Microsoft Graph o su un'API Web che accetta token rilasciati da Microsoft Identity Platform. Usa Microsoft Authentication Library (MSAL) per Angular, un wrapper della libreria MSAL.js di base. La libreria MSAL Angular consente alle applicazioni Angular della versione 6 e successive di autenticare gli utenti aziendali usando Azure Active Directory (Azure AD), nonché gli utenti con account Microsoft e identità dei social network come Facebook, Google e LinkedIn. La libreria consente inoltre di ottenere l'accesso a servizi cloud Microsoft e a Microsoft Graph.

Per questo scenario, dopo l'accesso di un utente, viene richiesto un token di accesso che viene aggiunto a richieste HTTP tramite l'intestazione dell'autorizzazione. L'acquisizione e il rinnovo dei token vengono gestiti da Microsoft Authentication Library (MSAL).

### <a name="libraries"></a>Librerie

Questa esercitazione la libreria seguente:

|Libreria|Descrizione|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library per il wrapper Angular JavaScript|

Il codice sorgente per la libreria MSAL.js è disponibile nel repository [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) in GitHub.

## <a name="create-your-project"></a>Creare il progetto

Generare una nuova applicazione Angular usando i comandi npm seguenti:

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Registrare l'applicazione

Seguire le [istruzioni per registrare un'applicazione a pagina singola](./scenario-spa-app-registration.md) nel portale di Azure.

Nella pagina **Panoramica** dell'app della registrazione prendere nota del valore di **ID applicazione (client)** che sarà necessario in seguito.

Registrare il valore dell' **URI di reindirizzamento** come **http://localhost:4200/** e abilitare le impostazioni di concessione implicita.

## <a name="configure-the-application"></a>Configurare l'applicazione

1. Nella cartella *src/app* modificare *app.module.ts* e aggiungere il modulo `MSALModule` a `imports` e la costante `isIE`:

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Sostituire questi valori:

    |Nome del valore|Informazioni|
    |---------|---------|
    |Enter_the_Application_Id_Here|Nella pagina **Panoramica** della registrazione dell'applicazione, è il valore di **ID applicazione (client)** . |
    |Enter_the_Cloud_Instance_Id_Here|Si tratta dell'istanza del cloud di Azure. Per il cloud globale o principale di Azure, immettere **https://login.microsoftonline.com** . Per i cloud nazionali (ad esempio, Cina), vedere [Cloud nazionali](./authentication-national-cloud.md).|
    |Enter_the_Tenant_Info_Here| Impostare il valore su una delle opzioni seguenti: Se l'applicazione supporta *account in questa directory dell'organizzazione* , sostituire questo valore con l'ID della directory (tenant) o il nome del tenant (ad esempio, **contoso.microsoft.com** ). Se l'applicazione supporta *Account in qualsiasi directory organizzativa* , sostituire questo valore con **organizations** . Se l'applicazione supporta *Account in qualsiasi directory organizzativa e account Microsoft personali* , sostituire questo valore con **common** . Per limitare il supporto ai *soli account Microsoft personali* , sostituire questo valore con **consumers** . |
    |Enter_the_Redirect_Uri_Here|Sostituire con **http://localhost:4200** .|

    Per altre informazioni sulle opzioni configurabili disponibili, vedere [Inizializzare le applicazioni client](msal-js-initializing-client-applications.md).

2. All'inizio dello stesso file aggiungere l'istruzione import seguente:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Aggiungere le istruzioni import seguenti all'inizio del file `src/app/app.component.ts`:

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Accesso di un utente

Aggiungere il codice seguente a `AppComponent` per consentire l'accesso di un utente:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> È consigliabile usare `loginRedirect` per gli utenti di Internet Explorer.

## <a name="acquire-a-token"></a>Acquisire un token

### <a name="angular-interceptor"></a>Intercettore Angular

Microsoft Authentication Library per Angular fornisce una classe `Interceptor` che acquisisce automaticamente i token per le richieste in uscita che usano il client `http` Angular per le risorse protette note.

In primo luogo, includere la classe `Interceptor` come provider per l'applicazione:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Fornire quindi una mappa delle risorse protette a `MsalModule.forRoot()` come `protectedResourceMap` e includere tali ambiti in `consentScopes`. Gli URL specificati nella raccolta `protectedResourceMap` fanno distinzione tra maiuscole e minuscole.

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Recuperare infine il profilo di un utente con una richiesta HTTP:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent, acquireTokenPopup, acquireTokenRedirect
In Microsoft Authentication Library sono disponibili tre metodi per acquisire i token: `acquireTokenRedirect`, `acquireTokenPopup` e `acquireTokenSilent`. Tuttavia, per le app Angular è consigliabile usare la classe `MsalInterceptor`, come illustrato nella sezione precedente.

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Il metodo `acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo aver eseguito il metodo `loginRedirect` o `loginPopup` la prima volta, per le chiamate successive il metodo comunemente usato per ottenere i token usati per accedere a risorse protette è `acquireTokenSilent`. Le chiamate per richiedere o rinnovare i token vengono eseguite in modo invisibile per l'utente.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

Nel codice `scopes` contiene gli ambiti da restituire nel token di accesso per l'API.

Ad esempio:

* `["user.read"]` per Microsoft Graph
* `["<Application ID URL>/scope"]` per le API Web personalizzate, ovvero `api://<Application ID>/access_as_user`

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

Talvolta è necessario che l'utente interagisca con l'endpoint Microsoft Identity Platform. Ad esempio:

* Un utente deve immettere nuovamente le credenziali perché la password è scaduta.
* L'applicazione richiede l'accesso ad ambiti di risorse aggiuntivi per cui è necessario il consenso dell'utente.
* È necessaria l'autenticazione a due fattori.

Per la maggior parte delle applicazioni, l'approccio consigliato è quello di chiamare prima `acquireTokenSilent`, quindi individuare l'eccezione e infine chiamare `acquireTokenPopup` (o `acquireTokenRedirect`) per avviare una richiesta interattiva.

La chiamata di `acquireTokenPopup` restituisce una finestra di accesso popup. In alternativa, `acquireTokenRedirect` reindirizza gli utenti all'endpoint Microsoft Identity Platform. In questa finestra gli utenti devono confermare le proprie credenziali, fornire il consenso per la risorsa necessaria o completare l'autenticazione a due fattori.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> Questa guida di avvio rapido usa i metodi `loginRedirect` e `acquireTokenRedirect` con Microsoft Internet Explorer a causa di un [problema noto](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) correlato alla gestione delle finestre popup da questo browser.

## <a name="log-out"></a>Effettuare la disconnessione

Aggiungere il codice seguente per disconnettere un utente:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Aggiungere l'interfaccia utente
Vedere l'[applicazione di esempio](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) per un esempio di come aggiungere l'interfaccia utente tramite la libreria dei componenti di Material Design Angular.

## <a name="test-your-code"></a>Testare il codice

1.  Avviare il server Web in modo che rimanga in ascolto sulla porta eseguendo questi comandi da un prompt della riga di comando nella cartella dell'applicazione:

    ```bash
    npm install
    npm start
    ```
1. Nel browser immettere **http://localhost:4200** o **http://localhost:{port}** , dove *porta* è la porta su cui il server Web è in ascolto.


### <a name="provide-consent-for-application-access"></a>Specificare il consenso per l'accesso all'applicazione

La prima volta che si avvia l'accesso all'applicazione, viene chiesto di concedere l'accesso al proprio profilo e di consentire l'accesso:

![Finestra "Autorizzazioni richieste"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Aggiungere ambiti e autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. Ad esempio, l'API Microsoft Graph richiede l'ambito *Calendars.Read* per generare l'elenco dei calendari dell'utente.

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata *Calendars.Read* alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito *Calendars.Read* alla chiamata `acquireTokenSilent`.

>[!NOTE]
>Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

Se un'API back-end non richiede alcun ambito (scelta non consigliata), è possibile usare *clientId* come ambito nelle chiamate per acquisire i token.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per approfondire ulteriormente lo sviluppo di applicazioni a pagina singola in Microsoft Identity Platform, è possibile vedere la serie di articoli in più parti.

> [!div class="nextstepaction"]
> [Scenario: Applicazione a pagina singola](scenario-spa-overview.md)
