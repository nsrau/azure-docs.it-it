---
title: Applicazione a singola pagina (acquisire un token per chiamare un'API)-piattaforma di identità Microsoft
description: Informazioni su come creare un'applicazione a singola pagina (acquisire un token per chiamare un'API)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135702"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Applicazione a pagina singola: acquisire un token per chiamare un'API

Il modello per l'acquisizione di token per le API con MSAL. js consiste nel tentare prima una richiesta di token invisibile `acquireTokenSilent` all'utente usando il metodo. Quando viene chiamato questo metodo, la libreria controlla prima di tutto la cache nell'archivio del browser per verificare se esiste un token valido e lo restituisce. Quando non è presente alcun token valido nella cache, invia una richiesta di token invisibile all'utente Azure Active Directory (Azure AD) da un iframe nascosto. Questo metodo consente inoltre alla libreria di rinnovare i token. Per ulteriori informazioni sui valori di durata Single Sign-On sessione e token in Azure AD, vedere [durate dei token](active-directory-configurable-token-lifetimes.md).

Per alcuni motivi, ad esempio una sessione di Azure AD scaduta o una modifica della password, è possibile che le richieste di token invisibile al Azure AD non vengano riuscite. In tal caso, è possibile richiamare uno dei metodi interattivi (che richiederanno all'utente) di acquisire i token.

* [Acquisire un token con una finestra popup](#acquire-token-with-a-pop-up-window) con`acquireTokenPopup`
* [Acquisire il token con reindirizzamento](#acquire-token-with-redirect) usando`acquireTokenRedirect`

**Scelta tra un'esperienza popup o un reindirizzamento**

 Non è possibile usare una combinazione dei metodi popup e redirect nell'applicazione. La scelta tra un'esperienza popup o un reindirizzamento dipende dal flusso dell'applicazione.

* Se non si vuole che l'utente passi dalla pagina principale dell'applicazione durante l'autenticazione, è consigliabile usare i metodi popup. Poiché il reindirizzamento dell'autenticazione avviene in una finestra popup, lo stato dell'applicazione principale viene mantenuto.

* In alcuni casi potrebbe essere necessario utilizzare i metodi di reindirizzamento. Se gli utenti dell'applicazione hanno vincoli o criteri del browser in cui le finestre popup sono disabilitate, è possibile usare i metodi di reindirizzamento. Si consiglia inoltre di utilizzare i metodi di reindirizzamento con il browser Internet Explorer poiché si verificano alcuni [problemi noti di Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) quando si gestiscono le finestre popup.

È possibile impostare gli ambiti dell'API che si desidera includere nel token di accesso quando si compila la richiesta del token di accesso. Si noti che tutti gli ambiti richiesti potrebbero non essere concessi nel token di accesso e dipendono dal consenso dell'utente.

## <a name="acquire-token-with-a-pop-up-window"></a>Acquisire un token con una finestra popup

### <a name="javascript"></a>JavaScript

Il modello precedente usa i metodi per un'esperienza popup:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
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

### <a name="angular"></a>Angular

Il wrapper MSAL angolare consente di aggiungere l'intercettore HTTP, che acquisisce automaticamente i token di accesso in modo invisibile all'utente e li associa alle richieste HTTP alle API.

È possibile specificare gli ambiti per le API nell' `protectedResourceMap` opzione config, che verrà richiesta da MsalInterceptor durante l'acquisizione automatica dei token.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

Per avere esito positivo e negativo dell'acquisizione del token invisibile all'utente, MSAL angolare fornisce i callback che è possibile sottoscrivere. È anche importante ricordare di annullare la sottoscrizione.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

In alternativa, è anche possibile acquisire in modo esplicito i token usando i metodi di acquisizione del token, come descritto nella libreria MSAL. js di base.

## <a name="acquire-token-with-redirect"></a>Acquisisci token con Reindirizzamento

### <a name="javascript"></a>JavaScript

Il modello è descritto in precedenza, ma viene visualizzato con un metodo di reindirizzamento per acquisire il token in modo interattivo. Sarà necessario registrare il callback di reindirizzamento come indicato in precedenza.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-for-optional-claims"></a>Richiedi attestazioni facoltative
È possibile richiedere attestazioni facoltative nell'app per specificare quali attestazioni aggiuntive includere nei token per l'applicazione. Per richiedere attestazioni facoltative in token ID, è possibile inviare un oggetto Claims file al campo claimsRequest della classe AuthenticationParameters. TS.

È possibile utilizzare le attestazioni facoltative per lo scopo seguente:

- Per includere attestazioni aggiuntive nei token per l'applicazione.
- Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
- Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.


### <a name="javascript"></a>JavaScript
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
Per altre informazioni sulle attestazioni facoltative, Estrai [attestazioni facoltative](active-directory-optional-claims.md)


### <a name="angular"></a>Angular

Questa operazione è identica a quella descritta in precedenza.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamata di un'API Web](scenario-spa-call-api.md)
