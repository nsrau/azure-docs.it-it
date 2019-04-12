---
title: Proteggere le applicazioni a pagina singola tramite il flusso implicito di Microsoft identity platform | Azure
description: Creazione di applicazioni web che usano l'implementazione della piattaforma Microsoft identity il flusso implicito per le app a pagina singola.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453a3316288cbc0b07d82e2fad9ecc7c3d353e9b
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501315"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Flusso di concessione implicita e piattaforma delle identità Microsoft

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Con l'endpoint di piattaforma delle identità di Microsoft, è possibile accedere gli utenti nelle tue App a pagina singola con account sia personali e aziendali o dell'istituto di istruzione di Microsoft. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche interessanti nell'ambito dell'autenticazione:

* Le caratteristiche di sicurezza di queste app sono considerevolmente diverse da quelle delle tradizionali applicazioni Web basate su server.
* Molti server di autorizzazione e provider di identità non supportano le richieste CORS.
* I reindirizzamenti dei browser a pagina intera dall'app diventano fastidiosi per l'esperienza utente.

Per queste applicazioni (AngularJS, ember, React. js e così via), piattaforma delle identità Microsoft supporta il flusso di concessione implicita OAuth 2.0. Il flusso implicito è descritto nella [specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Il principale vantaggio è che consente all'app di ottenere i token da piattaforma delle identità Microsoft senza eseguire un server back-end scambio di credenziali. In questo modo l'app può far accedere l'utente, gestire la sessione e ottenere i token per altre API Web, il tutto nel codice JavaScript client. Esistono alcune importanti osservazioni sulla sicurezza da considerare quando si usa il flusso implicito, in particolare per la [rappresentazione del client](https://tools.ietf.org/html/rfc6749#section-10.3) e dell'[utente](https://tools.ietf.org/html/rfc6749#section-10.3).

Se si desidera utilizzare il flusso implicito e la piattaforma delle identità di Microsoft per aggiungere l'autenticazione all'App JavaScript, è consigliabile usare la libreria JavaScript open source, [msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Se, tuttavia, si preferisce non usare una libreria nell'app a pagina singola e inviare manualmente i messaggi dei protocolli, seguire questa procedura generale.

> [!NOTE]
> Non tutte le funzionalità e scenari di Azure Active Directory (Azure AD) sono supportate dall'endpoint della piattaforma Microsoft identity. Per determinare se è necessario usare l'endpoint di piattaforma Microsoft identity, a conoscenza [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra come appare l'intero flusso di accesso implicito e le sezioni seguenti descrivono ogni passaggio in modo più dettagliato.

![Corsie di OpenID Connect](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso

Per l'accesso iniziale dell'utente nell'app, è possibile inviare un' [OpenID Connect](v2-protocols-oidc.md) richiesta di autorizzazione e ottenere un `id_token` dall'endpoint di Microsoft identity platform.

> [!IMPORTANT]
> Per richiedere un token ID, la registrazione dell'app nel [portale di Azure - registrazioni di App](https://go.microsoft.com/fwlink/?linkid=2083908) pagina deve avere il flusso di concessione implicita abilitato correttamente, selezionando **i token di accesso** e**Token ID** sotto il **concessione implicita** sezione. Se non è abilitato, un `unsupported_response` viene restituito l'errore: **The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'** (Il valore fornito per il parametro di input 'response_type' non è consentito per questo client. Il valore previsto è 'code')

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
| `tenant` | Obbligatoria |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Obbligatoria | ID applicazione (client) che il [portale di Azure - registrazioni di App](https://go.microsoft.com/fwlink/?linkid=2083908) pagina assegnato all'app. |
| `response_type` | Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere `token` come response_type. Usando qui il `token` , l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint. Se si usa il `token` come response_type, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| `redirect_uri` | Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| `scope` | Obbligatoria |Elenco di ambiti separati da [spazi](v2-permissions-and-consent.md). Per OpenID Connect, deve includere l'ambito `openid`che esegue la conversione all'autorizzazione per l'accesso nell'interfaccia utente di consenso. Facoltativamente, è anche possibile includere l'ambito `email` o `profile` per ottenere l'accesso a dati aggiuntivi dell'utente. È anche possibile includere altri ambiti in questa richiesta per richiedere il consenso per varie risorse. |
| `response_mode` | Facoltativo |Specifica il metodo da usare per restituire il token risultante all'app. L'impostazione predefinita è la query per un token di accesso, ma è un frammento se la richiesta include un id_token. |
| `state` | Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](https://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `nonce` | Obbligatoria |Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. Obbligatorio solo quando viene richiesto un id_token. |
| `prompt` | Facoltativo |Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi al momento sono "login", "none", "select_account" e "consent". `prompt=login` si imporrà all'utente di immettere le credenziali alla richiesta, negando single sign-on. `prompt=none` è l'opposto: garantisce che l'utente non riceve alcuna richiesta interattiva. Se la richiesta non può essere completata automaticamente tramite single sign-on, l'endpoint di Microsoft identity platform restituirà un errore. `prompt=select_account` Invia all'utente a un selettore di account in cui tutti gli account memorizzati nella sessione verranno visualizzati. `prompt=consent` Attiva la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, in cui viene chiesto all'utente di concedere le autorizzazioni per l'app. |
| `login_hint`  |Facoltativo |Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username` .|
| `domain_hint` | Facoltativo |Può essere uno di `consumers` o `organizations`. Se incluso, non verrà eseguito il processo di individuazione basata sulla posta elettronica che l'utente passa attraverso nella pagina di accesso comporta un'esperienza utente leggermente semplificata. Le app usano spesso questo parametro durante la riautenticazione, estraendo l'attestazione `tid` dall'id_token. Se il valore di attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad` (tenant consumer dell'account Microsoft), usare `domain_hint=consumers`. In caso contrario, è possibile usare `domain_hint=organizations` durante la riautenticazione. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint di piattaforma di identità di Microsoft si assicurerà che l'utente ha acconsentito alle autorizzazioni indicate nel `scope` parametro di query. Se l'utente non ha acconsentito a **nessuna** di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Per altre informazioni, vedere [autorizzazioni, consenso e app multi-tenant](v2-permissions-and-consent.md).

Dopo che l'utente esegue l'autenticazione e concesso il consenso, l'endpoint di Microsoft identity platform restituirà una risposta per le app nell'URI `redirect_uri`, usando il metodo specificato nel `response_mode` parametro.

#### <a name="successful-response"></a>Risposta con esito positivo

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
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app, in questo caso per Microsoft Graph. Il token di accesso non deve essere decodificato o controllato in altro modo, dovrebbe essere considerato come una stringa opaca. |
| `token_type` |Incluso se `response_type` include `token`. È sempre `Bearer`. |
| `expires_in`|Incluso se `response_type` include `token`. Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` |Incluso se `response_type` include `token`. Indica gli ambiti per cui access_token è valido. Potrebbe non includere tutti gli ambiti richiesti, se non sono stati applicabili all'utente (nel caso di Azure AD gli ambiti solo richiesto quando viene usato un account persona per l'accesso). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma consigliabile non basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sugli oggetti id_token, vedere [`id_token reference`](id-tokens.md). <br> **Nota:** viene fornito solo se è stato richiesto l'ambito `openid`. |
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

## <a name="validate-the-idtoken"></a>Convalidare il token ID

Semplice ricezione di un token ID non è sufficiente per autenticare l'utente. è inoltre necessario convalidare la firma e verificare le attestazioni nel token in base ai requisiti dell'app. Usa l'endpoint di Microsoft identity platform [token Web JSON (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e crittografia a chiave pubblica per firmare i token e verificare che siano validi.

È possibile scegliere di convalidare `id_token` nel codice client, ma una procedura comune consiste nell'inviare `id_token` a un server back-end dove verrà eseguita la convalida. Dopo aver convalidato la firma dell'id_token, sono disponibili è necessario verificare alcune attestazioni. Vedere le [informazioni di riferimento su `id_token`](id-tokens.md) per altre informazioni, ad esempio relative alla [convalida del token](id-tokens.md#validating-an-id_token) e al [rollover della chiave di firma](active-directory-signing-key-rollover.md). È consigliabile usare una libreria per l'analisi e la convalida dei token. È disponibile almeno una libreria per la maggior parte dei linguaggi e delle piattaforme.

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

* Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
* Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
* Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Dopo aver convalidato il token ID, è possibile avviare una sessione con l'utente e usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'app. Queste informazioni sono utilizzabile per la visualizzazione, i record, personalizzazione e altro ancora.

## <a name="get-access-tokens"></a>Ottenere i token di accesso

Ora che l'utente dopo l'accesso alla tua app a pagina singola, è possibile ottenere i token di accesso per la chiamata API web protette da piattaforma delle identità Microsoft, ad esempio la [Microsoft Graph](https://developer.microsoft.com/graph). Anche se si è già ricevuto un token usando il `token` response_type, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

Nel normale flusso OpenID Connect/OAuth, verrebbe tale scopo, effettuare una richiesta alla piattaforma delle identità Microsoft `/token` endpoint. Tuttavia, l'endpoint di piattaforma di identità di Microsoft non supporta le richieste CORS, in modo da eseguire chiamate AJAX per ottenere token di aggiornamento è però le. È possibile usare invece il flusso implicito in un iframe nascosto per ottenere nuovi token per altre API Web: 

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

| Parametro | DESCRIZIONE |
| --- | --- |
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app, in questo caso per Microsoft Graph. Il token di accesso non deve essere decodificato o controllato in altro modo, dovrebbe essere considerato come una stringa opaca. |
| `token_type` | È sempre `Bearer`. |
| `expires_in` | Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` | Indica gli ambiti per cui access_token è valido. Potrebbe non includere tutti gli ambiti richiesti, se non sono stati applicabili all'utente (nel caso di Azure AD gli ambiti solo richiesto quando viene usato un account persona per l'accesso). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. Incluso se `response_type` include `id_token`. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma consigliabile non basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sugli oggetti id_token, vedere le [informazioni di riferimento su `id_token`](id-tokens.md). <br> **Nota:** viene fornito solo se è stato richiesto l'ambito `openid`. |
| `state` |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente. Nel caso di `prompt=none`, un errore previsto sarà:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | DESCRIZIONE |
| --- | --- |
| `error` |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

Se si riceve questo errore nella richiesta iframe, l'utente deve accedere di nuovo in modo interattivo per recuperare un nuovo token. È possibile scegliere di gestire questa situazione in qualsiasi modo appropriato per l'applicazione.

## <a name="validating-access-tokens"></a>Convalida dei token di accesso

Dopo aver ricevuto un oggetto access_token, assicurarsi di convalidare la firma del token e le attestazioni riportate di seguito. È anche possibile scegliere di convalidare attestazioni aggiuntive in base allo scenario specifico.

* Attestazione **destinatari**: per verificare che il token fosse destinato all'app
* **autorità di certificazione** di attestazione, verificare che il token sia stato rilasciato all'App dall'endpoint di Microsoft identity platform
* Attestazioni **non prima** e **scadenza**: per verificare che il token non sia scaduto

Per altre informazioni sulle attestazioni presenti nel token di accesso, vedere le [informazioni di riferimento sui token di accesso](access-tokens.md).

## <a name="refreshing-tokens"></a>Aggiornare i token

La concessione implicita non fornisce token di aggiornamento. I token `id_token` e `access_token` scadranno dopo un breve periodo, quindi l'app deve essere preparata per aggiornare regolarmente questi token. Per aggiornare entrambi i tipi di token, è possibile eseguire la stessa richiesta iframe nascosto nell'uso di `prompt=none` parametro per controllare il comportamento della piattaforma delle identità. Per ricevere un nuovo `id_token`, assicurarsi di usare `response_type=id_token` e `scope=openid`, oltre al parametro `nonce`.

## <a name="send-a-sign-out-request"></a>Invio di una richiesta di disconnessione

Di OpenID Connect `end_session_endpoint` consente all'app di inviare una richiesta all'endpoint di piattaforma di identità Microsoft per terminare una sessione utente e cancellare i cookie impostati dall'endpoint della piattaforma Microsoft identity. Per disconnettere completamente un utente da un'applicazione Web, l'app deve terminare la sessione in cui è presente l'utente (in genere cancellando la cache di un token o eliminando i cookie) e quindi reindirizzare il browser all'indirizzo seguente:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametro |  | DESCRIZIONE |
| --- | --- | --- |
| `tenant` |Obbligatoria |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Consigliato | URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Questo valore deve corrispondere a uno degli URI di reindirizzamento registrati per l'applicazione. Se omesso, l'utente visualizzerà un messaggio generico dall'endpoint della piattaforma Microsoft identity. |

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a codificare, passare agli [esempi di MSAL JS](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples).
