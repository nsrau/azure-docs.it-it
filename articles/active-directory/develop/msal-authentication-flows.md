---
title: I flussi di autenticazione (Microsoft Authentication Library) | Azure
description: Scopri le concessioni/flussi di autenticazione utilizzate da Microsoft Authentication Library (MSAL).
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
ms.openlocfilehash: 612bdd2a5813237f05e9a30a0c90c3b643ece4b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111456"
---
# <a name="authentication-flows"></a>Flussi di autenticazione

Questo articolo descrive i flussi di autenticazione diversi forniti da Microsoft Authentication Library (MSAL).  Questi flussi sono utilizzabile in un'ampia gamma di scenari delle diverse applicazioni.

| Flusso | Descrizione | Usato in|  
| ---- | ----------- | ------- | 
| [Interattivo](#interactive) | Ottiene il token tramite un processo interattivo che richiede all'utente le credenziali tramite un browser o una finestra popup. | [Le app desktop](scenario-desktop-overview.md), [App per dispositivi mobili](scenario-mobile-overview.md) |
| [Concessione implicita](#implicit-grant) | Consente all'app ottenere i token senza eseguire uno scambio di credenziali di server back-end. In questo modo l'app può far accedere l'utente, gestire la sessione e ottenere i token per altre API Web, il tutto nel codice JavaScript client.| [Applicazioni a singola pagina (SPA)](scenario-spa-overview.md) |
| [Codice di autorizzazione](#authorization-code) | Usato nelle App installate in un dispositivo per accedere alle risorse protette, ad esempio le API web. In questo modo è possibile aggiungere l'accesso e l'API di accesso alle App per dispositivi mobili e desktop. | [Le app desktop](scenario-desktop-overview.md), [App per dispositivi mobili](scenario-mobile-overview.md), [App Web](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Un'applicazione richiama un servizio o un'API web, che a sua volta deve chiamare un altro servizio o un'API web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. | [API Web](scenario-web-api-call-api-overview.md) |
| [Credenziali del client](#client-credentials) | Consente di accedere alle risorse ospitate sul web usando l'identità di un'applicazione. In genere usata per le interazioni di server-to-server che devono essere eseguito in background, senza l'interazione immediata con un utente. | [App daemon](scenario-daemon-overview.md) |
| [Codice del dispositivo](#device-code) | Consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una smart TV, dispositivo IoT o stampante. | [App desktop o per dispositivi mobili](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Autenticazione integrata di Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Consente alle applicazioni in Azure AD o dominio aggiunto al computer in modo da acquisire un token automaticamente (senza alcuna interazione dell'interfaccia utente da parte dell'utente).| [App desktop o per dispositivi mobili](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome utente/password](scenario-desktop-acquire-token.md#username--password) | Consente a un'applicazione di accedere all'utente gestendo direttamente le loro password. Questo flusso non è consigliato. | [App desktop o per dispositivi mobili](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interattività
MSAL supporta anche la possibilità di richiedere in modo interattivo all'utente le proprie credenziali per accedere e ottenere un token usando tali credenziali.

![Flusso interattivo](media/msal-authentication-flows/interactive.png)

Per altre informazioni sull'uso di MSAL.NET per acquisire in modo interattivo i token in piattaforme specifiche, leggere gli articoli seguenti:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Piattaforma UWP (Universal Windows Platform)](msal-net-uwp-considerations.md)

Per altre informazioni sulle chiamate interattive in msal. js, vedere [richiedere comportamento nelle richieste interattive msal. js](msal-js-prompt-behavior.md)

## <a name="implicit-grant"></a>Implicit grant (Concessione implicita)

MSAL supporta anche il [flusso di concessione implicita di OAuth 2](v2-oauth2-implicit-grant-flow.md), che consente all'app di ottenere i token da piattaforma delle identità Microsoft senza eseguire un server back-end scambio di credenziali. In questo modo l'app può far accedere l'utente, gestire la sessione e ottenere i token per altre API Web, il tutto nel codice JavaScript client.

![Flusso di concessione implicita](media/msal-authentication-flows/implicit-grant.svg)

Molte applicazioni web moderne vengono compilate come applicazioni a pagina singola lato client scritte in JavaScript o un framework di applicazione a singola pagina, ad esempio VUE, Angular e React. js. Queste applicazioni eseguite in un web browser e dispongono di caratteristiche di autenticazione diverso rispetto alle applicazioni web tradizionali sul lato server. La piattaforma delle identità Microsoft consente alle applicazioni singola pagina di accesso degli utenti e ottenere i token per accedere a servizi back-end o le API web usando il flusso di concessione implicita. Il flusso implicito consente all'applicazione ottenere i token ID per rappresentare l'utente autenticato e anche i token necessari per chiamare le API protette di accesso.

Questo flusso di autenticazione non sono inclusi gli scenari di applicazione usando i framework JavaScript più piattaforme, ad esempio Electron e React-Native, poiché richiedono altre funzionalità per l'interazione con piattaforme native.

## <a name="authorization-code"></a>Authorization code (Codice di autorizzazione)
MSAL supporta anche il [concessione del codice di autorizzazione OAuth 2](v2-oauth2-auth-code-flow.md), che può essere usato nelle App installate in un dispositivo per accedere alle risorse protette, ad esempio le API web. In questo modo è possibile aggiungere l'accesso e l'API di accesso alle App per dispositivi mobili e desktop. 

Quando gli utenti accedono alle applicazioni web (siti web), l'applicazione web riceve un codice di autorizzazione.  Il codice di autorizzazione viene riscattato per acquisire un token per chiamare le API web. In ASP.NET o ASP.NET core web App, l'unico obiettivo della `AcquireTokenByAuthorizationCode` consiste nell'aggiungere un token per la cache dei token, in modo che può quindi essere utilizzata dall'applicazione (in genere nei controller) che è sufficiente ottenere un token per un'API usando `AcquireTokenSilent`.

![Flusso del codice di autorizzazione](media/msal-authentication-flows/authorization-code.png)

1. Richiede un codice di autorizzazione, che viene riscattato un token di accesso.
2. Usa il token di accesso per chiamare un'API web.

### <a name="considerations"></a>Considerazioni
- Il codice di autorizzazione è utilizzabile solo una volta per riscattare un token. Non tentare di acquisire un token più volte con lo stesso codice di autorizzazione (è esplicitamente vietato dalla specifica dello standard protocol). Se si Riscatta il codice più volte intenzionalmente oppure perché non si è consapevoli che un framework esegue anche per l'utente, si otterrà un errore: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se si sta scrivendo un'applicazione ASP.NET/ASP.NET Core, questo può verificarsi che se si non indicare al framework ASP.NET/Core che hai già riscattato il codice di autorizzazione. A tale scopo, è necessario chiamare `context.HandleCodeRedemption()` metodo di `AuthorizationCodeReceived` gestore dell'evento.

- Evitare di condividere il token di accesso con ASP.NET, che potrebbero impedire il consenso incrementale correttamente in corso.  Per altre informazioni, vedere [problema #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

MSAL supporta anche il [flusso di autenticazione on-behalf-of di OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Questo flusso viene usato quando un'applicazione richiama un servizio o un'API web, che a sua volta deve chiamare un altro servizio o un'API web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. Per il servizio di livello intermedio effettuare richieste autenticate al servizio downstream, è necessario proteggere un token di accesso dalla piattaforma delle identità Microsoft per conto dell'utente.

![Flusso on-behalf-of](media/msal-authentication-flows/on-behalf-of.png)

1. Acquisisce un token di accesso per l'API Web
2. Un client (Web, applicazione a singola pagina per dispositivi mobili, desktop,) chiama un'API Web protetta, aggiungere il token di accesso come un token di connessione nell'intestazione di autenticazione della richiesta HTTP. L'API Web che autentica l'utente.
3. Quando il client chiama l'API Web, l'API Web richiede un altro token on-behalf-of all'utente.  
4. L'API Web protetta Usa questo token per chiamare un'API Web downstream on-behalf-of all'utente.  L'API Web può richiedere anche in un secondo momento i token per le altre API downstream (ma comunque per conto dell'utente stesso).

## <a name="client-credentials"></a>Credenziali del client

MSAL supporta anche il [flusso di credenziali client OAuth 2](v2-oauth2-client-creds-grant-flow.md). Questo flusso consente di accedere alle risorse ospitate sul web usando l'identità di un'applicazione. Questo tipo di concessione viene comunemente usato per le interazioni da server a server che devono essere eseguite in background, senza l'interazione immediata con un utente. Questi tipi di applicazioni sono spesso detta daemon o gli account del servizio. 

La concessione di credenziali client del flusso consente un servizio web (client riservati) per usare le proprie credenziali, invece di rappresentare un utente, eseguire l'autenticazione quando si chiama un altro servizio web. In questo scenario il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web. Per un livello più elevato di sicurezza, Microsoft Identity Platform consente anche al servizio chiamante di usare un certificato (invece di un segreto condiviso) come credenziale.

> [!NOTE]
> Il flusso client riservato non è disponibile nelle piattaforme per dispositivi mobili (UWP, xamarin. IOS e xamarin. Android), dal momento che supportano solo le applicazioni client pubblici.  Le applicazioni client pubblici non sanno come dimostrare l'identità dell'applicazione per il Provider di identità. Una connessione sicura può essere ottenuta in un'app web o API web back-end tramite la distribuzione di un certificato.

MSAL.NET supporta due tipi di credenziali client. Queste credenziali client devono essere registrati con Azure AD. Le credenziali vengono passate per i costruttori dell'applicazione client riservata nel codice.

### <a name="application-secrets"></a>Segreti dell'applicazione 

![Client riservato con password](media/msal-authentication-flows/confidential-client-password.png)

1. Acquisisce un token usando le credenziali di chiave privata e la password dell'applicazione.
2. Usa il token per effettuare richieste della risorsa.

### <a name="certificates"></a>Certificati 

![Client riservato con un certificato](media/msal-authentication-flows/confidential-client-certificate.png)

1. Acquisisce un token usando le credenziali del certificato.
2. Usa il token per effettuare richieste della risorsa.

Queste credenziali client devono essere:
- Registrato con Azure AD.
- Passato in fase di creazione dell'applicazione client riservata nel codice.


## <a name="device-code"></a>Codice del dispositivo
MSAL supporta anche il [flusso del codice OAuth 2 dispositivi](v2-oauth2-device-code.md), che consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una smart TV, dispositivo IoT o stampante. Autenticazione interattiva con Azure AD richiede un web browser. Il flusso di codice del dispositivo consente all'utente di usare un altro dispositivo (ad esempio un altro computer o un telefono cellulare) per accedere in modo interattivo in cui il dispositivo o sistema operativo non fornisce un Web browser.

Tramite il flusso del codice di dispositivo, l'applicazione ottiene i token mediante un processo in due passaggi appositamente progettato per questi dispositivi/sistemi operativi. Esempi di tali applicazioni sono applicazioni in esecuzione su dispositivi iOT o strumenti da riga di comando (CLI). 

![Flusso del codice del dispositivo](media/msal-authentication-flows/device-code.png)

1. Ogni volta che è necessaria l'autenticazione utente, l'app fornisce un codice e chiede all'utente di usare un altro dispositivo (ad esempio un smartphone connesso a internet) per passare a un URL (ad esempio, https://microsoft.com/devicelogin), in cui l'utente verrà richiesto di immettere il codice. Di fatto, la pagina web guiderà l'utente tramite un'esperienza di autenticazione normali, incluse le richieste di consenso e l'autenticazione a più fattori se necessario.

2. Al termine dell'autenticazione, l'app della riga di comando riceverà il token richiesto tramite un canale di supporto e verrà usato per eseguire le chiamate all'API web che è necessario.

### <a name="constraints"></a>Vincoli

- Flusso del codice di dispositivo è disponibile solo in applicazioni client pubblici.
- L'autorità passato durante la creazione dell'applicazione client pubblica deve essere:
  - tenant (nel formato `https://login.microsoftonline.com/{tenant}/` in cui `{tenant}` è il GUID che rappresenta l'ID tenant o in un dominio associato al tenant.
  - o, eventuali account aziendali e dell'istituto di istruzione (`https://login.microsoftonline.com/organizations/`).
- Gli account personali Microsoft non sono ancora supportati dall'endpoint v2.0 di Azure AD (non è possibile usare la `/common` o `/consumers` tenant).

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows
MSAL supporta anche l'autenticazione integrata di Windows (IWA) per desktop o applicazioni per dispositivi mobili eseguite in un dominio o in Azure AD aggiunti al computer Windows. Usa autenticazione integrata di Windows, queste applicazioni possono acquisire un token automaticamente (senza alcuna interazione dell'interfaccia utente da parte dell'utente). 

![Autenticazione integrata di Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

1. Acquisisce un token tramite l'autenticazione integrata di Windows.
2. Usa il token per effettuare richieste della risorsa.

### <a name="constraints"></a>Vincoli

Supporta l'autenticazione integrata di Windows **federati** solo utenti.  Gli utenti creati in Active Directory e supportato da Azure Active Directory. Gli utenti creati direttamente in Azure AD, senza il backup di Active Directory (**gestito** utenti) non è possibile usare questo flusso di autenticazione. Questa limitazione influisce il [flusso di nome utente/password](#usernamepassword).

Autenticazione integrata di Windows sia per le app scritte per le piattaforme .NET Framework e .NET Core (Universal Windows Platform).

Autenticazione integrata di Windows non bypassano MFA (multi-factor authentication). Se è configurata l'autenticazione a più fattori, autenticazione integrata di Windows potrebbe non riuscire se una richiesta di autenticazione a più fattori è necessaria perché l'autenticazione a più fattori richiede l'intervento dell'utente. Questo è difficile. Autenticazione integrata di Windows non è interattivo, ma richiede l'autorizzazione a due fattori (2FA) interattività con gli utenti. Non si controlla quando il provider di identità richiede 2FA per essere eseguita, l'amministratore del tenant viene. Dal nostro osservazioni, 2FA è obbligatorio quando si accede da un altro paese, quando non è connesso tramite VPN a una rete aziendale e a volte anche quando si è connessi tramite VPN. Non prevede un set di regole deterministico, Azure Active Directory Usa l'intelligenza artificiale per conoscere in modo continuativo se 2FA è necessaria. Si deve effettuare il fallback a un prompt utente (https://aka.ms/msal-net-interactive) se si verifica un errore di autenticazione integrata di Windows.

L'autorità passato durante la creazione dell'applicazione client pubblica deve essere:
- tenant (nel formato `https://login.microsoftonline.com/{tenant}/` in cui `tenant` è il guid che rappresenta l'ID tenant o in un dominio associato al tenant.
- per qualsiasi lavoro e gli account dell'istituto di istruzione (`https://login.microsoftonline.com/organizations/`). Non sono supportati gli account personali Microsoft (non è possibile usare `/common` o `/consumers` tenant).

Poiché l'autenticazione integrata di Windows è un flusso invisibile all'utente:
- l'utente dell'applicazione deve avere precedentemente fornito il consenso per usare l'applicazione. 
- In alternativa, l'amministratore del tenant deve avere precedentemente fornito il consenso a tutti gli utenti nel tenant di usare l'applicazione.
- Vale a dire che:
    - gli sviluppatori hanno sia premuto la **Concedi** pulsante nel portale di Azure per l'utente 
    - un amministratore del tenant è premuto o la **concedere/revocare il consenso dell'amministratore per {dominio del tenant}** pulsante il **autorizzazioni delle API** scheda della finestra di registrazione per l'applicazione (vedere [aggiungere le autorizzazioni per accedere ad API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))
    - oppure, è stato fornito un modo per gli utenti a fornire il consenso all'applicazione (vedere [richiesta di consenso utente singoli](v2-permissions-and-consent.md#requesting-individual-user-consent))
    - oppure, è stato fornito un modo per l'amministratore del tenant di concedere il consenso per l'applicazione (vedere [consenso dell'amministratore](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))

Il flusso di autenticazione integrata di Windows è abilitato per desktop .NET, .NET Core e le app della piattaforma universale di Windows. In .NET Core solo l'overload richiede il nome utente è disponibile come la piattaforma .NET Core non è possibile richiedere il nome utente per il sistema operativo.
  
Per altre informazioni sul consenso dell'utente, vedere [v2.0 autorizzazioni e consenso](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Username/password 
MSAL supporta anche il [concessione credenziali password proprietario risorsa di OAuth 2](v2-oauth-ropc.md), che consente a un'applicazione di accedere all'utente gestendo direttamente le loro password. Nell'applicazione desktop, è possibile usare il flusso di nome utente/password per acquisire un token invisibile all'utente. Nessuna interfaccia utente è obbligatorio quando si usa l'applicazione.

![Flusso di nome utente/password](media/msal-authentication-flows/username-password.png)

1. Acquisisce un token inviando il nome utente e la password per il provider di identità.
2. Chiama un'API web usando il token.

> [!WARNING]
> Questo flusso verrà **sconsigliato** perché richiede un elevato livello di attendibilità e l'utente l'esposizione.  Questo flusso si utilizzino solo quando non è possibile usare altri, più sicuro, i flussi. Per altre informazioni su questo problema, vedere [questo articolo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Il flusso preferito prima di acquisire un token in modo invisibile in computer appartenenti al dominio di Windows viene [l'autenticazione integrata di Windows](#integrated-windows-authentication). In caso contrario, è anche possibile usare [flusso del codice di dispositivo](#device-code)

Anche se ciò è utile in alcuni casi (scenari DevOps), se si vuole usare nome utente/password negli scenari interattivi in cui è fornire la propria interfaccia utente, è consigliabile pensare davvero sullo spostamento dal controllo. Usando nome utente/password, si è offrendo remota una serie di operazioni:
- Core ai tenant dell'identità moderna: password Ottiene operava riprodotti. Poiché abbiamo questo concetto di un segreto di condivisione che può essere intercettato.
Questa impostazione è incompatibile con senza password.
- gli utenti che devono eseguire MFA non sarà in grado di accedere (perché non è disponibile alcuna interazione)
- Utenti non saranno in grado di eseguire single sign-on

### <a name="constraints"></a>Vincoli

Oltre a indicare il [vincoli l'autenticazione Windows integrata](#integrated-windows-authentication), si applicano anche i vincoli seguenti:

- Il flusso di nome utente/password non è compatibile con l'accesso condizionale e multi-factor authentication: Di conseguenza, se l'app viene eseguita in un tenant di Azure AD in cui l'amministratore del tenant richiede l'autenticazione a più fattori, è possibile usare questo flusso. Molte organizzazioni di farlo.
- Funziona solo per lavoro e gli account dell'istituto di istruzione (non MSA)
- Il flusso è disponibile per desktop .NET e .NET core, ma non in Universal Windows Platform.

### <a name="azure-ad-b2c-specifics"></a>Specifiche di Azure AD B2C

Per altre informazioni sull'uso di MSAL.NET e Azure AD B2C, leggere [ROPC uso con Azure Active Directory B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
