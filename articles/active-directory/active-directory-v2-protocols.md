<properties
	pageTitle="Modello app 2.0 | Microsoft Azure"
	description="Protocolli supportati dall'anteprima pubblica di Modello app 2.0 di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Anteprima di Modello app 2.0: Protocolli OAuth 2.0 e OpenID Connect

Modello app 2.0 fornisce l'identità come servizio per le app grazie al supporto di protocolli standard del settore, OpenID Connect e OAuth 2.0. Anche se il servizio è conforme agli standard, possono esistere sottili differenze tra le implementazioni di questi protocolli. Le informazioni in questo argomento sono utili se si sceglie di scrivere codice inviando e gestendo direttamente le richieste HTTP, anziché usando una delle librerie open source. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

## Token
L'implementazione di OAuth 2.0 e OpenID Connect in Modello app 2.0 fa un uso intensivo dei token di connessione, inclusi quelli rappresentati come JWT (Token Web JSON). Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, per "portatore" si intende qualsiasi parte che sia in grado di presentare il token. Anche se il rilascio del token di connessione è condizionato dal completamento del processo di autenticazione in Azure AD, se non vengono adottate le misure necessarie per proteggere il token durante la trasmissione e l'archiviazione, è possibile che venga intercettato e usato da parti non autorizzate. Molti token di sicurezza hanno meccanismi integrati per prevenire l'uso non autorizzato, ma i token di connessione ne sono sprovvisti e devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza essere protetto, un utente malintenzionato potrebbe usare un attacco "man in the middle" per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](http://tools.ietf.org/html/rfc6750).

Altri dettagli sui diversi tipi di token usati in Modello app 2.0 sono disponibili nel [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md).

## Nozioni di base
Ogni app che usa Modello app 2.0 dovrà essere registrata in [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Il processo di registrazione raccoglie e assegna all'app alcuni valori:

- Un **ID applicazione** che identifica l'app in modo univoco
- Un **URI di reindirizzamento** o un **identificatore di pacchetto** che possono essere usati per indirizzare le risposte all'app
- Altri valori specifici dello scenario Per informazioni dettagliate, consultare l'argomento relativo alla [registrazione di un'app](active-directory-v2-app-registration.md).

Dopo la registrazione, l'app comunica con Azure AD inviando richieste all'endpoint 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

In quasi tutti i flussi di OAuth e OpenID Connect, sono coinvolte nello scambio quattro parti:

