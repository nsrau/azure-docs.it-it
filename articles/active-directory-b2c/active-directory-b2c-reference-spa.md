---
title: Accesso a singola pagina tramite il flusso implicito
titleSuffix: Azure AD B2C
description: Informazioni su come aggiungere l'accesso a una singola pagina usando il flusso implicito OAuth 2,0 con Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9a7d6a0a4e341158b37de73a74390d87a135d65f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947982"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Accesso a pagina singola con il flusso implicito OAuth 2,0 in Azure Active Directory B2C

Molte applicazioni moderne hanno un front-end di app a singola pagina scritto principalmente in JavaScript. Spesso l'app viene scritta usando un Framework come REACT, angolare o VME. js. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche aggiuntive per l'autenticazione:

- Le caratteristiche di sicurezza di queste app sono diverse dalle tradizionali applicazioni Web basate su server.
- Molti server di autorizzazione e provider di identità non supportano le richieste CORS (Condivisione risorse tra le origini).
- I reindirizzamenti del browser a pagina intera lontani dall'app possono essere invasivi per l'esperienza utente.

Per supportare queste applicazioni, Azure Active Directory B2C (Azure AD B2C) usa il flusso implicito OAuth 2.0. Il flusso di concessione implicita OAuth 2.0 è descritto nella [sezione 4.2 della specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749). Nel flusso implicito l'app riceve i token direttamente dall'endpoint di autorizzazione di Azure Active Directory (Azure AD), senza alcuno scambio tra server. Tutta la logica di autenticazione e la gestione delle sessioni vengono eseguite interamente nel client JavaScript con un reindirizzamento della pagina o una finestra popup.

Azure AD B2C estende il flusso implicito OAuth 2.0 standard e va oltre le semplici operazioni di autorizzazione e autenticazione. Azure AD B2C introduce il [parametro di criteri](active-directory-b2c-reference-policies.md). Con il parametro di criteri è possibile usare OAuth 2.0 per aggiungere criteri all'app, ad esempio i flussi utente di iscrizione, accesso e gestione dei profili. Nelle richieste HTTP di esempio in questo articolo viene usato **{tenant}. onmicrosoft. com** come esempio. Sostituire `{tenant}` con il nome del tenant se ne è stato creato uno ed è stato creato anche un flusso utente.

Il flusso di accesso implicito è simile a quello illustrato nella figura seguente. Ogni passaggio verrà descritto in dettaglio più avanti nell'articolo.

