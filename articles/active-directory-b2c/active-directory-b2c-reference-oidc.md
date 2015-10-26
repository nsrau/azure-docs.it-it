<properties
	pageTitle="Anteprima di Azure AD B2C | Microsoft Azure"
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Anteprima Azure AD B2C: accesso Web con OpenID Connect

OpenID Connect è un protocollo di autenticazione basato su OAuth 2.0 utilizzabile per far accedere in sicurezza gli utenti nelle applicazioni Web. Tramite l'implementazione Azure AD B2C di OpenID Connect è possibile assegnare iscrizione, accesso ed altre esperienze di gestione identità nelle applicazioni Web di Azure AD. Questa guida illustra come effettuare questa operazione in modo indipendente dal linguaggio, descrivendo come inviare e ricevere messaggi HTTP senza utilizzare le nostre librerie open-source.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 da usare come protocollo di *autenticazione* per consentire di eseguire l'accesso single sign-on tramite OAuth. Introduce il concetto di un `id_token`, ovvero un token di sicurezza che consente al client di verificare l'identità dell'utente e ottenere informazioni di base sul profilo dell'utente. Poiché estende OAuth 2.0, consente anche alle applicazioni di acquisire in modo sicuro **access\_token**, utilizzabili per accedere alle risorse protette da un [server di autorizzazione](active-directory-b2c-reference-protocols.md#the-basics). Consigliamo OpenID Connect per la creazione di un'applicazione Web ospitata su un server e accessibile da browser. Per aggiungere la gestione dell’identità per dispositivi mobili o applicazioni desktop utilizzando Azure AD B2C, è necessario utilizzare [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) anziché OpenID Connect.

Azure AD B2C estende il protocollo standard OpenID Connect per non limitarsi esclusivamente a semplici operazioni di autenticazione e autorizzazione. Introduce il [**parametro criteri**](active-directory-b2c-reference-poliices.md), per utilizzare OpenID Connect per aggiungere esperienze utente all'applicazione, come ad esempio, iscrizione, accesso e gestione del profilo. Di seguito viene illustrato come utilizzare OpenID Connect e i criteri per implementare ciascuna di queste esperienze nelle applicazioni Web e ottenere access\_token per l'accesso ad API Web.

Le richieste HTTP di esempio seguenti utilizzano la directory B2C di esempio, **fabrikamb2c.onmicrosoft.com**, nonché l'applicazione di esempio * ***https://aadb2cplayground.azurewebsites.net** e i criteri. Si possono provare le richieste in totale autonomia utilizzando questi valori oppure è possibile sostituirli con valori personalizzati. Ulteriori informazioni su come [ottenere directory, applicazione e i criteri B2C](#use-your-own-b2c-directory).

## Invio di richieste di autenticazione
Quando l'app Web deve autenticare l'utente ed eseguire un criterio, può indirizzarlo all'endpoint `/authorize`. Questa è la parte interattiva del flusso, dove l’utente opera effettivamente in base ai criteri. In questa richiesta, il client indica le autorizzazioni da acquisire dall'utente nel parametro `scope` e il criterio da eseguire nel parametro `p`. Di seguito vengono forniti tre esempi (con interruzioni di riga per migliorare la leggibilità), ciascuno dei quali utilizza un criterio diverso. Per apprendere il funzionamento di ogni richiesta, provare a incollare la richiesta in un browser ed eseguirla.

#### Uso di un criterio di accesso

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### Uso di un criterio di iscrizione

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### Uso di un criterio del profilo di modifica

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | obbligatorio | L'ID applicazione assegnato all’applicazione dal [Portale di Azure](https://portal.azure.com). |
| response\_type | obbligatorio | Deve includere `id_token` per OpenID Connect. Se anche l'applicazione Web richiede token per chiamare un'API Web, è possibile utilizzare `code+id_token`, come illustrato qui. |
| redirect\_uri | obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Un valore per l’ambito indica ad Azure AD che entrambe le autorizzazioni sono richieste. L’ambito `openid` indica un'autorizzazione per l'accesso dell’utente e per ottenere i dati relativi all'utente sotto forma di **id\_tokens** (ulteriori informazioni più avanti). L’ambito `offline_access` è facoltativo per le applicazioni Web. Indica che l'applicazione richiede un **refresh\_token** per un accesso prolungato alle risorse. |
| response\_mode | consigliato | Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app. Può essere 'query', 'form\_post' o 'fragment'. |
| state | consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina in cui si trovava. |
| nonce | obbligatorio | Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| p | obbligatorio | Indica i criteri da eseguire. È il nome di un criterio creato nella directory B2C, il cui valore deve iniziare con "b2c\_1\_". Ulteriori informazioni sui criteri [qui](active-directory-b2c-reference-policies.md). |
| prompt | facoltativo | Indica il tipo di interazione obbligatoria dell'utente. L'unico valore valido in questa fase è 'login', che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non ha effetto. |

