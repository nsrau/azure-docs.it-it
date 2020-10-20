---
title: Panoramica dei token - Azure Active Directory B2C
description: Informazioni sui token usati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d7a143f99eca73e0620e24ac5d93141ddb7d99e6
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215961"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Panoramica dei token in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) rilascia tipi diversi di token di sicurezza durante l'elaborazione di ogni [flusso di autenticazione](application-types.md). Questo documento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

## <a name="token-types"></a>Tipi di token

Azure AD B2C supporta i [protocolli OAuth 2.0 e OpenID Connect](protocols-overview.md) e usa i token per l'autenticazione e l'accesso protetto alle risorse. Tutti i token usati in Azure AD B2C sono [token JWT (JSON Web)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) che contengono asserzioni di informazioni relative al bearer token e al soggetto del token.

Per la comunicazione con Azure AD B2C vengono usati i token seguenti:

- *Token ID*: token JWT che contiene attestazioni che è possibile usare per identificare gli utenti nell'applicazione. Questo token viene inviato in modo sicuro in richieste HTTP per la comunicazione tra due componenti della stessa applicazione o dello stesso servizio. Le attestazioni nei token ID possono essere usate in base alle esigenze. Vengono comunemente usate per visualizzare informazioni sull'account o per prendere decisioni relative al controllo di accesso in un'applicazione. I token ID sono firmati, ma non crittografati. Quando l'API o l'applicazione riceve un token ID, deve convalidare la firma per dimostrare l'autenticità del token. Per dimostrarne la validità, l'API o l'applicazione deve anche convalidare alcune delle attestazioni del token. A seconda dei requisiti dello scenario, le attestazioni convalidate da un'applicazione possono variare, ma l'applicazione deve eseguire alcune operazioni comuni di convalida delle attestazioni in ogni scenario.
- *Token di accesso* : token JWT che contiene attestazioni che è possibile usare per l'identificazione delle autorizzazioni concesse alle API. I token di accesso sono firmati, ma non crittografati. Vengono usati per consentire l'accesso alle API e ai server delle risorse.  Quando l'API riceve un token di accesso, deve convalidare la firma per dimostrare l'autenticità del token. Per dimostrarne la validità, l'API deve anche convalidare alcune attestazioni del token. A seconda dei requisiti dello scenario, le attestazioni convalidate da un'applicazione possono variare, ma l'applicazione deve eseguire alcune operazioni comuni di convalida delle attestazioni in ogni scenario.
- *Token di aggiornamento*: vengono usati per acquisire nuovi token ID e token di accesso in un flusso di OAuth 2.0. Consentono all'applicazione di ottenere l'accesso a lungo termine alle risorse per conto degli utenti senza richiedere l'interazione degli utenti. I token di aggiornamento sono opachi per l'applicazione. Vengono rilasciati da Azure AD B2C e possono essere controllati e interpretati solo da Azure AD B2C. Hanno lunga durata, ma l'applicazione non deve essere scritta in base a una durata specifica prevista per il token di aggiornamento. I token di aggiornamento possono essere annullati in qualsiasi momento per vari motivi. L'unico modo per l'applicazione di sapere se un token di aggiornamento è valido è tentare di riscattarlo con una richiesta di token ad Azure AD B2C. Quando si riscattano i token di aggiornamento per un nuovo token, viene visualizzato un nuovo token di aggiornamento nella risposta del token. Salvare il nuovo token di aggiornamento, che sostituisce il token di aggiornamento usato in precedenza nella richiesta. Questa azione consente di garantire che il token di aggiornamento rimanga valido il più a lungo possibile. Si noti che le applicazioni a singola pagina che usano il flusso del codice di autorizzazione con PKCE hanno sempre una durata del token di aggiornamento di 24 ore. [Altre informazioni sulle implicazioni di sicurezza dei token di aggiornamento nel browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="endpoints"></a>Endpoint

Un'[applicazione registrata](tutorial-register-applications.md) riceve i token e comunica con Azure AD B2C inviando le richieste a questi endpoint:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

