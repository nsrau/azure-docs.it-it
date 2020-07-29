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
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 741e7a13513d571fbaabd17016b2282a860271cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84263279"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity Platform e protocollo OpenID Connect

OpenID Connect (OIDC) è un protocollo di autenticazione basato su OAuth 2,0 che è possibile usare per l'accesso sicuro di un utente a un'applicazione. Quando si usa l'implementazione dell'endpoint della piattaforma di identità Microsoft di OpenID Connect, è possibile aggiungere l'accesso all'API e all'accesso alle app. In questo articolo viene illustrato come eseguire questa operazione indipendentemente dalla lingua e viene descritto come inviare e ricevere messaggi HTTP senza usare alcuna [libreria open source Microsoft](reference-v2-libraries.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2,0 per l'uso come protocollo di *autenticazione* , in modo che sia possibile eseguire Single Sign-on tramite OAuth. OpenID Connect introduce il concetto di *token ID*, ovvero un token di sicurezza che consente al client di verificare l'identità dell'utente. Il token ID consente anche di ottenere informazioni di base sul profilo dell'utente. Introduce anche l' [endpoint UserInfo](userinfo.md), un'API che restituisce informazioni sull'utente. 


## <a name="protocol-diagram-sign-in"></a>Diagramma di protocollo: Accesso

Il flusso di accesso di base include i passaggi illustrati nella figura seguente. Questo articolo descrive dettagliatamente i singoli passaggi.

