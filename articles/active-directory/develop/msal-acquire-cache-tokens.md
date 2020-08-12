---
title: Acquisire & token della cache con Microsoft Authentication Library (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come acquisire e memorizzare nella cache i token con MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47af4015fa5c6d9a73ee597146890a29b4b9ef9d
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119896"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Acquisire e memorizzare nella cache i token tramite Microsoft Authentication Library (MSAL)

I [token di accesso](access-tokens.md) consentono ai client di chiamare in modo sicuro le API Web protette da Azure. Esistono diversi modi per acquisire un token tramite Microsoft Authentication Library (MSAL). Alcune richiedono l'interazione dell'utente tramite un Web browser, mentre altre non richiedono l'interazione dell'utente. In generale, il metodo usato per acquisire un token dipende dal fatto che l'applicazione sia un'applicazione client pubblica, ad esempio un'app desktop o per dispositivi mobili, oppure un'applicazione client riservata come app Web, API Web o applicazione daemon.

MSAL memorizza nella cache un token dopo che è stato acquisito. Il codice dell'applicazione deve prima provare a ottenere un token automaticamente dalla cache prima di provare ad acquisire un token in altro modo.

È anche possibile cancellare la cache dei token, attraverso la rimozione degli account dalla cache. Questa operazione non rimuove tuttavia il cookie di sessione che si trova nel browser.

## <a name="scopes-when-acquiring-tokens"></a>Ambiti durante l'acquisizione dei token

Gli [ambiti](v2-permissions-and-consent.md) sono le autorizzazioni che un'API Web espone a cui le applicazioni client possono richiedere l'accesso. Le applicazioni client richiedono il consenso dell'utente per questi ambiti quando si effettuano richieste di autenticazione per ottenere i token per l'accesso alle API Web. MSAL consente di ottenere i token per accedere alle API di Azure AD per sviluppatori (v1.0) e Microsoft Identity Platform (v2.0). Il protocollo della versione 2.0 usa gli ambiti invece delle risorse nelle richieste. Per altre informazioni, leggere il [confronto tra v1.0 e v2.0](../azuread-dev/azure-ad-endpoint-comparison.md). Sulla base della configurazione dell'API Web della versione del token che accetta, l'endpoint v2.0 restituisce il token di accesso a MSAL.

Molti dei metodi di acquisizione di token di MSAL richiedono un `scopes` parametro. Il `scopes` parametro è un elenco di stringhe che dichiarano le autorizzazioni desiderate e le risorse richieste. Gli ambiti noti sono le [autorizzazioni Microsoft Graph](/graph/permissions-reference).

In MSAL è anche possibile accedere alle risorse v1.0. Per ulteriori informazioni, vedere [ambiti per un'applicazione v 1.0](msal-v1-app-scopes.md).

### <a name="request-scopes-for-a-web-api"></a>Ambiti di richiesta per un'API Web

Quando l'applicazione deve richiedere un token di accesso con autorizzazioni specifiche per un'API di risorsa, passare gli ambiti che contengono l'URI ID app dell'API nel formato `<app ID URI>/<scope>` .

Alcuni valori di ambito di esempio per risorse diverse:

- API Microsoft Graph:`https://graph.microsoft.com/User.Read`
- API Web personalizzata:`api://11111111-1111-1111-1111-111111111111/api.read`

Il formato del valore dell'ambito varia a seconda della risorsa (API) che riceve il token di accesso e dei `aud` valori di attestazione accettati.

Solo per Microsoft Graph, l' `user.read` ambito viene mappato a `https://graph.microsoft.com/User.Read` ed entrambi i formati di ambito possono essere usati in modo interscambiabile.

Alcune API Web, ad esempio l'API Azure Resource Manager ( https://management.core.windows.net/) aspettano una barra finale ('/') nell'attestazione del pubblico ( `aud` ) del token di accesso. In questo caso, passare l'ambito come `https://management.core.windows.net//user_impersonation` , inclusa la barra doppia ('//').

Altre API potrebbero richiedere che *non sia incluso alcuno schema o host* nel valore di ambito e si aspettano solo l'ID app (un GUID) e il nome dell'ambito, ad esempio:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Se la risorsa downstream non è sotto il controllo, potrebbe essere necessario provare formati dei valori di ambito diversi, ad esempio con/senza schema e host, se si ricevono `401` o altri errori quando si passa il token di accesso alla risorsa.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Richiedere ambiti dinamici per il consenso incrementale

Quando le funzionalità fornite dall'applicazione o i relativi requisiti cambiano, è possibile richiedere ulteriori autorizzazioni in base alle esigenze utilizzando il parametro scope. Tali *ambiti dinamici* consentono agli utenti di fornire il consenso incrementale agli ambiti.

Ad esempio, è possibile accedere all'utente, ma negare inizialmente l'accesso a tutte le risorse. In un secondo momento, è possibile fornire loro la possibilità di visualizzare il calendario richiedendo l'ambito del calendario nel metodo di acquisizione del token e ottenendo il consenso dell'utente a tale scopo. Ad esempio, richiedendo gli `https://graph.microsoft.com/User.Read` `https://graph.microsoft.com/Calendar.Read` ambiti e.

## <a name="acquiring-tokens-silently-from-the-cache"></a>Acquisizione di token in modo automatico (dalla cache)

MSAL gestisce una cache dei token (o due cache per le applicazioni client riservate) e memorizza nella cache un token dopo che è stato acquisito. In molti casi, se si tenta di ottenere automaticamente un token, verrà acquisito un altro token con più ambiti in base a un token nella cache. È inoltre possibile aggiornare un token quando si avvicina alla scadenza (perché la cache dei token contiene anche un token di aggiornamento).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Modello di chiamata consigliato per le applicazioni client pubbliche

Il codice dell'applicazione deve prima provare a ottenere un token automaticamente dalla cache. Se la chiamata al metodo restituisce un errore o un'eccezione che indica che è necessaria l'interfaccia utente, provare ad acquisire un token con altri mezzi.

Esistono due flussi, tuttavia, in cui non si **deve** tentare di acquisire un token in modo invisibile all'utente:

- [Flusso di credenziali client](msal-authentication-flows.md#client-credentials), che non usa la cache dei token utente, ma una cache del token dell'applicazione. Questo metodo esegue la verifica della cache dei token dell'applicazione prima di inviare una richiesta al servizio token di sicurezza (STS).
- Il [flusso del codice di autorizzazione](msal-authentication-flows.md#authorization-code) nelle app Web, in quanto Riscatta un codice che l'applicazione ha ottenuto effettuando l'accesso dell'utente e consentindo loro di concedere il consenso a più ambiti. Poiché un codice e non un account vengono passati come parametro, il metodo non può cercare nella cache prima di riscattare il codice, che richiama una chiamata al servizio.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Modello di chiamata consigliato nelle app Web che usano il flusso del codice di autorizzazione

Per le applicazioni Web che usano il [flusso del codice di autorizzazione OpenID Connect](v2-protocols-oidc.md), il modello consigliato nei controller è il seguente:

- Creare un'istanza di un'applicazione client riservata con una cache dei token con serializzazione personalizzata.
- Acquisire il token usando il flusso del codice di autorizzazione

## <a name="acquiring-tokens"></a>Acquisizione dei token

In genere, il metodo di acquisizione di un token varia a seconda del fatto che si tratti di un client pubblico o di un'applicazione client riservata.

### <a name="public-client-applications"></a>Applicazioni client pubbliche

Per le applicazioni client pubbliche (app desktop o per dispositivi mobili):

- Spesso si acquisiscono i token in modo interattivo, chiedendo all'utente di eseguire l'accesso tramite un'interfaccia utente o una finestra popup.
- È possibile [ottenere un token in modo automatico per l'utente connesso](msal-authentication-flows.md#integrated-windows-authentication) usando l'autenticazione integrata di Windows (autenticazione integrata di Windows/Kerberos) se l'applicazione desktop è in esecuzione in un computer Windows aggiunto a un dominio o ad Azure.
- Può [ottenere un token con nome utente e password](msal-authentication-flows.md#usernamepassword) nelle applicazioni client desktop .NET Framework (scelta non consigliata). Non usare nome utente e password nelle applicazioni client riservate.
- Può acquisire un token tramite il [flusso del codice del dispositivo](msal-authentication-flows.md#device-code) in applicazioni in esecuzione su dispositivi che non dispongono di un Web browser. Vengono forniti un URL e un codice all'utente, che quindi passa a un Web browser in un altro dispositivo, immette il codice ed esegue l'accesso. Azure AD invia quindi un token al dispositivo senza browser.

### <a name="confidential-client-applications"></a>Applicazioni client riservate

Per le applicazioni client riservate (app Web, API Web o un'applicazione daemon come un servizio Windows), è necessario:

- Acquisire i token **per l'applicazione stessa**, anziché per un utente, usando il [flusso di credenziali client](msal-authentication-flows.md#client-credentials). Questa tecnica può essere utilizzata per sincronizzare gli strumenti o gli strumenti che elaborano gli utenti in generale e non in un utente specifico.
- Usare il [flusso per conto](msal-authentication-flows.md#on-behalf-of) di un'API Web per chiamare un'API per conto dell'utente. L'applicazione viene identificata con le credenziali client per acquisire un token basato su un'asserzione utente (SAML, ad esempio, o un token JWT). Questo flusso è usato dalle applicazioni che devono accedere alle risorse di un determinato utente nelle chiamate da servizio a servizio.
- Acquisire i token usando il [flusso del codice di autorizzazione](msal-authentication-flows.md#authorization-code) nelle app Web dopo che l'utente esegue l'accesso tramite l'URL della richiesta di autorizzazione. Un'applicazione OpenID Connect in genere usa questo meccanismo, che consente all'utente di eseguire l'accesso usando OpenID Connect e quindi di accedere alle API Web per conto dell'utente.

## <a name="authentication-results"></a>Risultati dell'autenticazione

Quando il client richiede un token di accesso, Azure AD restituisce anche un risultato di autenticazione che include i metadati relativi al token di accesso. Queste informazioni includono l'ora di scadenza del token di accesso e gli ambiti per cui è valido. Questi dati consentono all'app di eseguire la memorizzazione intelligente nella cache dei token di accesso senza la necessità di analizzare il token di accesso stesso. Il risultato dell'autenticazione espone:

- Il [token di accesso](access-tokens.md) per l'API Web per l'accesso alle risorse. Questa stringa è in genere un JWT con codifica Base64, ma il client non deve mai apparire all'interno del token di accesso. Il formato non è garantito che rimanga stabile e possa essere crittografato per la risorsa. Le persone che scrivono codice a seconda del contenuto del token di accesso nel client sono una delle fonti più comuni di errori e la rottura della logica del client.
- [Token ID](id-tokens.md) per l'utente (JWT).
- La scadenza del token, che indica la data/ora in cui scade il token.
- L'ID tenant contiene il tenant in cui è stato trovato l'utente. Per gli utenti guest (scenari B2B con Azure AD), l'ID tenant è il tenant guest, non il tenant univoco. Quando il token viene inviato a nome di un utente, il risultato dell'autenticazione contiene anche le informazioni sull'utente. Per i flussi client riservati in cui vengono richiesti token senza utente (per l'applicazione), queste informazioni sull'utente sono null.
- Ambiti per cui è stato emesso il token.
- ID univoco per l'utente.

## <a name="next-steps"></a>Passaggi successivi

Se si usa MSAL per Java, informazioni sulla [serializzazione della cache dei token personalizzata in MSAL per Java](msal-java-token-cache-serialization.md).

Informazioni sulla [gestione di errori ed eccezioni](msal-handling-exceptions.md).