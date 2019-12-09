---
title: Flussi di autenticazione MSAL | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sui flussi di autenticazione e sulle concessioni usate da Microsoft Authentication Library (MSAL).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c818b7d7508555e1233d4ef954502728f65abfb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917200"
---
# <a name="authentication-flows"></a>Flussi di autenticazione

Questo articolo descrive i diversi flussi di autenticazione forniti da Microsoft Authentication Library (MSAL).  Questi flussi possono essere utilizzati in diversi scenari di applicazione.

| Flusso | Description | Usato in|  
| ---- | ----------- | ------- | 
| [Interattivo](#interactive) | Ottiene il token tramite un processo interattivo che richiede all'utente le credenziali tramite un browser o una finestra popup. | [App desktop](scenario-desktop-overview.md), [app per dispositivi mobili](scenario-mobile-overview.md) |
| [Concessione implicita](#implicit-grant) | Consente all'app di ottenere i token senza eseguire uno scambio di credenziali del server back-end. Ciò consente all'app di eseguire l'accesso dell'utente, gestire la sessione e ottenere i token per altre API Web, tutto all'interno del codice JavaScript del client.| [Applicazioni a singola pagina (SPA)](scenario-spa-overview.md) |
| [Codice di autorizzazione](#authorization-code) | Usato nelle app installate in un dispositivo per ottenere l'accesso alle risorse protette, ad esempio le API Web. In questo modo è possibile aggiungere l'accesso e l'API alle app desktop e per dispositivi mobili. | [App desktop](scenario-desktop-overview.md), [app per dispositivi mobili](scenario-mobile-overview.md), [app Web](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Un'applicazione richiama un servizio o un'API Web, che a sua volta deve chiamare un altro servizio o un'API Web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. | [API Web](scenario-web-api-call-api-overview.md) |
| [Credenziali del client](#client-credentials) | Consente di accedere alle risorse ospitate sul Web tramite l'identità di un'applicazione. Utilizzato comunemente per le interazioni tra server che devono essere eseguite in background, senza interazione immediata con un utente. | [App daemon](scenario-daemon-overview.md) |
| [Codice del dispositivo](#device-code) | Consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una Smart TV, un dispositivo Internet o una stampante. | [App desktop/per dispositivi mobili](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Autenticazione integrata di Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Consente alle applicazioni in un dominio o in un Azure Active Directory (Azure AD) di acquisire un token automaticamente (senza alcuna interazione dell'interfaccia utente da parte dell'utente).| [App desktop/per dispositivi mobili](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome utente/password](scenario-desktop-acquire-token.md#username--password) | Consente a un'applicazione di accedere all'utente gestendo direttamente la propria password. Questo flusso non è consigliato. | [App desktop/per dispositivi mobili](scenario-desktop-acquire-token.md#username--password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Modo in cui ogni flusso emette token e codici
 
A seconda del modo in cui viene compilato il client, può usare uno o più dei flussi di autenticazione supportati dalla piattaforma di identità Microsoft.  Questi flussi possono produrre un'ampia gamma di token (id_tokens, token di aggiornamento, token di accesso) e codici di autorizzazione e richiedono token diversi per consentirne il funzionamento. Questo grafico fornisce una panoramica:
 
|Flusso | Obbligatorio | id_token | token di accesso | token di aggiornamento | codice di autorizzazione | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Flusso implicito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Flusso OIDC ibrido](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Riscatto token di aggiornamento](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token di aggiornamento | x | x | x| |
|[Flusso on-behalf-of](v2-oauth2-on-behalf-of-flow.md) | token di accesso| x| x| x| |
|[Flusso del codice del dispositivo](v2-oauth2-device-code.md) | | x| x| x| |
|[Credenziali del client](v2-oauth2-client-creds-grant-flow.md) | | | x (solo app)| | |
 
I token emessi tramite la modalità implicita hanno una limitazione di lunghezza perché vengono passati di nuovo al browser tramite l'URL (dove `response_mode` è `query` o `fragment`).  Alcuni browser hanno un limite per le dimensioni dell'URL che possono essere inseriti nella barra del browser e hanno esito negativo quando è troppo lungo.  Di conseguenza, questi token non dispongono di attestazioni `groups` o `wids`.

## <a name="interactive"></a>Interattività

MSAL supporta la possibilità di richiedere all'utente in modo interattivo le credenziali per l'accesso e ottenere un token usando tali credenziali.

![Diagramma del flusso interattivo](media/msal-authentication-flows/interactive.png)

Per ulteriori informazioni sull'utilizzo di MSAL.NET per acquisire in modo interattivo i token su piattaforme specifiche, vedere:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Novell iOS](msal-net-xamarin-ios-considerations.md)
- [Piattaforma UWP (Universal Windows Platform)](msal-net-uwp-considerations.md)

Per altre informazioni sulle chiamate interattive in MSAL. js, vedere il [comportamento della richiesta nelle richieste interattive MSAL. js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicit grant (Concessione implicita)

MSAL supporta il [flusso di concessione implicita OAuth 2](v2-oauth2-implicit-grant-flow.md), che consente all'app di ottenere i token dalla piattaforma di identità Microsoft senza eseguire uno scambio di credenziali del server back-end. Ciò consente all'app di eseguire l'accesso dell'utente, gestire la sessione e ottenere i token per altre API Web, tutto all'interno del codice JavaScript del client.

![Diagramma del flusso di concessione implicita](media/msal-authentication-flows/implicit-grant.svg)

Molte applicazioni Web moderne sono compilate come applicazioni a pagina singola sul lato client, scritte usando JavaScript o un Framework SPA come angolare, VME. js e React. js. Queste applicazioni vengono eseguite in un Web browser e presentano caratteristiche di autenticazione diverse rispetto alle applicazioni Web tradizionali sul lato server. La piattaforma Microsoft Identity consente alle applicazioni a singola pagina di accedere agli utenti e ottenere i token per accedere ai servizi back-end o alle API Web usando il flusso di concessione implicito. Il flusso implicito consente all'applicazione di ottenere i token ID per rappresentare l'utente autenticato e anche di accedere ai token necessari per chiamare le API protette.

Questo flusso di autenticazione non include scenari di applicazioni che usano framework JavaScript multipiattaforma, ad esempio Electron e React-native, perché richiedono ulteriori funzionalità per l'interazione con le piattaforme native.

## <a name="authorization-code"></a>Authorization code (Codice di autorizzazione)

MSAL supporta la [concessione del codice di autorizzazione OAuth 2](v2-oauth2-auth-code-flow.md). Questa concessione può essere usata nelle app installate in un dispositivo per ottenere l'accesso alle risorse protette, ad esempio le API Web. In questo modo è possibile aggiungere l'accesso e l'API alle app desktop e per dispositivi mobili. 

Quando l'utente accede ad applicazioni Web (siti Web), l'applicazione Web riceve un codice di autorizzazione.  Il codice di autorizzazione viene riscattato per acquisire un token per chiamare le API Web. Nelle app Web ASP.NET e ASP.NET Core l'unico obiettivo della `AcquireTokenByAuthorizationCode` consiste nell'aggiungere un token alla cache dei token. Il token può quindi essere usato dall'applicazione (in genere nei controller, che ottiene solo un token per un'API usando `AcquireTokenSilent`).

![Diagramma del flusso del codice di autorizzazione](media/msal-authentication-flows/authorization-code.png)

Nel diagramma precedente, l'applicazione:

1. Richiede un codice di autorizzazione, che viene riscattato per un token di accesso.
2. Usa il token di accesso per chiamare un'API Web.

### <a name="considerations"></a>Considerazioni

- È possibile usare il codice di autorizzazione una sola volta per riscattare un token. Non tentare di acquisire un token più volte con lo stesso codice di autorizzazione (non è esplicitamente consentito dalla specifica standard del protocollo). Se il codice viene riscattato più volte intenzionalmente o perché non si è consapevoli del fatto che un Framework esegue anche questa operazione, verrà riportato il seguente errore: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se si sta scrivendo un'applicazione ASP.NET o ASP.NET Core, questa situazione può verificarsi se non si indica al Framework che è già stato riscattato il codice di autorizzazione. A tale proposito, è necessario chiamare il metodo `context.HandleCodeRedemption()` del gestore dell'evento `AuthorizationCodeReceived`.

- Evitare di condividere il token di accesso con ASP.NET, che potrebbe impedire il corretto funzionamento del consenso incrementale. Per ulteriori informazioni, vedere [issue #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>Per conto di

MSAL supporta il [flusso di autenticazione OAuth 2 per conto del](v2-oauth2-on-behalf-of-flow.md).  Questo flusso viene usato quando un'applicazione richiama un servizio o un'API Web, che a sua volta deve chiamare un altro servizio o un'API Web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. Affinché il servizio di livello intermedio effettui richieste autenticate al servizio downstream, deve proteggere un token di accesso dalla piattaforma di identità Microsoft per conto dell'utente.

![Diagramma del flusso per conto di](media/msal-authentication-flows/on-behalf-of.png)

Nel diagramma precedente:

1. L'applicazione acquisisce un token di accesso per l'API Web.
2. Un client (Web, desktop, mobile o applicazione a singola pagina) chiama un'API Web protetta, aggiungendo il token di accesso come bearer token nell'intestazione di autenticazione della richiesta HTTP. L'API Web autentica l'utente.
3. Quando il client chiama l'API Web, l'API Web richiede un altro token per conto dell'utente.  
4. L'API Web protetta usa questo token per chiamare un'API Web downstream per conto dell'utente.  L'API Web può anche richiedere in seguito token per altre API downstream (ma ancora per conto dello stesso utente).

## <a name="client-credentials"></a>Credenziali del client

MSAL supporta il [flusso di credenziali client OAuth 2](v2-oauth2-client-creds-grant-flow.md). Questo flusso consente di accedere alle risorse ospitate sul Web tramite l'identità di un'applicazione. Questo tipo di concessione viene comunemente usato per le interazioni da server a server che devono essere eseguite in background, senza l'interazione immediata con un utente. Questi tipi di applicazioni sono spesso denominati daemon o account di servizio. 

Il flusso di concessione delle credenziali client consente a un servizio Web (un client riservato) di usare le proprie credenziali, anziché rappresentare un utente, per eseguire l'autenticazione quando si chiama un altro servizio Web. In questo scenario il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web. Per un livello più elevato di sicurezza, Microsoft Identity Platform consente anche al servizio chiamante di usare un certificato (invece di un segreto condiviso) come credenziale.

> [!NOTE]
> Il flusso client riservato non è disponibile sulle piattaforme per dispositivi mobili (UWP, Novell. iOS e Novell. Android), perché supportano solo le applicazioni client pubbliche. Le applicazioni client pubbliche non sanno come dimostrare l'identità dell'applicazione al provider di identità. È possibile ottenere una connessione sicura sui back-end dell'app Web o dell'API Web distribuendo un certificato.

MSAL.NET supporta due tipi di credenziali client. Queste credenziali client devono essere registrate con Azure AD. Le credenziali vengono passate ai costruttori dell'applicazione client riservata nel codice.

### <a name="application-secrets"></a>Segreti dell'applicazione

![Diagramma del client riservato con password](media/msal-authentication-flows/confidential-client-password.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token utilizzando il segreto dell'applicazione o le credenziali della password.
2. Usa il token per effettuare richieste della risorsa.

### <a name="certificates"></a>Certificati

![Diagramma del client riservato con certificato](media/msal-authentication-flows/confidential-client-certificate.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token usando le credenziali del certificato.
2. Usa il token per effettuare richieste della risorsa.

Queste credenziali client devono essere:
- Registrato con Azure AD.
- Passato alla costruzione dell'applicazione client riservata nel codice.

## <a name="device-code"></a>Codice dispositivo

MSAL supporta il [flusso del codice del dispositivo OAuth 2](v2-oauth2-device-code.md), che consente agli utenti di accedere ai dispositivi con vincoli di input, ad esempio una Smart TV, un dispositivo Internet o una stampante. L'autenticazione interattiva con Azure AD richiede un Web browser. Il flusso del codice del dispositivo consente all'utente di usare un altro dispositivo, ad esempio un altro computer o un telefono cellulare, per eseguire l'accesso in modo interattivo, in cui il dispositivo o il sistema operativo non fornisce un browser Web.

Usando il flusso del codice del dispositivo, l'applicazione ottiene i token tramite un processo in due passaggi appositamente progettato per questi dispositivi o sistemi operativi. Esempi di applicazioni di questo tipo includono quelli in esecuzione su dispositivi o strumenti da riga di comando (CLI). 

![Diagramma del flusso di codice del dispositivo](media/msal-authentication-flows/device-code.png)

Nel diagramma precedente:

1. Ogni volta che è necessaria l'autenticazione utente, l'app fornisce un codice e chiede all'utente di usare un altro dispositivo, ad esempio uno smartphone connesso a Internet, per passare a un URL, ad esempio https://microsoft.com/devicelogin). All'utente viene quindi richiesto di immettere il codice e si procede con una normale esperienza di autenticazione, incluse le richieste di consenso e l'autenticazione a più fattori, se necessario.

2. Una volta completata l'autenticazione, l'app della riga di comando riceve i token necessari tramite un canale di ritorno e li usa per eseguire le chiamate all'API Web necessarie.

### <a name="constraints"></a>Vincoli

- Il flusso del codice del dispositivo è disponibile solo per le applicazioni client pubbliche.
- L'autorità passata quando si crea l'applicazione client pubblica deve essere una delle seguenti:
  - Tenant (nel formato `https://login.microsoftonline.com/{tenant}/` dove `{tenant}` è il GUID che rappresenta l'ID tenant o un dominio associato al tenant).
  - Per gli account aziendali e dell'Istituto di istruzione (`https://login.microsoftonline.com/organizations/`).
- Gli account personali Microsoft non sono ancora supportati dall'endpoint Azure AD v 2.0 (non è possibile usare i tenant `/common` o `/consumers`).

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

MSAL supporta l'autenticazione integrata di Windows (IWA) per applicazioni desktop o per dispositivi mobili che vengono eseguite in un computer Windows aggiunto a un dominio o Azure AD. Con l'autenticazione integrata, queste applicazioni possono acquisire un token in modo invisibile all'utente (senza alcuna interazione dell'interfaccia utente da parte dell'utente). 

![Diagramma dell'autenticazione integrata di Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token utilizzando l'autenticazione integrata di Windows.
2. Usa il token per effettuare richieste della risorsa.

### <a name="constraints"></a>Vincoli

IWA supporta solo utenti federati, ovvero gli utenti creati in Active Directory e supportati da Azure AD. Gli utenti creati direttamente in Azure AD, senza Active Directory supporto (utenti gestiti) non possono usare questo flusso di autenticazione. Questa limitazione non influisce sul [flusso di nome utente/password](#usernamepassword).

IWA è per le app scritte per .NET Framework, .NET Core e le piattaforme di piattaforma UWP (Universal Windows Platform).

La funzionalità di autenticazione a più fattori non viene ignorata. Se è configurata l'autenticazione a più fattori, è possibile che l'autenticazione a più fattori non riesca se è necessaria una richiesta di autenticazione a più fattori La funzionalità di autenticazione a più fattori richiede l'interazione dell'utente.

Non si controlla quando il provider di identità richiede l'esecuzione dell'autenticazione a due fattori. L'amministratore del tenant lo esegue. In genere, è richiesta l'autenticazione a due fattori quando si accede da un paese diverso, quando non si è connessi tramite VPN a una rete aziendale e talvolta anche quando si è connessi tramite VPN. Azure AD usa l'intelligenza artificiale per apprendere continuamente se è richiesta l'autenticazione a due fattori. Se non è possibile eseguire l'autenticazione integrata, è necessario eseguire il fallback a un [prompt utente interattivo] (#interactive).

L'autorità passata quando si crea l'applicazione client pubblica deve essere una delle seguenti:
- Tenant (nel formato `https://login.microsoftonline.com/{tenant}/` dove `tenant` è il GUID che rappresenta l'ID tenant o un dominio associato al tenant).
- Per gli account aziendali e dell'Istituto di istruzione (`https://login.microsoftonline.com/organizations/`). Gli account personali Microsoft non sono supportati (non è possibile usare `/common` o `/consumers` tenant).

Poiché l'autenticazione integrata di Microsoft è un flusso invisibile all'utente, è necessario che siano soddisfatte le condizioni seguenti:
- L'utente dell'applicazione deve avere precedentemente acconsentito all'uso dell'applicazione. 
- L'amministratore del tenant deve avere precedentemente acconsentito a tutti gli utenti del tenant per l'uso dell'applicazione.

Ciò significa che si verifica una delle condizioni seguenti:
- Gli sviluppatori hanno selezionato la **concessione** per la portale di Azure.
- Un amministratore tenant ha selezionato **Concedi/revoca il consenso dell'amministratore per {tenant dominio}** nella scheda **autorizzazioni API** della registrazione per l'applicazione (vedere [aggiungere autorizzazioni per accedere alle API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- È stato fornito un modo per consentire agli utenti di concedere il consenso all'applicazione (vedere [richiedere il consenso dell'utente singolo](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- È stato fornito un modo per consentire all'amministratore del tenant di acconsentire per l'applicazione (vedere il [consenso dell'amministratore](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

Il flusso IWA è abilitato per le app .NET desktop, .NET Core e Windows Universal Platform. In .NET Core è necessario fornire il nome utente a IWA, perché .NET Core non è in grado di ottenere i nomi utente dal sistema operativo.
  
Per ulteriori informazioni sul consenso, vedere [autorizzazioni e consenso per la versione 2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nome utente/password

MSAL supporta la [concessione delle credenziali password del proprietario della risorsa OAuth 2](v2-oauth-ropc.md), che consente a un'applicazione di accedere all'utente gestendo direttamente la password. Nell'applicazione desktop è possibile usare il flusso di nome utente/password per acquisire un token in modo invisibile all'utente. Quando si usa l'applicazione non è necessaria alcuna interfaccia utente.

![Diagramma del flusso di nome utente/password](media/msal-authentication-flows/username-password.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token inviando il nome utente e la password al provider di identità.
2. Chiama un'API Web usando il token.

> [!WARNING]
> Questo flusso non è consigliato. Richiede un elevato livello di attendibilità e l'esposizione degli utenti.  Usare questo flusso solo quando non è possibile usare altri flussi, più sicuri. Per altre informazioni, vedere [Qual è la soluzione per il problema crescente delle password?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Il flusso preferito per acquisire un token in modo invisibile all'utente nei computer Windows aggiunti a un dominio è [l'autenticazione integrata di Windows](#integrated-windows-authentication). In caso contrario, è anche possibile usare il [flusso del codice del dispositivo](#device-code).

Sebbene questo sia utile in alcuni casi (scenari DevOps), se si desidera utilizzare il nome utente e la password in scenari interattivi in cui si fornisce un'interfaccia utente personalizzata, provare a evitarla. Utilizzando nome utente/password:
- Gli utenti che devono eseguire l'autenticazione a più fattori non saranno in grado di accedere (poiché non esiste alcuna interazione).
- Gli utenti non saranno in grado di eseguire Single Sign-On.

### <a name="constraints"></a>Vincoli

Oltre ai [vincoli di autenticazione integrata di Windows](#integrated-windows-authentication), si applicano anche i vincoli seguenti:

- Il flusso di nome utente/password non è compatibile con l'accesso condizionale e l'autenticazione a più fattori. Di conseguenza, se l'app viene eseguita in un tenant di Azure AD in cui l'amministratore tenant richiede multi-factor authentication, non è possibile usare questo flusso. Molte organizzazioni lo eseguono.
- Funziona solo per gli account aziendali e dell'Istituto di istruzione (non per gli account Microsoft).
- Il flusso è disponibile su .NET desktop e .NET Core, ma non su piattaforma UWP (Universal Windows Platform).

### <a name="azure-ad-b2c-specifics"></a>Specifiche di Azure AD B2C

Per ulteriori informazioni sull'utilizzo di MSAL.NET e Azure AD B2C, vedere [utilizzo di ROPC con Azure ad B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
