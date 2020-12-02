---
title: Flusso di concessione implicita OAuth 2,0-piattaforma di identità Microsoft | Azure
description: Proteggere le app a singola pagina usando Microsoft Identity Platform implicite Flow.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4b5465cc5c1c3447af5303a5c0bfe82874705362
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511194"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Piattaforma di identità Microsoft e flusso di concessione implicita

La piattaforma Microsoft Identity supporta il flusso di concessione implicita OAuth 2,0, come descritto nella [specifica oauth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). La caratteristica di definizione della concessione implicita è che i token (token ID o token di accesso) vengono restituiti direttamente dall'endpoint/Authorize anziché dall'endpoint/token. Questa operazione viene spesso usata come parte del [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md), in ciò che viene definito "flusso ibrido", recuperando il token ID nella richiesta/Authorize insieme a un codice di autorizzazione.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>Preferisci il flusso del codice di autenticazione

Con i piani per i [cookie di terze parti da rimuovere dai browser](reference-third-party-cookies-spas.md), il **flusso di concessione implicita non è più un metodo di autenticazione appropriato**.  Le [funzionalità di accesso SSO invisibile](#getting-access-tokens-silently-in-the-background) al flusso implicito non funzionano senza cookie di terze parti, causando interruzioni delle applicazioni quando tentano di ottenere un nuovo token. È consigliabile che tutte le nuove applicazioni usino il [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md) che ora supporta le app a pagina singola al posto del flusso implicito e che le [app a pagina singola esistenti inizino la migrazione anche al flusso del codice di autorizzazione](migrate-spa-implicit-to-auth-code.md) .

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Scenari adatti alla concessione implicita OAuth2

La concessione implicita è affidabile solo per la parte iniziale interattiva del flusso di accesso, in cui la mancanza di [cookie di terze parti](reference-third-party-cookies-spas.md) non può influire sull'applicazione. Questa limitazione significa che è consigliabile usarla esclusivamente come parte del flusso ibrido, in cui l'applicazione richiede un codice e un token dall'endpoint di autorizzazione. Ciò garantisce che l'applicazione riceva un codice che può essere riscattato per un token di aggiornamento, assicurando così che la sessione di accesso dell'app rimanga valida nel tempo.

## <a name="protocol-diagram"></a>Diagramma del protocollo

Il diagramma seguente illustra l'intero flusso di accesso implicito e le sezioni che seguono descrivono ogni passaggio in modo più dettagliato.

![Diagramma che mostra il flusso di accesso implicito](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso

Per eseguire inizialmente l'accesso dell'utente all'app, è possibile inviare una richiesta di autenticazione [OpenID Connect](v2-protocols-oidc.md) e ottenere un `id_token` dall'endpoint della piattaforma Microsoft Identity.

> [!IMPORTANT]
> Per richiedere un token ID e/o un token di accesso, per la registrazione dell'app nella pagina [portale di Azure-registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) deve essere abilitato il flusso di concessione implicito corrispondente, selezionando **token ID** e **token di accesso** nella sezione **concessione implicita** . Se non è abilitato, `unsupported_response` verrà restituito un errore: **il valore specificato per il parametro di input ' response_type ' non è consentito per questo client. Il valore previsto è' code '**

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
> Per testare l'accesso tramite il flusso implicito, fare clic su <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. .</a> Dopo l'accesso, il browser deve essere reindirizzato a `https://localhost/myapp/` con un `id_token` nella barra degli indirizzi.
>

| Parametro | Tipo | Descrizione |
| --- | --- | --- |
| `tenant` | obbligatorio |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obbligatorio | ID dell'applicazione (client) che la pagina [Portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) ha assegnato all'app. |
| `response_type` | necessario |Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere `token` come response_type. Usando qui il `token` , l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint. Se si usa la `token` response_type, il `scope` parametro deve contenere un ambito che indica la risorsa per cui emettere il token (ad esempio, User. read su Microsoft Graph). Può anche contenere al `code` posto di `token` per fornire un codice di autorizzazione, da usare nel [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md). Questa id_token + risposta al codice viene talvolta chiamata flusso ibrido.  |
| `redirect_uri` | Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| `scope` | necessario |Elenco di [ambiti](v2-permissions-and-consent.md)separati da spazi. Per OpenID Connect (id_tokens), deve includere l'ambito `openid` , che viene convertito nell'autorizzazione "Sign Your in" nell'interfaccia utente del consenso. Facoltativamente, è anche possibile includere gli `email` `profile` ambiti e per ottenere l'accesso a dati utente aggiuntivi. È anche possibile includere in questa richiesta altri ambiti per richiedere il consenso a diverse risorse, se è richiesto un token di accesso. |
| `response_mode` | facoltative |Specifica il metodo da usare per inviare il token risultante a un'app. Per impostazione predefinita viene eseguita una query solo per un token di accesso, ma Fragment se la richiesta include un id_token. |
| `state` | Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](https://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Il parametro state viene anche usato per codificare le informazioni sullo stato dell'utente nell'app attivo prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista attiva. |
| `nonce` | necessarie |Valore incluso nella richiesta, generato dall'app, che verrà incluso come attestazione nel parametro id_token risultante. L'app può quindi verificare questo valore per l'attenuazione degli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. Obbligatorio solo quando viene richiesto un valore id_token. |
| `prompt` | facoltativo |Indica il tipo di interazione utente richiesto. Gli unici valori validi al momento sono "login", "none", "select_account" e "consent". `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On. `prompt=none` è l'opposto: garantisce che all'utente non venga visualizzata alcuna richiesta interattiva. Se la richiesta non può essere completata in modo invisibile all'utente tramite Single Sign-on, l'endpoint della piattaforma Microsoft Identity restituisce un errore. `prompt=select_account` invia l'utente a un selettore di account in cui verranno visualizzati tutti gli account memorizzati nella sessione. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, che chiede all'utente di concedere le autorizzazioni all'app. |
| `login_hint`  |facoltative |Può essere usato per precompilare il campo nome utente/indirizzo di posta elettronica della pagina di accesso per l'utente, se ne conosci già il nome. Spesso le app utilizzeranno questo parametro durante la riautenticazione, avendo già estratto il nome utente da un accesso precedente usando l' `preferred_username` attestazione.|
| `domain_hint` | facoltativo |Se incluso, ignorerà il processo di individuazione basato sulla posta elettronica che l'utente passa alla pagina di accesso, ottenendo un'esperienza utente leggermente più semplificata. Questo parametro viene comunemente usato per le app line-of-business che operano in un singolo tenant, in cui forniranno un nome di dominio all'interno di un determinato tenant, che invia l'utente al provider federativo per il tenant.  Si noti che questo hint impedisce ai guest di accedere a questa applicazione e limita l'uso di credenziali cloud come FIDO.  |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint di Microsoft Identity Platform garantisce inoltre che l'utente ha dato il consenso all'autorizzazione indicata nel parametro di query `scope`. Se l'utente non ha acconsentito a **nessuna** di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Per altre informazioni, vedere [autorizzazioni, consenso e app multi-tenant](v2-permissions-and-consent.md).

Dopo che l'utente viene autenticato e fornisce il consenso, l'endpoint di Microsoft Identity Platform restituisce una risposta all'app nell'URI `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`.

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=fragment` e `response_type=id_token+code` è simile a quanto riportato di seguito. Sono state aggiunte interruzioni di riga per migliorare la leggibilità:

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametro | Descrizione |
| --- | --- |
| `code` | Incluso se `response_type` include `code`. Si tratta di un codice di autorizzazione adatto per l'uso nel [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md).  |
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app. Il token di accesso non deve essere decodificato o controllato in altro modo, deve essere trattato come una stringa opaca. |
| `token_type` |Incluso se `response_type` include `token`. È sempre `Bearer`. |
| `expires_in`|Incluso se `response_type` include `token`. Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` |Incluso se `response_type` include `token`. Indica gli ambiti per i quali avrà validità access_token. Potrebbe non includere tutti gli ambiti richiesti, se non sono applicabili all'utente (nel caso di ambiti solo Azure AD richiesti quando si usa un account personale per l'accesso). |
| `id_token` | Token JSON Web (JWT) firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per i limiti di autorizzazione o di sicurezza. Per altre informazioni sugli oggetti id_token, vedere [`id_token reference`](id-tokens.md). <br> **Nota:** Fornito solo se l' `openid` ambito è stato richiesto e `response_type` incluso `id_tokens` . |
| `state` |Se nella richiesta è incluso un parametro state, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| --- | --- |
| `error` |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Ottenere i token di accesso in modo invisibile all'utente in background

> [!Important]
> Questa parte del flusso implicito è improbabile che funzioni per l'applicazione, perché viene usata in diversi browser a causa della [rimozione dei cookie di terze parti per impostazione predefinita](reference-third-party-cookies-spas.md).  Anche se ancora attualmente funziona nei browser basati su Chrome che non sono in incognito, gli sviluppatori devono riconsiderare l'uso di questa parte del flusso. Nei browser che non supportano i cookie di terze parti, verrà visualizzato un errore che indica che nessun utente è connesso, perché i cookie di sessione della pagina di accesso sono stati rimossi dal browser. 

Ora che l'utente ha eseguito l'accesso all'app a singola pagina, è possibile ottenere i token di accesso in modo invisibile all'utente per chiamare le API Web protette dalla piattaforma di identità Microsoft, ad esempio la [Microsoft Graph](https://developer.microsoft.com/graph). Anche se si è già ricevuto un token usando il `token` response_type, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

Nel normale flusso OpenID Connect/OAuth, è possibile eseguire questa operazione effettuando una richiesta all'endpoint della piattaforma di identità Microsoft `/token` . È possibile eseguire la richiesta in un iframe nascosto per ottenere nuovi token per altre API Web:

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
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> Si noti che questa operazione funzionerà anche nei browser senza supporto per cookie di terze parti, perché si immette direttamente in una barra del browser anziché aprirla in un iframe. 

Con il parametro `prompt=none` , questa richiesta avrà immediatamente esito positivo o negativo e farà tornare all'applicazione. La risposta verrà inviata all'app all'indirizzo indicato `redirect_uri` , usando il metodo specificato nel `response_mode` parametro.

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=fragment` ha un aspetto simile al seguente:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parametro | Descrizione |
| --- | --- |
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app, in questo caso per Microsoft Graph. Il token di accesso non deve essere decodificato o controllato in altro modo, deve essere trattato come una stringa opaca. |
| `token_type` | È sempre `Bearer`. |
| `expires_in` | Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` | Indica gli ambiti per i quali avrà validità access_token. Potrebbe non includere tutti gli ambiti richiesti, se non sono applicabili all'utente (nel caso di ambiti solo Azure AD richiesti quando si usa un account personale per l'accesso). |
| `id_token` | Token JSON Web (JWT) firmato. Incluso se `response_type` include `id_token`. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per i limiti di autorizzazione o di sicurezza. Per ulteriori informazioni su id_tokens, vedere il [ `id_token` riferimento](id-tokens.md). <br> **Nota:** viene fornito solo se è stato richiesto l'ambito `openid`. |
| `state` |Se nella richiesta è incluso un parametro state, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente. Nel caso di `prompt=none`, un errore previsto sarà:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | Descrizione |
| --- | --- |
| `error` |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

Se si riceve questo errore nella richiesta iframe, l'utente deve accedere di nuovo in modo interattivo per recuperare un nuovo token. È possibile scegliere di gestire questa situazione in qualsiasi modo appropriato per l'applicazione.

## <a name="refreshing-tokens"></a>Aggiornare i token

La concessione implicita non fornisce token di aggiornamento. I token `id_token` e `access_token` scadranno dopo un breve periodo, quindi l'app deve essere preparata per aggiornare regolarmente questi token. Per aggiornare entrambi i tipi di token, è possibile eseguire la stessa richiesta iframe nascosta precedente usando il `prompt=none` parametro per controllare il comportamento della piattaforma di identità. Se si desidera ricevere un nuovo `id_token` , assicurarsi di utilizzare `id_token` in `response_type` e e `scope=openid` un `nonce` parametro.

Nei browser che non supportano i cookie di terze parti, verrà generato un errore che indica che nessun utente è connesso. 

## <a name="send-a-sign-out-request"></a>Invio di una richiesta di disconnessione

OpenID Connect `end_session_endpoint` consente all'app di inviare una richiesta all'endpoint della piattaforma di identità Microsoft per terminare la sessione di un utente e cancellare i cookie impostati dall'endpoint della piattaforma di identità Microsoft. Per disconnettere completamente un utente da un'applicazione Web, l'app deve terminare la sessione in cui è presente l'utente (in genere cancellando la cache di un token o eliminando i cookie) e quindi reindirizzare il browser all'indirizzo seguente:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametro | Tipo | Descrizione |
| --- | --- | --- |
| `tenant` |obbligatorio |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Consigliato | URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Questo valore deve corrispondere a uno degli URI di reindirizzamento registrati per l'applicazione. Se non è incluso, l'utente verrà visualizzato un messaggio generico dall'endpoint della piattaforma di identità Microsoft. |

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a codificare, passare agli [esempi di MSAL JS](sample-v2-code.md).
* Esaminare il [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md) come una nuova e migliore alternativa alla concessione implicita. 
