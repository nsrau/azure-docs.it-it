---
title: Token di sicurezza | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle nozioni di base dei token di sicurezza nella piattaforma di identità Microsoft (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: faaf4a9c4fe37bc184b9860390f1eb99eede035c
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584285"
---
# <a name="security-tokens"></a>Token di sicurezza

Un provider di identità centralizzato è particolarmente utile per le app che hanno utenti dislocati in tutto il mondo che non devono necessariamente accedere dalla rete aziendale. Microsoft Identity Platform autentica gli utenti e fornisce i token di sicurezza, ad esempio [token di accesso](developer-glossary.md#access-token), token di [aggiornamento](developer-glossary.md#refresh-token)e [token ID](developer-glossary.md#id-token), che consentono a un' [applicazione client](developer-glossary.md#client-application) di accedere alle risorse protette in un [server di risorse](developer-glossary.md#resource-server).

Un **token di accesso** è un token di sicurezza emesso da un [server di autorizzazione](developer-glossary.md#authorization-server) come parte di un flusso [OAuth 2,0](active-directory-v2-protocols.md) . Contiene informazioni sull'utente e sull'app per cui è previsto il token. che può essere usato per accedere alle API Web e ad altre risorse protette. Per altre informazioni sul modo in cui la piattaforma di identità Microsoft rilascia i token di accesso, vedere [token di accesso](access-tokens.md).

I token di accesso sono validi solo per un breve periodo di tempo, quindi i server di autorizzazione emetteranno a volte un **token di aggiornamento** nello stesso momento in cui viene emesso il token di accesso. Quando necessario, l'applicazione client può scambiare questo token di aggiornamento per un nuovo token di accesso. Per altre informazioni sul modo in cui la piattaforma di identità Microsoft usa i token di aggiornamento per revocare le autorizzazioni, vedere revoca dei [token](access-tokens.md#token-revocation).

I **token ID** vengono inviati all'applicazione client come parte di un flusso di [OpenID Connect](v2-protocols-oidc.md) . Possono essere inviati insieme o in sostituzione di un token di accesso e vengono usati dal client per autenticare l'utente. Per altre informazioni sul modo in cui la piattaforma di identità Microsoft rilascia i token ID, vedere [token ID](id-tokens.md).

## <a name="validating-security-tokens"></a>Convalida di token di sicurezza

È l'app per cui è stato generato il token, l'app Web che ha eseguito l'accesso all'utente o l'API Web chiamata per convalidare il token. Il token è firmato dal **server del token di sicurezza (STS)** con una chiave privata. Il servizio token di stato pubblica la chiave pubblica corrispondente. Per convalidare un token, l'app verifica la firma usando la chiave pubblica STS per verificare che la firma sia stata creata usando la chiave privata.

I token sono validi solo per un periodo di tempo limitato. In genere, il servizio token di servizio fornisce una coppia di token:

* Un token di accesso per accedere all'applicazione o alla risorsa protetta e
* Token di aggiornamento usato per aggiornare il token di accesso quando il token di accesso è prossimo alla scadenza.

I token di accesso vengono passati a un'API Web come bearer token nell' `Authorization` intestazione. Un'app può fornire un token di aggiornamento al servizio token di accesso e, se l'accesso dell'utente all'app non è stato revocato, otterrà un nuovo token di accesso e un nuovo token di aggiornamento. Questo è il modo in cui viene gestito lo scenario di un utente che lascia l'organizzazione. Quando il servizio token di accesso riceve il token di aggiornamento, non emette un altro token di accesso valido se l'utente non è più autorizzato.

## <a name="json-web-tokens-jwts-and-claims"></a>Token Web JSON (token JWT) e attestazioni

La piattaforma di identità Microsoft implementa i token di sicurezza come **token Web JSON (token JWT)** che contengono **attestazioni**.

Un' [attestazione](developer-glossary.md#claim) fornisce asserzioni su un'entità, ad esempio un'applicazione client o un [proprietario della risorsa](developer-glossary.md#resource-owner), a un'altra entità, ad esempio un server di risorse.

Le attestazioni sono coppie nome/valore che inoltrano informazioni sull'oggetto del token. Ad esempio, un'attestazione può contenere fact sull'entità di sicurezza autenticata dal server di autorizzazione. Le attestazioni presenti in un determinato token dipendono da molti elementi, tra cui il tipo di token, il tipo di credenziale usato per autenticare l'oggetto, la configurazione dell'applicazione e così via.

Le applicazioni possono utilizzare attestazioni per diverse attività, ad esempio:

* Convalida del token
* Identificazione del [tenant](developer-glossary.md#tenant) del soggetto del token
* Visualizzazione delle informazioni utente
* Determinazione dell'autorizzazione del soggetto

Un'attestazione è costituita da coppie chiave-valore che forniscono informazioni come:

* Server del token di sicurezza che ha generato il token
* Data di generazione del token
* Subject (ad esempio l'utente, ad eccezione dei daemon)
* Destinatari, ovvero l'app per cui è stato generato il token
* App (il client) che ha richiesto il token. Nel caso di app Web, questo potrebbe essere lo stesso dei destinatari

Per altre informazioni sul modo in cui la piattaforma di identità Microsoft implementa i token e le informazioni sulle attestazioni, vedere token di [accesso](access-tokens.md) e [token ID](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Modo in cui ogni flusso emette token e codici

A seconda del modo in cui viene compilato il client, può usare uno o più dei flussi di autenticazione supportati dalla piattaforma di identità Microsoft. Questi flussi possono produrre un'ampia gamma di token (token ID, token di aggiornamento, token di accesso) e codici di autorizzazione e richiedono token diversi per consentirne il funzionamento. Questo grafico fornisce una panoramica:

|Flusso | Richiede | token ID | token di accesso | token di aggiornamento | codice di autorizzazione |
|-----|----------|----------|--------------|---------------|--------------------|
|[Flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Flusso implicito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Flusso OIDC ibrido](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Riscatto token di aggiornamento](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token di aggiornamento | x | x | x| |
|[Flusso per conto di](v2-oauth2-on-behalf-of-flow.md) | token di accesso| x| x| x| |
|[Credenziali del client](v2-oauth2-client-creds-grant-flow.md) | | | x (solo app)| | |

I token emessi tramite la modalità implicita hanno una limitazione di lunghezza perché vengono passati di nuovo al browser tramite l'URL, `response_mode` dove `query` è `fragment`o.  Alcuni browser hanno un limite per le dimensioni dell'URL che possono essere inseriti nella barra del browser e hanno esito negativo quando è troppo lungo.  Pertanto, questi token non dispongono `groups` di attestazioni `wids` o.

## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti relativi alle nozioni di base sull'autenticazione e sull'autorizzazione:

* Vedere [autenticazione e autorizzazione](authentication-vs-authorization.md) per informazioni sui concetti di base di autenticazione e autorizzazione nella piattaforma di identità Microsoft.
* Vedere [modello di applicazione](application-model.md) per informazioni sul processo di registrazione dell'applicazione in modo che possa integrarsi con la piattaforma di identità Microsoft.
* Vedere [flusso di accesso all'app](app-sign-in-flow.md) per informazioni sul flusso di accesso di app Web, desktop e per dispositivi mobili nella piattaforma di identità Microsoft.
