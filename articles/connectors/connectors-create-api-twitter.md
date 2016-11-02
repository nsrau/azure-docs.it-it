<properties
	pageTitle="Informazioni su come usare il connettore Twitter nelle app per la logica | Microsoft Azure"
	description="Panoramica del connettore Twitter con i parametri dell'API REST"
	services=""
	documentationCenter="" 
	authors="msftman"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="deonhe"/>


# Introduzione al connettore Twitter

Con il connettore Twitter è possibile:

- Pubblicare e recuperare tweet
- Accedere a timeline, amici e follower
- Eseguire le altre azioni e trigger descritti di seguito

Per usare [qualsiasi connettore](./apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi a Twitter

Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](./connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.

### Creare una connessione a Twitter

>[AZURE.INCLUDE [Passaggi per creare una connessione a Twitter](../../includes/connectors-create-api-twitter.md)]

## Usare un trigger di Twitter

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Questo esempio illustrerà come usare il trigger **Quando viene pubblicato un nuovo tweet** per cercare #Seattle e, se viene individuato, aggiornare un file in Dropbox con il testo del tweet. In un esempio riguardante un'organizzazione si potrebbe cercare il nome dell'azienda e aggiornare un database SQL con il testo del tweet.

1. Immettere *twitter* nella casella di ricerca nella finestra di progettazione delle app per la logica e selezionare il trigger **Twitter - When a new tweet is posted** (Twitter - Quando viene inserito un nuovo tweet)  
![Immagine di trigger Twitter 1](./media/connectors-create-api-twitter/trigger-1.png)  
- Immettere *#Seattle* nel controllo **Testo di ricerca**  
![Immagine di trigger Twitter 2](./media/connectors-create-api-twitter/trigger-2.png)  

A questo punto, l’app per la logica è stata configurata con un trigger che avvierà l'esecuzione di altri trigger e altre azioni nel flusso di lavoro.

>[AZURE.NOTE]Affinché sia funzionale, l’app per la logica deve contenere almeno un trigger e un’azione. Seguire i passaggi nella sezione successiva per aggiungere un'azione.

## Aggiungere una condizione
Poiché si è interessati esclusivamente ai tweet degli utenti con più di 50 follower, è necessario prima di tutto aggiungere all'app per la logica una condizione che conferma il numero di follower.

1. Selezionare **+ Nuovo passaggio** per aggiungere l'azione che si vuole venga eseguita quando viene individuato #Seattle in un nuovo tweet  
![Immagine di azione Twitter 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
- Selezionare il collegamento **Aggiungi una condizione**.  
![Immagine di condizione Twitter 1](../../includes/media/connectors-create-api-twitter/condition-1.png)  
Si apre il controllo **Condizione** in cui è possibile controllare condizioni come *è uguale a*, *è minore di*, *è maggiore di*, *contiene* e altre.  
![Immagine di condizione Twitter 2](../../includes/media/connectors-create-api-twitter/condition-2.png)  
- Selezionare il controllo **Scegli un valore**. In questo controllo è possibile selezionare una o più proprietà di azioni o trigger precedenti come valori la cui condizione restituirà true o false.  
![Immagine di condizione Twitter 3](../../includes/media/connectors-create-api-twitter/condition-3.png)  
- Selezionare **...** per espandere l'elenco delle proprietà in modo da vedere tutte le proprietà disponibili.  
![Immagine di condizione Twitter 4](../../includes/media/connectors-create-api-twitter/condition-4.png)  
- Selezionare la proprietà **Followers count**.  
![Immagine di condizione Twitter 5](../../includes/media/connectors-create-api-twitter/condition-5.png)  
- Si noti che la proprietà Followers count si trova ora nel controllo del valore.  
![Immagine di condizione Twitter 6](../../includes/media/connectors-create-api-twitter/condition-6.png)  
- Selezionare **è maggiore di** nell'elenco degli operatori.  
![Immagine di condizione Twitter 7](../../includes/media/connectors-create-api-twitter/condition-7.png)  
- Immettere 50 come operando per l'operatore *è maggiore di*. La condizione viene aggiunta. Salvare il lavoro mediante il collegamento **Salva** nel menu sopra.  
![Immagine di condizione Twitter 8](../../includes/media/connectors-create-api-twitter/condition-8.png)  

## Usare un'azione di Twitter

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Dopo aver aggiunto un trigger, seguire questi passaggi per aggiungere un'azione che pubblicherà un nuovo tweet con il contenuto dei tweet individuati dal trigger. Ai fini di questa procedura dettagliata verranno pubblicati solo i tweet degli utenti con più di 50 follower.

Nel passaggio successivo si aggiungerà un'azione di Twitter che pubblicherà un tweet usando alcune delle proprietà di ciascun tweet che è stato pubblicato da un utente che ha più di 50 follower.

1. Selezionare **Aggiungi un'azione**. Sia apre il controllo di ricerca in cui è possibile cercare altre azioni e trigger.  
![Immagine di condizione Twitter 9](../../includes/media/connectors-create-api-twitter/condition-9.png)  
- Immettere *twitter* nella casella di ricerca e selezionare l'azione **Twitter - Post a tweet** (Twitter - Pubblica un tweet). Si apre il controllo **Post a tweet** (Pubblica un tweet) in cui si inseriranno tutti i dettagli del tweet da pubblicare.  
![Immagine di azione Twitter 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)  
- Selezionare il controllo **Tweet text** (Testo del tweet). Ora sono visibili tutti gli output di azioni e trigger precedenti nell'app per la logica. È possibile selezionarli e usarli come parte del testo del nuovo tweet.  
![Immagine di azione Twitter 2](../../includes/media/connectors-create-api-twitter/action-2.png)  
- Selezionare **Nome utente**
- Immettere *dice:* nel controllo di testo del tweet. Eseguire questa operazione subito dopo il nome utente.
- Selezionare *Tweet text* (Testo del tweet).  
![Immagine di azione Twitter 3](../../includes/media/connectors-create-api-twitter/action-3.png)  
- Salvare il lavoro e inviare un tweet con l'hashtag #Seattle per attivare il flusso di lavoro.

## Dettagli tecnici

Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## Trigger di Twitter

Il connettore Twitter supporta i trigger seguenti:

|Trigger | Descrizione|
|--- | ---|
|[Quando viene visualizzato un nuovo tweet](connectors-create-api-twitter.md#when-a-new-tweet-is-posted)|Questa operazione attiva un flusso quando viene pubblicato un nuovo tweet che corrisponde a una query di ricerca specificata.|


## Azioni di Twitter

Il connettore Twitter supporta le azioni seguenti:


|Azione|Descrizione|
|--- | ---|
|[Recupera sequenza temporale utente](connectors-create-api-twitter.md#get-user-timeline)|Questa operazione recupera un elenco dei tweet più recenti pubblicati da un determinato utente.|
|[Recupera la sequenza temporale principale](connectors-create-api-twitter.md#get-home-timeline)|Questa operazione recupera i tweet e i re-tweet più recenti pubblicati dall'utente e dai follower.|
|[Cercare tweet](connectors-create-api-twitter.md#search-tweets)|Questa operazione recupera un elenco dei tweet pertinenti che soddisfano la query di ricerca.|
|[Recupera follower](connectors-create-api-twitter.md#get-followers)|Questa operazione recupera l'elenco degli utenti che seguono un determinato utente.|
|[Recupera follower personali](connectors-create-api-twitter.md#get-my-followers)|Questa operazione recupera l'elenco degli utenti che sono follower.|
|[Recupera following](connectors-create-api-twitter.md#get-following)|L'operazione recupera l'elenco delle persone che l'utente specificato segue.|
|[Recupera following personali](connectors-create-api-twitter.md#get-my-following)|Questa operazione recupera l'elenco degli utenti seguiti.|
|[Recupera utente](connectors-create-api-twitter.md#get-user)|Questa operazione recupera i dettagli del profilo per un determinato utente, come nome utente, descrizione, conteggio dei follower e altro ancora.|
|[Pubblica un tweet](connectors-create-api-twitter.md#post-a-tweet)|Questa operazione pubblica un nuovo tweet.|
## Informazioni dettagliate sulle azioni

Ecco i dettagli per le azioni e i trigger per questo connettore con le relative risposte:



### Recupera sequenza temporale utente
Questa operazione recupera un elenco dei tweet più recenti pubblicati da un determinato utente.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|userName*|Nome utente|Handle Twitter per l'utente|
|maxResults|Numero massimo di risultati|Numero massimo di tweet da restituire|

* indica che la proprietà è obbligatoria



#### Dettagli output

TweetModel: rappresentazione dell'oggetto tweet


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|TweetText|string|Contenuto di testo del tweet|
|TweetId|string|ID del tweet|
|CreatedAt|string|Ora di pubblicazione del tweet|
|RetweetCount|integer|Numero totale di re-tweet per il tweet|
|TweetedBy|string|Nome dell'utente che ha pubblicato il tweet|
|MediaUrls|array|URL del contenuto multimediale pubblicato insieme al tweet|
|TweetLanguageCode|string|Codice lingua del tweet|
|TweetInReplyToUserId|string|ID utente dell'autore del tweet di cui il tweet corrente è una risposta|
|Favorited|boolean|Indica se il tweet è contrassegnato come favorito o meno|
|UserMentions|array|Elenco degli utenti menzionati nel tweet|
|OriginalTweet|non definito|Tweet originale di cui il tweet corrente è una ripetizione|
|UserDetails|non definito|Dettagli dell'utente che ha pubblicato il tweet|




### Recupera la sequenza temporale principale
Questa operazione recupera i tweet e i re-tweet più recenti pubblicati dall'utente e dai follower.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|maxResults|Numero massimo di risultati|Numero massimo di tweet da restituire|

* indica che la proprietà è obbligatoria



#### Dettagli output

TweetModel: rappresentazione dell'oggetto tweet


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|TweetText|string|Contenuto di testo del tweet|
|TweetId|string|ID del tweet|
|CreatedAt|string|Ora di pubblicazione del tweet|
|RetweetCount|integer|Numero totale di re-tweet per il tweet|
|TweetedBy|string|Nome dell'utente che ha pubblicato il tweet|
|MediaUrls|array|URL del contenuto multimediale pubblicato insieme al tweet|
|TweetLanguageCode|string|Codice lingua del tweet|
|TweetInReplyToUserId|string|ID utente dell'autore del tweet di cui il tweet corrente è una risposta|
|Favorited|boolean|Indica se il tweet è contrassegnato come favorito o meno|
|UserMentions|array|Elenco degli utenti menzionati nel tweet|
|OriginalTweet|non definito|Tweet originale di cui il tweet corrente è una ripetizione|
|UserDetails|non definito|Dettagli dell'utente che ha pubblicato il tweet|




### Cercare tweet
Questa operazione recupera un elenco dei tweet pertinenti che soddisfano la query di ricerca.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|searchQuery*|Testo di ricerca|Termine di ricerca come "happy hour", #haiku, amore O odio|
|maxResults|Numero massimo di risultati|Numero massimo di tweet da restituire|

* indica che la proprietà è obbligatoria



#### Dettagli output

TweetModel: rappresentazione dell'oggetto tweet


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|TweetText|string|Contenuto di testo del tweet|
|TweetId|string|ID del tweet|
|CreatedAt|string|Ora di pubblicazione del tweet|
|RetweetCount|integer|Numero totale di re-tweet per il tweet|
|TweetedBy|string|Nome dell'utente che ha pubblicato il tweet|
|MediaUrls|array|URL del contenuto multimediale pubblicato insieme al tweet|
|TweetLanguageCode|string|Codice lingua del tweet|
|TweetInReplyToUserId|string|ID utente dell'autore del tweet di cui il tweet corrente è una risposta|
|Favorited|boolean|Indica se il tweet è contrassegnato come favorito o meno|
|UserMentions|array|Elenco degli utenti menzionati nel tweet|
|OriginalTweet|non definito|Tweet originale di cui il tweet corrente è una ripetizione|
|UserDetails|non definito|Dettagli dell'utente che ha pubblicato il tweet|




### Recupera follower
Questa operazione recupera l'elenco degli utenti che seguono un determinato utente.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|userName*|Nome utente|Handle Twitter per l'utente|
|maxResults|Numero massimo di risultati|Numero massimo di utenti da restituire|

* indica che la proprietà è obbligatoria



#### Dettagli output

UserDetailsModel: dettagli dell'utente di Twitter


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|FullName|string|Nome dell'utente|
|Percorso|string|Posizione dell'utente|
|ID|integer|ID Twitter dell'utente|
|UserName|string|Nome visualizzato dell'utente|
|FollowersCount|integer|Numero di follower|
|Descrizione|string|Descrizione dell'utente|
|StatusesCount|integer|Conteggio dello stato degli utenti|
|FriendsCount|integer|Numero di amici|
|FavouritesCount|integer|Numero di tweet che l'utente ha contassegnato come favoriti|
|ProfileImageUrl|string|URL dell'immagine del profilo|




### Recupera follower personali
Questa operazione recupera l'elenco degli utenti che sono follower.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|maxResults|Numero massimo di risultati|Numero massimo di utenti da restituire|

* indica che la proprietà è obbligatoria



#### Dettagli output

UserDetailsModel: dettagli dell'utente di Twitter


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|FullName|string|Nome dell'utente|
|Percorso|string|Posizione dell'utente|
|ID|integer|ID Twitter dell'utente|
|UserName|string|Nome visualizzato dell'utente|
|FollowersCount|integer|Numero di follower|
|Descrizione|string|Descrizione dell'utente|
|StatusesCount|integer|Conteggio dello stato degli utenti|
|FriendsCount|integer|Numero di amici|
|FavouritesCount|integer|Numero di tweet che l'utente ha contassegnato come favoriti|
|ProfileImageUrl|string|URL dell'immagine del profilo|




### Recupera following
L'operazione recupera l'elenco delle persone che l'utente specificato segue.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|userName*|Nome utente|Handle Twitter per l'utente|
|maxResults|Numero massimo di risultati|Numero massimo di utenti da restituire|

* indica che la proprietà è obbligatoria



#### Dettagli output

UserDetailsModel: dettagli dell'utente di Twitter


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|FullName|string|Nome dell'utente|
|Percorso|string|Posizione dell'utente|
|ID|integer|ID Twitter dell'utente|
|UserName|string|Nome visualizzato dell'utente|
|FollowersCount|integer|Numero di follower|
|Descrizione|string|Descrizione dell'utente|
|StatusesCount|integer|Conteggio dello stato degli utenti|
|FriendsCount|integer|Numero di amici|
|FavouritesCount|integer|Numero di tweet che l'utente ha contassegnato come favoriti|
|ProfileImageUrl|string|URL dell'immagine del profilo|




### Recupera following personali
Questa operazione recupera l'elenco degli utenti seguiti.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|maxResults|Numero massimo di risultati|Numero massimo di utenti da restituire|

* indica che la proprietà è obbligatoria



#### Dettagli output

UserDetailsModel: dettagli dell'utente di Twitter


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|FullName|string|Nome dell'utente|
|Percorso|string|Posizione dell'utente|
|ID|integer|ID Twitter dell'utente|
|UserName|string|Nome visualizzato dell'utente|
|FollowersCount|integer|Numero di follower|
|Descrizione|string|Descrizione dell'utente|
|StatusesCount|integer|Conteggio dello stato degli utenti|
|FriendsCount|integer|Numero di amici|
|FavouritesCount|integer|Numero di tweet che l'utente ha contassegnato come favoriti|
|ProfileImageUrl|string|URL dell'immagine del profilo|




### Recupera utente
Questa operazione recupera i dettagli del profilo per un determinato utente, come nome utente, descrizione, conteggio dei follower e altro ancora.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|userName*|Nome utente|Handle Twitter per l'utente|

* indica che la proprietà è obbligatoria

#### Dettagli output

UserDetailsModel: dettagli dell'utente di Twitter


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|FullName|string|Nome dell'utente|
|Percorso|string|Posizione dell'utente|
|ID|integer|ID Twitter dell'utente|
|UserName|string|Nome visualizzato dell'utente|
|FollowersCount|integer|Numero di follower|
|Descrizione|string|Descrizione dell'utente|
|StatusesCount|integer|Conteggio dello stato degli utenti|
|FriendsCount|integer|Numero di amici|
|FavouritesCount|integer|Numero di tweet che l'utente ha contassegnato come favoriti|
|ProfileImageUrl|string|URL dell'immagine del profilo|




### Pubblica un tweet
Questa operazione pubblica un nuovo tweet.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|tweetText|Testo del tweet|Testo da pubblicare|
|body|Contenuti multimediali|Contenuto multimediale da pubblicare|

* indica che la proprietà è obbligatoria

#### Dettagli output

TweetResponseModel: modello che rappresenta un tweet pubblicato


| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|TweetId|string|ID del tweet recuperato|




### Quando viene visualizzato un nuovo tweet
Questa operazione attiva un flusso quando viene pubblicato un nuovo tweet che corrisponde a una query di ricerca specificata.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|searchQuery*|Testo di ricerca|Termine di ricerca come "happy hour", #haiku, amore O odio|

* indica che la proprietà è obbligatoria

#### Dettagli output

TriggerBatchResponse[TweetModel]


| Nome proprietà | Tipo di dati |
|---|---|
|value|array|



## Risposte HTTP

Le azioni e i trigger riportati sopra possono restituire uno o più dei seguenti codici di stato HTTP:

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto.|
|default|Operazione non riuscita.|









## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->
