---
title: Panoramica dei token - Azure Active Directory B2C | Microsoft Docs
description: Informazioni sui token usati in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 11361bc6ab75e873e1b4081dcfc6492abc093b54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316936"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Panoramica dei token in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active B2C di Directory (Azure AD) B2C rilascia tipi diversi di token di sicurezza durante l'elaborazione di ciascuna [flusso di autenticazione](active-directory-b2c-apps.md). Questo documento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

## <a name="token-types"></a>Tipi di token

Azure AD B2C supporta il [protocolli OAuth 2.0 e OpenID Connect](active-directory-b2c-reference-protocols.md), che usa i token per l'autenticazione e accesso sicuro alle risorse. Sono tutti i token usati in Azure AD B2C [token web JSON (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) che contengono le asserzioni di informazioni relative alla connessione e l'oggetto del token.

I token seguenti vengono utilizzati nelle comunicazioni con Azure Active Directory B2C:

- *Token ID* -token JWT oggetto contenente le attestazioni che è possibile usare per identificare gli utenti nell'applicazione. Questo token viene inviato in modo sicuro nelle richieste HTTP per la comunicazione tra due componenti dell'applicazione o del servizio stesso. Le attestazioni nei token ID possono essere usate in base alle esigenze. Vengono comunemente utilizzati per visualizzare le informazioni sull'account o per prendere decisioni di controllo di accesso in un'applicazione. I token ID sono firmati, ma non crittografati. Quando l'applicazione o l'API riceve un token ID, è necessario convalidare la firma per dimostrare che il token sia autentico. L'API o l'applicazione deve anche convalidare alcune attestazioni nel token per dimostrare che è valido. A seconda dei requisiti di scenario, le attestazioni convalidate da un'applicazione possono variare, ma l'applicazione deve eseguire alcune convalide di attestazione comuni in ogni scenario.
- *Token di accesso* -token JWT oggetto contenente le attestazioni che è possibile usare per identificare le autorizzazioni concesse alle API. I token di accesso sono firmati, ma non sono crittografati. I token di accesso vengono usati per fornire l'accesso al server API e risorse.  Quando l'API riceve un token di accesso, è necessario convalidare la firma per dimostrare che il token sia autentico. Per dimostrarne la validità, l'API deve anche convalidare alcune attestazioni del token. A seconda dei requisiti di scenario, le attestazioni convalidate da un'applicazione possono variare, ma l'applicazione deve eseguire alcune convalide di attestazione comuni in ogni scenario.
- *Token di aggiornamento* -i token di aggiornamento vengono usati per acquisire nuovi token ID e token di accesso in un flusso di OAuth 2.0. Forniscono l'applicazione con accesso a lungo termine alle risorse per conto degli utenti senza richiedere l'interazione degli utenti. I token di aggiornamento sono opachi per l'applicazione. Vengono rilasciati da Azure AD B2C e possono essere controllati e interpretati solo da Azure AD B2C. Hanno lunga durate, ma l'applicazione non deve essere scritta aspettandosi che un token di aggiornamento duri per un periodo di tempo specifico. I token di aggiornamento possono essere annullati in qualsiasi momento per vari motivi. L'unico modo per l'applicazione di sapere se un token di aggiornamento è valido è tentare di riscattarlo mediante una richiesta di token da Azure AD B2C. Quando si Riscatta un token di aggiornamento per un nuovo token, si riceve un nuovo token di aggiornamento nella risposta del token. Salvare il nuovo token di aggiornamento. Sostituisce il token di aggiornamento usata in precedenza nella richiesta. Questa azione consente di garantire che il token di aggiornamento rimanga valido per più a lungo possibile. 

## <a name="endpoints"></a>Endpoint

Oggetto [registrazione applicazione](tutorial-register-applications.md) riceve i token e comunica con Azure AD B2C inviando richieste a questi endpoint:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

