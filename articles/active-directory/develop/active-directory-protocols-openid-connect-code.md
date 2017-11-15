---
title: Informazioni sul flusso del codice di autenticazione OpenID Connect in Azure AD | Documentazione Microsoft
description: Questo articolo descrive come usare messaggi HTTP per autorizzare l'accesso ad applicazioni Web e API Web nel proprio tenant con Azure Active Directory e OpenID Connect.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 1bb944997caa0c43354e82bf9b1a70e3e104a476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizzare l'accesso ad applicazioni Web con OpenID Connect e Azure Active Directory
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) è un semplice livello di gestione delle identità basato sul protocollo OAuth 2.0. OAuth 2.0 definisce i meccanismi per ottenere e usare **token di accesso** per accedere a risorse protette, ma non definisce metodi standard per fornire informazioni relative all'identità. OpenID Connect implementa l'autenticazione come estensione del processo di autorizzazione di OAuth 2.0. Fornisce informazioni sull'utente finale sotto forma di un `id_token` che verifica l'identità dell'utente e fornisce informazioni di base sul profilo dell'utente.

È consigliabile usare OpenID Connect se si compila un'applicazione Web ospitata su un server e accessibile da browser.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Flusso di autenticazione con OpenID Connect
Il flusso di accesso di base include i passaggi seguenti: ognuno di essi è descritto in dettaglio di seguito.

