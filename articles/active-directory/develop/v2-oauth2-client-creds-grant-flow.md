---
title: Flusso di credenziali client OAuth 2,0 nella piattaforma di identità Microsoft | Azure
description: Consente di creare applicazioni Web usando l'implementazione della piattaforma di identità Microsoft del protocollo di autenticazione OAuth 2,0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 7/27/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e5fe8e751077bc04850879d27827c197767a81c2
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87759071"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft Identity Platform e il flusso di credenziali client OAuth 2,0

La [concessione di credenziali client OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.4) specificata in RFC 6749, anche detta *OAuth a due vie*, può essere usata per accedere alle risorse ospitate sul Web tramite l'identità di un'applicazione. Questo tipo di concessione viene comunemente usato per le interazioni da server a server che devono essere eseguite in background, senza interazione immediata con un utente. Questi tipi di applicazioni sono spesso denominati *daemon* o *account di servizio*.

Questo articolo descrive come programmare direttamente in base al protocollo nell'applicazione. Quando possibile, è consigliabile usare le librerie di autenticazione Microsoft (MSAL) supportate anziché [acquisire i token e chiamare le API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Vedere anche le [app di esempio che usano MSAL](sample-v2-code.md).

Il flusso di concessione di credenziali client OAuth 2.0 permette a un servizio Web (client riservato) di usare le proprie credenziali per l'autenticazione in caso di chiamata a un altro servizio Web, invece di rappresentare un utente. In questo scenario il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web. Per un livello più elevato di sicurezza, Microsoft Identity Platform consente anche al servizio chiamante di usare un certificato (invece di un segreto condiviso) come credenziale.

