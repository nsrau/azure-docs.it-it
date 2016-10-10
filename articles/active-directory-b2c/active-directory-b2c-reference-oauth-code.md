<properties
	pageTitle="Azure Active Directory B2C | Microsoft Azure"
	description="Creazione di applicazioni Web tramite l'implementazione di Azure Active Directory del protocollo di autenticazione OpenID Connect."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>  

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="dastrock"/>  

# Azure Active Directory B2C: flusso del codice di autorizzazione di OAuth 2.0

La concessione del codice di autorizzazione OAuth 2.0 può essere utilizzata nelle app che vengono installate su un dispositivo per ottenere l'accesso alle risorse protette, come l'API web. Usando l'implementazione di Azure Active Directory (Azure AD) B2C di OAuth 2.0, è possibile aggiungere attività di gestione dell'iscrizione, dell'accesso e altre attività di gestione delle identità alle app desktop e per dispositivi mobili. Questa guida è indipendente dal linguaggio usato. La guida descrive come inviare e ricevere messaggi HTTP senza usare una delle librerie Microsoft open source.

<!-- TODO: Need link to libraries -->  

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749). È possibile usarlo per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui [app Web](active-directory-b2c-apps.md#web-apps) e [app native](active-directory-b2c-apps.md#mobile-and-native-apps). Consente alle app di acquisire in modo sicuro i **token di accesso** che possono essere usati per accedere alle risorse protette tramite un [server di autorizzazione](active-directory-b2c-reference-protocols.md#the-basics).

Questa guida è incentrata su una versione particolare del flusso del codice di autorizzazione di OAuth 2.0: i **client pubblici**. Un client pubblico è qualsiasi applicazione client che non può essere considerata attendibile in modo sicuro per mantenere l'integrità di una password segreta. Questo include app per dispositivi mobili, app per desktop e pressoché qualsiasi applicazione che viene eseguito su un dispositivo e deve ottenere i token di accesso. Se si vuole aggiungere la gestione delle identità a un'app Web tramite Azure AD B2C, è consigliabile usare [OpenID Connect](active-directory-b2c-reference-oidc.md) invece di OAuth 2.0.

Azure AD B2C estende i flussi standard OAuth 2.0 per non limitarsi esclusivamente a semplici operazioni di autorizzazione e autenticazione. Introduce il [**parametro criteri**](active-directory-b2c-reference-policies.md), che consente di usare OAuth 2.0 per aggiungere esperienze utente all'app, ad esempio gestione dell'iscrizione, dell'accesso e del profilo. Di seguito verrà illustrato come usare OAuth 2.0 e i criteri per implementare ognuna di queste esperienze nelle applicazioni native. Verrà illustrato anche come ottenere i token di accesso alle API Web.

Le richieste HTTP di esempio seguenti utilizzano la directory B2C di esempio, **fabrikamb2c.onmicrosoft.com**, nonché l'applicazione di esempio e i criteri. Si possono provare le richieste in totale autonomia usando questi valori oppure è possibile sostituirli con valori personalizzati. Ulteriori informazioni su come [ottenere directory, applicazione e i criteri B2C](#use-your-own-b2c-directory).

## 1\. Ottenere un codice di autorizzazione
Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize`. Questa è la parte interattiva del flusso, dove l'utente opera effettivamente. In questa richiesta il client indica le autorizzazioni che deve acquisire dall'utente nel parametro `scope` e i criteri da eseguire nel parametro `p`. Di seguito vengono forniti tre esempi (con interruzioni di riga per migliorare la leggibilità), ciascuno dei quali utilizza un criterio diverso.

#### Uso di un criterio di accesso

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### Uso di un criterio di iscrizione

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### Uso di un criterio di modifica del profilo

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parametro | Obbligatorio? | Descrizione |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | Obbligatorio | ID applicazione assegnato all'app dal [portale di Azure](https://portal.azure.com). |
| response\_type | Obbligatorio | Tipo di risposta, che deve includere `code` per il flusso del codice di autorizzazione. |
| redirect\_uri | Obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | Obbligatorio | Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'uso dell'ID client come ambito indica che l'applicazione richiede un **token di accesso**, che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client. Lo scopo `offline_access` indica che l'applicazione richiede un **refresh\_token** per un accesso duraturo alle risorse. È anche possibile usare l'ambito `openid` per richiedere un **id\_token** ad Azure Active Directory B2C. |
| response\_mode | Consigliato | Metodo da usare per inviare il codice di autorizzazione risultante all'app. Può essere 'query', 'form\_post' o 'fragment'.
| state | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina in cui si trovava o i criteri utilizzati. |
| p | Obbligatorio | Criteri che verranno eseguiti. Si tratta del nome di un criterio creato nella directory B2C. Il valore del nome del criterio deve iniziare con "b2c\_1\_". Altre informazioni sui criteri sono disponibili nell'articolo [Framework di criteri estendibile](active-directory-b2c-reference-policies.md). |
| prompt | Facoltativo | Tipo di interazione utente obbligatoria. L'unico valore valido in questa fase è 'login', che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non avrà effetto. |

A questo punto, viene richiesto all'utente di completare il flusso di lavoro del criterio. È possibile che venga richiesto all'utente di immettere nome utente e password, di accedere con un'identità di social networking, di iscriversi alla directory o qualsiasi altro passaggio, a seconda di come sono definiti i criteri.

Dopo che l'utente ha completato il criterio, Azure AD restituisce una risposta all'app nel `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`. La risposta corrisponde esattamente a ciascuno dei casi precedenti, indipendentemente dai criteri eseguiti.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| code | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per una risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve. In genere scadono dopo circa 10 minuti. |
| state | Vedere la descrizione completa nella tabella precedente. Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'app possa gestirle adeguatamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| state | Vedere la descrizione completa nella prima tabella di questa sezione. Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |


## 2\. Acquisizione di un token
Ora che è stato acquisito il codice di autorizzazione, è possibile riscattare `code` per un token nella risorsa desiderata, inviando una richiesta `POST` all'endpoint `/token`. In Azure AD B2C l'unica risorsa per la quale è possibile richiedere un token è l'API Web back-end dell'app stessa. La convenzione usata per richiedere un token di questo tipo consiste nell'usare l'ID client dell'app come ambito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametro | Obbligatorio? | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obbligatorio | Il criterio utilizzato per acquisire il codice di autorizzazione. Non è possibile usare un criterio diverso in questa richiesta. Si noti che questo parametro viene aggiunto alla *stringa di query*, non al corpo della richiesta POST. |
| client\_id | Obbligatorio | ID applicazione assegnato all'app dal [portale di Azure](https://portal.azure.com). |
| grant\_type | Obbligatorio | Tipo di concessione, che deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| scope | Consigliato | Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'uso dell'ID client come ambito indica che l'applicazione richiede un **token di accesso**, che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client. Lo scopo `offline_access` indica che l'applicazione richiede un **refresh\_token** per un accesso duraturo alle risorse. È anche possibile usare l'ambito `openid` per richiedere un **id\_token** ad Azure Active Directory B2C. |
| code | Obbligatorio | Codice di autorizzazione acquisito durante la prima sezione del flusso. |
| redirect\_uri | Obbligatorio | Il parametro redirect\_uri dell'applicazione, dove è stato ricevuto l’authorization\_code. |

Una risposta token con esito positivo ha un aspetto simile al seguente:

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
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| not\_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token\_type | Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| access\_token | Token Web JSON (JWT) firmato richiesto. |
| scope | Gli ambiti per i quali il token è valido, utilizzabili per i token di caching successivamente. |
| expires\_in | Periodo di validità del token (in secondi). |
| refresh\_token | Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare il [riferimento al token B2C](active-directory-b2c-reference-tokens.md). |

Le risposte di errore hanno un aspetto simile al seguente:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## 3\. Uso del token
Dopo aver ottenuto un `access_token`, è possibile usare il token nelle richieste alle API Web back-end includendolo nell'intestazione `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4\. Aggiornamento del token
I token di accesso e ID hanno breve durata. È necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. A tale scopo, inviare un'altra richiesta `POST` all'endpoint `/token`, questa volta specificando `refresh_token` invece di `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametro | Obbligatorio? | Descrizione |
| ----------------------- | ------------------------------- | -------- |
| p | Obbligatorio | Criterio usato per acquisire il token di aggiornamento originale. Non è possibile usare un criterio diverso in questa richiesta. Si noti che questo parametro viene aggiunto alla *stringa di query*, non al corpo della richiesta POST. |
| client\_id | Consigliato | ID applicazione assegnato all'app dal [portale di Azure](https://portal.azure.com). |
| grant\_type | Obbligatorio | Tipo di concessione, che deve essere `refresh_token` per questa sezione del flusso del codice di autorizzazione. |
| scope | Consigliato | Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'uso dell'ID client come ambito indica che l'applicazione richiede un **token di accesso**, che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client. Lo scopo `offline_access` indica che l'applicazione richiede un **refresh\_token** per un accesso duraturo alle risorse. È anche possibile usare l'ambito `openid` per richiedere un **id\_token** ad Azure Active Directory B2C. |
| redirect\_uri | Facoltativo | Il parametro redirect\_uri dell'applicazione, dove è stato ricevuto l’authorization\_code. |
| refresh\_token | Obbligatorio | refresh\_token acquisito durante la seconda sezione del flusso. |

Una risposta token con esito positivo ha un aspetto simile al seguente:

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
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| not\_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token\_type | Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| access\_token | Token Web JSON (JWT) firmato richiesto. |
| scope | Gli ambiti per i quali il token è valido, utilizzabili per i token di caching successivamente. |
| expires\_in | Periodo di validità del token (in secondi). |
| refresh\_token | Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare il [riferimento al token B2C](active-directory-b2c-reference-tokens.md). |

Le risposte di errore hanno un aspetto simile al seguente:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## Uso della propria directory B2C

Per provare queste richieste autonomamente, è innanzitutto necessario eseguire questi tre passaggi, quindi sostituire i valori di esempio con i propri:

- [Creare una directory B2C](active-directory-b2c-get-started.md) e usare il nome della directory nelle richieste.
- [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione e un URI di reindirizzamento. Si potrebbe voler includere un **native client** nell'app.
- [Creare i criteri](active-directory-b2c-reference-policies.md) per ottenere i nomi dei criteri.

<!---HONumber=AcomDC_0928_2016-->