---
title: 'Azure Active Directory B2C: app a singola pagina tramite flusso implicito | Microsoft Docs'
description: Informazioni su come creare app a singola pagina tramite il flusso implicito OAuth 2.0 con Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.openlocfilehash: ac0351ce220da5194d3a447e51185409b7368f21
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Azure Active Directory B2C: app a singola pagina tramite il flusso implicito OAuth 2.0

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

Molte app moderne hanno un front-end dell'app a singola pagina scritto principalmente in JavaScript. L'app viene scritta spesso usando un framework come AngularJS, Ember.js o Durandal. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche aggiuntive per l'autenticazione:

* Le caratteristiche di sicurezza di queste app sono considerevolmente diverse da quelle delle tradizionali applicazioni Web basate su server.
* Molti server di autorizzazione e provider di identità non supportano le richieste CORS (Condivisione risorse tra le origini).
* I reindirizzamenti dei browser a pagina intera dall'app possono risultare particolarmente fastidiosi per l'esperienza utente.

Per supportare queste applicazioni, Azure Active Directory B2C (Azure AD B2C) usa il flusso implicito OAuth 2.0. Il flusso di concessione implicita OAuth 2.0 è descritto nella [sezione 4.2 della specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749). Nel flusso implicito l'app riceve i token direttamente dall'endpoint di autorizzazione di Azure Active Directory (Azure AD), senza alcuno scambio tra server. Tutta la logica di autenticazione e gestione della sessione avviene interamente nel client JavaScript, senza eseguire ulteriori reindirizzamenti di pagina.

Azure AD B2C estende il flusso implicito OAuth 2.0 standard e va oltre le semplici operazioni di autorizzazione e autenticazione. Azure AD B2C introduce il [parametro di criteri](active-directory-b2c-reference-policies.md). Con il parametro di criteri è possibile usare OAuth 2.0 per aggiungere esperienze utente all'app, ad esempio la gestione dell'iscrizione, dell'accesso e del profilo. Questo articolo spiega come usare il flusso implicito e Azure AD per implementare ogni esperienza nelle applicazioni a singola pagina. Per iniziare, vedere anche gli esempi relativi a [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) e [Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi).

