---
title: Flusso del codice di autorizzazione OAuth e della piattaforma di identità Microsoft | Azure
description: Creazione di applicazioni Web tramite l'implementazione della piattaforma di identità Microsoft del protocollo di autenticazione OAuth 2,0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a20f540d4d220d5cf7171633b817a496d48b8128
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207721"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Flusso del codice di autorizzazione di OAuth 2,0 e piattaforma di identità Microsoft

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

La concessione del codice di autorizzazione OAuth 2.0 può essere utilizzata nelle app che vengono installate su un dispositivo per ottenere l'accesso alle risorse protette, come l'API web. Usando l'implementazione della piattaforma di identità Microsoft di OAuth 2,0, è possibile aggiungere l'accesso e l'API di accesso alle app desktop e per dispositivi mobili. Questa guida, indipendente dal linguaggio, descrive come inviare e ricevere messaggi HTTP senza usare una delle [librerie di autenticazione open source di Azure](reference-v2-libraries.md).

Questo articolo descrive come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile usare invece le librerie di autenticazione Microsoft (MSAL) supportate per [acquisire i token e chiamare le API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Esaminare anche le [app di esempio che usano MSAL](sample-v2-code.md).

> [!NOTE]
> Non tutti gli scenari di Azure Active Directory & funzionalità sono supportati dall'endpoint della piattaforma di identità Microsoft. Per determinare se è necessario usare l'endpoint della piattaforma Microsoft Identity, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749). Viene usato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui app [Web](v2-app-types.md#web-apps) e [app installate](v2-app-types.md#mobile-and-native-apps)in modo nativo. Il flusso consente alle app di acquisire in modo sicuro access_tokens che possono essere usate per accedere alle risorse protette dall'endpoint della piattaforma di identità Microsoft.

## <a name="protocol-diagram"></a>Diagramma di protocollo

In generale, l'intero flusso di autenticazione per un'applicazione nativa/mobile ha un aspetto analogo al seguente:

![Flusso del codice di autenticazione di OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Richiedere un codice di autorizzazione

Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize`. In questa richiesta, il client indica le autorizzazioni che deve acquisire dall'utente:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> Fai clic sul collegamento seguente per eseguire questa richiesta. Dopo l'accesso, il browser deve essere reindirizzato a `https://localhost/myapp/` con un `code` nella barra degli indirizzi.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| .    | Obbligatorio/Facoltativo | DESCRIZIONE |
|--------------|-------------|--------------|
| `tenant`    | Obbligatorio    | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | Obbligatorio    | **ID dell'applicazione (client)** che la [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) l'esperienza assegnata all'app.  |
| `response_type` | Obbligatorio    | Deve includere `code` per il flusso del codice di autorizzazione.       |
| `redirect_uri`  | Obbligatorio | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. Per le app native e le app per dispositivi mobili è necessario usare il valore predefinito `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| `scope`  | Obbligatorio    | Elenco separato da spazi di [ambiti](v2-permissions-and-consent.md) a cui si vuole che l'utente dia il consenso.  Per la parte `/authorize` della richiesta, questo può coprire più risorse, consentendo all'app di ottenere il consenso per più API Web che si vuole chiamare. |
| `response_mode`   | consigliato | Specifica il metodo da usare per restituire il token risultante all'app. Può essere uno dei valori seguenti:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` fornisce il codice come parametro della stringa di query nell'URI di reindirizzamento. Se si richiede un token ID usando il flusso implicito, non è possibile usare `query` come specificato nella [specifica OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se si richiede solo il codice, è possibile usare `query`, `fragment`o `form_post`. `form_post` esegue un POST contenente il codice all'URI di reindirizzamento. Per altre informazioni, vedere [Protocollo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](https://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Questo valore può essere usato anche per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `prompt`  | Facoltativa    | Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi al momento sono `login`, `none` e `consent`.<br/><br/>- `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On.<br/>- `prompt=none` è l'opposto, assicurerà che all'utente non venga visualizzata alcuna richiesta interattiva. Se la richiesta non può essere completata in modo invisibile all'utente tramite Single Sign-on, l'endpoint della piattaforma di identità Microsoft restituirà un errore di `interaction_required`.<br/>- `prompt=consent` attiva la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, che chiede all'utente di concedere le autorizzazioni all'app. |
| `login_hint`  | Facoltativa    | Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username` .   |
| `domain_hint`  | Facoltativa    | Può essere uno di `consumers` o `organizations`.<br/><br/>Se incluso, ignorerà il processo di individuazione basato sulla posta elettronica utilizzato dall'utente nella pagina di accesso, ottenendo un'esperienza utente leggermente più semplificata. Le app usano spesso questo parametro durante la riautenticazione, estraendo `tid` da un accesso precedente. Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`. In caso contrario, usare `domain_hint=organizations`.  |
| `code_challenge_method` | Facoltativa    | Metodo usato per codificare `code_verifier` per il parametro `code_challenge`. Può avere uno dei valori seguenti:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Se escluso, `code_challenge` viene considerato testo non crittografato se `code_challenge` è incluso. Microsoft Identity Platform supporta sia `plain` sia `S256`. Per altre informazioni, vedere il [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | Facoltativa | Usato per proteggere i privilegi concessi sui codici di autorizzazione tramite la chiave di prova per Code Exchange (PKCE) da un client nativo. Obbligatorio con `code_challenge_method` incluso. Per altre informazioni, vedere il [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint della piattaforma Microsoft Identity garantisce inoltre che l'utente abbia acconsentito alle autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Questo articolo contiene informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant](v2-permissions-and-consent.md).

Una volta che l'utente esegue l'autenticazione e concede il consenso, l'endpoint della piattaforma di identità Microsoft restituirà una risposta all'app in corrispondenza del `redirect_uri`indicato, usando il metodo specificato nel parametro `response_mode`.

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| . | DESCRIZIONE  |
|-----------|--------------|
| `code` | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. Authorization_codes sono di breve durata, in genere scadono dopo circa 10 minuti. |
| `state` | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| . | DESCRIZIONE  |
|----------|------------------|
| `error`  | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codici per gli errori dell'endpoint di autorizzazione

La tabella seguente descrive i diversi codici errore che possono essere restituiti nel parametro `error` della risposta di errore.

| Codice di errore  | DESCRIZIONE    | Azione client   |
|-------------|----------------|-----------------|
| `invalid_request` | Errore del protocollo, ad esempio un parametro obbligatorio mancante. | Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| `unauthorized_client` | L'applicazione client non è autorizzata a richiedere un codice di autorizzazione. | Questo errore si verifica in genere quando l'applicazione client non è registrata in Azure AD o non viene aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| `access_denied`  | Consenso negato dal proprietario della risorsa  | L'applicazione client può notificare all'utente che non può proseguire a meno che l'utente non acconsente. |
| `unsupported_response_type` | Il server di autorizzazione non supporta il tipo di risposta nella richiesta. | Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale.  |
| `server_error`  | Errore imprevisto rilevato dal server.| ripetere la richiesta. Questi errori possono dipendere da condizioni temporanee. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di un errore temporaneo. |
| `temporarily_unavailable`   | Il server è temporaneamente troppo occupato per gestire la richiesta. | ripetere la richiesta. L'applicazione client può spiegare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |
| `invalid_resource`  | La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è configurata correttamente. | Questo errore indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| `login_required` | Troppi o nessun utente | Il client ha richiesto l'autenticazione invisibile all'utente (`prompt=none`), ma non è stato trovato un singolo utente. Questo può significare che sono presenti più utenti attivi nella sessione o nessun utente. In questo modo viene preso in considerazione il tenant scelto (ad esempio, se sono presenti due account Azure AD attivi e uno account Microsoft e `consumers` viene scelto, l'autenticazione invisibile all'utente funzionerà). |
| `interaction_required` | La richiesta richiede l'interazione dell'utente. | È necessario un passaggio di autenticazione o un consenso aggiuntivo. Ripetere la richiesta senza `prompt=none`. |

## <a name="request-an-access-token"></a>Richiedere un token di accesso

Dopo avere ottenuto un codice di autorizzazione e l'autorizzazione dell'utente, è possibile riscattare il valore `code` per un `access_token` per la risorsa desiderata. A tale scopo, inviare una richiesta `POST` all'endpoint `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Provare a eseguire la richiesta in Postman. (Non dimenticare di sostituire la `code`) [![provare a eseguire la richiesta in un post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| .  | Obbligatorio/Facoltativo | DESCRIZIONE     |
|------------|-------------------|----------------|
| `tenant`   | Obbligatorio   | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | Obbligatorio  | ID dell'applicazione (client) a cui è stata assegnata la pagina [portale di Azure-registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) all'app. |
| `grant_type` | Obbligatorio   | Deve essere `authorization_code` per il flusso del codice di autorizzazione.   |
| `scope`      | Obbligatorio   | Elenco di ambiti separati da spazi. Gli ambiti richiesti in questa sezione devono essere equivalenti agli ambiti richiesti nella prima sezione o un sottoinsieme di questi ultimi. Gli ambiti devono essere tutti di una singola risorsa, insieme agli ambiti OIDC (`profile`, `openid``email`). Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](v2-permissions-and-consent.md). |
| `code`          | Obbligatorio  | Codice di autorizzazione acquisito durante la prima sezione del flusso. |
| `redirect_uri`  | Obbligatorio  | Stesso valore redirect_uri usato per acquisire il codice di autorizzazione. |
| `client_secret` | Obbligatorio per app Web | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. Non è consigliabile usare il segreto dell'applicazione in un'app nativa perché client_secrets non può essere archiviato in modo affidabile nei dispositivi. È necessario per le app Web e le API Web, che hanno la possibilità di archiviare il client_secret in modo sicuro sul lato server.  Prima di essere inviato, il segreto client deve essere codificato come URL.  |
| `code_verifier` | Facoltativa  | Stesso code_verifier usato per ottenere il codice di autorizzazione. Obbligatorio se nella richiesta di concessione del codice di autorizzazione è stato usato PKCE. Per altre informazioni, vedere il [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Risposta con esito positivo

Una risposta token con esito positivo ha un aspetto simile al seguente:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| .     | DESCRIZIONE   |
|---------------|------------------------------|
| `access_token`  | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web.  |
| `token_type`    | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| `expires_in`    | Validità del token di accesso (espressa in secondi). |
| `scope`         | Ambiti per i quali il token di accesso è valido. |
| `refresh_token` | Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token di accesso aggiuntivi dopo la scadenza del token di accesso corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate sull'aggiornamento di un token di accesso, vedere la [sezione successiva](#refresh-the-access-token). <br> **Nota:** viene fornito solo è stato richiesto l'ambito `offline_access`. |
| `id_token`      | Token JSON Web (JWT). L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sugli oggetti id_token, vedere [`id_token reference`](id-tokens.md). <br> **Nota:** viene fornito solo è stato richiesto l'ambito `openid`. |

### <a name="error-response"></a>Risposta di errore

Le risposte di errore hanno un aspetto simile al seguente:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| .         | DESCRIZIONE    |
|-------------------|----------------|
| `error`       | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| `error_codes` | Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica.  |
| `timestamp`   | Ora in cui si è verificato l'errore. |
| `trace_id`    | Identificatore univoco per la richiesta utile per la diagnostica. |
| `correlation_id` | Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |

### <a name="error-codes-for-token-endpoint-errors"></a>Codici per gli errori degli endpoint di token

| Codice di errore         | DESCRIZIONE        | Azione client    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Errore del protocollo, ad esempio un parametro obbligatorio mancante. | Correggere e inviare di nuovo la richiesta.   |
| `invalid_grant`    | Il codice di autorizzazione o PKCE non è valido o è scaduto. | Provare una nuova richiesta per l'endpoint `/authorize` e verificare che il parametro code_verifier sia corretto.  |
| `unauthorized_client` | Il client autenticato non è autorizzato a utilizzare questo tipo di concessione di autorizzazione. | Questo problema si verifica in genere quando l'applicazione client non è registrata in Azure AD o non viene aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| `invalid_client` | Autenticazione client non riuscita.  | Le credenziali client non sono valide. Per risolvere il problema, l'amministratore applicazione aggiorna le credenziali.   |
| `unsupported_grant_type` | Il server di autorizzazione non supporta il tipo di concessione dell'autorizzazione. | Modificare il tipo di concessione nella richiesta. Questo tipo di errore dovrebbe verificarsi solo durante lo sviluppo ed essere rilevato durante il test iniziale. |
| `invalid_resource` | La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è configurata correttamente. | Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD.  |
| `interaction_required` | La richiesta richiede l'interazione dell'utente. Ad esempio, è necessario un passaggio di autenticazione aggiuntivo. | Ripetere la richiesta con la stessa risorsa.  |
| `temporarily_unavailable` | Il server è temporaneamente troppo occupato per gestire la richiesta. | ripetere la richiesta. L'applicazione client può spiegare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |

## <a name="use-the-access-token"></a>Usare il token di accesso

Dopo aver ottenuto un `access_token` è possibile usarlo in richieste alle API Web includendolo nell'intestazione `Authorization`:

> [!TIP]
> Eseguire la richiesta in Postman. (Sostituire prima l'intestazione `Authorization`) [![provare a eseguire la richiesta in un post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Aggiornare il token di accesso

I token di accesso hanno breve durata ed è quindi necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. A tale scopo, inviare un'altra richiesta `POST` all'endpoint `/token`, specificando il `refresh_token` anziché il valore `code`.  I token di aggiornamento sono validi per tutte le autorizzazioni per cui il client ha già ottenuto il consenso; in questo modo, un token di aggiornamento emesso su richiesta per `scope=mail.read` può essere usato per richiedere un nuovo token di accesso per `scope=api://contoso.com/api/UseResource`.  

I token di aggiornamento non hanno una durata specificata. La durata dei token di aggiornamento è in genere relativamente lunga. In alcuni casi, tuttavia, i token di aggiornamento scadono, vengono revocati o non hanno privilegi sufficienti per l'azione desiderata. L'applicazione deve prevedere e gestire correttamente gli [errori restituiti dall'endpoint di rilascio del token](#error-codes-for-token-endpoint-errors). 

Anche se i token di aggiornamento non vengono revocati quando vengono usati per acquisire nuovi token di accesso, è prevedibile rimuovere il vecchio token di aggiornamento. La [specifica OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-6) indica: "il server di autorizzazione può emettere un nuovo token di aggiornamento, nel qual caso il client deve rimuovere il token di aggiornamento precedente e sostituirlo con il nuovo token di aggiornamento. Il server di autorizzazione può revocare il token di aggiornamento precedente dopo aver emesso un nuovo token di aggiornamento per il client ".  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Provare a eseguire la richiesta in Postman. (Non dimenticare di sostituire la `refresh_token`) [![provare a eseguire la richiesta in un post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| .     |                | DESCRIZIONE        |
|---------------|----------------|--------------------|
| `tenant`        | Obbligatorio     | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | Obbligatorio    | **ID dell'applicazione (client)** che la [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) l'esperienza assegnata all'app. |
| `grant_type`    | Obbligatorio    | Deve essere `refresh_token` per questa sezione del flusso del codice di autorizzazione. |
| `scope`         | Obbligatorio    | Elenco di ambiti separati da spazi. Gli ambiti richiesti in questa sezione devono essere equivalenti agli ambiti richiesti nella sezione di richiesta del codice di autorizzazione originale o un sottoinsieme di questi ultimi. Se gli ambiti specificati in questa richiesta si estendono su più server di risorse, l'endpoint della piattaforma Microsoft Identity restituirà un token per la risorsa specificata nel primo ambito. Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](v2-permissions-and-consent.md). |
| `refresh_token` | Obbligatorio    | Token di aggiornamento acquisito durante la seconda sezione del flusso. |
| `client_secret` | Obbligatorio per app Web | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. Non deve essere usato in un'app nativa, perché client_secrets non può essere archiviato in modo affidabile nei dispositivi. È necessario per le app Web e le API Web, che hanno la possibilità di archiviare il client_secret in modo sicuro sul lato server. |

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta token con esito positivo ha un aspetto simile al seguente:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| .     | DESCRIZIONE         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| `token_type`    | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| `expires_in`    | Validità del token di accesso (espressa in secondi).   |
| `scope`         | Ambiti per i quali il token di accesso è valido.    |
| `refresh_token` | Nuovo token di aggiornamento di OAuth 2.0. È necessario sostituire il token di aggiornamento precedente con quello appena acquisito, per garantire che i token di aggiornamento rimangano validi il più a lungo possibile. <br> **Nota:** viene fornito solo è stato richiesto l'ambito `offline_access`.|
| `id_token`      | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sugli oggetti id_token, vedere [`id_token reference`](id-tokens.md). <br> **Nota:** viene fornito solo è stato richiesto l'ambito `openid`. |

#### <a name="error-response"></a>Risposta di errore

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| .         | DESCRIZIONE                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione.           |
| `error_codes` |Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| `timestamp` | Ora in cui si è verificato l'errore. |
| `trace_id` | Identificatore univoco per la richiesta utile per la diagnostica. |
| `correlation_id` | Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |

Per una descrizione dei codici di errore e l'azione consigliata per il client, vedere [Codici per gli errori degli endpoint di token](#error-codes-for-token-endpoint-errors).
