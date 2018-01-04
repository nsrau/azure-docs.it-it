---
title: Proteggere le applicazioni a singola pagina usando il flusso implicito 2.0 di Azure AD | Documentazione Microsoft
description: Compilazione di applicazioni Web con l'implementazione della versione 2.0 del flusso implicito per le app a singola pagina definita in Azure AD.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 7ecc9de6a9eb910ac4c31290710530555441890d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>Protocolli della versione 2.0: applicazioni a singola pagina che usano il flusso implicito
Con l'endpoint v2.0 è possibile far accedere gli utenti alle app a singola pagina con account Microsoft sia personali che aziendali o dell'istituto di istruzione.  Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche interessanti nell'ambito dell'autenticazione:

* Le caratteristiche di sicurezza di queste app sono considerevolmente diverse da quelle delle tradizionali applicazioni Web basate su server.
* Molti server di autorizzazione e provider di identità non supportano le richieste CORS.
* I reindirizzamenti dei browser a pagina intera dall'app diventano fastidiosi per l'esperienza utente.

Per queste applicazioni (ad esempio, AngularJS, Ember.js, React.js e così via) Azure AD supporta il flusso di concessione implicita di OAuth 2.0.  Il flusso implicito è descritto nella [specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2).  Il principale vantaggio è che consente all'app di ottenere i token da Azure AD senza eseguire uno scambio di credenziali del server back-end.  In questo modo l'app può far accedere l'utente, gestire la sessione e ottenere i token per altre API Web, il tutto nel codice JavaScript client.  Esistono alcune importanti osservazioni sulla sicurezza da considerare quando si usa il flusso implicito, in particolare per la [rappresentazione del client](http://tools.ietf.org/html/rfc6749#section-10.3) e dell'[utente](http://tools.ietf.org/html/rfc6749#section-10.3).

Per usare il flusso implicito e Azure AD per aggiungere l'autenticazione all'app JavaScript, è consigliabile usare la libreria JavaScript open source, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  [Qui](active-directory-appmodel-v2-overview.md#getting-started) sono disponibili alcune esercitazioni introduttive su AngularJS.  

Se, tuttavia, si preferisce non usare una libreria nell'app a pagina singola e inviare manualmente i messaggi dei protocolli, seguire questi passaggi generali.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint 2.0.  Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="protocol-diagram"></a>Diagramma di protocollo
L'intero flusso di accesso implicito è simile al seguente: i singoli passaggi sono descritti in dettaglio di seguito.

![Corsie di OpenID Connect](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso
Per l'accesso iniziale dell'utente all'app, è possibile inviare una richiesta di autorizzazione [OpenID Connect](active-directory-v2-protocols-oidc.md) e ottenere un `id_token` dall'endpoint 2.0:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Fare clic sul collegamento seguente per eseguire questa richiesta. Dopo l'accesso, il browser deve essere reindirizzato a `https://localhost/myapp/` con un `id_token` nella barra degli indirizzi.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parametro |  | DESCRIZIONE |
| --- | --- | --- |
| tenant |Obbligatoria |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione.  I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant.  Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client_id |Obbligatoria |ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) ha assegnato all'app. |
| response_type |Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect.  Può anche includere `token` come response_type. Usando qui il `token` , l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint.  Se si usa il `token` come response_type, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| redirect_uri |Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app.  Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatoria |Elenco di ambiti separati da spazi.  Per OpenID Connect, deve includere l'ambito `openid`che esegue la conversione all'autorizzazione per l'accesso nell'interfaccia utente di consenso.  Facoltativamente, è anche possibile includere l'[ambito](active-directory-v2-scopes.md) `email` o `profile` per ottenere l'accesso a dati aggiuntivi dell'utente.  È anche possibile includere altri ambiti in questa richiesta per richiedere il consenso per varie risorse. |
| response_mode |Consigliato |Specifica il metodo da usare per restituire il token risultante all'app.  Deve essere `fragment` per il flusso implicito. |
| state |Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto.  Per [evitare gli attacchi di richiesta intersito falsa](http://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente.  Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| nonce |Obbligatoria |Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token.  Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| prompt |Facoltativo |Indica il tipo di interazione obbligatoria dell'utente.  Gli unici valori validi al momento sono "login", "none" e "consent".  `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On.  `prompt=none` è l'opposto: garantisce che all'utente non venga presentata alcuna richiesta interattiva.  Se la richiesta non può essere completata automaticamente tramite Single-Sign-On, l'endpoint 2.0 restituirà un errore.  `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, che chiede all'utente di concedere le autorizzazioni all'app. |
| login_hint |Facoltativo |Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente.  Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`. |
| domain_hint |Facoltativo |Può essere uno di `consumers` o `organizations`.  Se incluso, non verrà eseguito il processo di individuazione basata sulla posta elettronica a cui viene sottoposto l'utente nella pagina di accesso della versione 2.0. Questo comporta un'esperienza utente leggermente semplificata.  Le app usano spesso questo parametro durante la riautenticazione, estraendo l'attestazione `tid` dall'id_token.  Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`.  In caso contrario, usare `domain_hint=organizations`. |

A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione.  L'endpoint 2.0 assicura anche che l'utente abbia fornito il consenso per le autorizzazioni indicate nel parametro di query `scope` .  Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie.  Questo articolo contiene informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant](active-directory-v2-scopes.md).

Dopo che l'utente viene autenticato e fornisce il consenso, l'endpoint 2.0 restituisce una risposta all'app nell'URI `redirect_uri`, usando il metodo specificato nel parametro `response_mode`.

#### <a name="successful-response"></a>Risposta con esito positivo
Una risposta con esito positivo che usa `response_mode=fragment` e `response_type=id_token+token` è simile a quanto riportato di seguito. Sono state aggiunte interruzioni di riga per migliorare la leggibilità:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametro | DESCRIZIONE |
| --- | --- |
| access_token |Incluso se `response_type` include `token`. Token di accesso richiesto dall'app, in questo caso per Microsoft Graph.  Il token di accesso non deve essere decodificato o controllato in altro modo ma può essere trattato come una stringa opaca. |
| token_type |Incluso se `response_type` include `token`.  È sempre `Bearer`. |
| expires_in |Incluso se `response_type` include `token`.  Indica il numero di secondi di validità del token, ai fini della memorizzazione nella cache. |
| scope |Incluso se `response_type` include `token`.  Indica gli ambiti per cui access_token è valido. |
| id_token |Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente.  Informazioni dettagliate sui token ID e il relativo contenuto sono incluse nel [riferimento al token dell'endpoint 2.0](active-directory-v2-tokens.md). |
| state |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | DESCRIZIONE |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## <a name="validate-the-idtoken"></a>Convalidare il token ID
La semplice ricezione di un token ID non è sufficiente per autenticare l'utente. È necessario convalidare la firma del token ID e verificare le attestazioni nel token per i requisiti dell'app.  L'endpoint 2.0 usa i [token Web JSON](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

È possibile scegliere di convalidare `id_token` nel codice client, ma una procedura comune consiste nell'inviare `id_token` a un server back-end dove verrà eseguita la convalida.  Dopo aver convalidato la firma dell'id_token, è necessario verificare alcune attestazioni.  Per altre informazioni, ad esempio relative alla [convalida del token](active-directory-v2-tokens.md) e al [rollover della chiave di firma](active-directory-v2-tokens.md#validating-tokens), vedere il [riferimento al token della versione 2.0](active-directory-v2-tokens.md#validating-tokens).  È consigliabile usare una libreria per l'analisi e la convalida dei token. È disponibile almeno una libreria per la maggior parte dei linguaggi e delle piattaforme.
<!--TODO: Improve the information on this-->

È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario.  Alcune convalide comuni includono:

* Garantire che l'utente o l'organizzazione dispongano dell'iscrizione all'app.
* Garantire che l'utente disponga di autorizzazioni/privilegi adeguati.
* Garantire che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori.

Per altre informazioni sulle attestazioni in un token ID, vedere il [riferimento al token dell'endpoint v2.0](active-directory-v2-tokens.md).

Dopo aver convalidato completamente il token ID, è possibile avviare una sessione con l'utente e usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'app.  Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## <a name="get-access-tokens"></a>Ottenere i token di accesso
Dopo avere fatto accedere l'utente all'app a pagina singola, è possibile ottenere i token di accesso per chiamare le API Web protette da Azure AD, ad esempio [Microsoft Graph](https://graph.microsoft.io).  Anche se si è già ricevuto un token usando il `token` response_type, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

Nel normale flusso OpenID Connect/OAuth, per eseguire questa operazione, si effettua una richiesta all'endpoint `/token` 2.0.  L'endpoint 2.0 non supporta però le richieste CORS e quindi non è possibile effettuare una chiamata ad AJAX per ottenere e aggiornare i token.  È possibile usare invece il flusso implicito in un iframe nascosto per ottenere nuovi token per altre API Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [!TIP]
> Provare a copiare e incollare questa richiesta in una scheda del browser. (Non dimenticare di sostituire i valori `domain_hint` e `login_hint` con i valori corretti per l'utente)
> 
> 

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parametro |  | DESCRIZIONE |
| --- | --- | --- |
| tenant |Obbligatoria |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione.  I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant.  Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client_id |Obbligatoria |ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) ha assegnato all'app. |
| response_type |Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect.  Può anche includere altri parametri response_type, ad esempio `code`. |
| redirect_uri |Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app.  Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatoria |Elenco di ambiti separati da spazi.  Per ottenere i token, includere tutti gli [ambiti](active-directory-v2-scopes.md) necessari per la risorsa di interesse. |
| response_mode |Consigliato |Specifica il metodo da usare per restituire il token risultante all'app.  Può essere uno di `query`, `form_post` o `fragment`. |
| state |Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto.  Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente.  Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| nonce |Obbligatoria |Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token.  Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| prompt |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, è necessario usare `prompt=none` per fare in modo che iframe non si blocchi alla pagina di accesso della versione 2.0 e risponda immediatamente. |
| login_hint |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, è necessario includere il nome utente dell'utente in questo hint per distinguere tra le eventuali varie sessioni dell'utente in un determinato momento. È possibile estrarre il nome utente da un accesso precedente usando l'attestazione `preferred_username`. |
| domain_hint |Obbligatoria |Può essere uno di `consumers` o `organizations`.  Per aggiornare e ottenere i token in un iframe nascosto, è necessario includere domain_hint nella richiesta.  È necessario estrarre l'attestazione `tid` dall'id_token di un accesso precedente per determinare il valore da usare.  Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`.  In caso contrario, usare `domain_hint=organizations`. |

Con il parametro `prompt=none` , questa richiesta avrà immediatamente esito positivo o negativo e farà tornare all'applicazione.  Un risposta con esito positivo verrà inviata all'app all'indirizzo `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`.

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
| access_token |Token richiesto dall'app. |
| token_type |È sempre `Bearer`. |
| state |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |
| expires_in |Validità del token di accesso (espressa in secondi). |
| scope |Ambiti per i quali il token di accesso è valido. |

#### <a name="error-response"></a>Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente.  Nel caso di `prompt=none`, un errore previsto sarà:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | DESCRIZIONE |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

Se si riceve questo errore nella richiesta iframe, l'utente deve accedere di nuovo in modo interattivo per recuperare un nuovo token.  È possibile scegliere di gestire questa situazione in qualsiasi modo appropriato per l'applicazione.

## <a name="refreshing-tokens"></a>Aggiornare i token
I token `id_token` e `access_token` scadranno dopo un breve periodo, quindi l'app deve essere preparata per aggiornare regolarmente questi token.  Per aggiornare entrambi i tipi di token, è possibile eseguire la stessa richiesta nell'iframe nascosto precedente usando il parametro `prompt=none` per controllare il comportamento di Azure AD.  Per ricevere un nuovo `id_token`, assicurarsi di usare `response_type=id_token` e `scope=openid`, oltre al parametro `nonce`.

## <a name="send-a-sign-out-request"></a>Invio di una richiesta di disconnessione
`end_session_endpoint` di OpenID Connect consente all'app di inviare una richiesta all'endpoint 2.0 per terminare una sessione utente e cancellare i cookie impostati dall'endpoint 2.0.  Per disconnettere completamente un utente da un'applicazione Web, l'app deve terminare la sessione in cui è presente l'utente (in genere cancellando la cache di un token o eliminando i cookie) e quindi reindirizzare il browser all'indirizzo seguente:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametro |  | DESCRIZIONE |
| --- | --- | --- |
| tenant |Obbligatoria |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione.  I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant.  Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| post_logout_redirect_uri | Consigliato | URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Questo valore deve corrispondere a uno degli URI di reindirizzamento registrati per l'applicazione. In caso contrario, all'utente verrà visualizzato un messaggio generico da parte dell'endpoint 2.0. |