A questo punto, viene richiesto all'utente di completare il flusso di lavoro del criterio. Potrebbe richiedere all'utente di immettere nome utente e password, accedere con un'identità di social networking, l'iscrizione alla directory o qualsiasi altro numero di passaggi a seconda di come sono definiti i criteri. Dopo che l'utente completa il criterio, Azure AD restituisce una risposta all'app all’`redirect_uri` indicato, utilizzando il metodo specificato nel parametro `response_mode`. La risposta corrisponde esattamente a ciascuno dei casi precedenti, indipendentemente dai criteri eseguiti.

Una risposta con esito positivo che utilizza `response_mode=query` ha un aspetto simile al seguente:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id\_token | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Informazioni dettagliate sugli id\_token e i relativi contenuti sono incluse nel [riferimento al token di Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| code | authorization\_code richiesto dall’app, se è stato utilizzato `response_type=code+id_token`. L'app può utilizzare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve, in genere scadono dopo circa 10 minuti. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'app possa gestirle adeguatamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |


## Convalidare il token ID
La semplice ricezione di un token ID non è sufficiente per autenticare l'utente. È necessario convalidare la firma del token ID e verificare le attestazioni nel token per i requisiti dell'app. Azure AD B2C utilizza i [token Web JSON](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità. Sono disponibili molte librerie open source per la convalida di JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame tali opzioni anziché implementare una logica di convalida personalizzata. Le presenti informazioni risultano utili per scoprire come utilizzare adeguatamente queste librerie.

Azure AD B2C dispone di un endpoint di metadati OpenID Connect, che consente a un'applicazione di recuperare informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Nella directory B2C, esiste un documento di metadati JSON per ogni criterio. Ad esempio, il documento dei metadati per il criterio in `b2c_1_sign_in` nel `fabrikamb2c.onmicrosoft.com` su:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una delle proprietà del documento di configurazione è `jwks_uri`, il cui valore per lo stesso criterio è:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Per determinare quale criterio è stato utilizzato per la firma di un id\_token (e la posizione dove recuperare i metadati), sono disponibili due opzioni. In primo luogo, il nome del criterio è incluso nell’attestazione `acr` dell’id\_token. Per informazioni su come analizzare le attestazioni da un id\_token, consultare il [riferimento al token Azure AD B2C](active-directory-b2c-reference-tokens.md). L'altra opzione consiste nel codificare i criteri nel valore del parametro `state` quando si rilascia la richiesta, per poi decodificarlo e determinare quali criteri sono stati utilizzati. Entrambi i metodi sono perfettamente validi.

Dopo aver acquistato il documento dei metadati dall'endpoint di metadati OpenID Connect, è possibile utilizzare le chiavi pubbliche RSA256 che si trovano in questo endpoint per convalidare la firma dell’id\_token. Possono essere presenti più chiavi elencate in questo endpoint , ognuna identificata da `kid`. L'intestazione dell’id\_token contiene inoltre un'attestazione `kid`, che indica quali di queste chiavi sono state utilizzate per firmare l’id\_token. Per ulteriori informazioni, relative alla [convalida del token](active-directory-b2c-reference-tokens.md#validating-tokens) e [Informazioni importanti sul rollover della chiave di firma in Azure AD](active-directory-b2c-reference-tokens.md#validating-tokens), consultare il [riferimento al token di Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Dopo aver convalidato la firma del token ID, è necessario verificare alcune attestazioni:

- È necessario convalidare l'attestazione `nonce` per impedire attacchi di tipo replay del token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
- È necessario convalidare l'attestazione `aud` per garantire che l’id\_token sia stato prodotto per l'applicazione. Il valore deve essere l'ID applicazione dell'applicazione.
- È necessario convalidare le attestazioni `iat` e `exp` per garantire che l’id\_token non sia scaduto.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

- Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
- Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
- Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Per ulteriori informazioni sulle attestazioni in un id\_token, vedere il [riferimento al token di Azure AD B2C](active-directory-b2c-reference-tokens.md).

Dopo aver convalidato completamente il token ID, è possibile avviare una sessione con l'utente e usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## Acquisizione di un token
Se l'applicazione Web deve solo eseguire criteri, è possibile saltare le prossime sezioni. Queste sezioni sono applicabili solo alle applicazioni Web che devono effettuare chiamate autenticate a un'API web, protette anche da Azure AD B2C.

È possibile riscattare l’authorization\_code acquisito (utilizzando `response_type=code+id_token`) per un token sulla risorsa desiderata, inviando una richiesta `POST` per l’endpoint `/token`. Nell'anteprima Azure AD B2C, l'unica risorsa per la quale è possibile richiedere un token è per l'API Web di back-end dell'applicazione stessa. La convenzione utilizzata per richiedere un token di questo tipo consiste nell'utilizzare l'ambito `openid`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| p | obbligatorio | Il criterio utilizzato per acquisire il codice di autorizzazione. Non è possibile utilizzare un criterio diverso in questa richiesta. **Questo parametro viene aggiunto alla stringa di query**, non al corpo della richiesta POST. |
| client\_id | obbligatorio | L'ID applicazione assegnato all’applicazione dal [Portale di Azure](https://portal.azure.com). |
| grant\_type | obbligatorio | Deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Un valore per l’ambito indica ad Azure AD che entrambe le autorizzazioni sono richieste. L’ambito `openid` indica un'autorizzazione per l'accesso dell’utente e per ottenere i dati relativi all'utente sotto forma di **id\_token**. Utilizzabile per ottenere token sull’API Web di back-end dell’applicazione, rappresentato dallo stesso ID applicazione del client. Lo scopo `offline_access` indica che l'applicazione richiede un **refresh\_token** per un accesso duraturo alle risorse. |
| code | obbligatorio | Codice di autorizzazione acquisito durante la prima sezione del flusso. |
| redirect\_uri | obbligatorio | Il parametro redirect\_uri dell'applicazione, dove è stato ricevuto l’authorization\_code. |
| client\_secret | obbligatorio | La chiave privata dell’applicazione generata nel [Portale Azure](https://portal.azure.com). La chiave privata dell'applicazione è un importante elemento di sicurezza e deve essere archiviata in modo protetto sul server. Inoltre, è necessario far ruotare periodicamente tale chiave privata del client. |

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
| refresh\_token | Token di aggiornamento di OAuth 2.0. L'app può utilizzare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare il [riferimento al token B2C](active-directory-b2c-reference-tokens.md). Per ricevere un token di aggiornamento, occorre aver utilizzato l’ambito `offline_access` nelle richieste sia di autorizzazione, sia del token. |
| refresh\_token\_expires\_in | Tempo massimo di validità per un token di aggiornamento (in secondi). Il token di aggiornamento può tuttavia perdere validità in qualsiasi momento. |

> [AZURE.NOTE]Qualora si cerchi l’access\_token, considerare quanto segue. Quando si richiede l’ambito `openid`, Azure AD rilascia un JWT `id_token` nella risposta. Sebbene questo `id_token` non sia tecnicamente un access\_token OAuth 2.0, può essere utilizzato come tale durante la comunicazione con il servizio di back-end dell’applicazione, rappresentato dallo stesso client\_id del client. `id_token` è ancora un token di connessione JWT inviabile a una risorsa in un'intestazione di autorizzazione HTTP e utilizzato per autenticare le richieste. La differenza è che un `id_token` non dispone di un meccanismo per determinare l'ambito di una particolare applicazione client. Tuttavia, quando l'applicazione client è l'unico client in grado di comunicare con il servizio di back-end (come nel caso dell'anteprima di Azure AD B2C corrente), non è necessario per tale meccanismo di individuazione dell’ambito. Se l'anteprima di Azure AD B2C aggiunge la funzionalità per i client di comunicare con risorse di prime e terze parti supplementari, si ricorrerà agli access\_token. Tuttavia, anche in tal caso si consiglia di utilizzare `id_tokens` per comunicare con il servizio di back-end dell'applicazione. Per ulteriori informazioni sui tipi di applicazioni creabili con l'anteprima Azure AD B2C, consultare [questo articolo](active-directory-b2c-apps.md).

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

## Uso del token
Dopo aver ottenuto un `id_token`, è possibile utilizzarlo in richieste alle API Web di back-end includendolo nell'intestazione `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Aggiornamento del token
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
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"	
}
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | -------- |
| p | obbligatorio | Il criterio utilizzato per acquisire il token di aggiornamento originale. Non è possibile utilizzare un criterio diverso in questa richiesta. **Questo parametro viene aggiunto alla stringa di query**, non al corpo della richiesta POST. |
| client\_id | obbligatorio | L'ID applicazione assegnato all’applicazione dal [Portale di Azure](https://portal.azure.com). |
| grant\_type | obbligatorio | Deve essere `refresh_token` per questa sezione del flusso del codice di autorizzazione. |
| scope | obbligatorio | Elenco di ambiti separati da spazi. Un valore per l’ambito indica ad Azure AD che entrambe le autorizzazioni sono richieste. L’ambito `openid` indica un'autorizzazione per l'accesso dell’utente e per ottenere i dati relativi all'utente sotto forma di **id\_token**. Utilizzabile per ottenere token sull’API Web di back-end dell’applicazione, rappresentato dallo stesso ID applicazione del client. Lo scopo `offline_access` indica che l'applicazione richiede un **refresh\_token** per un accesso duraturo alle risorse. |
| redirect\_uri | obbligatorio | Il parametro redirect\_uri dell'applicazione, dove è stato ricevuto l’authorization\_code. |
| refresh\_token | obbligatorio | refresh\_token acquisito durante la seconda sezione del flusso. Per ricevere un token di aggiornamento, occorre aver utilizzato l’ambito `offline_access` nelle richieste sia di autorizzazione, sia del token. |
| client\_secret | obbligatorio | La chiave privata dell’applicazione generata nel [Portale Azure](https://portal.azure.com). La chiave privata dell'applicazione è un importante elemento di sicurezza e deve essere archiviata in modo protetto sul server. Inoltre, è necessario far ruotare periodicamente tale chiave privata del client. |

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

## Invio di una richiesta di disconnessione

Durante la disconnessione di un utente dall’applicazione, non basta cancellare i cookie dell’applicazione o terminare la sessione con l’utente. Bisogna anche reindirizzare l’utente su Azure AD per la disconnessione. In caso contrario, l'utente potrebbe essere in grado di autenticarsi nuovamente sull'applicazione senza immettere le credenziali, poiché disporrà di una sessione single sign-on valida con Azure AD.

Basta reindirizzare l’utente sull’`end_session_endpoint` elencato nello stesso documento dei metadati di OpenID Connect descritto [sopra](#validate-the-id-token):

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | ------------ |
| p | obbligatorio | L’ultimo criterio utilizzato dall’utente per accedere all'applicazione. |
| post\_logout\_redirect\_uri | consigliato | L'URL di destinazione al quale l’utente deve essere reindirizzato dopo la disconnessione. Se omesso, l'utente visualizza un messaggio generico da Azure AD B2C. |

> [AZURE.NOTE]Sebbene l'indirizzamento dell'utente sull’`end_session_endpoint` comporti la cancellazione di alcuni stati single sign-on degli utenti con Azure AD, in realtà non disconnette effettivamente l’utente. Infatti, se l'utente seleziona l’IDP al quale accedere, viene ri-autenticato senza dover inserire le credenziali. In caso di IDP di social network, questo è il comportamento previsto. Se un utente desidera disconnettersi dalla directory B2C, non significa necessariamente che intenda disconnettersi del tutto dall’account Facebook. Tuttavia, in caso di account locali, deve essere possibile terminare la sessione dell'utente in modo corretto. Una delle [limitazioni](active-directory-b2c-limitations.md) note dell’anteprima di Azure AD è il funzionamento inadeguato della disconnessione locale dell’account. Una soluzione alternativa immediata è inviare il parametro `&prompt=login` in ogni richiesta di autenticazione, che avrà l'aspetto del comportamento desiderato, ma suddivide la sessione single sign-on tra le applicazioni nella directory B2C.

## Uso della propria directory B2C

Per provare queste richieste autonomamente, è innanzitutto necessario eseguire questi tre passaggi, quindi sostituire i valori di esempio con i propri:

- [Creare una directory B2C](active-directory-b2c-get-started.md) e utilizzare il nome della directory nelle richieste.
- [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione e un redirect\_uri. È possibile includere poi un’**app Web/API Web** nell'applicazione e, facoltativamente, creare una **chiave privata dell'applicazione**.
- [Creare i criteri](active-directory-b2c-reference-policies.md) per ottenere i nomi dei criteri.

<!--

TODO

OpenID Connect for the v2.0 app model is the recommended way to implement sign-in for a [web  app](active-directory-v2-flows.md#web-apps).  The most basic sign-in flow contains the following steps:

image goes here

-->

<!---HONumber=Oct15_HO3-->