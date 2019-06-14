---
title: Applicazione a singola pagina (acquisire un token per chiamare un'API) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione a singola pagina (Acquisisci un token per chiamare un'API)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138806"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Applicazione a singola pagina - acquisire un token per chiamare un'API

Il modello per l'acquisizione dei token per le API con msal. js sono tentare prima di tutto una richiesta di token invisibile all'utente tramite la `acquireTokenSilent` (metodo). Quando questo metodo viene chiamato, la libreria controlla prima di tutto la cache nell'archivio del browser per vedere se è presente un token valido e lo restituisce. Quando è presente alcun token valido nella cache, invia una richiesta di token invisibile all'utente di Azure Active Directory (Azure AD) da un iframe nascosto. Questo metodo consente inoltre la libreria da rinnovare i token. Per altre informazioni sulla sessione single sign-on e i valori di durata dei token di Azure AD, vedere [durate dei token](active-directory-configurable-token-lifetimes.md).

Le richieste di token invisibile all'utente in Azure AD potrebbero non riuscire per alcuni dei motivi, ad esempio una sessione AD di Azure scaduta o una modifica della password. In tal caso, è possibile richiamare uno dei metodi interattivi (che verranno richiesto all'utente) per acquisire i token.

* [Acquisire i token con una finestra popup](#acquire-token-with-a-pop-up-window) usando `acquireTokenPopup`
* [Acquisire i token con reindirizzamento](#acquire-token-with-redirect) usando `acquireTokenRedirect`

**Scelta tra un'esperienza di popup o di reindirizzamento**

 È possibile usare una combinazione di metodi di reindirizzamento sia il menu a comparsa nell'applicazione. La scelta tra un'esperienza di menu a comparsa o reindirizzamento dipende il flusso dell'applicazione.

* Se non si desidera all'utente di uscire dalla pagina principale dell'applicazione durante l'autenticazione, è consigliabile utilizzare i metodi a comparsa. Poiché il reindirizzamento di autenticazione si verifica in una finestra popup, viene mantenuto lo stato dell'applicazione principale.

* Esistono casi in cui potrebbe essere necessario usare i metodi di reindirizzamento. Se gli utenti dell'applicazione dispongono di vincoli del browser o i criteri in cui le finestre popup sono disabilitate, è possibile usare i metodi di reindirizzamento. È anche consigliabile usare i metodi di reindirizzamento con browser Internet Explorer perché non vi sono determinati [problemi noti di Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) quando si gestiscono le finestre popup.

È possibile impostare gli ambiti API che si vuole che il token di accesso da includere quando si compila la richiesta di token di accesso. Si noti che tutti gli ambiti richiesti non può essere concesso nel token di accesso e dipende il consenso dell'utente.

## <a name="acquire-token-with-a-pop-up-window"></a>Acquisire i token con una finestra popup

### <a name="javascript"></a>JavaScript

Il modello precedente utilizzando i metodi per un'esperienza di popup:

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

Il wrapper MSAL Angular fornisce la possibilità di aggiungere l'intercettore HTTP `MsalInterceptor` che automaticamente acquisisce automaticamente i token di accesso e collegarli alle richieste HTTP alle API.

È possibile specificare gli ambiti per le API di `protectedResourceMap` opzione di configurazione che richiederà il MsalInterceptor quando automaticamente l'acquisizione dei token.

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

Per esito positivo o negativo dell'acquisizione di token invisibile all'utente, MSAL Angular fornisce i callback che è possibile sottoscrivere. È anche importante da ricordare annullare la sottoscrizione.

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

In alternativa, è possibile acquisire in modo esplicito i token usando i metodi di acquisizione token, come descritto nella libreria msal. js di base.

## <a name="acquire-token-with-redirect"></a>Acquisire i token con reindirizzamento

### <a name="javascript"></a>JavaScript

Il modello è come descritto in precedenza ma illustrato un metodo di reindirizzamento per acquisire i token in modo interattivo. Si noti che è necessario registrare il callback di reindirizzamento, come indicato in precedenza.

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

### <a name="angular"></a>Angular

Ciò è identico a quello descritto in precedenza.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamare un'API Web](scenario-spa-call-api.md)
