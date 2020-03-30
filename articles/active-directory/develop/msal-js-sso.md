---
title: Single Sign-On (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulla creazione di esperienze Single Sign-On con Microsoft Authentication Library per JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262854"
---
# <a name="single-sign-on-with-msaljs"></a>Single Sign-On con MSAL.js

Single Sign-On (SSO) consente agli utenti di immettere le proprie credenziali una volta per accedere e stabilire una sessione che può essere riutilizzata in più applicazioni senza dover eseguire nuovamente l'autenticazione. Ciò offre un'esperienza senza problemi all'utente e riduce le richieste ripetute di credenziali.

Azure AD offre funzionalità SSO alle applicazioni impostando un cookie di sessione quando l'utente esegue l'autenticazione per la prima volta. La libreria MSAL.js consente alle applicazioni di sfruttare questo in alcuni modi.

## <a name="sso-between-browser-tabs"></a>SSO tra le schede del browser

Quando l'applicazione è aperta in più schede e si accede per la prima volta all'utente in una scheda, l'utente ha eseguito l'accesso anche nelle altre schede senza che venga richiesto. MSAL.js memorizza nella cache il token `localStorage` ID per l'utente nel browser e l'utente accederà all'applicazione nelle altre schede aperte.

Per impostazione predefinita, `sessionStorage` MSAL.js utilizza che non consente la condivisione della sessione tra le schede. Per ottenere SSO tra le schede, assicurarsi di impostare il `cacheLocation` in MSAL.js come `localStorage` illustrato di seguito.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO tra le app

Quando un utente esegue l'autenticazione, viene impostato un cookie di sessione nel dominio di Azure AD nel browser. MSAL.js si basa su questo cookie di sessione per fornire SSO per l'utente tra applicazioni diverse. MSAL.js memorizza inoltre nella cache i token ID e i token di accesso dell'utente nell'archivio del browser per ogni dominio applicazione. Di conseguenza, il comportamento SSO varia in base ai diversi casi:  

### <a name="applications-on-the-same-domain"></a>Applicazioni nello stesso dominio

Quando le applicazioni sono ospitate nello stesso dominio, l'utente può accedere a un'app una sola volta e quindi ottenere l'autenticazione alle altre applicazioni senza una richiesta. MSAL.js sfrutta i token memorizzati nella cache per l'utente nel dominio per fornire SSO.

### <a name="applications-on-different-domain"></a>Applicazioni in un dominio diverso

Quando le applicazioni sono ospitate in domini diversi, i token memorizzati nella cache del dominio A non sono accessibili da MSAL.js nel dominio B.

Ciò significa che quando gli utenti hanno eseguito l'accesso al dominio A passano a un'applicazione nel dominio B, verranno reindirizzati o richiesti con la pagina di Azure AD. Poiché Azure AD ha ancora il cookie di sessione utente, verrà eseguito l'accesso all'utente e non dovrà immettere nuovamente le credenziali. Se l'utente dispone di più account utente in sessione con Azure AD, all'utente verrà richiesto di selezionare l'account pertinente con cui accedere.

### <a name="automatically-select-account-on-azure-ad"></a>Selezionare automaticamente l'account in Azure ADAutomatically select account on Azure AD

In alcuni casi, l'applicazione ha accesso al contesto di autenticazione dell'utente e vuole evitare la richiesta di selezione dell'account Azure AD quando più account sono connessi.  Questo può essere fatto in diversi modi:

**Utilizzo dell'ID sessione (SID)**

L'ID sessione è [un'attestazione facoltativa](active-directory-optional-claims.md) che può essere configurata nei token ID. Questa attestazione consente all'applicazione di identificare la sessione di Azure AD dell'utente indipendentemente dal nome account o dal nome utente dell'utente. È possibile passare il SID nei `acquireTokenSilent` parametri della richiesta alla chiamata. Ciò consentirà ad Azure AD di ignorare la selezione dell'account. SID è associato al cookie di sessione e non attraverserà i contesti del browser.

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
> SID può essere utilizzato solo con `acquireTokenSilent` richieste di autenticazione invisibile all'utente effettuate tramite chiamata in MSAL.js.
I passaggi per configurare attestazioni facoltative nel manifesto dell'applicazione sono disponibili [qui](active-directory-optional-claims.md).