![Ruoli di OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- Il **server di autorizzazione** è l'endpoint 2.0. Garantisce l'identità dell'utente, concede e revoca l'accesso alle risorse e rilascia i token. Agisce anche come provider di identità: gestisce in modo sicuro le informazioni dell'utente, l'accesso e le relazioni di trust tra le parti di un flusso.
- Il **proprietario della risorsa** è in genere l'utente finale. È l'entità che possiede i dati e consente a terze parti di accedere a tali dati o risorse.
- Il **client OAuth** è l'app, identificata dall'ID applicazione. In genere è l'entità con cui interagisce l'utente finale e che richiede i token dal server di autorizzazione. Per accedere alla risorsa, il client deve ottenere l'autorizzazione da parte del proprietario della risorsa.
- Il **server della risorsa** è la posizione in cui si trova la risorsa o i dati. Consente al server di autorizzazione di autenticare e autorizzare il client OAuth in modo sicuro e usa i token di accesso Bearer per assicurarsi che sia garantito l'accesso a una risorsa.

## Flusso del codice di autorizzazione di OAuth 2.0
Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749). Viene usato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui [app Web](active-directory-v2-flows.md#web-apps) e [app native](active-directory-v2-flows.md#mobile-and-native-apps). Tale flusso consente alle app di acquisire in modo sicuro i token di accesso che possono essere usati per accedere alle risorse protette tramite Modello app 2.0.

Di seguito viene descritto l'intero flusso di un'app nativa. Ogni richiesta è descritta in dettaglio nelle sezioni seguenti:![Flusso del codice di autenticazione di OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

#### Richiedere un codice di autorizzazione
Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize`. In questa richiesta, il client indica le autorizzazioni che deve acquisire dall'utente:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=											   // See table below for scope parameter details.
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value provided by your app
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| response\_type | obbligatorio | Deve includere `code` per il flusso del codice di autorizzazione. |
| redirect\_uri | obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Un valore di ambito singolo indica all'endpoint 2.0 che sono state richieste sia la risorsa che le autorizzazioni per tale risorsa. Il formato degli ambiti è `<app identifier URI>/<scope value>`. Nell'esempio precedente viene usato l'identificatore dell'app per l'API Graph di Azure AD, `https://graph.windows.net`, e vengono richieste due autorizzazioni: `directory.read` e `directory.write`. Per una spiegazione dettagliata degli ambiti, fare riferimento a c. |
| response\_mode | consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. Può essere 'query', 'form\_post' o 'fragment'.
| state | consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | facoltativo | Indica il tipo di interazione obbligatoria dell'utente. L'unico valore valido in questa fase è 'login', che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non ha effetto. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint 2.0 assicura inoltre che l'utente ha fornito il consenso per le autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Questo articolo contiene informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant](active-directory-v2-scopes.md).

Dopo che l'utente viene autenticato e fornisce il consenso, l'endpoint 2.0 restituisce una risposta all'app nell'URI `redirect_uri`, usando il metodo specificato nel parametro `response_mode`.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET https://localhost/myapp/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  							// the value provided in the request
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| code | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve, in genere scadono dopo circa 10 minuti. |
| session\_state | Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere trattato come un valore opaco passato senza alcun controllo. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'app possa gestirle adeguatamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

#### Richiedere un token di accesso
Dopo aver ottenuto un codice di autorizzazione e l'autorizzazione dell'utente, è possibile riscattare il valore `code` per un token di accesso alla risorsa desiderata, inviando una richiesta `POST` all'endpoint `/token`:

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
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| access\_token | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| token\_type | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| expires\_in | Validità del token di accesso (espressa in secondi). |
| expires\_on | Scadenza del token di accesso. La data viene rappresentata come numero di secondi dal valore epoch time. |
| scope | Ambiti per i quali il token di accesso è valido. |
| refresh\_token | Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token di accesso aggiuntivi dopo la scadenza del token di accesso corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare il [riferimento al token 2.0](active-directory-v2-tokens.md). |
| id\_token | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sui token ID, vedere il [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md). |

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

#### Usare il token di accesso
Dopo aver ottenuto un token di accesso è possibile usarlo in richieste alle API Web includendolo nell'intestazione `Authorization`:

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

#### Aggiornare il token di accesso
I token di accesso hanno breve durata ed è quindi necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. A tale scopo, inviare un'altra richiesta `POST` all'endpoint `/authorize`, specificando il token di aggiornamento anziché il valore `code`:

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
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| access\_token | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| token\_type | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| expires\_in | Validità del token di accesso (espressa in secondi). |
| expires\_on | Scadenza del token di accesso. La data viene rappresentata come numero di secondi dal valore epoch time. |
| scope | Ambiti per i quali il token di accesso è valido. |
| refresh\_token | Nuovo token di aggiornamento di OAuth 2.0. È necessario sostituire il token di aggiornamento precedente con quello appena acquisito, per garantire che i token di aggiornamento rimangano validi il più a lungo possibile. |
| id\_token | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sui token ID, vedere il [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md). |

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






## Flusso di accesso di OpenID Connect
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 da usare come protocollo di *autenticazione* per consentire di eseguire l'accesso Single Sign-On tramite OAuth. Introduce il concetto di un token ID, ovvero un token di sicurezza che consente al client di verificare l'identità dell'utente e ottenere informazioni di profilo di base sull'utente.

OpenID Connect per Modello app 2.0 è il metodo consigliato per implementare l'accesso per un'[app Web](active-directory-v2-flows.md#web-apps). Il flusso di accesso di base include i passaggi seguenti:

![Corsie di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

#### Inviare la richiesta di accesso
Quando l'app Web deve autenticare l'utente, può indirizzarlo all'endpoint `/authorize`. Questa richiesta è simile alla prima sezione del [flusso del codice di autorizzazione di OAuth 2.0](#oauth2-authorization-code-flow) con alcune differenze importanti: - La richiesta deve includere l'ambito `openid` nel parametro `scope`. - Il parametro `response_type` deve includere `id_token` - La richiesta deve includere il parametro `nonce`.

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid										 // Translates to the 'Read your profile' permission
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| response\_type | obbligatorio | Deve includere `id_token` per l'accesso a OpenID Connect. Può inoltre includere altri tipi di risposta, come descritto nella sezione [OpenID Connect con il flusso del codice di OAuth](#OpenID-Connect-with-OAuth-Code-Flow). |
| redirect\_uri | obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Per OpenID Connect, deve includere l'ambito `openid` che esegue la conversione all'autorizzazione per l'accesso e la lettura del profilo nell'interfaccia utente di consenso. È possibile includere anche altri ambiti in questa richiesta di consenso, come descritto nella sezione [OpenID Connect con il flusso del codice di OAuth](#OpenID-Connect-with-OAuth-Code-Flow). |
| nonce | obbligatorio | Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| response\_mode | consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. Può essere 'query', 'form\_post' o 'fragment'.  
| state | consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | facoltativo | Indica il tipo di interazione obbligatoria dell'utente. L'unico valore valido in questa fase è 'login', che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non ha effetto. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint 2.0 assicura inoltre che l'utente ha fornito il consenso per le autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Questo articolo contiene informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant](active-directory-v2-scopes.md).

Dopo che l'utente viene autenticato e fornisce il consenso, l'endpoint 2.0 restituisce una risposta all'app nell'URI `redirect_uri`, usando il metodo specificato nel parametro `response_mode`.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id\_token | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Informazioni dettagliate sui token ID e il relativo contenuto sono incluse nel [riferimento al token dell'endpoint 2.0](active-directory-v2-tokens.md). |
| session\_state | Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere trattato come un valore opaco passato senza alcun controllo. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'app possa gestirle adeguatamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

#### Convalidare il token ID
La semplice ricezione di un token ID non è sufficiente per autenticare l'utente. È necessario convalidare la firma del token ID e verificare le attestazioni nel token per i requisiti dell'app. L'endpoint 2.0 usa i [token Web JSON](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

Modello app 2.0 dispone di un endpoint di metadati OpenID Connect, che consente a un'app di recuperare informazioni su Modello app 2.0 in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. L'endpoint di metadati contiene un documento JSON disponibile sul sito Web all'indirizzo:

`https://login.microsoftonline.com/common/v2.0/.well-known/configuration`

Una delle proprietà del documento di configurazione è `jwks_uri`, il cui valore per Modello app 2.0 è:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

È possibile usare le chiavi pubbliche RSA256 che si trovano in questo endpoint per convalidare la firma del token ID. Sono presenti più chiavi elencate in questo endpoint in qualsiasi momento, ognuna identificata da un valore `kid`. L'intestazione del token ID contiene inoltre un'attestazione `kid`, che indica quali di queste chiavi sono state usate per firmare il token ID.

Per altre informazioni, ad esempio relative alla [convalida del token](active-directory-v2-tokens.md#validating-tokens) e al [rollover della chiave di firma](active-directory-v2-tokens.md#validating-tokens), vedere il [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md).<!--TODO: Improve the information on this-->

Dopo aver convalidato la firma del token ID, è necessario verificare alcune attestazioni:

- È necessario convalidare l'attestazione `nonce` per impedire attacchi di riproduzione del token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
- È necessario convalidare l'attestazione `aud` per garantire che il token ID sia stato emesso per l'app. Il valore deve essere l'ID client dell'app.
- È necessario convalidare le attestazioni `iat` e `exp` per garantire che il token ID non sia scaduto.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

- Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
- Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
- Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Per altre informazioni sulle attestazioni in un token ID, vedere il [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md).

Dopo aver convalidato completamente il token ID, è possibile avviare una sessione con l'utente e usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

#### Inviare una richiesta di disconnessione

L'endpoint `end_session_endpoint` di OpenID Connect non è attualmente supportato dall'anteprima di Modello app 2.0. Ciò significa che l'app non può inviare una richiesta all'endpoint 2.0 per terminare una sessione utente e cancellare i cookie impostati dall'endpoint 2.0. Per disconnettere un utente, l'app può semplicemente terminare una sessione con l'utente e lasciare la sessione dell'utente con l'endpoint 2.0 invariato. Al successivo accesso, l'utente visualizzerà una pagina per la scelta dell'account con l'elenco degli account di accesso attivi. In tale pagina, l'utente può scegliere di disconnettersi da qualsiasi account, terminando la sessione con l'endpoint 2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## OpenID Connect con il flusso del codice di OAuth
Molte app Web eseguono l'accesso dell'utente e quindi accedono a un servizio Web per conto dell'utente tramite OAuth. Questo scenario consente di combinare le due sezioni precedenti: usa OpenID Connect per l'autenticazione dell'utente e contemporaneamente acquisisce un codice di autorizzazione che può essere usato per ottenere i token di accesso mediante il flusso del codice di autorizzazione di OAuth.

![Corsie di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Questo flusso differisce dalle sezioni precedenti solo nella modalità di invio della richiesta di accesso.

#### Inviare la richiesta di accesso
Quando l'app Web deve autenticare l'utente e ottenere le autorizzazioni necessarie per accedere alle risorse, può indirizzare l'utente all'endpoint `/authorize`. In questo caso, l'app deve richiedere nella risposta sia un token ID che un valore `code`:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20										 // Include both 'openid' and scopes your app needs  
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | ----------------- |
| client\_id | obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| response\_type | obbligatorio | Deve includere `id_token` e `code` per questo scenario. |
| redirect\_uri | obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Per OpenID Connect, deve includere l'ambito `openid` che esegue la conversione all'autorizzazione per l'accesso e la lettura del profilo nell'interfaccia utente di consenso. È necessario includere anche gli ambiti relativi alle risorse per le quali l'app richiede l'accesso. Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](active-directory-v2-scopes.md). |
| nonce | obbligatorio | Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| response\_mode | consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. Può essere 'query', 'form\_post' o 'fragment'.  
| state | consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | facoltativo | Indica il tipo di interazione obbligatoria dell'utente. L'unico valore valido in questa fase è 'login', che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non ha effetto. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint 2.0 assicura inoltre che l'utente ha fornito il consenso per le autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Questo articolo contiene informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant](active-directory-v2-scopes.md).

Dopo che l'utente viene autenticato e fornisce il consenso, l'endpoint 2.0 restituisce una risposta all'app nell'URI `redirect_uri`, usando il metodo specificato nel parametro `response_mode`.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id\_token | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Informazioni dettagliate sui token ID e il relativo contenuto sono incluse nel [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md). |
| code | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve, in genere scadono dopo circa 10 minuti. |
| session\_state | Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere trattato come un valore opaco passato senza alcun controllo. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'app possa gestirle adeguatamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

#### Convalidare il token ID
Questo processo è identico a quello descritto in precedenza nella sezione [Flusso di accesso di OpenID Connect](#OpenID-Connect-Sign-In-Flow).

#### Inviare una richiesta di disconnessione
Questo processo è identico a quello descritto in precedenza nella sezione [Flusso di accesso di OpenID Connect](#OpenID-Connect-Sign-In-Flow).

#### Richiedere un token di accesso
Questo processo è identico a quello descritto in precedenza nella sezione [Flusso del codice di autorizzazione di OAuth 2.0](#oauth2-authorization-code-flow).

#### Usare il token di accesso
Questo processo è identico a quello descritto in precedenza nella sezione [Flusso del codice di autorizzazione di OAuth 2.0](#oauth2-authorization-code-flow).

#### Aggiornare il token di accesso
Questo processo è identico a quello descritto in precedenza nella sezione [Flusso del codice di autorizzazione di OAuth 2.0](#oauth2-authorization-code-flow).





## Flusso di concessione delle credenziali client di OAuth 2.0
La concessione delle credenziali client di OAuth 2.0 è descritta nella [specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4). È utile per processi a esecuzione prolungata e altri scenari da server a server, in cui l'app può eseguire l'autenticazione a una risorsa mediante l'identità dell'applicazione, piuttosto che mediante un'identità delegata dell'utente.

Questo flusso non è attualmente supportato dall'anteprima di Modello app 2.0. Per vedere come funziona nel servizio Azure AD disponibile a livello generale, vedere [questo esempio di codice di Azure AD](https://github/com/AzureADSamples/Daemon-DotNet).

## Flusso implicito di OAuth 2.0
Il flusso implicito di OAuth 2.0 è descritto nella [specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). Viene spesso usato per le app a pagina singola/JavaScript eseguite in un browser.

Questo flusso non è attualmente supportato dall'anteprima di Modello app 2.0. Per vedere come funziona nel servizio Azure AD disponibile a livello generale, vedere [questo esempio di codice di Azure AD](active-directory-devquickstarts-angular.md).

## Concessione delle credenziali di tipo password del proprietario della risorsa di OAuth 2.0
La concessione delle credenziali di tipo password del proprietario della risorsa di OAuth 2.0 è descritta nella [specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.3). Consente a un'app di acquisire token di accesso specificando nome utente e password in una richiesta di token.

Questo flusso non è attualmente supportato dall'anteprima di Modello app 2.0. Per vedere come funziona nel servizio Azure AD disponibile a livello generale, vedere [questo esempio di codice di Azure AD](https://github.com/AzureADSamples/NativeClient-Headless-DotNet).

## Flusso On-Behalf-Of di OAuth 2.0
Il flusso On-Behalf-Of o la concessione delle credenziali di connessione JWT è descritta nella [bozza sulla concessione delle estensioni di OAuth 2.0](https://tools.ietf.org/html/draft-ietf-oauth-jwt-bearer-12). Consente a un'API Web che riceve un token di accesso di usare tale token come credenziale per l'acquisizione di token di accesso ad altre risorse. In questo modo un'API Web può chiamare in modo sicuro un'altra API Web downstream.

Questo flusso non è attualmente supportato dall'anteprima di Modello app 2.0. Per vedere come funziona nel servizio Azure AD disponibile a livello generale, vedere [questo esempio di codice di Azure AD](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=August15_HO7-->