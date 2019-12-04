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
ms.openlocfilehash: e0fd546724b8d684746a9f4d63a03bc6b58ded52
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764645"
---
# <a name="single-page-application-sign-in"></a>Applicazione a pagina singola: accesso

Informazioni su come aggiungere l'accesso al codice per l'applicazione a singola pagina.

Prima di poter ottenere i token per accedere alle API nell'applicazione, è necessario un contesto utente autenticato. È possibile eseguire l'accesso degli utenti all'applicazione in MSAL. js in due modi:

* [Finestra popup](#sign-in-with-a-pop-up-window), tramite il metodo `loginPopup`
* [Reindirizzamento](#sign-in-with-redirect), tramite il metodo `loginRedirect`

Facoltativamente, è anche possibile passare gli ambiti delle API per cui è necessario che l'utente acconsente al momento dell'accesso.

> [!NOTE]
> Se l'applicazione dispone già dell'accesso a un contesto utente autenticato o a un token ID, è possibile ignorare il passaggio di accesso e acquisire direttamente i token. Per informazioni dettagliate, vedere [SSO senza accesso a MSAL. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Scelta tra un'esperienza popup o un reindirizzamento

Non è possibile usare entrambi i metodi popup e redirect nell'applicazione. La scelta tra un'esperienza popup o un reindirizzamento dipende dal flusso dell'applicazione:

* Se non si vuole che gli utenti passino dalla pagina principale dell'applicazione durante l'autenticazione, è consigliabile usare il metodo popup. Poiché il reindirizzamento dell'autenticazione avviene in una finestra popup, lo stato dell'applicazione principale viene mantenuto.

* Se gli utenti dispongono di vincoli o criteri del browser in cui le finestre popup sono disabilitate, è possibile usare il metodo di reindirizzamento. Utilizzare il metodo di reindirizzamento con il browser Internet Explorer, perché sono presenti [problemi noti relativi alle finestre popup in Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

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

Il wrapper MSAL angolare consente di proteggere route specifiche nell'applicazione aggiungendo `MsalGuard` alla definizione di route. Questa funzione Guard richiama il metodo per accedere quando si accede a tale route.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Per un'esperienza di finestra popup, abilitare l'opzione di configurazione `popUp`. È anche possibile passare gli ambiti che richiedono il consenso come indicato di seguito:

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

## <a name="sign-in-with-redirect"></a>Accesso con Reindirizzamento

### <a name="javascript"></a>JavaScript

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

### <a name="angular"></a>Angular

Il codice qui è identico a quello descritto in precedenza nella sezione relativa all'accesso con una finestra popup. Il flusso predefinito è redirect.

> [!NOTE]
> Il token ID non contiene gli ambiti consentiti e rappresenta solo l'utente autenticato. Gli ambiti consentiti vengono restituiti nel token di accesso, che verrà acquisito nel passaggio successivo.

## <a name="sign-out"></a>Disconnessione

La libreria MSAL fornisce un metodo `logout` che cancella la cache nell'archivio del browser e invia una richiesta di disconnessione a Azure Active Directory (Azure AD). Dopo la disconnessione, la libreria reindirizza nuovamente alla pagina di avvio dell'applicazione per impostazione predefinita.

È possibile configurare l'URI a cui deve essere reindirizzato dopo la disconnessione impostando `postLogoutRedirectUri`. Questo URI deve essere registrato anche come URI di disconnessione nella registrazione dell'applicazione.

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
