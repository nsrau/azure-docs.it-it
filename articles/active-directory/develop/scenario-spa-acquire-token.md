---
title: Acquisire un token per chiamare un'API Web (app a pagina singola) - Piattaforma di identità Microsoft . Azure
description: Informazioni su come compilare un'applicazione a pagina singola (acquisire un token per chiamare un'API)Learn how to build a single-page application (acquire a token to call an API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 393c3a06a2366a7d6947faf8bbfe038d6c5982fc
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419653"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Applicazione a pagina singola: acquisire un token per chiamare un'APISingle-page application: Acquire a token to call an API

Il modello per l'acquisizione di token per le API con MSAL.js `acquireTokenSilent` consiste nel tentare prima una richiesta di token invisibile all'utente utilizzando il metodo . Quando questo metodo viene chiamato, la libreria controlla innanzitutto la cache nella memoria del browser per verificare se esiste un token valido e lo restituisce. Quando nella cache non è presente alcun token valido, invia una richiesta di token invisibile all'utente ad Azure Active Directory (Azure AD) da un iframe nascosto. Questo metodo consente inoltre alla libreria di rinnovare i token. Per altre informazioni sui valori della durata della sessione Single Sign-On e del token in Azure AD, vedere [Durata dei token.](active-directory-configurable-token-lifetimes.md)

Le richieste di token invisibile all'utente in Azure AD potrebbero non riuscire per motivi come una sessione di Azure AD scaduta o una modifica della password. In tal caso, è possibile richiamare uno dei metodi interattivi (che richiederà all'utente) di acquisire i token:

* [Finestra pop-up](#acquire-a-token-with-a-pop-up-window), utilizzando`acquireTokenPopup`
* [Reindirizzare](#acquire-a-token-with-a-redirect), utilizzando`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Scegliere tra un'esperienza pop-up o di reindirizzamento

 Non è possibile utilizzare entrambi i metodi popup e redirect nell'applicazione. La scelta tra un'esperienza popup o di reindirizzamento dipende dal flusso dell'applicazione:The choice between a pop-up or redirect experience depends on your application flow:

* Se non si desidera che gli utenti si allontanino dalla pagina principale dell'applicazione durante l'autenticazione, è consigliabile utilizzare il metodo popup. Poiché il reindirizzamento dell'autenticazione avviene in una finestra popup, lo stato dell'applicazione principale viene mantenuto.

* Se gli utenti hanno vincoli del browser o criteri in cui le finestre popup sono disabilitate, è possibile utilizzare il metodo di reindirizzamento. Utilizzare il metodo di reindirizzamento con il browser Internet Explorer, poiché esistono [problemi noti con le finestre popup in Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

È possibile impostare gli ambiti API che si desidera includere nel token di accesso durante la compilazione della richiesta del token di accesso. Si noti che tutti gli ambiti richiesti potrebbero non essere concessi nel token di accesso. Dipende dal consenso dell'utente.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Acquisire un token con una finestra popupAcquire a token with a pop-up window

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il codice seguente combina il modello descritto in precedenza con i metodi per un'esperienza popup:The following code combines the previously described pattern with the methods for a pop-up experience:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

Il wrapper angolare MSAL fornisce l'intercettore HTTP, che acquisirà automaticamente i token di accesso in modo invisibile all'utente e li allegherà alle richieste HTTP alle API.

È possibile specificare gli ambiti per `protectedResourceMap` le API nell'opzione di configurazione. `MsalInterceptor`questi ambiti durante l'acquisizione automatica dei token.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Per l'esito positivo e negativo dell'acquisizione di token invisibile all'utente, MSAL Angular fornisce callback che è possibile sottoscrivere. È anche importante ricordarsi di annullare l'iscrizione.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

In alternativa, è possibile acquisire in modo esplicito i token usando i metodi acquire-token come descritto nella libreria MSAL.js di base.

---

## <a name="acquire-a-token-with-a-redirect"></a>Acquisire un token con un reindirizzamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il modello seguente è come descritto in precedenza, ma illustrato con un metodo di reindirizzamento per acquisire i token in modo interattivo. È necessario registrare il callback di reindirizzamento come indicato in precedenza.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Richiedere attestazioni facoltativeRequest optional claims

È possibile utilizzare attestazioni facoltative per gli scopi seguenti:You can use optional claims for the following purposes:

- Includere attestazioni aggiuntive nei token per l'applicazione.
- Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
- Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.

Per richiedere attestazioni facoltative in `IdToken`, è `claimsRequest` possibile inviare un oggetto attestazioni stringato al campo della `AuthenticationParameters.ts` classe.

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

Per altre informazioni, vedere [Attestazioni facoltative](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Questo codice è lo stesso descritto in precedenza.

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamata a un'API Web](scenario-spa-call-api.md)
