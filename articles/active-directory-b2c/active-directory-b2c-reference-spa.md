---
title: Accesso a singola pagina con Azure Active Directory B2C del flusso implicito
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
ms.openlocfilehash: 1196f3b186abcd914c409db06b52654f82f4158b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377328"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Accesso a pagina singola con il flusso implicito OAuth 2,0 in Azure Active Directory B2C

Molte applicazioni moderne hanno un front-end di app a singola pagina scritto principalmente in JavaScript. Spesso l'app viene scritta usando un Framework come REACT, angolare o VME. js. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche aggiuntive per l'autenticazione:

- Le caratteristiche di sicurezza di queste app sono diverse dalle tradizionali applicazioni Web basate su server.
- Molti server di autorizzazione e provider di identità non supportano le richieste CORS (Condivisione risorse tra le origini).
- I reindirizzamenti del browser a pagina intera lontani dall'app possono essere invasivi per l'esperienza utente.

Per supportare queste applicazioni, Azure Active Directory B2C (Azure AD B2C) usa il flusso implicito OAuth 2.0. Il flusso di concessione implicita OAuth 2.0 è descritto nella [sezione 4.2 della specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749). Nel flusso implicito l'app riceve i token direttamente dall'endpoint di autorizzazione di Azure Active Directory (Azure AD), senza alcuno scambio tra server. Tutta la logica di autenticazione e la gestione delle sessioni vengono eseguite interamente nel client JavaScript con un reindirizzamento della pagina o una finestra popup.

Azure AD B2C estende il flusso implicito OAuth 2.0 standard e va oltre le semplici operazioni di autorizzazione e autenticazione. Azure AD B2C introduce il [parametro di criteri](active-directory-b2c-reference-policies.md). Con il parametro di criteri è possibile usare OAuth 2.0 per aggiungere criteri all'app, ad esempio i flussi utente di iscrizione, accesso e gestione dei profili. Nelle richieste HTTP di esempio in questo articolo, **fabrikamb2c.onmicrosoft.com** viene usato come esempio. È possibile sostituire `fabrikamb2c` con il nome del tenant se ne è stato creato uno ed è stato creato un flusso utente.

Il flusso di accesso implicito è simile a quello illustrato nella figura seguente. Ogni passaggio verrà descritto in dettaglio più avanti nell'articolo.

