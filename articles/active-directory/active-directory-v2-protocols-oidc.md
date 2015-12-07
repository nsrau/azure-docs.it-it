
<properties
	pageTitle="Protocollo OpenID Connect di Modello app 2.0 | Microsoft Azure"
	description="Creazione di applicazioni Web utilizzando l'implementazione di Azure AD del protocollo di autenticazione OpenID Connect."
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

# Anteprima di Modello app 2.0: protocolli - OpenID Connect
OpenID Connect è un protocollo di autenticazione basato su OAuth 2.0 utilizzabile per far accedere in sicurezza gli utenti nelle applicazioni Web. Usando l'implementazione di OpenID Connect definita in Modello app 2.0, è possibile aggiungere le informazioni di accesso e l'API di accesso alle applicazioni basate sul Web. Questa guida illustra come effettuare questa operazione in modo indipendente dal linguaggio, descrivendo come inviare e ricevere messaggi HTTP senza utilizzare le nostre librerie open-source.

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 da usare come protocollo di *autenticazione* per consentire di eseguire l'accesso single sign-on tramite OAuth. Introduce il concetto di un `id_token`, ovvero un token di sicurezza che consente al client di verificare l'identità dell'utente e ottenere informazioni di base sul profilo dell'utente. Poiché estende OAuth 2.0, consente anche alle applicazioni di acquisire in modo sicuro **access\_token**, utilizzabili per accedere alle risorse protette da un [server di autorizzazione](active-directory-v2-protocols.md#the-basics). Si consiglia OpenID Connect per la compilazione di un'[applicazione Web](active-directory-v2-flows.md#web-apps) ospitata su un server e accessibile da browser.

## Inviare la richiesta di accesso
Quando l'app Web deve autenticare l'utente, può indirizzarlo all'endpoint `/authorize`. Questa richiesta è simile a quella della prima parte di [Flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols-oauth-code.md), con alcune importanti differenze:

- La richiesta deve includere l'ambito `openid` nel parametro `scope`.
- Il parametro `response_type` deve includere `id_token`
- La richiesta deve includere il parametro `nonce`.

```
// Line breaks for legibility only

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
| response\_type | obbligatorio | Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere altri parametri response\_type, ad esempio `code`. |
| redirect\_uri | obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Per OpenID Connect, deve includere l'ambito `openid` che esegue la conversione all'autorizzazione per l'accesso e la lettura del profilo nell'interfaccia utente di consenso. È anche possibile includere in questa richiesta altri ambiti per richiedere il consenso. |
| nonce | Obbligatoria | Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| response\_mode | consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. Può essere 'query', 'form\_post' o 'fragment'.  
| state | consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | facoltativo | Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi in questa fase sono "login", "none" e "consent". `prompt=login` impone all'utente di immettere le credenziali per la richiesta, negando l'accesso Single Sign-On, mentre `prompt=none` provoca l'effetto contrario, ovvero garantisce che all'utente non venga presentato alcun prompt interattivo. Se la richiesta non può essere completata automaticamente tramite Single-Sign-On, l'endpoint 2.0 restituirà un errore. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth in seguito all'accesso dell'utente, chiedendo all'utente di concedere le autorizzazioni per l'app. |
| login\_hint | Facoltativa | Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint 2.0 assicura anche che l'utente abbia fornito il consenso per le autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Questo articolo contiene informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant](active-directory-v2-scopes.md).

Dopo che l'utente viene autenticato e fornisce il consenso, l'endpoint 2.0 restituisce una risposta all'app nell'URI `redirect_uri`, usando il metodo specificato nel parametro `response_mode`.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&id_token_expires_in=3600

```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id\_token | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Informazioni dettagliate sui token ID e il relativo contenuto sono incluse nel [riferimento al token dell'endpoint 2.0](active-directory-v2-tokens.md). |
| session\_state | Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere trattato come un valore opaco passato senza alcun controllo. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |
| id\_token\_expires\_in | Validità del token ID (in secondi). |


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

## Convalidare il token ID
La semplice ricezione di un token ID non è sufficiente per autenticare l'utente. È necessario convalidare la firma del token ID e verificare le attestazioni nel token per i requisiti dell'app. L'endpoint 2.0 usa i [token Web JSON](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

Modello app 2.0 dispone di un endpoint di metadati OpenID Connect, che consente a un'app di recuperare informazioni su Modello app 2.0 in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. L'endpoint di metadati contiene un documento JSON disponibile sul sito Web all'indirizzo:

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Una delle proprietà del documento di configurazione è `jwks_uri`, il cui valore per Modello app 2.0 è:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

È possibile usare le chiavi pubbliche RSA256 che si trovano in questo endpoint per convalidare la firma del token ID. Sono presenti più chiavi elencate in questo endpoint in qualsiasi momento, ognuna identificata da un valore `kid`. L'intestazione del token ID contiene inoltre un'attestazione `kid`, che indica quali di queste chiavi sono state usate per firmare il token ID.

Per altre informazioni, ad esempio relative alla [convalida del token](active-directory-v2-tokens.md#validating-tokens) e al [rollover della chiave di firma](active-directory-v2-tokens.md#validating-tokens), vedere il [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md). <!--TODO: Improve the information on this-->

Dopo aver convalidato la firma del token ID, è necessario verificare alcune attestazioni:

- È necessario convalidare l'attestazione `nonce` per impedire attacchi di riproduzione del token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
- È necessario convalidare l'attestazione `aud` per garantire che il token ID sia stato emesso per l'app. Il valore deve essere il `client_id` dell'app.
- È necessario convalidare le attestazioni `iat` e `exp` per garantire che il token ID non sia scaduto.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

- Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
- Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
- Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Per altre informazioni sulle attestazioni in un token ID, vedere il [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md).

Dopo aver convalidato completamente il token ID, è possibile avviare una sessione con l'utente e usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## Inviare una richiesta di disconnessione

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

## Diagramma di riepilogo sull'accesso
Il flusso di accesso di base include i passaggi seguenti:

![Corsie di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## Ottenere i token di accesso
Molte app Web non solo eseguono l'accesso dell'utente, ma accedono anche a un servizio Web per conto dell'utente tramite OAuth. Questo scenario combina OpenID Connect per l'autenticazione dell'utente e contemporaneamente acquisisce un codice di autorizzazione che può essere usato per ottenere i token di accesso mediante il flusso del codice di autorizzazione di OAuth. Per acquisire i token di accesso, sarà necessario modificare leggermente la richiesta di accesso precedente:


```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20										 
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

Includendo gli ambiti di autorizzazione nella richiesta e usando `response_type=code+id_token`, l'endpoint 2.0 verificherà che l'utente abbia dato il consenso alle autorizzazioni indicate nel parametro di query `scope` e restituirà all'app un codice di autorizzazione da scambiare con un token di accesso.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&id_token_expires_in=3599

```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id\_token | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Informazioni dettagliate sui token ID e il relativo contenuto sono incluse nel [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md). |
| code | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve, in genere scadono dopo circa 10 minuti. |
| session\_state | Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere trattato come un valore opaco passato senza alcun controllo. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |
| id\_token\_expires\_in | Validità del token ID (in secondi). |

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

Una volta ottenuti un `code` e un `id_token` di autorizzazione, è possibile far accedere l'utente e ottenere i token di accesso per suo conto. Per far accedere l'utente, è necessario convalidare l'`id_token` esattamente come descritto [sopra](#validating-the-id-token). Per ottenere i token di accesso, è possibile seguire i passaggi descritti nella [documentazione del protocollo OAuth](active-directory-v2-protocols-oidc.md#request-an-access-token).

## Diagramma di riepilogo sull'acquisizione dei token

Come riferimento, il flusso completo di acquisizione delle informazioni di accesso e dei token di accesso di OpenID è simile al seguente:

![Corsie di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

<!---HONumber=AcomDC_1125_2015-->