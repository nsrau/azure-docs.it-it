---
title: Usare Azure AD 2.0 per accedere a risorse sicure senza interazione dell'utente | Documentazione Microsoft
description: Creare applicazioni Web usando l'implementazione del protocollo di autenticazione OAuth 2.0 definita in Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25963d44960ec3ab15fdee2c264c3bf18e26c2a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540569"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory v2.0 e il flusso di credenziali client OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

La [concessione di credenziali client OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.4) specificata in RFC 6749, anche detta *OAuth a due vie*, può essere usata per accedere alle risorse ospitate sul Web tramite l'identità di un'applicazione. Questo tipo di concessione viene comunemente usato per le interazioni da server a server che devono essere eseguite in background, senza l'interazione immediata con un utente. Questo tipo di applicazioni vengono spesso definite *daemon* o *account di servizio*.

Il flusso di concessione delle credenziali client OAuth 2.0 consente a un servizio Web (client riservato) di usare le proprie credenziali, invece di rappresentare un utente, per l'autenticazione durante la chiamata a un altro servizio Web. In questo scenario il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web. Per un livello più elevato di sicurezza, Microsoft Identity Platform consente anche al servizio chiamante di usare un certificato (invece di un segreto condiviso) come credenziale.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure AD sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint 2.0, vedere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).

Nel più comune *OAuth a tre vie*, a un'applicazione client viene concessa l'autorizzazione per accedere a una risorsa per conto di un determinato utente. L'autorizzazione è delegata dall'utente all'applicazione, in genere, durante il processo di [concessione](v2-permissions-and-consent.md). Tuttavia, nel flusso di credenziali client (*OAuth a due vie*), le autorizzazioni vengono concesse direttamente all'applicazione stessa. Quando l'app presenta un token a una risorsa, quest'ultima obbliga l'app stessa, e non l'utente, ad avere l'autorizzazione per eseguire un'azione. 

## <a name="protocol-diagram"></a>Diagramma di protocollo

L'intero flusso di credenziali client ha un aspetto simile a quello illustrato nel diagramma seguente. Tutti i passaggi vengono descritti in seguito nell'articolo.

