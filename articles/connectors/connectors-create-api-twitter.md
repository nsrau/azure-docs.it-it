<properties
	pageTitle="Aggiungere il connettore Twitter in PowerApps Enterprise e nelle app per la logica | Microsoft Azure"
	description="Panoramica del connettore Twitter con i parametri dell'API REST"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/18/2016"
   ms.author="mandia"/>


# Introduzione al connettore Twitter
Connettersi a Twitter per pubblicare un tweet, recuperare la sequenza temporale dell'utente e così via. Il connettore Twitter può essere usato da:

- App per la logica 
- PowerApps

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/connectors-create-api-twitter.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-twitter.md)

&nbsp;

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Con Twitter è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Twitter. 
- Usare i trigger quando c'è un nuovo tweet.
- Usare le azioni per pubblicare un tweet, cercare tweet e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene visualizzato un nuovo tweet, è possibile pubblicarlo su Facebook.
- Aggiungere il connettore Twitter a PowerApps Enterprise, in modo che gli utenti possano usarlo nelle proprie app. 

Per informazioni su come aggiungere un connettore in PowerApps Enterprise,vedere la pagina relativa alla [registrazione dei connettori in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Trigger e azioni
Twitter include i trigger e le azioni seguenti.

Trigger | Azioni
--- | ---
<ul><li>When a new tweet appears</li></ul>| <ul><li>Post a new tweet</li><li>When a new tweet appears</li><li>Get home timeline</li><li>Get user</li><li>Get user timeline</li><li>Search tweet</li><li>Get followers</li><li>Get my followers</li><li>Get following</li><li>Get my following</li></ul>

Tutti i connettori supportano dati nei formati JSON e XML.


## Creare una connessione a Twitter

Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione all'account Twitter.

1. Accedere all'account Twitter.
2. Selezionare **Autorizza** e consentire alle app per la logica di connettersi e usare l'account Twitter. 

>[AZURE.INCLUDE [Passaggi per creare una connessione a Twitter](../../includes/connectors-create-api-twitter.md)]

>[AZURE.TIP] È possibile usare la stessa connessione di Twitter in altre app per la logica.


## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Pubblica un nuovo tweet 
Invia un tweet. ```POST: /posttweet```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tweetText|string|no|query|nessuno|Testo da pubblicare|
|body| string (binary) |no|body|nessuno|Contenuto multimediale da pubblicare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Quando viene visualizzato un nuovo tweet 
Attiva un flusso di lavoro quando viene pubblicato un nuovo tweet corrispondente alla query di ricerca. ```GET: /onnewtweet```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|searchQuery|string|yes|query|nessuno|Testo della query per cui si può usare qualsiasi operatore di query supportato da Twitter: http://www.twitter.com/search)|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Recupera la sequenza temporale principale 
Recupera i tweet e i re-tweet più recenti pubblicati dall'utente e dai follower. ```GET: /hometimeline```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|Numero massimo di tweet da recuperare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Recupera utente 
Recupera i dettagli relativi all'utente specificato, ad esempio nome utente, descrizione, numero di follower e così via. ```GET: /user```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|userName|string|yes|query|nessuno|Gestione dell'utente da parte di Twitter|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Recupera sequenza temporale utente 
Recupera una raccolta dei tweet più recenti pubblicati dall'utente specificato. ```GET: /usertimeline```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|userName|string|yes|query|nessuno|Gestione di Twitter|
|maxResults|integer|no|query|20|Numero massimo di tweet da recuperare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Cerca tweet 
Recupera una raccolta di tweet rilevanti corrispondenti a una query specificata. ```GET: /searchtweets```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|searchQuery|string|yes|query|nessuno|Testo della query per cui si può usare qualsiasi operatore di query supportato da Twitter: http://www.twitter.com/search)|
|maxResults|integer|no|query|20|Numero massimo di tweet da recuperare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Recupera follower 
Recupera gli utenti che seguono l'utente specificato. ```GET: /followers```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|userName|string|yes|query|nessuno|Gestione dell'utente da parte di Twitter|
|maxResults|integer|no|query|20|Numero massimo di utenti da recuperare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Recupera follower personali 
Recupera gli utenti che seguono l'utente principale. ```GET: /myfollowers```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|Numero massimo di utenti da recuperare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Recupera following 
Recupera gli utenti che l'utente specificato sta seguendo. ```GET: /friends```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|userName|string|yes|query|nessuno|Gestione dell'utente da parte di Twitter|
|maxResults|integer|no|query|20|Numero massimo di utenti da recuperare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


### Recupera following personali 
Recupera gli utenti seguiti dall'utente principale. ```GET: /myfriends```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|Numero massimo di utenti da recuperare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## Definizioni oggetto

### TweetModel: rappresentazione dell'oggetto tweet

| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---| --- | 
|TweetText|string|sì|
|TweetId|string|no|
|CreatedAt|string|no|
|RetweetCount|integer|sì|
|TweetedBy|string|sì|
|MediaUrls|array|no|

### UserDetailsModel: rappresentazione dei dettagli di un utente di Twitter

|Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|FullName|string|sì|
|Percorso|string|sì|
|ID|integer|no|
|UserName|string|sì|
|FollowersCount|integer|no|
|Descrizione|string|sì|
|StatusesCount|integer|no|
|FriendsCount|integer|no|

### TweetResponseModel: modello che rappresenta un tweet pubblicato

| Nome | Tipo di dati | Obbligatorio |
|---|---|---|
|TweetId|string|sì|

### TriggerBatchResponse[TweetModel]

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|value|array|no|


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all'[elenco di API](apis-list.md).

<!--References-->

[6]: ./media/connectors-create-api-twitter/twitter-apps-page.png
[7]: ./media/connectors-create-api-twitter/twitter-app-create.png

<!---HONumber=AcomDC_0525_2016-->