![Diagramma di tipo corsia che mostra il flusso implicito di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Invio di richieste di autenticazione

Quando l'applicazione Web deve autenticare l'utente ed eseguire un flusso utente, può indirizzare l'utente all'endpoint `/authorize`. L'utente esegue un'azione a seconda del flusso utente.

In questa richiesta il client indica le autorizzazioni che deve acquisire dall'utente nel parametro `scope` e il flusso utente per l'esecuzione. Per avere un'idea del funzionamento della richiesta, provare a incollare la richiesta in un browser e a eseguirla. Sostituire `{tenant}` con il nome del tenant di Azure AD B2C. Sostituire `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` con l'ID app dell'applicazione registrata in precedenza nel tenant. Sostituire `{policy}` con il nome di un criterio creato nel tenant, ad esempio `b2c_1_sign_in`.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametro | Obbligatoria | Description |
| --------- | -------- | ----------- |
|inquilino| SÌ | Nome del tenant di Azure AD B2C|
|politica| SÌ| Flusso utente da eseguire. Specificare il nome di un flusso utente creato nel tenant del Azure AD B2C. Ad esempio, `b2c_1_sign_in`, `b2c_1_sign_up` o `b2c_1_edit_profile`. |
| client_id | SÌ | ID applicazione assegnato dall' [portale di Azure](https://portal.azure.com/) all'applicazione. |
| response_type | SÌ | Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere il tipo di risposta `token`. Se si usa `token`, l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint.  Se si usa il tipo di risposta `token`, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| redirect_uri | No | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| response_mode | No | Specifica il metodo da usare per restituire il token risultante all'app.  Per i flussi impliciti, usare `fragment`. |
| scope | SÌ | Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. L’ambito `offline_access` è facoltativo per le applicazioni Web. Indica che l'app necessita di un token di aggiornamento per avere un accesso duraturo alle risorse. |
| state | No | Valore incluso nella richiesta che viene anche restituito nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto si voglia usare. Per evitare attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina in cui si trovava. |
| nonce | SÌ | Valore incluso nella richiesta, generata dall'app, che viene incorporato nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| prompt | No | Tipo di interazione utente obbligatoria. Al momento l'unico valore valido è `login`. Questo parametro impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-on non ha effetto. |

Viene a questo punto richiesto all'utente di completare il flusso di lavoro dei criteri. È possibile che l'utente debba immettere il nome utente e la password, accedere con un'identità di social networking, iscriversi alla directory o a qualsiasi altro numero di passaggi. Le azioni dell'utente dipendono dal modo in cui è definito il flusso utente.

Dopo che l'utente ha completato il flusso utente, Azure AD restituisce una risposta per l'app in corrispondenza del valore usato per `redirect_uri`. Viene usato il metodo specificato nel parametro `response_mode`. La risposta è esattamente la stessa per ogni scenario di azione dell'utente, indipendentemente dal flusso utente eseguito.

### <a name="successful-response"></a>Risposta con esito positivo
Una risposta con esito positivo che usa `response_mode=fragment` e `response_type=id_token+token` è simile a quanto riportato di seguito. Sono state aggiunte interruzioni di riga per migliorare la leggibilità:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parametro | Description |
| --------- | ----------- |
| access_token | Token di accesso richiesto dall'app. |
| token_type | Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| expires_in | Periodo di validità del token di accesso (in secondi). |
| scope | Ambiti per i quali il token è valido. È possibile usare gli ambiti anche per memorizzare i token nella cache per un uso successivo. |
| id_token | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Per informazioni dettagliate sui token ID e sul relativo contenuto, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md). |
| state | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |

### <a name="error-response"></a>Risposta di errore
Anche le risposte di errore possono essere inviate all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Description |
| --------- | ----------- |
| error | Codice utilizzato per classificare i tipi di errori che si verificano. |
| error_description | Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| state | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici.|

## <a name="validate-the-id-token"></a>Convalidare il token ID

La ricezione di un token ID non è sufficiente per autenticare l'utente. Convalidare la firma del token ID e verificare le attestazioni nel token in base ai requisiti dell'app. Azure AD B2C usa i [token Web JSON](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

Sono disponibili molte librerie open source per la convalida dei token JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame le librerie open source disponibili anziché implementare una logica di convalida personalizzata. È possibile usare le informazioni contenute in questo articolo permettono di imparare a usare correttamente tali librerie.

Azure AD B2C include un endpoint dei metadati di OpenID Connect. Un'app può usare l'endpoint per recuperare informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant Azure AD B2C include un documento di metadati JSON per ogni flusso utente. Ad esempio, il documento di metadati per il flusso utente b2c_1_sign_in nel tenant fabrikamb2c.onmicrosoft.com si trova in:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Una proprietà di questo documento di configurazione è `jwks_uri`. Il valore per lo stesso flusso utente sarà:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Per individuare il flusso utente usato per la firma di un token ID e la posizione da cui recuperare i metadati, sono disponibili due opzioni. Innanzitutto, il nome del flusso utente è incluso nell'attestazione `acr` in `id_token`. Per informazioni su come analizzare le attestazioni da un token ID, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md). In secondo luogo, è possibile codificare il flusso utente nel valore del parametro `state` quando si emette la richiesta, per poi decodificare il parametro `state` e determinare il flusso utente usato. Entrambi i metodi sono validi.

Dopo aver acquisito il documento dei metadati dall'endpoint di metadati OpenID Connect, è possibile usare le chiavi pubbliche RSA 256 che si trovano in questo endpoint per convalidare la firma del token ID. In un determinato punto nel tempo è possibile che siano presenti più chiavi elencate in questo endpoint, ognuna identificata da un'attestazione `kid`. Anche l'intestazione di `id_token` contiene un'attestazione `kid`, che indica le chiavi usate per firmare il token ID. Per altre informazioni, inclusa la [convalida dei token](active-directory-b2c-reference-tokens.md), vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Dopo la convalida della firma del token ID sarà necessario verificare anche diverse altre attestazioni, ad esempio:

* Convalidare l'attestazione `nonce` per impedire attacchi di riproduzione dei token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
* Convalidare l'attestazione `aud` per verificare che il token ID sia stato emesso per l'app. Il valore deve essere l'ID applicazione dell'app.
* Convalidare le attestazioni `iat` e `exp` per verificare che il token ID non sia scaduto.

Altre convalide da eseguire sono descritte in dettaglio nella [specifica di OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). Potrebbe anche essere necessario convalidare attestazioni aggiuntive, a seconda dello scenario. Alcune convalide comuni includono:

* Verificare che l'utente o l'organizzazione abbia eseguito l'iscrizione all'app.
* Verificare che l'utente abbia le autorizzazioni e i privilegi adeguati.
* Verificare che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori di Azure.

Per altri dettagli sulle attestazioni in un token ID, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).

