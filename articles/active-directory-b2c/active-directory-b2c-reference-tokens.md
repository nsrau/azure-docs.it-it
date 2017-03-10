---
title: Azure Active Directory B2C | Documentazione Microsoft
description: Tipi di token rilasciati in Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 0ae9ad40f2e32d56fd50c90b86339cbb458d7291
ms.openlocfilehash: a3276c764ebb6382594cf7002e7c7e8e328862ef
ms.lasthandoff: 03/09/2017


---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: informazioni di riferimento sui token
Azure Active Directory (Azure AD) B2C rilascia tipi diversi di token di sicurezza durante l'elaborazione di ogni [flusso di autenticazione](active-directory-b2c-apps.md). Questo documento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

## <a name="types-of-tokens"></a>Tipi di token
Azure AD B2C supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-b2c-reference-protocols.md), che usa sia token di accesso che token di aggiornamento. L'endpoint supporta inoltre l'autenticazione e l'accesso tramite [OpenID Connect](active-directory-b2c-reference-protocols.md), che introduce un terzo tipo di token: il token ID. Ognuno di questi token viene rappresentato come token di connessione.

Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. Per "portatore" si intende qualsiasi parte che possa presentare il token. Per poter ricevere un token di connessione, Azure AD deve prima autenticare una parte. Se non vengono adottate le misure necessarie per proteggere il token durante la trasmissione e l'archiviazione, potrebbe essere intercettato e usato da parti non autorizzate. Alcuni token di sicurezza hanno un meccanismo predefinito che ne impedisce l'uso da parti non autorizzate, ma i token di connessione non presentano questo meccanismo. Devono essere trasportati usando un canale protetto, ad esempio il protocollo Transport Layer Security (HTTPS).

Se un token di connessione viene trasmesso al di fuori di un canale protetto, un utente malintenzionato potrebbe usare un attacco man-in-the-middle per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando i token di connessione vengono archiviati o memorizzati nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro.

Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

