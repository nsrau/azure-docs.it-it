---
title: Azure Active Directory B2C | Documentazione Microsoft
description: Come compilare direttamente app a singola pagina tramite il flusso implicito.
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: f2eb1f67c5687ac9bd8b94f6480617e6dc87ac9f
ms.openlocfilehash: ace35adc1cd2745ea11cb60429a1e7892d1f66d3


---
# <a name="azure-active-directory-b2c-sign-in-for-single-page-apps-using-oauth-20-implicit-flow"></a>Azure Active Directory B2C: accedere ad app a singola pagina tramite il flusso implicito OAuth 2.0

> [!NOTE]
> Questa funzionalità è ancora in anteprima.
> 

Molte app moderne hanno un front-end dell'app a singola pagina scritto principalmente in JavaScript. Il front-end viene scritto spesso usando un framework come AngularJS, Ember.js o Durandal. Le app a pagina singola e le altre app JavaScript che vengono eseguite soprattutto in un browser presentano alcune problematiche interessanti nell'ambito dell'autenticazione:

* Le caratteristiche di sicurezza di queste app sono considerevolmente diverse da quelle delle tradizionali applicazioni Web basate su server.
* Molti server di autorizzazione e provider di identità non supportano le richieste CORS.
* I reindirizzamenti dei browser a pagina intera dall'app diventano fastidiosi per l'esperienza utente.

