---
title: Accesso all'app a pagina singola & disconnessione - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'applicazione a pagina singola (accesso)Learn how to build a single-page application (sign-in)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 7e809def048c95b6688a13ac99783615eb045d11
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885190"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Applicazione a pagina singola: Accesso e disconnessione

Informazioni su come aggiungere l'accesso al codice per l'applicazione a pagina singola.

Prima di poter ottenere i token per accedere alle API nell'applicazione, è necessario un contesto utente autenticato. È possibile accedere agli utenti dell'applicazione in MSAL.js in due modi:You can sign in users to your application in MSAL.js in two ways:

* [Finestra popup](#sign-in-with-a-pop-up-window), utilizzando `loginPopup` il metodo
* [Reindirizzare](#sign-in-with-redirect), `loginRedirect` utilizzando il metodo

Facoltativamente, è anche possibile passare gli ambiti delle API per le quali è necessario che l'utente acconsenta al momento dell'accesso.

> [!NOTE]
> Se l'applicazione ha già accesso a un contesto utente autenticato o a un token ID, è possibile ignorare il passaggio di accesso e acquisire direttamente i token. Per informazioni dettagliate, vedere [SSO senza accesso MSAL.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Scelta tra un'esperienza pop-up o di reindirizzamento

Non è possibile utilizzare entrambi i metodi popup e redirect nell'applicazione. La scelta tra un'esperienza popup o di reindirizzamento dipende dal flusso dell'applicazione:The choice between a pop-up or redirect experience depends on your application flow:

* Se non si desidera che gli utenti si allontanino dalla pagina principale dell'applicazione durante l'autenticazione, è consigliabile utilizzare il metodo popup. Poiché il reindirizzamento dell'autenticazione avviene in una finestra popup, lo stato dell'applicazione principale viene mantenuto.

* Se gli utenti hanno vincoli del browser o criteri in cui le finestre popup sono disabilitate, è possibile utilizzare il metodo di reindirizzamento. Utilizzare il metodo di reindirizzamento con il browser Internet Explorer, poiché esistono [problemi noti con le finestre popup in Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Accesso con una finestra popup

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

Il wrapper angolare MSAL consente di proteggere route `MsalGuard` specifiche nell'applicazione aggiungendo alla definizione della route. Questa protezione richiamerà il metodo per accedere quando si accede a tale route.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Per un'esperienza di finestra `popUp` popup, abilitare l'opzione di configurazione. È inoltre possibile passare gli ambiti che richiedono il consenso come segue:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Accedi con il reindirizzamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I metodi di reindirizzamento non restituiscono una promessa a causa dello spostamento dall'app principale. Per elaborare e accedere ai token restituiti, è necessario registrare i callback di esito positivo e di errore prima di chiamare i metodi di reindirizzamento.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

Il codice qui è lo stesso descritto in precedenza nella sezione sull'accesso con una finestra popup. Il flusso predefinito è redirect.

> [!NOTE]
> Il token ID non contiene gli ambiti consentiti e rappresenta solo l'utente autenticato. Gli ambiti autorizzati vengono restituiti nel token di accesso, che verrà acquisito nel passaggio successivo.

---

## <a name="sign-out"></a>Disconnessione

La libreria MSAL `logout` fornisce un metodo che cancella la cache nell'archiviazione del browser e invia una richiesta di disconnessione ad Azure Active Directory (Azure AD). Dopo la disconnessione, la libreria reindirizza alla pagina iniziale dell'applicazione per impostazione predefinita.

È possibile configurare l'URI a cui deve `postLogoutRedirectUri`essere reindirizzato dopo la disconnessione impostando . Questo URI deve essere registrato anche come URI di disconnessione nella registrazione dell'applicazione.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisizione di un token per l'app](scenario-spa-acquire-token.md)
