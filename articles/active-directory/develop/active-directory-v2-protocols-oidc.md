---
title: Azure Active Directory v2.0 e il protocollo OpenID | Documentazione Microsoft
description: Creare applicazioni Web usando l&quot;implementazione Azure Active Directory v2.0 del protocollo di autenticazione OpenID Connect.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: d24fd29cfe453a12d72998176177018f322e64d8
ms.openlocfilehash: 4e43304c108fceb7df70fc37898ffcf989beb922
ms.lasthandoff: 02/21/2017


---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Azure Active Directory v2.0 e il protocollo OpenID Connect
OpenID Connect è un protocollo di autenticazione basato su OAuth 2.0 che può essere usato per fare in modo che gli utenti accedano in modo protetto a un'applicazione Web. Quando si usa l'implementazione dell'endpoint v2.0 di OpenID Connect, è possibile aggiungere l'accesso e l'accesso all'API alle app basate sul Web. In questo articolo viene illustrato come eseguire l'operazione indipendentemente dal linguaggio. Verrà descritto come inviare e ricevere messaggi HTTP senza usare alcuna libreria open source Microsoft.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint&2;.0, vedere l'articolo relativo alle [limitazioni della versione&2;.0](active-directory-v2-limitations.md).
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 da usare come protocollo di *autenticazione* per consentire di eseguire l'accesso Single Sign-On tramite OAuth. OpenID Connect introduce il concetto di *token ID*, ovvero un token di sicurezza che consente al client di verificare l'identità dell'utente. Il token ID consente anche di ottenere informazioni di base sul profilo dell'utente. Poiché OpenID Connect estende OAuth 2.0, le app potranno acquisire in modo sicuro *token di accesso* che possono essere usati per accedere alle risorse protette da un [server di autorizzazione](active-directory-v2-protocols.md#the-basics). È consigliabile usare OpenID Connect per la compilazione di un'[applicazione Web](active-directory-v2-flows.md#web-apps) ospitata su un server e accessibile tramite browser.

## <a name="protocol-diagram-sign-in"></a>Diagramma di protocollo: accesso
Il flusso di accesso di base include i passaggi illustrati nella figura seguente. Questo articolo descrive dettagliatamente i singoli passaggi.

![Protocollo OpenID Connect: accesso](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Recuperare il documento di metadati OpenID Connect
OpenID Connect descrive un documento di metadati che contiene la maggior parte delle informazioni necessarie perché un'applicazione esegua l'accesso. Il documento include informazioni come gli URL da usare e il percorso delle chiavi di firma pubbliche del servizio. Per l'endpoint v2.0, usare il documento di metadati OpenID Connect seguente:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

`{tenant}` può assumere uno dei quattro valori seguenti:

| Valore | Descrizione |
| --- | --- |
| `common` |Possono accedere all'applicazione gli utenti con account Microsoft e account aziendali o dell'istituto d'istruzione di Azure Active Directory (Azure AD). |
| `organizations` |Possono accedere all'applicazione solo gli utenti con account aziendali o dell'istituto d'istruzione di Azure AD. |
| `consumers` |Possono accedere all'applicazione solo gli utenti con account Microsoft personali. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` oppure `contoso.onmicrosoft.com` |Possono accedere all'applicazione solo gli utenti con account aziendali o dell'istituto d'istruzione di un tenant specifico di Azure AD. È possibile usare il nome di dominio descrittivo del tenant di Azure AD o l'identificatore GUID. |

I metadati sono un semplice documento JavaScript Object Notation (JSON). Per un esempio, vedere il frammento di codice seguente. Il contenuto del frammento di codice è descritto dettagliatamente nelle [specifiche di OpenID Connect](https://openid.net).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

In genere, si usa questo documento di metadati per configurare una libreria di OpenID Connect o un SDK. La libreria usa i metadati per le proprie operazioni. Tuttavia, se non si usa una libreria OpenID Connect precompilata, è possibile seguire i passaggi nella parte restante di questo articolo per eseguire l'accesso in un'app Web tramite l'endpoint v&2;.0.

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso
Quando l'app Web deve autenticare l'utente, può indirizzarlo all'endpoint `/authorize` . Questa richiesta è simile a quella della prima parte del [flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols-oauth-code.md), con alcune importanti differenze:

* La richiesta deve includere l'ambito `openid` nel parametro `scope`.
* Il parametro `response_type` deve includere `id_token`.
* La richiesta deve includere il parametro `nonce` .

Ad esempio:

```
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
> Fare clic sul collegamento seguente per eseguire la richiesta. Dopo l'accesso, il browser verrà reindirizzato a https://localhost/myapp/ con un token ID nella barra degli indirizzi. Si noti che questa richiesta usa `response_mode=query` (solo a scopo dimostrativo). È consigliabile usare `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| tenant |Obbligatorio |È possibile usare il valore `{tenant}` nel percorso della richiesta per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sui protocolli](active-directory-v2-protocols.md#endpoints). |
| client_id |Obbligatorio |ID applicazione che il [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ha assegnato all'app. |
| response_type |Obbligatorio |Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere altri valori `response_types`, ad esempio `code`. |
| redirect_uri |Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatorio |Elenco di ambiti separati da spazi. Per OpenID Connect, deve includere l'ambito `openid`che esegue la conversione all'autorizzazione per l'accesso nell'interfaccia utente di consenso. È anche possibile includere in questa richiesta altri ambiti per richiedere il consenso. |
| nonce |Obbligatorio |Valore incluso nella richiesta, generato dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| response_mode |Consigliato |Specifica il metodo che deve essere usato per inviare il codice di autorizzazione risultante all'app. Può essere uno di `query`, `form_post` o `fragment`. Per le applicazioni Web è consigliabile usare `response_mode=form_post` per assicurare il trasferimento più sicuro dei token nell'applicazione. |
| state |Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](http://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Anche lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione corrente dell'utente. |
| prompt |Facoltativo |Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi al momento sono `login`, `none` e `consent`. L'attestazione `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On. L'attestazione `prompt=none` è l'opposto, ovvero garantisce che all'utente non venga visualizzata alcuna richiesta interattiva. Se la richiesta non può essere completata automaticamente tramite Single-Sign-On, l'endpoint v2.0 restituisce un errore. L'attestazione `prompt=consent` attiva la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente. La finestra di dialogo chiede all'utente di concedere le autorizzazioni per l'app. |
| login_hint |Facoltativo |È possibile usare questo parametro per pre-compilare i campi nome utente e indirizzo di posta elettronica nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`. |
| domain_hint |Facoltativo |Questo valore può essere `consumers` o `organizations`. Se incluso, non viene eseguito il processo di individuazione basato sulla posta elettronica a cui viene sottoposto l'utente nella pagina di accesso della versione&2;.0 per garantire un'esperienza utente più semplice. Le app usano spesso questo parametro durante la riautenticazione, estraendo l'attestazione `tid` dal token ID. Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`. In caso contrario, usare `domain_hint=organizations`. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint v2.0 verifica anche che l'utente abbia dato il consenso per le autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha dato il consenso per nessuna di queste autorizzazioni, l'endpoint v2.0 chiederà all'utente di dare il consenso per le autorizzazioni obbligatorie. Sono disponibili altre informazioni su [autorizzazioni, consenso e app multi-tenant](active-directory-v2-scopes.md).

Dopo che l'utente ha eseguito l'autenticazione e dato il consenso, l'endpoint v2.0 restituisce una risposta all'app nell'URI di reindirizzamento indicato usando il metodo specificato nel parametro `response_mode`.

### <a name="successful-response"></a>Risposta con esito positivo
La risposta con esito positivo quando si usa `response_mode=form_post` è simile alla seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametro | Descrizione |
| --- | --- |
| id_token |Token ID richiesto dall'app. È possibile usare il parametro `id_token` per verificare l'identità dell'utente e avviare una sessione con l'utente. Per altre informazioni sui token ID e il relativo contenuto, vedere il [riferimento ai token dell'endpoint v2.0](active-directory-v2-tokens.md). |
| state |Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

### <a name="error-response"></a>Risposta di errore
Le risposte di errore possono essere inviate anche all'URI di reindirizzamento in modo che l'app possa gestirle. La risposta di errore è simile alla seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Codici per gli errori dell'endpoint di autorizzazione
La tabella seguente descrive i codici di errore che possono essere restituiti nel parametro `error` della risposta di errore:

| Codice di errore | Descrizione | Azione client |
| --- | --- | --- |
| invalid_request |Errore del protocollo, ad esempio un parametro obbligatorio mancante. |Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| unauthorized_client |L'applicazione client non può richiedere un codice di autorizzazione. |Si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| access_denied |Il proprietario della risorsa ha negato il consenso. |L'applicazione client può notificare all'utente che non può proseguire a meno che l'utente non acconsenta. |
| unsupported_response_type |Il server di autorizzazione non supporta il tipo di risposta nella richiesta. |Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| server_error |Errore imprevisto rilevato dal server. |ripetere la richiesta. Questi errori possono dipendere da condizioni temporanee. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di un errore temporaneo. |
| temporarily_unavailable |Il server è temporaneamente troppo occupato per gestire la richiesta. |ripetere la richiesta. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |
| invalid_resource |La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è configurata correttamente. |Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |

## <a name="validate-the-id-token"></a>Convalidare il token ID
La ricezione di un token ID non è sufficiente per autenticare l'utente. È anche necessario convalidare la firma del token ID e verificare se le attestazioni nel token soddisfano i requisiti dell'app. L'endpoint&2;.0 usa i [token Web JSON](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

È possibile scegliere di convalidare il token ID nel codice client, ma una procedura comune consiste nell'inviare il token ID a un server back-end in cui eseguire la convalida. Dopo aver convalidato la firma del token ID, è necessario verificare alcune attestazioni. Per altre informazioni, ad esempio relative alla [convalida del token](active-directory-v2-tokens.md#validating-tokens) e al [rollover della chiave di firma](active-directory-v2-tokens.md#validating-tokens), vedere il [riferimento ai token della versione&2;.0](active-directory-v2-tokens.md). È consigliabile usare una libreria per analizzare e convalidare i token. È disponibile almeno una libreria per la maggior parte dei linguaggi e delle piattaforme.
<!--TODO: Improve the information on this-->

È anche possibile convalidare attestazioni aggiuntive, a seconda dello scenario. Alcune convalide comuni includono:

* Verificare che l'utente o l'organizzazione abbia eseguito la registrazione per l'app.
* Assicurarsi che l'utente abbia l'autorizzazione o i privilegi necessari.
* Verificare che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Per altre informazioni sulle attestazioni di un token ID, vedere il [riferimento ai token dell'endpoint v2.0](active-directory-v2-tokens.md).

Dopo aver convalidato il token ID, è possibile avviare una sessione con l'utente. Usare le attestazioni del token ID per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione
Attualmente, l'endpoint v2.0 non supporta `end_session_endpoint` di OpenID Connect. Ciò significa che l'app non può inviare una richiesta all'endpoint v2.0 per terminare una sessione utente e cancellare i cookie impostati dall'endpoint v2.0.
Per disconnettere un utente, l'app termina la sessione con l'utente e lascia la sessione utente con l'endpoint v2.0 invariata. Al successivo tentativo di accesso, all'utente viene visualizzata una pagina per la scelta dell'account con l'elenco degli account di accesso attivi. Nella pagina l'utente può scegliere di disconnettersi da qualsiasi account e terminare la sessione con l'endpoint v2.0.

<!--

When you want to sign out the user from your app, it isn't sufficient to clear your app's cookies or otherwise end the user's session. You must also redirect the user to the v2.0 endpoint to sign out. If you don't do this, the user re-authenticates to your app without entering their credentials again, because they will have a valid single sign-in session with the v2.0 endpoint.

You can redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Condition | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Recommended | The URL that the user is redirected to after successfully signing out. If the parameter is not included, the user is shown a generic message that's generated by the v2.0 endpoint. |

-->

## <a name="single-sign-out"></a>Single Sign-Out
L'endpoint v2.0 usa i cookie per identificare una sessione utente. Quando un utente accede per la prima volta a un'applicazione, l'endpoint v2.0 imposta un cookie nel browser dell'utente. Quando l'utente accede successivamente a un'altra applicazione, Azure AD controlla innanzitutto il cookie per stabilire se l'utente presenta una sessione di accesso valida con l'endpoint v2.0 di Azure AD invece di ripetere l'autenticazione dell'utente.

Analogamente, quando l'utente si disconnette per la prima volta da un'applicazione, l'endpoint v2.0 elimina il cookie dal browser. Tuttavia, l'utente può ancora essere connesso ad altre applicazioni che usano l'endpoint v2.0 di Azure AD per l'autenticazione. Per verificare che l'utente si disconnetta da tutte le applicazioni, l'endpoint v2.0 invia una richiesta HTTP GET all'oggetto `LogoutUrl` di tutte le applicazioni a cui l'utente è attualmente connesso. Le applicazioni devono rispondere a questa richiesta cancellando tutti i cookie che identificano la sessione dell'utente. È possibile impostare `LogoutUrl` da portale di Azure.

1. Passare al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure Directory facendo clic sull'account nell'angolo superiore destro della pagina.
3. Dal pannello di navigazione a sinistra, scegliere **Azure Active Directory**, quindi scegliere **Registrazioni per l'app** e selezionare l'applicazione di interesse.
4. Fare clic su **Proprietà** e cercare la casella di testo **URL disconnessione**. 

## <a name="protocol-diagram-token-acquisition"></a>Diagramma di protocollo: acquisizione dei token
Molte app Web non richiedono soltanto l'accesso dell'utente, ma necessitano anche di accedere a un servizio Web per conto dell'utente tramite OAuth. In questo scenario viene usato OpenID Connect per l'autenticazione utente e contemporaneamente viene acquisito un codice di autorizzazione che può essere usato per ottenere i token di accesso quando si usa il flusso del codice di autorizzazione di OAuth.

Il flusso completo di accesso OpenID Connect e di acquisizione dei token è simile a quello illustrato nella figura seguente. Le sezioni seguenti dell'articolo descrivono i singoli passaggi.

![Protocollo OpenID Connect: acquisizione dei token](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Ottenere i token di accesso
Per acquisire i token di accesso, modificare la richiesta di accesso:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Fare clic sul collegamento seguente per eseguire la richiesta. Dopo l'accesso, il browser viene reindirizzato a https://localhost/myapp/ con un token ID e un codice nella barra degli indirizzi. Si noti che questa richiesta usa `response_mode=query` (solo a scopo dimostrativo). È consigliabile usare `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

Includendo gli ambiti di autorizzazione nella richiesta e usando `response_type=id_token code`, l'endpoint v2.0 verificherà che l'utente abbia dato il consenso alle autorizzazioni indicate nel parametro di query `scope`. Verrà restituito nell'app un codice di autorizzazione da scambiare con un token di accesso.

### <a name="successful-response"></a>Risposta con esito positivo
La risposta con esito positivo quando si usa `response_mode=form_post` è simile alla seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametro | Descrizione |
| --- | --- |
| id_token |Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Altre informazioni sui token ID e il relativo contenuto sono disponibili nel [riferimento ai token dell'endpoint v2.0](active-directory-v2-tokens.md). |
| code |Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. La durata del codice di autorizzazione è molto breve. In genere, un codice di autorizzazione scade dopo circa 10 minuti. |
| state |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

### <a name="error-response"></a>Risposta di errore
Le risposte di errore possono essere inviate anche all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente. La risposta di errore è simile alla seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

Per una descrizione dei possibili codici di errore e delle risposte client consigliate, vedere [Codici per gli errori dell'endpoint di autorizzazione](#error-codes-for-authorization-endpoint-errors).

Dopo aver ottenuto un codice di autorizzazione e un token ID, è possibile far accedere l'utente e ottenere i token di accesso per suo conto. Per far accedere l'utente, è necessario convalidare il token ID [esattamente come descritto](#validate-the-id-token). Per ottenere i token di accesso, seguire i passaggi descritti nella [documentazione del protocollo OAuth](active-directory-v2-protocols-oauth-code.md#request-an-access-token).