I token di sicurezza che l'applicazione riceve da Azure AD B2C possono provenire dagli endpoint `/authorize` o `/token`. Quando i token ID vengono acquisiti dall'endpoint `/authorize`, viene usato il [flusso implicito](implicit-flow-single-page-application.md), a cui spesso si ricorre per consentire l'accesso degli utenti alle applicazioni Web basate su JavaScript. Quando invece vengono acquisiti dall'endpoint `/token`, viene usato il [flusso del codice di autorizzazione](openid-connect.md#get-a-token), che mantiene il token nascosto al browser.

## <a name="claims"></a>Claims

Azure AD B2C consente un controllo con granularità fine sul contenuto dei token. È possibile configurare i [flussi utente](user-flow-overview.md) e i [criteri personalizzati](custom-policy-overview.md) per inviare set di dati utente specifici nelle attestazioni richieste per l'applicazione. Le attestazioni possono includere proprietà standard, ad esempio **displayName** e **emailAddress**. Le applicazioni possono usare queste attestazioni per autenticare in modo sicuro gli utenti e le richieste.

Le attestazioni nei token ID non vengono restituite in un ordine particolare. Possono essere introdotte nuove attestazioni nei token ID in qualsiasi momento. L'introduzione delle nuove attestazioni non deve interrompere il funzionamento dell'applicazione. È anche possibile includere [attributi utente personalizzati](user-flow-custom-attributes.md) nelle attestazioni.

Nella tabella seguente sono elencate le attestazioni che è possibile prevedere nei token ID e token di accesso emessi da Azure AD B2C.

| Nome | Attestazione | Valore di esempio | Descrizione |
| ---- | ----- | ------------- | ----------- |
| Destinatari | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica il destinatario del token. Per Azure AD B2C, il destinatario è l'ID applicazione. L'applicazione deve convalidare questo valore e rifiutare il token, se il valore non corrisponde. Audience equivale a risorsa. |
| Issuer | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token. Identifica inoltre la directory in cui è stato autenticato l'utente. L'applicazione deve convalidare l'attestazione dell'autorità di certificazione per assicurarsi che il token provenga dall'endpoint appropriato. |
| Ora di emissione | `iat` | `1438535543` | Ora in cui il token è stato generato, rappresentata dal valore epoch time. |
| Scadenza | `exp` | `1438539443` | Ora in cui il token non è più valido, rappresentata dal valore epoch time. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. |
| Non prima | `nbf` | `1438535543` | L’ora in cui il token diventa valido, rappresentata dal valore epoch time. Equivale in genere all'ora di rilascio del token. L'applicazione deve usare questa attestazione per verificare la validità della durata del token. |
| Versione | `ver` | `1.0` | Versione del token ID, come definito da Azure AD B2C. |
| Hash del codice | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | L'hash del codice è incluso in un token ID solo quando quest'ultimo viene rilasciato insieme a un codice di autorizzazione di OAuth 2.0. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Per altre informazioni su come eseguire la convalida, vedere la [specifica di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash del token di accesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | L'hash del token di accesso è incluso in un token ID solo quando quest'ultimo viene rilasciato insieme a un token di accesso di OAuth 2.0. Può essere usato per convalidare l'autenticità di un token di accesso. Per altre informazioni su come eseguire la convalida, vedere la [specifica di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Strategia per ridurre gli attacchi di riproduzione dei token. L'applicazione può specificare un'attestazione Nonce in una richiesta di autorizzazione usando il parametro di query `nonce`. Il valore specificato nella richiesta viene rilasciato senza modifica nell'attestazione `nonce` solo di un token ID. Questa attestazione consente all'applicazione di verificare il valore rispetto al valore specificato nella richiesta. L'applicazione deve eseguire la convalida durante il processo di convalida del token ID. |
| Oggetto | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Entità su cui il token asserisce informazioni, ad esempio l'utente di un'applicazione. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Per impostazione predefinita, l'attestazione dell'oggetto viene popolata con l'ID oggetto dell'utente nella directory. |
| Riferimento alla classe contesto di autenticazione | `acr` | Non applicabile | Usato solo con i criteri meno recenti. |
| Criteri del framework di attendibilità | `tfp` | `b2c_1_signupsignin1` | Nome del criterio usato per acquisire il token ID. |
| Ora di autenticazione | `auth_time` | `1438535543` | Ora in cui l'utente ha immesso le credenziali l'ultima volta, rappresentata come valore epoch time. Non esiste alcuna distinzione tra l'autenticazione che rappresenta un accesso appena creato, una sessione di Single Sign-On (SSO) o un altro tipo di accesso. `auth_time` rappresenta l'ultima volta che l'applicazione (o l'utente) ha avviato un tentativo di autenticazione per Azure AD B2C. Il metodo usato per l'autenticazione non è differenziato. |
| Scope | `scp` | `Read`| Le autorizzazioni concesse alla risorsa per un token di accesso. Le autorizzazioni multiple concesse sono separate da uno spazio. |
| Entità autorizzati | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | L'**ID applicazione** dell'applicazione client che ha avviato la richiesta. |

## <a name="configuration"></a>Configurazione

Le proprietà seguenti vengono usate per [gestire le durate dei token di sicurezza](configure-tokens.md) emessi da Azure AD B2C:

- **Durate dei token di accesso e ID (minuti)** : durata del token di connessione OAuth 2.0 usato per ottenere l'accesso a una risorsa protetta. Il valore predefinito è 60 minuti. Il valore minimo (inclusivo) è 5 minuti. Il valore massimo (inclusivo) è 1440 minuti.

- **Durata del token di aggiornamento (giorni)** : periodo di tempo massimo prima che sia possibile usare un token di aggiornamento per acquisire un nuovo token di accesso o ID. Il periodo di tempo riguarda anche l'acquisizione di un nuovo token di aggiornamento se all'applicazione è stato concesso l'ambito `offline_access`. Il valore predefinito è 14 giorni. Il valore minimo (inclusivo) è un giorno. Il valore massimo (inclusivo) è 90 giorni.

- **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)** : allo scadere di questo periodo, l'utente dovrà eseguire di nuovo l'autenticazione, indipendentemente dal periodo di validità del token di accesso più recente acquisito dall'applicazione. Questo valore può essere specificato solo se l'opzione è impostata su **Con vincoli**. Deve essere superiore o uguale al valore **Durata del token di aggiornamento (giorni)** . Se l'opzione è impostata su **Senza vincoli**, non sarà possibile specificare un valore specifico. Il valore predefinito è 90 giorni. Il valore minimo (inclusivo) è un giorno. Il valore massimo (inclusivo) è 365 giorni.

