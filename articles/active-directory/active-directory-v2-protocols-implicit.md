<properties
	pageTitle="Flusso implicito di Modello app 2.0 | Microsoft Azure"
	description="Compilazione di applicazioni Web con l'implementazione del flusso implicito definito in Azure AD per le app a pagina singola."
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

# Anteprima di Modello app model 2.0: protocolli - Flusso implicito
Con Modello app 2.0 di Azure AD, è possibile far accedere gli utenti alle app a pagina singola con account Microsoft sia personali che aziendali o dell'istituto di istruzione. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche interessanti nell'ambito dell'autenticazione:

- Le caratteristiche di sicurezza di queste app sono considerevolmente diverse da quelle delle tradizionali applicazioni Web basate su server.
- Molti server di autorizzazione e provider di identità non supportano le richieste CORS per motivi di sicurezza ben documentati.
- I reindirizzamenti dei browser a pagina intera dall'app diventano fastidiosi per l'esperienza utente.

Per queste applicazioni (ad esempio, AngularJS, Ember.js, React.js e così via) Azure AD supporta il flusso di concessione implicita di OAuth 2.0. Il flusso implicito è descritto nella [specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). Il principale vantaggio è che consente all'app di ottenere i token da Azure AD senza eseguire uno scambio di credenziali del server back-end. In questo modo l'app può far accedere l'utente, gestire la sessione e ottenere i token per altre API Web, il tutto nel codice JavaScript client. Esistono alcune importanti osservazioni sulla sicurezza da considerare quando si usa il flusso implicito, in particolare per la [rappresentazione client](http://tools.ietf.org/html/rfc6749#section-10.3) e [utente](http://tools.ietf.org/html/rfc6749#section-10.3).

Per usare il flusso implicito e Azure AD per aggiungere l'autenticazione all'app JavaScript, è consigliabile usare la libreria JavaScript open source, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). [Qui](active-directory-appmodel-v2-overview.md#getting-started) sono disponibili alcune esercitazioni introduttive su AngularJS.

Se, tuttavia, si preferisce non usare una libreria nell'app a pagina singola e inviare manualmente i messaggi dei protocolli, seguire questi passaggi generali.

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

## Inviare la richiesta di accesso

Per far accedere l'utente all'app per la prima volta, è possibile inviare una richiesta di autorizzazione [OpenID Connect](active-directory-v2-protocols-oidc.md) e ottenere un `id_token` dall'endpoint versione v2.0:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=openid%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
&state=12345
&nonce=678910
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

Una risposta con esito positivo che usa `response_mode=fragment` ha un aspetto simile al seguente:

```
GET https://localhost/myapp/#
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
GET https://localhost/myapp/#
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

È possibile scegliere di convalidare il parametro `id_token` nel codice client, ma una procedura comune consiste nell'inviare `id_token` a un server back-end dove verrà eseguita la convalida. Sono disponibili diverse librerie per analizzare e convalidare i token JWT. Dopo aver convalidato la firma del token ID, è necessario verificare alcune attestazioni:

- È necessario convalidare l'attestazione `nonce` per impedire attacchi di riproduzione del token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
- È necessario convalidare l'attestazione `aud` per garantire che il token ID sia stato emesso per l'app. Il valore deve essere il `client_id` dell'app.
- È necessario convalidare le attestazioni `iat` e `exp` per garantire che il token ID non sia scaduto.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

- Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
- Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
- Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Per altre informazioni sulle attestazioni in un token ID, vedere il [riferimento al token di Modello app 2.0](active-directory-v2-tokens.md).

Dopo aver convalidato completamente il token ID, è possibile avviare una sessione con l'utente e usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## Ottenere i token di accesso

Dopo avere fatto accedere l'utente all'app a pagina singola, è possibile ottenere i token di accesso per chiamare le API Web protette da Azure AD, ad esempio [Microsoft Graph](https://graph.microsoft.io). Nel normale flusso OpenID Connect/OAuth, per eseguire questa operazione, si effettua una richiesta all'endpoint `/token` 2.0. L'endpoint 2.0 non supporta però le richieste CORS e quindi non è possibile effettuare una chiamata ad AJAX per ottenere e aggiornare i token. È possibile usare invece il flusso implicito in un iframe nascosto per ottenere nuovi token per altre API Web:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read  // The space separated scope(s) you want an access token for
&state=12345
&prompt=none
&login_hint=joe.user@outlook.com
&domain_hint=consumers
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| response\_type | Obbligatoria | Deve essere `token` per il flusso implicito. |
| redirect\_uri | obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | Obbligatoria | Elenco di ambiti separati da spazi necessari nel token di accesso. |
| response\_mode | consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. Può essere 'query', 'form\_post' o 'fragment'.  
| state | consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | Obbligatoria | Qui si usa `prompt=none` per specificare che Azure AD non deve presentare alcuna interfaccia utente. Poiché questa richiesta viene eseguita in un iframe nascosto, è opportuno assicurarsi che, se il token non può essere acquisito automaticamente, Azure AD deve restituire un errore invece di chiedere all'utente di accedere. |
| login\_hint | Obbligatoria | Obbligatorio per l'autenticazione invisibile all'utente. Questo hint consente ad Azure AD di distinguere tra più account se l'utente ha una sessione attiva con più di un account. Il valore specificato qui può essere l'esatto valore ricevuto nell'attestazione `preferred_uesrname` nel parametro id\_token. |
| domain\_hint | Obbligatoria | Obbligatorio per l'autenticazione invisibile all'utente. Per questo flusso, domain\_hint può avere uno di questi due valori: `consumers` o `organizations`. Se il valore dell'attestazione `tid` nel parametro `id_token` ricevuto era esattamente "9188040d-6c67-4c5b-b112-36a304b66dad", usare `consumers` come domain\_hint. In caso contrario, usare `organizations`. |

Con il parametro `prompt=none`, questa richiesta avrà immediatamente esito positivo o negativo e farà tornare all'applicazione. Un risposta con esito positivo verrà inviata all'app all'indirizzo `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`.

Una risposta con esito positivo che usa `response_mode=fragment` ha un aspetto simile al seguente:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the access_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&expires_in=3600
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read

```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| access\_token | Token richiesto dall'app. |
| session\_state | Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere trattato come un valore opaco passato senza alcun controllo. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |
| expires\_in | Validità del token di accesso (espressa in secondi). |
| scope | Ambiti per i quali il token di accesso è valido. |

Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'app possa gestirle adeguatamente. Nel caso di `prompt=none`, un errore previsto sarà:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

Se si riceve questo errore nella richiesta iframe, l'utente deve accedere di nuovo in modo interattivo per recuperare un nuovo token. È possibile scegliere di gestire questa situazione in qualsiasi modo appropriato per l'applicazione.

## Aggiornamento dei token

I token `id_token` e `access_token` scadranno dopo un breve periodo, quindi l'app deve essere preparata per aggiornare regolarmente questi token. Per aggiornare entrambi i tipi di token, è possibile eseguire la stessa richiesta nell'iframe nascosto precedente usando il parametro `prompt=none` per controllare il comportamento di Azure AD. Per ricevere un nuovo `id_token`, assicurarsi di usare `response_type=id_token` e `scope=openid`, oltre al parametro `nonce`.


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

<!---HONumber=AcomDC_1203_2015-->