Dopo avere convalidato il token ID, è possibile avviare una sessione con l'utente. Usare nell'app le attestazioni del token ID per ottenere informazioni sull'utente. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## <a name="get-access-tokens"></a>Ottenere i token di accesso
Se l'app Web deve solo eseguire flussi utente, è possibile saltare le prossime sezioni. Le informazioni nelle sezioni seguenti si applicano solo alle app Web che devono eseguire chiamate autenticate a un'API Web e che sono protette da Azure AD B2C.

Ora che l'utente ha eseguito l'accesso all'app a singola pagina, è possibile ottenere i token di accesso per chiamare le API Web protette da Azure AD. Anche se si è già ricevuto un token usando il tipo di risposta `token`, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

In un tipico flusso dell'app Web è necessario effettuare una richiesta all'endpoint `/token`. Tuttavia, l'endpoint non supporta le richieste CORS, pertanto non è possibile eseguire chiamate AJAX per ottenere un token di aggiornamento. È possibile usare invece il flusso implicito in un elemento iframe HTML nascosto per ottenere nuovi token per altre API Web. Di seguito è riportato un esempio, con le interruzioni di riga per migliorare la leggibilità:

```HTTP
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Parametro | Obbligatorio? | Description |
| --- | --- | --- |
|inquilino| Obbligatoria | Nome del tenant di Azure AD B2C|
politica| Obbligatoria| Flusso utente da eseguire. Specificare il nome di un flusso utente creato nel tenant del Azure AD B2C. Ad esempio, `b2c_1_sign_in`, `b2c_1_sign_up` o `b2c_1_edit_profile`. |
| client_id |Obbligatoria |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com). |
| response_type |Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect.  Può anche includere il tipo di risposta `token`. Se si usa `token` in questo momento, l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint. Se si usa il tipo di risposta `token`, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| redirect_uri |Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatoria |Elenco di ambiti separati da spazi.  Per ottenere i token, includere tutti gli ambiti necessari per la risorsa di interesse. |
| response_mode |Consigliato |Specifica il metodo usato per restituire il token risultante all'app. Per il flusso implicito, usare `fragment`. È possibile specificare due altre modalità, `query` e `form_post`, ma non funzionano nel flusso implicito. |
| state |Consigliato |Valore incluso nella richiesta che viene restituito nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto si voglia usare.  Per evitare attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente.  Anche lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima del verificarsi della richiesta di autenticazione, ad esempio, la pagina o la vista in cui si trovava l'utente. |
| nonce |Obbligatoria |Valore incluso nella richiesta, generata dall'app, che viene incluso nel token ID risultante come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che identifica l'origine della richiesta. |
| prompt |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, usare `prompt=none` per fare in modo che l'iframe non si blocchi alla pagina di accesso e risponda immediatamente. |
| login_hint |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, includere il nome utente dell'utente in questo hint per distinguere tra le eventuali varie sessioni dell'utente in un determinato momento. È possibile estrarre il nome utente da un accesso precedente usando l'attestazione `preferred_username` (è necessario l'ambito `profile` per ricevere l'attestazione `preferred_username`). |
| domain_hint |Obbligatoria |Può essere `consumers` o `organizations`.  Per aggiornare e ottenere i token in un iframe nascosto, includere il valore `domain_hint` nella richiesta.  Estrarre l'attestazione `tid` dal token ID di un accesso precedente per determinare il valore da usare (l'ambito del `profile` è necessario per ricevere l'attestazione `tid`). Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`.  In caso contrario, usare `domain_hint=organizations`. |

