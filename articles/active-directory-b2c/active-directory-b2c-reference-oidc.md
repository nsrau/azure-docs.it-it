---
title: Accesso Web con OpenID Connect - Azure Active Directory B2C | Microsoft Docs
description: Creare applicazioni web con il protocollo di autenticazione OpenID Connect in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4137360fadab0206c6569b58d6a9a0519ce74450
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703941"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Accesso Web con OpenID Connect in Azure Active Directory B2C

OpenID Connect è un protocollo di autenticazione basato su OAuth 2.0 che può essere usato per consentire agli utenti di accedere in modo sicuro alle applicazioni Web. Tramite l'implementazione in Azure Active Directory B2C (Azure AD B2C) di OpenID Connect è possibile assegnare esperienze di gestione delle iscrizioni, degli accessi e altre esperienze di gestione delle identità nelle applicazioni Web ad Azure AD. Questa guida illustra come eseguire questa operazione in modo indipendente dal linguaggio. La guida descrive come inviare e ricevere messaggi HTTP senza usare una delle librerie Microsoft open source.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 per consentirne l'uso come protocollo di *autenticazione*. Questo protocollo di autenticazione consente di eseguire single sign-on. Introduce il concetto di un' *token ID*, che consente al client di verificare l'identità dell'utente e ottenere informazioni sul profilo di base sull'utente.

Poiché estende OAuth 2.0, consente inoltre alle applicazioni di acquisire in modo sicuro *token di accesso*. I token di accesso possono essere usati per accedere alle risorse protette tramite un [server di autorizzazione](active-directory-b2c-reference-protocols.md). OpenID Connect è consigliato se si sta compilando un'applicazione web che ha ospitato in un server e accessibili tramite un browser. Se si desidera aggiungere la gestione delle identità per dispositivo mobile o applicazioni desktop usando Azure AD B2C, è consigliabile usare [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) invece di OpenID Connect. Per altre informazioni sui token, vedere il [panoramica dei token in Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Azure AD B2C estende il protocollo standard OpenID Connect per non limitarsi esclusivamente a semplici operazioni di autenticazione e autorizzazione. Introduce la [parametro flusso utente](active-directory-b2c-reference-policies.md), che consente di usare OpenID Connect per aggiungere l'utente esperienze per l'applicazione, ad esempio iscrizione, accesso e gestione dei profili.

## <a name="send-authentication-requests"></a>Invio di richieste di autenticazione

Quando l'applicazione web deve autenticare l'utente ed eseguire un flusso utente, può indirizzarlo all'utente di `/authorize` endpoint. L'utente esegue operazioni a seconda del flusso utente.

In questa richiesta, il client indica le autorizzazioni che deve acquisire dall'utente nel `scope` parametro e il flusso utente per l'esecuzione nel `p` parametro. Di seguito vengono illustrati tre esempi (con interruzioni di riga per migliorare la leggibilità), ognuno dei quali usa un flusso utente diverso. Per apprendere il funzionamento di ogni richiesta, provare a incollare la richiesta in un browser ed eseguirla. È possibile sostituire `fabrikamb2c` con il nome del tenant, se si dispone di uno e aver creato un flusso utente.