Nel flusso di credenziali client, le autorizzazioni vengono concesse direttamente all'applicazione stessa da un amministratore. Quando l'app presenta un token a una risorsa, la risorsa impone che l'app stessa disponga dell'autorizzazione per eseguire un'azione poiché non è presente alcun utente nell'autenticazione.  Questo articolo illustra i passaggi necessari per [autorizzare un'applicazione a chiamare un'API](#application-permissions), nonché come [ottenere i token necessari per chiamare tale API](#get-a-token).

## <a name="protocol-diagram"></a>Diagramma del protocollo

L'intero flusso di credenziali client ha un aspetto simile a quello illustrato nel diagramma seguente. Ciascuno di questi passaggi viene descritto più avanti in questo articolo.

![Diagramma che mostra il flusso di credenziali client](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Ottenere l'autorizzazione diretta

Un'app riceve generalmente l'autorizzazione diretta per accedere a una risorsa in uno dei due modi:

* [Tramite un elenco di controllo di accesso (ACL) per la risorsa](#access-control-lists)
* [Tramite l'assegnazione di autorizzazioni dell'applicazione in Azure AD](#application-permissions)

Questi due metodi sono i più comuni in Azure AD e sono consigliati per i client e le risorse che eseguono il flusso di credenziali client. Una risorsa può anche scegliere di autorizzare i client in altri modi. Ciascun server di risorse può scegliere il metodo più adatto all'applicazione.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso

Un provider di risorse potrebbe imporre un controllo delle autorizzazioni in base a un elenco di ID applicazione (client) che riconosce e a cui concede uno specifico livello di accesso. Quando la risorsa riceve un token dall'endpoint della piattaforma di identità Microsoft, può decodificare il token ed estrarre l'ID dell'applicazione del client `appid` dalle `iss` attestazioni e. A quel punto, la risorsa verifica la presenza dell'applicazione nell'elenco di controllo di accesso (ACL) esistente. La granularità e il metodo relativi all'elenco possono variare in maniera sostanziale da risorsa a risorsa.

Un caso d'uso comune prevede l'uso di un ACL per eseguire test per un'applicazione Web o per un'API Web. L'API Web potrebbe fornire solo un sottoinsieme delle autorizzazioni complete a un client specifico. Per eseguire test end-to-end sull'API, creare un client di prova che acquisisca i token dall'endpoint della piattaforma Microsoft Identity e li invia all'API. L'API controlla quindi nell'ACL se è presente l'ID dell'applicazione del client di test per concedere accesso completo alle funzionalità dell'API. Se si usa questo tipo di ACL, assicurarsi di convalidare non solo il valore `appid` del chiamante, ma verificare anche che il valore `iss` del token sia attendibile.

Questo tipo di autorizzazione è comune per gli account daemon e di servizio che devono accedere ai dati di proprietà di utenti che dispongono di account Microsoft personale. Per i dati appartenenti a organizzazioni, è consigliabile acquisire l'autorizzazione necessaria tramite le autorizzazioni dell'applicazione.

> [!NOTE]
> Per abilitare questo modello di autorizzazione basata su ACL, Azure AD non richiede che le applicazioni siano autorizzate a ottenere i token per un'altra applicazione, quindi i token solo app possono essere emessi senza un' `roles` attestazione. Per accettare i token, le applicazioni che espongono le API devono implementare i controlli delle autorizzazioni.

### <a name="application-permissions"></a>Autorizzazioni dell'applicazione

Anziché utilizzare gli ACL, è possibile utilizzare le API per esporre un set di **autorizzazioni dell'applicazione**. Un'autorizzazione dell'applicazione viene concessa a un'applicazione da un amministratore dell'organizzazione e può essere usata solo per accedere ai dati di proprietà dell'organizzazione e dei propri dipendenti. Ad esempio, Microsoft Graph espone diverse autorizzazioni dell'applicazione per le operazioni seguenti:

* Lettura di e-mail in tutte le caselle e-mail
* Lettura e scrittura di e-mail in tutte le caselle e-mail
* Invio di e-mail come utente
* Lettura dati directory

Per usare le autorizzazioni dell'applicazione con la propria API (in contrapposizione a Microsoft Graph), è necessario prima [esporre l'API](quickstart-configure-app-expose-web-apis.md) definendo gli ambiti nella registrazione dell'app dell'API nel portale di Azure. Configurare quindi [l'accesso all'API](quickstart-configure-app-access-web-apis.md) selezionando tali autorizzazioni nella registrazione dell'app dell'applicazione client. Se non è stato esposto alcun ambito nella registrazione dell'app dell'API, non sarà possibile specificare le autorizzazioni dell'applicazione per tale API nella registrazione dell'app dell'applicazione client nell'portale di Azure.

Quando si esegue l'autenticazione come applicazione (in contrapposizione a un utente), non è possibile usare *autorizzazioni delegate* : ambiti concessi da un utente. È necessario usare le autorizzazioni dell'applicazione, note anche come ruoli, concesse da un amministratore per l'applicazione o tramite la preautorizzazione dall'API Web.

Per ulteriori informazioni sulle autorizzazioni dell'applicazione, vedere [autorizzazioni e consenso](v2-permissions-and-consent.md#permission-types).

#### <a name="recommended-sign-the-user-into-your-app"></a>Consigliato: accedere all'utente nell'app

In genere, durante la compilazione di un'applicazione che usa le autorizzazioni, l'app necessita di una pagina o vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione. Questa pagina può essere parte del flusso di accesso all'app, delle impostazioni dell'applicazione o di un flusso di "connessione" dedicato. In molti casi, è utile per l'applicazione visualizzare la pagina di "connessione" solo dopo che un utente ha eseguito l'accesso con un account di lavoro o dell'istituto di istruzione Microsoft.

Se si esegue l'accesso dell'utente all'app, è possibile identificare l'organizzazione a cui appartiene l'utente prima di richiedere all'utente di approvare le autorizzazioni dell'applicazione. Sebbene non sia strettamente necessario, questo consente di creare un'esperienza più intuitiva per gli utenti. Per accedere all'utente, seguire le [esercitazioni del protocollo Microsoft Identity Platform](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory

Quando si è pronti per richiedere le autorizzazioni all'amministratore dell'organizzazione, è possibile reindirizzare l'utente all'endpoint di *consenso dell'amministratore*di Microsoft Identity Platform.

> [!TIP]
> Provare a eseguire la richiesta in Postman. Usare il proprio ID app per ottenere risultati ottimali. l'applicazione dell'esercitazione non richiederà autorizzazioni utili. [ ![ Provare a eseguire la richiesta in un post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Suggerimento Pro: provare a incollare la richiesta seguente in un browser.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere fornito nel formato di nome descrittivo o GUID. Se non si conosce il tenant di appartenenza dell'utente e si desidera consentire l'accesso con qualsiasi tentant, usare `common`. |
| `client_id` | Obbligatoria | L'**ID dell'applicazione (client)** assegnato all'app dall'esperienza[Portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908). |
| `redirect_uri` | Obbligatoria | URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL e disporre di segmenti di percorso aggiuntivi. |
| `state` | Implementazione consigliata | Valore incluso nella richiesta che viene restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |

A questo punto, Azure AD impone che solo un amministratore tenant possa accedere per completare la richiesta. L'amministratore dovrà approvare tutte le autorizzazioni dirette dell'applicazione richieste per l'app nel portale di registrazione.

##### <a name="successful-response"></a>Risposta di esito positivo

Se l'amministratore approva le autorizzazioni per l'applicazione, la risposta con esito positivo si presenta come segue:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametro | Descrizione |
| --- | --- |
| `tenant` | Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID. |
| `state` | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `admin_consent` | Impostare su **true**. |

##### <a name="error-response"></a>Risposta di errore

Se l'amministratore non approva le autorizzazioni per l'applicazione, la risposta di errore avrà l'aspetto seguente:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | Descrizione |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errori e correggerli. |
| `error_description` | Messaggio di errore specifico che consente di identificare la causa principale di un errore. |

Dopo aver ricevuto una risposta con esito positivo dall'endpoint di provisioning dell'app, l'applicazione ha ottenuto le autorizzazioni dirette dell'applicazione richieste. Successivamente, è possibile richiedere un token per la risorsa desiderata.

## <a name="get-a-token"></a>Acquisizione di un token

Dopo aver acquisito l'autorizzazione necessaria per l'applicazione, è possibile procedere con l'acquisizione dei token di accesso per le API. Per ottenere un token usando la concessione delle credenziali client, inviare una richiesta POST all' `/token` endpoint della piattaforma di identità Microsoft:

> [!TIP]
> Provare a eseguire la richiesta in Postman. Usare il proprio ID app per ottenere risultati ottimali. l'applicazione dell'esercitazione non richiederà autorizzazioni utili. [ ![ Provare a eseguire la richiesta in un post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primo caso: richiesta di un token di accesso con un segreto condiviso

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | Tenant di directory su cui l'applicazione intende agire, in formato di GUID o nome di dominio. |
| `client_id` | Obbligatoria | ID applicazione assegnato all'app. È possibile trovare queste informazioni nel portale in cui è stata registrata l'app. |
| `scope` | Obbligatoria | Il valore passato per il parametro `scope` nella richiesta deve essere l'identificatore della risorsa (URI ID applicazione) della risorsa desiderata, con l'aggiunta del suffisso `.default`. Per l'esempio di Microsoft Graph, il valore deve essere `https://graph.microsoft.com/.default`. <br/>Questo valore indica all'endpoint della piattaforma di identità Microsoft che di tutte le autorizzazioni dirette dell'applicazione configurate per l'app, l'endpoint deve emettere un token per quelli associati alla risorsa che si vuole usare. Per altre informazioni sull'ambito `/.default`, vedere la [documentazione relativa al consenso](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Obbligatoria | Segreto client generato per l'app nel portale di registrazione delle app. Il segreto client deve essere codificato nell'URL prima dell'invio. |
| `grant_type` | Obbligatoria | Il valore deve essere impostato su `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Secondo caso: richiesta di un token di accesso con un certificato

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | Tenant di directory su cui l'applicazione intende agire, in formato di GUID o nome di dominio. |
| `client_id` | Obbligatoria |ID applicazione (client) assegnato all'app. |
| `scope` | Obbligatoria | Il valore passato per il parametro `scope` nella richiesta deve essere l'identificatore della risorsa (URI ID applicazione) della risorsa desiderata, con l'aggiunta del suffisso `.default`. Per l'esempio di Microsoft Graph, il valore deve essere `https://graph.microsoft.com/.default`. <br/>Questo valore informa l'endpoint della piattaforma di identità Microsoft che di tutte le autorizzazioni dirette dell'applicazione configurate per l'app, deve emettere un token per quelli associati alla risorsa che si vuole usare. Per altre informazioni sull'ambito `/.default`, vedere la [documentazione relativa al consenso](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Obbligatoria | Il valore deve essere impostato su `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Obbligatoria | Un'asserzione (un token JSON Web) che è necessario creare e firmare con il certificato registrato come credenziale per l'applicazione. Leggere l'articolo relativo alle [credenziali basate su certificato](active-directory-certificate-credentials.md) per informazioni sulla registrazione del certificato e il formato dell'asserzione.|
| `grant_type` | Obbligatoria | Il valore deve essere impostato su `client_credentials`. |

Si noti che i parametri sono quasi uguali a quelli usati nella richiesta tramite segreto condiviso, con l'eccezione del parametro client_secret che viene sostituito da due parametri: client_assertion_type e client_assertion.

### <a name="successful-response"></a>Risposta di esito positivo

Una risposta con esito positivo ha un aspetto simile al seguente:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametro | Descrizione |
| --- | --- |
| `access_token` | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| `token_type` | Indica il valore del tipo di token. L'unico tipo supportato da Microsoft Identity Platform è `bearer`. |
| `expires_in` | Periodo di validità di un token di accesso (in secondi). |

### <a name="error-response"></a>Risposta di errore

La risposta di errore è simile alla seguente:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametro | Descrizione |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| `error_codes` | Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| `timestamp` | Data/ora in cui si è verificato l'errore. |
| `trace_id` | Identificatore univoco per la richiesta utile per la diagnostica. |
| `correlation_id` | Identificatore univoco per la richiesta utile per la diagnostica dei componenti. |

## <a name="use-a-token"></a>Usare un token

Ora che hai acquisito un token, usalo per effettuare richieste alla risorsa. Alla scadenza del token, ripetere la richiesta all'endpoint `/token` per ottwnere un token di accesso aggiornato.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Esempi di codice e altra documentazione

Leggere la [documentazione generale sulle credenziali client](https://aka.ms/msal-net-client-credentials) da Microsoft Authentication Library

| Esempio | Piattaforma |Descrizione |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Console .NET Core 2.1 | Un'applicazione .NET Core semplice che visualizza gli utenti di un tenant eseguendo una query in Microsoft Graph con la propria identità, anziché per conto di un utente. L'esempio illustra anche la variante in cui vengono usati certificati per l'autenticazione. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Un'applicazione Web che sincronizza i dati da Microsoft Graph usando la propria identità, anziché per conto di un utente. |