Nelle richieste HTTP di esempio in questo articolo si usa la directory di Azure AD B2C di esempio, **fabrikamb2c.onmicrosoft.com**. Si usano anche i criteri e l'applicazione di esempio. È possibile provare le richieste in autonomia usando questi valori o sostituendoli con valori personalizzati.
Altre informazioni su come [ottenere la directory, l'applicazione e i criteri di Azure AD B2C personalizzati](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Diagramma di protocollo

Il flusso di accesso implicito è simile a quello illustrato nella figura seguente. Ogni passaggio verrà descritto in dettaglio più avanti nell'articolo.

![Corsie di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Invio di richieste di autenticazione
Quando l'app Web deve autenticare l'utente ed eseguire un criterio, indirizza l'utente all'endpoint `/authorize`. Questa è la parte interattiva del flusso, dove l'utente esegue operazioni in base ai criteri. L'utente riceve un token ID dall'endpoint di Azure AD.

In questa richiesta il client indica nel parametro `scope` le autorizzazioni che deve acquisire dall'utente. Nel parametro `p` indica i criteri da eseguire. Di seguito vengono presentati tre esempi (con interruzioni di riga per migliorare la leggibilità), ognuno dei quali usa criteri diversi. Per apprendere il funzionamento di ogni richiesta, provare a incollare la richiesta in un browser ed eseguirla.

### <a name="use-a-sign-in-policy"></a>Uso di un criterio di accesso
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Uso di un criterio di iscrizione
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Uso di un criterio di modifica del profilo
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametro | Obbligatorio? | DESCRIZIONE |
| --- | --- | --- |
| client_id |Obbligatoria |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com). |
| response_type |Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect. Può anche includere il tipo di risposta `token`. Se si usa `token`, l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint.  Se si usa il tipo di risposta `token`, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| redirect_uri |Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| response_mode |Consigliato |Specifica il metodo da usare per restituire il token risultante all'app.  Per i flussi impliciti, usare `fragment`. |
| scope |Obbligatoria |Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. Verranno fornite informazioni più avanti in questo articolo. L’ambito `offline_access` è facoltativo per le applicazioni Web. Indica che l'app necessita di un token di aggiornamento per avere un accesso duraturo alle risorse. |
| state |Consigliato |Valore incluso nella richiesta che viene anche restituito nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto si voglia usare. Per evitare attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina in cui si trovava. |
| nonce |Obbligatoria |Valore incluso nella richiesta, generata dall'app, che viene incorporato nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| p |Obbligatoria |Criterio da eseguire. Si tratta del nome di un criterio creato nel tenant di Azure AD B2C. Il valore del nome dei criteri deve iniziare con **b2c\_1\_**. Per altre informazioni, vedere [Criteri incorporati di Azure AD B2C](active-directory-b2c-reference-policies.md). |
| prompt |Facoltativo |Tipo di interazione utente obbligatoria. Al momento l'unico valore valido è `login`. L'utente è costretto a immettere le credenziali alla richiesta. L'accesso Single Sign-On non avrà effetto. |

Viene a questo punto richiesto all'utente di completare il flusso di lavoro dei criteri. È possibile che venga richiesto all'utente di immettere nome utente e password, di accedere con un'identità di social networking, di iscriversi alla directory o di effettuare qualsiasi altro passaggio. Le azioni dell'utente dipendono dal modo in cui sono definiti i criteri.

Dopo che l'utente ha completato i criteri, Azure AD restituisce una risposta per l'app in corrispondenza del valore usato per `redirect_uri`. Viene usato il metodo specificato nel parametro `response_mode`. La risposta è esattamente la stessa per ogni scenario di azione dell'utente, indipendentemente dai criteri eseguiti.

### <a name="successful-response"></a>Risposta con esito positivo
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

| Parametro | DESCRIZIONE |
| --- | --- |
| access_token |Token di accesso richiesto dall'app.  Il token di accesso non deve essere decodificato o controllato in altro modo, ma può essere trattato come una stringa opaca. |
| token_type |Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| expires_in |Periodo di validità del token di accesso (in secondi). |
| scope |Ambiti per i quali il token è valido. È possibile usare gli ambiti anche per memorizzare i token nella cache per un uso successivo. |
| id_token |Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Per informazioni dettagliate sui token ID e sul relativo contenuto, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md). |
| state |Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |

### <a name="error-response"></a>Risposta di errore
Anche le risposte di errore possono essere inviate all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | DESCRIZIONE |
| --- | --- |
| error |Stringa di codice di errore usata per classificare i tipi di errore che si verificano. È inoltre possibile usare il codice di errore per la gestione degli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| state |Vedere la descrizione completa nella tabella precedente. Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici.|

