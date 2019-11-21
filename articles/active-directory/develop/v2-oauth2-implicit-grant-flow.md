---
title: Secure single-page applications using the Microsoft identity platform implicit flow | Azure
description: Building web applications using Microsoft identity platform implementation of the implicit flow for single-page apps.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1775106d7f8de9f6bbc2d9a36114e5bfda2625cb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207616"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft identity platform and Implicit grant flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

With the Microsoft identity platform endpoint, you can sign users into your single-page apps with both personal and work or school accounts from Microsoft. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche interessanti nell'ambito dell'autenticazione:

* Le caratteristiche di sicurezza di queste app sono considerevolmente diverse da quelle delle tradizionali applicazioni Web basate su server.
* Molti server di autorizzazione e provider di identità non supportano le richieste CORS.
* I reindirizzamenti dei browser a pagina intera dall'app diventano fastidiosi per l'esperienza utente.

For these applications (AngularJS, Ember.js, React.js, and so on), Microsoft identity platform supports the OAuth 2.0 Implicit Grant flow. Il flusso implicito è descritto nella [specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Its primary benefit is that it allows the app to get tokens from Microsoft identity platform without performing a backend server credential exchange. In questo modo l'app può far accedere l'utente, gestire la sessione e ottenere i token per altre API Web, il tutto nel codice JavaScript client. Esistono alcune importanti osservazioni sulla sicurezza da considerare quando si usa il flusso implicito, in particolare per la [rappresentazione del client](https://tools.ietf.org/html/rfc6749#section-10.3) e dell'[utente](https://tools.ietf.org/html/rfc6749#section-10.3).

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

Se, tuttavia, si preferisce non usare una libreria nell'app a pagina singola e inviare manualmente i messaggi dei protocolli, seguire questa procedura generale.

> [!NOTE]
> Not all Azure Active Directory (Azure AD) scenarios and features are supported by the Microsoft identity platform endpoint. To determine if you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra come appare l'intero flusso di accesso implicito e le sezioni seguenti descrivono ogni passaggio in modo più dettagliato.

![Diagram showing the implicit sign-in flow](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso

To initially sign the user into your app, you can send an [OpenID Connect](v2-protocols-oidc.md) authentication request and get an `id_token` from the Microsoft identity platform endpoint.

> [!IMPORTANT]
> To successfully request an ID token and/or an access token, the app registration in the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page must have the corresponding implicit grant flow enabled, by selecting **ID tokens** and.or **access tokens** under the **Implicit grant** section. If it's not enabled, an `unsupported_response` error will be returned: **The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> To test signing in using the implicit flow, click <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> After signing in, your browser should be redirected to `https://localhost/myapp/` with an `id_token` in the address bar.
>

| Parametro |  | Description |
| --- | --- | --- |
| `tenant` | obbligatorio |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obbligatorio | The Application (client) ID that the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page assigned to your app. |
| `response_type` | obbligatorio |Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere `token` come response_type. Usando qui il `token` , l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint. If you use the `token` response_type, the `scope` parameter must contain a scope indicating which resource to issue the token for (for example, user.read on Microsoft Graph).  |
| `redirect_uri` | Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| `scope` | obbligatorio |Elenco di ambiti separati da [spazi](v2-permissions-and-consent.md). For OpenID Connect (id_tokens), it must include the scope `openid`, which translates to the "Sign you in" permission in the consent UI. Optionally you may also want to include the `email` and `profile` scopes for gaining access to additional user data. You may also include other scopes in this request for requesting consent to various resources, if an access token is requested. |
| `response_mode` | facoltativo |Specifica il metodo da usare per restituire il token risultante all'app. Defaults to query for just an access token, but fragment if the request includes an id_token. |
| `state` | Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](https://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `nonce` | obbligatorio |Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. Obbligatorio solo quando viene richiesto un id_token. |
| `prompt` | facoltativo |Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi al momento sono "login", "none", "select_account" e "consent". `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On. `prompt=none` is the opposite - it will ensure that the user isn't presented with any interactive prompt whatsoever. If the request can't be completed silently via single-sign on, the Microsoft identity platform endpoint will return an error. `prompt=select_account` invia l'utente a un selettore di account in cui verranno visualizzati tutti gli account memorizzati nella sessione. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, che chiede all'utente di concedere le autorizzazioni all'app. |
| `login_hint`  |facoltativo |Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`.|
| `domain_hint` | facoltativo |If included, it will skip the email-based discovery process that user goes through on the sign in page, leading to a slightly more streamlined user experience. This is commonly used for Line of Business apps that operate in a single tenant, where they will provide a domain name within a given tenant.  This will forward the user to the federation provider for that tenant.  Note that this will prevent guests from signing into this application.  |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. The Microsoft identity platform endpoint will also ensure that the user has consented to the permissions indicated in the `scope` query parameter. Se l'utente non ha acconsentito a **nessuna** di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Per altre informazioni, vedere [autorizzazioni, consenso e app multi-tenant](v2-permissions-and-consent.md).

Once the user authenticates and grants consent, the Microsoft identity platform endpoint will return a response to your app at the indicated `redirect_uri`, using the method specified in the `response_mode` parameter.

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=fragment` e `response_type=id_token+token` è simile a quanto riportato di seguito. Sono state aggiunte interruzioni di riga per migliorare la leggibilità:

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametro | Description |
| --- | --- |
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app. The access token shouldn't be decoded or otherwise inspected, it should be treated as an opaque string. |
| `token_type` |Incluso se `response_type` include `token`. È sempre `Bearer`. |
| `expires_in`|Incluso se `response_type` include `token`. Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` |Incluso se `response_type` include `token`. Indica gli ambiti per cui access_token è valido. May not include all of the scopes requested, if they were not applicable to the user (in the case of Azure AD-only scopes being requested when a personal account is used to log in). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. The  app can cache the values and display them, but it shouldn't rely on them for any authorization or security boundaries. Per altre informazioni sugli oggetti id_token, vedere [`id_token reference`](id-tokens.md). <br> **Nota:** viene fornito solo è stato richiesto l'ambito `openid`. |
| `state` |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Description |
| --- | --- |
| `error` |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Getting access tokens silently in the background

Now that you've signed the user into your single-page app, you can silently get access tokens for calling web APIs secured by Microsoft identity platform, such as the [Microsoft Graph](https://developer.microsoft.com/graph). Anche se si è già ricevuto un token usando il `token` response_type, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

In the normal OpenID Connect/OAuth flow, you would do this by making a request to the Microsoft identity platform `/token` endpoint. However, the Microsoft identity platform endpoint does not support CORS requests, so making AJAX calls to get and refresh tokens is out of the question. È possibile usare invece il flusso implicito in un iframe nascosto per ottenere nuovi token per altre API Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Per informazioni dettagliate sui parametri di query nell'URL, vedere [Inviare la richiesta di accesso](#send-the-sign-in-request).

> [!TIP]
> Provare a copiare e incollare questa richiesta in una scheda del browser. (Non dimenticare di sostituire i valori `login_hint` con i valori corretti per l'utente)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Con il parametro `prompt=none` , questa richiesta avrà immediatamente esito positivo o negativo e farà tornare all'applicazione. Un risposta con esito positivo verrà inviata all'app all'indirizzo `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`.

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=fragment` ha un aspetto simile al seguente:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametro | Description |
| --- | --- |
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app, in questo caso per Microsoft Graph. The access token shouldn't be decoded or otherwise inspected, it should be treated as an opaque string. |
| `token_type` | È sempre `Bearer`. |
| `expires_in` | Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` | Indica gli ambiti per cui access_token è valido. May not include all of the scopes requested, if they were not applicable to the user (in the case of Azure AD-only scopes being requested when a personal account is used to log in). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. Incluso se `response_type` include `id_token`. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. The  app can cache the values and display them, but it shouldn't rely on them for any authorization or security boundaries. Per altre informazioni sugli oggetti id_token, vedere le [informazioni di riferimento su `id_token`](id-tokens.md). <br> **Nota:** viene fornito solo è stato richiesto l'ambito `openid`. |
| `state` |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente. Nel caso di `prompt=none`, un errore previsto sarà:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | Description |
| --- | --- |
| `error` |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

Se si riceve questo errore nella richiesta iframe, l'utente deve accedere di nuovo in modo interattivo per recuperare un nuovo token. È possibile scegliere di gestire questa situazione in qualsiasi modo appropriato per l'applicazione.

## <a name="refreshing-tokens"></a>Aggiornare i token

La concessione implicita non fornisce token di aggiornamento. I token `id_token` e `access_token` scadranno dopo un breve periodo, quindi l'app deve essere preparata per aggiornare regolarmente questi token. To refresh either type of token, you can perform the same hidden iframe request from above using the `prompt=none` parameter to control the identity platform's behavior. If you want to receive a new `id_token`, be sure to use `id_token` in the `response_type` and `scope=openid`, as well as a `nonce` parameter.

## <a name="send-a-sign-out-request"></a>Invio di una richiesta di disconnessione

The OpenID Connect `end_session_endpoint` allows your app to send a request to the Microsoft identity platform endpoint to end a user's session and clear cookies set by the Microsoft identity platform endpoint. Per disconnettere completamente un utente da un'applicazione Web, l'app deve terminare la sessione in cui è presente l'utente (in genere cancellando la cache di un token o eliminando i cookie) e quindi reindirizzare il browser all'indirizzo seguente:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametro |  | Description |
| --- | --- | --- |
| `tenant` |obbligatorio |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Consigliato | URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Questo valore deve corrispondere a uno degli URI di reindirizzamento registrati per l'applicazione. If not included, the user will be shown a generic message by the Microsoft identity platform endpoint. |

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a codificare, passare agli [esempi di MSAL JS](sample-v2-code.md).
