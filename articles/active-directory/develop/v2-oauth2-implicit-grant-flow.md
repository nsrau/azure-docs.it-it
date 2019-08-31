---
title: Proteggere le applicazioni a singola pagina usando il flusso implicito della piattaforma di identità Microsoft | Azure
description: Creazione di applicazioni Web tramite l'implementazione della piattaforma di identità Microsoft del flusso implicito per le app a singola pagina.
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
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab5ba4fde7469854954ed19d2e643f2b8a23f34f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193264"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Piattaforma di identità Microsoft e flusso di concessione implicita

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Con l'endpoint della piattaforma di identità Microsoft, è possibile accedere agli utenti nelle app a singola pagina con account personali e aziendali o dell'Istituto di istruzione di Microsoft. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche interessanti nell'ambito dell'autenticazione:

* Le caratteristiche di sicurezza di queste app sono considerevolmente diverse da quelle delle tradizionali applicazioni Web basate su server.
* Molti server di autorizzazione e provider di identità non supportano le richieste CORS.
* I reindirizzamenti dei browser a pagina intera dall'app diventano fastidiosi per l'esperienza utente.

Per queste applicazioni (AngularJS, Brac. js, React. js e così via), Microsoft Identity Platform supporta il flusso di concessione implicita OAuth 2,0. Il flusso implicito è descritto nella [specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Il suo vantaggio principale è che consente all'app di ottenere i token dalla piattaforma di identità Microsoft senza eseguire uno scambio di credenziali del server back-end. In questo modo l'app può far accedere l'utente, gestire la sessione e ottenere i token per altre API Web, il tutto nel codice JavaScript client. Esistono alcune importanti osservazioni sulla sicurezza da considerare quando si usa il flusso implicito, in particolare per la [rappresentazione del client](https://tools.ietf.org/html/rfc6749#section-10.3) e dell'[utente](https://tools.ietf.org/html/rfc6749#section-10.3).

Se si vuole usare il flusso implicito e la piattaforma di identità Microsoft per aggiungere l'autenticazione all'app JavaScript, è consigliabile usare la libreria JavaScript open source, [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Se, tuttavia, si preferisce non usare una libreria nell'app a pagina singola e inviare manualmente i messaggi dei protocolli, seguire questa procedura generale.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory (Azure AD) sono supportati dall'endpoint della piattaforma di identità Microsoft. Per determinare se è necessario usare l'endpoint della piattaforma Microsoft Identity, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra come appare l'intero flusso di accesso implicito e le sezioni seguenti descrivono ogni passaggio in modo più dettagliato.

![Diagramma che mostra il flusso di accesso implicito](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso

Per eseguire inizialmente l'accesso dell'utente all'app, è possibile inviare una richiesta di autenticazione [OpenID Connect](v2-protocols-oidc.md) e `id_token` ottenere un dall'endpoint della piattaforma Microsoft Identity.

> [!IMPORTANT]
> Per richiedere un token ID, è necessario che la registrazione dell'app nella pagina [portale di Azure-registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) abbia il flusso di concessione implicito abilitato correttamente, selezionando i **token di accesso** e i **token ID** nella **concessione implicita** sezione. Se non è abilitato, verrà restituito `unsupported_response` un errore: **The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'** (Il valore fornito per il parametro di input 'response_type' non è consentito per questo client. Il valore previsto è 'code')

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
> Per testare l'accesso con il flusso implicito, fare clic su <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Dopo l'accesso, il browser deve essere reindirizzato a `https://localhost/myapp/` con un `id_token` nella barra degli indirizzi.
>

| Parametro |  | DESCRIZIONE |
| --- | --- | --- |
| `tenant` | obbligatorio |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obbligatorio | ID dell'applicazione (client) che la pagina [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) assegnata all'app. |
| `response_type` | obbligatorio |Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere `token` come response_type. Usando qui il `token` , l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint. Se si usa il `token` come response_type, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| `redirect_uri` | Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| `scope` | obbligatorio |Elenco di ambiti separati da [spazi](v2-permissions-and-consent.md). Per OpenID Connect, deve includere l'ambito `openid`che esegue la conversione all'autorizzazione per l'accesso nell'interfaccia utente di consenso. Facoltativamente, è anche possibile includere l'ambito `email` o `profile` per ottenere l'accesso a dati aggiuntivi dell'utente. È anche possibile includere altri ambiti in questa richiesta per richiedere il consenso per varie risorse. |
| `response_mode` | facoltativo |Specifica il metodo da usare per restituire il token risultante all'app. L'impostazione predefinita è la query per un token di accesso, ma è un frammento se la richiesta include un id_token. |
| `state` | Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](https://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `nonce` | obbligatorio |Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. Obbligatorio solo quando viene richiesto un id_token. |
| `prompt` | facoltativo |Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi al momento sono "login", "none", "select_account" e "consent". `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On. `prompt=none`è l'opposto: garantisce che all'utente non venga visualizzata alcuna richiesta interattiva. Se la richiesta non può essere completata in modo invisibile all'utente tramite Single Sign-on, l'endpoint della piattaforma Microsoft Identity restituisce un errore. `prompt=select_account` invia l'utente a un selettore di account in cui verranno visualizzati tutti gli account memorizzati nella sessione. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, che chiede all'utente di concedere le autorizzazioni all'app. |
| `login_hint`  |facoltativo |Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username` .|
| `domain_hint` | facoltativo |Può essere uno di `consumers` o `organizations`. Se incluso, ignorerà il processo di individuazione basato sulla posta elettronica che l'utente passa alla pagina di accesso, ottenendo un'esperienza utente leggermente più semplificata. Le app usano spesso questo parametro durante la riautenticazione, estraendo l'attestazione `tid` dall'id_token. Se il valore di attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad` (tenant consumer dell'account Microsoft), usare `domain_hint=consumers`. In caso contrario, è possibile usare `domain_hint=organizations` durante la riautenticazione. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint della piattaforma Microsoft Identity garantisce inoltre che l'utente abbia acconsentito alle autorizzazioni indicate nel parametro di `scope` query. Se l'utente non ha acconsentito a **nessuna** di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Per altre informazioni, vedere [autorizzazioni, consenso e app multi-tenant](v2-permissions-and-consent.md).

Una volta che l'utente esegue l'autenticazione e concede il consenso, l'endpoint della piattaforma di identità Microsoft restituirà una risposta `redirect_uri`all'app all'indirizzo indicato, usando `response_mode` il metodo specificato nel parametro.

#### <a name="successful-response"></a>Risposta riuscita

Una risposta con esito positivo che usa `response_mode=fragment` e `response_type=id_token+token` è simile a quanto riportato di seguito. Sono state aggiunte interruzioni di riga per migliorare la leggibilità:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametro | DESCRIZIONE |
| --- | --- |
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app, in questo caso per Microsoft Graph. Il token di accesso non deve essere decodificato o controllato in altro modo, deve essere trattato come una stringa opaca. |
| `token_type` |Incluso se `response_type` include `token`. È sempre `Bearer`. |
| `expires_in`|Incluso se `response_type` include `token`. Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` |Incluso se `response_type` include `token`. Indica gli ambiti per cui access_token è valido. Potrebbe non includere tutti gli ambiti richiesti, se non sono applicabili all'utente (nel caso di ambiti solo Azure AD richiesti quando si usa un account personale per l'accesso). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per i limiti di autorizzazione o di sicurezza. Per altre informazioni sugli oggetti id_token, vedere [`id_token reference`](id-tokens.md). <br> **Nota:** viene fornito solo se è stato richiesto l'ambito `openid`. |
| `state` |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | DESCRIZIONE |
| --- | --- |
| `error` |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## <a name="validate-the-id_token"></a>Convalidare il token ID

Solo la ricezione di un token ID non è sufficiente per autenticare l'utente. è anche necessario convalidare la firma token ID e verificare le attestazioni nel token in base ai requisiti dell'app. L'endpoint della piattaforma Microsoft Identity usa i [token Web JSON (token JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificare che siano validi.

È possibile scegliere di convalidare `id_token` nel codice client, ma una procedura comune consiste nell'inviare `id_token` a un server back-end dove verrà eseguita la convalida. Dopo aver convalidato la firma del token ID, è necessario verificare alcune attestazioni. Vedere le [informazioni di riferimento su `id_token`](id-tokens.md) per altre informazioni, ad esempio relative alla [convalida del token](id-tokens.md#validating-an-id_token) e al [rollover della chiave di firma](active-directory-signing-key-rollover.md). È consigliabile usare una libreria per l'analisi e la convalida dei token. È disponibile almeno una libreria per la maggior parte dei linguaggi e delle piattaforme.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

* Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
* Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
* Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Dopo aver convalidato il token ID, è possibile avviare una sessione con l'utente e usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, la personalizzazione e altro ancora.

## <a name="get-access-tokens"></a>Ottenere i token di accesso

Ora che l'utente ha eseguito l'accesso all'app a singola pagina, è possibile ottenere i token di accesso per chiamare le API Web protette dalla piattaforma di identità Microsoft, ad esempio la [Microsoft Graph](https://developer.microsoft.com/graph). Anche se si è già ricevuto un token usando il `token` response_type, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

Nel normale flusso OpenID Connect/OAuth, è possibile eseguire questa operazione effettuando una richiesta all'endpoint della piattaforma `/token` di identità Microsoft. Tuttavia, l'endpoint della piattaforma di identità Microsoft non supporta le richieste CORS, quindi l'esecuzione di chiamate AJAX per ottenere e aggiornare i token è fuori dalla domanda. È possibile usare invece il flusso implicito in un iframe nascosto per ottenere nuovi token per altre API Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Per informazioni dettagliate sui parametri di query nell'URL, vedere [Inviare la richiesta di accesso](#send-the-sign-in-request).

> [!TIP]
> Provare a copiare e incollare questa richiesta in una scheda del browser. (Non dimenticare di sostituire i valori `login_hint` con i valori corretti per l'utente)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Con il parametro `prompt=none` , questa richiesta avrà immediatamente esito positivo o negativo e farà tornare all'applicazione. Un risposta con esito positivo verrà inviata all'app all'indirizzo `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`.

#### <a name="successful-response"></a>Risposta riuscita

Una risposta con esito positivo che usa `response_mode=fragment` ha un aspetto simile al seguente:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametro | Descrizione |
| --- | --- |
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app, in questo caso per Microsoft Graph. Il token di accesso non deve essere decodificato o controllato in altro modo, deve essere trattato come una stringa opaca. |
| `token_type` | È sempre `Bearer`. |
| `expires_in` | Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` | Indica gli ambiti per cui access_token è valido. Potrebbe non includere tutti gli ambiti richiesti, se non sono applicabili all'utente (nel caso di ambiti solo Azure AD richiesti quando si usa un account personale per l'accesso). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. Incluso se `response_type` include `id_token`. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per i limiti di autorizzazione o di sicurezza. Per altre informazioni sugli oggetti id_token, vedere le [informazioni di riferimento su `id_token`](id-tokens.md). <br> **Nota:** viene fornito solo se è stato richiesto l'ambito `openid`. |
| `state` |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente. Nel caso di `prompt=none`, un errore previsto sarà:

```
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

La concessione implicita non fornisce token di aggiornamento. I token `id_token` e `access_token` scadranno dopo un breve periodo, quindi l'app deve essere preparata per aggiornare regolarmente questi token. Per aggiornare entrambi i tipi di token, è possibile eseguire la stessa richiesta iframe nascosta precedente usando il `prompt=none` parametro per controllare il comportamento della piattaforma di identità. Per ricevere un nuovo `id_token`, assicurarsi di usare `response_type=id_token` e `scope=openid`, oltre al parametro `nonce`.

## <a name="send-a-sign-out-request"></a>Invio di una richiesta di disconnessione

OpenID Connect `end_session_endpoint` consente all'app di inviare una richiesta all'endpoint della piattaforma di identità Microsoft per terminare la sessione di un utente e cancellare i cookie impostati dall'endpoint della piattaforma di identità Microsoft. Per disconnettere completamente un utente da un'applicazione Web, l'app deve terminare la sessione in cui è presente l'utente (in genere cancellando la cache di un token o eliminando i cookie) e quindi reindirizzare il browser all'indirizzo seguente:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametro |  | Descrizione |
| --- | --- | --- |
| `tenant` |obbligatorio |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Consigliato | URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Questo valore deve corrispondere a uno degli URI di reindirizzamento registrati per l'applicazione. Se non è incluso, l'utente verrà visualizzato un messaggio generico dall'endpoint della piattaforma di identità Microsoft. |

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a codificare, passare agli [esempi di MSAL JS](sample-v2-code.md).