![Diagramma di tipo corsia che mostra il flusso implicito di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Invio di richieste di autenticazione

Quando l'applicazione Web deve autenticare l'utente ed eseguire un flusso utente, può indirizzare l'utente all' `/authorize` endpoint. L'utente esegue un'azione a seconda del flusso utente.

In questa richiesta il client indica le autorizzazioni che deve acquisire dall'utente nel `scope` parametro e il flusso utente da eseguire `p` nel parametro. Di seguito vengono illustrati tre esempi (con interruzioni di riga per migliorare la leggibilità), ognuno dei quali usa un flusso utente diverso. Per apprendere il funzionamento di ogni richiesta, provare a incollare la richiesta in un browser ed eseguirla. È possibile sostituire `fabrikamb2c` con il nome del tenant se ne è stato creato uno ed è stato creato un flusso utente.

### <a name="use-a-sign-in-user-flow"></a>Usare un flusso utente di accesso

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Usare un flusso utente di iscrizione
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Usare un flusso utente di modifica del profilo
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametro | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| client_id | Sì | ID applicazione assegnato dall' [portale di Azure](https://portal.azure.com/) all'applicazione. |
| response_type | Sì | Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere il tipo di risposta `token`. Se si usa `token`, l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint.  Se si usa il tipo di risposta `token`, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| redirect_uri | No | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| response_mode | No | Specifica il metodo da usare per restituire il token risultante all'app.  Per i flussi impliciti, usare `fragment`. |
| scope | Yes | Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. L’ambito `offline_access` è facoltativo per le applicazioni Web. Indica che l'app necessita di un token di aggiornamento per avere un accesso duraturo alle risorse. |
| stato | No | Valore incluso nella richiesta che viene anche restituito nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto si voglia usare. Per evitare attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina in cui si trovava. |
| nonce | Sì | Valore incluso nella richiesta, generata dall'app, che viene incorporato nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| p | Sì | Criterio da eseguire. Si tratta del nome di un criterio (flusso utente) creato nel tenant di Azure AD B2C. Il valore del nome dei criteri deve iniziare con **b2c\_1\_** . |
| prompt | No | Tipo di interazione utente obbligatoria. Al momento l'unico valore valido è `login`. Questo parametro impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-on non ha effetto. |

Viene a questo punto richiesto all'utente di completare il flusso di lavoro dei criteri. È possibile che l'utente debba immettere il nome utente e la password, accedere con un'identità di social networking, iscriversi alla directory o a qualsiasi altro numero di passaggi. Le azioni dell'utente dipendono dal modo in cui è definito il flusso utente.

Dopo che l'utente ha completato il flusso utente, Azure AD restituisce una risposta per l'app in corrispondenza del valore usato per `redirect_uri`. Viene usato il metodo specificato nel parametro `response_mode`. La risposta è esattamente la stessa per ogni scenario di azione dell'utente, indipendentemente dal flusso utente eseguito.

### <a name="successful-response"></a>Risposta riuscita
Una risposta con esito positivo che usa `response_mode=fragment` e `response_type=id_token+token` è simile a quanto riportato di seguito. Sono state aggiunte interruzioni di riga per migliorare la leggibilità:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parametro | Descrizione |
| --------- | ----------- |
| access_token | Token di accesso richiesto dall'app. |
| token_type | Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| expires_in | Periodo di validità del token di accesso (in secondi). |
| scope | Ambiti per i quali il token è valido. È possibile usare gli ambiti anche per memorizzare i token nella cache per un uso successivo. |
| id_token | Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Per informazioni dettagliate sui token ID e sul relativo contenuto, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md). |
| stato | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |

### <a name="error-response"></a>Risposta di errore
Anche le risposte di errore possono essere inviate all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| --------- | ----------- |
| error | Codice utilizzato per classificare i tipi di errori che si verificano. |
| error_description | Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| stato | Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici.|

## <a name="validate-the-id-token"></a>Convalidare il token ID