I token di sicurezza che l'applicazione riceve da Azure AD B2C possono provenire dal `/authorize` o `/token` gli endpoint. Quando i token ID vengono acquisiti dal `/authorize` endpoint, ma vengono effettuate usando il [flusso implicito](active-directory-b2c-reference-spa.md), che viene spesso usata per l'accesso degli utenti alle applicazioni web basate su javascript. Quando invece vengono acquisiti dall'endpoint `/token`, viene usato il [flusso di codice riservato](active-directory-b2c-reference-oidc.md), che mantiene il token nascosto al browser.

## <a name="claims"></a>Claims

Azure AD B2C consente un controllo con granularità fine sul contenuto dei token. È possibile configurare [flussi utente](active-directory-b2c-reference-policies.md) e [i criteri personalizzati](active-directory-b2c-overview-custom.md) per inviare determinati set di dati utente nelle attestazioni che sono necessari per l'applicazione. Le attestazioni possono includere proprietà standard, ad esempio **displayName** e **emailAddress**. Le applicazioni possono usare queste attestazioni per autenticare in modo sicuro gli utenti e le richieste. 

Le attestazioni nei token ID non vengono restituite in un ordine particolare. Possono essere introdotte nuove attestazioni nei token ID in qualsiasi momento. L'applicazione non deve interrompersi quando vengono introdotte nuove attestazioni. È anche possibile includere [attributi utente personalizzati](active-directory-b2c-reference-custom-attr.md) nelle attestazioni.

Nella tabella seguente elenca le attestazioni che è possibile aspettarsi nel token ID e accedere ai token emessi da Azure AD B2C.

| Name | Attestazione | Valore di esempio | DESCRIZIONE |
| ---- | ----- | ------------- | ----------- |
| Audience | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica il destinatario del token. Per Azure AD B2C, il destinatario è l'ID applicazione. L'applicazione deve convalidare questo valore e rifiutare il token, se non corrisponde. Audience equivale a risorsa. |
| Issuer | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token. Identifica, inoltre, la directory in cui è stato autenticato l'utente. L'applicazione deve convalidare l'attestazione dell'autorità di certificazione per assicurarsi che il token provenga all'endpoint appropriato. |
| Ora di emissione | `iat` | `1438535543` | Ora in cui il token è stato generato, rappresentata dal valore epoch time. |
| Scadenza | `exp` | `1438539443` | Ora in cui il token non è più valido, rappresentata dal valore epoch time. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. |
| Non prima | `nbf` | `1438535543` | L’ora in cui il token diventa valido, rappresentata dal valore epoch time. Questa volta equivale in genere il tempo che è stato rilasciato il token. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. |
| Version | `ver` | `1.0` | La versione del token ID, come definito da Azure AD B2C. |
| Hash del codice | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hash del codice incluso in un token ID solo quando il token viene rilasciato insieme a un codice di autorizzazione OAuth 2.0. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Per altre informazioni su come eseguire la convalida, vedere la [specifiche di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash del token di accesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hash token di accesso incluso in un token ID solo quando il token viene rilasciato insieme a un token di accesso di OAuth 2.0. Può essere usato per convalidare l'autenticità di un token di accesso. Per altre informazioni su come eseguire la convalida, vedere il [specifiche di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Strategia per ridurre gli attacchi di riproduzione dei token. L'applicazione può specificare un parametro nonce in una richiesta di autorizzazione usando il `nonce` parametro di query. Il valore specificato nella richiesta viene generato senza modifiche nel `nonce` attestazioni del token ID solo. Questa attestazione consente all'applicazione verificare il valore rispetto al valore specificato nella richiesta. L'applicazione deve eseguire la convalida durante il processo di convalida del token ID. |
| Subject | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | L'entità su cui il token asserisce informazioni, ad esempio l'utente di un'applicazione. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Per impostazione predefinita, l'attestazione dell'oggetto viene popolata con l'ID oggetto dell'utente nella directory. |
| Riferimento alla classe contesto di autenticazione | `acr` | Non applicabile | Utilizzato solo con i criteri precedenti. |
| Criteri del framework di attendibilità | `tfp` | `b2c_1_signupsignin1` | Il nome del criterio usato per acquisire il token ID. |
| Ora di autenticazione | `auth_time` | `1438535543` | L'ora in cui un utente ha immesso ultima credenziali, rappresentata dal valore epoch Time. |
| Scope | `scp` | `Read`| Le autorizzazioni concesse alla risorsa per un token di accesso. Le autorizzazioni multiple concesse sono separate da uno spazio. |
| Entità autorizzati | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | L'**ID applicazione** dell'applicazione client che ha avviato la richiesta. |

## <a name="configuration"></a>Configurazione

Le proprietà seguenti vengono usate per [gestire la durata dei token di sicurezza](configure-tokens.md) generati da Azure AD B2C:

- **Durate dei token di accesso e ID (minuti)**: durata del token di connessione OAuth 2.0 usato per ottenere l'accesso a una risorsa protetta. Il valore predefinito è 60 minuti. Il valore minimo (inclusivo) è 5 minuti. Il valore massimo (inclusivo) è 1440 minuti.

- **Durata del token di aggiornamento (giorni)** : periodo di tempo massimo prima che un token di aggiornamento è utilizzabile per acquisire un nuovo accesso o un token ID. Il periodo di tempo riguarda anche l'acquisizione di un nuovo token di aggiornamento, se l'applicazione è stata concessa la `offline_access` ambito. Il valore predefinito è 14 giorni. Il valore minimo (inclusivo) è un giorno. Il valore massimo (inclusivo) è 90 giorni.

- **Durata finestra temporale scorrevole del token di aggiornamento (giorni)** - dopo questo periodo di tempo specificato l'utente è obbligato a ripetere l'autenticazione, indipendentemente dal periodo di validità dell'ultimo aggiornamento dei token acquisito dall'applicazione. Questo valore può essere specificato solo se l'opzione è impostata su **Con vincoli**. Deve essere superiore o uguale al valore **Durata del token di aggiornamento (giorni)** . Se l'opzione è impostata su **Senza vincoli**, non sarà possibile specificare un valore specifico. Il valore predefinito è 90 giorni. Il valore minimo (inclusivo) è un giorno. Il valore massimo (inclusivo) è 365 giorni.

I seguenti casi d'uso sono abilitati usando queste proprietà:

- Consentire a un utente di mantenere la connessione a un'applicazione mobile per un periodo illimitato, purché l'utente sia continuamente attivo sull'applicazione. È possibile impostare l'opzione **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)** su **Non vincolato** nel flusso utente di accesso.
- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per i token di accesso.

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password. 

