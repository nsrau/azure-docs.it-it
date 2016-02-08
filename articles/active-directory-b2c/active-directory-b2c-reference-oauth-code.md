<properties
	pageTitle="Anteprima di AD B2C di Azure | Microsoft Azure"
	description="Creazione di applicazioni Web utilizzando l'implementazione di Azure AD del protocollo di autenticazione OpenID Connect."
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Anteprima AD B2C Azure: Flusso di codice di autorizzazione OAuth 2.0

La concessione del codice di autorizzazione OAuth 2.0 può essere utilizzata nelle app che vengono installate su un dispositivo per ottenere l'accesso alle risorse protette, come l'API web. Utilizzando l'implementazione del AD B2C Azure di OAuth 2.0, è possibile aggiungere attività di gestione per la registrazione, l’accesso e altre identità alle app per il desktop e per i dispositivi mobili. Questa guida è indipendente dal linguaggio, e descrive come inviare e ricevere messaggi HTTP senza utilizzare une delle nostre librerie open source.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749). Può essere usato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui [app Web](active-directory-b2c-apps.md#web-apps) e [app native](active-directory-b2c-apps.md#mobile-and-native-apps). Consente alle app di acquisire in modo sicuro i **token di accesso** che possono essere usati per accedere alle risorse protette tramite un [server di autorizzazione](active-directory-b2c-reference-protocols.md#the-basics). Questa guida si concentrerà su una particolare versione del flusso di codice di autorizzazione OAuth 2.0 - **client pubblici**. Un client pubblico è qualsiasi applicazione client che non può essere considerata attendibile in modo sicuro per mantenere l'integrità di una password segreta. Questo include app per dispositivi mobili, app per desktop e pressoché qualsiasi applicazione che viene eseguito su un dispositivo e deve ottenere i token di accesso. Se si desidera aggiungere la gestione delle identità a un'app web utilizzando AD B2C Azure, è necessario utilizzare [OpenID Connect](active-directory-b2c-reference-oidc.md) piuttosto che OAuth 2.0.

Azure AD B2C estende i flussi standard OAuth 2.0 per non limitarsi esclusivamente a semplici operazioni di autorizzazione e autenticazione. Introduce il [**parametro criteri**](active-directory-b2c-reference-poliices.md), per utilizzare OAuth 2.0 per aggiungere esperienze utente all'applicazione, come ad esempio, iscrizione, accesso e gestione del profilo. Di seguito viene illustrato come utilizzare OAuth 2.0 e i criteri per implementare ciascuna di queste esperienze nelle applicazioni native e ottenere access\_token per l'accesso ad API Web.

Le richieste HTTP di esempio seguenti utilizzano la directory B2C di esempio, **fabrikamb2c.onmicrosoft.com**, nonché l'applicazione di esempio e i criteri. Si possono provare le richieste in totale autonomia utilizzando questi valori oppure è possibile sostituirli con valori personalizzati. Ulteriori informazioni su come [ottenere directory, applicazione e i criteri B2C](#use-your-own-b2c-directory).

