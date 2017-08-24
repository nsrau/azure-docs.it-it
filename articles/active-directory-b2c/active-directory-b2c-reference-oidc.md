---
title: 'Azure Active Directory B2C: accesso Web con OpenID Connect | Microsoft Docs'
description: Creazione di applicazioni Web tramite l'implementazione in Azure Active Directory del protocollo di autenticazione OpenID Connect
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 21d420c8-3c10-4319-b681-adf2e89e7ede
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: 8457865d21bbf4d1c0cc91167a1e75cd82ad8306
ms.contentlocale: it-it
ms.lasthandoff: 06/24/2017


---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: accesso Web con OpenID Connect
OpenID Connect è un protocollo di autenticazione basato su OAuth 2.0 che può essere usato per consentire agli utenti di accedere in modo sicuro alle applicazioni Web. Tramite l'implementazione in Azure Active Directory B2C (Azure AD B2C) di OpenID Connect è possibile assegnare esperienze di gestione delle iscrizioni, degli accessi e altre esperienze di gestione delle identità nelle applicazioni Web ad Azure AD. Questa guida illustra come eseguire questa operazione in modo indipendente dal linguaggio. La guida descrive come inviare e ricevere messaggi HTTP senza usare una delle librerie Microsoft open source.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 per consentirne l'uso come protocollo di *autenticazione*. Ciò consente di eseguire l'accesso Single Sign-On tramite OAuth. Introduce il concetto di *token ID*, che è un token di sicurezza che consente al client di verificare l'identità dell'utente e di ottenere informazioni di base sul profilo dell'utente.

Dal momento che è un'estensione di OAuth 2.0, consente anche alle app di acquisire in modo sicuro i *token di accesso*. I token di accesso possono essere usati per accedere alle risorse protette tramite un [server di autorizzazione](active-directory-b2c-reference-protocols.md#the-basics). Si consiglia OpenID Connect per la compilazione di un'applicazione Web ospitata su un server e accessibile da browser. Per aggiungere la gestione delle identità alle applicazioni desktop o per dispositivi mobili tramite Azure AD B2C, è consigliabile usare [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) invece di OpenID Connect.

Azure AD B2C estende il protocollo standard OpenID Connect per non limitarsi esclusivamente a semplici operazioni di autenticazione e autorizzazione. Introduce il [parametro criteri](active-directory-b2c-reference-policies.md), che consente di usare OpenID Connect per aggiungere esperienze utente all'app, ad esempio la gestione delle iscrizioni, degli accessi e dei profili. Di seguito viene illustrato come usare OpenID Connect e i criteri per implementare ognuna di queste esperienze nelle applicazioni Web. Viene anche illustrato come ottenere i token di accesso per accedere alle API Web.

