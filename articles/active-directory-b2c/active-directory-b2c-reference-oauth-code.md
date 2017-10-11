---
title: Flusso del codice di autorizzazione - Azure AD B2C | Microsoft Docs
description: Informazioni su come compilare app Web tramite Azure AD B2C e il protocollo di autenticazione OpenID Connect.
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: c371aaab-813a-4317-97df-b62e2f53d865
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: dfc4f2e84704307ccbea6141c0dbc8d089733b22
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: flusso del codice di autorizzazione di OAuth 2.0
È possibile usare la concessione del codice di autorizzazione OAuth 2.0 nelle app che vengono installate su un dispositivo per ottenere l'accesso a risorse protette, ad esempio le API Web. Con l'implementazione di Azure Active Directory B2C (Azure AD B2C) di OAuth 2.0 è possibile aggiungere attività di gestione dell'iscrizione, dell'accesso e altre attività di gestione delle identità alle app per desktop e per dispositivi mobili. Questo articolo è indipendente dal linguaggio. Descrive come inviare e ricevere messaggi HTTP senza usare alcuna libreria open source.

<!-- TODO: Need link to libraries -->

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749). È possibile usarlo per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui le [App Web](active-directory-b2c-apps.md#web-apps) e le [app installate in modo nativo](active-directory-b2c-apps.md#mobile-and-native-apps). È possibile usare il flusso del codice di autorizzazione di OAuth 2.0 per acquisire in modo sicuro *token di accesso* per le app, i quali possono essere usati per accedere a risorse protette da un [server di autorizzazione](active-directory-b2c-reference-protocols.md#the-basics).

Questo articolo illustra il flusso del codice di autorizzazione di OAuth 2.0 dei **client pubblici**. Un client pubblico è qualsiasi applicazione client che non può essere considerata attendibile in modo sicuro per mantenere l'integrità di una password segreta. Questo include app per dispositivi mobili, app per desktop e pressoché qualsiasi applicazione che viene eseguita su un dispositivo e deve ottenere token di accesso. 

> [!NOTE]
> Per aggiungere la gestione delle identità a un'App Web usando Azure AD B2C, usare [OpenID Connect](active-directory-b2c-reference-oidc.md) al posto di OAuth 2.0.

Azure AD B2C estende i flussi standard OAuth 2.0 per non limitarsi esclusivamente a semplici operazioni di autorizzazione e autenticazione. Introduce il [parametro di criteri](active-directory-b2c-reference-policies.md). Con i criteri integrati è possibile usare OAuth 2.0 per aggiungere esperienze utente all'app, ad esempio la gestione dell'iscrizione, dell'accesso e del profilo. Questo articolo illustra come usare OAuth 2.0 e i criteri per implementare ognuna di queste esperienze nelle applicazioni native. Illustra anche come ottenere i token di accesso per accedere alle API Web.

Nelle richieste HTTP di esempio in questo articolo si usa la directory di Azure AD B2C di esempio, **fabrikamb2c.onmicrosoft.com**. Si usano anche i criteri e l'applicazione di esempio. È possibile provare le richieste in autonomia usando questi valori o sostituendoli con valori personalizzati.
Altre informazioni su come [ottenere la directory, l'applicazione e i criteri di Azure AD B2C personalizzati](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Ottenere un codice di autorizzazione
Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize` . Questa è la parte interattiva del flusso, dove l'utente esegue operazioni. In questa richiesta il client indica nel parametro `scope` le autorizzazioni che deve acquisire dall'utente. Nel parametro `p` indica i criteri da eseguire. Di seguito vengono presentati tre esempi (con interruzioni di riga per migliorare la leggibilità), ognuno dei quali usa criteri diversi.

### <a name="use-a-sign-in-policy"></a>Uso di un criterio di accesso
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Uso di un criterio di iscrizione
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Uso di un criterio di modifica del profilo
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
| client_id |Obbligatorio |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com). |
| response_type |Obbligatorio |Tipo di risposta, che deve includere `code` per il flusso del codice di autorizzazione. |
| redirect_uri |Obbligatorio |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatorio |Elenco di ambiti separati da spazi. Un singolo valore di ambito indica ad Azure Active Directory (Azure AD) entrambe le autorizzazioni richieste. L'uso dell'ID client come ambito indica che l'app necessita di un token di accesso, che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client.  L'ambito `offline_access` indica che l'app necessita di un token di aggiornamento per avere un accesso di lunga durata alle risorse. È anche possibile usare l'ambito `openid` per richiedere un token ID ad Azure Active Directory B2C. |
| response_mode |Consigliato |Metodo da usare per inviare all'app il codice di autorizzazione risultante. Può essere `query`, `form_post` o `fragment`. |
| state |Consigliato |Valore incluso nella richiesta che viene restituito nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto si voglia usare. Per evitare attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Anche lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima del verificarsi della richiesta di autenticazione, ad esempio la pagina in cui si trova l'utente o i criteri che vengono eseguiti. |
| p |Obbligatorio |Criteri che vengono eseguiti. Si tratta del nome di criteri creati nella directory di Azure AD B2C. Il valore del nome dei criteri deve iniziare con **b2c\_1\_**. Per altre informazioni sui criteri, vedere l'articolo relativo ai [criteri predefiniti di Azure AD B2C](active-directory-b2c-reference-policies.md). |
| prompt |Facoltativo |Tipo di interazione utente obbligatoria. L'unico valore valido in questa fase è `login`, che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non avrà effetto. |

Viene a questo punto richiesto all'utente di completare il flusso di lavoro dei criteri. È possibile che venga richiesto all'utente di immettere nome utente e password, di accedere con un'identità di social networking, di iscriversi alla directory o di effettuare qualsiasi altro passaggio. Le azioni dell'utente dipendono dal modo in cui sono definiti i criteri.

Dopo che l'utente ha completato i criteri, Azure AD restituisce una risposta per l'app in corrispondenza del valore usato per `redirect_uri`. Viene usato il metodo specificato nel parametro `response_mode`. La risposta è esattamente la stessa per ogni scenario di azione dell'utente, indipendentemente dai criteri eseguiti.

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametro | Descrizione |
| --- | --- |
| code |Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per una risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve. In genere scadono dopo circa 10 minuti. |
| state |Vedere la descrizione completa nella tabella della sezione precedente. Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |

Anche le risposte di errore possono essere inviate all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che è possibile usare per classificare i tipi di errori che si verificano. È possibile usare la stringa anche per rispondere agli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| state |Vedere la descrizione completa nella tabella precedente. Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |

## <a name="2-get-a-token"></a>2. Acquisizione di un token
Ora che è stato acquisito il codice di autorizzazione, è possibile riscattare `code` per un token per la risorsa desiderata inviando una richiesta POST all'endpoint `/token`. In Azure AD B2C, l'unica risorsa per la quale è possibile richiedere un token è l'API Web back-end dell'app stessa. La convenzione usata per richiedere un token di questo tipo consiste nell'usare l'ID client dell'app come ambito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
| p |Obbligatorio |Il criterio utilizzato per acquisire il codice di autorizzazione. Non è possibile usare un criterio diverso in questa richiesta. Si noti che questo parametro viene aggiunto alla *stringa di query*, non al corpo della richiesta POST. |
| client_id |Obbligatorio |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com). |
| grant_type |Obbligatorio |Tipo di concessione. Per il flusso del codice di autorizzazione il tipo di concessione deve essere `authorization_code`. |
| scope |Consigliato |Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'uso dell'ID client come ambito indica che l'app necessita di un token di accesso, che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client.  L'ambito `offline_access` indica che l'app necessita di un token di aggiornamento per avere un accesso di lunga durata alle risorse.  È anche possibile usare l'ambito `openid` per richiedere un token ID ad Azure Active Directory B2C. |
| code |Obbligatorio |Codice di autorizzazione acquisito nella prima sezione del flusso. |
| redirect_uri |Obbligatorio |L'URI di reindirizzamento dell'applicazione dove è stato ricevuto il codice di autorizzazione. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | Descrizione |
| --- | --- |
| not_before |Il momento in cui il token viene considerato valido, nel periodo. |
| token_type |Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| access_token |Token JSON Web (JWT) firmato richiesto. |
| scope |Ambiti per i quali il token è valido. È possibile usare gli ambiti anche per memorizzare i token nella cache per un uso successivo. |
| expires_in |Periodo di validità del token (in secondi). |
| refresh_token |Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento sono di lunga durata. È possibile usarli per mantenere l'accesso alle risorse per periodi prolungati di tempo. Per altre informazioni, vedere le [informazioni di riferimento sul token di Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Le risposte di errore si presentano nel modo seguente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che è possibile usare per classificare i tipi di errori che si verificano. È possibile usare la stringa anche per rispondere agli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

## <a name="3-use-the-token"></a>3. Uso del token
Dopo avere acquisito un token di accesso, è possibile usarlo nelle richieste alle API Web back-end includendolo nell'intestazione `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Aggiornamento del token
I token di accesso e i token ID hanno breve durata. È necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. Inviare a tale scopo un'altra richiesta POST per l'endpoint `/token`. questa volta specificando `refresh_token` invece di `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
| p |Obbligatorio |Criteri usati per acquisire il token di aggiornamento originale. Non è possibile usare un criterio diverso in questa richiesta. Si noti che questo parametro viene aggiunto alla *stringa di query*, non al corpo della richiesta POST. |
| client_id |Consigliato |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com). |
| grant_type |Obbligatorio |Tipo di concessione. Per questa parte del flusso del codice di autorizzazione il tipo di concessione deve essere `refresh_token`. |
| scope |Consigliato |Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'uso dell'ID client come ambito indica che l'app necessita di un token di accesso, che può essere usato per il servizio o l'API Web, rappresentato dallo stesso ID client.  L'ambito `offline_access` indica che l'app necessita di un token di aggiornamento per un accesso di lunga durata alle risorse.  È anche possibile usare l'ambito `openid` per richiedere un token ID ad Azure Active Directory B2C. |
| redirect_uri |Facoltativo |L'URI di reindirizzamento dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| refresh_token |Obbligatorio |Token di aggiornamento originale acquisito nella seconda sezione del flusso. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | Descrizione |
| --- | --- |
| not_before |Il momento in cui il token viene considerato valido, nel periodo. |
| token_type |Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| access_token |Token JWT firmato richiesto. |
| scope |Ambiti per i quali il token è valido. È possibile usare gli ambiti anche per memorizzare i token nella cache per un uso successivo. |
| expires_in |Periodo di validità del token (in secondi). |
| refresh_token |Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento hanno durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per altre informazioni, vedere le [informazioni di riferimento sul token di Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Le risposte di errore si presentano nel modo seguente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che è possibile usare per classificare i tipi di errori che si verificano. È possibile usare la stringa anche per rispondere agli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Usare la directory di Azure AD B2C
Per provare queste richieste, completare i passaggi seguenti. Sostituire i valori dell'esempio usato in questo articolo con valori personalizzati.

1. [Creare una directory Azure AD B2C](active-directory-b2c-get-started.md). Usare il nome della directory nelle richieste.
2. [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione e un URI di reindirizzamento. Includere un client nativo nell'app.
3. [Creare i criteri](active-directory-b2c-reference-policies.md) per ottenere i nomi dei criteri.

