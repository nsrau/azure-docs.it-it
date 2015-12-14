
<properties
	pageTitle="Protocollo OAuth di Modello app 2.0 | Microsoft Azure"
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
	ms.date="11/19/2015"
	ms.author="dastrock"/>

# Anteprima di Modello app 2.0 - Protocolli: Flusso del codice di autorizzazione di OAuth 2.0

La concessione del codice di autorizzazione OAuth 2.0 può essere utilizzata nelle app che vengono installate su un dispositivo per ottenere l'accesso alle risorse protette, come l'API web. Usando l'implementazione di OAuth 2.0 definita in Modello app 2.0, è possibile aggiungere i criteri e l'API di accesso alle applicazioni desktop e mobili. Questa guida è indipendente dal linguaggio, e descrive come inviare e ricevere messaggi HTTP senza utilizzare une delle nostre librerie open source.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749). Viene usato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui [app Web](active-directory-v2-flows.md#web-apps) e [app native](active-directory-v2-flows.md#mobile-and-native-apps). Tale flusso consente alle app di acquisire in modo sicuro i token di accesso che possono essere usati per accedere alle risorse protette tramite Modello app 2.0.



## Richiedere un codice di autorizzazione
Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize`. In questa richiesta, il client indica le autorizzazioni che deve acquisire dall'utente:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		  // Your registered Application Id
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob 	  // Your registered Redirect Uri, url encoded
&response_mode=query							        // 'query', 'form_post', or 'fragment'
&scope=											     // See table below
openid%20
offline_access%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				   // Any value provided by your app
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| response\_type | obbligatorio | Deve includere `code` per il flusso del codice di autorizzazione. |
| redirect\_uri | obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Un valore di ambito singolo indica all'endpoint 2.0 che sono state richieste sia la risorsa che le autorizzazioni per tale risorsa. Il formato degli ambiti è `<app identifier URI>/<scope value>`. Nell'esempio precedente viene usato l'identificatore app per l'API Graph di Azure AD, `https://graph.windows.net`, e vengono richieste due autorizzazioni: `directory.read` e `directory.write`. Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento [Anteprima di Modello app 2.0: Ambiti, autorizzazioni e consenso](active-directory-v2-scopes.md). |
| response\_mode | consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. Può essere 'query', 'form\_post' o 'fragment'.
| state | consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | facoltativo | Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi in questa fase sono "login", "none" e "consent". `prompt=login` impone all'utente di immettere le credenziali per la richiesta, negando l'accesso Single Sign-On, mentre `prompt=none` provoca l'effetto contrario, ovvero garantisce che all'utente non venga presentato alcun prompt interattivo. Se la richiesta non può essere completata automaticamente tramite Single-Sign-On, l'endpoint 2.0 restituirà un errore. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth in seguito all'accesso dell'utente, chiedendo all'utente di concedere le autorizzazioni per l'app. |
| login\_hint | Facoltativa | Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint 2.0 assicura inoltre che l'utente abbia fornito il consenso per le autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant vengono fornite qui](active-directory-v2-scopes.md).

Dopo che l'utente è stato autenticato e ha fornito il consenso, l'endpoint 2.0 restituisce una risposta all'app in corrispondenza dell'URI `redirect_uri`, usando il metodo specificato nel parametro `response_mode`.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												      // the value provided in the request
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| code | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve, in genere scadono dopo circa 10 minuti. |
| session\_state | Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere trattato come un valore opaco passato senza alcun controllo. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

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
Dopo aver ottenuto un codice di autorizzazione e l'autorizzazione dell'utente, è ora possibile riscattare il valore `code` per un oggetto `access_token` alla risorsa desiderata, inviando una richiesta `POST` all'endpoint `/token`:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| client\_id | obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| grant\_type | obbligatorio | Deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Gli ambiti richiesti in questa sezione devono essere equivalenti agli ambiti richiesti nella prima sezione o un sottoinsieme di questi ultimi. Se gli ambiti specificati in questa richiesta si estendono su più server di risorse, l'endpoint 2.0 restituirà un token per la risorsa specificata nel primo ambito. Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](active-directory-v2-scopes.md). |
| code | obbligatorio | Codice di autorizzazione acquisito durante la prima sezione del flusso. |
| client\_secret | obbligatorio per app Web | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. È consigliabile non usarlo in un'app nativa, perché i segreti client non possono essere archiviati in modo affidabile nei dispositivi. Il segreto è obbligatorio per le app Web e le API Web che possono archiviare in modo sicuro il segreto client sul lato server. |

Una risposta token con esito positivo ha un aspetto simile al seguente:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
	"id_token_expires_in": "3599"
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| access\_token | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| token\_type | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| expires\_in | Validità del token di accesso (espressa in secondi). |
| scope | Ambiti per i quali il token di accesso è valido. |
| refresh\_token | Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token di accesso aggiuntivi dopo la scadenza del token di accesso corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare l'argomento [Anteprima Azure AD B2C: Riferimento al Token](active-directory-v2-tokens.md). |
| id\_token | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sui token ID, vedere l'argomento [Anteprima di Modello app 2.0: Riferimento al token](active-directory-v2-tokens.md). |
| id\_token\_expires\_in | Validità dell'id\_token (in secondi). |


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

## Usare il token di accesso
Dopo aver ottenuto un `access_token` è possibile usarlo in richieste alle API Web includendolo nell'intestazione `Authorization`:

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Aggiornare il token di accesso
I token di accesso hanno breve durata ed è quindi necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. A tale scopo, inviare un'altra richiesta `POST` all'endpoint `/token`, specificando il `refresh_token` anziché il valore `code`:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | -------- |
| client\_id | obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| grant\_type | obbligatorio | Deve essere `refresh_token` per questa sezione del flusso del codice di autorizzazione. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Gli ambiti richiesti in questa sezione devono essere equivalenti agli ambiti richiesti nella sezione di richiesta del codice di autorizzazione originale o un sottoinsieme di questi ultimi. Se gli ambiti specificati in questa richiesta si estendono su più server di risorse, l'endpoint 2.0 restituirà un token per la risorsa specificata nel primo ambito. Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](active-directory-v2-scopes.md). |
| refresh\_token | obbligatorio | Token di aggiornamento acquisito durante la seconda sezione del flusso. |
| client\_secret | obbligatorio per app Web | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. È consigliabile non usarlo in un'app nativa, perché i segreti client non possono essere archiviati in modo affidabile nei dispositivi. Il segreto è obbligatorio per le app Web e le API Web che possono archiviare in modo sicuro il segreto client sul lato server. |

Una risposta token con esito positivo ha un aspetto simile al seguente:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
	"id_token_expires_in": "3599"
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| access\_token | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| token\_type | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| expires\_in | Validità del token di accesso (espressa in secondi). |
| scope | Ambiti per i quali il token di accesso è valido. |
| refresh\_token | Nuovo token di aggiornamento di OAuth 2.0. È necessario sostituire il token di aggiornamento precedente con quello appena acquisito, per garantire che i token di aggiornamento rimangano validi il più a lungo possibile. |
| id\_token | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sui token ID, vedere l'argomento [Anteprima di Modello app 2.0: Riferimento al token](active-directory-v2-tokens.md). |
| id\_token\_expires\_in | Validità dell'id\_token (in secondi). |

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

## Riepilogo
In generale, l'intero flusso di autenticazione per un'applicazione nativa/mobile ha un aspetto analogo al seguente:

![Flusso del codice di autenticazione di OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

<!---HONumber=AcomDC_1203_2015-->