## <a name="compatibility"></a>Compatibilità

Le proprietà seguenti vengono usate per [gestire compatibilità dei token](configure-tokens.md):

- **Attestazione autorità di certificazione (iss)**: identifica il tenant di Azure AD B2C che ha emesso il token. Il valore predefinito è `https://<domain>/{B2C tenant GUID}/v2.0/`. Il valore di `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` include gli ID per il tenant di Azure AD B2C e il flusso utente che è stato usato nella richiesta del token. Se l'applicazione o libreria richiede Azure AD B2C è compatibile con il [specifica di OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html), usare questo valore.

- **Attestazione soggetto (sub)**: questa proprietà identifica l'entità per cui il token esegue l'asserzione delle informazioni. Il valore predefinito è **ObjectID**, che popola la `sub` attestazione nel token con l'ID oggetto dell'utente. Il valore di **non è supportato** viene fornito solo per motivi di compatibilità. È consigliabile passare al **ObjectID** appena possibile.

- **Attestazione che rappresenta l'ID criteri** -questa proprietà identifica il tipo di attestazione in cui viene popolato il nome del criterio usato nella richiesta del token. Il valore predefinito è `tfp`. Il valore di `acr` viene fornito solo per motivi di compatibilità.

## <a name="pass-through"></a>Pass-through