![Flusso di credenziali client](./media/v2-oauth2-client-creds-grant-flow/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Ottenere l'autorizzazione diretta

Un'app riceve generalmente l'autorizzazione diretta per accedere a una risorsa in uno dei due modi: 

* [Tramite un elenco di controllo di accesso (ACL) per la risorsa](#access-control-lists)
* [Tramite l'assegnazione di autorizzazioni dell'applicazione in Azure AD](#application-permissions)

Questi due metodi sono i più comuni in Azure AD e sono consigliati per i client e le risorse che eseguono il flusso di credenziali client. Una risorsa può anche scegliere di autorizzare i client in altri modi. Ciascun server di risorse può scegliere il metodo più adatto all'applicazione.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso

Un provider di risorse potrebbe imporre un controllo delle autorizzazioni in base a un elenco di ID applicazione (client) che riconosce e a cui concede uno specifico livello di accesso. Quando la risorsa riceve un token dall'endpoint 2.0, può decodificare il token ed estrarre l'ID dell'applicazione client dalle attestazioni `appid` e `iss`. A quel punto, la risorsa verifica la presenza dell'applicazione nell'elenco di controllo di accesso (ACL) esistente. La granularità e il metodo relativi all'elenco possono variare in maniera sostanziale da risorsa a risorsa.

Un caso d'uso comune prevede l'uso di un ACL per eseguire test per un'applicazione Web o per un'API Web. L'API Web potrebbe fornire solo un sottoinsieme delle autorizzazioni complete a un client specifico. Per eseguire test end-to-end sull'API, è necessario creare un client di prova che acquisisce i token dall'endpoint 2.0 e li invia all'API. L'API controlla quindi nell'ACL se è presente l'ID dell'applicazione del client di test per concedere accesso completo alle funzionalità dell'API. Se si usa questo tipo di ACL, assicurarsi di convalidare non solo il valore `appid` del chiamante, ma verificare anche che il valore `iss` del token sia attendibile.

Questo tipo di autorizzazione è comune per gli account daemon e di servizio che devono accedere ai dati di proprietà di utenti che dispongono di account Microsoft personale. Per i dati appartenenti a organizzazioni, è consigliabile acquisire l'autorizzazione necessaria tramite le autorizzazioni dell'applicazione.

### <a name="application-permissions"></a>Autorizzazioni dell'applicazione

Anziché utilizzare gli elenchi di controllo di accesso, è possibile utilizzare le API per esporre un set di autorizzazioni per l'applicazione. Un'autorizzazione dell'applicazione viene concessa a un'applicazione da un amministratore dell'organizzazione e può essere usata solo per accedere ai dati di proprietà dell'organizzazione e dei propri dipendenti. Ad esempio, Microsoft Graph espone diverse autorizzazioni dell'applicazione per le operazioni seguenti:

* Lettura di e-mail in tutte le caselle e-mail
* Lettura e scrittura di e-mail in tutte le caselle e-mail
* Invio di e-mail come utente
* Leggi i dati della directory

Per ulteriori informazioni sulle autorizzazioni per le applicazioni, vedere [Microsoft Graph](https://developer.microsoft.com/graph).

Per usare le autorizzazioni per le applicazioni nell'app, seguire i passaggi illustrati nelle sezioni successive.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione dell'app

1. Registrare e creare un'app tramite il [portale di registrazione delle applicazioni](quickstart-v2-register-an-app.md) o la nuova [esperienza Registrazioni app (anteprima)](quickstart-register-app.md).
1. Passare all'applicazione nel portale usato per registrare o creare l'app. Sarà necessario usare almeno un segreto dell'applicazione durante la creazione dell'app.
1. Individuare la sezione **Autorizzazioni API** e quindi aggiungere le **autorizzazioni dell'applicazione** richieste dall'app.
1. **Salvare** la registrazione dell'app.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Consigliato: Connettere l'utente all'applicazione

In genere, durante la compilazione di un'applicazione che usa le autorizzazioni, l'app necessita di una pagina o vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione. Questa pagina può essere parte del flusso di accesso all'app, delle impostazioni dell'applicazione o di un flusso di "connessione" dedicato. In molti casi, è utile per l'applicazione visualizzare la pagina di "connessione" solo dopo che un utente ha eseguito l'accesso con un account di lavoro o dell'istituto di istruzione Microsoft.

L'accesso dell'utente all'app consente di identificarne l'organizzazione di appartenenza prima di richiedere l'approvazione delle autorizzazioni dell'applicazione. Sebbene non sia strettamente necessario, questo consente di creare un'esperienza più intuitiva per gli utenti. Per l'accesso utente, seguire le [esercitazioni sui protocolli 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory

Quando si è pronti per richiedere le autorizzazioni dall'amministratore dell'azienda, è possibile reindirizzare l'utente sull'*endpoint di consenso dell'amministratore* 2.0.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametro | Condizione | DESCRIZIONE |
| --- | --- | --- |
| `tenant` | Obbligatorio | Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere fornito nel formato di nome descrittivo o GUID. Se non si conosce il tenant di appartenenza dell'utente e si desidera consentire l'accesso con qualsiasi tentant, usare `common`. |
| `client_id` | Obbligatorio | ID applicazione (client) assegnato all'app. È possibile trovare queste informazioni nel portale in cui è stata registrata l'app. |
| `redirect_uri` | Obbligatorio | URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL e disporre di segmenti di percorso aggiuntivi. |
| `state` | Consigliato | Valore incluso nella richiesta che viene restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |

A questo punto, Azure AD imporrà che solo un amministratore tenant possa accedere per completare la richiesta. L'amministratore dovrà approvare tutte le autorizzazioni dirette dell'applicazione richieste per l'app nel portale di registrazione.

##### <a name="successful-response"></a>Risposta con esito positivo

Se l'amministratore approva le autorizzazioni per l'applicazione, la risposta con esito positivo si presenta come segue:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametro | DESCRIZIONE |
| --- | --- |
| `tenant` | Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID. |
| `state` | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `admin_consent` | Impostare su **True**. |

##### <a name="error-response"></a>Risposta di errore

Se l'amministratore non approva le autorizzazioni per l'applicazione, la risposta di errore avrà l'aspetto seguente:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | DESCRIZIONE |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errori e correggerli. |
| `error_description` | Messaggio di errore specifico che consente di identificare la causa principale di un errore. |

Dopo aver ricevuto una risposta con esito positivo dall'endpoint di provisioning dell'app, l'applicazione ha ottenuto le autorizzazioni dirette dell'applicazione richieste. Successivamente, è possibile richiedere un token per la risorsa desiderata.

## <a name="get-a-token"></a>Acquisizione di un token

Dopo aver acquisito l'autorizzazione necessaria per l'applicazione, è possibile procedere con l'acquisizione dei token di accesso per le API. Per ottenere un token con concessione delle credenziali client, inviare una richiesta POST all'endpoint `/token` 2.0:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primo caso: richiesta del token di accesso con un segreto condiviso

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parametro | Condizione | DESCRIZIONE |
| --- | --- | --- |
| `tenant` | Obbligatorio | Tenant di directory su cui l'applicazione intende agire, in formato di GUID o nome di dominio. |
| `client_id` | Obbligatorio | ID applicazione assegnato all'app. È possibile trovare queste informazioni nel portale in cui è stata registrata l'app. |
| `scope` | Obbligatorio | Il valore passato per il parametro `scope` nella richiesta deve essere l'identificatore della risorsa (URI ID applicazione) della risorsa desiderata, con l'aggiunta del suffisso `.default`. Per l'esempio di Microsoft Graph, il valore deve essere `https://graph.microsoft.com/.default`. </br>Questo valore indica all'endpoint 2.0 che, per tutte le autorizzazioni dirette dell'applicazione configurate per l'app, è necessario emettere un token per le autorizzazioni riguardanti la risorsa da usare. Per altre informazioni sull'ambito `/.default`, vedere la [documentazione relativa al consenso](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Obbligatorio | Segreto dell'applicazione generato per l'app nel portale di registrazione. Prima di essere inviato, il segreto client deve essere codificato come URL. |
| `grant_type` | Obbligatorio | Il valore deve essere impostato su `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Secondo caso: richiesta del token di accesso con un certificato

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametro | Condizione | DESCRIZIONE |
| --- | --- | --- |
| `tenant` | Obbligatorio | Tenant di directory su cui l'applicazione intende agire, in formato di GUID o nome di dominio. |
| `client_id` | Obbligatorio |ID applicazione (client) assegnato all'app. |
| `scope` | Obbligatorio | Il valore passato per il parametro `scope` nella richiesta deve essere l'identificatore della risorsa (URI ID applicazione) della risorsa desiderata, con l'aggiunta del suffisso `.default`. Per l'esempio di Microsoft Graph, il valore deve essere `https://graph.microsoft.com/.default`. <br>Questo valore indica all'endpoint 2.0 che, per tutte le autorizzazioni dirette dell'applicazione configurate per l'app, è necessario emettere un token per le autorizzazioni riguardanti la risorsa da usare. Per altre informazioni sull'ambito `/.default`, vedere la [documentazione relativa al consenso](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Obbligatorio | Il valore deve essere impostato su `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Obbligatorio | Un'asserzione (un token JSON Web) che è necessario creare e firmare con il certificato registrato come credenziale per l'applicazione. Leggere l'articolo relativo alle [credenziali basate su certificato](active-directory-certificate-credentials.md) per informazioni sulla registrazione del certificato e il formato dell'asserzione.|
| `grant_type` | Obbligatorio | Il valore deve essere impostato su `client_credentials`. |

Si noti che i parametri sono quasi uguali a quelli usati nella richiesta tramite segreto condiviso, con l'eccezione del parametro client_secret che viene sostituito da due parametri: client_assertion_type e client_assertion.

### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo ha un aspetto simile al seguente:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametro | DESCRIZIONE |
| --- | --- |
| `access_token` | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| `token_type` | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è `bearer`. |
| `expires_in` | Periodo di validità di un token di accesso (in secondi). |

### <a name="error-response"></a>Risposta di errore

La risposta di errore è simile alla seguente:

```
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

| Parametro | DESCRIZIONE |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| `error_codes` | Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| `timestamp` | Data/ora in cui si è verificato l'errore. |
| `trace_id` | Identificatore univoco per la richiesta utile per la diagnostica. |
| `correlation_id` | Identificatore univoco per la richiesta utile per la diagnostica dei componenti. |

## <a name="use-a-token"></a>Usare di un token

Ora che è stato acquisito un token, è possibile usarlo per inoltrare richieste alla risorsa. Alla scadenza del token, ripetere la richiesta all'endpoint `/token` per ottwnere un token di accesso aggiornato.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Esempi di codice e altra documentazione

Leggere la [documentazione generale sulle credenziali client](https://aka.ms/msal-net-client-credentials) da Microsoft Authentication Library

| Esempio | Piattaforma |DESCRIZIONE |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Console .NET Core 2.1 | Un'applicazione .NET Core semplice che visualizza gli utenti di un tenant eseguendo una query in Microsoft Graph con la propria identità, anziché per conto di un utente. L'esempio illustra anche la variante in cui vengono usati certificati per l'autenticazione. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Un'applicazione Web che sincronizza i dati da Microsoft Graph usando la propria identità, anziché per conto di un utente. |
