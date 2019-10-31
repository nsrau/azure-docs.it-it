---
title: Single Sign-on (Microsoft Authentication Library per JavaScript)
titleSuffix: Microsoft identity platform
description: Informazioni sulla creazione di Single Sign-On esperienze usando Microsoft Authentication Library per JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
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
ms.openlocfilehash: da39b8435acdd11108a945c6bac5147dc8b6ad50
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150583"
---
# <a name="single-sign-on-with-msaljs"></a>Single Sign-On con MSAL.js

Single Sign-on (SSO) consente agli utenti di immettere le credenziali una sola volta per accedere e stabilire una sessione che può essere riutilizzata tra più applicazioni senza che sia necessario eseguire di nuovo l'autenticazione. In questo modo si offre un'esperienza uniforme all'utente e si riducono le richieste ripetute per le credenziali.

Azure AD fornisce funzionalità SSO alle applicazioni impostando un cookie di sessione quando l'utente esegue l'autenticazione per la prima volta. La libreria MSAL. js consente alle applicazioni di sfruttare questo in diversi modi.

## <a name="sso-between-browser-tabs"></a>SSO tra le schede del browser

Quando l'applicazione è aperta in più schede e l'utente accede per la prima volta a una scheda, l'utente è anche connesso alle altre schede senza che venga richiesto. MSAL. js memorizza nella cache il token ID per l'utente nel browser `localStorage` e consente di accedere all'applicazione nelle altre schede aperte.

Per impostazione predefinita, MSAL. js USA `sessionStorage` che non consente la condivisione della sessione tra le schede. Per ottenere l'accesso SSO tra le schede, assicurarsi di impostare il `cacheLocation` in MSAL. js su `localStorage`, come illustrato di seguito.

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

Quando un utente esegue l'autenticazione, un cookie di sessione viene impostato sul dominio Azure AD nel browser. MSAL. js si basa su questo cookie di sessione per fornire l'accesso SSO per l'utente tra applicazioni diverse. MSAL. js memorizza anche nella cache i token ID e i token di accesso dell'utente nello spazio di archiviazione del browser per ogni dominio dell'applicazione. Di conseguenza, il comportamento SSO varia in base ai diversi casi:  

### <a name="applications-on-the-same-domain"></a>Applicazioni nello stesso dominio

Quando le applicazioni sono ospitate nello stesso dominio, l'utente può accedere a un'app una sola volta e quindi essere autenticato per le altre app senza una richiesta. MSAL. js utilizza i token memorizzati nella cache per l'utente nel dominio per fornire SSO.

### <a name="applications-on-different-domain"></a>Applicazioni in un dominio diverso

Quando le applicazioni sono ospitate in domini diversi, non è possibile accedere ai token memorizzati nella cache nel dominio A da MSAL. js nel dominio B.

Ciò significa che quando gli utenti connessi al dominio a passano a un'applicazione nel dominio B, verranno reindirizzati o richiesti con la pagina Azure AD. Poiché Azure AD ha ancora il cookie della sessione utente, effettuerà l'accesso dell'utente e non sarà necessario immettere di nuovo le credenziali. Se l'utente dispone di più account utente in sessione con Azure AD, all'utente verrà richiesto di selezionare l'account pertinente per l'accesso.

### <a name="automatically-select-account-on-azure-ad"></a>Seleziona automaticamente l'account Azure AD

In alcuni casi, l'applicazione ha accesso al contesto di autenticazione dell'utente e desidera evitare il Azure AD richiesta di selezione dell'account quando più account sono connessi.  Questa operazione può essere eseguita in diversi modi:

**Utilizzo dell'ID sessione (SID)**

