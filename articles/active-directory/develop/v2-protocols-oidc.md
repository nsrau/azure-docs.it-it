---
title: Microsoft Identity Platform e protocollo OpenID Connect | Azure
titleSuffix: Microsoft identity platform
description: Creare applicazioni Web usando l'implementazione Microsoft identity platform del protocollo di autenticazione OpenID Connect.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0e1284b94500ae6b6f1aa5eb632e94e03f3d3df3
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771588"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity Platform e protocollo OpenID Connect

OpenID Connect (OIDC) è un protocollo di autenticazione basato su OAuth 2.0 che può essere usato per fare in modo che gli utenti accedano in modo protetto a un'applicazione Web. Quando si usa l'implementazione dell'endpoint Microsoft Identity Platform di OpenID Connect, è possibile aggiungere l'accesso e l'accesso API alle app basate sul Web. Questo articolo descrive la procedura, indipendente dal linguaggio, per inviare e ricevere messaggi HTTP senza usare nessuna delle librerie open source di Microsoft.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 da usare come protocollo di *autenticazione* per consentire di eseguire l'accesso Single Sign-On tramite OAuth. OpenID Connect introduce il concetto di *token ID*, ovvero un token di sicurezza che consente al client di verificare l'identità dell'utente. Il token ID consente anche di ottenere informazioni di base sul profilo dell'utente. Poiché OpenID Connect estende OAuth 2.0, le app potranno acquisire in modo sicuro *token di accesso* che possono essere usati per accedere alle risorse protette da un [server di autorizzazione](active-directory-v2-protocols.md#the-basics). L'endpoint v2.0 Microsoft Identity Platform consente inoltre alle app di terze parti registrate in Azure AD di rilasciare token di accesso per le risorse protette, ad esempio le API Web. Per altre informazioni su come configurare un'applicazione per rilasciare token di accesso, vedere [Come registrare un'app con l'endpoint Microsoft Identity Platform](quickstart-register-app.md). È consigliabile usare OpenID Connect per la compilazione di un'[applicazione Web](v2-app-types.md#web-apps) ospitata su un server e accessibile tramite browser.

## <a name="protocol-diagram-sign-in"></a>Diagramma di protocollo: Accesso

Il flusso di accesso di base include i passaggi illustrati nella figura seguente. Questo articolo descrive dettagliatamente i singoli passaggi.

![Protocollo OpenID Connect: Accesso](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Recuperare il documento di metadati OpenID Connect

OpenID Connect descrive un documento di metadati che contiene la maggior parte delle informazioni necessarie perché un'applicazione esegua l'accesso. Il documento include informazioni come gli URL da usare e il percorso delle chiavi di firma pubbliche del servizio. Per l'endpoint Microsoft Identity Platform, usare il documento di metadati OpenID Connect seguente:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Prova Fare clic su [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) per visualizzare la configurazione dei tenant `common`.

`{tenant}` può assumere uno dei quattro valori seguenti:

| valore | Descrizione |
| --- | --- |
| `common` |Possono accedere all'applicazione gli utenti con account Microsoft e account aziendali o dell'istituto d'istruzione di Azure AD. |
| `organizations` |Possono accedere all'applicazione solo gli utenti con account aziendali o dell'istituto d'istruzione di Azure AD. |
| `consumers` |Possono accedere all'applicazione solo gli utenti con account Microsoft personali. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` | Solo gli utenti di uno specifico tenant di Azure AD, che siano membri nella directory con un account aziendale o dell'istituto di istruzione oppure guest nella directory con un account Microsoft personale, possono accedere all'applicazione. È possibile usare il nome di dominio descrittivo del tenant di Azure AD o l'identificatore GUID. È anche possibile usare il tenant consumer, `9188040d-6c67-4c5b-b112-36a304b66dad`, al posto del tenant `consumers`.  |

I metadati sono un semplice documento JavaScript Object Notation (JSON). Per un esempio, vedere il frammento di codice seguente. Il contenuto del frammento di codice è descritto dettagliatamente nelle [specifiche di OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```json
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Se l'app include chiavi di firma personalizzate perché è stata usata la funzionalità di [mapping delle attestazioni](active-directory-claims-mapping.md), è necessario accodare un parametro di query `appid` contenente l'ID app per ottenere un indirizzo `jwks_uri` che punta alle informazioni della chiave di firma dell'app. Ad esempio, `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contiene una l'indirizzo `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

In genere, si usa questo documento di metadati per configurare una libreria di OpenID Connect o un SDK. La libreria usa i metadati per le proprie operazioni. Tuttavia, se non si usa una libreria OpenID Connect precompilata, è possibile seguire i passaggi nella parte restante di questo articolo per eseguire l'accesso in un'app Web tramite l'endpoint Microsoft Identity Platform.

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso

Quando l'app Web deve autenticare l'utente, può indirizzarlo all'endpoint `/authorize` . Questa richiesta è simile a quella della prima parte del [flusso del codice di autorizzazione di OAuth 2.0](v2-oauth2-auth-code-flow.md), con alcune importanti differenze:

* La richiesta deve includere l'ambito `openid` nel parametro `scope`.
* Il parametro `response_type` deve includere `id_token`.
* La richiesta deve includere il parametro `nonce` .

> [!IMPORTANT]
> Per richiedere un token ID dall'endpoint /authorize, la registrazione dell'app nel [portale di registrazione](https://portal.azure.com) deve avere la concessione implicita di id_tokens abilitata nella scheda Autenticazione (che imposta il flag `oauth2AllowIdTokenImplicitFlow` nel [manifesto dell'applicazione](reference-app-manifest.md) su `true`). Se non è abilitata, viene restituito un errore `unsupported_response`: "The provided value for the input parameter 'response_type' isn't allowed for this client. Expected value is 'code'" (Il valore fornito per il parametro di input 'response_type' non è consentito per questo client. Il valore previsto è 'code')

Ad esempio:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Fare clic sul collegamento seguente per eseguire la richiesta. Dopo l'accesso, il browser verrà reindirizzato a `https://localhost/myapp/`, con un token ID nella barra degli indirizzi. Si noti che questa richiesta usa `response_mode=fragment` (solo a scopo dimostrativo). È consigliabile usare `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | È possibile usare il valore `{tenant}` nel percorso della richiesta per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sui protocolli](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Obbligatoria | L'**ID dell'applicazione (client)** assegnato all'app dall'esperienza[Portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908). |
| `response_type` | Obbligatoria | Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere altri valori `response_type`, ad esempio `code`. |
| `redirect_uri` | Consigliato | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. Se non è presente, l'endpoint seleziona un URI di reindirizzamento redirect_uri registrato in modo casuale da restituire all'utente. |
| `scope` | Obbligatoria | Elenco di ambiti separati da spazi. Per OpenID Connect, deve includere l'ambito `openid`che esegue la conversione all'autorizzazione per l'accesso nell'interfaccia utente di consenso. È anche possibile includere in questa richiesta altri ambiti per richiedere il consenso. |
| `nonce` | Obbligatoria | Valore incluso nella richiesta, generato dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| `response_mode` | Consigliato | Specifica il metodo che deve essere usato per inviare il codice di autorizzazione risultante all'app. Può essere `form_post` o `fragment`. Per le applicazioni Web è consigliabile usare `response_mode=form_post` per assicurare il trasferimento più sicuro dei token nell'applicazione. |
| `state` | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](https://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Anche lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione corrente dell'utente. |
| `prompt` | Facoltativo | Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi al momento sono `login`, `none` e `consent`. L'attestazione `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On. L'attestazione `prompt=none` è l'opposto, ovvero garantisce che l'utente non visualizzi alcuna richiesta interattiva. Se la richiesta non può essere completata automaticamente mediante Single-Sign-On, l'endpoint Microsoft Identity Platform restituisce un errore. L'attestazione `prompt=consent` attiva la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente. La finestra di dialogo chiede all'utente di concedere le autorizzazioni per l'app. |
| `login_hint` | Facoltativo | È possibile usare questo parametro per pre-compilare i campi nome utente e indirizzo di posta elettronica nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`. |
| `domain_hint` | Facoltativo | Area di autenticazione dell'utente in una directory federata.  Non viene eseguito il processo di individuazione basato sulla posta elettronica a cui viene sottoposto l'utente nella pagina di accesso per garantire un'esperienza utente più semplice. Per i tenant federati mediante una directory locale come AD FS, questo spesso comporta un accesso senza problemi per via della sessione di accesso esistente. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint Microsoft Identity Platform garantisce che l'utente abbia dato il consenso alle autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha dato il consenso per alcuna autorizzazione, l'endpoint Microsoft Identity Platform chiede all'utente di dare il consenso per le autorizzazioni obbligatorie. Sono disponibili altre informazioni su [autorizzazioni, consenso e app multi-tenant](v2-permissions-and-consent.md).

Dopo che l'utente ha eseguito l'autenticazione e dato il consenso, l'endpoint Microsoft Identity Platform restituisce una risposta all'app nell'URI di reindirizzamento indicato usando il metodo specificato nel parametro `response_mode`.

### <a name="successful-response"></a>Risposta con esito positivo

La risposta con esito positivo quando si usa `response_mode=form_post` è simile alla seguente:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametro | Descrizione |
| --- | --- |
| `id_token` | Token ID richiesto dall'app. È possibile usare il parametro `id_token` per verificare l'identità dell'utente e avviare una sessione con l'utente. Per informazioni dettagliate sui token ID e sul relativo contenuto, vedere le [informazioni di riferimento su `id_tokens`](id-tokens.md). |
| `state` | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche all'URI di reindirizzamento in modo che l'app possa gestirle. La risposta di errore è simile alla seguente:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Codici per gli errori dell'endpoint di autorizzazione

La tabella seguente descrive i codici di errore che possono essere restituiti nel parametro `error` della risposta di errore:

| Codice di errore | Descrizione | Azione client |
| --- | --- | --- |
| `invalid_request` | Errore del protocollo, ad esempio un parametro obbligatorio mancante. |Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| `unauthorized_client` | L'applicazione client non può richiedere un codice di autorizzazione. |Questo si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| `access_denied` | Il proprietario della risorsa ha negato il consenso. |L'applicazione client può indicare all'utente che non può proseguire, a meno che l'utente non fornisca il consenso. |
| `unsupported_response_type` |Il server di autorizzazione non supporta il tipo di risposta nella richiesta. |Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| `server_error` | Errore imprevisto rilevato dal server. |ripetere la richiesta. Questi errori possono dipendere da condizioni temporanee. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di un errore temporaneo. |
| `temporarily_unavailable` | Il server è temporaneamente troppo occupato per gestire la richiesta. |ripetere la richiesta. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |
| `invalid_resource` | La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è configurata correttamente. |Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |

## <a name="validate-the-id-token"></a>Convalidare il token ID

La semplice ricezione di un elemento id_token non è sufficiente per autenticare l'utente. È necessario convalidare la firma di id_token e verificare le attestazioni nel token in base ai requisiti dell'app. L'endpoint Microsoft Identity Platform usa i [token Web JSON (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

È possibile scegliere di convalidare `id_token` nel codice client, ma una procedura comune consiste nell'inviare `id_token` a un server back-end dove verrà eseguita la convalida. Dopo aver convalidato la firma di id_token, è necessario verificare alcune attestazioni. Vedere le [informazioni di riferimento su `id_token`](id-tokens.md) per altre informazioni, ad esempio relative alla [convalida del token](id-tokens.md#validating-an-id_token) e al [rollover della chiave di firma](active-directory-signing-key-rollover.md). È consigliabile usare una libreria per l'analisi e la convalida dei token. È disponibile almeno una libreria per la maggior parte dei linguaggi e delle piattaforme.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

* Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
* Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
* Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'[autenticazione a più fattori](../authentication/concept-mfa-howitworks.md).

Dopo aver convalidato id_token, è possibile avviare una sessione con l'utente e usare le attestazioni in id_token per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, la personalizzazione e così via.

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione

Durante la disconnessione di un utente dall'app, non basta cancellare i cookie dell'app o terminare la sessione dell'utente. È anche necessario reindirizzare l'utente all'endpoint Microsoft Identity Platform per la disconnessione. In caso contrario, l'utente esegue nuovamente l'autenticazione nell'app senza immettere di nuovo le credenziali, poiché disporrà di una sessione Single Sign-On valida con l'endpoint Microsoft Identity Platform.

È possibile reindirizzare l'utente all'`end_session_endpoint` riportato nel documento dei metadati di OpenID Connect:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametro | Condizione | Descrizione |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Consigliato | L'URL di destinazione al quale l'utente viene reindirizzato dopo la disconnessione. Se il parametro non è incluso, viene visualizzato un messaggio generico generato dall'endpoint Microsoft Identity Platform. Questo URL deve corrispondere esattamente a uno degli URI di reindirizzamento registrati per l'applicazione nel portale di registrazione delle app. |

## <a name="single-sign-out"></a>Single Sign-Out

Quando si reindirizza l'utente a `end_session_endpoint`, l'endpoint Microsoft Identity Platform cancella la sessione dell'utente dal browser. L'utente può tuttavia essere ancora connesso ad altre applicazioni che usano account Microsoft per l'autenticazione. Per consentire che tutte le applicazioni eseguano la disconnessione dell'utente simultaneamente, l'endpoint Microsoft Identity Platform invia una richiesta HTTP GET all'oggetto `LogoutUrl` registrato di tutte le applicazioni a cui l'utente è attualmente connesso. Le applicazioni devono rispondere a questa richiesta cancellando qualsiasi sessione che identifica l'utente e restituendo una risposta `200`. Se si vuole supportare Single Sign-Out nell'applicazione, è necessario implementare questo tipo di oggetto `LogoutUrl` nel codice dell'applicazione. È possibile impostare `LogoutUrl` dal portale di registrazione delle app.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagramma di protocollo: Acquisizione dei token di accesso

Molte app Web non richiedono soltanto l'accesso dell'utente, ma necessitano anche di accedere a un servizio Web per conto dell'utente tramite OAuth. In questo scenario viene usato OpenID Connect per l'autenticazione utente e contemporaneamente viene acquisito un codice di autorizzazione che può essere usato per ottenere i token di accesso quando si usa il flusso del codice di autorizzazione di OAuth.

Il flusso completo di accesso OpenID Connect e di acquisizione dei token è simile a quello illustrato nella figura seguente. Le sezioni seguenti dell'articolo descrivono i singoli passaggi.

![Protocollo OpenID Connect: Acquisizione dei token](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Ottenere i token di accesso
Per acquisire i token di accesso, modificare la richiesta di accesso:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs
offline_access%20
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Fare clic sul collegamento seguente per eseguire la richiesta. Dopo l'accesso, il browser verrà reindirizzato a `https://localhost/myapp/`, con un token ID e codice nella barra degli indirizzi. Si noti che questa richiesta usa `response_mode=fragment` solo a scopo dimostrativo. È consigliabile usare `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Includendo gli ambiti di autorizzazione nella richiesta e usando `response_type=id_token code`, l'endpoint Microsoft Identity Platform verifica che l'utente abbia dato il consenso alle autorizzazioni indicate nel parametro di query `scope`. Verrà restituito nell'app un codice di autorizzazione da scambiare con un token di accesso.

### <a name="successful-response"></a>Risposta con esito positivo

La risposta con esito positivo quando si usa `response_mode=form_post` è simile alla seguente:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametro | Descrizione |
| --- | --- |
| `id_token` | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Altre informazioni sui token ID e il relativo contenuto sono disponibili nelle [informazioni di riferimento su `id_tokens`](id-tokens.md). |
| `code` | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. La durata del codice di autorizzazione è breve. In genere, un codice di autorizzazione scade dopo circa 10 minuti. |
| `state` | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente. La risposta di errore è simile alla seguente:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

Per una descrizione dei possibili codici di errore e delle risposte client consigliate, vedere [Codici per gli errori dell'endpoint di autorizzazione](#error-codes-for-authorization-endpoint-errors).

Dopo aver ottenuto un codice di autorizzazione e un token ID, è possibile far accedere l'utente e ottenere i token di accesso per suo conto. Per far accedere l'utente, è necessario convalidare il token ID [esattamente come descritto](id-tokens.md#validating-an-id_token). Per ottenere i token di accesso, seguire la procedura descritta nella [documentazione del flusso del codice OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