La ricezione di un token ID non è sufficiente per autenticare l'utente. Convalidare la firma del token ID e verificare le attestazioni nel token in base ai requisiti dell'app. Azure AD B2C usa i [token Web JSON](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

Sono disponibili molte librerie open source per la convalida dei token JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame le librerie open source disponibili anziché implementare una logica di convalida personalizzata. È possibile usare le informazioni contenute in questo articolo permettono di imparare a usare correttamente tali librerie.

Azure AD B2C include un endpoint dei metadati di OpenID Connect. Un'app può usare l'endpoint per recuperare informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant Azure AD B2C include un documento di metadati JSON per ogni flusso utente. Ad esempio, il documento di metadati per il flusso utente b2c_1_sign_in nel tenant fabrikamb2c.onmicrosoft.com si trova in:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una proprietà di questo documento di configurazione è `jwks_uri`. Il valore per lo stesso flusso utente sarà:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Per individuare il flusso utente usato per la firma di un token ID e la posizione da cui recuperare i metadati, sono disponibili due opzioni. Innanzitutto, il nome del flusso utente è incluso nell'attestazione `acr` in `id_token`. Per informazioni su come analizzare le attestazioni da un token ID, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md). In secondo luogo, è possibile codificare il flusso utente nel valore del parametro `state` quando si emette la richiesta, per poi decodificare il parametro `state` e determinare il flusso utente usato. Entrambi i metodi sono validi.

Dopo aver acquisito il documento dei metadati dall'endpoint di metadati OpenID Connect, è possibile usare le chiavi pubbliche RSA 256 che si trovano in questo endpoint per convalidare la firma del token ID. In un determinato punto nel tempo è possibile che siano presenti più chiavi elencate in questo endpoint, ognuna identificata da un'attestazione `kid`. Anche l'intestazione di `id_token` contiene un'attestazione `kid`, che indica le chiavi usate per firmare il token ID. Per altre informazioni, inclusa la [convalida dei token](active-directory-b2c-reference-tokens.md), vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Dopo la convalida della firma del token ID sarà necessario verificare anche diverse altre attestazioni, Ad esempio:

* Convalidare l'attestazione `nonce` per impedire attacchi di riproduzione dei token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
* Convalidare l'attestazione `aud` per verificare che il token ID sia stato emesso per l'app. Il valore deve essere l'ID applicazione dell'app.
* Convalidare le attestazioni `iat` e `exp` per verificare che il token ID non sia scaduto.

Esistono anche diverse altre convalide che è opportuno eseguire, descritte in dettaglio nella [specifica di OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

* Verificare che l'utente o l'organizzazione abbia eseguito l'iscrizione all'app.
* Verificare che l'utente abbia le autorizzazioni e i privilegi adeguati.
* Verificare che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori di Azure.

Per altri dettagli sulle attestazioni in un token ID, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).

Dopo avere convalidato il token ID, è possibile avviare una sessione con l'utente. Usare nell'app le attestazioni del token ID per ottenere informazioni sull'utente. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## <a name="get-access-tokens"></a>Ottenere i token di accesso
Se l'app Web deve solo eseguire flussi utente, è possibile saltare le prossime sezioni. Le informazioni nelle sezioni seguenti si applicano solo alle app Web che devono eseguire chiamate autenticate a un'API Web e che sono protette da Azure AD B2C.

Ora che l'utente ha eseguito l'accesso all'app a singola pagina, è possibile ottenere i token di accesso per chiamare le API Web protette da Azure AD. Anche se si è già ricevuto un token usando il tipo di risposta `token`, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

In un tipico flusso dell' `/token` app Web, è necessario effettuare una richiesta all'endpoint. Tuttavia, l'endpoint non supporta le richieste CORS, pertanto non è possibile eseguire chiamate AJAX per ottenere un token di aggiornamento. È possibile usare invece il flusso implicito in un elemento iframe HTML nascosto per ottenere nuovi token per altre API Web. Di seguito è riportato un esempio, con le interruzioni di riga per migliorare la leggibilità:

```
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&p=b2c_1_sign_in
```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
| client_id |Obbligatoria |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com). |
| response_type |Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect.  Può anche includere il tipo di risposta `token`. Se si usa `token` in questo momento, l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint. Se si usa il tipo di risposta `token`, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| redirect_uri |Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatoria |Elenco di ambiti separati da spazi.  Per ottenere i token, includere tutti gli ambiti necessari per la risorsa di interesse. |
| response_mode |Consigliato |Specifica il metodo usato per restituire il token risultante all'app.  Può essere `query`, `form_post` o `fragment`. |
| stato |Consigliato |Valore incluso nella richiesta che viene restituito nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto si voglia usare.  Per evitare attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente.  Anche lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima del verificarsi della richiesta di autenticazione, ad esempio, la pagina o la vista in cui si trovava l'utente. |
| nonce |Obbligatoria |Valore incluso nella richiesta, generata dall'app, che viene incluso nel token ID risultante come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che identifica l'origine della richiesta. |
| prompt |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, usare `prompt=none` per fare in modo che l'iframe non si blocchi alla pagina di accesso e risponda immediatamente. |
| login_hint |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, includere il nome utente dell'utente in questo hint per distinguere tra le eventuali varie sessioni dell'utente in un determinato momento. È possibile estrarre il nome utente da un accesso precedente usando l' `preferred_username` attestazione (l' `profile` ambito è obbligatorio per poter ricevere l' `preferred_username` attestazione). |
| domain_hint |Obbligatoria |Può essere `consumers` o `organizations`.  Per aggiornare e ottenere i token in un iframe nascosto, includere il `domain_hint` valore nella richiesta.  Estrarre l' `tid` attestazione dal token ID di un accesso precedente per determinare il valore da usare (l' `profile` ambito è necessario per ricevere l' `tid` attestazione). Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`.  In caso contrario, usare `domain_hint=organizations`. |