Impostando il parametro `prompt=none`, la richiesta ha immediatamente esito positivo o negativo e torna all'applicazione.  Una risposta con esito positivo verrà inviata all'app all'URI di reindirizzamento indicato, usando il metodo specificato nel parametro `response_mode`.

### <a name="successful-response"></a>Risposta con esito positivo
Una risposta con esito positivo utilizzando `response_mode=fragment` è simile a questo esempio:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametro | Description |
| --- | --- |
| access_token |Token richiesto dall'app. |
| token_type |Il tipo di token sarà sempre una connessione. |
| state |Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |
| expires_in |Validità del token di accesso (espressa in secondi). |
| scope |Ambiti per i quali il token di accesso è valido. |

### <a name="error-response"></a>Risposta di errore
Anche le risposte di errore possono essere inviate all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente.  Per `prompt=none`, un errore previsto è simile a questo esempio:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | Description |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errore che si verificano. È possibile usare la stringa anche per rispondere agli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

Se si riceve questo errore nella richiesta iframe, l'utente deve accedere di nuovo in modo interattivo per recuperare un nuovo token.

## <a name="refresh-tokens"></a>Token di aggiornamento
Token ID e token di accesso scadono entrambi dopo un breve periodo di tempo. L'app deve essere predisposta ad aggiornare periodicamente questi token.  Per aggiornare entrambi i tipi di token, eseguire la stessa richiesta nell'iframe nascosto usata in un esempio precedente tramite il parametro `prompt=none` per controllare i passaggi di Azure AD.  Per ricevere un nuovo valore `id_token`, assicurarsi di usare `response_type=id_token`, `scope=openid` e un parametro `nonce`.

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione
Quando si vuole disconnettersi dall'app, reindirizzare l'utente a Azure AD per disconnettersi. Se non si reindirizza l'utente, potrebbe essere in grado di eseguire di nuovo l'autenticazione all'app senza immettere di nuovo le credenziali perché hanno una sessione di Single Sign-On valida con Azure AD.

È sufficiente reindirizzare l'utente all'oggetto `end_session_endpoint` elencato nello stesso documento dei metadati di OpenID Connect descritto in [Convalidare il token ID](#validate-the-id-token). ad esempio:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametro | Obbligatoria | Description |
| --------- | -------- | ----------- |
| inquilino | SÌ | Nome del tenant di Azure AD B2C |
| politica | SÌ | Flusso utente da usare per disconnettere l'utente dall'applicazione. |
| post_logout_redirect_uri | No | URL a cui l'utente deve essere reindirizzato dopo la disconnessione. Se non è incluso, Azure AD B2C Visualizza un messaggio generico da parte dell'utente. |
| state | No | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'applicazione deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |


> [!NOTE]
> Anche se indirizzare l'utente a `end_session_endpoint` permette di cancellare parte dello stato Single Sign-On dell'utente con Azure AD B2C, l'utente non viene disconnesso dalla sessione del provider di identità basato su social network. Se l'utente seleziona lo stesso provider di identità durante un accesso successivo, l'utente viene autenticato nuovamente, senza immettere le credenziali. Se un utente vuole disconnettersi dall'applicazione Azure AD B2C, non significa necessariamente che intenda, ad esempio, disconnettersi del tutto dall'account Facebook. Tuttavia, per gli account locali, la sessione dell'utente viene terminata in modo corretto.
>

## <a name="next-steps"></a>Passaggi successivi

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>Esempio di codice: Hello. js con Azure AD B2C

[Applicazione a singola pagina basata su Hello. js con Azure ad B2C][github-hello-js-example] (GitHub)

Questo esempio su GitHub è concepito per aiutare a iniziare a usare Azure AD B2C in una semplice applicazione Web basata su [Hello. js][github-hello-js] e con l'autenticazione popup.

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-hellojs-singlepageapp
[github-hello-js]: https://github.com/MrSwitch/hello.js
