---
title: Gestione dei token (Microsoft Authentication Library) | Azure
description: Informazioni sull'acquisizione e la memorizzazione nella cache dei token tramite Microsoft Authentication Library (MSAL).
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
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f7914744073f82d8a35d3679a1c65459e10b2f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532878"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>Acquisizione e memorizzazione nella cache dei token tramite MSAL
I [token di accesso](access-tokens.md) consentono ai client di chiamare in modo sicuro le API Web protette da Azure. Esistono diversi modi per acquisire un token usando Microsoft Authentication Library (MSAL). Alcuni modi richiedono interazioni dell'utente tramite un Web browser. Altri non richiedono alcuna interazione dell'utente. In generale, il modo per acquisire un token dipende dal fatto che l'applicazione sia un'applicazione client pubblica (app desktop o per dispositivi mobili) oppure un'applicazione client riservata (app Web, API Web o applicazione daemon, ad esempio un servizio di Windows).

MSAL memorizza un token nella cache dopo che è stato acquisito.  Il codice dell'applicazione deve tentare di ottenere un token in modo automatico (dalla cache) prima di acquisire un token con altri mezzi.

È anche possibile cancellare la cache dei token, attraverso la rimozione degli account dalla cache. Questa operazione, tuttavia, non rimuove il cookie di sessione, che si trova nel browser.

## <a name="scopes-when-acquiring-tokens"></a>Ambiti durante l'acquisizione dei token
Gli [ambiti](v2-permissions-and-consent.md) sono le autorizzazioni esposte da un'API Web per consentire alle applicazioni client di richiedere l'accesso. Le applicazioni client richiedono il consenso dell'utente per questi ambiti quando si effettuano richieste di autenticazione per ottenere i token per l'accesso alle API Web. MSAL consente di ottenere i token per accedere alle API di Azure AD per sviluppatori (v1.0) e Microsoft Identity Platform (v2.0). Il protocollo della versione 2.0 usa gli ambiti invece delle risorse nelle richieste. Per altre informazioni, leggere il [confronto tra v1.0 e v2.0](active-directory-v2-compare.md). Sulla base della configurazione dell'API Web della versione del token che accetta, l'endpoint v2.0 restituisce il token di accesso a MSAL.

Diversi metodi di acquisizione dei token di MSAL richiedono un parametro *ambiti*. Questo parametro è un semplice elenco di stringhe che dichiarano le autorizzazioni desiderate e le risorse richieste. Ambiti ben noti sono le [autorizzazioni di Microsoft Graph](/graph/permissions-reference).

