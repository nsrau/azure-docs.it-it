---
title: Flusso di concessione implicita OAuth 2,0-piattaforma di identità Microsoft | Azure
description: Proteggere le app a singola pagina usando Microsoft Identity Platform implicite Flow.
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
ms.openlocfilehash: 4f35162be513873637f9a87260410d891fdace10
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300469"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Piattaforma di identità Microsoft e flusso di concessione implicita

Con l'endpoint della piattaforma di identità Microsoft, è possibile accedere agli utenti nelle app a singola pagina con account personali e aziendali o dell'Istituto di istruzione di Microsoft. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche interessanti nell'ambito dell'autenticazione:

* Le caratteristiche di sicurezza di queste app sono considerevolmente diverse da quelle delle tradizionali applicazioni Web basate su server.
* Molti server di autorizzazione e provider di identità non supportano le richieste CORS.
* I reindirizzamenti dei browser a pagina intera dall'app diventano fastidiosi per l'esperienza utente.

Per queste applicazioni (AngularJS, Brac. js, React. js e così via), Microsoft Identity Platform supporta il flusso di concessione implicita OAuth 2,0. Il flusso implicito è descritto nella [specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Il suo vantaggio principale è che consente all'app di ottenere i token dalla piattaforma di identità Microsoft senza eseguire uno scambio di credenziali del server back-end. In questo modo l'app può far accedere l'utente, gestire la sessione e ottenere i token per altre API Web, il tutto nel codice JavaScript client. Esistono alcune importanti osservazioni sulla sicurezza da considerare quando si usa il flusso implicito, in particolare per la [rappresentazione del client](https://tools.ietf.org/html/rfc6749#section-10.3) e dell'[utente](https://tools.ietf.org/html/rfc6749#section-10.3).

Questo articolo descrive come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile usare invece le librerie di autenticazione Microsoft (MSAL) supportate per [acquisire i token e chiamare le API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Esaminare anche le [app di esempio che usano MSAL](sample-v2-code.md).

Se, tuttavia, si preferisce non usare una libreria nell'app a pagina singola e inviare manualmente i messaggi dei protocolli, seguire questa procedura generale.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory (Azure AD) sono supportati dall'endpoint della piattaforma di identità Microsoft. Per determinare se è necessario usare l'endpoint della piattaforma Microsoft Identity, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md).

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Scenari adatti alla concessione implicita OAuth2

La specifica OAuth2 dichiara che la concessione implicita è stata definita per abilitare le applicazioni agente utente, ovvero le applicazioni JavaScript in esecuzione in un browser. La caratteristica distintiva di tali applicazioni è che il codice JavaScript viene usato per accedere alle risorse del server, in genere un'API Web, e per aggiornare di conseguenza l'esperienza utente delle applicazioni. Ad esempio in applicazioni come Gmail o Outlook Web Access, quando si seleziona un messaggio dalla Posta in arrivo, solo il pannello per la visualizzazione dei messaggi viene modificato per poter visualizzare la nuova selezione, mentre il resto della pagina rimane invariato. Avviene invece il contrario nelle app Web basate sul reindirizzamento tradizionale, in cui ogni interazione dell'utente restituisce un postback di pagina completo e un rendering di pagina completo della nuova risposta del server.

Le applicazioni che portano l'approccio basato su JavaScript all'estremo sono chiamate applicazioni a pagina singola o SPA. L'idea è che queste applicazioni gestiscano solo una pagina HTML iniziale e il codice JavaScript associato, mentre tutte le interazioni successive vengono gestite dalle chiamate all'API Web eseguite tramite JavaScript. Gli approcci ibridi, tuttavia, in cui l'applicazione è basata principalmente sul postback, ma esegue chiamate JS occasionali, non sono comuni e il discorso sull'utilizzo del flusso implicito riguarda anche tali approcci.

Le applicazioni basate sul reindirizzamento in genere proteggono le richieste tramite cookie, tuttavia questo approccio non funziona anche per le applicazioni JavaScript. I cookie funzionano solo nel dominio per cui sono stati generati, mentre le chiamate JavaScript potrebbero essere dirette ad altri domini. Di fatto ciò accade molto spesso. Si pensi, ad esempio, alle applicazioni che richiamano l'API Microsoft Graph, l'API di Office e l'API di Azure, che si trovano tutte all'esterno del dominio da cui è gestita l'applicazione. Una tendenza sempre più diffusa per le applicazioni JavaScript è quella di non avere back-end e di basarsi al 100% su API Web di terze parti per implementare la funzione aziendale.

Attualmente, il metodo preferito per proteggere le chiamate a un'API Web consiste nell'usare l'approccio basato sul token di connessione OAuth2, in cui ogni chiamata è accompagnata da un token di accesso OAuth2. L'API Web esamina il token di accesso in ingresso e, se vi trova gli ambiti necessari, concede l'accesso all'operazione richiesta. Il flusso implicito fornisce alle applicazioni JavaScript un pratico meccanismo per ottenere i token di accesso per un'API Web, con numerosi vantaggi rispetto ai cookie:

* I token possono essere ottenuti in modo affidabile senza bisogno di chiamate multiorigine. La registrazione obbligatoria dell'URI di reindirizzamento a cui i token vengono restituiti garantisce che non vengano spostati.
* Le applicazioni JavaScript possono ottenere tutti i token di accesso necessari per tutte le API Web di destinazione, senza alcuna restrizione sui domini.
* Le funzionalità HTML5, come l'archiviazione locale o di sessione, garantiscono il controllo completo sulla memorizzazione nella cache e sulla gestione del ciclo di vita dei token, mentre la gestione dei cookie è opaca per l'app.
* I token di accesso non sono soggetti agli attacchi di tipo richiesta intersito falsa.

Il flusso di concessione implicita non rilascia token di aggiornamento, soprattutto per motivi di sicurezza. Un token di aggiornamento non ha un ambito limitato come token di accesso, garantendo molto più potenza, quindi infliggendo molto più danni in caso di perdita. Nel flusso implicito i token vengono recapitati nell'URL, quindi il rischio di intercettazione è superiore rispetto alla concessione del codice di autorizzazione.

Tuttavia, un'applicazione JavaScript ha a disposizione un altro meccanismo per rinnovare i token di accesso senza chiedere ripetutamente all'utente le credenziali. L'applicazione può usare un iframe nascosto per eseguire le nuove richieste di token nell'endpoint di autorizzazione di Azure AD: finché il browser ha una sessione attiva (ovvero, finché ha un cookie di sessione) nel dominio di Azure AD, la richiesta di autenticazione può essere eseguita correttamente senza alcuna interazione dell'utente.

Questo modello concede all'applicazione JavaScript la possibilità di rinnovare in modo indipendente i token di accesso e anche di acquisirne di nuovi per una nuova API, purché l'utente abbia già dato il consenso. In questo modo si evita di acquisire, gestire e proteggere un elemento di valore elevato, ad esempio un token di aggiornamento. L'elemento che rende possibile il rinnovo non interattivo, ovvero il cookie della sessione di Azure AD, viene gestito all'esterno dell'applicazione. Un altro vantaggio di questo approccio è che un utente può disconnettersi da Azure AD usando una delle applicazioni connesse ad Azure AD in esecuzione in una scheda del browser. Ciò comporta l'eliminazione del cookie della sessione di Azure AD e l'applicazione JavaScript perderà automaticamente la possibilità di rinnovare i token per l'utente disconnesso.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>La concessione implicita è adatta all'app?

La concessione implicita presenta maggiori rischi rispetto ad altre concessioni e le aree a cui è necessario prestare attenzione sono ben documentate (ad esempio, [uso improprio del token di accesso per rappresentare il proprietario delle risorse nel flusso implicito] [OAuth2-spec-Implicit-sviamento] e [modello di minaccia OAuth 2,0 e considerazioni sulla sicurezza] [OAuth2-Threat-Model-and-Security-implications]). Tuttavia il profilo di rischio maggiore è per lo più generato dal fatto che lo scopo è abilitare le applicazioni che eseguono codice attivo, fornito da una risorsa remota a un browser. Se si pianifica un'architettura di applicazione a singola pagina, non si hanno componenti back-end o si intende richiamare un'API Web tramite JavaScript, è consigliabile usare il flusso implicito per l'acquisizione dei token.

Se l'applicazione è un client nativo, il flusso implicito non è la soluzione ideale. L'assenza del cookie della sessione di Azure AD nel contesto di un client nativo non consente all'applicazione di mantenere una sessione di lunga durata. Di conseguenza, 'applicazione chiederà ripetutamente l'interazione dell'utente per ottenere i token di accesso per le nuove risorse.

Se si sta sviluppando un'applicazione Web che include un back-end e utilizza un'API dal codice back-end, nemmeno in questo caso il flusso implicito è la soluzione ideale. Altre concessioni offrono molte più possibilità. Ad esempio, la concessione delle credenziali client OAuth2 consente di ottenere token che rispecchiano le autorizzazioni assegnate all'applicazione stessa invece che alle deleghe utente. Ciò significa che il client è in grado di mantenere l'accesso a livello di codice alle risorse anche quando un utente non è attivamente impegnato in una sessione e così via. Tali concessioni offrono poi anche maggiori garanzie di sicurezza. Ad esempio, i token di accesso non passano mai dal browser dell'utente e non rischiano di essere salvati nella cronologia del browser e così via. L'applicazione client può anche eseguire l'autenticazione avanzata quando si richiede un token.

## <a name="protocol-diagram"></a>Diagramma di protocollo

Il diagramma seguente mostra come appare l'intero flusso di accesso implicito e le sezioni seguenti descrivono ogni passaggio in modo più dettagliato.

![Diagramma che mostra il flusso di accesso implicito](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso

Per eseguire inizialmente l'accesso dell'utente all'app, è possibile inviare una richiesta di autenticazione [OpenID Connect](v2-protocols-oidc.md) e ottenere un `id_token` dall'endpoint della piattaforma Microsoft Identity.

> [!IMPORTANT]
> Per richiedere un token ID e/o un token di accesso, per la registrazione dell'app nella pagina [portale di Azure-registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) deve essere abilitato il flusso di concessione implicito corrispondente, selezionando **token ID** e **token di accesso** nella sezione **concessione implicita** . Se non è abilitata, verrà restituito un errore `unsupported_response`: **il valore specificato per il parametro di input ' response_type ' non è consentito per questo client. Il valore previsto è' code '**

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
> Per testare l'accesso tramite il flusso implicito, fare clic su <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Dopo l'accesso, il browser deve essere reindirizzato a `https://localhost/myapp/` con un `id_token` nella barra degli indirizzi.
>

| Parametro |  | Descrizione |
| --- | --- | --- |
| `tenant` | required |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | required | ID dell'applicazione (client) che la pagina [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) assegnata all'app. |
| `response_type` | required |Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere `token` come response_type. Usando qui il `token` , l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint. Se si usa il `token` response_type, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token (ad esempio, User. Read in Microsoft Graph).  |
| `redirect_uri` | Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| `scope` | required |Elenco di ambiti separati da [spazi](v2-permissions-and-consent.md). Per OpenID Connect (id_tokens), deve includere l'ambito `openid`, che viene convertito nell'autorizzazione "Sign Your in" nell'interfaccia utente del consenso. Facoltativamente, è anche possibile includere gli ambiti `email` e `profile` per ottenere l'accesso a dati utente aggiuntivi. È anche possibile includere in questa richiesta altri ambiti per richiedere il consenso a diverse risorse, se è richiesto un token di accesso. |
| `response_mode` | facoltativo |Specifica il metodo da usare per restituire il token risultante all'app. Per impostazione predefinita viene eseguita una query solo per un token di accesso, ma Fragment se la richiesta include un id_token. |
| `state` | Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](https://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `nonce` | required |Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. Obbligatorio solo quando viene richiesto un id_token. |
| `prompt` | facoltativo |Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi al momento sono "login", "none", "select_account" e "consent". `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On. `prompt=none` è l'opposto: garantisce che all'utente non venga visualizzata alcuna richiesta interattiva. Se la richiesta non può essere completata in modo invisibile all'utente tramite Single Sign-on, l'endpoint della piattaforma Microsoft Identity restituisce un errore. `prompt=select_account` invia l'utente a un selettore di account in cui verranno visualizzati tutti gli account memorizzati nella sessione. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, che chiede all'utente di concedere le autorizzazioni all'app. |
| `login_hint`  |facoltativo |Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`.|
| `domain_hint` | facoltativo |Se incluso, ignorerà il processo di individuazione basato sulla posta elettronica che l'utente passa alla pagina di accesso, ottenendo un'esperienza utente leggermente più semplificata. Questa operazione viene in genere usata per le app line-of-business che operano in un singolo tenant, in cui forniranno un nome di dominio all'interno di un determinato tenant.  In questo modo l'utente verrà trasmesso al provider federativo per il tenant.  Si noti che questa operazione impedirà ai guest di accedere all'applicazione.  |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. L'endpoint della piattaforma Microsoft Identity garantisce inoltre che l'utente abbia acconsentito alle autorizzazioni indicate nel parametro di query `scope`. Se l'utente non ha acconsentito a **nessuna** di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Per altre informazioni, vedere [autorizzazioni, consenso e app multi-tenant](v2-permissions-and-consent.md).

Una volta che l'utente esegue l'autenticazione e concede il consenso, l'endpoint della piattaforma di identità Microsoft restituirà una risposta all'app in corrispondenza del `redirect_uri`indicato, usando il metodo specificato nel parametro `response_mode`.

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=fragment` e `response_type=id_token+token` è simile a quanto riportato di seguito. Sono state aggiunte interruzioni di riga per migliorare la leggibilità:

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametro | Descrizione |
| --- | --- |
| `access_token` |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app. Il token di accesso non deve essere decodificato o controllato in altro modo, deve essere trattato come una stringa opaca. |
| `token_type` |Incluso se `response_type` include `token`. È sempre `Bearer`. |
| `expires_in`|Incluso se `response_type` include `token`. Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| `scope` |Incluso se `response_type` include `token`. Indica gli ambiti per cui access_token è valido. Potrebbe non includere tutti gli ambiti richiesti, se non sono applicabili all'utente (nel caso di ambiti solo Azure AD richiesti quando si usa un account personale per l'accesso). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per i limiti di autorizzazione o di sicurezza. Per altre informazioni sugli oggetti id_token, vedere [`id_token reference`](id-tokens.md). <br> **Nota:** viene fornito solo è stato richiesto l'ambito `openid`. |
| `state` |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| --- | --- |
| `error` |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Ottenere i token di accesso in modo invisibile all'utente in background

Ora che l'utente ha eseguito l'accesso all'app a singola pagina, è possibile ottenere i token di accesso in modo invisibile all'utente per chiamare le API Web protette dalla piattaforma di identità Microsoft, ad esempio la [Microsoft Graph](https://developer.microsoft.com/graph). Anche se si è già ricevuto un token usando il `token` response_type, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

Nel normale flusso OpenID Connect/OAuth, è possibile eseguire questa operazione effettuando una richiesta alla piattaforma Microsoft Identity `/token` endpoint. Tuttavia, l'endpoint della piattaforma di identità Microsoft non supporta le richieste CORS, quindi l'esecuzione di chiamate AJAX per ottenere e aggiornare i token è fuori dalla domanda. È possibile usare invece il flusso implicito in un iframe nascosto per ottenere nuovi token per altre API Web: 

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

```
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
| `scope` | Indica gli ambiti per cui access_token è valido. Potrebbe non includere tutti gli ambiti richiesti, se non sono applicabili all'utente (nel caso di ambiti solo Azure AD richiesti quando si usa un account personale per l'accesso). |
| `id_token` | Un token JWT (Token Web JSON) non firmato. Incluso se `response_type` include `id_token`. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per i limiti di autorizzazione o di sicurezza. Per altre informazioni sugli oggetti id_token, vedere le [informazioni di riferimento su `id_token`](id-tokens.md). <br> **Nota:** viene fornito solo è stato richiesto l'ambito `openid`. |
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

La concessione implicita non fornisce token di aggiornamento. I token `id_token` e `access_token` scadranno dopo un breve periodo, quindi l'app deve essere preparata per aggiornare regolarmente questi token. Per aggiornare entrambi i tipi di token, è possibile eseguire la stessa richiesta iframe nascosta precedente usando il parametro `prompt=none` per controllare il comportamento della piattaforma di identità. Se si vuole ricevere un nuovo `id_token`, assicurarsi di usare `id_token` nel `response_type` e `scope=openid`, oltre a un parametro di `nonce`.

## <a name="send-a-sign-out-request"></a>Invio di una richiesta di disconnessione

Il `end_session_endpoint` OpenID Connect consente all'app di inviare una richiesta all'endpoint della piattaforma di identità Microsoft per terminare la sessione di un utente e cancellare i cookie impostati dall'endpoint della piattaforma di identità Microsoft. Per disconnettere completamente un utente da un'applicazione Web, l'app deve terminare la sessione in cui è presente l'utente (in genere cancellando la cache di un token o eliminando i cookie) e quindi reindirizzare il browser all'indirizzo seguente:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametro |  | Descrizione |
| --- | --- | --- |
| `tenant` |required |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Consigliato | URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Questo valore deve corrispondere a uno degli URI di reindirizzamento registrati per l'applicazione. Se non è incluso, l'utente verrà visualizzato un messaggio generico dall'endpoint della piattaforma di identità Microsoft. |

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a codificare, passare agli [esempi di MSAL JS](sample-v2-code.md).
