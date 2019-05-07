---
title: L'accesso Single sign-on (Microsoft Authentication Library per JavaScript) | Azure
description: Informazioni sulla creazione di esperienze single sign-on usando Microsoft Authentication Library per JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0fb0731b7ac46210294e3766b33bd6a239dcc2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075851"
---
# <a name="single-sign-on-with-msaljs"></a>Single sign-on con msal. js

Single Sign-On (SSO) consente agli utenti di immettere le credenziali una sola volta per accedere e stabilire una sessione che può essere riutilizzata in più applicazioni senza la necessità di ripetere l'autenticazione. Ciò garantisce un'esperienza all'utente e riduce le ripetute richieste di credenziali.

Azure AD offre le funzionalità SSO per applicazioni impostando un cookie di sessione quando l'utente autentica la prima volta. La libreria msal. js consente alle applicazioni di sfruttare questa in diversi modi.

## <a name="sso-between-browser-tabs"></a>SSO tra le schede del browser

Quando l'applicazione è aperta in più schede e prima di tutto l'accesso dell'utente in una scheda, l'utente viene anche effettuato nelle altre schede senza che venga richiesto. Msal. js vengono memorizzati nella cache il token ID per l'utente nel browser `localStorage` e si connetterà l'utente all'applicazione nelle altre schede aperte.

Per impostazione predefinita, Usa msal. js `sessionStorage` che non consente la sessione devono essere condivisi tra le schede. Per ottenere l'accesso SSO tra le schede, assicurarsi di impostare il `cacheLocation` di msal. js per `localStorage` come illustrato di seguito.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO tra App

Quando un utente esegue l'autenticazione, un cookie di sessione viene impostato sul dominio di Azure AD nel browser. Msal. js si basa su questo cookie di sessione per fornire l'accesso SSO per l'utente tra diverse applicazioni. Msal. js inoltre memorizza nella cache il token ID e token di accesso dell'utente nell'archivio del browser per ogni dominio dell'applicazione. Di conseguenza, il comportamento SSO varia per diversi casi:  

### <a name="applications-on-the-same-domain"></a>Applicazioni nello stesso dominio

Quando le applicazioni sono ospitate nello stesso dominio, l'utente potrà accedere una sola volta a un'app e quindi ottenere autenticato alle altre App senza un prompt dei comandi. Msal. js Usa i token memorizzati nella cache per l'utente nel dominio fornire l'accesso SSO.

### <a name="applications-on-different-domain"></a>Applicazioni in un dominio diverso

Quando le applicazioni sono ospitate in domini diversi, i token memorizzati nella cache in un dominio non sono accessibile da msal. js nel dominio B.

Ciò significa che quando gli utenti connessi a dominio passare a un'applicazione nel dominio B, si verrà reindirizzati o richiesto con la pagina di Azure AD. Poiché Azure AD ha ancora il cookie di sessione utente, eseguiranno l'accesso utente e non dovranno immettere nuovamente le credenziali. Se l'utente ha più account utente nella sessione con Azure AD, richiederà all'utente di selezionare l'account pertinente per l'accesso.

### <a name="automatically-select-account-on-azure-ad"></a>Selezionare automaticamente l'account in Azure AD

In alcuni casi, l'applicazione abbia accesso al contesto di autenticazione dell'utente e vuole evitare la richiesta di selezione account di Azure AD quando più account hanno eseguito l'accesso.  Questa operazione può essere eseguita alcuni modi diversi:

**Usando un ID di sessione (SID)**

ID sessione è un [attestazione facoltativa](active-directory-optional-claims.md) che può essere configurato nei token ID. Questa attestazione consente all'applicazione per l'identificazione Azure dell'utente AD sessione indipendentemente dal nome dell'account dell'utente o un nome utente. È possibile passare i parametri della richiesta per il SID di `acquireTokenSilent` chiamare. Ciò consente ad Azure AD ignorare la selezione account. SID associato al cookie di sessione e non supera i contesti del browser.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID può essere utilizzato solo con le richieste di autenticazione invisibile all'utente effettuate da `acquireTokenSilent` chiamare in msal. js.
È possibile trovare la procedura per configurare le attestazioni facoltative nel manifesto dell'applicazione [qui](active-directory-optional-claims.md).

**Utilizzo di Hint di account di accesso**

Se non è SID attestazione configurato o è necessario ignorare la richiesta di selezione account nelle chiamate di autenticazione interattiva, è possibile farlo, fornendo una `login_hint` nei parametri di richiesta e, facoltativamente, una `domain_hint` come `extraQueryParameters` in di msal. js metodi interattivi (`loginPopup`, `loginRedirect`, `acquireTokenPopup` e `acquireTokenRedirect`). Ad esempio: 

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

È possibile ottenere i valori per login_hint e domain_hint leggendo le attestazioni restituite nel token ID per l'utente.

* **loginHint** deve essere impostato sul `preferred_username` attestazione nel token ID.

* **domain_hint** è richiesto solo per essere passato quando si usa l'endpoint /common autorità. L'hint di dominio è determinato dal tenant ID(tid).  Se il `tid` attestazione nel token ID è `9188040d-6c67-4c5b-b112-36a304b66dad` è consumer. È in caso contrario, le organizzazioni.

Lettura [qui](v2-oauth2-implicit-grant-flow.md) per altre informazioni sui valori per l'hint di account di accesso e hint di dominio.

> [!Note]
> È possibile passare SID e login_hint nello stesso momento. Questo comporta in risposta a un errore.

## <a name="sso-without-msaljs-login"></a>Accesso SSO senza account di accesso di msal. js

Per impostazione predefinita, msal. js richiede che viene chiamato un metodo di accesso per stabilire un contesto utente prima di ottenere i token per le API. Poiché i metodi di accesso sono interattivi, l'utente visualizza un prompt dei comandi.

Esistono casi in cui le applicazioni hanno accesso al contesto dell'utente autenticato o token ID tramite l'autenticazione ha avviato in un'altra applicazione e si desidera utilizzare SSO per acquisire i token senza prima effettuare l'accesso tramite msal. js.

Un esempio è: Un utente è connesso a un'applicazione web padre che ospita un'altra applicazione di JavaScript in esecuzione come un componente aggiuntivo o plug-in.

L'esperienza SSO in questo scenario può essere ottenuta nel modo seguente:

Passare il `sid` se disponibile (o `login_hint` e facoltativamente `domain_hint`) come parametri per il msal. js della richiesta `acquireTokenSilent` chiamare come indicato di seguito:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Accesso SSO di adal. js a update msal. js

Msal. js offre parità delle funzionalità con adal. js per scenari di autenticazione di Azure AD. Per facilitare la migrazione da adal. js per msal. js per evitare di richiedere agli utenti di accedere di nuovo, la libreria legge il token ID che rappresenta la sessione utente nella cache adal. js e perfettamente accede l'utente di msal. js.  

Per sfruttare i vantaggi del comportamento di single sign-on (SSO) durante l'aggiornamento da adal. js, è necessario assicurarsi che usano le librerie `localStorage` per la memorizzazione nella cache i token. Impostare il `cacheLocation` a `localStorage` nella configurazione di msal. js sia adal. js in fase di inizializzazione come segue:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Dopo la configurazione, msal. js sarà in grado di leggere lo stato memorizzato nella cache dell'utente autenticato in adal. js e usarla per fornire l'accesso SSO di msal. js.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [l'accesso single sign-on di sessione e durata dei token](active-directory-configurable-token-lifetimes.md) valori in Azure AD.
