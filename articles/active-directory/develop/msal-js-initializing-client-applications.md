---
title: Inizializzazione delle app client MSAL.js Azure
titleSuffix: Microsoft identity platform
description: Informazioni sull'inizializzazione di applicazioni client tramite Microsoft Authentication Library per JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084034"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inizializzare le applicazioni client utilizzando MSAL.js
In questo articolo viene descritta l'inizializzazione di Microsoft Authentication Library per JavaScript (MSAL.js) con un'istanza di un'applicazione agente utente. L'applicazione agente utente è una forma di applicazione client pubblica in cui il codice client viene eseguito in un agente utente, ad esempio un Web browser. Questi client non archiviano segreti, poiché il contesto del browser è apertamente accessibile. Per ulteriori informazioni sui tipi di applicazione client e sulle opzioni di configurazione dell'applicazione, leggere la [panoramica](msal-client-applications.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di inizializzare un'applicazione, è necessario [registrarla con il portale](scenario-spa-app-registration.md) di Azure in modo che l'app possa essere integrata con la piattaforma di identità Microsoft.Before initializing an application, you first to register it with the Azure portal so that your app can be integrated with the Microsoft identity platform. Dopo la registrazione, potrebbero essere necessarie le informazioni seguenti (che possono essere trovate nel portale di Azure):After registration, you may need the following information (which can be found in the Azure portal):

- L'ID client (una stringa che rappresenta un GUID per l'applicazione)
- L'URL del provider di identità (denominato l'istanza) e il gruppo di destinatari di accesso per l'applicazione. Questi due parametri sono collettivamente noti come l'autorità.
- ID tenant se si scrive un'applicazione line-of-business esclusivamente per l'organizzazione (denominata anche applicazione single-tenant).
- Per le app Web, è necessario impostare anche il redirectUri in cui il provider di identità tornerà all'applicazione con i token di sicurezza.

## <a name="initializing-applications"></a>Inizializzazione delle applicazioni

È possibile utilizzare MSAL.js come segue in una semplice applicazione JavaScript/Typescript. Inizializzare il contesto di `UserAgentApplication` autenticazione MSAL eseguendo un'istanza con un oggetto di configurazione. La configurazione minima richiesta per inizializzare MSAL.js è il clientID dell'applicazione che si dovrebbe ottenere dal portale di registrazione dell'applicazione.

Per i metodi di`loginRedirect` `acquireTokenRedirect`autenticazione con flussi di reindirizzamento ( `handleRedirectCallback()` e ), è necessario registrare in modo esplicito un callback per l'esito positivo o un errore tramite il metodo . Ciò è necessario poiché i flussi di reindirizzamento non restituiscono promesse come i metodi con un'esperienza pop-up fanno.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
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

MSAL.js è progettato per avere una `UserAgentApplication` singola istanza e configurazione di per rappresentare un singolo contesto di autenticazione. Più istanze non sono consigliate in quanto causano il comportamento e le voci della cache in conflitto nel browser.

## <a name="configuration-options"></a>Opzioni di configurazione

MSAL.js dispone di un oggetto di configurazione illustrato di seguito che `UserAgentApplication`fornisce un raggruppamento di opzioni configurabili disponibili per la creazione di un'istanza di .

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
    navigateFrameWait?: number;
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

- **clientID**: Obbligatorio. Il clientID dell'applicazione, è necessario ottenere questo dal portale di registrazione dell'applicazione.

- **authority**: Facoltativo. URL che indica una directory da cui MSAL può richiedere token. Il valore predefinito è: `https://login.microsoftonline.com/common`.
    * In Azure AD è nel&lt;formato&gt;/&lt;&gt;https:// &lt;gruppo&gt; di destinatari dell'istanza `https://login.microsoftonline.com`, &lt;dove&gt; istanza è il dominio del provider di identità ( ad esempio, ) e il gruppo di destinatari è un identificatore che rappresenta il gruppo di destinatari di accesso. Questi possono essere i seguenti valori:
        * `https://login.microsoftonline.com/<tenant>`- tenant è un dominio associato al tenant, ad esempio `TenantID` contoso.onmicrosoft.com, o il GUID che rappresenta la proprietà della directory utilizzata solo per accedere agli utenti di un'organizzazione specifica.
        * `https://login.microsoftonline.com/common`- Utilizzato per accedere agli utenti con account aziendali e dell'istituto di istruzione o un account personale Microsoft.- Used to sign in users with work and school accounts or a Microsoft personal account.
        * `https://login.microsoftonline.com/organizations/`- Utilizzato per accedere agli utenti con account aziendali e dell'istituto di istruzione.
        * `https://login.microsoftonline.com/consumers/`- Utilizzato per accedere agli utenti solo con account Microsoft personale (live).
    * In Azure AD B2C è `https://<instance>/tfp/<tenant>/<policyName>/`nel formato , dove instance è il dominio B2C di Azure AD, ad esempio ,nome-tenant, tenant è il nome del tenant di Azure AD, ad esempio, "nome-tenant".onmicrosoft.com, policyName è il nome del criterio B2C da applicare.


