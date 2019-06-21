---
title: I flussi di autenticazione (Microsoft Authentication Library) | Azure
description: Informazioni sui flussi di autenticazione e concede utilizzate da Microsoft Authentication Library (MSAL).
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
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 821143d39f8a4c06501ee38ef598a9d06d267d72
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273108"
---
# <a name="authentication-flows"></a>Flussi di autenticazione

Questo articolo descrive i flussi di autenticazione diversi forniti da Microsoft Authentication Library (MSAL).  Questi flussi sono utilizzabile in un'ampia gamma di scenari delle diverse applicazioni.

| Flusso | Descrizione | Usato in|  
| ---- | ----------- | ------- | 
| [Interattivo](#interactive) | Ottiene il token tramite un processo interattivo che richiede all'utente le credenziali tramite un browser o una finestra popup. | [Le app desktop](scenario-desktop-overview.md), [App per dispositivi mobili](scenario-mobile-overview.md) |
| [Concessione implicita](#implicit-grant) | Consente all'app ottenere i token senza eseguire uno scambio di credenziali del server back-end. Ciò consente all'app di accesso dell'utente, gestire la sessione e ottenere i token per altre API web, tutto all'interno del client il codice JavaScript.| [Applicazioni a singola pagina (SPA)](scenario-spa-overview.md) |
| [Codice di autorizzazione](#authorization-code) | Usato nelle App installate in un dispositivo per accedere alle risorse protette, ad esempio le API web. In questo modo è possibile aggiungere accesso e accesso API alle App per dispositivi mobili e desktop. | [Le app desktop](scenario-desktop-overview.md), [App per dispositivi mobili](scenario-mobile-overview.md), [App web](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Un'applicazione richiama un servizio o web API, che a sua volta deve chiamare un altro servizio o l'API web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. | [API Web](scenario-web-api-call-api-overview.md) |
| [Credenziali del client](#client-credentials) | Consente di accedere alle risorse ospitate sul web usando l'identità di un'applicazione. In genere usata per le interazioni di server-to-server che devono essere eseguito in background, senza l'interazione immediata con un utente. | [App daemon](scenario-daemon-overview.md) |
| [Codice del dispositivo](#device-code) | Consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una smart TV, dispositivo IoT o stampante. | [App desktop o per dispositivi mobili](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Autenticazione integrata di Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Consente ai computer di applicazioni in Azure Active Directory (Azure AD) o di dominio aggiunto acquisire un token automaticamente (senza alcuna interazione dell'interfaccia utente da parte dell'utente).| [App desktop o per dispositivi mobili](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome utente/password](scenario-desktop-acquire-token.md#username--password) | Consente a un'applicazione di accedere all'utente gestendo direttamente le loro password. Questo flusso non è consigliato. | [App desktop o per dispositivi mobili](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interattività
MSAL supporta anche la possibilità di richiedere in modo interattivo all'utente le credenziali di accesso e ottenere un token usando tali credenziali.

![Diagramma di flusso interattivo](media/msal-authentication-flows/interactive.png)

Per altre informazioni sull'uso MSAL.NET per acquisire in modo interattivo i token in piattaforme specifiche, vedere:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Piattaforma UWP (Universal Windows Platform)](msal-net-uwp-considerations.md)

Per altre informazioni sulle chiamate interattive in msal. js, vedere [comportamento nelle richieste interattive msal. js del Prompt](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicit grant (Concessione implicita)

MSAL supporta anche il [flusso di concessione implicita di OAuth 2](v2-oauth2-implicit-grant-flow.md), che consente all'app di ottenere i token da piattaforma delle identità Microsoft senza eseguire un server back-end scambio di credenziali. Ciò consente all'app di accesso dell'utente, gestire la sessione e ottenere i token per altre API web, tutto all'interno del client il codice JavaScript.

![Diagramma di flusso di concessione implicita](media/msal-authentication-flows/implicit-grant.svg)

Molte applicazioni web moderne vengono compilate come applicazioni a pagina singola, client-side, scritte usando JavaScript o un framework di applicazione a singola pagina, ad esempio VUE, Angular e React. js. Queste applicazioni eseguite in un web browser e dispongono di caratteristiche di autenticazione diverso rispetto alle applicazioni web sul lato server tradizionali. La piattaforma delle identità Microsoft attiva applicazioni a pagina singola per l'accesso degli utenti e ottenere i token per accedere ai servizi back-end o le API web usando il flusso di concessione implicita. Il flusso implicito consente all'applicazione ottenere i token ID per rappresentare l'utente autenticato e anche i token necessari per chiamare le API protette di accesso.

Questo flusso di autenticazione non include gli scenari di applicazioni che usano i framework JavaScript più piattaforme, ad esempio Electron e React-Native, in quanto richiedono altre funzionalità per l'interazione con piattaforme native.

## <a name="authorization-code"></a>Authorization code (Codice di autorizzazione)
MSAL supporta anche il [concessione del codice di autorizzazione OAuth 2](v2-oauth2-auth-code-flow.md). Questa autorizzazione è utilizzabile nelle App installate in un dispositivo per accedere alle risorse protette, ad esempio le API web. In questo modo è possibile aggiungere accesso e accesso API alle App per dispositivi mobili e desktop. 

Quando gli utenti accedono alle applicazioni web (siti Web), l'applicazione web riceve un codice di autorizzazione.  Il codice di autorizzazione viene riscattato per acquisire un token per chiamare le API web. Nelle App web ASP.NET e ASP.NET Core, il cui unico scopo di `AcquireTokenByAuthorizationCode` consiste nell'aggiungere un token per la cache dei token. Il token può quindi essere usato dall'applicazione (in genere nel controller, che sufficiente ottenere un token per un'API usando `AcquireTokenSilent`).

![Diagramma di flusso del codice di autorizzazione](media/msal-authentication-flows/authorization-code.png)

Nel diagramma precedente, l'applicazione:

1. Richiede un codice di autorizzazione, che viene riscattato un token di accesso.
2. Usa il token di accesso per chiamare un'API web.

### <a name="considerations"></a>Considerazioni
- È possibile utilizzare il codice di autorizzazione una sola volta per riscattare un token. Non tentare di acquisire un token più volte con lo stesso codice di autorizzazione (è esplicitamente vietato dalla specifica dello standard protocol). Se si Riscatta il codice più volte intenzionalmente oppure perché non si è consapevoli che un framework esegue anche per l'utente, si otterrà l'errore seguente: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se si sta scrivendo un'applicazione ASP.NET o ASP.NET Core, questo può verificarsi che se si non indicare al framework che è già stato riscattato il codice di autorizzazione. A tale scopo, è necessario chiamare il `context.HandleCodeRedemption()` metodo di `AuthorizationCodeReceived` gestore dell'evento.

- Evitare di condividere il token di accesso con ASP.NET, che potrebbero impedire il consenso incrementale correttamente in corso. Per altre informazioni, vedere [problema #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

MSAL supporta anche il [flusso di autenticazione on-behalf-of di OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Questo flusso viene usato quando un'applicazione richiama un servizio o web API, che a sua volta deve chiamare un altro servizio o l'API web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. Per il servizio di livello intermedio effettuare richieste autenticate al servizio downstream, è necessario proteggere un token di accesso dalla piattaforma delle identità Microsoft per conto dell'utente.

![Diagramma di flusso on-behalf-of](media/msal-authentication-flows/on-behalf-of.png)

Nel diagramma precedente:

1. L'applicazione acquisisce un token di accesso per l'API web.
2. Un client (web, applicazioni desktop, mobili o singola pagina) chiama un'API, aggiungere il token di accesso come un token di connessione nell'intestazione di autenticazione della richiesta HTTP di web protetto. L'API web autentica l'utente.
3. Quando il client chiama l'API web, l'API web richiede un altro token on-behalf-of all'utente.  
4. L'API web protetta Usa questo token per chiamare un'API web downstream on-behalf-of all'utente.  L'API web può inoltre successiva richiesta di token per le altre API downstream (ma comunque per conto dell'utente stesso).

## <a name="client-credentials"></a>Credenziali del client

MSAL supporta anche il [flusso di credenziali client OAuth 2](v2-oauth2-client-creds-grant-flow.md). Questo flusso consente di accedere alle risorse ospitate sul web usando l'identità di un'applicazione. Questo tipo di concessione viene comunemente usato per le interazioni da server a server che devono essere eseguite in background, senza l'interazione immediata con un utente. Questi tipi di applicazioni sono spesso detta daemon o gli account del servizio. 

La concessione di credenziali client del flusso consente un servizio web (client riservati) per usare le proprie credenziali, invece di rappresentare un utente, eseguire l'autenticazione quando si chiama un altro servizio web. In questo scenario, il client è in genere un servizio web di livello intermedio, un servizio daemon o un sito Web. Per un livello più elevato di sicurezza, Microsoft Identity Platform consente anche al servizio chiamante di usare un certificato (invece di un segreto condiviso) come credenziale.

> [!NOTE]
> Poiché questi supportano solo le applicazioni client pubblici non è disponibile nelle piattaforme per dispositivi mobili (UWP, xamarin. IOS e xamarin. Android), il flusso client riservato. Le applicazioni client pubblici non sanno come dimostrare l'identità dell'applicazione per il Provider di identità. Una connessione sicura può essere ottenuta in un'app web o API web nuovamente termina tramite la distribuzione di un certificato.

MSAL.NET supporta due tipi di credenziali client. Queste credenziali client devono essere registrati con Azure AD. Le credenziali vengono passate per i costruttori dell'applicazione client riservata nel codice.

### <a name="application-secrets"></a>Segreti dell'applicazione 

![Diagramma del client riservato con password](media/msal-authentication-flows/confidential-client-password.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token usando le credenziali di password o segreto dell'applicazione.
2. Usa il token per effettuare richieste della risorsa.

### <a name="certificates"></a>Certificati 

![Diagramma del client riservato con un certificato](media/msal-authentication-flows/confidential-client-certificate.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token usando le credenziali del certificato.
2. Usa il token per effettuare richieste della risorsa.

Queste credenziali client devono essere:
- Registrato con Azure AD.
- Passato in fase di creazione dell'applicazione client riservata nel codice.


## <a name="device-code"></a>Codice del dispositivo
MSAL supporta anche il [flusso del codice OAuth 2 dispositivi](v2-oauth2-device-code.md), che consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una smart TV, dispositivo IoT o stampante. Autenticazione interattiva con Azure AD richiede un web browser. Il flusso di codice del dispositivo consente all'utente di usare un altro dispositivo (ad esempio, un altro computer o un telefono cellulare) per accedere in modo interattivo, in cui il dispositivo o sistema operativo non fornisce un web browser.

Tramite il flusso del codice di dispositivo, l'applicazione ottiene i token mediante un processo in due passaggi appositamente progettato per questi sistemi operativi o dispositivi. Esempi di tali applicazioni sono quelle in esecuzione su dispositivi IoT o strumenti da riga di comando (CLI). 

![Diagramma di flusso del codice di dispositivo](media/msal-authentication-flows/device-code.png)

Nel diagramma precedente:

1. Ogni volta che è necessaria l'autenticazione utente, l'app fornisce un codice e chiede all'utente di usare un altro dispositivo (ad esempio un smartphone connesso a internet) per passare a un URL (ad esempio, https://microsoft.com/devicelogin). L'utente verrà quindi richiesto di immettere il codice e procede attraverso un'esperienza di autenticazione normali, tra cui le richieste di consenso e multi-factor authentication se necessario.

2. Al termine dell'autenticazione, l'app della riga di comando riceve il token richiesto tramite un canale di supporto e usati per eseguire le chiamate all'API web che è necessario.

### <a name="constraints"></a>Vincoli

- Flusso del codice di dispositivo è disponibile solo in applicazioni client pubblici.
- L'autorità passato durante la creazione dell'applicazione client pubblica deve essere uno dei seguenti:
  - Tenant (nel formato `https://login.microsoftonline.com/{tenant}/` in cui `{tenant}` è il GUID che rappresenta l'ID tenant o in un dominio associato al tenant).
  - per qualsiasi lavoro e gli account dell'istituto di istruzione (`https://login.microsoftonline.com/organizations/`).
- Gli account personali Microsoft non sono ancora supportati dall'endpoint v2.0 di Azure AD (non è possibile usare la `/common` o `/consumers` tenant).

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows
MSAL supporta anche l'autenticazione integrata di Windows (IWA) per desktop o applicazioni per dispositivi mobili eseguite in un dominio o in Azure AD aggiunti al computer Windows. Usa autenticazione integrata di Windows, queste applicazioni possono acquisire un token automaticamente (senza alcuna interazione dell'interfaccia utente da parte dell'utente). 

![Diagramma di autenticazione Windows integrata](media/msal-authentication-flows/integrated-windows-authentication.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token usando l'autenticazione integrata di Windows.
2. Usa il token per effettuare richieste della risorsa.

### <a name="constraints"></a>Vincoli

Autenticazione integrata di Windows supporta gli utenti federati solo, vale a dire gli utenti creati in Active Directory e supportato da Azure AD. Gli utenti creati direttamente in Azure AD, senza Active Directory di backup (utenti gestiti) non è possibile usare questo flusso di autenticazione. Questa limitazione non influenza la [flusso di nome utente/password](#usernamepassword).

Autenticazione integrata di Windows sia per le app scritte per le piattaforme .NET Framework e .NET Core (Universal Windows Platform).

Autenticazione integrata di Windows non ignorare multi-factor authentication. Se multi-factor authentication è configurato, autenticazione integrata di Windows potrebbe non riuscire se è necessaria una richiesta di autenticazione a più fattori. Multi-factor authentication richiede l'intervento dell'utente.

Non è possibile controllare quando il provider di identità richiede l'autenticazione a due fattori da eseguire. Esegue l'amministratore del tenant. In genere, l'autenticazione a due fattori è obbligatorio quando si accede da un altro paese, quando non si è connessi tramite VPN a una rete aziendale e talvolta persino quando si è connessi tramite VPN. Azure AD Usa l'intelligenza artificiale per conoscere in modo continuativo se è necessaria l'autenticazione a due fattori. Se si verifica un errore di autenticazione integrata di Windows, è necessario eseguire il fallback a un prompt utente (https://aka.ms/msal-net-interactive).

L'autorità passato durante la creazione dell'applicazione client pubblica deve essere uno dei seguenti:
- Tenant (nel formato `https://login.microsoftonline.com/{tenant}/` in cui `tenant` è il guid che rappresenta l'ID tenant o in un dominio associato al tenant).
- per qualsiasi lavoro e gli account dell'istituto di istruzione (`https://login.microsoftonline.com/organizations/`). Non sono supportati gli account personali Microsoft (non è possibile usare `/common` o `/consumers` tenant).

Poiché l'autenticazione integrata di Windows è un flusso invisibile all'utente, uno dei seguenti deve essere soddisfatte:
- l'utente dell'applicazione deve avere precedentemente fornito il consenso per usare l'applicazione. 
- L'amministratore del tenant deve avere precedentemente fornito il consenso a tutti gli utenti nel tenant di usare l'applicazione.

Ciò significa che viene soddisfatta una delle operazioni seguenti:
- Gli sviluppatori hanno scelto **Concedi** nel portale di Azure per se stessi.
- Un amministratore del tenant ha selezionato **concedere/revocare il consenso dell'amministratore per {dominio del tenant}** nel **autorizzazioni delle API** scheda della finestra di registrazione per l'applicazione (vedere [aggiungere le autorizzazioni per accedere alle API web ](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Si have fornito un modo per gli utenti a fornire il consenso all'applicazione (vedere [richiesta di consenso utente singoli](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Si have fornito un modo per l'amministratore del tenant di concedere il consenso per l'applicazione (vedere [consenso dell'amministratore](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Il flusso di autenticazione integrata di Windows è abilitato per desktop .NET, .NET Core e le app della piattaforma universale di Windows. In .NET Core, è disponibile solo l'overload richiede il nome utente. La piattaforma .NET Core non è possibile richiedere il nome utente per il sistema operativo.
  
Per altre informazioni sul consenso dell'utente, vedere [v2.0 autorizzazioni e consenso](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Username/password 
MSAL supporta anche il [concessione credenziali password proprietario risorsa di OAuth 2](v2-oauth-ropc.md), che consente a un'applicazione di accedere all'utente gestendo direttamente le loro password. Nell'applicazione desktop, è possibile usare il flusso di nome utente/password per acquisire un token invisibile all'utente. Nessuna interfaccia utente è obbligatorio quando si usa l'applicazione.

![Diagramma del flusso di nome utente/password](media/msal-authentication-flows/username-password.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token inviando il nome utente e la password per il provider di identità.
2. Chiama un'API web usando il token.

> [!WARNING]
> Questo flusso non è consigliato. Richiede un elevato livello di attendibilità e l'utente l'esposizione.  Questo flusso si utilizzino solo quando non è possibile usare altri, più sicuro, i flussi. Per altre informazioni, vedere [qual è la soluzione al problema in continua crescita delle password?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Il flusso preferito prima di acquisire un token invisibile all'utente nel computer del dominio Windows viene [l'autenticazione integrata di Windows](#integrated-windows-authentication). In caso contrario, è anche possibile usare [flusso del codice di dispositivo](#device-code).

Anche se ciò è utile in alcuni casi (scenari DevOps), se si vuole usare nome utente/password negli scenari interattivi in cui è fornire la propria interfaccia utente, provare a evitare tale situazione. Usando nome utente/password:
- Utenti che hanno bisogno per eseguire l'autenticazione a più fattori non saranno in grado di accedere (perché non è disponibile alcuna interazione).
- Utenti non saranno in grado di eseguire single sign-on.

### <a name="constraints"></a>Vincoli

Oltre a indicare il [vincoli l'autenticazione Windows integrata](#integrated-windows-authentication), si applicano anche i vincoli seguenti:

- Il flusso di nome utente/password non è compatibile con l'accesso condizionale e multi-factor authentication. Di conseguenza, se l'app viene eseguita in un tenant di Azure AD in cui l'amministratore del tenant richiede l'autenticazione a più fattori, è possibile usare questo flusso. Molte organizzazioni di farlo.
- Funziona solo per gli account aziendali e dell'istituto di istruzione (non gli account Microsoft).
- Il flusso è disponibile per desktop .NET e .NET Core, ma non in Universal Windows Platform.

### <a name="azure-ad-b2c-specifics"></a>Specifiche di Azure AD B2C

Per altre informazioni sull'uso di MSAL.NET e Azure AD B2C, vedere [ROPC uso con Azure Active Directory B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
