---
title: Inizializzare le applicazioni client (Microsoft Authentication Library per JavaScript) | Azure
description: Informazioni sull'inizializzazione di applicazioni client mediante Microsoft Authentication Library per JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd26f36356affbc8c272bd093757a8482773baf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544017"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inizializzare le applicazioni client usando msal. js
Questo articolo descrive l'inizializzazione di Microsoft Authentication Library per JavaScript (msal) con un'istanza di un'applicazione agente utente. L'applicazione di agente utente è un tipo di applicazione client pubblica in cui viene eseguito il codice client in un agente utente, ad esempio un web browser. Questi client non archiviano i segreti, poiché il contesto del browser è accessibile pubblicamente. Per altre informazioni sui tipi di applicazioni client e le opzioni di configurazione dell'applicazione, vedere la [Panoramica](msal-client-applications.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di inizializzare un'applicazione, è necessario innanzitutto [registrarlo con il portale di Azure](scenario-spa-app-registration.md) in modo che l'app può essere integrato con la piattaforma delle identità Microsoft. Dopo la registrazione, potrebbe essere necessario le informazioni seguenti (che sono reperibile nel portale di Azure):

- L'ID client (una stringa che rappresenta un GUID per l'applicazione)
- URL di provider di identità (l'istanza denominata) e i destinatari di accesso per l'applicazione. Questi due parametri sono noti collettivamente come autorità.
- L'ID tenant se si sta scrivendo un'applicazione line-of-business esclusivamente per l'organizzazione (anche denominati applicazione a tenant singolo).
- Per le app web, è necessario impostare anche l'URI di reindirizzamento in cui il provider di identità restituirà all'applicazione con i token di sicurezza.

## <a name="initializing-applications"></a>Inizializzazione di applicazioni

È possibile usare msal. js come indicato di seguito in un'applicazione JavaScript/Typescript normale. Inizializzare il contesto di autenticazione MSAL creando `UserAgentApplication` con un oggetto di configurazione. La configurazione minima necessaria per inizializzare msal. js è l'ID client dell'applicazione che è possibile ottenerlo dal portale di registrazione dell'applicazione.

Per i metodi di autenticazione con reindirizzare i flussi (`loginRedirect` e `acquireTokenRedirect`), sarà necessario registrare in modo esplicito un callback di esito positivo o errore tramite `handleRedirectCallback()` (metodo). Ciò è necessario poiché i flussi di reindirizzamento non restituiscono promesse così come i metodi con un'esperienza di popup.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

Msal. js è progettato per avere una singola istanza e la configurazione del `UserAgentApplication` per rappresentare un contesto di autenticazione singola. Più istanze non sono consigliate perché comportano le voci della cache in conflitto e il comportamento nel browser.

## <a name="configuration-options"></a>Opzioni di configurazione

Msal. js prevede una configurazione oggetto illustrato di seguito che fornisce un raggruppamento di opzioni configurabili disponibili per la creazione di un'istanza di `UserAgentApplication`.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Di seguito è il set complessivo di opzioni configurabili che sono attualmente supportati nell'oggetto di configurazione:

- **clientID**: Richiesto. L'ID client dell'applicazione, è deve ottenere nel portale di registrazione dell'applicazione.

- **authority**: facoltativo. Che indica una directory che MSAL può richiedere token da un URL. Il valore predefinito è: `https://login.microsoftonline.com/common`.
    * In Azure AD ha il formato https://&lt;istanza&gt;/&lt;audience&gt;, dove &lt;istanza&gt; è il dominio del provider di identità (ad esempio, `https://login.microsoftonline.com`) e &lt;audience&gt; è un identificatore che rappresenta i destinatari di accesso. Può trattarsi di valori seguenti:
        * `https://login.microsoftonline.com/<tenant>`-tenant è un dominio associato al tenant, ad esempio contoso.onmicrosoft.com, o il GUID che rappresenta il `TenantID` proprietà della directory utilizzata solo per consentire agli utenti di un'organizzazione specifica.
        * `https://login.microsoftonline.com/common`-Viene usato per consentire agli utenti con e dell'istituto di istruzione oppure un account persona Microsoft.
        * `https://login.microsoftonline.com/organizations/`-Viene usato per consentire agli utenti con account aziendali e dell'istituto di istruzione.
        * `https://login.microsoftonline.com/consumers/` -Viene usato per consentire agli utenti con account Microsoft personale solo (interattiva).
    * In Azure AD B2C, è nel formato `https://<instance>/tfp/<tenant>/<policyName>/`, dove istanza è il dominio di Azure AD B2C, il tenant è il nome del tenant di Azure AD B2C, policyName corrisponde a quello di applicare i criteri B2C.


- **validateAuthority**: facoltativo.  Convalidare l'autorità emittente di token. Il valore predefinito è `true`. Per le applicazioni B2C, poiché il valore dell'autorità è noto e può essere diverso per ogni criterio, la convalida dell'autorità non funzionerà e deve essere impostato su `false`.

- **redirectUri**: facoltativo.  URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. Il valore predefinito è `window.location.href`.

- **postLogoutRedirectUri**: facoltativo.  Reindirizza l'utente al `postLogoutRedirectUri` dopo la disconnessione. Il valore predefinito è `redirectUri`.

- **navigateToLoginRequestUrl**: facoltativo. Possibilità di disattivare la navigazione predefinito alla pagina iniziale dopo l'accesso. Il valore predefinito è true. Viene utilizzato solo per i flussi di reindirizzamento.

- **cacheLocation**: facoltativo.  Imposta l'archiviazione del browser a uno `localStorage` o `sessionStorage`. Il valore predefinito è `sessionStorage`.

- **storeAuthStateInCookie**: facoltativo.  Questo flag è stato introdotto in v0.2.2 msal. js come una correzione per il [problemi di autenticazione ciclo](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) in Microsoft Internet Explorer e Microsoft Edge. Abilitare il flag `storeAuthStateInCookie` a true per usufruire di questa correzione. Quando questa opzione è abilitata, msal. js archivia lo stato di richiesta di autenticazione necessario per la convalida dei flussi di autenticazione nel cookie del browser. Per impostazione predefinita, questo flag è impostato su `false`.

- **logger**: facoltativo.  Un oggetto Logger con un'istanza di callback che può essere fornito dallo sviluppatore per utilizzare e pubblicare i log in modo personalizzato. Per altre informazioni sul passaggio oggetto logger, vedere [la registrazione con msal. js](msal-logging.md).

- **loadFrameTimeout**: facoltativo.  Il numero di millisecondi di inattività prima che una risposta di rinnovo del token da Azure AD deve essere considerata il timeout. Valore predefinito è 6 secondi.

- **tokenRenewalOffsetSeconds**: facoltativo. Il numero di millisecondi che imposta la finestra di offset doveva rinnovare il token prima della scadenza. Valore predefinito è 300 millisecondi.

Queste sono applicabili solo a essere trasmessi dalla libreria MSAL Angular wrapper:
- **unprotectedResources**: facoltativo.  Matrice di URI di risorse non protette. MSAL non collegherà un token per le richieste in uscita che hanno tali URI. Il valore predefinito è `null`.

- **protectedResourceMap**: facoltativo.  Questo è il mapping delle risorse per gli ambiti utilizzati da MSAL per allegare automaticamente i token di accesso nelle chiamate API web. Viene ottenuto un token di accesso singolo per la risorsa. Pertanto è possibile mappare un percorso della risorsa specifica, come indicato di seguito: {"https://graph.microsoft.com/v1.0/me", ["User. Read"]}, o l'URL dell'app della risorsa come: {"https://graph.microsoft.com/", ["User. Read", "mail.send"]}. Ciò è necessario per le chiamate CORS. Il valore predefinito è `null`.
