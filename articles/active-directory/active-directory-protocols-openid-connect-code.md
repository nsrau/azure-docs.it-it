<properties
	pageTitle="Panoramica del protocollo .NET per Azure AD | Microsoft Azure"
	description="Questo articolo descrive come usare messaggi HTTP per autorizzare l'accesso ad applicazioni Web e API Web nel proprio tenant con Azure Active Directory e OpenID Connect."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# Autorizzare l'accesso ad applicazioni Web con OpenID Connect e Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) è un semplice livello di gestione delle identità basato sul protocollo OAuth 2.0. OAuth 2.0 definisce i meccanismi per ottenere e usare **token di accesso** per accedere a risorse protette, ma non definisce metodi standard per fornire informazioni relative all'identità. OpenID Connect implementa l'autenticazione come estensione del processo di autorizzazione OAuth 2.0, fornendo informazioni sull'utente finale sotto forma di `id_token` che verifica l'identità dell'utente e fornisce informazioni di base sul profilo dell'utente.

È consigliabile usare OpenID Connect se si compila un'applicazione Web ospitata su un server e accessibile da browser.

## Flusso di autenticazione con OpenID Connect

Il flusso di accesso di base include i passaggi seguenti: ognuno di essi è descritto in dettaglio di seguito.

![Flusso di autenticazione di OpenID Connect](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## Inviare la richiesta di accesso

Quando l'applicazione Web deve autenticare l'utente, deve indirizzarlo all'endpoint `/authorize`. Questa richiesta è simile a quella della prima parte di [Flusso del codice di autorizzazione di OAuth 2.0](active-directory-protocols-oauth-code.md), con alcune importanti differenze:

- La richiesta deve includere l'ambito `openid` nel parametro `scope`.
- Il parametro `response_type` deve includere `id_token`.
- La richiesta deve includere il parametro `nonce`.

Una richiesta di esempio si presenta quindi come segue:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatorio | Il valore `{tenant}` nel percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono gli identificatori dei tenant, ad esempio `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com`, e `common` per i token indipendenti dai tenant. |
| client\_id | Obbligatorio | ID applicazione assegnato all'app quando è stata registrata in Azure AD. È disponibile nel portale di Azure classico. Fare clic su **Active Directory** e quindi sulla directory, sull'applicazione e infine su **Configura**. |
| response\_type | Obbligatorio | Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere altri parametri response\_type, ad esempio `code`. |
| scope | Obbligatorio | Elenco di ambiti separati da spazi. Per OpenID Connect, deve includere l'ambito `openid` che esegue la conversione all'autorizzazione per l'accesso nell'interfaccia utente di consenso. È anche possibile includere in questa richiesta altri ambiti per richiedere il consenso. |
| nonce | Obbligatorio | Valore incluso nella richiesta, generata dall'app, che verrà incluso nel risultante `id_token` come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere un GUID o una stringa univoca casuale che consente di identificare l'origine della richiesta. |
| redirect\_uri | consigliato | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| response\_mode | Consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. I valori supportati sono `form_post` per *POST modulo HTTP* o `fragment` per *frammento URL*. Per le applicazioni Web è consigliabile usare `response_mode=form_post` per garantire la massima sicurezza di trasferimento dei token all'applicazione.  
| state | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [prevenire attacchi di richiesta intersito falsa](http://tools.ietf.org/html/rfc6749#section-10.12) viene in genere usato un valore univoco generato in modo casuale. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | Facoltativo | Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi in questa fase sono "login", "none" e "consent". `prompt=login` impone all'utente di immettere le credenziali per la richiesta, negando l'accesso Single Sign-On, mentre `prompt=none` provoca l'effetto contrario, ovvero garantisce che all'utente non venga presentato alcun prompt interattivo. Se la richiesta non può essere completata automaticamente tramite Single-Sign-On, l'endpoint restituirà un errore. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth in seguito all'accesso dell'utente, chiedendo all'utente di concedere le autorizzazioni per l'app. |
| login\_hint | Facoltativo | Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app in genere usano questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente con l'attestazione `preferred_username`. |


A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione.

### Risposta di esempio

Una risposta di esempio dopo l'autenticazione dell'utente può avere un aspetto simile al seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id\_token | `id_token` richiesto dall'app. È possibile usare `id_token` per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

### Risposta di errore
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


La semplice ricezione di un `id_token` non è sufficiente per autenticare l'utente. È necessario convalidare la firma e verificare le attestazioni in `id_token` in base ai requisiti dell'app. L'endpoint di Azure AD usa i token Web JSON (JWT) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

Si può scegliere di convalidare `id_token` nel codice client, ma una procedura comune consiste nell'inviare `id_token` a un server back-end in cui verrà eseguita la convalida. Dopo aver convalidato la firma di `id_token`, è necessario verificare alcune attestazioni.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

- Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
- Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
- Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Dopo aver completato la convalida di `id_token`, è possibile avviare una sessione con l'utente e usare le attestazioni in `id_token` per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via. Per altre informazioni sui tipi di token e sulle attestazioni, vedere [Token e tipi di attestazioni supportati](active-directory-token-and-claims.md).

## Inviare una richiesta di disconnessione

Durante la disconnessione di un utente dall’applicazione, non basta cancellare i cookie dell’applicazione o terminare la sessione con l’utente. È anche necessario reindirizzare l'utente a `end_session_endpoint` per la disconnessione. In caso contrario, l'utente potrà autenticarsi nuovamente all'app senza immettere le credenziali grazie alla presenza di una sessione Single Sign-On valida con l'endpoint di Azure AD.

È sufficiente reindirizzare l'utente all'`end_session_endpoint` riportato nel documento dei metadati di OpenID:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | ------------ |
| post\_logout\_redirect\_uri | consigliato | L'URL di destinazione al quale l’utente deve essere reindirizzato dopo la disconnessione. Se omesso, all'utente viene visualizzato un messaggio generico. |

## Acquisizione dei token

Molte app Web non solo eseguono l'accesso dell'utente, ma accedono anche a un servizio Web per conto dell'utente tramite OAuth. Questo scenario combina OpenID Connect per l'autenticazione dell'utente e la contemporanea acquisizione di un `authorization_code` che può essere usato per ottenere `access_tokens` con il flusso del codice di autorizzazione di OAuth.

## Ottenere i token di accesso

Per acquisire i token di accesso, è necessario modificare la richiesta di accesso precedente:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=form_post						      // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F									 
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

Includendo gli ambiti di autorizzazione nella richiesta e usando `response_type=code+id_token`, l'endpoint `authorize` verificherà che l'utente abbia dato il consenso per le autorizzazioni indicate nel parametro di query `scope` e restituirà all'app un codice di autorizzazione da scambiare con un token di accesso.

### Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=form_post` ha un aspetto simile al seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id\_token | `id_token` richiesto dall'app. È possibile usare `id_token` per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| code | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve, in genere scadono dopo circa 10 minuti. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

### Risposta di errore

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

Una volta ottenuti un `code` e un `id_token` di autorizzazione, è possibile far accedere l'utente e ottenere i token di accesso per suo conto. Per l'accesso dell'utente, è necessario convalidare `id_token` esattamente come descritto sopra. Per ottenere i token di accesso, è possibile seguire i passaggi descritti nella [documentazione del protocollo OAuth](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token).

<!---HONumber=AcomDC_0601_2016-->