## 1\. Ottenere un codice di autorizzazione
Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize`. Questa è la parte interattiva del flusso, dove l'utente opera effettivamente. In questa richiesta, il client indica le autorizzazioni da acquisire dall'utente nel parametro `scope` e il criterio da eseguire nel parametro `p`. Di seguito vengono forniti tre esempi (con interruzioni di riga per migliorare la leggibilità), ciascuno dei quali utilizza un criterio diverso.

#### Uso di un criterio di accesso

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
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
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### Uso di un criterio del profilo di modifica

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | obbligatorio | L'ID applicazione assegnato all’applicazione dal [Portale di Azure](https://portal.azure.com/). |
| response\_type | obbligatorio | Deve includere `code` per il flusso del codice di autorizzazione. |
| redirect\_uri | obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Un valore per l’ambito indica ad Azure AD che entrambe le autorizzazioni sono richieste. L’ambito `openid` indica un'autorizzazione per l'accesso dell’utente e per ottenere i dati relativi all'utente sotto forma di **id\_tokens** (ulteriori informazioni più avanti). Lo scopo `offline_access` indica che l'applicazione richiede un **refresh\_token** per un accesso duraturo alle risorse. |
| response\_mode | consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. Può essere 'query', 'form\_post' o 'fragment'.
| state | consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina in cui si trovava o i criteri utilizzati. |
| p | obbligatorio | Indica i criteri da eseguire. È il nome di un criterio creato nella directory B2C, il cui valore deve iniziare con "b2c\_1\_". Ulteriori informazioni sui criteri [qui](active-directory-b2c-reference-policies.md). |
| prompt | facoltativo | Indica il tipo di interazione obbligatoria dell'utente. L'unico valore valido in questa fase è 'login', che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non ha effetto. |

A questo punto, viene richiesto all'utente di completare il flusso di lavoro del criterio. Potrebbe richiedere all'utente di immettere nome utente e password, accedere con un'identità di social networking, l'iscrizione alla directory o qualsiasi altro numero di passaggi a seconda di come sono definiti i criteri. Dopo che l'utente completa il criterio, Azure AD restituisce una risposta all'app all’`redirect_uri` indicato, utilizzando il metodo specificato nel parametro `response_mode`. La risposta corrisponde esattamente a ciascuno dei casi precedenti, indipendentemente dai criteri eseguiti.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| code | Codice di autorizzazione richiesto dall'app. L'app può utilizzare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve, in genere scadono dopo circa 10 minuti. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

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
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |


## 2\. Acquisizione di un token
Ora che l’authorization\_code è stato acquisito, è possibile riscattare `code` per un token sulla risorsa desiderata, inviando una richiesta `POST` per l’endpoint `/token`. Nell'anteprima Azure AD B2C, l'unica risorsa per la quale è possibile richiedere un token è per l'API Web di back-end dell'applicazione stessa. La convenzione utilizzata per richiedere un token di questo tipo consiste nell'utilizzare l'ambito `openid`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| p | obbligatorio | Il criterio utilizzato per acquisire il codice di autorizzazione. Non è possibile utilizzare un criterio diverso in questa richiesta. **Questo parametro viene aggiunto alla stringa di query**, non al corpo della richiesta POST. |
| client\_id | obbligatorio | L'ID applicazione assegnato all’applicazione dal [Portale di Azure](https://portal.azure.com/). |
| grant\_type | obbligatorio | Deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Un valore per l’ambito indica ad Azure AD che entrambe le autorizzazioni sono richieste. L’ambito `openid` indica un'autorizzazione per l'accesso dell’utente e per ottenere i dati relativi all'utente sotto forma di **id\_token**. Utilizzabile per ottenere token sull’API Web di back-end dell’applicazione, rappresentato dallo stesso ID applicazione del client. Lo scopo `offline_access` indica che l'applicazione richiede un **refresh\_token** per un accesso duraturo alle risorse. |
| code | obbligatorio | Codice di autorizzazione acquisito durante la prima sezione del flusso. |
| redirect\_uri | obbligatorio | Il parametro redirect\_uri dell'applicazione, dove è stato ricevuto l’authorization\_code. |

Una risposta token con esito positivo ha un aspetto simile al seguente:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| not\_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token\_type | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| id\_token | Il token JWT firmato richiesto. |
| scope | Gli ambiti per i quali il token è valido, utilizzabili per i token di caching successivamente. |
| id\_token\_expires\_in | Validità dell’id\_token (in secondi). |
| profile\_info | Stringa JSON con codifica base-64 che può contenere informazioni utili sull'utente per la visualizzazione nell'applicazione originale. I contenuti esatti dipendono dalle attestazioni dell’applicazione configurate nel criterio |
| refresh\_token | Token di aggiornamento di OAuth 2.0. L'app può utilizzare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare il [riferimento al token B2C](active-directory-b2c-reference-tokens.md). |
| refresh\_token\_expires\_in | Tempo massimo di validità per un token di aggiornamento (in secondi). Il token di aggiornamento può tuttavia perdere validità in qualsiasi momento. |

> [AZURE.NOTE]
	Qualora si cerchi l’access\_token, considerare quanto segue. Quando si richiede l’ambito `openid`, Azure AD rilascia un JWT `id_token` nella risposta. Sebbene questo `id_token` non sia tecnicamente un access\_token OAuth 2.0, può essere utilizzato come tale durante la comunicazione con il servizio di back-end dell’applicazione, rappresentato dallo stesso client\_id del client. `id_token` è ancora un token di connessione JWT inviabile a una risorsa in un'intestazione di autorizzazione HTTP e utilizzato per autenticare le richieste. La differenza è che un `id_token` non dispone di un meccanismo per determinare l'ambito di una particolare applicazione client. Tuttavia, quando l'applicazione client è l'unico client in grado di comunicare con il servizio di back-end (come nel caso dell'anteprima di Azure AD B2C corrente), non è necessario per tale meccanismo di individuazione dell’ambito. Se l'anteprima di Azure AD B2C aggiunge la funzionalità per i client di comunicare con risorse di prime e terze parti supplementari, si ricorrerà agli access\_token. Tuttavia, anche in tal caso si consiglia di utilizzare `id_tokens` per comunicare con il servizio di back-end dell'applicazione. Per ulteriori informazioni sui tipi di applicazioni creabili con l'anteprima Azure AD B2C, consultare [questo articolo](active-directory-b2c-apps.md).

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
Dopo aver ottenuto un `id_token`, è possibile utilizzarlo in richieste alle API Web di back-end includendolo nell'intestazione `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4\. Aggiornamento del token
Gli id\_token di accesso hanno breve durata ed è quindi necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. A tale scopo, inviare un'altra richiesta `POST` all'endpoint `/token`, specificando il `refresh_token` anziché `code`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | -------- |
| p | obbligatorio | Il criterio utilizzato per acquisire il token di aggiornamento originale. Non è possibile utilizzare un criterio diverso in questa richiesta. **Questo parametro viene aggiunto alla stringa di query**, non al corpo della richiesta POST. |
| client\_id | obbligatorio | L'ID applicazione assegnato all’applicazione dal [Portale di Azure](https://portal.azure.com/). |
| grant\_type | obbligatorio | Deve essere `refresh_token` per questa sezione del flusso del codice di autorizzazione. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Un valore per l’ambito indica ad Azure AD che entrambe le autorizzazioni sono richieste. L’ambito `openid` indica un'autorizzazione per l'accesso dell’utente e per ottenere i dati relativi all'utente sotto forma di **id\_token**. Utilizzabile per ottenere token sull’API Web di back-end dell’applicazione, rappresentato dallo stesso ID applicazione del client. Lo scopo `offline_access` indica che l'applicazione richiede un **refresh\_token** per un accesso duraturo alle risorse. |
| redirect\_uri | obbligatorio | Il parametro redirect\_uri dell'applicazione, dove è stato ricevuto l’authorization\_code. |
| refresh\_token | obbligatorio | refresh\_token acquisito durante la seconda sezione del flusso. |