In MSAL è anche possibile accedere alle risorse v1.0. Per altre informazioni, vedere [Ambiti per un'applicazione v1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Richiedere ambiti specifici per un'API Web
Quando l'applicazione deve richiedere i token con autorizzazioni specifiche per l'API di una risorsa, è necessario passare gli ambiti che contengono l'URI dell'ID app dell'API nel formato seguente: *&lt;URI ID app&gt;/&lt;ambito&gt;*

Ad esempio, gli ambiti per l'API Microsoft Graph sono: `https://graph.microsoft.com/User.Read`

In alternativa, gli ambiti per un'API Web personalizzata sono: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Solo per l'API Microsoft Graph, un valore di ambito `user.read` è associato al formato `https://graph.microsoft.com/User.Read` e possono essere usati in modo intercambiabile.

> [!NOTE]
> Alcune API Web come l'API di Azure Resource Manager (https://management.core.windows.net/) prevedono una barra finale ("/") nell'attestazione dei destinatari (aud) del token di accesso. In questo caso, è importante passare l'ambito come https://management.core.windows.net//user_impersonation (si noti la doppia barra) perché il token sia valido nell'API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Richiedere ambiti dinamici per il consenso incrementale
Durante la creazione di applicazioni tramite v1.0, era necessario registrare il set completo di autorizzazioni (ambiti statici) richiesto dall'applicazione per ottenere il consenso dell'utente al momento dell'accesso. Nella versione 2.0 è possibile richiedere autorizzazioni aggiuntive in base alle esigenze usando il parametro di ambito. Tali autorizzazioni sono denominate ambiti dinamici e permettono all'utente di offrire un consenso incrementale agli ambiti.

Inizialmente, ad esempio, è possibile eseguire l'accesso dell'utente e negare a quest'ultimo qualsiasi tipo di accesso. In un secondo momento, è possibile offrire all'utente la possibilità di leggere il proprio calendario richiedendo l'ambito del calendario nei metodi di acquisizione dei token e ottenere il consenso dell'utente.

Ad esempio: `https://graph.microsoft.com/User.Read` e `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Acquisizione di token in modo automatico (dalla cache)
MSAL gestisce una cache dei token (o due cache per le applicazioni client riservate) e memorizza nella cache un token dopo che è stato acquisito.  In molti casi, se si tenta di ottenere automaticamente un token, verrà acquisito un altro token con più ambiti in base a un token nella cache. È inoltre possibile aggiornare un token quando si avvicina alla scadenza (perché la cache dei token contiene anche un token di aggiornamento).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Modello di chiamata consigliato per le applicazioni client pubbliche
Il codice dell'applicazione deve prima tentare di ottenere un token in modo automatico (dalla cache).  Se la chiamata al metodo restituisce un errore o un'eccezione che indica che è necessaria l'interfaccia utente, provare ad acquisire un token con altri mezzi. 

Tuttavia, esistono due flussi prima dei quali **non si deve** tentare di acquisire un token automaticamente:

- Il [flusso di credenziali client](msal-authentication-flows.md#client-credentials), che non usa la cache dei token utente, ma una cache dei token dell'applicazione. Questo metodo verifica tale cache dei token dell'applicazione prima di inviare una richiesta al servizio token di sicurezza.
- Il [flusso del codice di autorizzazione](msal-authentication-flows.md#authorization-code) nelle app Web, perché riscatta un codice che l'applicazione ha ricevuto eseguendo l'accesso dell'utente e ottenendo il consenso per più ambiti. Poiché il codice viene passato come un parametro, anziché come un account, il metodo non può eseguire una ricerca nella cache prima di riscattare il codice, pertanto è comunque necessaria una chiamata al servizio.

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
- È possibile [ottenere un token con nome utente e password](msal-authentication-flows.md#usernamepassword) nelle applicazioni client desktop .NET Framework, ma questa operazione non è consigliata. Non usare nome utente e password nelle applicazioni client riservate.
- È possibile acquisire un token tramite il [flusso del codice del dispositivo](msal-authentication-flows.md#device-code) nelle applicazioni in esecuzione nei dispositivi che non dispongono di un Web browser. Vengono forniti un URL e un codice all'utente, che quindi passa a un Web browser in un altro dispositivo, immette il codice ed esegue l'accesso.  Azure AD invia quindi un token al dispositivo senza browser.

### <a name="confidential-client-applications"></a>Applicazioni client riservate 
Per le applicazioni client riservate (app Web, API Web o applicazioni daemon come un servizio di Windows):
- Acquisire i token **per l'applicazione stessa**, anziché per un utente, usando il [flusso di credenziali client](msal-authentication-flows.md#client-credentials). Questo metodo può essere usato per strumenti di sincronizzazione o per strumenti che elaborano gli utenti in generale e non un utente specifico. 
- Usare il [flusso On-Behalf-Of](msal-authentication-flows.md#on-behalf-of) per un'API Web per chiamare un'API per conto dell'utente. L'applicazione viene identificata con le credenziali del client al fine di acquisire un token basato su un'asserzione utente (ad esempio, SAML o un token JWT). Questo flusso è usato dalle applicazioni che devono accedere alle risorse di un determinato utente nelle chiamate da servizio a servizio.
- Acquisire i token usando il [flusso del codice di autorizzazione](msal-authentication-flows.md#authorization-code) nelle app Web dopo che l'utente esegue l'accesso tramite l'URL della richiesta di autorizzazione. Un'applicazione OpenID Connect in genere usa questo meccanismo, che consente all'utente di eseguire l'accesso usando OpenID Connect e quindi di accedere alle API Web per conto dell'utente.


## <a name="authentication-results"></a>Risultati dell'autenticazione 
Quando il client richiede un token di accesso, Azure AD restituisce anche un risultato dell'autenticazione che include alcuni metadati relativi al token di accesso. Queste informazioni includono l'ora di scadenza del token di accesso e gli ambiti per cui è valido. Questi dati consentono all'app di eseguire la memorizzazione intelligente nella cache dei token di accesso senza la necessità di analizzare il token di accesso stesso.  Il risultato dell'autenticazione espone:

- Il [token di accesso](access-tokens.md) per l'API Web per l'accesso alle risorse. Questa è una stringa, in genere JWT con codifica base64, ma il client non deve mai fare riferimento al contenuto del token di accesso. Non è garantito che il formato rimanga stabile e può essere crittografato per la risorsa. La scrittura di codice dipendente dal contenuto del token di accesso nel client è una delle principali fonti di errori e interruzioni per la logica client.
- Il [token ID](id-tokens.md) per l'utente (si tratta di un token JWT).
- La scadenza del token, che indica la data/ora in cui scade il token.
- L'ID tenant contiene il tenant in cui è stato trovato l'utente. Per gli utenti guest (scenari B2B con Azure AD), l'ID tenant è il tenant guest, non il tenant univoco. Quando il token viene inviato a nome di un utente, il risultato dell'autenticazione contiene anche le informazioni sull'utente. Per i flussi client riservati in cui vengono richiesti token senza utente (per l'applicazione), queste informazioni sull'utente sono null.
- Ambiti per cui è stato emesso il token.
- ID univoco per l'utente.

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulla [gestione di errori ed eccezioni](msal-handling-exceptions.md). 
