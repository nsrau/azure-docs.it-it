---
title: Accesso Web con OpenID Connect - Azure Active Directory B2C
description: Compilare applicazioni Web usando il protocollo di autenticazione OpenID Connect in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6640ab1660e6499a97a8c990a0001d5fbae4e997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264388"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Accesso Web con OpenID Connect in Azure Active Directory B2C

OpenID Connect è un protocollo di autenticazione basato su OAuth 2.0 che può essere usato per consentire agli utenti di accedere in modo sicuro alle applicazioni Web. Tramite l'implementazione in Azure Active Directory B2C (Azure AD B2C) di OpenID Connect è possibile assegnare esperienze di gestione delle iscrizioni, degli accessi e altre esperienze di gestione delle identità nelle applicazioni Web ad Azure AD. Questa guida illustra come eseguire questa operazione in modo indipendente dal linguaggio. La guida descrive come inviare e ricevere messaggi HTTP senza usare una delle librerie Microsoft open source.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 per consentirne l'uso come protocollo di *autenticazione*. Questo protocollo di autenticazione consente di eseguire l'accesso Single Sign-On. Introduce il concetto di *token ID*, che consente al client di verificare l'identità dell'utente e ottenere informazioni di base sul profilo dell'utente.

Poiché estende OAuth 2.0, consente inoltre alle applicazioni di acquisire in modo sicuro i token di *accesso.* I token di accesso possono essere usati per accedere alle risorse protette tramite un [server di autorizzazione](protocols-overview.md). OpenID Connect è consigliato se si sta creando un'applicazione Web ospitata in un server e accessibile tramite un browser. Per altre informazioni sui token, vedere [panoramica dei token in Azure Active Directory B2CFor](tokens-overview.md) more information about tokens, see the Overview of tokens in Azure Active Directory B2C

Azure AD B2C estende il protocollo standard OpenID Connect per non limitarsi esclusivamente a semplici operazioni di autenticazione e autorizzazione. Viene introdotto il parametro del [flusso utente,](user-flow-overview.md)che consente di utilizzare OpenID Connect per aggiungere esperienze utente all'applicazione, ad esempio l'iscrizione, l'accesso e la gestione dei profili.

## <a name="send-authentication-requests"></a>Invio di richieste di autenticazione

Quando l'applicazione Web deve autenticare l'utente ed eseguire un `/authorize` flusso utente, può indirizzare l'utente all'endpoint. L'utente esegue un'azione a seconda del flusso dell'utente.

