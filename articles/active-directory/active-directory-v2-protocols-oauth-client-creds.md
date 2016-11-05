
---
title: Flusso di credenziali client OAuth 2.0 di Azure AD | Microsoft Docs
description: Creazione di applicazioni Web usando l'implementazione del protocollo di autenticazione OAuth 2.0 definita in Azure AD.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: dastrock

---
# <a name="v2.0-protocols---oauth-2.0-client-credentials-flow"></a>Protocolli 2.0 - Flusso di credenziali client di OAuth 2.0
La [concessione di credenziali client OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4), anche detta "OAuth a due vie", può essere usata per accedere alle risorse ospitate sul Web tramite l'identità di un'applicazione.  Viene comunemente impiegata per le interazioni di tutti i server in esecuzione in background senza la presenza immediata di un utente finale.  Questo tipo di applicazioni vengono spesso definite **daemon** o **account di servizio**.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint 2.0.  Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
> 
> 

Nel più comune "OAuth a tre vie," a un'applicazione client viene concessa l'autorizzazione per accedere a una risorsa per conto di un determinato utente.  L'autorizzazione è **delegata** dall'utente all'applicazione, in genere, durante il processo di [concessione](active-directory-v2-scopes.md) .  Tuttavia, nel flusso di credenziali client, le autorizzazioni vengono concesse **direttamente** all'applicazione stessa.  Quando l'applicazione presenta un token a una risorsa, la risorsa obbliga l'applicazione stessa ad avere l'autorizzazione per eseguire alcune azioni e non l'utente.

## <a name="protocol-diagram"></a>Diagramma di protocollo
L'intero flusso di credenziali client è simile al seguente: i singoli passaggi sono descritti in dettaglio di seguito.