## <a name="validate-the-id-token"></a>Convalidare il token ID
La ricezione di un token ID non è sufficiente per autenticare l'utente. È necessario convalidare la firma del token ID e verificare se le attestazioni nel token soddisfano i requisiti dell'app. Azure AD B2C usa i [token Web JSON](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

Sono disponibili molte librerie open source per la convalida dei token JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame le librerie open source disponibili anziché implementare una logica di convalida personalizzata. È possibile usare le informazioni contenute in questo articolo permettono di imparare a usare correttamente tali librerie.

Azure AD B2C include un endpoint dei metadati di OpenID Connect. Un'app può usare l'endpoint per recuperare informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant Azure AD B2C include un documento di metadati JSON per ogni criterio. Ad esempio, il documento di metadati per il criterio b2c_1_sign_in nel tenant fabrikamb2c.onmicrosoft.com si trova in:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una proprietà di questo documento di configurazione è `jwks_uri`. Il valore per lo stesso criterio sarebbe:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Per determinare quale criterio è stato usato per la firma di un token ID e la posizione da cui recuperare i metadati, sono disponibili due opzioni. Prima di tutto, il nome del criterio è incluso nell'attestazione `acr` in `id_token`. Per informazioni su come analizzare le attestazioni da un token ID, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md). L'altra opzione consiste nel codificare i criteri nel valore del parametro `state` quando si emette la richiesta, per poi decodificare il parametro `state` e determinare quali criteri sono stati usati. Entrambi i metodi sono validi.

Dopo aver acquisito il documento dei metadati dall'endpoint di metadati OpenID Connect, è possibile usare le chiavi pubbliche RSA 256 che si trovano in questo endpoint per convalidare la firma del token ID. In un determinato punto nel tempo è possibile che siano presenti più chiavi elencate in questo endpoint, ognuna identificata da un'attestazione `kid`. Anche l'intestazione di `id_token` contiene un'attestazione `kid`, che indica le chiavi usate per firmare il token ID. Per altre informazioni, inclusa la [convalida dei token](active-directory-b2c-reference-tokens.md#token-validation), vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Dopo la convalida della firma del token ID sarà necessario verificare anche diverse altre attestazioni, Ad esempio: 

* Convalidare l'attestazione `nonce` per impedire attacchi di riproduzione dei token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
* Convalidare l'attestazione `aud` per verificare che il token ID sia stato emesso per l'app. Il valore deve essere l'ID applicazione dell'app.
* Convalidare le attestazioni `iat` e `exp` per verificare che il token ID non sia scaduto.

Esistono anche diverse altre convalide che è opportuno eseguire, descritte in dettaglio nella [specifica di OpenID Connect Core](http://openid.net/specs/openid-connect-core-1_0.html). È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

* Verificare che l'utente o l'organizzazione abbia eseguito l'iscrizione all'app.
* Verificare che l'utente abbia le autorizzazioni e i privilegi adeguati.
* Verificare che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori di Azure.

Per altri dettagli sulle attestazioni in un token ID, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).

Dopo aver convalidato il token ID, è possibile avviare una sessione con l'utente. Usare nell'app le attestazioni del token ID per ottenere informazioni sull'utente. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## <a name="get-access-tokens"></a>Ottenere i token di accesso
Se l'app Web deve solo eseguire criteri, è possibile saltare le prossime sezioni. Le informazioni nelle sezioni seguenti sono applicabili solo alle app Web che devono eseguire chiamate autenticate a un'API Web e che sono protette da Azure AD B2C.

Dopo l'accesso dell'utente all'app a singola pagina, è possibile ottenere i token di accesso per chiamare le API Web protette da Azure AD. Anche se si è già ricevuto un token usando il tipo di risposta `token`, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

In un tipico flusso di app Web, per eseguire questa operazione si invia una richiesta all'endpoint di `/token`.  L'endpoint non supporta però le richieste CORS. Non è quindi possibile eseguire chiamate AJAX per ottenere e aggiornare i token. È possibile usare invece il flusso implicito in un elemento iframe HTML nascosto per ottenere nuovi token per altre API Web. Di seguito è riportato un esempio, con le interruzioni di riga per migliorare la leggibilità:

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parametro | Obbligatorio? | DESCRIZIONE |
| --- | --- | --- |
| client_id |Obbligatoria |ID applicazione assegnato all'app nel [portale di Azure](https://portal.azure.com). |
| response_type |Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect.  Può anche includere il tipo di risposta `token`. Se si usa `token` in questo momento, l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint. Se si usa il tipo di risposta `token`, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| redirect_uri |Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatoria |Elenco di ambiti separati da spazi.  Per ottenere i token, includere tutti gli ambiti necessari per la risorsa di interesse. |
| response_mode |Consigliato |Specifica il metodo usato per restituire il token risultante all'app.  Può essere `query`, `form_post` o `fragment`. |
| state |Consigliato |Valore incluso nella richiesta che viene restituito nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto si voglia usare.  Per evitare attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente.  Anche lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima del verificarsi della richiesta di autenticazione, ad esempio, la pagina o la vista in cui si trovava l'utente. |
| nonce |Obbligatoria |Valore incluso nella richiesta, generata dall'app, che viene incluso nel token ID risultante come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che identifica l'origine della richiesta. |
| prompt |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, usare `prompt=none` per fare in modo che l'iframe non si blocchi alla pagina di accesso e risponda immediatamente. |
| login_hint |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, includere il nome utente dell'utente in questo hint per distinguere tra le eventuali varie sessioni dell'utente in un determinato momento. È possibile estrarre il nome utente da un accesso precedente usando l'attestazione `preferred_username`. |
| domain_hint |Obbligatoria |Può essere `consumers` o `organizations`.  Per aggiornare e ottenere i token in un iframe nascosto, è necessario includere il valore `domain_hint` nella richiesta.  Estrarre l'attestazione `tid` dal token ID di un accesso precedente per determinare il valore da usare.  Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`.  In caso contrario, usare `domain_hint=organizations`. |

Impostando il parametro `prompt=none`, la richiesta ha immediatamente esito positivo o negativo e torna all'applicazione.  Una risposta con esito positivo verrà inviata all'app all'URI di reindirizzamento indicato, usando il metodo specificato nel parametro `response_mode`.

### <a name="successful-response"></a>Risposta con esito positivo
La risposta con esito positivo quando si usa `response_mode=fragment` è simile alla seguente:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametro | DESCRIZIONE |
| --- | --- |
| access_token |Token richiesto dall'app. |
| token_type |Il tipo di token sarà sempre una connessione. |
| state |Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |
| expires_in |Validità del token di accesso (espressa in secondi). |
| scope |Ambiti per i quali il token di accesso è valido. |

### <a name="error-response"></a>Risposta di errore
Anche le risposte di errore possono essere inviate all'URI di reindirizzamento in modo che l'app possa gestirle adeguatamente.  Per `prompt=none` un errore previsto è simile al seguente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | DESCRIZIONE |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errore che si verificano. È possibile usare la stringa anche per rispondere agli errori. |
| error_description |Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |

Se si riceve questo errore nella richiesta iframe, l'utente deve accedere di nuovo in modo interattivo per recuperare un nuovo token. È possibile gestire questa condizione in modo appropriato per l'applicazione.

## <a name="refresh-tokens"></a>Token di aggiornamento
Token ID e token di accesso scadono entrambi dopo un breve periodo di tempo. L'app deve essere predisposta ad aggiornare periodicamente questi token.  Per aggiornare entrambi i tipi di token, eseguire la stessa richiesta nell'iframe nascosto usata in un esempio precedente tramite il parametro `prompt=none` per controllare i passaggi di Azure AD.  Per ricevere un nuovo valore `id_token`, assicurarsi di usare `response_type=id_token`, `scope=openid` e un parametro `nonce`.

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione
Per disconnettere l'utente dall'app, reindirizzare l'utente alla disconnessione da Azure AD. In caso contrario, l'utente potrebbe essere in grado di autenticarsi nuovamente all'app senza immettere le credenziali, in quanto avrà accesso a una sessione Single Sign-On valida con Azure AD.

È sufficiente reindirizzare l'utente all'oggetto `end_session_endpoint` elencato nello stesso documento dei metadati di OpenID Connect descritto in [Convalidare il token ID](#validate-the-id-token). Ad esempio: 

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametro | Obbligatorio? | DESCRIZIONE |
| --- | --- | --- |
| p |Obbligatoria |Criteri da usare per disconnettere l'utente dall'applicazione. |
| post_logout_redirect_uri |Consigliato |URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Se omesso, Azure AD B2C visualizzerà un messaggio generico. |

> [!NOTE]
> Anche se indirizzare l'utente a `end_session_endpoint` permette di cancellare parte dello stato Single Sign-On dell'utente con Azure AD B2C, l'utente non viene disconnesso dalla sessione del provider di identità basato su social network. Se l'utente seleziona lo stesso provider di identità in un accesso successivo, l'autenticazione verrà eseguita nuovamente senza immettere le credenziali. Se un utente vuole disconnettersi dall'applicazione Azure AD B2C, non significa necessariamente che intenda, ad esempio, disconnettersi del tutto dall'account Facebook. Tuttavia, per gli account locali, la sessione dell'utente viene terminata in modo corretto.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>Usare il tenant personale di Azure AD B2C
Per provare queste richieste, completare i tre passaggi seguenti. Sostituire i valori dell'esempio usato in questo articolo con valori personalizzati:

1. [Creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md). Usare il nome del tenant nelle richieste.
2. [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione e un valore `redirect_uri`. Includere un'API Web o un'app Web nell'app. Se lo si desidera, è possibile creare un segreto dell'applicazione.
3. [Creare i criteri](active-directory-b2c-reference-policies.md) per ottenere i nomi dei criteri.

## <a name="samples"></a>Esempi

* [Creare un'app a singola pagina con Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Creare un'app a singola pagina con .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

