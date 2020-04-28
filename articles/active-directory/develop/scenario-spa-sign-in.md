---
title: "Accesso all'app a singola pagina & Sign-out: piattaforma di identità Microsoft | Azure"
description: Informazioni su come creare un'applicazione a singola pagina (accesso)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885190"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Applicazione a singola pagina: accesso e disconnessione

Informazioni su come aggiungere l'accesso al codice per l'applicazione a singola pagina.

Prima di poter ottenere i token per accedere alle API nell'applicazione, è necessario un contesto utente autenticato. È possibile eseguire l'accesso degli utenti all'applicazione in MSAL. js in due modi:

* [Finestra popup](#sign-in-with-a-pop-up-window), tramite il `loginPopup` metodo
* [Reindirizzamento](#sign-in-with-redirect), tramite il `loginRedirect` metodo

Facoltativamente, è anche possibile passare gli ambiti delle API per cui è necessario che l'utente acconsente al momento dell'accesso.

> [!NOTE]
> Se l'applicazione dispone già dell'accesso a un contesto utente autenticato o a un token ID, è possibile ignorare il passaggio di accesso e acquisire direttamente i token. Per informazioni dettagliate, vedere [SSO senza accesso a MSAL. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Scelta tra un'esperienza popup o un reindirizzamento

Non è possibile usare entrambi i metodi popup e redirect nell'applicazione. La scelta tra un'esperienza popup o un reindirizzamento dipende dal flusso dell'applicazione:

* Se non si vuole che gli utenti passino dalla pagina principale dell'applicazione durante l'autenticazione, è consigliabile usare il metodo popup. Poiché il reindirizzamento dell'autenticazione avviene in una finestra popup, lo stato dell'applicazione principale viene mantenuto.

* Se gli utenti dispongono di vincoli o criteri del browser in cui le finestre popup sono disabilitate, è possibile usare il metodo di reindirizzamento. Utilizzare il metodo di reindirizzamento con il browser Internet Explorer, perché sono presenti [problemi noti relativi alle finestre popup in Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Accedere con una finestra popup

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

Il wrapper MSAL angolare consente di proteggere route specifiche nell'applicazione aggiungendo `MsalGuard` alla definizione di route. Questa funzione Guard richiama il metodo per accedere quando si accede a tale route.

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

Per un'esperienza di finestra popup, abilitare l' `popUp` opzione di configurazione. È anche possibile passare gli ambiti che richiedono il consenso come indicato di seguito:

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

## <a name="sign-in-with-redirect"></a>Accesso con Reindirizzamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I metodi di reindirizzamento non restituiscono una promessa a causa dell'allontanamento dall'app principale. Per elaborare e accedere ai token restituiti, è necessario registrare i callback di esito positivo ed errore prima di chiamare i metodi di reindirizzamento.

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

Il codice qui è identico a quello descritto in precedenza nella sezione relativa all'accesso con una finestra popup. Il flusso predefinito è redirect.

> [!NOTE]
> Il token ID non contiene gli ambiti consentiti e rappresenta solo l'utente autenticato. Gli ambiti consentiti vengono restituiti nel token di accesso, che verrà acquisito nel passaggio successivo.

---

## <a name="sign-out"></a>Disconnessione

La libreria MSAL fornisce un `logout` metodo che cancella la cache nell'archivio del browser e invia una richiesta di disconnessione a Azure Active Directory (Azure ad). Dopo la disconnessione, la libreria reindirizza nuovamente alla pagina di avvio dell'applicazione per impostazione predefinita.

È possibile configurare l'URI a cui deve essere reindirizzato dopo la disconnessione `postLogoutRedirectUri`impostando. Questo URI deve essere registrato anche come URI di disconnessione nella registrazione dell'applicazione.

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