I seguenti casi d'uso sono abilitati usando queste proprietà:

- Consentire a un utente di mantenere la connessione a un'applicazione mobile per un periodo illimitato, purché l'utente sia continuamente attivo sull'applicazione. È possibile impostare l'opzione **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)** su **Non vincolato** nel flusso utente di accesso.
- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per i token di accesso.

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password.

## <a name="compatibility"></a>Compatibilità

Per [gestire la compatibilità dei token](configure-tokens.md) vengono usate le proprietà seguenti:

- **Attestazione autorità di certificazione (iss)** : identifica il tenant di Azure AD B2C che ha emesso il token. Il valore predefinito è `https://<domain>/{B2C tenant GUID}/v2.0/`. Il valore `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` include gli ID sia per il tenant Azure AD B2C, sia per il flusso utente usato nella richiesta di token. Usare questo valore se l'applicazione o la libreria richiede Azure AD B2C per essere conforme alla [specifica OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html).

- **Attestazione soggetto (sub)** : questa proprietà identifica l'entità per cui il token esegue l'asserzione delle informazioni. Il valore predefinito è **ObjectID**, che popola l'attestazione `sub` nel token con l'ID oggetto dell'utente. Il valore **Non supportato** viene specificato solo per la compatibilità con le versioni precedenti. Si consiglia di passare a **ObjectID** non appena è possibile.

- **Attestazione che rappresenta l'ID criteri**: questa proprietà identifica il tipo di attestazione in cui viene popolato il nome criterio usato nella richiesta di token. Il valore predefinito è `tfp`. Il valore `acr` viene specificato solo per la compatibilità con le versioni precedenti.

## <a name="pass-through"></a>Pass-through

