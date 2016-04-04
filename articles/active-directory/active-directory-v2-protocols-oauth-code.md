

<properties
	pageTitle="Flusso del codice di autorizzazione OAuth 2.0 di Azure AD | Microsoft Azure"
	description="Creazione di applicazioni Web usando l'implementazione del protocollo di autenticazione OAuth 2.0 definita in Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Protocolli della versione 2.0: flusso del codice di autorizzazione di OAuth 2.0

La concessione del codice di autorizzazione OAuth 2.0 può essere utilizzata nelle app che vengono installate su un dispositivo per ottenere l'accesso alle risorse protette, come l'API web. Usando l'implementazione di OAuth 2.0 definita in Modello app 2.0, è possibile aggiungere i criteri e l'API di accesso alle applicazioni desktop e mobili. Questa guida è indipendente dal linguaggio, e descrive come inviare e ricevere messaggi HTTP senza utilizzare une delle nostre librerie open source.

<!-- TODO: Need link to libraries -->	

> [AZURE.NOTE]
	Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749). Viene usato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui [app Web](active-directory-v2-flows.md#web-apps) e [app native](active-directory-v2-flows.md#mobile-and-native-apps). Tale flusso consente alle app di acquisire in modo sicuro i token di accesso che possono essere usati per accedere alle risorse protette tramite endpoint v2.0.

## Diagramma di protocollo
In generale, l'intero flusso di autenticazione per un'applicazione nativa/mobile ha un aspetto analogo al seguente:

![Flusso del codice di autenticazione di OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Richiedere un codice di autorizzazione
Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize`. In questa richiesta, il client indica le autorizzazioni che deve acquisire dall'utente:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] Provare a incollare questa richiesta in un browser.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatoria | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client\_id | Obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| response\_type | Obbligatorio | Deve includere `code` per il flusso del codice di autorizzazione. |
| redirect\_uri | consigliato | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. Per le app native e le app per dispositivi mobili è necessario usare il valore predefinito `urn:ietf:wg:oauth:2.0:oob`. |
| scope | Obbligatorio | Elenco separato da spazi di [ambiti](active-directory-v2-scopes.md) a cui si vuole che l'utente dia il consenso. |
| response\_mode | Consigliato | Specifica il metodo da usare per restituire il token risultante all'app. Può essere `query` o `form_post`. |
| state | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](http://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | Facoltativo | Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi in questa fase sono "login", "none" e "consent". `prompt=login` impone all'utente di immettere le credenziali per la richiesta, negando l'accesso Single Sign-On, mentre `prompt=none` provoca l'effetto contrario, ovvero garantisce che all'utente non venga presentato alcun prompt interattivo. Se la richiesta non può essere completata automaticamente tramite Single-Sign-On, l'endpoint 2.0 restituirà un errore. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth in seguito all'accesso dell'utente, chiedendo all'utente di concedere le autorizzazioni per l'app. |
| login\_hint | Facoltativo | Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`. |
| domain\_hint | Facoltativo | Può essere uno di `consumers` o `organizations`. Se incluso, non verrà eseguito il processo di individuazione basata sulla posta elettronica a cui viene sottoposto l'utente nella pagina di accesso della versione 2.0. Questo comporta un'esperienza utente leggermente semplificata. Le app usano spesso questo parametro durante la riautenticazione, estraendo `tid` da un accesso precedente. Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`. In caso contrario, usare `domain_hint=organizations`. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint 2.0 assicura anche che l'utente abbia fornito il consenso per le autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Questo articolo contiene informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant](active-directory-v2-scopes.md).

Dopo che l'utente viene autenticato e fornisce il consenso, l'endpoint 2.0 restituisce una risposta all'app nell'URI `redirect_uri`, usando il metodo specificato nel parametro `response_mode`.

#### Risposta con esito positivo
Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| code | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve, in genere scadono dopo circa 10 minuti. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'app possa gestirle adeguatamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## Richiedere un token di accesso
Dopo aver ottenuto un codice di autorizzazione e l'autorizzazione dell'utente, è possibile riscattare il valore `code` per un token di accesso alla risorsa desiderata, inviando una richiesta `POST` all'endpoint `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Provare a importare il comando curl seguente in postman. Perché abbia esito positivo è necessario sostituire `code` con il proprio codice.

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=authorization_code' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```


| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | Obbligatoria | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client\_id | Obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| grant\_type | Obbligatorio | Deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| scope | Obbligatorio | Elenco di ambiti separati da spazi. Gli ambiti richiesti in questa sezione devono essere equivalenti agli ambiti richiesti nella prima sezione o un sottoinsieme di questi ultimi. Se gli ambiti specificati in questa richiesta si estendono su più server di risorse, l'endpoint 2.0 restituirà un token per la risorsa specificata nel primo ambito. Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](active-directory-v2-scopes.md). |
| code | Obbligatorio | Codice di autorizzazione acquisito durante la prima sezione del flusso. |
| redirect\_uri | Obbligatorio | Stesso valore redirect\_uri usato per acquisire il codice di autorizzazione. |
| client\_secret | Obbligatorio per app Web | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. È consigliabile non usarlo in un'app nativa, perché i segreti client non possono essere archiviati in modo affidabile nei dispositivi. Il segreto è obbligatorio per le app Web e le API Web che possono archiviare in modo sicuro il segreto client sul lato server. |

#### Risposta con esito positivo
Una risposta token con esito positivo ha un aspetto simile al seguente:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| access\_token | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| token\_type | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| expires\_in | Validità del token di accesso (espressa in secondi). |
| scope | Ambiti per i quali il token di accesso è valido. |
| refresh\_token | Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token di accesso aggiuntivi dopo la scadenza del token di accesso corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare l'argomento [Anteprima Azure AD B2C: Riferimento al Token](active-directory-v2-tokens.md). |
| id\_token | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sui token ID, vedere il [riferimento al token dell'endpoint v2.0](active-directory-v2-tokens.md). |

#### Risposta di errore
Le risposte di errore hanno un aspetto simile al seguente:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| error\_codes | Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| timestamp | Ora in cui si è verificato l'errore. |
| trace\_id | Identificatore univoco per la richiesta utile per la diagnostica. |
| correlation\_id | Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |

## Usare il token di accesso
Dopo aver ottenuto un `access_token` è possibile usarlo in richieste alle API Web includendolo nell'intestazione `Authorization`:

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

> [AZURE.TIP] Provare il comando seguente sostituendo il token con il proprio.

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## Aggiornare il token di accesso
I token di accesso hanno breve durata ed è quindi necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. A tale scopo, inviare un'altra richiesta `POST` all'endpoint `/token`, specificando il `refresh_token` anziché il valore `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh	  // NOTE: Only required for web apps
```

> [AZURE.TIP] Provare a importare il comando curl seguente in postman. Perché abbia esito positivo è necessario sostituire refresh\_token con il proprio token.

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=refresh_token' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | -------- |
| tenant | Obbligatoria | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client\_id | Obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| grant\_type | Obbligatorio | Deve essere `refresh_token` per questa sezione del flusso del codice di autorizzazione. |
| scope | Obbligatorio | Elenco di ambiti separati da spazi. Gli ambiti richiesti in questa sezione devono essere equivalenti agli ambiti richiesti nella sezione di richiesta del codice di autorizzazione originale o un sottoinsieme di questi ultimi. Se gli ambiti specificati in questa richiesta si estendono su più server di risorse, l'endpoint 2.0 restituirà un token per la risorsa specificata nel primo ambito. Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](active-directory-v2-scopes.md). |
| refresh\_token | Obbligatorio | Token di aggiornamento acquisito durante la seconda sezione del flusso. |
| redirect\_uri | Obbligatorio | Stesso valore redirect\_uri usato per acquisire il codice di autorizzazione. |
| client\_secret | Obbligatorio per app Web | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. È consigliabile non usarlo in un'app nativa, perché i segreti client non possono essere archiviati in modo affidabile nei dispositivi. Il segreto è obbligatorio per le app Web e le API Web che possono archiviare in modo sicuro il segreto client sul lato server. |

#### Risposta con esito positivo
Una risposta token con esito positivo ha un aspetto simile al seguente:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| access\_token | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| token\_type | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| expires\_in | Validità del token di accesso (espressa in secondi). |
| scope | Ambiti per i quali il token di accesso è valido. |
| refresh\_token | Nuovo token di aggiornamento di OAuth 2.0. È necessario sostituire il token di aggiornamento precedente con quello appena acquisito, per garantire che i token di aggiornamento rimangano validi il più a lungo possibile. |
| id\_token | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sui token ID, vedere il [riferimento al token dell'endpoint v2.0](active-directory-v2-tokens.md). |

#### Risposta di errore
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| error\_codes | Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| timestamp | Ora in cui si è verificato l'errore. |
| trace\_id | Identificatore univoco per la richiesta utile per la diagnostica. |
| correlation\_id | Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |

<!---HONumber=AcomDC_0323_2016-->