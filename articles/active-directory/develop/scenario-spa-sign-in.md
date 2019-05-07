---
title: Applicazione a singola pagina (accesso) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione a singola pagina (Accedi)
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
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138794"
---
# <a name="single-page-application---sign-in"></a>Applicazione a singola pagina - accesso

Informazioni su come aggiungere accesso al codice per l'applicazione a pagina singola.

Prima di poter ottenere i token per accedere alle API nell'applicazione, è necessario un contesto dell'utente autenticato. È possibile accedere gli utenti all'applicazione in msal. js in due modi:

* [Accedere con una finestra popup](#sign-in-with-a-pop-up-window) usando `loginPopup` (metodo)
* [Accesso con reindirizzamento](#sign-in-with-redirect) usando `loginRedirect` (metodo)

È possibile passare facoltativamente anche gli ambiti delle API per cui è necessario il consenso al momento dell'accesso all'utente.

> [!NOTE]
> Se l'applicazione contiene già l'accesso a un contesto dell'utente autenticato o un id token, è possibile ignorare il passaggio di accesso e acquisire direttamente i token. Per altre informazioni, vedere [sso senza account di accesso di msal. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Scelta tra un'esperienza di popup o di reindirizzamento

È possibile usare una combinazione di metodi di reindirizzamento sia il menu a comparsa nell'applicazione. La scelta tra un'esperienza di menu a comparsa o reindirizzamento dipende il flusso dell'applicazione.

* Se non si desidera all'utente di uscire dalla pagina principale dell'applicazione durante l'autenticazione, è consigliabile utilizzare i metodi a comparsa. Poiché il reindirizzamento di autenticazione si verifica in una finestra popup, viene mantenuto lo stato dell'applicazione principale.

* Esistono casi in cui potrebbe essere necessario usare i metodi di reindirizzamento. Se gli utenti dell'applicazione dispongono di vincoli del browser o i criteri in cui le finestre popup sono disabilitate, è possibile usare i metodi di reindirizzamento. Usare i metodi di reindirizzamento con browser Internet Explorer perché non vi sono determinati [problemi noti di Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) quando si gestiscono le finestre popup.

## <a name="sign-in-with-a-pop-up-window"></a>Accedere con una finestra popup

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
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

Il wrapper MSAL Angular consente di proteggere le route specifiche nell'applicazione aggiungendo semplicemente il `MsalGuard` alla definizione della route. La guardia richiamerà il metodo di accesso quando si accede a tale route.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Per un'esperienza di finestra a comparsa, abilitare il `popUp` opzione config. È anche possibile passare gli ambiti che richiedono il consenso come indicato di seguito:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Accedi con il reindirizzamento

### <a name="javascript"></a>JavaScript

I metodi di reindirizzamento non restituiscono una promessa a causa di un riquadro di spostamento dall'app principale. Per elaborare e il token restituito di accesso, si dovrà registrare callback di esito positivo e negativo prima di chiamare i metodi di reindirizzamento.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Qui il codice è identico a quello descritto in precedenza con l'accesso con una sezione di finestra a comparsa. Il flusso predefinito è di reindirizzamento.

> [!NOTE]
> Il token ID non contiene gli ambiti di stato fornito il consenso e rappresenta solo l'utente autenticato. Gli ambiti di stato fornito il consenso vengono restituiti nel token di accesso che verrà acquisito nel passaggio successivo.

## <a name="sign-out"></a>Disconnessione

La libreria MSAL fornisce un `logout` metodo che verrà cancellato la cache nell'archivio del browser e invia una richiesta di disconnessione ad Azure AD. Dopo la disconnessione, viene reindirizzato alla pagina iniziale dell'applicazione per impostazione predefinita.

È possibile configurare l'URI a cui deve reindirizzare dopo la disconnessione out impostando il `postLogoutRedirectUri`. Questo URI deve essere registrato anche come URI Logout nella registrazione dell'applicazione.

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
> [Acquisire un token per l'app](scenario-spa-acquire-token.md)