![Flusso di credenziali client](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Ottenere l'autorizzazione diretta
Esistono due metodi tramite i quali l'applicazione riceve generalmente autorizzazione diretta per l'accesso alla risorsa: tramite un elenco di controllo di accesso alla risorsa o per mezzo dell'assegnazione dell'autorizzazione a un'applicazione in Azure AD.  Esistono diversi altri metodi per l'autorizzazione dei client da parte della risorsa e ogni server della risorsa può scegliere il metodo più appropriato per l'applicazione.  Questi due metodi sono i più comuni in Azure AD e sono consigliati per i client e le risorse che desiderano eseguire il flusso di credenziali client.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso
Un determinato provider di risorse potrebbe imporre un controllo delle autorizzazioni in base a un elenco di ID applicazione che riconosce e concedere uno specifico livello di accesso.  Quando la risorsa riceve un token dall'endpoint 2.0, può decodificare il token ed estrarre l'ID dell'applicazione client dalle attestazioni `appid` e `iss`.  A questo punto è in grado di confrontare l'applicazione con alcuni elenchi di controllo di accesso (ACL) gestiti.  La granularità e il metodo dell'elenco di controllo di accesso può variare notevolmente tra le risorse.

Per tali ACL, un caso d'uso comune sono i test runner per un'applicazione o API Web.  L'API Web può concedere ai diversi clienti solo un sottoinsieme delle autorizzazioni complete.  Per eseguire test end-to-end sull'API, viene creato un client di prova che acquisisce i token dall'endpoint 2.0 e li invia all'API.  A quel punto, l'API applicare gli elenchi di controllo di accesso all'ID dell'applicazione del client di prova per concedere accesso completo alle funzionalità dell'API.  Si noti che se si dispone di tale elenco nel servizio, è necessario convalidare non solo l'`appid` del chiamante, ma verificare anche che l'`iss` del token sia considerato attendibile.

Questo tipo di autorizzazione è comune per gli account daemon e di servizio che devono accedere ai dati di proprietà di utenti che dispongono di account Microsoft personale.  Per i dati appartenenti a organizzazioni, è consigliabile acquisire l'autorizzazione necessaria tramite le autorizzazioni dell'applicazione.

### <a name="application-permissions"></a>Autorizzazioni dell'applicazione
Invece di usare gli elenchi di controllo di accesso, le API possono esporre un set di **autorizzazioni dell'applicazione** che possono essere concesse a un'applicazione.  Un'autorizzazione dell'applicazione viene concessa a un'applicazione da un amministratore dell'organizzazione e può essere usata solo per accedere ai dati di proprietà dell'organizzazione e dei propri dipendenti.  Ad esempio, Microsoft Graph espone diverse autorizzazioni dell'applicazione:

* Lettura di messaggi di posta elettronica in tutte le caselle di posta elettronica
* Lettura e scrittura di messaggi di posta elettronica in tutte le caselle di posta elettronica
* Invio di posta elettronica come utente
* Lettura dei dati della directory
* [Più informazioni](https://graph.microsoft.io)

Per acquisire queste autorizzazioni nell'applicazione, attenersi alla procedura seguente.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione dell'app
* Passare all'applicazione in [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o [creare un'app](active-directory-v2-app-registration.md) se ancora non è stata creata.  Verificare che l'applicazione abbia creato almeno un segreto dell'applicazione.
* Individuare la sezione **Autorizzazioni applicazione dirette** e aggiungere le autorizzazioni richieste dall'applicazione.
* **Salvare** la registrazione dell'app.

#### <a name="recommended:-sign-the-user-into-your-app"></a>Consigliato: disconnettere l'utente dall'applicazione
In genere durante la compilazione di un'applicazione che usa autorizzazioni dell'applicazione, l'app disporrà di una pagina/vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione.  Questa pagina può essere parte del flusso di iscrizione all'app, delle impostazioni dell'applicazione o di un flusso di "connessione" dedicato.  In molti casi, è utile per l'applicazione visualizzare la pagina di "connessione" solo dopo che un utente ha eseguito l'accesso con un account di lavoro o dell'istituto di istruzione Microsoft.

L'accesso dell'utente nell'app consente di identificarne l'organizzazione di appartenenza prima di richiedere l'approvazione delle autorizzazioni dell'applicazione.  Sebbene non sia strettamente necessario, questo consente di creare un'esperienza più intuitiva per gli utenti dell'organizzazione.  Per l'accesso utente, seguire le [esercitazioni sui protocolli 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory
Quando si è pronti per richiedere le autorizzazioni dall'amministratore dell'azienda, è possibile reindirizzare l'utente sull' **endpoint di consenso dell'amministratore**2.0.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametro |  | Descrizione |
| --- | --- | --- |
| tenant |Obbligatoria |Il tenant della directory da cui si desidera richiedere autorizzazioni.  Può essere fornito nel formato di nome descrittivo o guid.  Se non si conosce il tenant di appartenenza dell'utente e si desidera consentire l'accesso con qualsiasi tentant, usare `common`. |
| client_id |Obbligatoria |ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| redirect_uri |Obbligatoria |URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app.  Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL e disporre di segmenti di percorso aggiuntivi. |
| state |Consigliato |Valore incluso nella richiesta che verrà restituito anche nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto.  Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |

A questo punto, Azure AD imporrà che solo un amministratore tenant possa accedere per completare la richiesta.  L'amministratore dovrà approvare tutte le autorizzazioni dirette dell'applicazione richieste per l'app nel portale di registrazione. 

##### <a name="successful-response"></a>Risposta con esito positivo
Se l'amministratore approva le autorizzazioni per l'applicazione, la risposta con esito positivo sarà:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametro | Descrizione |
| --- | --- | --- |
| tenant |Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato guid. |
| state |Valore incluso nella richiesta che verrà restituito anche nella risposta del token.  Può trattarsi di una stringa di qualsiasi contenuto.  Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| admin_consent |Sarà impostato su `True`. |

##### <a name="error-response"></a>Risposta di errore
Se l'amministratore non approva le autorizzazioni per l'applicazione, la risposta di errore sarà:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | Descrizione |
| --- | --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore. |

Dopo aver ricevuto una risposta con esito positivo dall'endpoint di provisioning dell'app, l'applicazione ha ottenuto le autorizzazioni dirette dell'applicazione richieste.  È ora possibile passare alla richiesta di un token per la risorsa desiderata.

## <a name="get-a-token"></a>Acquisizione di un token
Dopo aver acquisito le autorizzazioni necessarie per l'applicazione, è possibile procedere con l'acquisizione dei token di accesso per le API.  Per ottenere un token con concessione delle credenziali client, inviare una richiesta POST all'endpoint `/token` 2.0:

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parametro |  | Descrizione |
| --- | --- | --- |
| client_id |Obbligatoria |ID applicazione che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) ha assegnato all'app. |
| scope |Obbligatoria |Il valore passato per il parametro `scope` nella richiesta deve essere l'identificatore della risorsa (URI ID App) della risorsa desiderata, con l'aggiunta del suffisso `.default`.  Per l'esempio di Microsoft Graph, il valore deve essere `https://graph.microsoft.com/.default`.  Questo valore indica all'endpoint 2.0 che, per tutte le autorizzazioni dirette dell'applicazione configurate per l'app, è necessario emettere un token per le autorizzazioni riguardanti la risorsa desiderata. |
| client_secret |Obbligatoria |Segreto dell'applicazione generato per l'app nel portale di registrazione. |
| grant_type |Obbligatoria |Deve essere `client_credentials`. |

#### <a name="successful-response"></a>Risposta con esito positivo
La risposta con esito positivo avrà il formato seguente:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametro | Descrizione |
| --- | --- |
| access_token |Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| token_type |Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è `Bearer`. |
| expires_in |Validità del token di accesso (espressa in secondi). |

#### <a name="error-response"></a>Risposta di errore
La risposta di errore avrà il formato seguente:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| error_codes |Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| timestamp |Ora in cui si è verificato l'errore. |
| trace_id |Identificatore univoco per la richiesta utile per la diagnostica. |
| correlation_id |Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |

## <a name="use-a-token"></a>Usare di un token
Ora che è stato acquisito un token, è possibile usarlo per inoltrare richieste alla risorsa.  Alla scadenza del token, è sufficiente ripetere la richiesta all'endpoint `/token` per ottwnere un token di accesso aggiornato.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Esempio di codice
Per un esempio di un'applicazione che implementa la concessione delle credenziali client tramite l'endpoint di consenso dell'amministratore, vedere l'[esempio di codice daemon 2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

<!--HONumber=Oct16_HO2-->