In questa richiesta, il client indica le autorizzazioni che deve `scope` acquisire dall'utente nel parametro e specifica il flusso utente da eseguire. Per avere un'idea del funzionamento della richiesta, provare a incollare la richiesta in un browser ed estrarla. Sostituire `{tenant}` con il nome del tenant. Sostituire `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` con l'ID app dell'applicazione registrata in precedenza nel tenant. Modificare anche il`{policy}`nome del criterio ( ) con il `b2c_1_sign_in`nome del criterio presente nel tenant, ad esempio .

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametro | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| "tenant" | Sì | Nome del tenant B2C di Azure AD |
| "Criteri" | Sì | Flusso utente da eseguire. Specificare il nome di un flusso utente creato nel tenant B2C di Azure AD. Ad `b2c_1_sign_in`esempio: `b2c_1_sign_up`, `b2c_1_edit_profile`, o . |
| client_id | Sì | ID applicazione assegnato dal portale di [Azure](https://portal.azure.com/) all'applicazione. |
| nonce | Sì | Valore incluso nella richiesta (generata dall'applicazione) inclusa nel token ID risultante come attestazione. L'applicazione può quindi verificare questo valore per ridurre gli attacchi di riproduzione dei token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| response_type | Sì | Deve includere un token ID per OpenID Connect. Se l'applicazione Web necessita anche di token `code+id_token`per chiamare un'API Web, è possibile utilizzare . |
| scope | Sì | Elenco di ambiti separati da spazi. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. L'ambito `offline_access` è facoltativo per le applicazioni Web. Indica che l'applicazione avrà bisogno di un token di *aggiornamento* per l'accesso esteso alle risorse. |
| prompt | No | Tipo di interazione utente obbligatoria. L'unico valore valido in questa fase è `login`, che impone all'utente di immettere le credenziali per la richiesta. |
| redirect_uri | No | Parametro `redirect_uri` dell'applicazione, in cui le risposte di autenticazione possono essere inviate e ricevute dall'applicazione. Deve corrispondere esattamente a `redirect_uri` uno dei parametri registrati nel portale di Azure, ad eccezione del fatto che deve essere codificato in URL. |
| response_mode | No | Metodo utilizzato per inviare il codice di autorizzazione risultante all'applicazione. Può essere `query`, `form_post` o `fragment`.  `form_post` è la modalità di risposta consigliata perché offre la sicurezza migliore. |
| state | No | Valore incluso nella richiesta restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene utilizzato anche per codificare le informazioni sullo stato dell'utente nell'applicazione prima che si verificasse la richiesta di autenticazione, ad esempio la pagina in cui si trovavano. |

A questo punto, all'utente viene chiesto di completare il flusso di lavoro. L'utente potrebbe dover immettere il nome utente e la password, accedere con un'identità di social networking o iscriversi alla directory. Potrebbe essere presente qualsiasi altro numero di passaggi a seconda di come è definito il flusso utente.

Dopo che l'utente ha completato il flusso utente, viene `redirect_uri` restituita una risposta all'applicazione in `response_mode` corrispondenza del parametro indicato, utilizzando il metodo specificato nel parametro. La risposta è la stessa per ognuno dei casi precedenti, indipendentemente dal flusso utente.

Una risposta con esito positivo che utilizza `response_mode=fragment` ha un aspetto simile al seguente:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| --------- | ----------- |
| id_token | Token ID richiesto dall'applicazione. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| codice | Codice di autorizzazione richiesto dall'applicazione, se utilizzato `response_type=code+id_token`. L'applicazione può usare il codice di autorizzazione per richiedere un token di accesso per una risorsa di destinazione. I codici di autorizzazione scadono in genere dopo circa 10 minuti. |
| state | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'applicazione deve `state` verificare che i valori nella richiesta e nella risposta siano identici. |

Le risposte di errore possono `redirect_uri` anche essere inviate al parametro in modo che l'applicazione possa gestirle in modo appropriato:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| --------- | ----------- |
| error | Codice che può essere utilizzato per classificare i tipi di errori che si verificano. |
| error_description | Un messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| state | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'applicazione deve `state` verificare che i valori nella richiesta e nella risposta siano identici. |

## <a name="validate-the-id-token"></a>Convalidare il token ID

La ricezione di un token ID non è sufficiente per autenticare l'utente. Convalidare la firma del token ID e verificare le attestazioni nel token in base ai requisiti dell'applicazione. Azure AD B2C usa i [token Web JSON](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità. Sono disponibili molte librerie open source per la convalida dei token JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame tali opzioni anziché implementare una logica di convalida personalizzata.

Azure AD B2C include un endpoint di metadati OpenID Connect, che consente a un'applicazione di ottenere informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant B2C include un documento di metadati JSON per ogni flusso utente. Ad esempio, il documento di metadati del flusso utente `b2c_1_sign_in` in `fabrikamb2c.onmicrosoft.com` si trova in:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Una delle proprietà del documento di configurazione è `jwks_uri`, il cui valore per lo stesso flusso utente è:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Per determinare quale flusso utente è stato utilizzato nella firma di un token ID (e da dove ottenere i metadati), sono disponibili due opzioni. Innanzitutto, il nome del flusso utente è incluso nell'attestazione `acr` del token ID. In secondo luogo, è possibile codificare il flusso utente nel valore del parametro `state` quando si rilascia la richiesta, per poi decodificarlo e determinare quale flusso utente è stato usato. Entrambi i metodi sono validi.

Dopo aver acquisito il documento di metadati dall'endpoint dei metadati OpenID Connect, è possibile usare le chiavi pubbliche RSA 256 per convalidare la firma del token ID. In questo endpoint potrebbero essere elencate più `kid` chiavi, ognuna identificata da un'attestazione. L'intestazione del token ID contiene anche un'attestazione `kid`, che indica quali di queste chiavi sono state usate per firmare il token ID.

Per verificare i token da Azure AD B2C, è necessario generare la chiave pubblica usando esponente(e) e modulo(n). È necessario determinare come eseguire questa operazione nel rispettivo linguaggio di programmazione di conseguenza. La documentazione ufficiale sulla generazione di chiave pubblica con il protocollo RSA è disponibile qui:https://tools.ietf.org/html/rfc3447#section-3.1

Dopo avere convalidato la firma del token ID, è necessario verificare diverse attestazioni. Ad esempio:

- Convalidare l'attestazione `nonce` per impedire attacchi di riproduzione dei token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
- Convalidare `aud` l'attestazione per assicurarsi che il token ID sia stato emesso per l'applicazione. Il valore deve essere l'ID applicazione dell'applicazione.
- Convalidare `iat` `exp` il e afferma di assicurarsi che il token ID non è scaduto.

Esistono numerose altre convalide che è consigliabile eseguire. Le convalide sono descritte in dettaglio in [OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html). È anche possibile convalidare attestazioni aggiuntive, a seconda dello scenario. Alcune convalide comuni includono:

- Verificare che l'utente/organizzazione si sia iscritto all'applicazione.
- Verificare che l'utente abbia le autorizzazioni o i privilegi adeguati.
- Verificare che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori di Azure.

Dopo aver convalidato il token ID, è possibile iniziare una sessione con l'utente. È possibile utilizzare le attestazioni nel token ID per ottenere informazioni sull'utente nell'applicazione. Gli usi di queste informazioni includono la visualizzazione, i record e l'autorizzazione.

## <a name="get-a-token"></a>Acquisizione di un token

Se è necessario che l'applicazione Web esedi solo i flussi utente, è possibile ignorare le sezioni successive. Queste sezioni sono applicabili solo alle applicazioni Web che devono effettuare chiamate autenticate a un'API Web e sono protette anche da Azure AD B2C.

È possibile riscattare il codice di autorizzazione acquisito, tramite `response_type=code+id_token`, per un token nella risorsa desiderata inviando una richiesta `POST` all'endpoint `/token`. In Azure AD B2C è possibile richiedere i token di [accesso per altre API](access-tokens.md#request-a-token) come di consueto specificandone l'ambito o le richieste.

Puoi anche richiedere un token di accesso per l'API Web back-end della tua app per convenzione di usare l'ID client dell'app come ambito richiesto (che comporterà un token di accesso con tale ID client come "audience"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametro | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| "tenant" | Sì | Nome del tenant B2C di Azure AD |
| "Criteri" | Sì | Flusso utente usato per acquisire il codice di autorizzazione. Non è possibile usare un flusso utente diverso in questa richiesta. Aggiungere questo parametro alla stringa di query, non al corpo POST. |
| client_id | Sì | ID applicazione assegnato dal portale di [Azure](https://portal.azure.com/) all'applicazione. |
| client_secret | Sì, nelle app Web | Il segreto dell'applicazione generato nel portale di [Azure.](https://portal.azure.com/) I segreti client vengono utilizzati in questo flusso per gli scenari di app Web, in cui il client può archiviare in modo sicuro un segreto client. Per gli scenari di app nativa (client pubblico), i segreti client non possono essere archiviati in modo sicuro, non vengono utilizzati in questo flusso. Se si utilizza un segreto client, si prega di cambiarlo su base periodica. |
| codice | Sì | Codice di autorizzazione acquisito all'inizio del flusso utente. |
| grant_type | Sì | Tipo di concessione, che deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| redirect_uri | Sì | Parametro `redirect_uri` dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| scope | No | Elenco di ambiti separati da spazi. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di parametri id_token. Può essere usato per ottenere i token per l'API Web back-end dell'applicazione, rappresentata dallo stesso ID applicazione del client. L'ambito `offline_access` indica che l'applicazione necessita di un token di aggiornamento per l'accesso esteso alle risorse. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parametro | Descrizione |
| --------- | ----------- |
| not_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token_type | Valore del tipo di token. `Bearer`è l'unico tipo supportato. |
| access_token | Il token JWT firmato richiesto. |
| scope | Ambiti per il quale il token è valido. |
| expires_in | Periodo di validità del token di accesso (in secondi). |
| refresh_token | Token di aggiornamento di OAuth 2.0. L'applicazione può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. L'ambito `offline_access` deve essere stato utilizzato nelle richieste di autorizzazione e token per ricevere un token di aggiornamento. |

Le risposte di errore si presentano nel modo seguente:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| --------- | ----------- |
| error | Codice che può essere utilizzato per classificare i tipi di errori che si verificano. |
| error_description | Messaggio che consente di identificare la causa principale di un errore di autenticazione. |

## <a name="use-the-token"></a>Uso del token

Dopo avere acquisito un token di accesso, è possibile usarlo nelle richieste alle API Web back-end includendolo nell'intestazione `Authorization`:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aggiornamento del token

I token ID scadono in un breve periodo di tempo. Aggiornare i token dopo la scadenza per continuare ad essere in grado di accedere alle risorse. È possibile aggiornare un `POST` token inviando `/token` un'altra richiesta all'endpoint. Specificare questa volta il parametro `refresh_token` al posto del parametro `code`:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametro | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| "tenant" | Sì | Nome del tenant B2C di Azure AD |
| "Criteri" | Sì | Flusso utente usato per acquisire il token di aggiornamento originale. Non è possibile usare un flusso utente diverso in questa richiesta. Aggiungere questo parametro alla stringa di query, non al corpo POST. |
| client_id | Sì | ID applicazione assegnato dal portale di [Azure](https://portal.azure.com/) all'applicazione. |
| client_secret | Sì, nelle app Web | Il segreto dell'applicazione generato nel portale di [Azure.](https://portal.azure.com/) I segreti client vengono utilizzati in questo flusso per gli scenari di app Web, in cui il client può archiviare in modo sicuro un segreto client. Per gli scenari di app nativa (client pubblico), i segreti client non possono essere archiviati in modo sicuro, non vengono utilizzati in questa chiamata. Se si utilizza un segreto client, si prega di cambiarlo su base periodica. |
| grant_type | Sì | Tipo di concessione, che deve essere un token di aggiornamento per questa parte del flusso del codice di autorizzazione. |
| refresh_token | Sì | Token di aggiornamento originale acquisito nella seconda parte del flusso. L'ambito `offline_access` deve essere utilizzato nelle richieste di autorizzazione e token per ricevere un token di aggiornamento. |
| redirect_uri | No | Parametro `redirect_uri` dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| scope | No | Elenco di ambiti separati da spazi. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. Può essere utilizzato per inviare token all'API Web back-end dell'applicazione, rappresentata dallo stesso ID applicazione del client. L'ambito `offline_access` indica che l'applicazione necessita di un token di aggiornamento per l'accesso esteso alle risorse. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parametro | Descrizione |
| --------- | ----------- |
| not_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token_type | Valore del tipo di token. `Bearer`è l'unico tipo supportato. |
| access_token | Token JWT firmato richiesto. |
| scope | Ambito per il quale il token è valido. |
| expires_in | Periodo di validità del token di accesso (in secondi). |
| refresh_token | Token di aggiornamento di OAuth 2.0. L'applicazione può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. |

Le risposte di errore si presentano nel modo seguente:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| --------- | ----------- |
| error | Codice che può essere utilizzato per classificare i tipi di errori che si verificano. |
| error_description | Messaggio che consente di identificare la causa principale di un errore di autenticazione. |

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione

Quando si desidera disconnettere l'utente dall'applicazione, non è sufficiente cancellare i cookie dell'applicazione o terminare in altro modo la sessione con l'utente. Reindirizzare l'utente ad Azure AD B2C per disconnettersi. In caso contrario, l'utente potrebbe essere in grado di eseguire nuovamente l'autenticazione nell'applicazione senza immettere nuovamente le credenziali.

Per disconnettere l'utente, reindirizzare l'utente all'endpoint `end_session` elencato nel documento di metadati OpenID Connect descritto in precedenza:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parametro | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| "tenant" | Sì | Nome del tenant B2C di Azure AD |
| "Criteri" | Sì | Flusso utente da usare per disconnettere l'utente dall'applicazione. |
| id_token_hint| No | Token ID rilasciato in precedenza da passare all'endpoint di disconnessione come suggerimento sulla sessione autenticata corrente dell'utente finale con il client. L'oggetto `id_token_hint` garantisce che l'URL `post_logout_redirect_uri` di risposta registrato nelle impostazioni dell'applicazione B2C di Azure AD sia assicurato. |
| client_id | No* | ID applicazione assegnato dal portale di [Azure](https://portal.azure.com/) all'applicazione.<br><br>\**Questa operazione è `Application` necessaria quando si utilizza la configurazione SSO `No`di isolamento e Richiedi token _ID_ nella richiesta di disconnessione è impostato su .* |
| post_logout_redirect_uri | No | URL a cui l'utente deve essere reindirizzato dopo la disconnessione. Se non è incluso, Azure AD B2C mostra all'utente un messaggio generico. A meno `id_token_hint`che non si fornisca un oggetto , non è necessario registrare questo URL come URL di risposta nelle impostazioni dell'applicazione B2C di Azure AD. |
| state | No | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'applicazione deve `state` verificare che i valori nella richiesta e nella risposta siano identici. |

### <a name="secure-your-logout-redirect"></a>Proteggere il reindirizzamento della disconnessione

Dopo la disconnessione, l'utente viene `post_logout_redirect_uri` reindirizzato all'URI specificato nel parametro, indipendentemente dagli URL di risposta specificati per l'applicazione. Tuttavia, se `id_token_hint` viene passato un valore valido, Azure AD `post_logout_redirect_uri` B2C verifica che il valore di corrisponda a uno degli URI di reindirizzamento configurati dell'applicazione prima di eseguire il reindirizzamento. Se per l'applicazione non è stato configurato alcun URL di risposta corrispondente, viene visualizzato un messaggio di errore e l'utente non viene reindirizzato.

### <a name="external-identity-provider-sign-out"></a>Disconnessione del provider di identità esterno

L'indirizzamento dell'utente all'endpoint `end_session` cancella parte dello stato Single Sign-On dell'utente con Azure AD B2C, ma non disconnette l'utente dalla sessione del provider di identità di social networking (IDP). Se l'utente seleziona lo stesso IDP durante un accesso successivo, viene autenticato nuovamente senza immettere le credenziali. Se un utente desidera disconnettersi dall'applicazione, non significa necessariamente che desideri disconnettersi dal proprio account Facebook. Tuttavia, se vengono utilizzati account locali, la sessione dell'utente termina correttamente.