Una risposta token con esito positivo ha un aspetto simile al seguente:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| not\_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token\_type | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| id\_token | Il token JWT firmato richiesto. |
| scope | Gli ambiti per i quali il token è valido, utilizzabili per i token di caching successivamente. |
| id\_token\_expires\_in | Validità dell’id\_token (in secondi). |
| profile\_info | Stringa JSON con codifica base-64 che può contenere informazioni utili sull'utente per la visualizzazione nell'applicazione originale. I contenuti esatti dipendono dalle attestazioni dell’applicazione configurate nel criterio |
| refresh\_token | Token di aggiornamento di OAuth 2.0. L'app può utilizzare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare il [riferimento al token B2C](active-directory-b2c-reference-tokens.md). |
| refresh\_token\_expires\_in | Tempo massimo di validità per un token di aggiornamento (in secondi). Il token di aggiornamento può tuttavia perdere validità in qualsiasi momento. |

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


<!--

Here is the entire flow for a native  app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png)

-->

## Uso della propria directory B2C

Per provare queste richieste autonomamente, è innanzitutto necessario eseguire questi tre passaggi, quindi sostituire i valori di esempio con i propri:

- [Creare una directory B2C](active-directory-b2c-get-started.md) e utilizzare il nome della directory nelle richieste.
- [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione e un redirect\_uri. Si potrebbe voler includere un **native client** nell'app.
- [Creare i criteri](active-directory-b2c-reference-policies.md) per ottenere i nomi dei criteri.

<!---HONumber=AcomDC_0128_2016-->