---
title: Applicazione a pagina singola (accesso)-piattaforma di identità Microsoft
description: Informazioni su come creare un'applicazione a singola pagina (accesso)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bf614a5523e78fc72918db973ef8d738a171fff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031782"
---
# <a name="single-page-application---sign-in"></a>Applicazione a pagina singola-accesso

Informazioni su come aggiungere l'accesso al codice per l'applicazione a singola pagina.

Prima di poter ottenere i token per accedere alle API nell'applicazione, sarà necessario un contesto utente autenticato. È possibile eseguire l'accesso degli utenti all'applicazione in MSAL. js in due modi:

* [Accedere con una finestra popup](#sign-in-with-a-pop-up-window) usando `loginPopup` il metodo
* [Accedi con](#sign-in-with-redirect) il metodo tramite `loginRedirect` Reindirizzamento

Facoltativamente, è anche possibile passare gli ambiti delle API per cui è necessario che l'utente acconsente al momento dell'accesso.

> [!NOTE]
> Se l'applicazione dispone già dell'accesso a un contesto utente autenticato o a un token ID, è possibile ignorare il passaggio di accesso e acquisire direttamente i token. Per altri dettagli, vedere [SSO senza accesso a MSAL. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Scelta tra un'esperienza popup o un reindirizzamento

Non è possibile usare una combinazione dei metodi popup e redirect nell'applicazione. La scelta tra un'esperienza popup o un reindirizzamento dipende dal flusso dell'applicazione.

* Se non si vuole che l'utente passi dalla pagina principale dell'applicazione durante l'autenticazione, è consigliabile usare i metodi popup. Poiché il reindirizzamento dell'autenticazione avviene in una finestra popup, lo stato dell'applicazione principale viene mantenuto.

* In alcuni casi potrebbe essere necessario utilizzare i metodi di reindirizzamento. Se gli utenti dell'applicazione hanno vincoli o criteri del browser in cui le finestre popup sono disabilitate, è possibile usare i metodi di reindirizzamento. Usare i metodi di reindirizzamento con il browser Internet Explorer poiché si verificano alcuni [problemi noti di Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) durante la gestione delle finestre popup.

## <a name="sign-in-with-a-pop-up-window"></a>Accedere con una finestra popup

### <a name="javascript"></a>JavaScript

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

### <a name="angular"></a>Angular

Il wrapper MSAL angolare consente di proteggere route specifiche nell'applicazione semplicemente aggiungendo `MsalGuard` alla definizione di route. Questa funzione Guard richiama il metodo per accedere quando si accede a tale route.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Per un'esperienza di finestra popup, abilitare l' `popUp` opzione config. È anche possibile passare gli ambiti che richiedono il consenso come indicato di seguito:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Accedi con Reindirizzamento

### <a name="javascript"></a>JavaScript

I metodi di reindirizzamento non restituiscono una promessa a causa della navigazione dall'app principale. Per elaborare e accedere ai token restituiti, sarà necessario registrare i callback di esito positivo ed errore prima di chiamare i metodi di reindirizzamento.

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

### <a name="angular"></a>Angular

Il codice qui è identico a quello descritto in precedenza nella sezione accedere con una finestra popup. Il flusso predefinito è redirect.

> [!NOTE]
> Il token ID non contiene gli ambiti consentiti e rappresenta solo l'utente autenticato. Gli ambiti consentiti vengono restituiti nel token di accesso che verrà acquisito nel passaggio successivo.

## <a name="sign-out"></a>Esci

La libreria MSAL fornisce un `logout` metodo che cancellerà la cache nell'archivio del browser e invierà una richiesta di disconnessione a Azure ad. Dopo la disconnessione, viene reindirizzato di nuovo alla pagina di avvio dell'applicazione per impostazione predefinita.

È possibile configurare l'URI a cui deve essere reindirizzato dopo la disconnessione `postLogoutRedirectUri`impostando il. Questo URI deve essere registrato anche come URI di disconnessione nella registrazione dell'applicazione.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisizione di un token per l'app](scenario-spa-acquire-token.md)