Quando viene avviato un percorso utente, Azure AD B2C riceve un token di accesso dal provider di identità. Azure AD B2C usa tale token per recuperare informazioni sull'utente. Si [attiva un'attestazione nel flusso utente](idp-pass-through-user-flow.md) o si [definisce un'attestazione nel criterio personalizzato](idp-pass-through-custom.md) per passare il token alle applicazioni registrate in Azure AD B2C. L'applicazione deve usare un [flusso utente consigliato](user-flow-versions.md) per sfruttare il passaggio del token come attestazione.

Azure AD B2C attualmente supporta solo il passaggio di token di accesso dei provider di identità OAuth 2.0, tra cui Facebook e Google. Per tutti gli altri provider di identità, l'attestazione viene restituita vuota.

## <a name="validation"></a>Convalida

Per convalidare un token, l'applicazione deve verificare sia la firma che le attestazioni del token stesso. Sono disponibili molte librerie open source per la convalida dei token JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame tali opzioni anziché implementare una logica di convalida personalizzata.

### <a name="validate-signature"></a>Convalidare la firma

Un token JWT contiene tre segmenti, un'*intestazione*, un *corpo* e una  *firma*. Il segmento di firma può essere usato per convalidare l'autenticità del token, in modo che possa essere considerato attendibile dall'applicazione. I token Azure AD B2C vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256.

L'intestazione del token contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Il valore dell'attestazione **alg** è l'algoritmo usato per firmare il token. Il valore dell'attestazione **kid** è la chiave pubblica usata per firmare il token. In qualsiasi momento Azure AD B2C può firmare un token usando un set di coppie di chiavi pubblica/privata. Azure AD B2C ruota periodicamente il set di chiavi possibile. L'applicazione deve essere scritta in modo da gestire automaticamente le modifiche delle chiavi. Una frequenza ragionevole per la ricerca di aggiornamenti per le chiavi pubbliche usate da Azure AD B2C è circa 24 ore. Per gestire le modifiche impreviste della chiave, l'applicazione deve essere scritta per recuperare nuovamente le chiavi pubbliche se riceve un valore **figlio** imprevisto.

Azure AD B2C include un endpoint dei metadati di OpenID Connect. Usando questo endpoint, le applicazioni possono richiedere informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant Azure AD B2C contiene un documento di metadati JSON per ogni criterio. Il documento metadati è un oggetto JSON che contiene diverse informazioni utili, I metadati contengono **jwks_uri**, che specifica la posizione del set di chiavi pubbliche usate per firmare i token. Tale posizione è indicata di seguito, ma è consigliabile recuperarla in modo dinamico usando il documento di metadati e analizzando **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
Il documento JSON in questo URL contiene tutte le informazioni sulla chiave pubblica usata in un determinato momento. L'app può usare l'attestazione `kid` nell'intestazione del token JWT per selezionare la chiave pubblica nel documento JSON usata per firmare un determinato token. Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

Il documento di metadati per il criterio `B2C_1_signupsignin1` nel tenant `contoso.onmicrosoft.com` si trova in:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

Per determinare quale criterio è stato usato per la firma di un token e la posizione da cui richiedere i metadati, sono disponibili due opzioni. Prima di tutto, il nome del criterio è incluso nell'attestazione `acr` del token. È possibile analizzare le attestazioni all'esterno del corpo del token JWT decodificando il corpo in base 64 e deserializzando la stringa JSON risultante. L'attestazione `acr` è il nome del criterio usato per rilasciare il token. L'altra opzione consiste nel codificare il criterio nel valore del parametro `state` quando si rilascia la richiesta, per poi decodificarlo e determinare quale criterio è stato usato. Entrambi i metodi sono validi.

La descrizione della convalida della firma non rientra nelle finalità di questo documento. Per convalidare un token, è possibile consultare le varie librerie open source disponibili.

### <a name="validate-claims"></a>Convalidare le attestazioni

Quando le applicazioni o l'API ricevono un token ID, devono eseguire anche diversi controlli in base alle attestazioni nel token ID. È necessario controllare le attestazioni seguenti:

- **audience**: verifica che il token ID fosse destinato all'applicazione.
- **not before** ed **expiration time**: verificano che il token ID non sia scaduto.
- **issuer**: verifica che il token sia stato rilasciato all'applicazione da Azure AD B2C.
- **nonce**: strategia per la mitigazione dei rischi di attacchi di riproduzione dei token.

Per un elenco completo delle convalide che l'applicazione deve eseguire, vedere le [specifiche di OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [usare i token di accesso](access-tokens.md).

