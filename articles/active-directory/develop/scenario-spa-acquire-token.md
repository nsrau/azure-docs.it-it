---
title: Acquisire un token per chiamare un'API Web (app a singola pagina)-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'applicazione a singola pagina (acquisire un token per chiamare un'API)
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: eeba01a609a1a21ed564c0b9cb78a28a4ad5c95a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882319"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Applicazione a pagina singola: acquisire un token per chiamare un'API

Il modello per l'acquisizione di token per le API con MSAL.js consiste nel tentare prima una richiesta di token invisibile all'utente usando il `acquireTokenSilent` metodo. Quando viene chiamato questo metodo, la libreria controlla prima di tutto la cache nell'archivio browser per verificare se esiste un token valido e lo restituisce. Quando non è presente alcun token valido nella cache, invia una richiesta di token invisibile all'utente Azure Active Directory (Azure AD) da un iframe nascosto. Questo metodo consente inoltre alla libreria di rinnovare i token. Per ulteriori informazioni sui valori di durata Single Sign-On sessione e token in Azure AD, vedere [durate dei token](active-directory-configurable-token-lifetimes.md).

Le richieste di token invisibile al Azure AD potrebbero non riuscire per motivi quali una sessione di Azure AD scaduta o una modifica della password. In tal caso, è possibile richiamare uno dei metodi interattivi (che richiederanno all'utente) di acquisire i token:

* [Finestra popup](#acquire-a-token-with-a-pop-up-window), tramite`acquireTokenPopup`
* [Reindirizzamento](#acquire-a-token-with-a-redirect)tramite`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Scegliere tra un'esperienza popup o un reindirizzamento

 Non è possibile usare entrambi i metodi popup e redirect nell'applicazione. La scelta tra un'esperienza popup o un reindirizzamento dipende dal flusso dell'applicazione:

* Se non si vuole che gli utenti passino dalla pagina principale dell'applicazione durante l'autenticazione, è consigliabile usare il metodo popup. Poiché il reindirizzamento dell'autenticazione avviene in una finestra popup, lo stato dell'applicazione principale viene mantenuto.

* Se gli utenti dispongono di vincoli o criteri del browser in cui le finestre popup sono disabilitate, è possibile usare il metodo di reindirizzamento. Utilizzare il metodo di reindirizzamento con il browser Internet Explorer, perché sono presenti [problemi noti relativi alle finestre popup in Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

È possibile impostare gli ambiti dell'API che si desidera includere nel token di accesso quando si compila la richiesta del token di accesso. Si noti che tutti gli ambiti richiesti potrebbero non essere concessi nel token di accesso. Dipende dal consenso dell'utente.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Acquisire un token con una finestra popup

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il codice seguente combina il modello descritto in precedenza con i metodi per un'esperienza popup:

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

Il wrapper angolare MSAL fornisce l'intercettore HTTP, che acquisisce automaticamente i token di accesso in modo invisibile all'utente e li associa alle richieste HTTP alle API.

È possibile specificare gli ambiti per le API nell' `protectedResourceMap` opzione di configurazione. `MsalInterceptor`questi ambiti verranno richiesti durante l'acquisizione automatica dei token.

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

Per avere esito positivo e negativo dell'acquisizione del token invisibile all'utente, MSAL angolare fornisce callback che è possibile sottoscrivere. È anche importante ricordare di annullare la sottoscrizione.

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

In alternativa, è possibile acquisire in modo esplicito i token usando i metodi di acquisizione dei token come descritto nella libreria principale MSAL.js.

---

## <a name="acquire-a-token-with-a-redirect"></a>Acquisire un token con un reindirizzamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il modello seguente è descritto in precedenza, ma mostrato con un metodo di reindirizzamento per acquisire i token in modo interattivo. È necessario registrare il callback di reindirizzamento come indicato in precedenza.

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

## <a name="request-optional-claims"></a>Richiedi attestazioni facoltative

È possibile utilizzare le attestazioni facoltative per gli scopi seguenti:

- Includere attestazioni aggiuntive nei token per l'applicazione.
- Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
- Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.

Per richiedere attestazioni facoltative in `IdToken` , è possibile inviare un oggetto Claims file al `claimsRequest` campo della `AuthenticationParameters.ts` classe.

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

Per altre informazioni, vedere [attestazioni facoltative](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Questo codice è identico a quello descritto in precedenza.

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamata a un'API Web](scenario-spa-call-api.md)