Le richieste HTTP di esempio nella sezione seguente usano la directory B2C di esempio, fabrikamb2c.onmicrosoft.com, nonché l'applicazione di esempio, https://aadb2cplayground.azurewebsites.net, e i criteri. Si possono provare le richieste in totale autonomia usando questi valori oppure è possibile sostituirli con valori personalizzati.
Altre informazioni su come [ottenere un tenant, un'applicazione e criteri B2C](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Invio di richieste di autenticazione
Quando l'app Web deve autenticare l'utente ed eseguire un criterio, può indirizzarlo all'endpoint `/authorize` . Questa è la parte interattiva del flusso, dove l'utente esegue operazioni in base ai criteri.

In questa richiesta il client indica le autorizzazioni che deve acquisire dall'utente nel parametro `scope` e i criteri da eseguire nel parametro `p`. Di seguito vengono illustrati tre esempi (con interruzioni di riga per migliorare la leggibilità), ognuno dei quali usa criteri diversi. Per apprendere il funzionamento di ogni richiesta, provare a incollare la richiesta in un browser ed eseguirla.

#### <a name="use-a-sign-in-policy"></a>Uso di un criterio di accesso
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Uso di un criterio di iscrizione
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Uso di un criterio di modifica del profilo
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
| client_id |Obbligatoria |ID applicazione assegnato all'app dal [portale di Azure](https://portal.azure.com/) . |
| response_type |Obbligatorio |Tipo di risposta, che deve includere un token ID per OpenID Connect. Se l'app Web necessita anche di token per chiamare un'API Web, è possibile usare `code+id_token`, come illustrato qui. |
| redirect_uri |Consigliato |Parametro `redirect_uri` dell'app, dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno dei parametri `redirect_uri` registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. |
| scope |Obbligatorio |Elenco di ambiti separati da spazi. Un valore per l'ambito singolo indica ad Azure AD entrambe le autorizzazioni richieste. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. Altre informazioni sono riportate più avanti in questo articolo. L’ambito `offline_access` è facoltativo per le applicazioni Web. Indica che l'app richiede un *token di aggiornamento* per un accesso di lunga durata alle risorse. |
| response_mode |Consigliato |Metodo da usare per inviare all'app il codice di autorizzazione risultante. Può essere `query`, `form_post` o `fragment`.  `form_post` è la modalità di risposta consigliata perché offre la sicurezza migliore. |
| state |Consigliato |Valore incluso nella richiesta che viene restituito nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina in cui si trovava. |
| nonce |Obbligatorio |Valore incluso nella richiesta, generato dall'app, che verrà incluso nel token ID risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| p |Obbligatorio |Criteri che verranno eseguiti. Si tratta del nome di un criterio creato nel tenant B2C. Il valore del nome dei criteri deve iniziare con `b2c\_1\_`. Altre informazioni sui criteri sono disponibili nell'articolo relativo al [framework di criteri estendibile](active-directory-b2c-reference-policies.md). |
| prompt |Facoltativo |Tipo di interazione utente obbligatoria. L'unico valore valido in questa fase è `login`, che impone all'utente di immettere le credenziali per la richiesta. L'accesso Single Sign-On non avrà effetto. |

Viene a questo punto richiesto all'utente di completare il flusso di lavoro dei criteri. È possibile che venga richiesto all'utente di immettere nome utente e password, di accedere con un'identità di social networking, di iscriversi alla directory o di seguire altre procedure, a seconda di come sono definiti i criteri.

Dopo che l'utente ha completato i criteri, Azure AD restituisce una risposta all'app nel parametro `redirect_uri` indicato, usando il metodo specificato nel parametro `response_mode`. La risposta è la stessa per ognuno dei casi precedenti, indipendentemente dai criteri eseguiti.

Una risposta con esito positivo che utilizza `response_mode=fragment` ha un aspetto simile al seguente:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| --- | --- |
| id_token |Token ID richiesto dall'app. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. Altre informazioni dettagliate sui token ID e sul relativo contenuto sono incluse nelle [informazioni di riferimento sui token di Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| code |Codice di autorizzazione richiesto dall'app, se è stato usato `response_type=code+id_token`. L'app può usare il codice di autorizzazione per richiedere un token di accesso per una risorsa di destinazione. I codici di autorizzazione hanno una durata molto breve. In genere scadono dopo circa 10 minuti. |
| state |Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |

È possibile inviare anche le risposte di errore al parametro `redirect_uri`. L'app può così gestirle adeguatamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e per generare una reazione. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| state |Vedere la descrizione completa nella prima tabella di questa sezione. Se un parametro `state` è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori `state` nella richiesta e nella risposta siano identici. |

## <a name="validate-the-id-token"></a>Convalidare il token ID
La ricezione di un token ID non è sufficiente per autenticare l'utente. È necessario convalidare la firma del token ID e verificare se le attestazioni nel token soddisfano i requisiti dell'app. Azure AD B2C usa i [token Web JSON](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità.

Sono disponibili molte librerie open source per la convalida dei token JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame tali opzioni anziché implementare una logica di convalida personalizzata. Le presenti informazioni sono utili per comprendere come usare correttamente queste librerie.

Azure AD B2C dispone di un endpoint di metadati OpenID Connect, che consente a un'applicazione di recuperare informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant B2C include un documento di metadati JSON per ogni criterio. Il documento di metadati dei criteri `b2c_1_sign_in` in `fabrikamb2c.onmicrosoft.com` si trova ad esempio in:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una delle proprietà del documento di configurazione è `jwks_uri`, il cui valore per gli stessi criteri è:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Per determinare quali criteri sono stati usati per la firma di un token ID (e la posizione dove recuperare i metadati), sono disponibili due opzioni. Prima di tutto il nome dei criteri è incluso nell'attestazione `acr` del token ID. Per informazioni su come analizzare le attestazioni da un token ID, vedere le [informazioni di riferimento sul token Azure AD B2C](active-directory-b2c-reference-tokens.md). L'altra opzione consiste nel codificare i criteri nel valore del parametro `state` quando si rilascia la richiesta, per poi decodificarlo e determinare quali criteri sono stati utilizzati. Entrambi i metodi sono validi.

Dopo avere acquisito il documento di metadati dall'endpoint dei metadati OpenID Connect, è possibile usare le chiavi pubbliche RSA 256 che si trovano in questo endpoint per convalidare la firma del token ID. In un determinato punto nel tempo è possibile che siano presenti più chiavi elencate in questo endpoint, ognuna identificata da un'attestazione `kid`. L'intestazione del token ID contiene anche un'attestazione `kid`, che indica quali di queste chiavi sono state usate per firmare il token ID. Per altre informazioni, vedere le [informazioni di riferimento sul token di Azure AD B2C](active-directory-b2c-reference-tokens.md) (in particolare la sezione relativa alla [convalida dei token](active-directory-b2c-reference-tokens.md#token-validation)).
<!--TODO: Improve the information on this-->

Dopo avere convalidato la firma del token ID, è necessario verificare diverse attestazioni. Ad esempio:

* È necessario convalidare l'attestazione `nonce` per impedire attacchi di riproduzione del token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
* È consigliabile convalidare l'attestazione `aud` per assicurarsi che il token ID sia stato rilasciato per l'app. Il valore deve essere l'ID applicazione dell'app.
* È consigliabile convalidare le attestazioni `iat` e `exp` per assicurarsi che il token ID non sia scaduto.

Esistono numerose altre convalide che è consigliabile eseguire. Queste convalide sono descritte in dettaglio nella [Specifica di base di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).  È inoltre consigliabile convalidare attestazioni aggiuntive in base allo scenario. Alcune convalide comuni includono:

* Verificare che l'utente o l'organizzazione abbiano eseguito l'iscrizione all'app.
* Verificare che l'utente abbia le autorizzazioni o i privilegi adeguati.
* Verificare che sia stato applicato un determinato livello di autenticazione, ad esempio l'autenticazione a più fattori di Azure.

Per altri dettagli sulle attestazioni in un token ID, vedere le [informazioni di riferimento sul token di Azure AD B2C](active-directory-b2c-reference-tokens.md).

Dopo avere convalidato il token ID, è possibile avviare una sessione con l'utente. Usare le attestazioni del token ID per ottenere informazioni sull'utente nell'app. Gli usi di queste informazioni includono la visualizzazione, i record e l'autorizzazione.

## <a name="get-a-token"></a>Acquisizione di un token
Se l'app Web deve solo eseguire criteri, è possibile ignorare le prossime sezioni. Queste sezioni sono applicabili solo alle app Web che devono eseguire chiamate autenticate a un'API Web e che sono inoltre protette da Azure AD B2C.

È possibile riscattare il codice di autorizzazione acquisito, tramite `response_type=code+id_token`, per un token nella risorsa desiderata inviando una richiesta `POST` all'endpoint `/token`. L'unica risorsa per la quale al momento è possibile richiedere un token è l'API Web back-end dell'app. La convenzione per richiedere un token di questo tipo consiste nell'usare l'ID client dell'app come ambito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parametro | Obbligatorio? | Descrizione |
| --- | --- | --- |
| p |Obbligatorio |Il criterio utilizzato per acquisire il codice di autorizzazione. Non è possibile usare un criterio diverso in questa richiesta. Si noti che questo parametro viene aggiunto alla stringa di query, non al corpo di `POST`. |
| client_id |Obbligatoria |ID applicazione assegnato all'app dal [portale di Azure](https://portal.azure.com/) . |
| grant_type |Obbligatorio |Tipo di concessione, che deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| scope |Consigliato |Elenco di ambiti separati da spazi. Un valore per l'ambito singolo indica ad Azure AD entrambe le autorizzazioni richieste. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di parametri id_token. Può essere usato per ottenere i token nell'API Web back-end dell'app, che è rappresentata dallo stesso ID applicazione del client. L'ambito `offline_access` indica che l'app necessita di un token di aggiornamento per un accesso di lunga durata alle risorse. |
| code |Obbligatorio |Codice di autorizzazione acquisito nella prima sezione del flusso. |
| redirect_uri |Obbligatorio |Parametro `redirect_uri` dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| client_secret |Obbligatoria |La chiave privata dell’applicazione generata nel [Portale Azure](https://portal.azure.com/). La chiave privata dell'applicazione è un elemento di sicurezza importante. È necessario archiviarla in modo sicuro nel server. È necessario anche far ruotare periodicamente la chiave privata client. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | Descrizione |
| --- | --- |
| not_before |Il momento in cui il token viene considerato valido, nel periodo. |
| token_type |Valore del tipo di token. L'unico tipo supportato da Azure AD è `Bearer`. |
| access_token |Il token JWT firmato richiesto. |
| scope |Ambiti per il quale il token è valido. Possono essere usati per memorizzare i token nella cache per un uso successivo. |
| expires_in |Periodo di validità del token di accesso (in secondi). |
| refresh_token |Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento hanno una lunga durata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per altre informazioni dettagliate, vedere le [informazioni di riferimento sul token di B2C](active-directory-b2c-reference-tokens.md). Si noti che per ricevere un token di aggiornamento, occorre avere usato l'ambito `offline_access` nelle richieste di autorizzazione e nelle richieste di token. |

Le risposte di errore si presentano nel modo seguente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e per generare una reazione. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## <a name="use-the-token"></a>Uso del token
Dopo avere acquisito un token di accesso, è possibile usarlo nelle richieste alle API Web back-end includendolo nell'intestazione `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aggiornamento del token
I token ID hanno breve durata. È necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. A tale scopo, inviare un'altra richiesta `POST` all'endpoint `/token`, Specificare questa volta il parametro `refresh_token` al posto del parametro `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametro | Obbligatorio | Descrizione |
| --- | --- | --- |
| p |Obbligatorio |Criteri usati per acquisire il token di aggiornamento originale. Non è possibile usare un criterio diverso in questa richiesta. Si noti che questo parametro viene aggiunto alla stringa di query, non al corpo di POST. |
| client_id |Obbligatoria |ID applicazione assegnato all'app dal [portale di Azure](https://portal.azure.com/) . |
| grant_type |Obbligatorio |Tipo di concessione, che deve essere un token di aggiornamento per questa parte del flusso del codice di autorizzazione. |
| scope |Consigliato |Elenco di ambiti separati da spazi. Un valore per l'ambito singolo indica ad Azure AD entrambe le autorizzazioni richieste. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. Può essere usato per ottenere i token nell'API Web back-end dell'app, che è rappresentata dallo stesso ID applicazione del client. L'ambito `offline_access` indica che l'app necessita di un token di aggiornamento per un accesso di lunga durata alle risorse. |
| redirect_uri |Consigliato |Parametro `redirect_uri` dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| refresh_token |Obbligatorio |Token di aggiornamento originale acquisito nella seconda sezione del flusso. Si noti che per ricevere un token di aggiornamento, occorre avere usato l'ambito `offline_access` nelle richieste di autorizzazione e nelle richieste di token. |
| client_secret |Obbligatoria |La chiave privata dell’applicazione generata nel [Portale Azure](https://portal.azure.com/). La chiave privata dell'applicazione è un elemento di sicurezza importante. È necessario archiviarla in modo sicuro nel server. È necessario anche far ruotare periodicamente la chiave privata client. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | Descrizione |
| --- | --- |
| not_before |Il momento in cui il token viene considerato valido, nel periodo. |
| token_type |Valore del tipo di token. L'unico tipo supportato da Azure AD è `Bearer`. |
| access_token |Il token JWT firmato richiesto. |
| scope |Ambito per il quale il token è valido, che può essere usato per memorizzare i token nella cache per un uso successivo. |
| expires_in |Periodo di validità del token di accesso (in secondi). |
| refresh_token |Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente.  I token di aggiornamento hanno una lunga durata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate, consultare il [riferimento al token B2C](active-directory-b2c-reference-tokens.md). |

Le risposte di errore si presentano nel modo seguente:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e per generare una reazione. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione
Per la disconnessione di un utente dall'app, non è sufficiente cancellare i cookie dell'app o terminare la sessione dell'utente. È anche necessario reindirizzare l'utente ad Azure AD per la disconnessione. In caso contrario, l'utente potrebbe essere in grado di autenticarsi nuovamente all'app senza immettere le credenziali, in quanto avrà accesso a una sessione Single Sign-On valida con Azure AD.

È sufficiente reindirizzare l'utente all'endpoint `end_session` elencato nel documento dei metadati di OpenID Connect descritto precedentemente nella sezione "Convalidare il token ID":

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
> Sebbene l'indirizzamento dell'utente all'endpoint `end_session` comporti la cancellazione di una parte dello stato Single Sign-On dell'utente con Azure AD B2C, l'utente non verrà disconnesso dalla sessione del provider di identità social. Se l'utente seleziona lo stesso provider di identità in un accesso successivo, l'autenticazione verrà eseguita nuovamente senza immettere le credenziali. Se un utente vuole disconnettersi dall'applicazione B2C, non significa necessariamente che intenda disconnettersi dall'account Facebook. Tuttavia, in caso di account locali, la sessione dell'utente viene terminata in modo corretto.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Uso del proprio tenant B2C
Per provare queste richieste autonomamente, è prima di tutto necessario eseguire questi tre passaggi e quindi sostituire i valori di esempio descritti in precedenza con i valori di proprio interesse:

1. [Creare un tenant B2C](active-directory-b2c-get-started.md)e usare il nome del tenant nelle richieste.
2. [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione. Includere un'API Web/app Web nell'app. Se lo si desidera, creare un segreto dell'applicazione.
3. [Creare i criteri](active-directory-b2c-reference-policies.md) per ottenere i nomi dei criteri.


