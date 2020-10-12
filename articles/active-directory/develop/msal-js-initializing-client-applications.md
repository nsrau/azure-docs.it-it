---
title: Inizializzare MSAL.js app client | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sull'inizializzazione di applicazioni client tramite Microsoft Authentication Library per JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5af5d3a88262792f4b32e2ce3d8143ac680f083a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87027037"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inizializzare applicazioni client usando MSAL.js

Questo articolo descrive l'inizializzazione di Microsoft Authentication Library per JavaScript (MSAL.js) con un'istanza di un'applicazione agente utente.

L'applicazione agente utente è una forma di applicazione client pubblica in cui il codice client viene eseguito in un agente utente, ad esempio un Web browser. Questi client non archiviano i segreti perché il contesto del browser è accessibile in modo aperto.

Per altre informazioni sui tipi di applicazioni client e sulle opzioni di configurazione dell'applicazione, vedere [app client pubbliche e riservate in MSAL](msal-client-applications.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di inizializzare un'applicazione, è innanzitutto necessario [registrarla con il portale di Azure](scenario-spa-app-registration.md), stabilendo una relazione di trust tra l'applicazione e la piattaforma di identità Microsoft.

Al termine della registrazione dell'app, sono necessari alcuni o tutti i valori seguenti che è possibile trovare nella portale di Azure.

| Valore | Obbligatoria | Descrizione |
|:----- | :------: | :---------- |
| ID applicazione (client) | Obbligatoria | GUID che identifica in modo univoco l'applicazione all'interno della piattaforma di identità Microsoft. |
| Authority | Facoltativo | URL del provider di identità (l' *istanza*) e *destinatari dell'accesso* per l'applicazione. L'istanza e i destinatari di accesso, se concatenati, costituiscono l' *autorità*. |
| ID directory (tenant) | Facoltativo | Specificare questa impostazione se si sta creando un'applicazione line-of-business esclusivamente per la propria organizzazione, spesso definita *applicazione a tenant singolo*. |
| URI di reindirizzamento | Facoltativo | Se si sta compilando un'app Web, `redirectUri` specifica la posizione in cui il provider di identità (la piattaforma di identità Microsoft) deve restituire i token di sicurezza emessi. |

## <a name="initialize-msaljs-2x-apps"></a>Inizializzare app MSAL.js 2. x

Inizializzare il contesto di autenticazione MSAL creando un'istanza di [PublicClientApplication][msal-js-publicclientapplication] con un oggetto di [configurazione][msal-js-configuration] . La proprietà di configurazione minima richiesta è la `clientID` dell'applicazione, mostrata come **ID applicazione (client)** nella pagina **Panoramica** della registrazione dell'app nel portale di Azure.

Ecco un esempio di oggetto di configurazione e creazione di un'istanza di `PublicClientApplication` :

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

Richiamare [handleRedirectPromise][msal-js-handleredirectpromise] quando l'applicazione usa i flussi di reindirizzamento. Quando si usano i flussi di reindirizzamento, `handleRedirectPromise` deve essere eseguito a ogni caricamento della pagina.

Ci sono tre possibili risultati dalla promessa:

- `.then` viene richiamato ed `tokenResponse` è veritiero: l'applicazione sta restituendo da un'operazione di reindirizzamento che ha avuto esito positivo.
- `.then` viene richiamato e `tokenResponse` è false ( `null` ): l'applicazione non restituisce da un'operazione di reindirizzamento.
- `.catch` viene richiamato: l'applicazione sta restituendo da un'operazione di reindirizzamento e si è verificato un errore.

## <a name="initialize-msaljs-1x-apps"></a>Inizializzare app MSAL.js 1. x

Inizializzare il contesto di autenticazione MSAL 1. x creando un'istanza di [UserAgentApplication][msal-js-useragentapplication] con un oggetto di configurazione. La proprietà di configurazione minima richiesta è la `clientID` dell'applicazione, mostrata come **ID applicazione (client)** nella pagina **Panoramica** della registrazione dell'app nel portale di Azure.

Per i metodi di autenticazione con flussi di reindirizzamento ([loginRedirect][msal-js-loginredirect] e [acquireTokenRedirect][msal-js-acquiretokenredirect]) in MSAL.js 1.2. x o versioni precedenti, è necessario registrare in modo esplicito un callback per l'esito positivo o negativo tramite il `handleRedirectCallback()` metodo. La registrazione esplicita del callback è necessaria in MSAL.js 1.2. x e versioni precedenti perché i flussi di reindirizzamento non restituiscono promesse come i metodi con un'esperienza popup. La registrazione del callback è *facoltativa* in MSAL.js versione 1.3. x e versioni successive.

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>Singola istanza e configurazione

Entrambi MSAL.js 1. x e 2. x sono progettati per avere una sola istanza e la configurazione del `UserAgentApplication` o `PublicClientApplication` , rispettivamente, per rappresentare un singolo contesto di autenticazione.

Più istanze di `UserAgentApplication` o `PublicClientApplication` non sono consigliate perché provocano comportamenti e voci della cache in conflitto nel browser.

## <a name="next-steps"></a>Passaggi successivi

Questo esempio di codice MSAL.js 2. x in GitHub illustra la creazione di un'istanza di un [PublicClientApplication][msal-js-publicclientapplication] con un oggetto di [configurazione][msal-js-configuration] :

[Azure-Samples/MS-Identity-JavaScript-V2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_configuration_.html
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