Per supportare queste applicazioni, Azure AD B2C usa il flusso implicito OAuth 2.0.  Il flusso di concessione implicita OAuth 2.0 è descritto nella [sezione 4.2 della specifica di OAuth 2.0](http://tools.ietf.org/html/rfc6749).  In questo flusso l'app riceve i token direttamente dall'endpoint di autorizzazione di Azure AD, senza alcuno scambio tra server. In questo modo, tutta la logica di autenticazione e gestione della sessione avviene interamente nel client JavaScript, senza eseguire reindirizzamenti a pagine aggiuntive.

Azure AD B2C estende i flussi impliciti OAuth 2.0 standard e va oltre le semplici operazioni di autorizzazione e autenticazione. Introduce il [**parametro criteri**](active-directory-b2c-reference-policies.md) che consente di usare OAuth 2.0 per aggiungere esperienze utente all'app, ad esempio gestione dell'iscrizione, dell'accesso e del profilo. Di seguito viene illustrato come usare il flusso implicito e Azure AD per implementare ognuna di queste esperienze nelle applicazioni a singola pagina.  Per iniziare, vedere anche gli esempi relativi a [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) o [.NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi).

Le richieste HTTP di esempio seguenti usano la directory B2C di esempio, **fabrikamb2c.onmicrosoft.com**, nonché l'applicazione di esempio e i criteri. Si possono provare le richieste in totale autonomia usando questi valori oppure è possibile sostituirli con valori personalizzati.
Ulteriori informazioni su come [ottenere directory, applicazione e i criteri B2C](#use-your-own-b2c-tenant).

## <a name="protocol-diagram"></a>Diagramma di protocollo
L'intero flusso di accesso implicito è simile alla procedura descritta in dettaglio di seguito.

![Corsie di OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Invio di richieste di autenticazione
Quando l'app Web deve autenticare l'utente ed eseguire un criterio, indirizza l'utente all'endpoint `/authorize`. Questa è la parte interattiva del flusso, in cui l'utente esegue effettivamente delle operazioni, in base ai criteri, e ottiene un `id_token` dall'endpoint di Azure AD.

In questa richiesta il client indica le autorizzazioni che deve acquisire dall'utente nel parametro `scope` e i criteri da eseguire nel parametro `p`. Di seguito vengono forniti tre esempi (con interruzioni di riga per migliorare la leggibilità), ciascuno dei quali utilizza un criterio diverso. Per apprendere il funzionamento di ogni richiesta, provare a incollare la richiesta in un browser ed eseguirla.

#### <a name="use-a-sign-in-policy"></a>Uso di un criterio di accesso
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

#### <a name="use-a-sign-up-policy"></a>Uso di un criterio di iscrizione
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

#### <a name="use-an-edit-profile-policy"></a>Uso di un criterio di modifica del profilo
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

| Parametro |  | Descrizione |
| --- | --- | --- |
| client_id |Obbligatoria |ID applicazione assegnato all'app dal [portale di Azure](https://portal.azure.com/) . |
| response_type |Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect.  Può anche includere `token` come response_type. Usando qui il `token`, l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint.  Se si usa il `token` come response_type, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| redirect_uri |Consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| response_mode |Consigliato |Specifica il metodo da usare per restituire il token risultante all'app.  Deve essere `fragment` per il flusso implicito. |
| scope |Obbligatorio |Elenco di ambiti separati da spazi. Un valore per l'ambito indica ad Azure AD entrambe le autorizzazioni richieste. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di **token ID**. Altre informazioni sono roportate più avanti in questo articolo. L’ambito `offline_access` è facoltativo per le applicazioni Web. Indica che l'applicazione richiede un **refresh_token** per un accesso prolungato alle risorse. |
| state |Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina in cui si trovava. |
| nonce |Obbligatorio |Valore incluso nella richiesta, generata dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| p |Obbligatorio |Criteri che verranno eseguiti. Si tratta del nome di un criterio creato nel tenant B2C. Il valore del nome del criterio deve iniziare con "b2c\_1\_". Altre informazioni sui criteri sono disponibili nell'articolo relativo al [framework di criteri estendibile](active-directory-b2c-reference-policies.md). |
| prompt |Facoltativo |Tipo di interazione utente obbligatoria. L'unico valore valido in questa fase è 'login', che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non avrà effetto. |

A questo punto, viene richiesto all'utente di completare il flusso di lavoro del criterio. È possibile che venga richiesto all'utente di immettere nome utente e password, di accedere con un'identità di social networking, di iscriversi alla directory o qualsiasi altro passaggio, a seconda di come sono definiti i criteri.

Dopo che l'utente ha completato il criterio, Azure AD restituisce una risposta all'app nel `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`. La risposta corrisponde esattamente a ciascuno dei casi precedenti, indipendentemente dai criteri eseguiti.

#### <a name="successful-response"></a>Risposta con esito positivo
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
| --- | --- |
| access_token |Token di accesso richiesto dall'app.  Il token di accesso non deve essere decodificato o controllato in altro modo ma può essere trattato come una stringa opaca. |
| token_type |Valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. |
| expires_in |Periodo di validità del token di accesso (in secondi). |
| scope |Ambiti per i quali il token è valido. Possono essere usati per la memorizzazione dei token nella cache per usarli in seguito. |
| id_token |Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Informazioni dettagliate sui token ID e sul relativo contenuto sono incluse nelle [informazioni di riferimento sui token di Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| state |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |

#### <a name="error-response"></a>Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| . | Descrizione |
| --- | --- |
| error |Stringa di codice di errore usata per classificare i tipi di errore che si verificano. Il codice di errore può essere usato per la gestione degli errori. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| state |Vedere la descrizione completa nella tabella precedente. Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici.|

## <a name="validate-the-idtoken"></a>Convalidare il token ID
La semplice ricezione di un token ID non è sufficiente per autenticare l'utente. È necessario convalidare la firma del token ID e verificare le attestazioni nel token per i requisiti dell'app. Azure AD B2C usa i [token Web JSON](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

Sono disponibili molte librerie open source per la convalida dei token JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame tali opzioni anziché implementare una logica di convalida personalizzata. Le presenti informazioni sono utili per comprendere come usare correttamente queste librerie.

Azure AD B2C dispone di un endpoint di metadati OpenID Connect, che consente a un'applicazione di recuperare informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant B2C include un documento di metadati JSON per ogni criterio. Il documento di metadati del criterio `b2c_1_sign_in` in `fabrikamb2c.onmicrosoft.com` si trova ad esempio in:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una delle proprietà del documento di configurazione è `jwks_uri`, il cui valore per lo stesso criterio è:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Per determinare quale criterio è stato utilizzato per la firma di un id_token (e la posizione dove recuperare i metadati), sono disponibili due opzioni. In primo luogo, il nome del criterio è incluso nell’attestazione `acr` dell’id_token. Per informazioni su come analizzare le attestazioni da un id_token, consultare le [informazioni di riferimento sul token Azure AD B2C](active-directory-b2c-reference-tokens.md). L'altra opzione consiste nel codificare i criteri nel valore del parametro `state` quando si rilascia la richiesta, per poi decodificarlo e determinare quali criteri sono stati utilizzati. Entrambi i metodi sono perfettamente validi.

Dopo aver acquisito il documento dei metadati dall'endpoint di metadati OpenID Connect, è possibile usare le chiavi pubbliche RSA 256 che si trovano in questo endpoint per convalidare la firma del token ID. Possono essere presenti più chiavi elencate in questo endpoint , ognuna identificata da `kid`. L'intestazione del token ID contiene inoltre un'attestazione `kid`, che indica quali di queste chiavi sono state usate per firmare il token ID. Per altre informazioni, inclusa la [convalida del token](active-directory-b2c-reference-tokens.md#token-validation), vedere il [riferimento al token di Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Dopo la convalida della firma del token ID è necessario verificare anche diverse altre attestazioni, ad esempio:

* Convalidare l'attestazione `nonce` per impedire attacchi di riproduzione dei token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
* Convalidare l'attestazione `aud` per assicurarsi che l'id_token sia stato rilasciato per l'app. Il valore deve essere l'ID applicazione dell'app.
* Convalidare le attestazioni `iat` e `exp` per assicurarsi che l'id_token non sia scaduto.

Esistono anche diverse altre convalide che è opportuno eseguire, descritte in dettaglio nella [specifica di OpenID Connect Core](http://openid.net/specs/openid-connect-core-1_0.html).  È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

* Verificare che l'utente o l'organizzazione abbiano eseguito l'iscrizione all'app.
* Verificare che l'utente abbia le autorizzazioni o i privilegi adeguati.
* Verificare che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori di Azure.

Per altri dettagli sulle attestazioni in un id_token, vedere le [informazioni di riferimento sul token di Azure AD B2C](active-directory-b2c-reference-tokens.md).

Dopo aver completato la convalida del token ID, è possibile avviare una sessione con l'utente e usare le attestazioni nel token ID per ottenere informazioni sull'utente nell'app. Queste informazioni possono essere usate per la visualizzazione, i record, le autorizzazioni e così via.

## <a name="get-access-tokens"></a>Ottenere i token di accesso
Se l'app Web deve solo eseguire criteri, è possibile ignorare le prossime sezioni. Queste sezioni sono applicabili solo alle app Web che devono eseguire chiamate autenticate a un'API Web e che sono protette da Azure AD B2C.

Dopo l'accesso dell'utente all'app a singola pagina, è possibile ottenere i token di accesso per chiamare le API Web protette da Azure AD.  Anche se si è già ricevuto un token usando il `token` response_type, è possibile usare questo metodo per acquisire i token per risorse aggiuntive senza dover reindirizzare l'utente perché esegua di nuovo l'accesso.

Nel normale flusso delle app Web, per eseguire questa operazione si invia una richiesta all'endpoint di `/token`.  L'endpoint non supporta però le richieste CORS. Non è quindi possibile eseguire chiamate AJAX per ottenere e aggiornare i token.  È possibile usare invece il flusso implicito in un iframe nascosto per ottenere nuovi token per altre API Web:

```
// Line breaks for legibility only

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

| Parametro |  | Descrizione |
| --- | --- | --- |
| client_id |Obbligatoria |ID applicazione assegnato all'app dal [portale di Azure](https://portal.azure.com/) . |
| response_type |Obbligatoria |Deve includere `id_token` per l'accesso a OpenID Connect.  Può anche includere `token` come response_type. Usando qui il `token` , l'app può ricevere immediatamente un token di accesso dall'endpoint di autorizzazione senza dover inviare una seconda richiesta a tale endpoint.  Se si usa il `token` come response_type, il parametro `scope` deve contenere un ambito che indica la risorsa per cui emettere il token. |
| redirect_uri |consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app.  Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatoria |Elenco di ambiti separati da spazi.  Per ottenere i token, includere tutti gli ambiti necessari per la risorsa di interesse. |
| response_mode |consigliato |Specifica il metodo usato per restituire il token risultante all'app.  Può essere uno di `query`, `form_post` o `fragment`. |
| state |consigliato |Valore incluso nella richiesta che viene restituito nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto.  Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente.  Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| nonce |Obbligatoria |Valore incluso nella richiesta, generata dall'app, che viene incluso nell'id_token risultante come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token.  Il valore è in genere una stringa casuale univoca che identifica l'origine della richiesta. |
| prompt |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, usare `prompt=none` per fare in modo che iframe non si blocchi alla pagina di accesso e risponda immediatamente. |
| login_hint |Obbligatoria |Per aggiornare e ottenere i token in un iframe nascosto, è necessario includere il nome utente dell'utente in questo hint per distinguere tra le eventuali varie sessioni dell'utente in un determinato momento. È possibile estrarre il nome utente da un accesso precedente usando l'attestazione `preferred_username`. |
| domain_hint |Obbligatoria |Può essere uno di `consumers` o `organizations`.  Per aggiornare e ottenere i token in un iframe nascosto, è necessario includere domain_hint nella richiesta.  Estrarre l'attestazione `tid` dall'id_token di un accesso precedente per determinare il valore da usare.  Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`.  In caso contrario, usare `domain_hint=organizations`. |

Impostando il parametro `prompt=none`, la richiesta ha immediatamente esito positivo o negativo e torna all'applicazione.  Un risposta con esito positivo verrà inviata all'app all'indirizzo `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`.

#### <a name="successful-response"></a>Risposta con esito positivo
Una risposta con esito positivo che usa `response_mode=fragment` ha un aspetto simile al seguente:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| . | Descrizione |
| --- | --- |
| access_token |Token richiesto dall'app. |
| token_type |È sempre `Bearer`. |
| state |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori dello stato nella richiesta e nella risposta siano identici. |
| expires_in |Validità del token di accesso (espressa in secondi). |
| scope |Ambiti per i quali il token di accesso è valido. |

#### <a name="error-response"></a>Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente.  Nel caso di `prompt=none`, un errore previsto sarà:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

Se si riceve questo errore nella richiesta iframe, l'utente deve accedere di nuovo in modo interattivo per recuperare un nuovo token.  È possibile scegliere di gestire questa situazione in qualsiasi modo appropriato per l'applicazione.

## <a name="refreshing-tokens"></a>Aggiornare i token
I token `id_token` e `access_token` hanno entrambi una scadenza a breve termine. È quindi consigliabile preparare l'app in modo che questi token vengano aggiornati regolarmente.  Per aggiornare entrambi i tipi di token, eseguire la stessa richiesta nell'iframe nascosto precedente usando il parametro `prompt=none` per controllare il comportamento di Azure AD.  Per ricevere un nuovo `id_token`, assicurarsi di usare `response_type=id_token` e `scope=openid`, oltre al parametro `nonce`.

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione
Per disconnettere l'utente dall'app, reindirizzare l'utente alla disconnessione da Azure AD. In caso contrario, l'utente potrebbe essere in grado di autenticarsi nuovamente all'app senza immettere le credenziali, in quanto avrà accesso a una sessione Single Sign-On valida con Azure AD.

È sufficiente reindirizzare l'utente all'`end_session_endpoint` elencato nello stesso documento dei metadati di OpenID Connect descritto nella sezione precedente "Convalidare il token ID":

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
| p |Obbligatorio |Criteri da usare per disconnettere l'utente dall'applicazione. |
| post_logout_redirect_uri |Consigliato |URL di destinazione al quale l'utente deve essere reindirizzato dopo la disconnessione. Se omesso, l'utente visualizzerà un messaggio generico di Azure AD B2C. |

> [!NOTE]
> Anche se indirizzare l'utente a `end_session_endpoint` permette di cancellare parte dello stato Single Sign-On dell'utente con Azure AD B2C, l'utente non viene disconnesso dalla sessione del provider di identità basato su social network. Se l'utente seleziona lo stesso provider di identità in un accesso successivo, l'autenticazione verrà eseguita nuovamente senza immettere le credenziali. Se un utente vuole disconnettersi dall'applicazione B2C, non significa necessariamente che intenda disconnettersi del tutto dall'account Facebook. Tuttavia, in caso di account locali, la sessione dell'utente viene terminata in modo corretto.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Uso del proprio tenant B2C
Per provare queste richieste autonomamente, è innanzitutto necessario eseguire questi tre passaggi, quindi sostituire i valori di esempio con i propri:

* [Creare un tenant B2C](active-directory-b2c-get-started.md)e usare il nome del tenant nelle richieste.
* [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione e un URI di reindirizzamento. È possibile includere poi un'**app Web/API Web** nell'applicazione e, facoltativamente, creare una **chiave privata dell'applicazione**.
* [Creare i criteri](active-directory-b2c-reference-policies.md) per ottenere i nomi dei criteri.

## <a name="samples"></a>Esempi

* [Creare un'app a singola pagina con Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Creare un'app a singola pagina con .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)




<!--HONumber=Feb17_HO2-->