**Utilizzo del suggerimento per l'accesso**

Se non è configurata l'attestazione SID o è necessario ignorare la richiesta `login_hint` di selezione dell'account `domain_hint` nelle `extraQueryParameters` chiamate di autenticazione interattive, è possibile farlo fornendo un nei parametri della richiesta e facoltativamente un`loginPopup`oggetto come nei metodi interattivi MSAL.js ( , `loginRedirect`e `acquireTokenPopup` `acquireTokenRedirect`). Ad esempio:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

È possibile ottenere i valori per login_hint e domain_hint leggendo le attestazioni restituite nel token ID per l'utente.

* **loginHint** deve essere `preferred_username` impostato sull'attestazione nel token ID.

* **domain_hint** deve essere passato solo quando si utilizza l'autorità /common. L'hint di dominio è determinato dall'ID tenant(tid).  Se `tid` l'attestazione nel `9188040d-6c67-4c5b-b112-36a304b66dad` token ID è è consumer. In caso contrario, si tratta di organizzazioni.

Leggi [qui](v2-oauth2-implicit-grant-flow.md) per ulteriori informazioni sui valori per l'hint di accesso e il suggerimento di dominio.

> [!Note]
> Non è possibile passare SID e login_hint contemporaneamente. Ciò comporterà una risposta di errore.

## <a name="sso-without-msaljs-login"></a>SSO senza account di accesso MSAL.js

Per impostazione specifica, MSAL.js richiede che venga chiamato un metodo di accesso per stabilire un contesto utente prima di ottenere i token per le API. Poiché i metodi di accesso sono interattivi, l'utente visualizza un prompt.

Esistono alcuni casi in cui le applicazioni hanno accesso al contesto o al token ID dell'utente autenticato tramite l'autenticazione avviata in un'altra applicazione e desiderano sfruttare SSO per acquisire token senza prima accedere tramite MSAL.js.

Un esempio è: un utente ha eseguito l'accesso a un'applicazione Web padre che ospita un'altra applicazione JavaScript in esecuzione come componente aggiuntivo o plug-in.

L'esperienza SSO in questo scenario può essere ottenuta come segue:

Passare `sid` if available `login_hint` (o `domain_hint`e facoltativamente) come parametri `acquireTokenSilent` di richiesta alla chiamata MSAL.js come segue:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO nell'aggiornamento di SSO in ADAL.js all'aggiornamento MSAL.js

MSAL.js porta la parità di funzionalità con ADAL.js per gli scenari di autenticazione di Azure AD. Per semplificare la migrazione da ADAL.js a MSAL.js ed evitare di chiedere agli utenti di accedere di nuovo, la libreria legge il token ID che rappresenta la sessione dell'utente nella cache di ADAL.js e accede senza problemi all'utente in MSAL.js.  

Per sfruttare il comportamento Single Sign-On (SSO) durante l'aggiornamento da ADAL.js, è necessario assicurarsi che le librerie utilizzino `localStorage` per la memorizzazione nella cache dei token. Impostare `cacheLocation` `localStorage` il su in entrambi i file di configurazione MSAL.js e ADAL.js al momento dell'inizializzazione come segue:


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
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Una volta configurato, MSAL.js sarà in grado di leggere lo stato memorizzato nella cache dell'utente autenticato in ADAL.js e utilizzarlo per fornire SSO in MSAL.js.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui valori della [sessione Single Sign-On e](active-directory-configurable-token-lifetimes.md) della durata del token in Azure AD.
