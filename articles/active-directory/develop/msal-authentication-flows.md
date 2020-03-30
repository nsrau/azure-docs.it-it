---
title: Flussi di autenticazione MSAL Azure
titleSuffix: Microsoft identity platform
description: Informazioni sui flussi di autenticazione e sulle concessioni utilizzate da Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050519"
---
# <a name="authentication-flows"></a>Flussi di autenticazione

In questo articolo vengono descritti i diversi flussi di autenticazione forniti da Microsoft Authentication Library (MSAL).  Questi flussi possono essere utilizzati in diversi scenari di applicazione.

| Flusso | Descrizione | Campo di utilizzo|  
| ---- | ----------- | ------- | 
| [Interattiva](#interactive) | Ottiene il token tramite un processo interattivo che richiede all'utente le credenziali tramite un browser o una finestra popup. | [App desktop](scenario-desktop-overview.md), [app per dispositivi mobili](scenario-mobile-overview.md) |
| [Implicit grant (Concessione implicita)](#implicit-grant) | Consente all'app di ottenere token senza eseguire uno scambio di credenziali del server back-end. Ciò consente all'app di accedere all'utente, gestire la sessione e ottenere token per altre API Web, il tutto all'interno del codice JavaScript client.| [Applicazione a pagina singola](scenario-spa-overview.md) |
| [Codice di autorizzazione](#authorization-code) | Usato nelle app installate in un dispositivo per accedere a risorse protette, ad esempio le API Web. Ciò consente di aggiungere l'accesso e l'accesso API alle app per dispositivi mobili e desktop. | [App desktop,](scenario-desktop-overview.md) [app per dispositivi mobili,](scenario-mobile-overview.md) [app Web](scenario-web-app-call-api-overview.md) | 
| [Per conto di](#on-behalf-of) | Un'applicazione richiama un servizio o un'API Web, che a sua volta deve chiamare un altro servizio o UN'API Web.An application invokes a service or web API, which in turn needs to call another service or web API. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. | [API Web](scenario-web-api-call-api-overview.md) |
| [Credenziali del client](#client-credentials) | Consente di accedere alle risorse ospitate sul Web utilizzando l'identità di un'applicazione. Comunemente utilizzato per le interazioni da server a server che devono essere eseguite in background, senza interazione immediata con un utente. | [App Daemon](scenario-daemon-overview.md) |
| [Codice dispositivo](#device-code) | Consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una smart TV, un dispositivo IoT o una stampante. | [App desktop/mobile](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Autenticazione integrata di Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Consente alle applicazioni nei computer aggiunti al dominio o ad Azure Active Directory (Azure AD) di acquisire un token in modo invisibile all'utente (senza alcuna interazione dell'interfaccia utente da parte dell'utente).| [App desktop/mobile](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome utente/password](scenario-desktop-acquire-token.md#username-and-password) | Consente a un'applicazione di accedere all'utente gestendo direttamente la password. Questo flusso non è consigliato. | [App desktop/mobile](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Come ogni flusso emette token e codici
 
A seconda della modalità di compilazione del client, può utilizzare uno o più flussi di autenticazione supportati dalla piattaforma di identità Microsoft.  Questi flussi possono produrre una varietà di token (id_tokens, token di aggiornamento, token di accesso) e codici di autorizzazione e richiedono token diversi per farli funzionare. Questo grafico fornisce una panoramica:This chart provides an overview:
 
|Flusso | Richiede | id_token | token di accesso | aggiornare il token | codice di autorizzazione | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Flusso del codice di autorizzazioneAuthorization code flow](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Flusso implicito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Flusso OIDC ibrido](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Riscatto della token di aggiornamento](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | aggiornare il token | x | x | x| |
|[Flusso on-behalf-of](v2-oauth2-on-behalf-of-flow.md) | token di accesso| x| x| x| |
|[Flusso del codice del dispositivo](v2-oauth2-device-code.md) | | x| x| x| |
|[Credenziali del client](v2-oauth2-client-creds-grant-flow.md) | | | x (solo app)| | |
 
I token emessi tramite la modalità implicita hanno un limite `response_mode` di `query` `fragment`lunghezza dovuto al ritorno al browser tramite l'URL (where is o ).  Alcuni browser hanno un limite alla dimensione dell'URL che può essere inserito nella barra del browser e non riescono quando è troppo lungo.  Pertanto, questi token `groups` non `wids` hanno o attestazioni.

## <a name="interactive"></a>Interattività

MSAL supporta la possibilità di richiedere in modo interattivo all'utente le credenziali per l'accesso e ottenere un token utilizzando tali credenziali.

![Diagramma del flusso interattivo](media/msal-authentication-flows/interactive.png)

Per altre informazioni sull'uso di MSAL.NET per acquisire in modo interattivo i token su piattaforme specifiche, vedere:For more information on using MSAL.NET to interactively acquire tokens on specific platforms, see:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Piattaforma UWP (Universal Windows Platform)](msal-net-uwp-considerations.md)

Per ulteriori informazioni sulle chiamate interattive in MSAL.js, vedere [Comportamento di prompt nelle richieste interattive MSAL.js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicit grant (Concessione implicita)

MSAL supporta il flusso di [concessione implicita OAuth 2](v2-oauth2-implicit-grant-flow.md), che consente all'app di ottenere token dalla piattaforma di identità Microsoft senza eseguire uno scambio di credenziali del server back-end. Ciò consente all'app di accedere all'utente, gestire la sessione e ottenere token per altre API Web, il tutto all'interno del codice JavaScript client.

![Diagramma del flusso di concessione implicita](media/msal-authentication-flows/implicit-grant.svg)

Molte applicazioni web moderne sono costruite come lato client, applicazioni a pagina singola, scritto utilizzando JavaScript o un framework SPA come Angular, Vue.js, e React.js. Queste applicazioni vengono eseguite in un Web browser e hanno caratteristiche di autenticazione diverse rispetto alle tradizionali applicazioni Web lato server. La piattaforma di identità Microsoft consente alle applicazioni a pagina singola di accedere agli utenti e ottenere token per accedere ai servizi back-end o alle API Web, utilizzando il flusso di concessione implicita. Il flusso implicito consente all'applicazione di ottenere token ID per rappresentare l'utente autenticato e anche di accedere ai token necessari per chiamare LE API protette.

Questo flusso di autenticazione non include scenari di applicazione che usano framework JavaScript multipiattaforma, ad esempio Electron e React-Native, perché richiedono ulteriori funzionalità per l'interazione con le piattaforme native.

## <a name="authorization-code"></a>Authorization code (Codice di autorizzazione)

MSAL supporta la concessione del codice di [autorizzazione OAuth 2.](v2-oauth2-auth-code-flow.md) Questa concessione può essere usata nelle app installate in un dispositivo per accedere a risorse protette, ad esempio le API Web.This grant can be used in apps that are installed on a device to gain access to protected resources, such as web APIs. Ciò consente di aggiungere l'accesso e l'accesso API alle app per dispositivi mobili e desktop. 

Quando gli utenti accedono alle applicazioni Web (siti Web), l'applicazione Web riceve un codice di autorizzazione.  Il codice di autorizzazione viene riscattato per acquisire un token per chiamare le API Web.The authorization code is redeemed to acquire a token to call web APIs. In ASP.NET e ASP.NET app Web `AcquireTokenByAuthorizationCode` Core, l'unico obiettivo è aggiungere un token alla cache dei token. Il token può quindi essere utilizzato dall'applicazione (in genere nei controller `AcquireTokenSilent`, che ottengono semplicemente un token per un'API utilizzando ).

![Diagramma del flusso del codice di autorizzazione](media/msal-authentication-flows/authorization-code.png)

Nel diagramma precedente, l'applicazione:

1. Richiede un codice di autorizzazione, che viene riscattato per un token di accesso.
2. Utilizza il token di accesso per chiamare un'API Web.

### <a name="considerations"></a>Considerazioni

- Puoi usare il codice di autorizzazione una sola volta per riscattare un token. Non tentare di acquisire un token più volte con lo stesso codice di autorizzazione (è esplicitamente vietato dalla specifica dello standard del protocollo). Se si riscatta il codice più volte intenzionalmente, o perché non si è consapevoli del fatto che un framework esegue anche per l'utente, si otterrà il seguente errore:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se stai scrivendo un'applicazione ASP.NET o ASP.NET Core, ciò potrebbe verificarsi se non dici al framework che hai già riscattato il codice di autorizzazione. A tale scopo, è `context.HandleCodeRedemption()` necessario `AuthorizationCodeReceived` chiamare il metodo del gestore eventi.

- Evitare di condividere il token di accesso con ASP.NET, che potrebbe impedire il corretto consenso incrementale. Per ulteriori informazioni, vedere [problema #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>Per conto di

MSAL supporta il flusso di autenticazione per [conto di OAuth 2.](v2-oauth2-on-behalf-of-flow.md)  Questo flusso viene usato quando un'applicazione richiama un servizio o un'API Web, che a sua volta deve chiamare un altro servizio o un'API Web.This flow is used when an application invokes a service or web API, which in turn needs to call another service or web API. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. Affinché il servizio di livello intermedio effettui richieste autenticate al servizio downstream, deve proteggere un token di accesso dalla piattaforma di identità Microsoft, per conto dell'utente.

![Diagramma del flusso per conto di](media/msal-authentication-flows/on-behalf-of.png)

Nel diagramma precedente:

1. L'applicazione acquisisce un token di accesso per l'API Web.
2. Un client (web, desktop, mobile o applicazione a pagina singola) chiama un'API Web protetta, aggiungendo il token di accesso come token di connessione nell'intestazione di autenticazione della richiesta HTTP. L'API Web autentica l'utente.
3. Quando il client chiama l'API Web, l'API Web richiede un altro token per conto dell'utente.  
4. L'API Web protetta utilizza questo token per chiamare un'API Web downstream per conto dell'utente.  L'API Web può anche richiedere in seguito i token per altre API downstream (ma comunque per conto dello stesso utente).

## <a name="client-credentials"></a>Credenziali del client

MSAL supporta il [flusso delle credenziali client OAuth 2.](v2-oauth2-client-creds-grant-flow.md) Questo flusso consente di accedere alle risorse ospitate sul Web utilizzando l'identità di un'applicazione. Questo tipo di concessione viene comunemente usato per le interazioni da server a server che devono essere eseguite in background, senza l'interazione immediata con un utente. Questo tipo di applicazioni vengono spesso definite daemon o account di servizio. 

Il flusso di concessione delle credenziali del client consente a un servizio Web (un client riservato) di utilizzare le proprie credenziali, anziché rappresentare un utente, di eseguire l'autenticazione quando si chiama un altro servizio Web. In questo scenario, il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web. Per un livello più elevato di sicurezza, Microsoft Identity Platform consente anche al servizio chiamante di usare un certificato (invece di un segreto condiviso) come credenziale.

> [!NOTE]
> Il flusso client riservato non è disponibile sulle piattaforme mobili (UWP, Xamarin.iOS e Xamarin.Android), poiché queste supportano solo applicazioni client pubbliche. Le applicazioni client pubbliche non sanno come dimostrare l'identità dell'applicazione al provider di identità. È possibile ottenere una connessione sicura nei back-end dell'app Web o dell'API Web distribuendo un certificato.

MSAL.NET supporta due tipi di credenziali client. Queste credenziali client devono essere registrate con Azure AD. Le credenziali vengono passate ai costruttori dell'applicazione client riservata nel codice.

### <a name="application-secrets"></a>Segreti dell'applicazione

![Diagramma del client riservato con password](media/msal-authentication-flows/confidential-client-password.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token utilizzando le credenziali del segreto dell'applicazione o della password.
2. Utilizza il token per effettuare richieste della risorsa.

### <a name="certificates"></a>Certificati

![Diagramma del client riservato con certificato](media/msal-authentication-flows/confidential-client-certificate.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token utilizzando le credenziali del certificato.
2. Utilizza il token per effettuare richieste della risorsa.

Queste credenziali client devono essere:These client credentials need to be:
- Registrato con Azure AD.
- Passato alla costruzione dell'applicazione client riservata nel codice.

## <a name="device-code"></a>Codice dispositivo

MSAL supporta il flusso di [codice del dispositivo OAuth 2,](v2-oauth2-device-code.md)che consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una smart TV, un dispositivo IoT o una stampante. L'autenticazione interattiva con Azure AD richiede un Web browser. Il flusso del codice del dispositivo consente all'utente di usare un altro dispositivo (ad esempio, un altro computer o un telefono cellulare) per accedere in modo interattivo, in cui il dispositivo o il sistema operativo non fornisce un Web browser.

Utilizzando il flusso del codice del dispositivo, l'applicazione ottiene i token tramite un processo in due passaggi appositamente progettato per questi dispositivi o sistemi operativi. Esempi di tali applicazioni includono quelli in esecuzione su dispositivi IoT o strumenti della riga di comando (CLI). 

![Diagramma del flusso del codice del dispositivo](media/msal-authentication-flows/device-code.png)

Nel diagramma precedente:

1. Ogni volta che è richiesta l'autenticazione dell'utente, l'app fornisce un codice e chiede all'utente `https://microsoft.com/devicelogin`di utilizzare un altro dispositivo (ad esempio uno smartphone connesso a Internet) per accedere a un URL (ad esempio, ). All'utente viene quindi richiesto di immettere il codice e procede attraverso una normale esperienza di autenticazione, incluse le richieste di consenso e l'autenticazione a più fattori, se necessario.

2. Al termine dell'autenticazione, l'app della riga di comando riceve i token necessari tramite un canale di back e li usa per eseguire le chiamate API Web necessarie.

### <a name="constraints"></a>Vincoli

- Il flusso del codice del dispositivo è disponibile solo nelle applicazioni client pubbliche.
- L'autorità passata durante la costruzione dell'applicazione client pubblica deve essere una delle seguenti:
  - Tenanted (nel `https://login.microsoftonline.com/{tenant}/` formato `{tenant}` in cui è il GUID che rappresenta l'ID tenant o un dominio associato al tenant).
  - Per tutti gli account`https://login.microsoftonline.com/organizations/`aziendali e dell'istituto di istruzione ( ).
- Gli account personali Microsoft non sono ancora supportati dall'endpoint di Azure `/common` AD `/consumers` 2.0 (non è possibile usare i tenant).

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

MSAL supporta l'autenticazione integrata di Windows (IWA) per le applicazioni desktop o mobili eseguite in un computer Windows aggiunto a un dominio o ad Azure AD. Utilizzando IWA, queste applicazioni possono acquisire un token in modo invisibile all'utente (senza alcuna interazione dell'interfaccia utente da parte dell'utente). 

![Diagramma dell'autenticazione integrata di Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token utilizzando l'autenticazione integrata di Windows.
2. Utilizza il token per effettuare richieste della risorsa.

### <a name="constraints"></a>Vincoli

IWA supporta solo gli utenti federati, ovvero gli utenti creati in Active Directory e supportati da Azure AD. Gli utenti creati direttamente in Azure AD, senza il backup di Active Directory (utenti gestiti) non possono usare questo flusso di autenticazione. Questa limitazione non influisce sul [flusso nome utente/password](#usernamepassword).

IWA è per le app scritte per le piattaforme .NET Framework, .NET Core e Universal Windows Platform.

IWA non ignora l'autenticazione a più fattori. Se è configurata l'autenticazione a più fattori, IWA potrebbe non riuscire se è necessaria una richiesta di verifica dell'autenticazione a più fattori. L'autenticazione a più fattori richiede l'interazione dell'utente.

Non è possibile controllare quando il provider di identità richiede l'autenticazione a due fattori da eseguire. L'amministratore tenant lo fa. In genere, l'autenticazione a due fattori è necessaria quando si accede da un paese diverso, quando non si è connessi tramite VPN a una rete aziendale e talvolta anche quando si è connessi tramite VPN. Azure AD usa l'ia per sapere continuamente se è necessaria l'autenticazione a due fattori. Se IWA non riesce, è necessario eseguire il rollback a un [prompt utente interattivo] (#interactive).

L'autorità passata durante la costruzione dell'applicazione client pubblica deve essere una delle seguenti:
- Tenanted (nel `https://login.microsoftonline.com/{tenant}/` formato `tenant` in cui è il guid che rappresenta l'ID tenant o un dominio associato al tenant).
- Per tutti gli account`https://login.microsoftonline.com/organizations/`aziendali e dell'istituto di istruzione ( ). Gli account personali Microsoft non sono `/common` supportati `/consumers` (non è possibile utilizzare o tenant).

Poiché IWA è un flusso invisibile all'utente, deve essere vera una delle seguenti condizioni:
- L'utente dell'applicazione deve aver precedentemente acconsentito all'utilizzo dell'applicazione. 
- L'amministratore tenant deve aver precedentemente acconsentito a tutti gli utenti nel tenant per utilizzare l'applicazione.

Ciò significa che si tratta di una delle seguenti condizioni:
- Lo sviluppatore ha scelto **Grant** nel portale di Azure per se stessi.
- Un amministratore tenant ha selezionato **il consenso di concedere/revocare l'amministratore per** il dominio tenant nella scheda Autorizzazioni **API** della registrazione per l'applicazione (vedere Aggiungere autorizzazioni per accedere alle [API Web).](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)
- L'utente ha fornito un modo per consentire agli utenti di acconsentire all'applicazione (vedere [Richiesta del consenso dei singoli utenti).](v2-permissions-and-consent.md#requesting-individual-user-consent)
- L'amministratore tenant consente di ottenere il consenso per l'applicazione (vedere [il consenso dell'amministratore](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Il flusso IWA è abilitato per le app desktop .NET, .NET Core e Piattaforma universale Windows. In .NET Core è necessario fornire il nome utente a IWA, perché .NET Core non è in grado di ottenere nomi utente dal sistema operativo.
  
Per ulteriori informazioni sul consenso, vedere [autorizzazioni e consenso v2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nome utente/password

MSAL supporta la concessione di credenziali della password del proprietario della [risorsa OAuth 2](v2-oauth-ropc.md), che consente a un'applicazione di accedere all'utente gestendo direttamente la password. Nell'applicazione desktop è possibile utilizzare il flusso nome utente/password per acquisire un token in modo invisibile all'utente. Non è necessaria alcuna interfaccia utente quando si utilizza l'applicazione.

![Diagramma del flusso nome utente/password](media/msal-authentication-flows/username-password.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token inviando il nome utente e la password al provider di identità.
2. Chiama un'API Web utilizzando il token.

> [!WARNING]
> Questo flusso non è consigliato. Richiede un alto grado di fiducia e l'esposizione dell'utente.  È consigliabile usare questo flusso solo quando non è possibile usare altri flussi più sicuri. Per ulteriori informazioni, vedere [Qual è la soluzione al problema crescente delle password?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Il flusso preferito per l'acquisizione di un token in modo invisibile all'utente nei computer aggiunti a un dominio Windows è [Autenticazione integrata](#integrated-windows-authentication)di Windows . In caso contrario, è anche possibile usare [Flusso di codice dispositivo](#device-code).

Anche se questo è utile in alcuni casi (scenari DevOps), se si desidera utilizzare nome utente/password in scenari interattivi in cui si fornisce la propria interfaccia utente, provare a evitarlo. Utilizzando nome utente/password:
- Gli utenti che devono eseguire l'autenticazione a più fattori non saranno in grado di accedere (poiché non vi è alcuna interazione).
- Gli utenti non saranno in grado di eseguire l'accesso Single Sign-On.

### <a name="constraints"></a>Vincoli

Oltre [ai vincoli Autenticazione integrata](#integrated-windows-authentication)di Windows , vengono applicati anche i vincoli seguenti:

- Il flusso nome utente/password non è compatibile con l'accesso condizionale e l'autenticazione a più fattori. Di conseguenza, se l'app viene eseguita in un tenant di Azure AD in cui l'amministratore tenant richiede l'autenticazione a più fattori, non è possibile usare questo flusso. Molte organizzazioni lo fanno.
- Funziona solo per gli account aziendali e dell'istituto di istruzione (non per gli account Microsoft).
- Il flusso è disponibile su .NET desktop e .NET Core, ma non su piattaforma Windows universale.

### <a name="azure-ad-b2c-specifics"></a>Specifiche di Azure AD B2CAzure AD B2C specifics

Per altre informazioni sull'uso di MSAL.NET e Azure AD B2C, vedere [Uso di ROPC con Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
