---
title: Panoramica dei token - Azure Active Directory B2C
description: Informazioni sui token usati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbbd083a6b62733d71c316af95dffaa188b28955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186489"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Panoramica dei token in Azure Active Directory B2COverview of tokens in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) rilascia tipi diversi di token di sicurezza durante l'elaborazione di ogni [flusso di autenticazione](application-types.md). Questo documento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

## <a name="token-types"></a>Tipi di token

Azure AD B2C supporta i [protocolli OAuth 2.0 e OpenID Connect](protocols-overview.md), che utilizzano token per l'autenticazione e l'accesso sicuro alle risorse. Tutti i token usati in Azure AD B2C sono [token Web JSON (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) che contengono asserzioni di informazioni sul portatore e sull'oggetto del token.

Nella comunicazione con Azure AD B2C vengono usati i token seguenti:The following tokens are used in communication with Azure AD B2C:

- *Token ID:* un token JWT che contiene attestazioni che è possibile utilizzare per identificare gli utenti nell'applicazione. Questo token viene inviato in modo sicuro nelle richieste HTTP di comunicazione tra due componenti della stessa applicazione o servizio. Le attestazioni nei token ID possono essere usate in base alle esigenze. Vengono comunemente utilizzati per visualizzare informazioni sull'account o per prendere decisioni di controllo di accesso in un'applicazione. I token ID sono firmati, ma non sono crittografati. Quando l'applicazione o l'API riceve un token ID, deve convalidare la firma per dimostrare che il token è autentico. L'applicazione o l'API deve inoltre convalidare alcune attestazioni nel token per dimostrare che è valido. A seconda dei requisiti dello scenario, le attestazioni convalidate da un'applicazione possono variare, ma l'applicazione deve eseguire alcune convalide delle attestazioni comuni in ogni scenario.
- Token di *accesso:* un token JWT che contiene attestazioni che è possibile utilizzare per identificare le autorizzazioni concesse alle API. I token di accesso sono firmati, ma non sono crittografati. I token di accesso vengono usati per fornire l'accesso alle API e ai server di risorse.  Quando l'API riceve un token di accesso, deve convalidare la firma per dimostrare l'autenticità del token. Per dimostrarne la validità, l'API deve anche convalidare alcune attestazioni del token. A seconda dei requisiti dello scenario, le attestazioni convalidate da un'applicazione possono variare, ma l'applicazione deve eseguire alcune convalide delle attestazioni comuni in ogni scenario.
- Token di aggiornamento: i token di aggiornamento vengono usati per acquisire nuovi token ID e token di accesso in un flusso OAuth 2.0.Refresh *tokens* - Refresh tokens are used to acquire new ID tokens and access tokens in an OAuth 2.0 flow. Forniscono all'applicazione l'accesso a lungo termine alle risorse per conto degli utenti senza richiedere l'interazione con tali utenti. I token di aggiornamento sono opachi per l'applicazione. Vengono rilasciati da Azure AD B2C e possono essere esaminati e interpretati solo da Azure AD B2C. Sono di lunga durata, ma l'applicazione non deve essere scritta con l'aspettativa che un token di aggiornamento durerà per un periodo di tempo specifico. I token di aggiornamento possono essere annullati in qualsiasi momento per vari motivi. L'unico modo per l'applicazione di sapere se un token di aggiornamento è valido consiste nel tentare di riscattarlo effettuando una richiesta di token ad Azure AD B2C. Quando si riscatta un token di aggiornamento per un nuovo token, si riceve un nuovo token di aggiornamento nella risposta del token. Salvare il nuovo token di aggiornamento. Sostituisce il token di aggiornamento utilizzato in precedenza nella richiesta. Questa azione consente di garantire che i token di aggiornamento rimangano validi il più a lungo possibile.

## <a name="endpoints"></a>Endpoint

[Un'applicazione registrata](tutorial-register-applications.md) riceve token e comunica con Azure AD B2C inviando richieste a questi endpoint:A registered application receives tokens and communicates with Azure AD B2C by sending requests to these endpoints:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

I token di sicurezza ricevuti dall'applicazione da Azure `/authorize` AD `/token` B2C possono provenire dagli endpoint o. Quando i token ID `/authorize` vengono acquisiti dall'endpoint, vengono eseguiti utilizzando il [flusso implicito](implicit-flow-single-page-application.md), che viene spesso utilizzato per gli utenti che accedono ad applicazioni Web basate su JavaScript. Quando i token ID `/token` vengono acquisiti dall'endpoint, viene eseguito l'utilizzo del [flusso di codice](openid-connect.md#get-a-token)di autorizzazione , che mantiene il token nascosto al browser.

## <a name="claims"></a>Claims

Azure AD B2C consente un controllo con granularità fine sul contenuto dei token. È possibile configurare [i flussi utente](user-flow-overview.md) e [i criteri personalizzati](custom-policy-overview.md) per inviare determinati set di dati utente nelle attestazioni necessarie per l'applicazione. Queste attestazioni possono includere proprietà standard, ad esempio **displayName** e **emailAddress**. Le applicazioni possono usare queste attestazioni per autenticare in modo sicuro gli utenti e le richieste.

Le attestazioni nei token ID non vengono restituite in un ordine particolare. Le nuove attestazioni possono essere introdotte nei token ID in qualsiasi momento. L'applicazione non deve interrompersi man mano che vengono introdotte nuove attestazioni. È inoltre possibile includere [attributi utente personalizzati](user-flow-custom-attributes.md) nelle attestazioni.

Nella tabella seguente sono elencate le attestazioni che è possibile aspettarsi nei token ID e nei token di accesso rilasciati da Azure AD B2C.

| Nome | Attestazione | Valore di esempio | Descrizione |
| ---- | ----- | ------------- | ----------- |
| Destinatari | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica il destinatario del token. Per Azure AD B2C, il gruppo di destinatari è l'ID applicazione. L'applicazione deve convalidare questo valore e rifiutare il token se non corrisponde. Audience equivale a risorsa. |
| Issuer | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token. Identifica inoltre la directory in cui l'utente è stato autenticato. L'applicazione deve convalidare l'attestazione dell'autorità emittente per assicurarsi che il token provenga dall'endpoint appropriato. |
| Ora di emissione | `iat` | `1438535543` | Ora in cui il token è stato generato, rappresentata dal valore epoch time. |
| Scadenza | `exp` | `1438539443` | Ora in cui il token non è più valido, rappresentata dal valore epoch time. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. |
| Non prima | `nbf` | `1438535543` | L’ora in cui il token diventa valido, rappresentata dal valore epoch time. Questa volta è in genere lo stesso momento in cui è stato emesso il token. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. |
| Versione | `ver` | `1.0` | Versione del token ID, come definito da Azure AD B2C. |
| Hash del codice | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hash del codice incluso in un token ID solo quando il token viene rilasciato con un codice di autorizzazione OAuth 2.0.A code hash included in an ID token only when the token is issued together with an OAuth 2.0 authorization code. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Per ulteriori informazioni su come eseguire questa convalida, vedere la [specifica OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash del token di accesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hash del token di accesso incluso in un token ID solo quando il token viene rilasciato con un token di accesso OAuth 2.0. Può essere usato per convalidare l'autenticità di un token di accesso. Per ulteriori informazioni su come eseguire questa convalida, vedere la [specifica OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Strategia per ridurre gli attacchi di riproduzione dei token. L'applicazione può specificare un nonce `nonce` in una richiesta di autorizzazione utilizzando il parametro query. Il valore fornito nella richiesta viene generato `nonce` senza modifiche solo nell'attestazione di un token ID. Questa attestazione consente all'applicazione di verificare il valore rispetto al valore specificato nella richiesta. L'applicazione deve eseguire questa convalida durante il processo di convalida del token ID. |
| Oggetto | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Entità su cui il token asserisce le informazioni, ad esempio l'utente di un'applicazione. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Per impostazione predefinita, l'attestazione dell'oggetto viene popolata con l'ID oggetto dell'utente nella directory. |
| Riferimento alla classe contesto di autenticazione | `acr` | Non applicabile | Utilizzato solo con criteri meno recenti. |
| Criteri del framework di attendibilità | `tfp` | `b2c_1_signupsignin1` | Nome del criterio utilizzato per acquisire il token ID. |
| Ora di autenticazione | `auth_time` | `1438535543` | Ora in cui un utente ha immesso le credenziali per l'ultima volta, rappresentate in epoca. Non vi è alcuna discriminazione tra tale autenticazione è un nuovo accesso, una sessione Single Sign-On (SSO) o un altro tipo di accesso. È `auth_time` l'ultima volta che l'applicazione (o l'utente) ha avviato un tentativo di autenticazione su Azure AD B2C. Il metodo utilizzato per l'autenticazione non è differenziato. |
| Scope | `scp` | `Read`| Autorizzazioni concesse alla risorsa per un token di accesso. Più autorizzazioni concesse sono separate da uno spazio. |
| Entità autorizzati | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | L'**ID applicazione** dell'applicazione client che ha avviato la richiesta. |

## <a name="configuration"></a>Configurazione

Le proprietà seguenti vengono usate per gestire le durate dei token di sicurezza generate da Azure AD B2C:The following properties are used to [manage lifetimes of security tokens](configure-tokens.md) emitted by Azure AD B2C:

- **Durate dei token di accesso e ID (minuti)**: durata del token di connessione OAuth 2.0 usato per ottenere l'accesso a una risorsa protetta. Il valore predefinito è 60 minuti. Il minimo (incluso) è 5 minuti. Il valore massimo (incluso) è 1440 minuti.

- Durata del token di aggiornamento **(giorni):** periodo di tempo massimo prima del quale un token di aggiornamento può essere usato per acquisire un nuovo accesso o un token ID. Il periodo di tempo riguarda anche l'acquisizione di `offline_access` un nuovo token di aggiornamento se all'applicazione è stato concesso l'ambito. Il valore predefinito è 14 giorni. Il minimo (incluso) è un giorno. Il valore massimo (incluso) è di 90 giorni.

- Durata della **finestra scorrevole del token** di aggiornamento (giorni): allo scadere di questo periodo di tempo, l'utente è costretto a eseguire nuovamente l'autenticazione, indipendentemente dal periodo di validità del token di aggiornamento più recente acquisito dall'applicazione. Questo valore può essere specificato solo se l'opzione è impostata su **Con vincoli**. Deve essere superiore o uguale al valore **Durata del token di aggiornamento (giorni)** . Se l'opzione è impostata su **Senza vincoli**, non sarà possibile specificare un valore specifico. Il valore predefinito è 90 giorni. Il minimo (incluso) è un giorno. Il valore massimo (incluso) è 365 giorni.

I seguenti casi d'uso sono abilitati usando queste proprietà:

- Consentire a un utente di mantenere la connessione a un'applicazione mobile per un periodo illimitato, purché l'utente sia continuamente attivo sull'applicazione. È possibile impostare l'opzione **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)** su **Non vincolato** nel flusso utente di accesso.
- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per i token di accesso.

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password.

## <a name="compatibility"></a>Compatibilità

Le proprietà seguenti vengono utilizzate per gestire la [compatibilità](configure-tokens.md)dei token:The following properties are used to manage token compatibility :

- **Attestazione autorità di certificazione (iss)**: identifica il tenant di Azure AD B2C che ha emesso il token. Il valore predefinito è `https://<domain>/{B2C tenant GUID}/v2.0/`. Il valore `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` di include gli ID sia per il tenant B2C di Azure AD che per il flusso utente usato nella richiesta di token. Se l'applicazione o la libreria richiede che Azure AD B2C sia conforme alla [specifica OpenID Connect Discovery 1.0,](https://openid.net/specs/openid-connect-discovery-1_0.html)usare questo valore.

- **Attestazione soggetto (sub)**: questa proprietà identifica l'entità per cui il token esegue l'asserzione delle informazioni. Il valore predefinito è **ObjectID** `sub` , che popola l'attestazione nel token con l'ID oggetto dell'utente. Il valore **Non supportato** viene fornito solo per compatibilità con le versioni precedenti. Si consiglia di passare a **ObjectID** non appena è possibile.

- **Attestazione che rappresenta l'ID criterio:** questa proprietà identifica il tipo di attestazione in cui viene popolato il nome del criterio utilizzato nella richiesta di token. Il valore predefinito è `tfp`. Il valore `acr` di viene fornito solo per compatibilità con le versioni precedenti.

## <a name="pass-through"></a>Pass-through

Quando inizia un percorso utente, Azure AD B2C riceve un token di accesso da un provider di identità. Azure AD B2C usa tale token per recuperare informazioni sull'utente. Si [abilita un'attestazione nel flusso utente](idp-pass-through-user-flow.md) o si [definisce un'attestazione nei criteri personalizzati](idp-pass-through-custom.md) per passare il token alle applicazioni registrate in Azure AD B2C. L'applicazione deve usare un [flusso utente v2](user-flow-versions.md) per sfruttare i vantaggi del passaggio del token come attestazione.

Azure AD B2C attualmente supporta solo il passaggio di token di accesso del provider di identità OAuth 2.0, tra cui Facebook e Google. Per tutti gli altri provider di identità, l'attestazione viene restituita vuota.

## <a name="validation"></a>Convalida

Per convalidare un token, l'applicazione deve controllare sia la firma che le attestazioni del token. Molte librerie open source sono disponibili per la convalida dei token JWT, a seconda della lingua preferita. È consigliabile esplorare tali opzioni anziché implementare la logica di convalida personalizzata.

### <a name="validate-signature"></a>Convalida firma

Un token JWT contiene tre segmenti, *un'intestazione,* un *corpo*e una *firma*. Il segmento della firma può essere usato per convalidare l'autenticità del token in modo che possa essere considerato attendibile dall'applicazione. I token Azure AD B2C vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256.

L'intestazione del token contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Il valore dell'attestazione **alg** è l'algoritmo utilizzato per firmare il token. Il valore dell'attestazione **kid** è la chiave pubblica utilizzata per firmare il token. In qualsiasi momento, Azure AD B2C può firmare un token usando uno qualsiasi di un set di coppie di chiavi pubblica/privata. Azure AD B2C ruota periodicamente il possibile set di chiavi. L'applicazione deve essere scritta per gestire automaticamente le modifiche chiave. Una frequenza ragionevole per verificare la disponibilità di aggiornamenti per le chiavi pubbliche usate da Azure AD B2C è ogni 24 ore.

Azure AD B2C include un endpoint dei metadati di OpenID Connect. Usando questo endpoint, le applicazioni possono richiedere informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant B2C di Azure AD contiene un documento di metadati JSON per ogni criterio. Il documento metadati è un oggetto JSON che contiene diverse informazioni utili, I metadati contengono **jwks_uri**, che fornisce il percorso del set di chiavi pubbliche utilizzate per firmare i token. Tale percorso viene fornito qui, ma è consigliabile recuperare il percorso in modo dinamico utilizzando il documento di metadati e analizzando **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Il documento JSON in questo URL contiene tutte le informazioni sulla chiave pubblica usata in un determinato momento. L'app può usare l'attestazione `kid` nell'intestazione del token JWT per selezionare la chiave pubblica nel documento JSON usata per firmare un determinato token. Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

Il documento di `B2C_1_signupsignin1` metadati `contoso.onmicrosoft.com` per i criteri nel tenant si trova in:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Per determinare quali criteri sono stati utilizzati per firmare un token (e dove andare per richiedere i metadati), sono disponibili due opzioni. Prima di tutto, il nome del criterio è incluso nell'attestazione `acr` del token. È possibile analizzare le attestazioni all'esterno del corpo del token JWT decodificando il corpo in base 64 e deserializzando la stringa JSON risultante. L'attestazione `acr` è il nome del criterio utilizzato per emettere il token. L'altra opzione consiste nel codificare il `state` criterio nel valore del parametro quando si invia la richiesta e quindi decodificarlo per determinare quale criterio è stato utilizzato. Entrambi i metodi sono validi.

La descrizione della convalida della firma non rientra nelle finalità di questo documento. Sono disponibili molte librerie open source che consentono di convalidare un token.

### <a name="validate-claims"></a>Convalidare le attestazioni

Quando le applicazioni o l'API riceve un token ID, deve anche eseguire diversi controlli sulle attestazioni nel token ID. Le seguenti rivendicazioni devono essere controllate:

- **audience** - Verifica che il token ID fosse destinato a essere assegnato all'applicazione.
- **non prima** e **la data di scadenza** - Verifica che il token ID non sia scaduto.
- **issuer** - verifica che il token sia stato rilasciato all'applicazione da Azure AD B2C.
- **nonce** - Una strategia per l'attenuazione dell'attacco replay dei token.

Per un elenco completo delle convalide che l'applicazione deve eseguire, fare riferimento alla [specifica OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [usare i token di accesso](access-tokens.md).

