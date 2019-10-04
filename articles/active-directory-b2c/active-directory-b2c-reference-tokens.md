---
title: Panoramica dei token-Azure Active Directory B2C
description: Informazioni sui token usati in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2b33c35b1e4f83c30e2efdf64aed0b5f2035c79b
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032085"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Panoramica dei token in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) rilascia tipi diversi di token di sicurezza durante l'elaborazione di ogni [flusso di autenticazione](active-directory-b2c-apps.md). Questo documento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

## <a name="token-types"></a>Tipi di token

Azure AD B2C supporta i [protocolli OAuth 2,0 e OpenID Connect](active-directory-b2c-reference-protocols.md), che usa i token per l'autenticazione e l'accesso sicuro alle risorse. Tutti i token usati in Azure AD B2C sono [token Web JSON (token JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) che contengono asserzioni di informazioni relative al titolare e all'oggetto del token.

Per la comunicazione con Azure AD B2C vengono usati i token seguenti:

- *Token ID* : JWT che contiene le attestazioni che è possibile usare per identificare gli utenti nell'applicazione. Questo token viene inviato in modo sicuro nelle richieste HTTP per la comunicazione tra due componenti della stessa applicazione o del medesimo servizio. Le attestazioni nei token ID possono essere usate in base alle esigenze. Vengono in genere usate per visualizzare le informazioni sull'account o per prendere decisioni relative al controllo di accesso in un'applicazione. I token ID sono firmati, ma non sono crittografati. Quando l'applicazione o l'API riceve un token ID, deve convalidare la firma per dimostrare che il token è autentico. L'applicazione o l'API deve anche convalidare alcune attestazioni nel token per dimostrarne la validità. A seconda dei requisiti dello scenario, le attestazioni convalidate da un'applicazione possono variare, ma l'applicazione deve eseguire alcune convalide di attestazione comuni in ogni scenario.
- *Token di accesso* : JWT che contiene le attestazioni che è possibile usare per identificare le autorizzazioni concesse alle API. I token di accesso sono firmati, ma non sono crittografati. I token di accesso vengono usati per fornire l'accesso alle API e ai server di risorse.  Quando l'API riceve un token di accesso, deve convalidare la firma per dimostrare che il token è autentico. Per dimostrarne la validità, l'API deve anche convalidare alcune attestazioni del token. A seconda dei requisiti dello scenario, le attestazioni convalidate da un'applicazione possono variare, ma l'applicazione deve eseguire alcune convalide di attestazione comuni in ogni scenario.
- I token di aggiornamento del *token* di aggiornamento vengono usati per acquisire nuovi token ID e token di accesso in un flusso OAuth 2,0. Forniscono all'applicazione l'accesso a lungo termine alle risorse per conto degli utenti senza richiedere l'interazione con tali utenti. I token di aggiornamento sono opachi per l'applicazione. Vengono rilasciati da Azure AD B2C e possono essere controllati e interpretati solo da Azure AD B2C. Sono di lunga durata, ma l'applicazione non deve essere scritta con la previsione che un token di aggiornamento durerà per un periodo di tempo specifico. I token di aggiornamento possono essere annullati in qualsiasi momento per vari motivi. L'unico modo per verificare se un token di aggiornamento è valido è tentare di riscattarlo effettuando una richiesta di token per Azure AD B2C. Quando si riscatta un token di aggiornamento per un nuovo token, si riceve un nuovo token di aggiornamento nella risposta del token. Salvare il nuovo token di aggiornamento. Sostituisce il token di aggiornamento usato in precedenza nella richiesta. Questa azione consente di garantire che i token di aggiornamento rimangano validi per il periodo di tempo più lungo possibile.

## <a name="endpoints"></a>Endpoint

Un' [applicazione registrata](tutorial-register-applications.md) riceve i token e comunica con Azure ad B2C inviando richieste a questi endpoint:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

I `/authorize` token di sicurezza ricevuti dall'applicazione da Azure ad B2C possono provenire dagli endpoint `/token` o. Quando vengono acquisiti dall' `/authorize` endpoint, i token ID vengono eseguiti usando il [flusso implicito](active-directory-b2c-reference-spa.md), che viene spesso usato per gli utenti che accedono alle applicazioni Web basate su JavaScript. Quando vengono acquisiti dall' `/token` endpoint, i token ID vengono eseguiti usando il flusso del [codice di autorizzazione](active-directory-b2c-reference-oidc.md#get-a-token), che mantiene il token nascosto dal browser.

## <a name="claims"></a>Attestazioni

Azure AD B2C consente un controllo con granularità fine sul contenuto dei token. È possibile configurare i [flussi utente](active-directory-b2c-reference-policies.md) e i [criteri personalizzati](active-directory-b2c-overview-custom.md) per inviare determinati set di dati utente nelle attestazioni necessarie per l'applicazione. Queste attestazioni possono includere proprietà standard, ad esempio **DisplayName** e **EmailAddress**. Le applicazioni possono usare queste attestazioni per autenticare in modo sicuro gli utenti e le richieste.

Le attestazioni nei token ID non vengono restituite in un ordine particolare. Le nuove attestazioni possono essere introdotte nei token ID in qualsiasi momento. L'applicazione non deve essere interrotta quando vengono introdotte nuove attestazioni. È anche possibile includere [attributi utente personalizzati](active-directory-b2c-reference-custom-attr.md) nelle attestazioni.

Nella tabella seguente sono elencate le attestazioni che è possibile prevedere in token ID e token di accesso emessi da Azure AD B2C.

| Name | Attestazione | Valore di esempio | DESCRIZIONE |
| ---- | ----- | ------------- | ----------- |
| Destinatari | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica il destinatario del token. Per Azure AD B2C, il destinatario è l'ID applicazione. L'applicazione deve convalidare questo valore e rifiutare il token se non corrisponde. Audience equivale a risorsa. |
| Rilasciato da | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token. Identifica inoltre la directory in cui l'utente è stato autenticato. L'applicazione deve convalidare l'attestazione dell'autorità emittente per verificare che il token provenga dall'endpoint appropriato. |
| Ora di emissione | `iat` | `1438535543` | Ora in cui il token è stato generato, rappresentata dal valore epoch time. |
| Ora di scadenza | `exp` | `1438539443` | Ora in cui il token non è più valido, rappresentata dal valore epoch time. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. |
| Non prima | `nbf` | `1438535543` | L’ora in cui il token diventa valido, rappresentata dal valore epoch time. Questa volta è in genere uguale all'ora in cui è stato emesso il token. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. |
| Versione | `ver` | `1.0` | Versione del token ID, come definito da Azure AD B2C. |
| Hash del codice | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hash di codice incluso in un token ID solo quando il token viene emesso insieme a un codice di autorizzazione OAuth 2,0. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Per ulteriori informazioni su come eseguire questa convalida, vedere la [specifica di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash del token di accesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hash del token di accesso incluso in un token ID solo quando il token viene emesso insieme a un token di accesso OAuth 2,0. Può essere usato per convalidare l'autenticità di un token di accesso. Per ulteriori informazioni su come eseguire questa convalida, vedere la [specifica di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Strategia per ridurre gli attacchi di riproduzione dei token. L'applicazione può specificare un parametro nonce in una richiesta di autorizzazione tramite `nonce` il parametro di query. Il valore fornito nella richiesta viene generato senza modifiche nell' `nonce` attestazione solo di un token ID. Questa attestazione consente all'applicazione di verificare il valore rispetto al valore specificato nella richiesta. L'applicazione deve eseguire la convalida durante il processo di convalida del token ID. |
| Subject | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Entità su cui il token asserisce informazioni, ad esempio l'utente di un'applicazione. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Per impostazione predefinita, l'attestazione dell'oggetto viene popolata con l'ID oggetto dell'utente nella directory. |
| Riferimento alla classe contesto di autenticazione | `acr` | Non applicabile | Utilizzato solo con i criteri precedenti. |
| Criteri del framework di attendibilità | `tfp` | `b2c_1_signupsignin1` | Nome del criterio utilizzato per acquisire il token ID. |
| Ora di autenticazione | `auth_time` | `1438535543` | Ora in cui un utente ha immesso le credenziali per l'ultima volta, rappresentate in Epoch Time. Non esiste alcuna distinzione tra l'autenticazione che rappresenta un accesso aggiornato, una sessione di Single Sign-On (SSO) o un altro tipo di accesso. L' `auth_time` ultima volta in cui l'applicazione (o l'utente) ha avviato un tentativo di autenticazione rispetto a Azure ad B2C. Il metodo usato per l'autenticazione non è differenziato. |
| Ambito | `scp` | `Read`| Autorizzazioni concesse alla risorsa per un token di accesso. Più autorizzazioni concesse sono separate da uno spazio. |
| Entità autorizzati | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | L'**ID applicazione** dell'applicazione client che ha avviato la richiesta. |

## <a name="configuration"></a>Configurazione

Le proprietà seguenti vengono usate per [gestire la durata dei token di sicurezza](configure-tokens.md) emessi da Azure ad B2C:

- **Durate dei token di accesso e ID (minuti)** : durata del token di connessione OAuth 2.0 usato per ottenere l'accesso a una risorsa protetta. Il valore predefinito è 60 minuti. Il valore minimo (inclusivo) è 5 minuti. Il valore massimo (inclusivo) è 1440 minuti.

- **Durata del token di aggiornamento (giorni)** : periodo di tempo massimo prima del quale è possibile usare un token di aggiornamento per acquisire un nuovo token di accesso o ID. Il periodo di tempo illustra anche l'acquisizione di un nuovo token di aggiornamento se all'applicazione è `offline_access` stato concesso l'ambito. Il valore predefinito è 14 giorni. Il valore minimo (inclusivo) è un giorno. Il valore massimo (inclusivo) è 90 giorni.

- **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)** -dopo questo periodo di tempo, l'utente deve eseguire nuovamente l'autenticazione, indipendentemente dal periodo di validità del token di aggiornamento più recente acquisito dall'applicazione. Questo valore può essere specificato solo se l'opzione è impostata su **Con vincoli**. Deve essere superiore o uguale al valore **Durata del token di aggiornamento (giorni)** . Se l'opzione è impostata su **Senza vincoli**, non sarà possibile specificare un valore specifico. Il valore predefinito è 90 giorni. Il valore minimo (inclusivo) è un giorno. Il valore massimo (inclusivo) è 365 giorni.

I seguenti casi d'uso sono abilitati usando queste proprietà:

- Consentire a un utente di mantenere la connessione a un'applicazione mobile per un periodo illimitato, purché l'utente sia continuamente attivo sull'applicazione. È possibile impostare l'opzione **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)** su **Non vincolato** nel flusso utente di accesso.
- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per i token di accesso.

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password.

## <a name="compatibility"></a>Compatibilità

Per [gestire la compatibilità dei token](configure-tokens.md)vengono utilizzate le proprietà seguenti:

- **Attestazione autorità di certificazione (iss)** : identifica il tenant di Azure AD B2C che ha emesso il token. Il valore predefinito è `https://<domain>/{B2C tenant GUID}/v2.0/`. Il valore di `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` include gli ID per il tenant Azure ad B2C e per il flusso utente usato nella richiesta del token. Se l'applicazione o la libreria deve Azure AD B2C essere conforme alla [specifica di OpenID Connect Discovery 1,0](https://openid.net/specs/openid-connect-discovery-1_0.html), usare questo valore.

- **Attestazione soggetto (sub)** : questa proprietà identifica l'entità per cui il token esegue l'asserzione delle informazioni. Il valore predefinito è **ObjectID**, che popola l' `sub` attestazione nel token con l'ID oggetto dell'utente. Il valore di **non supportato** viene fornito solo per compatibilità con le versioni precedenti. Si consiglia di passare a **ObjectID** non appena è possibile.

- **Attestazione che rappresenta l'ID criterio** : questa proprietà identifica il tipo di attestazione in cui viene popolato il nome dei criteri usato nella richiesta del token. Il valore predefinito è `tfp`. Il valore di `acr` viene fornito solo per compatibilità con le versioni precedenti.

## <a name="pass-through"></a>Pass-through

Quando viene avviato un percorso utente, Azure AD B2C riceve un token di accesso da un provider di identità. Azure AD B2C usa tale token per recuperare informazioni sull'utente. Si [Abilita un'attestazione nel flusso utente](idp-pass-through-user-flow.md) o si [definisce un'attestazione nel criterio personalizzato](idp-pass-through-custom.md) per passare il token attraverso le applicazioni registrate in Azure ad B2C. L'applicazione deve usare un [flusso utente V2](user-flow-versions.md) per sfruttare i vantaggi offerti dal passaggio del token come attestazione.

Azure AD B2C attualmente supporta solo il passaggio del token di accesso dei provider di identità OAuth 2,0, che includono Facebook e Google. Per tutti gli altri provider di identità, l'attestazione viene restituita vuota.

## <a name="validation"></a>Convalida

Per convalidare un token, l'applicazione deve verificare sia la firma che le attestazioni del token. Sono disponibili molte librerie open source per la convalida di token JWT, a seconda del linguaggio preferito. È consigliabile esplorare tali opzioni anziché implementare una logica di convalida personalizzata.

### <a name="validate-signature"></a>Convalida firma

Un JWT contiene tre segmenti, un' *intestazione*, un *corpo*e una *firma*. Il segmento della firma può essere usato per convalidare l'autenticità del token in modo che possa essere considerato attendibile dall'applicazione. I token Azure AD B2C vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256.

L'intestazione del token contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Il valore dell'attestazione **ALG** è l'algoritmo usato per firmare il token. Il valore dell'attestazione **Kid** è la chiave pubblica usata per firmare il token. In qualsiasi momento, Azure AD B2C possibile firmare un token usando uno qualsiasi di un set di coppie di chiavi pubbliche/private. Azure AD B2C ruota periodicamente il set di chiavi possibile. L'applicazione deve essere scritta per gestire automaticamente le modifiche delle chiavi. Una frequenza ragionevole per verificare la disponibilità di aggiornamenti per le chiavi pubbliche utilizzate da Azure AD B2C è ogni 24 ore.

Azure AD B2C include un endpoint dei metadati di OpenID Connect. Utilizzando questo endpoint, le applicazioni possono richiedere informazioni sui Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant di Azure AD B2C contiene un documento di metadati JSON per ogni criterio. Il documento metadati è un oggetto JSON che contiene diverse informazioni utili, I metadati contengono **jwks_uri**, che fornisce la posizione del set di chiavi pubbliche usate per firmare i token. Questo percorso è disponibile qui, ma è preferibile recuperare il percorso in modo dinamico usando il documento di metadati e analizzando **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Il documento JSON in questo URL contiene tutte le informazioni sulla chiave pubblica usata in un determinato momento. L'app può usare l'attestazione `kid` nell'intestazione del token JWT per selezionare la chiave pubblica nel documento JSON usata per firmare un determinato token. Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

Il documento di metadati per `B2C_1_signupsignin1` i criteri `contoso.onmicrosoft.com` nel tenant si trova in:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Per determinare quale criterio è stato usato per firmare un token e dove andare per richiedere i metadati, sono disponibili due opzioni. Prima di tutto, il nome del criterio è incluso nell'attestazione `acr` del token. È possibile analizzare le attestazioni all'esterno del corpo del token JWT decodificando il corpo in base 64 e deserializzando la stringa JSON risultante. L' `acr` attestazione è il nome del criterio usato per emettere il token. L'altra opzione consiste nel codificare i criteri nel valore del `state` parametro quando si emette la richiesta e quindi decodificarla per determinare quale criterio è stato usato. Entrambi i metodi sono validi.

La descrizione della convalida della firma non rientra nelle finalità di questo documento. Sono disponibili molte librerie open source che consentono di convalidare un token.

### <a name="validate-claims"></a>Convalida attestazioni

Quando le applicazioni o l'API riceve un token ID, deve eseguire anche alcuni controlli sulle attestazioni nel token ID. È necessario controllare le attestazioni seguenti:

- **audience** : verifica che il token ID sia stato assegnato all'applicazione.
- **non prima** e **scadenza** : verifica che il token ID non sia scaduto.
- **autorità emittente** : verifica che il token sia stato emesso per l'applicazione da Azure ad B2C.
- **nonce** : strategia per la mitigazione degli attacchi di riproduzione del token.

Per un elenco completo delle convalide che l'applicazione deve eseguire, vedere la [specifica di OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [usare i token di accesso](active-directory-b2c-access-tokens.md).