Molti dei token rilasciati da Azure AD B2C vengono implementati come token Web JSON (JWT). Un token JWT è un modo compatto e indipendente dall'URL di trasferimento delle informazioni tra due parti. I token JWT contengono informazioni note come attestazioni. Si tratta di asserzioni di informazioni relative alla connessione e all'oggetto del token. Le attestazioni nei token JWT sono oggetti JSON codificati e serializzati per la trasmissione. I token JWT rilasciati da Azure AD B2C sono firmati, ma non crittografati, è quindi possibile esaminarne facilmente i contenuti per il debug. Sono disponibili diversi strumenti adatti a questo scopo, ad esempio [calebb.net](http://calebb.net). Per altre informazioni sui token JWT, vedere le [specifiche dei token JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Token ID
I token ID sono un tipo di token di sicurezza che l'app riceve dagli endpoint `authorize` e `token` di Azure AD B2C. Vengono rappresentati come token [JWT](#types-of-tokens)e contengono attestazioni che è possibile usare per l'identificazione degli utenti nell'app. Quando vengono acquisiti dall'endpoint `authorize` , i token ID vengono spesso usati per l'accesso degli utenti alle applicazioni Web. Quando vengono acquisiti dall'endpoint `token` , i token ID possono essere inviati in richieste HTTP durante la comunicazione tra due componenti della stessa applicazione o dello stesso servizio. Le attestazioni nei token ID possono essere usate in base alle esigenze. Vengono comunemente usate per visualizzare informazioni sull'account o per prendere decisioni relative al controllo di accesso in un'app.  

Al momento i token ID sono firmati, ma non crittografati. Quando l'API o l'app riceve un token ID, deve [convalidare la firma](#token-validation) per dimostrare l'autenticità del token. Per dimostrarne la validità, l'API o l'app deve anche convalidare alcune delle attestazioni del token. A seconda dei requisiti dello scenario, le attestazioni convalidate da un'app possono variare, ma l'app deve eseguire alcune [operazioni comuni di convalida delle attestazioni](#token-validation) in ogni scenario.

#### <a name="sample-id-token"></a>Token ID di esempio
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Token di accesso
Un token di accesso è anche un tipo di token di sicurezza che l'app riceve dagli endpoint `authorize` e `token` di Azure AD B2C. I token di accesso vengono rappresentati come token [JWT](#types-of-tokens) e contengono attestazioni che è possibile usare per l'identificazione degli utenti nell'app.

Al momento i token di accesso sono firmati, ma non crittografati e sono molto simili ai token ID.  I token di accesso devono essere usati per fornire l'accesso alle API e ai servizi Web e per identificare e autenticare l'utente in tali servizi.  Tuttavia, non forniscono alcuna asserzione di autorizzazione in tali servizi.  Vale a dire che l'attestazione `scp` nei token di accesso non limita né rappresenta l'accesso che è stato concesso all'oggetto del token.

Quando l'API riceve un token di accesso, deve [convalidare la firma](#token-validation) per dimostrare l'autenticità del token. Per dimostrarne la validità, l'API deve anche convalidare alcune attestazioni del token. A seconda dei requisiti dello scenario, le attestazioni convalidate da un'app possono variare, ma l'app deve eseguire alcune [operazioni comuni di convalida delle attestazioni](#token-validation) in ogni scenario.

### <a name="claims-in-id--access-tokens"></a>Attestazioni nei token di accesso e ID
Azure AD B2C consente un controllo con granularità fine sul contenuto dei token. È possibile configurare [criteri](active-directory-b2c-reference-policies.md) per inviare un set di dati utente specifici in attestazioni necessarie per il funzionamento dell'app. Le attestazioni possono includere proprietà standard, ad esempio le proprietà `displayName` e `emailAddress` relative all'utente. Possono includere anche [attributi utente personalizzati](active-directory-b2c-reference-custom-attr.md) che è possibile definire nella directory B2C. Ogni token ID e di accesso ricevuto contiene un determinato set di attestazioni relative alla sicurezza. Le applicazioni possono usare queste attestazioni per autenticare in modo sicuro gli utenti e le richieste.

Si noti che le attestazioni nei token ID non vengono restituite in un ordine particolare. Si noti anche che possono essere introdotte nuove attestazioni nei token ID in qualsiasi momento. L'introduzione delle nuove attestazioni non deve interrompere il funzionamento dell'app. Di seguito è riportato un elenco delle attestazioni previste in ogni token ID e di accesso rilasciato da Azure AD B2C. Eventuali attestazioni aggiuntive saranno determinate dai criteri. A scopo di esercitazione, provare a verificare le attestazioni nel token ID di esempio incollandole in [calebb.net](http://calebb.net). Per informazioni più dettagliate, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

| Nome | Attestazione | Valore di esempio | Descrizione |
| --- | --- | --- | --- |
| Audience |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Identifica il destinatario del token. Per Azure AD B2C il destinatario è l'ID applicazione assegnato all'app nel portale di registrazione delle app. L'app deve convalidare questo valore e rifiutare il token, se il valore non corrisponde. |
| Issuer |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token e identifica la directory di Azure AD in cui è stato autenticato l'utente. L'app deve convalidare l'attestazione Autorità di certificazione per assicurarsi che il token sia stato fornito dall'endpoint&2;.0. |
| Ora di emissione |`iat` |`1438535543` |Indica l'ora in cui il token è stato rilasciato, rappresentata come valore epoch time. |
| Scadenza |`exp` |`1438539443` |Indica l'ora di scadenza del token, rappresentata come valore epoch time. L'app deve usare questa attestazione per verificare la validità della durata del token. |
| Non prima |`nbf` |`1438535543` |Indica l'ora di inizio della validità del token, rappresentata come valore epoch time. Equivale in genere all'ora di rilascio del token. L'app deve usare questa attestazione per verificare la validità della durata del token. |
| Version |`ver` |`1.0` |Versione del token ID, definita da Azure AD. |
| Hash del codice |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |L'hash del codice è incluso in un token ID solo quando quest'ultimo viene rilasciato insieme a un codice di autorizzazione di OAuth 2.0. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) . |
| Hash del token di accesso |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |L'hash del token di accesso è incluso in un token ID solo quando quest'ultimo viene rilasciato insieme a un token di accesso di OAuth 2.0. Può essere usato per convalidare l'autenticità di un token di accesso. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) . |
| Nonce |`nonce` |`12345` |Strategia per ridurre gli attacchi di riproduzione dei token. L'app può specificare un'attestazione Nonce in una richiesta di autorizzazione usando il parametro di query `nonce` . Il valore specificato nella richiesta verrà rilasciato senza modifica nell'attestazione `nonce` solo di un token ID. In questo modo l'app può verificare il valore rispetto al valore specificato nella richiesta che associa la sessione dell'app a un determinato token ID. L'app deve eseguire la convalida durante il processo di convalida del token ID. |
| Oggetto |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Indica l'entità su cui il token rilascia informazioni, ad esempio l'utente di un'app. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Per impostazione predefinita, l'attestazione dell'oggetto viene popolata con l'ID oggetto dell'utente nella directory. Per altre informazioni, vedere [questo articolo](active-directory-b2c-token-session-sso.md). |
| Riferimento alla classe contesto di autenticazione |`acr` |Non applicabile |Attualmente non in uso, tranne nel caso di criteri meno recenti. Per altre informazioni, vedere [questo articolo](active-directory-b2c-token-session-sso.md). |
| TrustFrameworkPolicy |`tfp` |`b2c_1_sign_in` |Nome del criterio usato per acquisire il token ID. |
| Ora di autenticazione |`auth_time` |`1438535543` |Ora in cui l'utente ha immesso le credenziali l'ultima volta, rappresentata come valore epoch time. |

### <a name="refresh-tokens"></a>Token di aggiornamento
I token di aggiornamento sono token di sicurezza che l'app può usare per acquisire nuovi token ID e token di accesso in un flusso di OAuth 2.0. Consentono all'app di ottenere l'accesso a lungo termine alle risorse per conto degli utenti senza richiedere l'interazione degli utenti.

Per ricevere un token di aggiornamento in una risposta del token, l'app deve richiedere l'ambito `offline_acesss` . Per ulteriori informazioni sull’ambito `offline_access` , fare riferimento al [riferimento al protocollo di Azure AD B2C](active-directory-b2c-reference-protocols.md).

I token di aggiornamento sono e saranno sempre, completamente opachi per l'app. Vengono rilasciati da Azure AD e possono essere controllati e interpretati solo da Azure AD. Hanno lunga durata, ma l'app non deve essere scritta in base a una durata specifica prevista per il token di aggiornamento. I token di aggiornamento possono essere annullati in qualsiasi momento per vari motivi. L'unico modo per l'app di sapere se un token di aggiornamento è valido è tentare di riscattarlo mediante una richiesta di token al Azure AD.

Quando si riscatta un token di aggiornamento per un nuovo token, se all'app è stata concessa l'autorizzazione per l'ambito `offline_access`, si riceve un nuovo token di aggiornamento nella risposta del token. È consigliabile salvare il token di aggiornamento appena rilasciato, che deve sostituire il token di aggiornamento usato in precedenza nella richiesta. In questo modo, il token di aggiornamento rimarrà valido il più a lungo possibile.

## <a name="token-validation"></a>Convalida dei token
Per convalidare un token, l'app deve verificare sia la firma che le attestazioni del token stesso.

Sono disponibili molte librerie open source per la convalida dei token JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame tali opzioni anziché implementare una logica di convalida personalizzata. Le informazioni contenute in questa guida permettono di imparare a usare correttamente tali librerie.

### <a name="validate-the-signature"></a>convalidare la firma
Un token JWT contiene tre segmenti separati dal carattere `.` . Il primo segmento costituisce l'**intestazione**, il secondo è il **corpo**, il terzo la **firma**. Il segmento di firma può essere usato per convalidare l'autenticità del token, in modo che possa essere considerato attendibile dall'app.

I token Azure AD B2C vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256. L'intestazione del token contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

L'attestazione `alg` indica l'algoritmo usato per firmare il token. L'attestazione `kid` indica la chiave pubblica specifica usata per firmare il token.

In qualsiasi momento Azure AD può firmare un token usando un determinato set di coppie di chiavi pubblica/privata. Azure AD ruota il set di chiavi su base periodica, quindi l'app deve essere scritta in modo da gestire automaticamente le modifiche delle chiavi. Una frequenza ragionevole per la ricerca di aggiornamenti per le chiavi pubbliche usate da Azure AD è di circa 24 ore.

Azure AD B2C include un endpoint dei metadati di OpenID Connect. Questo consente alle app di recuperare informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. La directory B2C contiene un documento metadati JSON per ogni criterio. Il documento di metadati del criterio `b2c_1_sign_in` in `fabrikamb2c.onmicrosoft.com` si trova ad esempio in:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` è la directory B2C usata per autenticare l'utente e `b2c_1_sign_in` sono i criteri usati per acquisire il token. Per determinare quale criterio è stato usato per la firma di un token e la posizione da cui recuperare i metadati, sono disponibili due opzioni. Prima di tutto, il nome del criterio è incluso nell'attestazione `acr` del token. È possibile analizzare le attestazioni all'esterno del corpo del token JWT decodificando il corpo in base&64; e deserializzando la stringa JSON risultante. L'attestazione `acr` è il nome del criterio usato per rilasciare il token.  L'altra opzione consiste nel codificare i criteri nel valore del parametro `state` quando si rilascia la richiesta, per poi decodificarlo e determinare quali criteri sono stati utilizzati. Entrambi i metodi sono validi.

Il documento metadati è un oggetto JSON che contiene diverse informazioni utili, come ad esempio il percorso degli endpoint necessari per eseguire l'autenticazione OpenID Connect. Include anche un oggetto `jwks_uri` che fornisce la posizione del set di chiavi pubbliche usate per firmare i token. Tale posizione è indicata di seguito, ma è consigliabile recuperarla in modo dinamico usando il documento di metadati e analizzando l'oggetto `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

Il documento JSON in questo URL contiene tutte le informazioni sulla chiave pubblica usata in un determinato momento. L'app può usare l'attestazione `kid` nell'intestazione del token JWT per selezionare la chiave pubblica nel documento JSON usata per firmare un determinato token. Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

La descrizione della convalida della firma non rientra nelle finalità di questo documento. A tale scopo è possibile consultare le varie librerie open source disponibili.

### <a name="validate-the-claims"></a>Convalidare le attestazioni
Quando l'app o l'API riceve un token ID, deve eseguire anche diversi controlli in base alle attestazioni nel token ID. Sono incluse, ad esempio:

* Attestazione **Destinatari** : verifica che il token ID fosse destinato all'app.
* Attestazioni **not before** (Non prima) e **expiration time** (Scadenza): verificano che il token ID non sia scaduto.
* Attestazione **Autorità di certificazione** : verifica che il token sia stato rilasciato all'app da Azure AD.
* Attestazione **Nonce**: strategia per ridurre gli attacchi di riproduzione dei token.

Per un elenco completo delle convalide che l'app deve eseguire, vedere le [specifiche di OpenID Connect](https://openid.net). Per informazioni dettagliate sui valori previsti per tali attestazioni, vedere la sezione [Token](#types-of-tokens)precedente.  

## <a name="token-lifetimes"></a>Durata dei token
A scopo di approfondimento viene riportata di seguito la durata dei vari token. Queste informazioni possono risultare utili durante lo sviluppo e il debug delle app. Si noti che le app non devono essere scritte in base a una durata specifica prevista come costante. La durata dei token può variare.  Altre informazioni sulla personalizzazione della durata dei token in Azure AD B2C, sono disponibili [qui](active-directory-b2c-token-session-sso.md).

| Token | Durata | Descrizione |
| --- | --- | --- |
| Token ID |Un'ora |I token ID sono in genere validi per un'ora. L'app Web può usare tale durata per mantenere le relative sessioni con gli utenti (scelta consigliati). È anche possibile scegliere una durata di sessione diversa. Se l'app deve ottenere un nuovo token ID, deve semplicemente inviare una nuova richiesta di accesso ad Azure AD. Se l'utente ha una sessione del browser valida con Azure AD, può non essere necessario immettere nuovamente le credenziali. |
| Token di aggiornamento |Fino a 14 giorni |Un singolo token di aggiornamento è valido per un periodo massimo di 14 giorni. Tuttavia, un token di aggiornamento potrebbe non essere più valido in qualsiasi momento per diversi motivi. L'app deve continuare a provare a usare un token di aggiornamento fino a quando la richiesta non ha esito negativo o l'app non sostituisce il token di aggiornamento con uno nuovo.  Se sono trascorsi 90 giorni dall'ultima volta in cui l'utente ha immesso le credenziali, un token di aggiornamento non è più valido. |
| Codici di autorizzazione |Cinque minuti |I codici di autorizzazione hanno intenzionalmente una breve durata. Alla ricezione devono essere riscattati immediatamente con token di accesso, token ID o token di aggiornamento. |