ID sessione è un' [attestazione facoltativa](active-directory-optional-claims.md) che può essere configurata nei token ID. Questa attestazione consente all'applicazione di identificare la sessione di Azure AD dell'utente indipendentemente dal nome dell'account utente o dal nome utente. È possibile passare il SID nei parametri della richiesta alla chiamata `acquireTokenSilent`. Ciò consentirà Azure AD di ignorare la selezione dell'account. Il SID è associato al cookie di sessione e non verrà incrociato tra i contesti del browser.

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
> SID può essere usato solo con le richieste di autenticazione invisibile all'utente effettuate da `acquireTokenSilent` chiamata in MSAL. js.
[Qui](active-directory-optional-claims.md)è possibile trovare i passaggi per configurare attestazioni facoltative nel manifesto dell'applicazione.

**Utilizzo dell'hint di accesso**

Se non si dispone di un'attestazione SID configurata o se è necessario ignorare la richiesta di selezione dell'account nelle chiamate di autenticazione interattiva, è possibile eseguire questa operazione fornendo un `login_hint` nei parametri della richiesta e, facoltativamente, una `domain_hint` come `extraQueryParameters` nei metodi interattivi MSAL. js (@no __t_3_, `loginRedirect`, `acquireTokenPopup` e `acquireTokenRedirect`). ad esempio:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

È possibile ottenere i valori per login_hint e domain_hint leggendo le attestazioni restituite nel token ID per l'utente.

* **loginHint** deve essere impostato sull'attestazione `preferred_username` nel token ID.

* è necessario passare **domain_hint** solo quando si usa l'autorità/Common. L'hint di dominio è determinato dall'ID tenant (TID).  Se il `tid` attestazione nel token ID è `9188040d-6c67-4c5b-b112-36a304b66dad` è consumer. In caso contrario, si tratta di organizzazioni.

Leggere [qui](v2-oauth2-implicit-grant-flow.md) per altre informazioni sui valori per l'hint di accesso e l'hint di dominio.

> [!Note]
> Non è possibile passare contemporaneamente a SID e a login_hint. Si otterrà quindi una risposta di errore.

## <a name="sso-without-msaljs-login"></a>SSO senza accesso a MSAL. js

Per impostazione predefinita, MSAL. js richiede che venga chiamato un metodo di accesso per stabilire un contesto utente prima di ottenere i token per le API. Poiché i metodi di accesso sono interattivi, l'utente visualizza un messaggio di richiesta.

Ci sono alcuni casi in cui le applicazioni hanno accesso al contesto o al token ID dell'utente autenticato tramite l'autenticazione avviata in un'altra applicazione e vogliono sfruttare SSO per acquisire i token senza prima accedere tramite MSAL. js.

Un esempio è: un utente ha eseguito l'accesso a un'applicazione Web padre che ospita un'altra applicazione JavaScript che viene eseguita come componente aggiuntivo o plug-in.

L'esperienza SSO in questo scenario può essere eseguita come indicato di seguito:

Passare il `sid` se disponibile (o `login_hint` ed eventualmente `domain_hint`) come parametri della richiesta alla chiamata a MSAL. js `acquireTokenSilent` come indicato di seguito:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO in ADAL. js per l'aggiornamento di MSAL. js

MSAL. js offre la parità di funzionalità con ADAL. js per gli scenari di autenticazione Azure AD. Per semplificare la migrazione da ADAL. js a MSAL. js e per evitare che gli utenti eseguano di nuovo l'accesso, la libreria legge il token ID che rappresenta la sessione dell'utente nella cache ADAL. js e firma facilmente l'utente in MSAL. js.  

Per sfruttare i vantaggi del comportamento del Single Sign-On (SSO) quando si esegue l'aggiornamento da ADAL. js, è necessario assicurarsi che le librerie utilizzino `localStorage` per la memorizzazione nella cache dei token. Impostare il `cacheLocation` su `localStorage` nella configurazione MSAL. js e ADAL. js in fase di inizializzazione, come indicato di seguito:


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

Una volta configurata la configurazione, MSAL. js potrà leggere lo stato memorizzato nella cache dell'utente autenticato in ADAL. js e usarlo per fornire l'accesso SSO in MSAL. js.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui valori di [durata Single Sign-on sessione e token,](active-directory-configurable-token-lifetimes.md) vedere Azure ad.