- **validateAuthority**: Facoltativo.  Convalidare l'autorità emittente di token. Il valore predefinito è `true`. Per le applicazioni B2C, poiché il valore dell'autorità è noto e può `false`essere diverso per ogni criterio, la convalida dell'autorità non funzionerà e dovrà essere impostata su .

- **redirectUri**: Facoltativo.  URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale. L'impostazione predefinita è `window.location.href`.

- **postLogoutRedirectUri:** facoltativo.  Reindirizza l'utente `postLogoutRedirectUri` a dopo la disconnessione. Il valore `redirectUri`predefinito è .

- **navigateToLoginRequestUrl:** facoltativo. Possibilità di disattivare la navigazione predefinita per la pagina iniziale dopo l'accesso. L'impostazione predefinita è true. Viene utilizzato solo per i flussi di reindirizzamento.

- **cacheLocation**: Facoltativo.  Imposta l'archiviazione `localStorage` `sessionStorage`del browser su o su . Il valore predefinito è `sessionStorage`.

- **storeAuthStateInCookie**: Facoltativo.  Questo flag è stato introdotto in MSAL.js v0.2.2 come correzione per i problemi del ciclo di [autenticazione](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) in Microsoft Internet Explorer e Microsoft Edge. Attivare `storeAuthStateInCookie` il flag su true per sfruttare questa correzione. Quando questa opzione è abilitata, MSAL.js memorizzerà lo stato della richiesta di autenticazione necessario per la convalida dei flussi di autenticazione nei cookie del browser. Per impostazione predefinita, `false`questo flag è impostato su .

- **logger**: Facoltativo.  Oggetto Logger oggetto con un'istanza di callback che può essere fornito dallo sviluppatore per utilizzare e pubblicare i log in modo personalizzato. Per informazioni dettagliate sul passaggio dell'oggetto logger, vedere [registrazione con msal.js](msal-logging.md).

- **loadFrameTimeout**: Facoltativo.  Il numero di millisecondi di inattività prima che una risposta al rinnovo di un token da Azure AD debba essere considerato timeout. Il valore predefinito è 6 secondi.

- **tokenRenewalOffsetSeconds:** facoltativo. Il numero di millisecondi che imposta la finestra di offset necessaria per rinnovare il token prima della scadenza. Il valore predefinito è 300 millisecondi.

- **navigateFrameWait**: Facoltativo. Il numero di millisecondi che imposta il tempo di attesa prima che gli iframe nascosti passino alla destinazione. Il valore predefinito è 500 millisecondi.

Questi sono applicabili solo per essere passati dalla libreria del wrapper angolare MSAL:
- **unprotectedResources**: Facoltativo.  Matrice di URI che sono risorse non protette. MSAL non allegherà un token alle richieste in uscita con questi URI. L'impostazione predefinita è `null`.

- **protectedResourceMap:** facoltativo.  Viene einserito nel mapping delle risorse agli ambiti utilizzati da MSAL per collegare automaticamente i token di accesso nelle chiamate API Web. Viene ottenuto un singolo token di accesso per la risorsa. In questo modo è possibile eseguire ilhttps://graph.microsoft.com/v1.0/memapping di un percorso di risorsa specifico nel modo seguente:https://graph.microsoft.com/"" ", ["utente.lettura"] o l'URL dell'app della risorsa come: "" ", ["user.read", "mail.send"]] . Questa operazione è necessaria per le chiamate CORS. L'impostazione predefinita è `null`.