Impostando il parametro `prompt=none`, la richiesta ha immediatamente esito positivo o negativo e torna all'applicazione.  Una risposta con esito positivo verrà inviata all'app all'URI di reindirizzamento indicato, usando il metodo specificato nel parametro `response_mode`.

### <a name="successful-response"></a>Risposta riuscita
Una risposta corretta con `response_mode=fragment` ha un aspetto simile all'esempio seguente:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametro | Descrizione |
| --- | --- |
| access_token |Token richiesto dall'app. |
| token_type |Il tipo di token sarà sempre una connessione. |
| stato |Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |
| expires_in |Validità del token di accesso (espressa in secondi). |
| scope |Ambiti per i quali il token di accesso è valido. |

### <a name="error-response"></a>Risposta di errore
Anche le risposte di errore possono essere inviate all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente.  Per `prompt=none`, un errore previsto è simile a questo esempio:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | DESCRIZIONE |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errore che si verificano. È possibile usare la stringa anche per rispondere agli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

Se si riceve questo errore nella richiesta iframe, l'utente deve accedere di nuovo in modo interattivo per recuperare un nuovo token.

## <a name="refresh-tokens"></a>Token di aggiornamento
Token ID e token di accesso scadono entrambi dopo un breve periodo di tempo. L'app deve essere predisposta ad aggiornare periodicamente questi token.  Per aggiornare entrambi i tipi di token, eseguire la stessa richiesta nell'iframe nascosto usata in un esempio precedente tramite il parametro `prompt=none` per controllare i passaggi di Azure AD.  Per ricevere un nuovo valore `id_token`, assicurarsi di usare `response_type=id_token`, `scope=openid` e un parametro `nonce`.

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione
Per disconnettere l'utente dall'app, reindirizzare l'utente alla disconnessione da Azure AD. Se non si reindirizza l'utente, potrebbe essere in grado di eseguire di nuovo l'autenticazione all'app senza immettere di nuovo le credenziali perché hanno una sessione Single Sign-on valida con Azure AD.

È sufficiente reindirizzare l'utente all'oggetto `end_session_endpoint` elencato nello stesso documento dei metadati di OpenID Connect descritto in [Convalidare il token ID](#validate-the-id-token). Ad esempio:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
| p |Obbligatoria |Criteri da usare per disconnettere l'utente dall'applicazione. |
| post_logout_redirect_uri |Consigliato |URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Se omesso, Azure AD B2C visualizzerà un messaggio generico. |

> [!NOTE]
> Anche se indirizzare l'utente a `end_session_endpoint` permette di cancellare parte dello stato Single Sign-On dell'utente con Azure AD B2C, l'utente non viene disconnesso dalla sessione del provider di identità basato su social network. Se l'utente seleziona lo stesso provider di identità durante un accesso successivo, l'utente viene autenticato nuovamente, senza immettere le credenziali. Se un utente vuole disconnettersi dall'applicazione Azure AD B2C, non significa necessariamente che intenda, ad esempio, disconnettersi del tutto dall'account Facebook. Tuttavia, per gli account locali, la sessione dell'utente viene terminata in modo corretto.
>

## <a name="next-steps"></a>Passaggi successivi

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>Esempio di codice: Hello. js con Azure AD B2C

[Applicazione a singola pagina basata su Hello. js con Azure ad B2C][github-hello-js-example] GitHub

Questo esempio su GitHub è concepito per aiutare a iniziare a usare Azure AD B2C in una semplice applicazione Web basata su [Hello. js][github-hello-js] e con l'autenticazione popup.

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/azure-ad-b2c/apps/tree/master/spa/javascript-hellojs-singlepageapp-popup
[github-hello-js]: https://github.com/MrSwitch/hello.js
