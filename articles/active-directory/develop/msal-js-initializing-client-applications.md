---
title: Inizializzare le applicazioni client (Microsoft Authentication Library per JavaScript)
titleSuffix: Microsoft identity platform
description: Informazioni sull'inizializzazione di applicazioni client tramite Microsoft Authentication Library per JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b24d9d79bf34325ec033b6ae6847579fa51769ac
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803092"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inizializzare le applicazioni client usando MSAL. js
Questo articolo descrive l'inizializzazione di Microsoft Authentication Library per JavaScript (MSAL. js) con un'istanza di un'applicazione agente utente. L'applicazione agente utente è una forma di applicazione client pubblica in cui il codice client viene eseguito in un agente utente, ad esempio un Web browser. Questi client non archiviano i segreti, perché il contesto del browser è accessibile apertamente. Per altre informazioni sui tipi di applicazioni client e sulle opzioni di configurazione dell'applicazione, vedere la [Panoramica](msal-client-applications.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di inizializzare un'applicazione, è prima necessario [registrarla con il portale di Azure](scenario-spa-app-registration.md) in modo che l'app possa essere integrata con la piattaforma di identità Microsoft. Dopo la registrazione, è possibile che siano necessarie le informazioni seguenti (disponibili nella portale di Azure):

- ID client (una stringa che rappresenta un GUID per l'applicazione)
- URL del provider di identità (denominato istanza) e destinatari dell'accesso per l'applicazione. Questi due parametri sono collettivamente noti come autorità.
- ID tenant se si sta scrivendo un'applicazione line-of-business esclusivamente per l'organizzazione (denominata anche applicazione a tenant singolo).
- Per le app Web, è necessario impostare anche il redirectUri in cui il provider di identità tornerà all'applicazione con i token di sicurezza.

## <a name="initializing-applications"></a>Inizializzazione di applicazioni

È possibile usare MSAL. js come indicato di seguito in un'applicazione JavaScript/typescript normale. Inizializzare il contesto di autenticazione MSAL creando un'istanza di `UserAgentApplication` con un oggetto di configurazione. Il file di configurazione minimo richiesto per inizializzare MSAL. js è l'ID client dell'applicazione che dovrebbe essere ottenuto dal portale di registrazione delle applicazioni.

Per i metodi di autenticazione con flussi di reindirizzamento (`loginRedirect` e `acquireTokenRedirect`), sarà necessario registrare in modo esplicito un callback per l'esito positivo o negativo tramite `handleRedirectCallback()` metodo. Questa operazione è necessaria perché i flussi di reindirizzamento non restituiscono le promesse come metodi con un'esperienza popup.

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

MSAL. js è progettato per avere una singola istanza e la configurazione del `UserAgentApplication` per rappresentare un singolo contesto di autenticazione. Non è consigliabile usare più istanze perché provocano il comportamento e le voci della cache in conflitto nel browser.

## <a name="configuration-options"></a>Opzioni di configurazione

MSAL. js dispone di un oggetto di configurazione illustrato di seguito che fornisce un raggruppamento di opzioni configurabili disponibili per la creazione di un'istanza di `UserAgentApplication`.

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

Di seguito è riportato il set totale di opzioni configurabili attualmente supportate nell'oggetto config:

- **ClientID**: obbligatorio. ClientID dell'applicazione, è necessario ottenerlo dal portale di registrazione delle applicazioni.

- **Authority**: facoltativa. URL che indica una directory da cui MSAL può richiedere token. Il valore predefinito è: `https://login.microsoftonline.com/common`.
    * In Azure AD, è nel formato https://&lt;istanza&gt;/&lt;audience&gt;, dove &lt;istanza&gt; è il dominio del provider di identità (ad esempio, `https://login.microsoftonline.com`) e &lt;audience&gt; è un identificatore che rappresenta i destinatari di accesso. I valori possibili sono i seguenti:
        * `https://login.microsoftonline.com/<tenant>`-tenant è un dominio associato al tenant, ad esempio contoso.onmicrosoft.com, o il GUID che rappresenta la proprietà `TenantID` della directory utilizzata solo per l'accesso agli utenti di un'organizzazione specifica.
        * `https://login.microsoftonline.com/common`: consente di accedere agli utenti con account aziendali o dell'Istituto di istruzione o con un account personale Microsoft.
        * `https://login.microsoftonline.com/organizations/`: consente di accedere agli utenti con account aziendali o dell'Istituto di istruzione.
        * `https://login.microsoftonline.com/consumers/`: consente di accedere agli utenti solo con account Microsoft personali (Live).
    * In Azure AD B2C, è nel formato `https://<instance>/tfp/<tenant>/<policyName>/`, dove istanza è il dominio Azure AD B2C, tenant è il nome del tenant Azure AD B2C, PolicyName è il nome del criterio B2C da applicare.


- **validateAuthority**: facoltativo.  Convalidare l'autorità emittente dei token. Il valore predefinito è `true`. Per le applicazioni B2C, poiché il valore dell'autorità è noto e può essere diverso per ogni criterio, la convalida dell'autorità non funzionerà e deve essere impostata su `false`.

- **RedirectUri**: facoltativo.  URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale. L'impostazione predefinita è `window.location.href`.

- **postLogoutRedirectUri**: facoltativo.  Reindirizza l'utente a `postLogoutRedirectUri` dopo la disconnessione. Il valore predefinito è `redirectUri`.

- **navigateToLoginRequestUrl**: facoltativo. Possibilità di disattivare la navigazione predefinita per avviare la pagina dopo l'accesso. Il valore predefinito è true. Viene usato solo per i flussi di reindirizzamento.

- **cacheLocation**: facoltativo.  Imposta lo spazio di archiviazione del browser su `localStorage` o `sessionStorage`. Il valore predefinito è `sessionStorage`.

- **storeAuthStateInCookie**: facoltativo.  Questo flag è stato introdotto in MSAL. js v 0.2.2 come correzione per i [problemi del ciclo di autenticazione](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) in Microsoft Internet Explorer e Microsoft Edge. Abilitare il flag `storeAuthStateInCookie` su true per sfruttare questa correzione. Quando questa funzionalità è abilitata, in MSAL. js verrà archiviato lo stato della richiesta di autenticazione necessario per la convalida dei flussi di autenticazione nei cookie del browser. Per impostazione predefinita, questo flag è impostato su `false`.

- **logger**: facoltativo.  Oggetto logger con un'istanza di callback che può essere fornita dallo sviluppatore per utilizzare e pubblicare i log in modo personalizzato. Per informazioni dettagliate sul passaggio di un oggetto logger, vedere [registrazione con MSAL. js](msal-logging.md).

- **loadFrameTimeout**: facoltativo.  Timeout del numero di millisecondi di inattività prima che venga presa in considerazione il timeout di una risposta di rinnovo del token da Azure AD. Il valore predefinito è 6 secondi.

- **tokenRenewalOffsetSeconds**: facoltativo. Numero di millisecondi che imposta la finestra di offset necessaria per rinnovare il token prima della scadenza. Il valore predefinito è 300 millisecondi.

Questi sono applicabili solo per essere passati dalla libreria di wrapper angolare MSAL:
- **unprotectedResources**: facoltativo.  Matrice di URI che sono risorse non protette. MSAL non collegherà un token alle richieste in uscita con questi URI. L'impostazione predefinita è `null`.

- **protectedResourceMap**: facoltativo.  Si tratta di un mapping delle risorse agli ambiti usati da MSAL per il collegamento automatico dei token di accesso nelle chiamate API Web. Per la risorsa viene ottenuto un singolo token di accesso. È quindi possibile eseguire il mapping di un percorso di risorsa specifico come segue: {"https://graph.microsoft.com/v1.0/me", ["User. Read"]} o l'URL dell'app della risorsa come: {"https://graph.microsoft.com/", ["User. Read", "mail. Send"]}. Questa operazione è necessaria per le chiamate CORS. L'impostazione predefinita è `null`.
