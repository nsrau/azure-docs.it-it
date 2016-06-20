
<properties
	pageTitle="Protocollo OpenID Connect della versione 2.0 di Azure AD | Microsoft Azure"
	description="Creazione di applicazioni Web usando l'implementazione della versione 2.0 del protocollo di autenticazione OpenID Connect definita in Azure AD."
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
	ms.date="05/31/2016"
	ms.author="dastrock"/>

# Protocolli della versione 2.0: OpenID Connect
OpenID Connect è un protocollo di autenticazione basato su OAuth 2.0 utilizzabile per far accedere in sicurezza gli utenti nelle applicazioni Web. Usando l'implementazione dell'endpoint v2.0 di OpenID Connect, è possibile aggiungere l'accesso e l'API di accesso alle applicazioni basate sul Web. Questa guida illustra come effettuare questa operazione in modo indipendente dal linguaggio, descrivendo come inviare e ricevere messaggi HTTP senza utilizzare le nostre librerie open-source.

> [AZURE.NOTE]
	Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
    
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 da usare come protocollo di *autenticazione* per consentire di eseguire l'accesso Single Sign-On tramite OAuth. Introduce il concetto di un `id_token`, ovvero un token di sicurezza che consente al client di verificare l'identità dell'utente e ottenere informazioni di base sul profilo dell'utente. Poiché estende OAuth 2.0, consente anche alle applicazioni di acquisire in modo sicuro **access\_token**, utilizzabili per accedere alle risorse protette da un [server di autorizzazione](active-directory-v2-protocols.md#the-basics). Si consiglia OpenID Connect per la compilazione di un'[applicazione Web](active-directory-v2-flows.md#web-apps) ospitata su un server e accessibile da browser.

## Diagramma di protocollo - Accesso
Il flusso di accesso di base include i passaggi seguenti: ognuno di essi è descritto in dettaglio di seguito.

![Corsie di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## Inviare la richiesta di accesso
Quando l'app Web deve autenticare l'utente, può indirizzarlo all'endpoint `/authorize`. Questa richiesta è simile a quella della prima parte di [Flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols-oauth-code.md), con alcune importanti differenze:

- La richiesta deve includere l'ambito `openid` nel parametro `scope`.
- Il parametro `response_type` deve includere `id_token`
- La richiesta deve includere il parametro `nonce`.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [AZURE.TIP] Fare clic sul collegamento seguente per eseguire questa richiesta. Dopo l'accesso, il browser deve essere reindirizzato a `https://localhost/myapp/` con un `id_token` nella barra degli indirizzi. Si noti che questa richiesta utilizza `response_mode=query` (solo per scopi di esercitazione). Si consiglia di utilizzare `response_mode=form_post`. <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatorio | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client\_id | Obbligatorio | ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| response\_type | Obbligatorio | Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere altri parametri response\_type, ad esempio `code`. |
| redirect\_uri | consigliato | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | Obbligatorio | Elenco di ambiti separati da spazi. Per OpenID Connect, deve includere l'ambito `openid` che esegue la conversione all'autorizzazione per l'accesso nell'interfaccia utente di consenso. È anche possibile includere in questa richiesta altri ambiti per richiedere il consenso. |
| nonce | Obbligatorio | Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| response\_mode | Consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. Può essere 'query', 'form\_post' o 'fragment'. Per le applicazioni Web si consiglia di usare `response_mode=form_post` per assicurare il trasferimento più sicuro dei token nell'applicazione.  
| state | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](http://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | Facoltativo | Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi in questa fase sono "login", "none" e "consent". `prompt=login` impone all'utente di immettere le credenziali per la richiesta, negando l'accesso Single Sign-On, mentre `prompt=none` provoca l'effetto contrario, ovvero garantisce che all'utente non venga presentato alcun prompt interattivo. Se la richiesta non può essere completata automaticamente tramite Single-Sign-On, l'endpoint 2.0 restituirà un errore. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth in seguito all'accesso dell'utente, chiedendo all'utente di concedere le autorizzazioni per l'app. |
| login\_hint | Facoltativo | Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`. |
| domain\_hint | Facoltativo | Può essere uno di `consumers` o `organizations`. Se incluso, non verrà eseguito il processo di individuazione basata sulla posta elettronica a cui viene sottoposto l'utente nella pagina di accesso della versione 2.0. Questo comporta un'esperienza utente leggermente semplificata. Le app usano spesso questo parametro durante la riautenticazione, estraendo l'attestazione `tid` dall'id\_token. Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`. In caso contrario, usare `domain_hint=organizations`. |
A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint 2.0 assicura anche che l'utente abbia fornito il consenso per le autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Questo articolo contiene informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant](active-directory-v2-scopes.md).

Dopo che l'utente viene autenticato e fornisce il consenso, l'endpoint 2.0 restituisce una risposta all'app nell'URI `redirect_uri`, usando il metodo specificato nel parametro `response_mode`.

#### Risposta con esito positivo
Una risposta con esito positivo che usa `response_mode=form_post` ha un aspetto simile al seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id\_token | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Informazioni dettagliate sui token ID e il relativo contenuto sono incluse nel [riferimento al token dell'endpoint 2.0](active-directory-v2-tokens.md). |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'app possa gestirle adeguatamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## Convalidare il token ID
La semplice ricezione di un token ID non è sufficiente per autenticare l'utente. È necessario convalidare la firma del token ID e verificare le attestazioni nel token per i requisiti dell'app. L'endpoint 2.0 usa i [token Web JSON](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

È possibile scegliere di convalidare `id_token` nel codice client, ma una procedura comune consiste nell'inviare `id_token` a un server back-end dove verrà eseguita la convalida. Dopo aver convalidato la firma dell'id\_token, è necessario verificare alcune attestazioni. Per altre informazioni, ad esempio relative alla [convalida del token](active-directory-v2-tokens.md#validating-tokens) e al [rollover della chiave di firma](active-directory-v2-tokens.md#validating-tokens), vedere il [riferimento al token della versione 2.0](active-directory-v2-tokens.md). È consigliabile usare una libreria per l'analisi e la convalida dei token. È disponibile almeno una libreria per la maggior parte dei linguaggi e delle piattaforme.
<!--TODO: Improve the information on this-->

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

- Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
- Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
- Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Per altre informazioni sulle attestazioni in un token ID, vedere il [riferimento al token dell'endpoint v2.0](active-directory-v2-tokens.md).

Dopo aver convalidato completamente il token ID, è possibile avviare una sessione con l'utente e usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## Inviare una richiesta di disconnessione

`end_session_endpoint` di OpenID Connect non è attualmente supportato dall'endpoint v2.0. Ciò significa che l'app non può inviare una richiesta all'endpoint 2.0 per terminare una sessione utente e cancellare i cookie impostati dall'endpoint 2.0. Per disconnettere un utente, l'app può semplicemente terminare una sessione con l'utente e lasciare la sessione dell'utente con l'endpoint 2.0 invariato. Al successivo accesso, l'utente visualizzerà una pagina per la scelta dell'account con l'elenco degli account di accesso attivi. In tale pagina, l'utente può scegliere di disconnettersi da qualsiasi account, terminando la sessione con l'endpoint 2.0.

<!--

When you wish to sign the user out of the app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## Diagramma di protocollo - Acquisizione di token
Molte app Web non solo eseguono l'accesso dell'utente, ma accedono anche a un servizio Web per conto dell'utente tramite OAuth. Questo scenario combina OpenID Connect per l'autenticazione dell'utente e contemporaneamente acquisisce un codice di autorizzazione che può essere usato per ottenere i token di accesso mediante il flusso del codice di autorizzazione di OAuth.

Il flusso completo di acquisizione delle informazioni di accesso e dei token di accesso di OpenID è simile al seguente: ogni passaggio è descritto in dettaglio di seguito.

![Corsie di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## Ottenere i token di accesso
Per acquisire i token di accesso, sarà necessario modificare leggermente la richiesta di accesso precedente:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=form_post						      // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20										 
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

> [AZURE.TIP] Fare clic sul collegamento seguente per eseguire questa richiesta. Dopo l'accesso, il browser deve essere reindirizzato a `https://localhost/myapp/` con un `id_token` e un `code` nella barra degli indirizzi. Si noti che questa richiesta utilizza `response_mode=query` (solo per scopi di esercitazione). Si consiglia di utilizzare `response_mode=form_post`. <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Includendo gli ambiti di autorizzazione nella richiesta e usando `response_type=code+id_token`, l'endpoint 2.0 verificherà che l'utente abbia dato il consenso alle autorizzazioni indicate nel parametro di query `scope` e restituirà all'app un codice di autorizzazione da scambiare con un token di accesso.

#### Risposta con esito positivo
Una risposta con esito positivo che usa `response_mode=form_post` ha un aspetto simile al seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id\_token | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Informazioni dettagliate sui token ID e il relativo contenuto sono incluse nel [riferimento al token dell'endpoint 2.0](active-directory-v2-tokens.md). |
| code | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve, in genere scadono dopo circa 10 minuti. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'app possa gestirle adeguatamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

Una volta ottenuti un `code` e un `id_token` di autorizzazione, è possibile far accedere l'utente e ottenere i token di accesso per suo conto. Per far accedere l'utente, è necessario convalidare l'`id_token` esattamente come descritto [sopra](#validating-the-id-token). Per ottenere i token di accesso, è possibile seguire i passaggi descritti nella [documentazione del protocollo OAuth](active-directory-v2-protocols-oauth-code.md#request-an-access-token).

<!---HONumber=AcomDC_0608_2016-->