Quando viene avviato un percorso utente, Azure AD B2C riceve un token di accesso da un provider di identità. Azure AD B2C usa tale token per recuperare informazioni sull'utente. Si [abilitare un'attestazione nel flusso utente](idp-pass-through-user-flow.md) oppure [definiscono un'attestazione nei criteri personalizzati](idp-pass-through-custom.md) passare il token tramite alle applicazioni che si registrano in Azure AD B2C. L'applicazione deve usare un [flusso utente v2](user-flow-versions.md) possa sfruttare i vantaggi di passando il token come attestazione.

Azure AD B2C attualmente supporta solo passando il token di accesso OAuth 2.0 provider di identità, tra cui Facebook e Google. Per tutti gli altri provider di identità, l'attestazione viene restituita vuota. 

## <a name="validation"></a>Convalida

Per convalidare un token, l'applicazione deve controllare le attestazioni del token sia la firma. Molte librerie open source sono disponibili per la convalida dei token Jwt, a seconda del linguaggio preferito. È consigliabile che si esame tali opzioni anziché implementare la propria logica di convalida.

### <a name="validate-signature"></a>Convalidare firma

Un token JWT contiene tre segmenti una *intestazione*, un *corpo*e un *firma*. Il segmento di firma può essere usato per convalidare l'autenticità del token in modo che possa essere considerato attendibile dall'applicazione. I token Azure AD B2C vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256. 

L'intestazione del token contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Il valore della **alg** attestazione è l'algoritmo utilizzato per firmare il token. Il valore della **kid** attestazione è la chiave pubblica usata per firmare il token. In qualsiasi momento, Azure AD B2C può firmare un token utilizzando uno qualsiasi di un set di coppie di chiavi pubblica / privata. Azure AD B2C ruota periodicamente il set di chiavi. L'applicazione deve essere scritta per gestire automaticamente le modifiche delle chiavi. Una frequenza ragionevole per cercare gli aggiornamenti per le chiavi pubbliche usate da Azure AD B2C è di 24 ore.

Azure AD B2C include un endpoint dei metadati di OpenID Connect. Usa questo endpoint, le applicazioni possono richiedere informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant di Azure AD B2C contiene un documento di metadati JSON per ogni criterio. Il documento metadati è un oggetto JSON che contiene diverse informazioni utili, Contengono i metadati **jwks_uri**, che fornisce la posizione del set di chiavi pubbliche usate per firmare i token. Percorso viene fornito in questo caso, ma del migliore per recuperare il percorso in modo dinamico usando il documento di metadati e l'analisi **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Il documento JSON in questo URL contiene tutte le informazioni sulla chiave pubblica usata in un determinato momento. L'app può usare l'attestazione `kid` nell'intestazione del token JWT per selezionare la chiave pubblica nel documento JSON usata per firmare un determinato token. Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

Il documento di metadati per il `B2C_1_signupsignin1` dei criteri nel `contoso.onmicrosoft.com` tenant si trova in:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Per determinare quale criterio è stato usato per firmare un token (e dove trovare le istruzioni per richiedere i metadati), sono disponibili due opzioni. Prima di tutto, il nome del criterio è incluso nell'attestazione `acr` del token. È possibile analizzare le attestazioni all'esterno del corpo del token JWT decodificando il corpo in base 64 e deserializzando la stringa JSON risultante. Il `acr` attestazione è il nome del criterio usato per emettere il token. L'altra opzione consiste nel codificare i criteri nel valore della `state` parametro quando si esegue la richiesta e poi decodificarlo e per determinare quale criterio è stato usato. Entrambi i metodi sono validi.

La descrizione della convalida della firma non rientra nelle finalità di questo documento. Molte librerie open source sono disponibili che consentono di convalidare un token.

### <a name="validate-claims"></a>Convalidare le attestazioni

Quando le applicazioni o l'API riceve un token ID, deve eseguire anche alcuni controlli nelle attestazioni nel token ID. È necessario verificare le attestazioni seguenti:

- **gruppo di destinatari** -verifica che il token ID fosse destinato a essere passati all'applicazione.
- **non prima** e **ora di scadenza** -verifica che il token ID non sia scaduta.
- **autorità di certificazione** -verifica che il token sia stato rilasciato all'applicazione da Azure AD B2C.
- **parametro nonce** -una strategia per la prevenzione di attacchi di riproduzione dei token.

Per un elenco completo delle convalide che l'applicazione deve eseguire, vedere la [specifiche di OpenID Connect](https://openid.net).  

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [usare i token di accesso](active-directory-b2c-access-tokens.md).

