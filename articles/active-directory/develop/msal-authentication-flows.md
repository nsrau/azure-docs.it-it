---
title: Flussi di autenticazione di MSAL | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sui flussi di autenticazione e sulle concessioni usate da Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/08/2020
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 0dbd7eb9203f31b580f586a8a7d1fa216533628c
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170514"
---
# <a name="authentication-flows"></a>Flussi di autenticazione

Microsoft Authentication Library (MSAL) supporta diversi flussi di autenticazione da utilizzare in diversi scenari di applicazione.

| Flusso | Descrizione | Campo di utilizzo |
|--|--|--|
| [Codice di autorizzazione](#authorization-code) | Usato nelle app che vengono installate su un dispositivo per ottenere l'accesso alle risorse protette, come le API Web. Consente di aggiungere accesso e API per le app per dispositivi mobili e desktop. | [Applicazioni desktop](scenario-desktop-overview.md), [applicazioni per dispositivi mobili](scenario-mobile-overview.md), [applicazioni Web](scenario-web-app-call-api-overview.md) |
| [Credenziali del client](#client-credentials) | Consente di accedere alle risorse ospitate sul Web tramite l'identità di un'applicazione. Comunemente usate per le interazioni da server a server che devono essere eseguite in background, senza l'interazione immediata con un utente. | [App daemon](scenario-daemon-overview.md) |
| [Codice del dispositivo](#device-code) | Consente agli utenti di accedere a dispositivi con vincoli di input, ad esempio una smart TV, un dispositivo IoT o una stampante. | [App desktop/per dispositivi mobili](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Concessione implicita](#implicit-grant) | Consente all'app di ottenere i token senza eseguire uno scambio di credenziali del server back-end. Consente all'app di eseguire l'accesso dell'utente, gestire la sessione e ottenere i token per altre API Web, tutto all'interno del codice JavaScript del client. | [Applicazioni a pagina singola (SPA)](scenario-spa-overview.md) |
| [On-Behalf-Of](#on-behalf-of) | Un'applicazione richiama un servizio o un'API Web, che a sua volta deve chiamare un altro servizio o un'altra API Web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. | [API Web](scenario-web-api-call-api-overview.md) |
| [Nome utente/password](#usernamepassword) | Consente a un'applicazione di eseguire l'accesso dell'utente gestendone direttamente la password. Questo flusso non è consigliato. | [App desktop/per dispositivi mobili](scenario-desktop-acquire-token.md#username-and-password) |
| [Autenticazione integrata di Windows](#integrated-windows-authentication) | Consente alle applicazioni sui computer aggiunti a un dominio o ad Azure Active Directory (Azure AD) di acquisire un token automaticamente (senza alcuna interazione con l'interfaccia utente da parte dell'utente). | [App desktop/per dispositivi mobili](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Modo in cui ogni flusso emette token e codici

A seconda di come viene compilata l'applicazione client, può usare uno o più flussi di autenticazione supportati dalla piattaforma di identità Microsoft. Questi flussi possono produrre diversi tipi di token, nonché codici di autorizzazione, e richiedono token diversi per consentirne il funzionamento.

| Flusso                                                                               | Richiede            | id_token | token di accesso | token di aggiornamento | codice di autorizzazione |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [Credenziali del client](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (solo app) |               |                    |
| [Flusso del codice del dispositivo](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Flusso implicito](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [Flusso on-behalf-of](v2-oauth2-on-behalf-of-flow.md)                                | token di accesso        | x        | x            | x             |                    |
| [Nome utente/password](v2-oauth-ropc.md) (ROPC)                                       | nome utente & password | x        | x            | x             |                    |
| [Flusso OIDC ibrido](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Riscatto del token di aggiornamento](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | token di aggiornamento       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>Autenticazione interattiva e non interattiva

Molti di questi flussi supportano l'acquisizione di token sia interattivi che non interattivi.

  - **Interactive** indica che all'utente può essere richiesto di immettere l'input. Ad esempio, richiedendo all'utente di eseguire l'accesso, eseguire l'autenticazione a più fattori o concedere il consenso aggiuntivo alle risorse.
  - L'autenticazione **non interattiva**o *invisibile*all'utente tenta di acquisire un token in un modo in cui il server di accesso *non può* richiedere informazioni aggiuntive.

L'applicazione basata su MSAL deve prima tentare di acquisire un token in modo *invisibile all'utente*e quindi in modo interattivo solo se il metodo non interattivo ha esito negativo. Per ulteriori informazioni su questo modello, vedere [acquisire e memorizzare nella cache i token tramite Microsoft Authentication Library (MSAL)](msal-acquire-cache-tokens.md).

## <a name="authorization-code"></a>Authorization code (Codice di autorizzazione)

La [concessione del codice di autorizzazione OAuth 2](v2-oauth2-auth-code-flow.md) può essere usata nelle app installate in un dispositivo per ottenere l'accesso alle risorse protette, ad esempio le API Web. Questo consente di aggiungere l'accesso e l'accesso all'API alle app desktop e per dispositivi mobili.

Quando l'utente accede ad applicazioni Web (siti Web), l'applicazione Web riceve un codice di autorizzazione. Il codice di autorizzazione viene riscattato per acquisire un token per chiamare le API Web.

![Diagramma del flusso del codice di autorizzazione](media/msal-authentication-flows/authorization-code.png)

Nel diagramma precedente, l'applicazione:

1. Richiede un codice di autorizzazione, che viene riscattato per un token di accesso.
2. Usa il token di accesso per chiamare un'API Web.

### <a name="considerations"></a>Considerazioni

- È possibile usare il codice di autorizzazione una sola volta per riscattare un token. Non tentare di acquisire un token più volte con lo stesso codice di autorizzazione perché è esplicitamente vietato dalla specifica del protocollo standard. Se il codice viene riscattato più volte, intenzionalmente o perché non si è consapevoli del fatto che un Framework esegue anche questa operazione, verrà riportato l'errore seguente:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>Credenziali client

Il [flusso di credenziali client OAuth 2](v2-oauth2-client-creds-grant-flow.md) consente di accedere alle risorse ospitate sul Web tramite l'identità di un'applicazione. Questo tipo di concessione viene comunemente usato per le interazioni da server a server che devono essere eseguite in background, senza l'interazione immediata con un utente. Questo tipo di applicazioni viene spesso definito daemon o account di servizio.

Il flusso di concessione delle credenziali client consente a un servizio Web (client riservato) di usare le proprie credenziali, invece di rappresentare un utente, per l'autenticazione durante la chiamata a un altro servizio Web. In questo scenario il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web. Per un livello più elevato di sicurezza, Microsoft Identity Platform consente anche al servizio chiamante di usare un certificato (invece di un segreto condiviso) come credenziale.

> [!NOTE]
> Il flusso client riservato non è disponibile su piattaforme mobili come UWP, Novell. iOS e Novell. Android perché supportano solo applicazioni client pubbliche. Le applicazioni client pubbliche non sanno come dimostrare l'identità dell'applicazione al provider di identità. È possibile ottenere una connessione sicura sui back-end dell'app Web o dell'API Web distribuendo un certificato.

### <a name="application-secrets"></a>Segreti dell'applicazione

![Diagramma del client riservato con password](media/msal-authentication-flows/confidential-client-password.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token usando il segreto dell'applicazione o credenziali della password.
2. Usa il token per effettuare richieste della risorsa.

### <a name="certificates"></a>Certificati

![Diagramma del client riservato con certificato](media/msal-authentication-flows/confidential-client-certificate.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token usando le credenziali del certificato.
2. Usa il token per effettuare richieste della risorsa.

Queste credenziali client devono essere:

- Registrate con Azure AD.
- Passato quando si costruisce l'oggetto applicazione client riservata nel codice.

## <a name="device-code"></a>Codice del dispositivo

Il [flusso del codice del dispositivo OAuth 2](v2-oauth2-device-code.md) consente agli utenti di accedere a dispositivi con vincoli di input come Smart TV, dispositivi Internet e stampanti. L'autenticazione interattiva con Azure AD richiede un Web browser. Quando il dispositivo o il sistema operativo non fornisce un Web browser, il flusso del codice del dispositivo consente all'utente di usare un altro dispositivo, ad esempio un computer o un telefono cellulare, per accedere in modo interattivo.

Usando il flusso del codice del dispositivo, l'applicazione ottiene i token tramite un processo in due passaggi progettato per questi dispositivi e sistemi operativi. Esempi di applicazioni di questo tipo includono quelli in esecuzione su dispositivi Internet e strumenti dell'interfaccia della riga di comando.

![Diagramma del flusso di codice del dispositivo](media/msal-authentication-flows/device-code.png)

Il diagramma precedente mostra quanto segue:

1. Ogni volta che è necessaria l'autenticazione utente, l'app fornisce un codice e chiede all'utente di usare un altro dispositivo come uno smartphone connesso a Internet per visitare un URL (ad esempio, `https://microsoft.com/devicelogin` ). All'utente viene quindi richiesto di immettere il codice e di procedere con una normale esperienza di autenticazione, incluse le richieste di consenso e [l'autenticazione](../authentication/concept-mfa-howitworks.md)a più fattori, se necessario.
1. Una volta completata correttamente l'autenticazione, l'app da riga di comando riceve i token necessari tramite un backchannel e li usa per eseguire le chiamate all'API Web necessarie.

### <a name="constraints"></a>Vincoli

- Il flusso del codice del dispositivo è disponibile solo nelle applicazioni client pubbliche.
- L'autorità passata durante la creazione dell'applicazione client pubblica deve essere una delle seguenti:
  - Tenant, nel formato in `https://login.microsoftonline.com/{tenant}/,` cui `{tenant}` è il GUID che rappresenta l'ID tenant o un nome di dominio associato al tenant.
  - Per gli account aziendali e dell'Istituto di istruzione nel formato `https://login.microsoftonline.com/organizations/` .

## <a name="implicit-grant"></a>Implicit grant (Concessione implicita)

Il flusso di [concessione implicita OAuth 2](v2-oauth2-implicit-grant-flow.md) consente all'app di ottenere i token dalla piattaforma di identità Microsoft senza eseguire uno scambio di credenziali del server back-end. Questo flusso consente all'app di eseguire l'accesso dell'utente, gestire una sessione e ottenere i token per altre API Web, tutto all'interno del codice JavaScript del client.

![Diagramma del flusso di concessione implicito](media/msal-authentication-flows/implicit-grant.svg)

Molte applicazioni Web moderne sono compilate come applicazioni a pagina singola (SPA) sul lato client scritte in JavaScript o in un Framework SPA, ad esempio angolare, Vue.js e React.js. Queste applicazioni vengono eseguite in un browser Web e presentano caratteristiche di autenticazione diverse rispetto alle applicazioni Web tradizionali sul lato server. Microsoft Identity Platform consente alle applicazioni a pagina singola di eseguire l'accesso degli utenti e ottenere token per l'accesso a servizi back-end o API Web usando il flusso di concessione implicito. Il flusso implicito consente all'applicazione di ottenere token ID per rappresentare l'utente autenticato e token di accesso necessari per chiamare le API protette.

Questo flusso di autenticazione non include scenari di applicazioni che usano framework JavaScript multipiattaforma come Electron o React-native perché richiedono ulteriori funzionalità per l'interazione con le piattaforme native.

I token emessi tramite la modalità flusso implicito hanno una **limitazione di lunghezza** perché vengono restituiti al browser in base all'URL, dove `response_mode` è `query` o `fragment` . Alcuni browser limitano la lunghezza dell'URL nella barra del browser e hanno esito negativo quando è troppo lungo. Quindi, questi token di flusso impliciti non contengono `groups` né `wids` attestazioni.

## <a name="on-behalf-of"></a>On-Behalf-Of

Il flusso [di flusso di autenticazione OAuth 2 per conto di](v2-oauth2-on-behalf-of-flow.md) viene usato quando un'applicazione richiama un servizio o un'API Web che a sua volta deve chiamare un altro servizio o un'API Web. L'idea consiste nel propagare l'identità e le autorizzazioni dell'utente delegato tramite la catena di richieste. Affinché il servizio di livello intermedio effettui richieste autenticate al servizio downstream, deve proteggere un token di accesso dalla piattaforma di identità Microsoft per *conto dell'* utente.

![Diagramma del flusso On-Behalf-Of](media/msal-authentication-flows/on-behalf-of.png)

Il diagramma precedente mostra quanto segue:

1. L'applicazione acquisisce un token di accesso per l'API Web.
2. Un client (Web, desktop, mobile o applicazione a pagina singola) chiama un'API Web protetta, aggiungendo il token di accesso come bearer token nell'intestazione di autenticazione della richiesta HTTP. L'API Web autentica l'utente.
3. Quando il client chiama l'API Web, essa richiede un altro token per conto dell'utente.
4. L'API Web protetta usa questo token per chiamare un'API Web downstream per conto dell'utente. L'API Web può anche richiedere in seguito token per altre API downstream (ma ancora per conto dello stesso utente).

## <a name="usernamepassword"></a>Nome utente/password

La concessione delle [credenziali password del proprietario della risorsa OAuth 2](v2-oauth-ropc.md) (ROPC) consente a un'applicazione di accedere all'utente gestendo direttamente la password. Nell'applicazione desktop è possibile usare il flusso nome utente/password per acquisire un token in modo invisibile all'utente. Quando si usa l'applicazione non è necessaria l'interfaccia utente.

![Diagramma del flusso nome utente/password](media/msal-authentication-flows/username-password.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token inviando il nome utente e la password al provider di identità.
2. Chiama un'API Web usando il token.

> [!WARNING]
> Questo flusso non è consigliato. Richiede un elevato livello di attendibilità e di esposizione delle credenziali. Usare questo flusso *solo* quando non è possibile usare flussi più sicuri. Per altre informazioni, vedere [Qual è la soluzione per il problema crescente delle password?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Il flusso preferito per acquisire un token in modo invisibile all'utente nei computer Windows aggiunti a un dominio è l'[autenticazione integrata di Windows](#integrated-windows-authentication). In altri casi, usare il [flusso del codice del dispositivo](#device-code).

Sebbene il flusso di nome utente/password possa essere utile in alcuni scenari come DevOps, evitarlo se si vuole usare il nome utente/password in scenari interattivi in cui si fornisce un'interfaccia utente personalizzata.

Usando nome utente e password:

- Gli utenti che devono eseguire l'autenticazione a più fattori non saranno in grado di eseguire l'accesso perché non è presente alcuna interazione.
- Gli utenti non saranno in grado di eseguire Single Sign-On.

### <a name="constraints"></a>Vincoli

Oltre ai [vincoli dell'autenticazione integrata di Windows](#integrated-windows-authentication), si applicano anche i vincoli seguenti:

- Il flusso di nome utente/password non è compatibile con l'accesso condizionale e l'autenticazione a più fattori. Di conseguenza, se l'app viene eseguita in un tenant di Azure AD in cui l'amministratore tenant richiede l'autenticazione a più fattori, non è possibile usare questo flusso. Molte organizzazioni lo fanno.
- ROPC funziona solo per gli account aziendali e dell'Istituto di istruzione. Non è possibile usare ROPC per gli account Microsoft (MSA).
- Il flusso è disponibile su .NET desktop e .NET Core, ma non su piattaforma UWP (Universal Windows Platform).
- In Azure AD B2C, il flusso ROPC funziona solo per gli account locali. Per informazioni su ROPC in MSAL.NET e Azure AD B2C, vedere [uso di ROPC con Azure ad B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

MSAL supporta l'autenticazione integrata di Windows (IWA) per applicazioni desktop e per dispositivi mobili che vengono eseguite in un computer Windows aggiunto a un dominio o Azure AD. Con l'autenticazione integrata, queste applicazioni possono acquisire un token in modo invisibile all'utente senza richiedere l'interazione dell'interfaccia utente.

![Diagramma dell'autenticazione integrata di Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

Nel diagramma precedente, l'applicazione:

1. Acquisisce un token con l'autenticazione integrata di Windows.
2. Usa il token per effettuare richieste della risorsa.

### <a name="constraints"></a>Vincoli

Autenticazione integrata di Windows (IWA) supporta *solo* utenti federati: gli utenti creati in Active Directory e supportati da Azure ad. Gli utenti creati direttamente in Azure AD senza Active Directory supporto (utenti gestiti) non possono usare questo flusso di autenticazione. Questa limitazione non influisce sul [flusso di nome utente/password](#usernamepassword).

Il sistema di autenticazione integrata è per applicazioni .NET Framework, .NET Core e piattaforma UWP (Universal Windows Platform).

L'autenticazione integrata di Windows non ignora l'autenticazione a più fattori. Se è configurata l'autenticazione a più fattori, è possibile che l'autenticazione integrata di Windows abbia esito negativo se è necessaria una richiesta di autenticazione a più fattori. L'autenticazione a più fattori richiede l'interazione dell'utente.

L'utente non controlla quando il provider di identità richiede l'esecuzione dell'autenticazione a due fattori, mentre l'amministratore tenant sì. In genere, l'autenticazione a due fattori è necessaria quando si accede da un paese o un'area geografica diversa, quando non si è connessi tramite VPN a una rete aziendale e talvolta anche quando si è connessi tramite VPN. Azure AD usa l'intelligenza artificiale per apprendere continuamente se è richiesta l'autenticazione a due fattori. Se non è possibile eseguire l'autenticazione integrata, è necessario eseguire il fallback a un [prompt utente interattivo](#interactive-and-non-interactive-authentication).

L'autorità passata quando si crea l'applicazione client pubblica deve essere una delle seguenti:

- Tenant, nel formato in `https://login.microsoftonline.com/{tenant}/,` cui `{tenant}` è il GUID che rappresenta l'ID tenant o un nome di dominio associato al tenant.
- Per qualunque account aziendale o di istituto di istruzione (`https://login.microsoftonline.com/organizations/`). Gli account personali Microsoft (MSA) non sono supportati. non è possibile usare i `/common` `/consumers` tenant o.

Poiché l'autenticazione integrata di Microsoft è un flusso invisibile all'utente, è necessario che siano soddisfatte le condizioni seguenti:

- L'utente dell'applicazione deve avere precedentemente acconsentito all'uso dell'applicazione.
- L'amministratore tenant deve avere precedentemente acconsentito all'uso dell'applicazione da parte di tutti gli utenti del tenant.

Questo significa che una delle seguenti condizioni è vera:

- Gli sviluppatori hanno selezionato **Concedi** nel portale di Azure per se stessi.
- Un amministratore tenant ha selezionato **Concedi/revoca il consenso dell'amministratore per {tenant dominio}** nella scheda **autorizzazioni API** della registrazione dell'app nel portale di Azure (vedere [aggiungere autorizzazioni per accedere alle API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- È stato fornito un modo per consentire agli utenti di fornire il consenso all'applicazione; vedere [richiesta di consenso utente singolo](v2-permissions-and-consent.md#requesting-individual-user-consent).
- È stato fornito un modo per consentire all'amministratore del tenant di concedere il consenso per l'applicazione. vedere il [consenso dell'amministratore](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

Il flusso di autenticazione integrata di Windows è abilitato per le app .NET desktop, .NET Core e Windows Universal Platform. In .NET Core è necessario fornire il nome utente all'autenticazione integrata di Windows, perché .NET Core non è in grado di ottenere i nomi utente dal sistema operativo.

Per altre informazioni sul consenso, vedere [autorizzazioni e consenso v 2.0](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati esaminati i flussi di autenticazione supportati da Microsoft Authentication Library (MSAL), informazioni sull'acquisizione e la memorizzazione nella cache dei token usati in questi flussi:

[Acquisire e memorizzare nella cache i token tramite Microsoft Authentication Library (MSAL)](msal-acquire-cache-tokens.md)