![Protocollo OpenID Connect: Accesso](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Recuperare il documento di metadati OpenID Connect

OpenID Connect descrive un documento di metadati [(RFC)](https://openid.net/specs/openid-connect-discovery-1_0.html) che contiene la maggior parte delle informazioni necessarie per l'accesso di un'app. Il documento include informazioni come gli URL da usare e il percorso delle chiavi di firma pubbliche del servizio. È possibile trovare questo documento aggiungendo il percorso del documento di individuazione all'URL dell'autorità:

Percorso del documento di individuazione:`/.well-known/openid-configuration`

Autorità`https://login.microsoftonline.com/{tenant}/v2.0`

`{tenant}` può assumere uno dei quattro valori seguenti:

| valore | Descrizione |
| --- | --- |
| `common` |Possono accedere all'applicazione gli utenti con account Microsoft e account aziendali o dell'istituto d'istruzione di Azure AD. |
| `organizations` |Possono accedere all'applicazione solo gli utenti con account aziendali o dell'istituto d'istruzione di Azure AD. |
| `consumers` |Possono accedere all'applicazione solo gli utenti con account Microsoft personali. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` | Solo gli utenti di uno specifico tenant di Azure AD, che siano membri nella directory con un account aziendale o dell'istituto di istruzione oppure guest nella directory con un account Microsoft personale, possono accedere all'applicazione. È possibile usare il nome di dominio descrittivo del tenant di Azure AD o l'identificatore GUID. È anche possibile usare il tenant consumer, `9188040d-6c67-4c5b-b112-36a304b66dad`, al posto del tenant `consumers`.  |

L'autorità differisce tra i cloud nazionali, ad esempio `https://login.microsoftonline.de` per l'istanza di Azure ad Germania. Se non si usa il cloud pubblico, consultare gli endpoint del [cloud nazionale](authentication-national-cloud.md#azure-ad-authentication-endpoints) per trovare quello appropriato. Verificare che il tenant e `/v2.0/` siano presenti nella richiesta in modo che sia possibile usare la versione 2.0 dell'endpoint.

> [!TIP]
> Prova Fare clic [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) per visualizzare la `common` configurazione.

### <a name="sample-request"></a>Richiesta di esempio

Per chiamare l'endpoint UserInfo per l'autorità comune sul cloud pubblico, usare quanto segue:

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>Risposta di esempio

I metadati sono un semplice documento JavaScript Object Notation (JSON). Per un esempio, vedere il frammento di codice seguente. Il contenuto è descritto completamente nella [specifica di OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
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

Solo la ricezione di un id_token non è sempre sufficiente per autenticare l'utente. potrebbe anche essere necessario convalidare la firma del id_token e verificare le attestazioni nel token in base ai requisiti dell'app. Come tutte le piattaforme OIDC, l'endpoint della piattaforma Microsoft Identity usa i [token Web JSON (token JWT)](https://tools.ietf.org/html/rfc7519) e la crittografia a chiave pubblica per firmare i token ID e verificare che siano validi.

Non tutte le app traggono vantaggio dalla verifica del token ID: le app native e le app a singola pagina, ad esempio, traggono raramente vantaggio dalla convalida del token ID.  Un utente con accesso fisico al dispositivo (o browser) può ignorare la convalida in molti modi: dalla modifica del traffico Web al dispositivo per fornire token e chiavi Fake per eseguire semplicemente il debug dell'applicazione per ignorare la logica di convalida.  D'altra parte, le app Web e le API che usano un token ID per l'autorizzazione devono convalidare con attenzione il token ID poiché eseguono il controllo dell'accesso ai dati.

Dopo aver convalidato la firma di id_token, è necessario verificare alcune attestazioni. Vedere le [informazioni di riferimento su `id_token`](id-tokens.md) per altre informazioni, ad esempio relative alla [convalida del token](id-tokens.md#validating-an-id_token) e al [rollover della chiave di firma](active-directory-signing-key-rollover.md). È consigliabile usare una libreria per l'analisi e la convalida dei token. È disponibile almeno una libreria per la maggior parte dei linguaggi e delle piattaforme.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

* Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
* Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
* Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'[autenticazione a più fattori](../authentication/concept-mfa-howitworks.md).

Dopo aver convalidato id_token, è possibile avviare una sessione con l'utente e usare le attestazioni in id_token per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, la personalizzazione e così via.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagramma di protocollo: Acquisizione dei token di accesso

Molte app Web non richiedono soltanto l'accesso dell'utente, ma necessitano anche di accedere a un servizio Web per conto dell'utente tramite OAuth. In questo scenario viene usato OpenID Connect per l'autenticazione utente e contemporaneamente viene acquisito un codice di autorizzazione che può essere usato per ottenere i token di accesso quando si usa il flusso del codice di autorizzazione di OAuth.

Il flusso completo di accesso OpenID Connect e di acquisizione dei token è simile a quello illustrato nella figura seguente. Le sezioni seguenti dell'articolo descrivono i singoli passaggi.

![Protocollo OpenID Connect: Acquisizione dei token](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>Ottenere un token di accesso per chiamare UserInfo

Per acquisire un token per l'endpoint UserInfo di OIDC, modificare la richiesta di accesso:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

È anche possibile usare il [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md), il [flusso del codice del dispositivo](v2-oauth2-device-code.md)o un token di [aggiornamento](v2-oauth2-auth-code-flow.md#refresh-the-access-token) al posto di `response_type=token` per ottenere un token per l'app.

> [!TIP]
> Fare clic sul collegamento seguente per eseguire la richiesta. Dopo aver eseguito l'accesso, il browser viene reindirizzato a `https://localhost/myapp/` , con un token ID e un token nella barra degli indirizzi. Si noti che questa richiesta USA `response_mode=fragment` solo a scopo dimostrativo: per un WebApp è consigliabile usare `form_post` per una maggiore sicurezza, ove possibile. 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>Risposta token riuscita

La risposta con esito positivo quando si usa `response_mode=form_post` è simile alla seguente:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

I parametri di risposta indicano lo stesso risultato indipendentemente dal flusso usato per acquisirli.

| Parametro | Descrizione |
| --- | --- |
| `token` | Token che verrà usato per chiamare l'endpoint UserInfo.|
| `token_type` | Sempre "Bearer" |
| `expires_in`| Tempo di attesa fino alla scadenza del token di accesso, in secondi. |
| `scope` | Autorizzazioni concesse per il token di accesso.  Si noti che poiché l'endpoint UserInfo è ospitato in MS Graph, è possibile che siano presenti ambiti grafici aggiuntivi (ad esempio, User. Read) se in precedenza sono stati concessi all'app.  Questo perché un token per una determinata risorsa include sempre tutte le autorizzazioni concesse al client.  |
| `id_token` | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Altre informazioni sui token ID e il relativo contenuto sono disponibili nelle [informazioni di riferimento su `id_tokens`](id-tokens.md). |
| `state` | Se nella richiesta è incluso un parametro state, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

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

### <a name="calling-the-userinfo-endpoint"></a>Chiamata all'endpoint UserInfo

Esaminare la [documentazione di UserInfo](userinfo.md#calling-the-api) per esaminare il modo in cui la chiamata all'endpoint UserInfo con questo token.

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

## <a name="next-steps"></a>Passaggi successivi

* Esaminare la [documentazione di UserInfo](userinfo.md)
* Informazioni su come [personalizzare i valori in un token](active-directory-claims-mapping.md) con i dati dei sistemi locali. 
* Informazioni su come [includere attestazioni standard aggiuntive nei token](active-directory-optional-claims.md).  
