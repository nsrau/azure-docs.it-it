---
title: Flusso di concessione implicita OAuth 2.0 - Piattaforma di identità Microsoft Azure
description: Proteggere le app a pagina singola usando il flusso implicito della piattaforma di identità Microsoft.Secure single-page apps using Microsoft identity platform implicit flow.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 89ae088b9cbb3bb3c593cfcbbfb4ce619baccfa8
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868418"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Piattaforma di identità Microsoft e flusso di concessione implicitaMicrosoft identity platform and Implicit grant flow

With the Microsoft identity platform endpoint, you can sign users into your single-page apps with both personal and work or school accounts from Microsoft. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche interessanti nell'ambito dell'autenticazione:

* Le caratteristiche di sicurezza di queste app sono considerevolmente diverse da quelle delle tradizionali applicazioni Web basate su server.
* Molti server di autorizzazione e provider di identità non supportano le richieste CORS.
* I reindirizzamenti dei browser a pagina intera dall'app diventano fastidiosi per l'esperienza utente.

Per queste applicazioni (Angular, Ember.js, React.js e così via), la piattaforma di identità Microsoft supporta il flusso di concessione implicita OAuth 2.0. Il flusso implicito è descritto nella [specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Il suo vantaggio principale è che consente all'app di ottenere token dalla piattaforma di identità Microsoft senza eseguire uno scambio di credenziali del server back-end. In questo modo l'app può far accedere l'utente, gestire la sessione e ottenere i token per altre API Web, il tutto nel codice JavaScript client. Esistono alcune importanti osservazioni sulla sicurezza da considerare quando si usa il flusso implicito, in particolare per la [rappresentazione del client](https://tools.ietf.org/html/rfc6749#section-10.3) e dell'[utente](https://tools.ietf.org/html/rfc6749#section-10.3).

In questo articolo viene descritto come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile utilizzare le librerie di autenticazione Microsoft (MSAL) supportate per [acquisire token e chiamare API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dai anche un'occhiata alle [app di esempio che utilizzano MSAL](sample-v2-code.md).

Se, tuttavia, si preferisce non usare una libreria nell'app a pagina singola e inviare manualmente i messaggi dei protocolli, seguire questa procedura generale.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Scenari adatti alla concessione implicita OAuth2

La specifica OAuth2 dichiara che la concessione implicita è stata definita per abilitare le applicazioni agente utente, ovvero le applicazioni JavaScript in esecuzione in un browser. La caratteristica distintiva di tali applicazioni è che il codice JavaScript viene utilizzato per accedere alle risorse del server (in genere un'API web) e per aggiornare l'esperienza utente dell'applicazione di conseguenza. Ad esempio in applicazioni come Gmail o Outlook Web Access, quando si seleziona un messaggio dalla Posta in arrivo, solo il pannello per la visualizzazione dei messaggi viene modificato per poter visualizzare la nuova selezione, mentre il resto della pagina rimane invariato. Avviene invece il contrario nelle app Web basate sul reindirizzamento tradizionale, in cui ogni interazione dell'utente restituisce un postback di pagina completo e un rendering di pagina completo della nuova risposta del server.

Le applicazioni che portano l'approccio basato su JavaScript all'estremo sono chiamate applicazioni a pagina singola o SPA. L'idea è che queste applicazioni servono solo una pagina HTML iniziale e JavaScript associato, con tutte le interazioni successive guidate da chiamate API web eseguite tramite JavaScript. Gli approcci ibridi, tuttavia, in cui l'applicazione è basata principalmente sul postback, ma esegue chiamate JS occasionali, non sono comuni e il discorso sull'utilizzo del flusso implicito riguarda anche tali approcci.

Le applicazioni basate sul reindirizzamento in genere proteggono le richieste tramite cookie, tuttavia questo approccio non funziona anche per le applicazioni JavaScript. I cookie funzionano solo nel dominio per cui sono stati generati, mentre le chiamate JavaScript potrebbero essere dirette ad altri domini. Di fatto ciò accade molto spesso. Si pensi, ad esempio, alle applicazioni che richiamano l'API Microsoft Graph, l'API di Office e l'API di Azure, che si trovano tutte all'esterno del dominio da cui è gestita l'applicazione. Una tendenza crescente per le applicazioni JavaScript è quella di non avere alcun back-end a tutti, affidandosi 100% su API web di terze parti per implementare la loro funzione di business.

Attualmente, il metodo preferito per proteggere le chiamate a un'API Web consiste nell'utilizzare l'approccio del token di connessione OAuth2, in cui ogni chiamata è accompagnata da un token di accesso OAuth2. L'API Web esamina il token di accesso in ingresso e, se trova in esso gli ambiti necessari, concede l'accesso all'operazione richiesta. Il flusso implicito fornisce un pratico meccanismo per le applicazioni JavaScript per ottenere i token di accesso per un'API web, offrendo numerosi vantaggi per quanto riguarda i cookie:

* I token possono essere ottenuti in modo affidabile senza bisogno di chiamate multiorigine. La registrazione obbligatoria dell'URI di reindirizzamento a cui i token vengono restituiti garantisce che non vengano spostati.
* Le applicazioni JavaScript possono ottenere tutti i token di accesso necessari, per tutte le API Web di destinazione, senza restrizioni sui domini
* Le funzionalità HTML5, come l'archiviazione locale o di sessione, garantiscono il controllo completo sulla memorizzazione nella cache e sulla gestione del ciclo di vita dei token, mentre la gestione dei cookie è opaca per l'app.
* I token di accesso non sono soggetti ad attacchi CSRF (Cross-site request forgery)

Il flusso di concessione implicita non rilascia token di aggiornamento, soprattutto per motivi di sicurezza. Un token di aggiornamento non è così ristretta come i token di accesso, concedendo molto più potere quindi infliggendo molti più danni nel caso in cui è trapelato. Nel flusso implicito, i token vengono recapitati nell'URL, pertanto il rischio di intercettazione è maggiore rispetto alla concessione del codice di autorizzazione.

Tuttavia, un'applicazione JavaScript ha a disposizione un altro meccanismo per rinnovare i token di accesso senza chiedere ripetutamente all'utente le credenziali. L'applicazione può usare un iframe nascosto per eseguire le nuove richieste di token nell'endpoint di autorizzazione di Azure AD: finché il browser ha una sessione attiva (ovvero, finché ha un cookie di sessione) nel dominio di Azure AD, la richiesta di autenticazione può essere eseguita correttamente senza alcuna interazione dell'utente.

Questo modello concede all'applicazione JavaScript la possibilità di rinnovare in modo indipendente i token di accesso e anche di acquisirne di nuovi per una nuova API, purché l'utente abbia già dato il consenso. In questo modo si evita di acquisire, gestire e proteggere un elemento di valore elevato, ad esempio un token di aggiornamento. L'elemento che rende possibile il rinnovo non interattivo, ovvero il cookie della sessione di Azure AD, viene gestito all'esterno dell'applicazione. Un altro vantaggio di questo approccio è che un utente può disconnettersi da Azure AD usando una delle applicazioni connesse ad Azure AD in esecuzione in una scheda del browser. Ciò comporta l'eliminazione del cookie della sessione di Azure AD e l'applicazione JavaScript perderà automaticamente la possibilità di rinnovare i token per l'utente disconnesso.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>La concessione implicita è adatta all'app?

La concessione implicita presenta maggiori rischi rispetto ad altre concessioni e le aree è necessario prestare attenzione sono ben documentate, ad esempio, nel paragrafo relativo all'[uso improprio dei token di accesso per rappresentare il proprietario della risorsa nel flusso implicito][OAuth2-Spec-Implicit-Misuse] e nel documento relativo al [modello di minaccia OAuth 2.0 e alle considerazioni sulla sicurezza][OAuth2-Threat-Model-And-Security-Implications]. Tuttavia il profilo di rischio maggiore è per lo più generato dal fatto che lo scopo è abilitare le applicazioni che eseguono codice attivo, fornito da una risorsa remota a un browser. Se si pianifica un'architettura SPA, non si hanno componenti back-end o si intende richiamare un'API Web tramite JavaScript, è consigliabile usare il flusso implicito per l'acquisizione di token.

Se l'applicazione è un client nativo, il flusso implicito non è adatto. L'assenza del cookie della sessione di Azure AD nel contesto di un client nativo non consente all'applicazione di mantenere una sessione di lunga durata. Di conseguenza, 'applicazione chiederà ripetutamente l'interazione dell'utente per ottenere i token di accesso per le nuove risorse.

Se si sta sviluppando un'applicazione Web che include un back-end e utilizza un'API dal codice back-end, nemmeno in questo caso il flusso implicito è la soluzione ideale. Altre concessioni offrono molte più possibilità. Ad esempio, la concessione delle credenziali client OAuth2 consente di ottenere token che rispecchiano le autorizzazioni assegnate all'applicazione stessa invece che alle deleghe utente. Ciò significa che il client è in grado di mantenere l'accesso a livello di codice alle risorse anche quando un utente non è attivamente impegnato in una sessione e così via. Tali concessioni offrono poi anche maggiori garanzie di sicurezza. Ad esempio, i token di accesso non transitano mai attraverso il browser dell'utente, non rischiano di essere salvati nella cronologia del browser e così via. L'applicazione client può anche eseguire l'autenticazione avanzata quando si richiede un token.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra come appare l'intero flusso di accesso implicito e le sezioni seguenti descrivono ogni passaggio in modo più dettagliato.

![Diagramma che mostra il flusso di accesso implicito](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso

Per accedere inizialmente all'utente nell'app, è possibile inviare `id_token` una richiesta di autenticazione OpenID Connect e ottenerne un endpoint della piattaforma di identità Microsoft.To initially sign the user into your app, you can send an [OpenID Connect](v2-protocols-oidc.md) authentication request and get an from the Microsoft identity platform endpoint.

> [!IMPORTANT]
> Per richiedere correttamente un token ID e/o un token di accesso, la registrazione dell'app nella pagina [Portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) deve avere il flusso di concessione implicita corrispondente abilitato, selezionando Token **ID** e.o token di **accesso** nella sezione **Concessione implicita.** Se non è abilitato, `unsupported_response` verrà restituito un errore: il valore fornito per il parametro di **input 'response_type' non è consentito per questo client. Il valore previsto è 'code'**

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
> Per testare l'accesso utilizzando <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a> il flusso implicito, fare clic su . Dopo l'accesso, il browser `https://localhost/myapp/` deve `id_token` essere reindirizzato a con un nella barra degli indirizzi.
>

| Parametro |  | Descrizione |
| --- | --- | --- |
| `tenant` | obbligatorio |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obbligatorio | ID applicazione (client) assegnato alla pagina Portale [di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) assegnata all'app. |
| `response_type` | obbligatorio |Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere `token` come response_type. Usando qui il `token` , l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint. Se si `token` utilizza l'response_type, il `scope` parametro deve contenere un ambito che indica per quale risorsa emettere il token (ad esempio, user.read su Microsoft Graph).  |
| `redirect_uri` | Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| `scope` | obbligatorio |Elenco di [ambiti](v2-permissions-and-consent.md)separati da spazi. Per OpenID Connect (id_tokens), deve `openid`includere l'ambito , che si traduce nell'autorizzazione "Accedi" nell'interfaccia utente di consenso. Facoltativamente, è anche possibile `email` `profile` includere gli ambiti e per ottenere l'accesso a dati utente aggiuntivi. È inoltre possibile includere altri ambiti in questa richiesta per richiedere il consenso a varie risorse, se viene richiesto un token di accesso. |
| `response_mode` | facoltativo |Specifica il metodo da usare per restituire il token risultante all'app. L'impostazione predefinita esegue una query solo per un token di accesso, ma frammenta se la richiesta include un id_token. |
| `state` | Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Un valore univoco generato casualmente viene in genere utilizzato per [impedire attacchi di contraffazione](https://tools.ietf.org/html/rfc6749#section-10.12)di richieste tra siti. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `nonce` | obbligatorio |Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. Obbligatorio solo quando viene richiesto un id_token. |
| `prompt` | facoltativo |Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi al momento sono "login", "none", "select_account" e "consent". `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On. `prompt=none`è l'opposto - farà in modo che l'utente non viene presentato con alcun prompt interattivo di sorta. Se la richiesta non può essere completata in modo invisibile all'utente tramite Single Sign-On, l'endpoint della piattaforma di identità Microsoft restituirà un errore. `prompt=select_account` invia l'utente a un selettore di account in cui verranno visualizzati tutti gli account memorizzati nella sessione. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, che chiede all'utente di concedere le autorizzazioni all'app. |
| `login_hint`  |facoltativo |Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`.|
| `domain_hint` | facoltativo |Se incluso, salterà il processo di individuazione basato su posta elettronica che l'utente passa attraverso la pagina di accesso, portando a un'esperienza utente leggermente più semplificata. Viene comunemente usato per le app Line of Business che operano in un singolo tenant, in cui verranno fornite un nome di dominio all'interno di un determinato tenant.  In questo modo l'utente verrà inoltrato al provider di federazione per tale tenant.  Si noti che questo impedirà agli ospiti di accedere a questa applicazione.  |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint della piattaforma di identità Microsoft garantirà inoltre che `scope` l'utente abbia acconsentito alle autorizzazioni indicate nel parametro di query. Se l'utente non ha acconsentito a **nessuna** di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Per altre informazioni, vedere [autorizzazioni, consenso e app multi-tenant](v2-permissions-and-consent.md).

Una volta che l'utente esegue l'autenticazione e concede il consenso, `redirect_uri`l'endpoint della `response_mode` piattaforma di identità Microsoft restituirà una risposta all'app in corrispondenza del metodo indicato, utilizzando il metodo specificato nel parametro.

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=fragment` e `response_type=id_token+token` è simile a quanto riportato di seguito. Sono state aggiunte interruzioni di riga per migliorare la leggibilità:

```HTTP
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametro | Descrizione |
| --- | --- |
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app. Il token di accesso non deve essere decodificato o controllato in altro modo, deve essere considerato come una stringa opaca. |
| `token_type` |Incluso se `response_type` include `token`. È sempre `Bearer`. |
| `expires_in`|Incluso se `response_type` include `token`. Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` |Incluso se `response_type` include `token`. Indica gli ambiti per cui access_token è valido. Non può includere tutti gli ambiti richiesti, se non sono applicabili all'utente (nel caso in cui vengono richiesti ambiti solo Azure AD quando viene usato un account personale per l'accesso). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali limiti di autorizzazione o sicurezza. Per ulteriori informazioni su [`id_token reference`](id-tokens.md)id_tokens, vedere la . <br> **Nota:** viene fornito solo è stato richiesto l'ambito `openid`. |
| `state` |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

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

## <a name="getting-access-tokens-silently-in-the-background"></a>Ottenere i token di accesso in background in background

Dopo aver eseguito l'accesso dell'utente all'app a pagina singola, è possibile ottenere automaticamente i token di accesso per chiamare le API Web protette dalla piattaforma di identità Microsoft, ad esempio [Microsoft Graph](https://developer.microsoft.com/graph). Anche se si è già ricevuto un token usando il `token` response_type, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

Nel normale flusso OpenID Connect/OAuth, è possibile eseguire questa operazione `/token` effettuando una richiesta all'endpoint della piattaforma di identità Microsoft.In the normal OpenID Connect/OAuth flow, you would do this by making a request to the Microsoft identity platform endpoint. Tuttavia, l'endpoint della piattaforma di identità Microsoft non supporta le richieste CORS, pertanto l'esecuzione di chiamate AJAX per ottenere e aggiornare i token non è in questione. È possibile usare invece il flusso implicito in un iframe nascosto per ottenere nuovi token per altre API Web:

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

Con il parametro `prompt=none` , questa richiesta avrà immediatamente esito positivo o negativo e farà tornare all'applicazione. Un risposta con esito positivo verrà inviata all'app all'indirizzo `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`.

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
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app, in questo caso per Microsoft Graph. Il token di accesso non deve essere decodificato o controllato in altro modo, deve essere considerato come una stringa opaca. |
| `token_type` | È sempre `Bearer`. |
| `expires_in` | Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` | Indica gli ambiti per cui access_token è valido. Non può includere tutti gli ambiti richiesti, se non sono applicabili all'utente (nel caso in cui vengono richiesti ambiti solo Azure AD quando viene usato un account personale per l'accesso). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. Incluso se `response_type` include `id_token`. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali limiti di autorizzazione o sicurezza. Per ulteriori informazioni sulle [ `id_token` ](id-tokens.md)id_tokens, vedere le informazioni di riferimento . <br> **Nota:** viene fornito solo è stato richiesto l'ambito `openid`. |
| `state` |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

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

La concessione implicita non fornisce token di aggiornamento. I token `id_token` e `access_token` scadranno dopo un breve periodo, quindi l'app deve essere preparata per aggiornare regolarmente questi token. Per aggiornare entrambi i tipi di token, è possibile `prompt=none` eseguire la stessa richiesta iframe nascosta dall'alto utilizzando il parametro per controllare il comportamento della piattaforma di identità. Se si desidera ricevere `id_token`un nuovo `id_token` , `response_type` assicurarsi di utilizzare in e `scope=openid`, nonché un `nonce` parametro.

## <a name="send-a-sign-out-request"></a>Invio di una richiesta di disconnessione

OpenID Connect `end_session_endpoint` consente all'app di inviare una richiesta all'endpoint della piattaforma di identità Microsoft per terminare la sessione di un utente e cancellare i cookie impostati dall'endpoint della piattaforma di identità Microsoft.The OpenID Connect allows your app to send a request to the Microsoft identity platform endpoint to end a user's session and clear cookies set by the Microsoft identity platform endpoint. Per disconnettere completamente un utente da un'applicazione Web, l'app deve terminare la sessione in cui è presente l'utente (in genere cancellando la cache di un token o eliminando i cookie) e quindi reindirizzare il browser all'indirizzo seguente:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametro |  | Descrizione |
| --- | --- | --- |
| `tenant` |obbligatorio |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Consigliato | URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Questo valore deve corrispondere a uno degli URI di reindirizzamento registrati per l'applicazione. Se non è incluso, all'utente verrà visualizzato un messaggio generico dall'endpoint della piattaforma di identità Microsoft.If not included, the user will be shown a generic message by the Microsoft identity platform endpoint. |

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a codificare, passare agli [esempi di MSAL JS](sample-v2-code.md).