![Flusso di autenticazione di OpenID Connect](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Documento di metadati OpenID Connect

OpenID Connect descrive un documento di metadati che contiene la maggior parte delle informazioni necessarie perché un'applicazione esegua l'accesso. Il documento include informazioni come gli URL da usare e il percorso delle chiavi di firma pubbliche del servizio. Il documento di metadati OpenID Connect è reperibile all'indirizzo:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
I metadati sono un semplice documento JavaScript Object Notation (JSON). Per un esempio, vedere il frammento di codice seguente. Il contenuto del frammento di codice è descritto dettagliatamente nelle [specifiche di OpenID Connect](https://openid.net).

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    
    ...
}
```

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso
Quando l'applicazione Web deve autenticare l'utente, deve indirizzarlo all'endpoint `/authorize` . Questa richiesta è simile a quella della prima parte di [Flusso del codice di autorizzazione di OAuth 2.0](active-directory-protocols-oauth-code.md), con alcune importanti differenze:

* La richiesta deve includere l'ambito `openid` nel parametro `scope`.
* Il parametro `response_type` deve includere `id_token`.
* La richiesta deve includere il parametro `nonce` .

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

| Parametro |  | Descrizione |
| --- | --- | --- |
| tenant |Obbligatoria |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione.  I valori consentiti sono gli identificatori dei tenant, ad esempio `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` o `common` per i token indipendenti dai tenant |
| client_id |Obbligatoria |ID applicazione assegnato all'app quando è stata registrata in Azure AD. ed è reperibile nel portale di Azure. Fare clic su **Azure Active Directory**, su **Registrazioni per l'app**, scegliere l'applicazione e individuarne applicazione nella pagina dell'applicazione. |
| response_type |Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect.  Può anche includere altri parametri response_type, ad esempio `code`. |
| scope |Obbligatoria |Elenco di ambiti separati da spazi.  Per OpenID Connect, deve includere l'ambito `openid`che esegue la conversione all'autorizzazione per l'accesso nell'interfaccia utente di consenso.  È anche possibile includere in questa richiesta altri ambiti per richiedere il consenso. |
| nonce |Obbligatoria |Valore incluso nella richiesta, generata dall'app, che verrà incluso nel `id_token` risultante come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token.  Il valore è in genere un GUID o una stringa univoca casuale che consente di identificare l'origine della richiesta. |
| redirect_uri |consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app.  Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| response_mode |Consigliato |Specifica il metodo che deve essere usato per inviare un codice di autorizzazione all'app.  I valori supportati sono `form_post` per *POST modulo HTTP* o `fragment` per *frammento URL*.  Per le applicazioni Web è consigliabile usare `response_mode=form_post` per assicurare il trasferimento più sicuro dei token nell'applicazione. |
| state |consigliato |Valore incluso nella richiesta che viene restituito nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto.  Per [evitare gli attacchi di richiesta intersito falsa](http://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente.  Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt |Facoltativa |Indica il tipo di interazione obbligatoria dell'utente.  Attualmente gli unici valori validi sono "login", "none" e "consent".  `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On.  `prompt=none` è l'opposto: garantisce che all'utente non venga presentata alcuna richiesta interattiva.  Se la richiesta non può essere completata automaticamente tramite Single-Sign-On, l'endpoint restituisce un errore.  `prompt=consent` attiva la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, che chiede all'utente di concedere le autorizzazioni all'app. |
| login_hint |Facoltativa |Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente.  Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`. |

A questo punto viene chiesto all'utente di immettere le credenziali e completare l'autenticazione.

### <a name="sample-response"></a>Risposta di esempio
Una risposta di esempio dopo l'autenticazione dell'utente può avere un aspetto simile al seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametro | Descrizione |
| --- | --- |
| id_token |`id_token` richiesto dall'app. È possibile usare `id_token` per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| state |Valore incluso nella richiesta che viene restituito nella risposta del token. Per [evitare gli attacchi di richiesta intersito falsa](http://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente.  Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |

### <a name="error-response"></a>Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codici per gli errori dell'endpoint di autorizzazione
La tabella seguente descrive i diversi codici errore che possono essere restituiti nel parametro `error` della risposta di errore.

| Codice di errore | Descrizione | Azione client |
| --- | --- | --- |
| invalid_request |Errore del protocollo, ad esempio un parametro obbligatorio mancante. |Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| unauthorized_client |All'applicazione client non è consentito richiedere un codice di autorizzazione. |Si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| access_denied |Consenso negato dal proprietario della risorsa |L'applicazione client può notificare all'utente che non può proseguire a meno che l'utente non acconsenta. |
| unsupported_response_type |Il server di autorizzazione non supporta il tipo di risposta nella richiesta. |Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| server_error |Errore imprevisto rilevato dal server. |ripetere la richiesta. Questi errori possono dipendere da condizioni temporanee. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di un errore temporaneo. |
| temporarily_unavailable |Il server è temporaneamente troppo occupato per gestire la richiesta. |ripetere la richiesta. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |
| invalid_resource |La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è attualmente configurata. |Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |

## <a name="validate-the-idtoken"></a>Convalidare il token ID
La semplice ricezione di un `id_token` non è sufficiente per autenticare l'utente. È necessario convalidare la firma e verificare le attestazioni in `id_token` in base ai requisiti dell'app. L'endpoint di Azure AD usa i token Web JSON (JWT) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

È possibile scegliere di convalidare `id_token` nel codice client, ma una procedura comune consiste nell'inviare `id_token` a un server back-end dove verrà eseguita la convalida. Dopo aver convalidato la firma di `id_token`, è necessario verificare alcune attestazioni.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

* Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
* Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
* Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Dopo aver completato la convalida di `id_token`, è possibile avviare una sessione con l'utente e usare le attestazioni in `id_token` per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via. Per altre informazioni sui tipi di token e sulle attestazioni, vedere [Token e tipi di attestazioni supportati](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione
Durante la disconnessione di un utente dall’applicazione, non basta cancellare i cookie dell’applicazione o terminare la sessione con l’utente.  È anche necessario reindirizzare l'utente a `end_session_endpoint` per la disconnessione.  In caso contrario l'utente potrà autenticarsi nuovamente all'app senza immettere le credenziali grazie alla presenza di una sessione Single Sign-On valida con l'endpoint di Azure AD.

È sufficiente reindirizzare l'utente all' `end_session_endpoint` riportato nel documento dei metadati di OpenID:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametro |  | Descrizione |
| --- | --- | --- |
| post_logout_redirect_uri |consigliato |URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione.  Se omesso, all'utente viene visualizzato un messaggio generico. |

## <a name="single-sign-out"></a>Single Sign-Out
Quando si reindirizza l'utente all'`end_session_endpoint`, Azure AD cancella la sessione dell'utente dal browser. L'utente può tuttavia essere ancora connesso ad altre applicazioni che usano Azure AD per l'autenticazione. Per consentire che tutte le applicazioni eseguano la disconnessione dell'utente simultaneamente, Azure AD invia una richiesta HTTP GET all'oggetto `LogoutUrl` registrato di tutte le applicazioni a cui l'utente è attualmente connesso. Le applicazioni devono rispondere a questa richiesta cancellando qualsiasi sessione che identifica l'utente e restituendo una risposta `200`.  Se si vuole supportare l'accesso Single Sign-Out nell'applicazione, è necessario implementare questo tipo di oggetto `LogoutUrl` nel codice dell'applicazione.  È possibile impostare `LogoutUrl` nel portale di Azure:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Fare clic sull'account nell'angolo superiore destro della pagina per scegliere l'istanza di Active Directory.
3. Nel pannello di navigazione a sinistra scegliere **Azure Active Directory**, quindi scegliere **Registrazioni per l'app** e selezionare l'applicazione desiderata.
4. Fare clic su **Proprietà** e individuare la casella di testo **URL disconnessione**. 

## <a name="token-acquisition"></a>Acquisizione dei token
Molte app Web non solo eseguono l'accesso dell'utente, ma accedono anche a un servizio Web per conto dell'utente tramite OAuth. Questo scenario combina OpenID Connect per l'autenticazione dell'utente e la contemporanea acquisizione di un `authorization_code` che può essere usato per ottenere `access_tokens` con il flusso del codice di autorizzazione di OAuth.

## <a name="get-access-tokens"></a>Ottenere i token di accesso
Per acquisire i token di accesso, è necessario modificare la richiesta di accesso precedente:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                     
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Includendo gli ambiti di autorizzazione nella richiesta e usando `response_type=code+id_token`, l'endpoint `authorize` verificherà che l'utente abbia dato il consenso per le autorizzazioni indicate nel parametro di query `scope` e restituirà all'app un codice di autorizzazione da scambiare con un token di accesso.

### <a name="successful-response"></a>Risposta con esito positivo
Una risposta con esito positivo che usa `response_mode=form_post` ha un aspetto simile al seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametro | Descrizione |
| --- | --- |
| id_token |`id_token` richiesto dall'app. È possibile usare `id_token` per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| code |Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata breve e in genere scadono dopo circa 10 minuti. |
| state |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

### <a name="error-response"></a>Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

Per una descrizione dei possibili codici di errore e l'azione consigliata per il client, vedere [Codici per gli errori dell'endpoint di autorizzazione](#error-codes-for-authorization-endpoint-errors).

Una volta ottenuti un `code` e un `id_token` di autorizzazione, è possibile far accedere l'utente e ottenere i token di accesso per suo conto.  Per far accedere l'utente è necessario convalidare l' `id_token` esattamente come descritto sopra. Per ottenere i token di accesso, è possibile seguire i passaggi descritti nella sezione "Usare un codice di autorizzazione per richiedere un token di accesso" della nostra [documentazione del protocollo OAuth](active-directory-protocols-oauth-code.md).