#### <a name="use-a-sign-in-user-flow"></a>Usare un flusso utente di accesso
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Usare un flusso utente di iscrizione
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Usare un flusso utente di modifica del profilo
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametro | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| client_id | Sì | ID applicazione che il [portale di Azure](https://portal.azure.com/) assegnato all'applicazione. |
| response_type | Sì | Deve includere un token ID per OpenID Connect. Se l'applicazione web necessita anche di token per chiamare un'API web, è possibile usare `code+id_token`. |
| redirect_uri | No  | Il `redirect_uri` parametro dell'applicazione, dove le risposte di autenticazione possono essere inviate e ricevute dall'applicazione. Deve corrispondere esattamente a uno del `redirect_uri` parametri che è stato registrato nel portale di Azure, ad eccezione del fatto che deve essere codificato in URL. |
| scope | Sì | Elenco di ambiti separati da spazi. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. Il `offline_access` ambito è facoltativo per le applicazioni web. Indica che l'applicazione richiede un *token di aggiornamento* per l'accesso esteso alle risorse. |
| response_mode | No  | Il metodo che consente di inviare il codice di autorizzazione risultante all'applicazione. Può essere `query`, `form_post` o `fragment`.  `form_post` è la modalità di risposta consigliata perché offre la sicurezza migliore. |
| state | No  | Valore incluso nella richiesta che viene restituita anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'applicazione prima che si è verificata la richiesta di autenticazione, ad esempio la pagina di che cui si trovava. |
| nonce | Sì | Valore incluso nella richiesta, generata dall'applicazione, che è incluso nel token ID risultante come attestazione. L'applicazione può verificare questo valore per limitare gli attacchi di riproduzione dei token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| p | Sì | Il flusso utente che viene eseguito. È il nome di un flusso utente creato nel tenant di Azure AD B2C. Il nome del flusso utente debba iniziare con `b2c\_1\_`. |
| prompt | No  | Tipo di interazione utente obbligatoria. L'unico valore valido in questa fase è `login`, che impone all'utente di immettere le credenziali per la richiesta. |

A questo punto, l'utente viene richiesto di completare il flusso di lavoro. L'utente potrebbe essere necessario immettere il nome utente e password, accedere con un'identità di social networking o segno di per la directory. Potrebbero esserci qualsiasi altro numero di passaggi a seconda della modalità di definizione del flusso utente.

Dopo che l'utente ha completato il flusso utente, viene restituita una risposta all'applicazione a indicato `redirect_uri` usando il metodo specificato nel parametro di `response_mode` parametro. La risposta è la stessa per ognuno dei casi precedenti, indipendenti del flusso utente.

Una risposta con esito positivo che utilizza `response_mode=fragment` ha un aspetto simile al seguente:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | DESCRIZIONE |
| --------- | ----------- |
| id_token | Il token ID richiesto dall'applicazione. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| code | Il codice di autorizzazione richiesto dall'applicazione, se è stato usato `response_type=code+id_token`. L'applicazione può usare il codice di autorizzazione per richiedere un token di accesso per una risorsa di destinazione. I codici di autorizzazione è in genere scadono dopo circa 10 minuti. |
| state | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'applicazione deve verificare che il `state` valori nella richiesta e risposta siano identici. |

Le risposte di errore possono essere inviate anche al `redirect_uri` parametro in modo che l'applicazione possa gestirle adeguatamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | DESCRIZIONE |
| --------- | ----------- |
| error | Un codice che può essere usato per classificare i tipi di errori che si verificano. |
| error_description | Messaggio di errore specifico che consente di identificare la causa radice di un errore di autenticazione. |
| state | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'applicazione deve verificare che il `state` valori nella richiesta e risposta siano identici. |

## <a name="validate-the-id-token"></a>Convalidare il token ID

La ricezione di un token ID non è sufficiente per autenticare l'utente. Convalidare la firma del token ID e verificare le attestazioni nel token soddisfano i requisiti dell'applicazione. Azure AD B2C usa i [token Web JSON](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità. Sono disponibili molte librerie open source per la convalida dei token JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame tali opzioni anziché implementare una logica di convalida personalizzata. 

Azure AD B2C ha un endpoint di metadati OpenID Connect, che consente a un'applicazione ottenere informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant B2C include un documento di metadati JSON per ogni flusso utente. Ad esempio, il documento di metadati del flusso utente `b2c_1_sign_in` in `fabrikamb2c.onmicrosoft.com` si trova in:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una delle proprietà del documento di configurazione è `jwks_uri`, il cui valore per lo stesso flusso utente è:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Per determinare quale flusso utente sono stato usato per la firma di un ID token (e da dove ottenere i metadati), sono disponibili due opzioni. Innanzitutto, il nome del flusso utente è incluso nell'attestazione `acr` del token ID. In secondo luogo, è possibile codificare il flusso utente nel valore del parametro `state` quando si rilascia la richiesta, per poi decodificarlo e determinare quale flusso utente è stato usato. Entrambi i metodi sono validi.

Dopo aver acquisito il documento di metadati dall'endpoint di metadati OpenID Connect, è possibile usare le chiavi pubbliche RSA 256 per convalidare la firma del token ID. Potrebbero esserci più chiavi elencate in questo endpoint, ognuna identificata da un `kid` attestazione. L'intestazione del token ID contiene anche un'attestazione `kid`, che indica quali di queste chiavi sono state usate per firmare il token ID.

Dopo avere convalidato la firma del token ID, è necessario verificare diverse attestazioni. Ad esempio:

- Convalidare l'attestazione `nonce` per impedire attacchi di riproduzione dei token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
- Convalidare il `aud` attestazione per garantire che sia stato rilasciato il token ID per l'applicazione. Il valore deve essere l'ID applicazione dell'applicazione.
- Convalidare il `iat` e `exp` attestazioni per assicurarsi che il token ID non sia scaduta.

Esistono numerose altre convalide che è consigliabile eseguire. Le convalide sono descritte dettagliatamente le [specifica di OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

- Verificare che l'utente o l'organizzazione ha effettuato l'iscrizione per l'applicazione.
- Verificare che l'utente abbia le autorizzazioni o i privilegi adeguati.
- Verificare che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori di Azure.

Dopo aver convalidato il token ID, è possibile avviare una sessione con l'utente. È possibile usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'applicazione. Gli usi di queste informazioni includono la visualizzazione, i record e l'autorizzazione.

## <a name="get-a-token"></a>Acquisizione di un token

Se è necessario solo eseguire flussi utente l'applicazione web, è possibile ignorare le prossime sezioni. Queste sezioni sono applicabili solo alle applicazioni che devono eseguire chiamate autenticate a un'API web e sono protette da Azure AD B2C di web.

È possibile riscattare il codice di autorizzazione acquisito, tramite `response_type=code+id_token`, per un token nella risorsa desiderata inviando una richiesta `POST` all'endpoint `/token`. Attualmente, l'unica risorsa che è possibile richiedere un token è l'API web di back-end dell'applicazione. La convenzione per richiedere un token a se stessi consiste nell'usare l'ID dell'applicazione client come ambito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametro | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| p | Sì | Flusso utente usato per acquisire il codice di autorizzazione. È possibile usare un flusso utente diverso in questa richiesta. Questo parametro può essere aggiunto alla stringa di query, non al corpo del POST. |
| client_id | Sì | ID applicazione che il [portale di Azure](https://portal.azure.com/) assegnato all'applicazione. |
| grant_type | Sì | Tipo di concessione, che deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| scope | No  | Elenco di ambiti separati da spazi. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di parametri id_token. Può essere utilizzato per ottenere i token per la specifica dell'applicazione back-end API web, che è rappresentata dallo stesso ID applicazione del client. Il `offline_access` ambito indica che l'applicazione necessita di un token di aggiornamento per l'accesso esteso alle risorse. |
| code | Sì | Il codice di autorizzazione acquisito all'inizio del flusso utente. |
| redirect_uri | Sì | Parametro `redirect_uri` dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| client_secret | Sì | Il segreto dell'applicazione a cui è stato generato la [portale di Azure](https://portal.azure.com/). La chiave privata dell'applicazione è un elemento di sicurezza importante. È necessario archiviarla in modo sicuro nel server. Modificare la chiave privata client su base periodica. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | DESCRIZIONE |
| --------- | ----------- |
| not_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token_type | Valore del tipo di token. `Bearer` è l'unico tipo supportato. |
| access_token | Il token JWT firmato richiesto. |
| scope | Ambiti per il quale il token è valido. |
| expires_in | Periodo di validità del token di accesso (in secondi). |
| refresh_token | Token di aggiornamento di OAuth 2.0. L'applicazione può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. Aggiornare i token possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. L'ambito `offline_access` deve essere stato utilizzato in entrambe le autorizzazioni e le richieste di token per ricevere un token di aggiornamento. |

Le risposte di errore si presentano nel modo seguente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | DESCRIZIONE |
| --------- | ----------- |
| error | Un codice che può essere usato per classificare i tipi di errori che si verificano. |
| error_description | Un messaggio che consentono di identificare la causa radice di un errore di autenticazione. |

## <a name="use-the-token"></a>Uso del token

Dopo avere acquisito un token di accesso, è possibile usarlo nelle richieste alle API Web back-end includendolo nell'intestazione `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aggiornamento del token

I token ID scadono entro un breve periodo di tempo. Il token di aggiornamento dopo la scadenza per continuare ad accedere alle risorse. È possibile aggiornare un token inviando un'altra `POST` richiesta per il `/token` endpoint. Specificare questa volta il parametro `refresh_token` al posto del parametro `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametro | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| p | Sì | Flusso utente usato per acquisire il token di aggiornamento originale. È possibile usare un flusso utente diverso in questa richiesta. Questo parametro può essere aggiunto alla stringa di query, non al corpo del POST. |
| client_id | Sì | ID applicazione che il [portale di Azure](https://portal.azure.com/) assegnato all'applicazione. |
| grant_type | Sì | Il tipo di concessione, che deve essere un token di aggiornamento per questa parte del flusso del codice di autorizzazione. |
| scope | No  | Elenco di ambiti separati da spazi. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. Può essere utilizzato per inviare i token dell'applicazione back-end API web, che è rappresentata dallo stesso ID applicazione del client. Il `offline_access` ambito indica che l'applicazione necessita di un token di aggiornamento per l'accesso esteso alle risorse. |
| redirect_uri | No  | Parametro `redirect_uri` dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| refresh_token | Sì | Il token di aggiornamento originale acquisito nella seconda parte del flusso. Il `offline_access` ambito deve essere usata nelle richieste di autorizzazione e le richieste di token per ricevere un token di aggiornamento. |
| client_secret | Sì | Il segreto dell'applicazione a cui è stato generato la [portale di Azure](https://portal.azure.com/). La chiave privata dell'applicazione è un elemento di sicurezza importante. È necessario archiviarla in modo sicuro nel server. Modificare la chiave privata client su base periodica. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | DESCRIZIONE |
| --------- | ----------- |
| not_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token_type | Valore del tipo di token. `Bearer` è l'unico tipo supportato. |
| access_token | Il token JWT firmato richiesto. |
| scope | L'ambito per il quale il token è valido. |
| expires_in | Periodo di validità del token di accesso (in secondi). |
| refresh_token | Token di aggiornamento di OAuth 2.0. L'applicazione può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. Aggiornare i token possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. |

Le risposte di errore si presentano nel modo seguente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | DESCRIZIONE |
| --------- | ----------- |
| error | Un codice che può essere usato per classificare i tipi di errori che si verificano. |
| error_description | Un messaggio che consentono di identificare la causa radice di un errore di autenticazione. |

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione

Quando si desidera disconnettere l'utente dall'applicazione, non è sufficiente cancellare i cookie dell'applicazione o in caso contrario, terminare la sessione con l'utente. Reindirizzare l'utente di Azure AD B2C per la disconnessione. Se non si riesce a eseguire questa operazione, l'utente potrebbe essere in grado di autenticarsi all'applicazione senza dover immettere nuovamente le proprie credenziali.

È sufficiente reindirizzare l'utente per il `end_session` endpoint elencato nel documento di metadati OpenID Connect descritto in precedenza:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametro | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| p | Sì | Flusso utente da usare per disconnettere l'utente dall'applicazione. |
| post_logout_redirect_uri | No  | L'URL all'utente al quale deve essere reindirizzato dopo la disconnessione ha esito positivo. Se non è incluso, Azure AD B2C Mostra all'utente un messaggio generico. |

Indirizza l'utente di `end_session` endpoint Cancella alcune delle stato single sign-on il suo con Azure AD B2C, ma non far accedere l'utente dalla sessione del provider (IDP) identità di social networking. Se l'utente seleziona il provider di identità stesso in un successivo accesso, si viene autenticata di nuovo, senza immettere le credenziali. Se un utente vuole disconnettersi l'applicazione, non significa necessariamente che intenda disconnettersi dal proprio account Facebook. Tuttavia, se si usano gli account locali, la sessione dell'utente termina correttamente.

