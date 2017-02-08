---
title: Informazioni su come usare il connettore Twitter nelle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Twitter con i parametri dell&quot;API REST
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 13ecb8936484b1c86938a16c7dd6da8000d4ffec


---
# <a name="get-started-with-the-twitter-connector"></a>Introduzione al connettore Twitter
Con il connettore Twitter è possibile:

* Pubblicare e recuperare tweet
* Accedere a timeline, amici e follower
* Eseguire le altre azioni e trigger descritti di seguito  

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).  

## <a name="connect-to-twitter"></a>Connettersi a Twitter
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.  

### <a name="create-a-connection-to-twitter"></a>Creare una connessione a Twitter
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Usare un trigger di Twitter
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Questo esempio illustra come usare il trigger **Quando viene pubblicato un nuovo tweet** per cercare #Seattle e, se viene individuato, aggiornare un file in Dropbox con il testo del tweet. In un esempio riguardante un'organizzazione si potrebbe cercare il nome dell'azienda e aggiornare un database SQL con il testo del tweet.

1. Immettere *twitter* nella casella di ricerca nella finestra di progettazione delle app per la logica e selezionare il trigger **Twitter - Quando viene pubblicato un nuovo tweet**   
   ![Immagine di trigger Twitter 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. Immettere *#Seattle* nel controllo **Testo di ricerca**  
   ![Immagine di trigger Twitter 2](./media/connectors-create-api-twitter/trigger-2.png) 

A questo punto, l'app per la logica è stata configurata con un trigger che avvierà l'esecuzione di altri trigger e altre azioni nel flusso di lavoro. 

> [!NOTE]
> Affinché sia funzionale, l’app per la logica deve contenere almeno un trigger e un’azione. Seguire i passaggi nella sezione successiva per aggiungere un'azione.  
> 
> 

## <a name="add-a-condition"></a>Aggiungere una condizione
Poiché si è interessati esclusivamente ai tweet degli utenti con più di 50 follower, è necessario prima di tutto aggiungere all'app per la logica una condizione che conferma il numero di follower.  

1. Selezionare **+ Nuovo passaggio** per aggiungere l'azione che deve essere eseguita quando viene individuato #Seattle in un nuovo tweet  
   ![Immagine di azione Twitter 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Selezionare il collegamento **Aggiungi una condizione**.  
   ![Immagine di condizione Twitter 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Si apre il controllo **Condizione** in cui è possibile controllare condizioni come *è uguale a*, *è minore di*, *è maggiore di*, *contiene* e altre.  
   ![Immagine di condizione Twitter 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Selezionare il controllo **Scegliere un valore**.  
   In questo controllo è possibile selezionare una o più proprietà di azioni o trigger precedenti come valori la cui condizione restituirà true o false.
   ![Immagine di condizione Twitter 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. Selezionare **...** per espandere l'elenco delle proprietà in modo da vedere tutte le proprietà disponibili.        
   ![Immagine di condizione Twitter 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. Selezionare la proprietà **Followers count**.    
   ![Immagine di condizione Twitter 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. Si noti che la proprietà Followers count si trova ora nel controllo del valore.    
   ![Immagine di condizione Twitter 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. Selezionare **è maggiore di** nell'elenco degli operatori.    
   ![Immagine di condizione Twitter 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. Immettere 50 come operando per l'operatore *è maggiore di*.  
   La condizione viene aggiunta. Salvare il lavoro usando il collegamento **Salva** nel menu precedente.    
   ![Immagine di condizione Twitter 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Usare un'azione di Twitter
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Dopo aver aggiunto un trigger, seguire questi passaggi per aggiungere un'azione che pubblicherà un nuovo tweet con il contenuto dei tweet individuati dal trigger. Ai fini di questa procedura dettagliata verranno pubblicati solo i tweet degli utenti con più di 50 follower.  

Nel passaggio successivo si aggiungerà un'azione di Twitter che pubblicherà un tweet usando alcune delle proprietà di ciascun tweet che è stato pubblicato da un utente che ha più di 50 follower.  

1. Selezionare **Aggiungi un'azione**. Viene aperto il controllo di ricerca in cui è possibile cercare altre azioni e trigger.  
   ![Immagine di condizione Twitter 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Immettere *twitter* nella casella di ricerca e selezionare l'azione **Twitter - Pubblica un tweet**. Si apre il controllo **Pubblica un tweet** in cui si potranno inserire tutti i dettagli del tweet da pubblicare.      
   ![Immagine di azione Twitter 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Selezionare il controllo **Testo del tweet**. Ora sono visibili tutti gli output di azioni e trigger precedenti nell'app per la logica. È possibile selezionarli e usarli come parte del testo del nuovo tweet.     
   ![Immagine di azione Twitter 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Selezionare **Nome utente**   
5. Immettere *dice:* nel controllo di testo del tweet. Eseguire questa operazione subito dopo il nome utente.  
6. Selezionare *Testo del tweet*.       
   ![Immagine di azione Twitter 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Salvare il lavoro e inviare un tweet con l'hashtag #Seattle per attivare il flusso di lavoro.  

## <a name="technical-details"></a>Dettagli tecnici
Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## <a name="twitter-triggers"></a>Trigger di Twitter
Il connettore Twitter supporta i trigger seguenti:  

| Trigger | Descrizione |
| --- | --- |
| [Quando viene pubblicato un nuovo tweet](connectors-create-api-twitter.md#when-a-new-tweet-is-posted) |Questa operazione attiva un flusso quando viene pubblicato un nuovo tweet che corrisponde a una query di ricerca specificata. |

## <a name="twitter-actions"></a>Azioni di Twitter
Il connettore Twitter supporta le azioni seguenti:

| Azione | Descrizione |
| --- | --- |
| [Recupera sequenza temporale utente](connectors-create-api-twitter.md#get-user-timeline) |Questa operazione recupera un elenco dei tweet più recenti pubblicati da un determinato utente. |
| [Recupera la sequenza temporale principale](connectors-create-api-twitter.md#get-home-timeline) |Questa operazione recupera i tweet e i re-tweet più recenti pubblicati dall'utente e dai follower. |
| [Cercare tweet](connectors-create-api-twitter.md#search-tweets) |Questa operazione recupera un elenco dei tweet pertinenti che soddisfano la query di ricerca. |
| [Recupera follower](connectors-create-api-twitter.md#get-followers) |Questa operazione recupera l'elenco degli utenti che seguono un determinato utente. |
| [Recupera follower personali](connectors-create-api-twitter.md#get-my-followers) |Questa operazione recupera l'elenco degli utenti che sono follower. |
| [Recupera following](connectors-create-api-twitter.md#get-following) |L'operazione recupera l'elenco delle persone che l'utente specificato segue. |
| [Recupera following personali](connectors-create-api-twitter.md#get-my-following) |Questa operazione recupera l'elenco degli utenti seguiti. |
| [Recupera utente](connectors-create-api-twitter.md#get-user) |Questa operazione recupera i dettagli del profilo per un determinato utente, come nome utente, descrizione, conteggio dei follower e altro ancora. |
| [Pubblica un tweet](connectors-create-api-twitter.md#post-a-tweet) |Questa operazione pubblica un nuovo tweet. |

## <a name="action-details"></a>Informazioni dettagliate sulle azioni
Ecco i dettagli per le azioni e i trigger per questo connettore con le relative risposte:

### <a name="get-user-timeline"></a>Recupera sequenza temporale utente
Questa operazione recupera un elenco dei tweet più recenti pubblicati da un determinato utente. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| userName* |Nome utente |Handle Twitter per l'utente |
| maxResults |Numero massimo di risultati |Numero massimo di tweet da restituire |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
TweetModel: rappresentazione dell'oggetto tweet

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| TweetText |string |Contenuto di testo del tweet |
| TweetId |string |ID del tweet |
| CreatedAt |string |Ora di pubblicazione del tweet |
| RetweetCount |integer |Numero totale di re-tweet per il tweet |
| TweetedBy |string |Nome dell'utente che ha pubblicato il tweet |
| MediaUrls |array |URL del contenuto multimediale pubblicato insieme al tweet |
| TweetLanguageCode |string |Codice lingua del tweet |
| TweetInReplyToUserId |string |ID utente dell'autore del tweet di cui il tweet corrente è una risposta |
| Favorited |boolean |Indica se il tweet è contrassegnato come favorito o meno |
| UserMentions |array |Elenco degli utenti menzionati nel tweet |
| OriginalTweet |non definito |Tweet originale di cui il tweet corrente è una ripetizione |
| UserDetails |non definito |Dettagli dell'utente che ha pubblicato il tweet |

### <a name="get-home-timeline"></a>Recupera la sequenza temporale principale
Questa operazione recupera i tweet e i re-tweet più recenti pubblicati dall'utente e dai follower. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| maxResults |Numero massimo di risultati |Numero massimo di tweet da restituire |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
TweetModel: rappresentazione dell'oggetto tweet

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| TweetText |string |Contenuto di testo del tweet |
| TweetId |string |ID del tweet |
| CreatedAt |string |Ora di pubblicazione del tweet |
| RetweetCount |integer |Numero totale di re-tweet per il tweet |
| TweetedBy |string |Nome dell'utente che ha pubblicato il tweet |
| MediaUrls |array |URL del contenuto multimediale pubblicato insieme al tweet |
| TweetLanguageCode |string |Codice lingua del tweet |
| TweetInReplyToUserId |string |ID utente dell'autore del tweet di cui il tweet corrente è una risposta |
| Favorited |boolean |Indica se il tweet è contrassegnato come favorito o meno |
| UserMentions |array |Elenco degli utenti menzionati nel tweet |
| OriginalTweet |non definito |Tweet originale di cui il tweet corrente è una ripetizione |
| UserDetails |non definito |Dettagli dell'utente che ha pubblicato il tweet |

### <a name="search-tweets"></a>Cercare tweet
Questa operazione recupera un elenco dei tweet pertinenti che soddisfano la query di ricerca. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| searchQuery* |Testo di ricerca |Termine di ricerca come "happy hour", #haiku, amore O odio |
| maxResults |Numero massimo di risultati |Numero massimo di tweet da restituire |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
TweetModel: rappresentazione dell'oggetto tweet

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| TweetText |string |Contenuto di testo del tweet |
| TweetId |string |ID del tweet |
| CreatedAt |string |Ora di pubblicazione del tweet |
| RetweetCount |integer |Numero totale di re-tweet per il tweet |
| TweetedBy |string |Nome dell'utente che ha pubblicato il tweet |
| MediaUrls |array |URL del contenuto multimediale pubblicato insieme al tweet |
| TweetLanguageCode |string |Codice lingua del tweet |
| TweetInReplyToUserId |string |ID utente dell'autore del tweet di cui il tweet corrente è una risposta |
| Favorited |boolean |Indica se il tweet è contrassegnato come favorito o meno |
| UserMentions |array |Elenco degli utenti menzionati nel tweet |
| OriginalTweet |non definito |Tweet originale di cui il tweet corrente è una ripetizione |
| UserDetails |non definito |Dettagli dell'utente che ha pubblicato il tweet |

### <a name="get-followers"></a>Recupera follower
Questa operazione recupera l'elenco degli utenti che seguono un determinato utente. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| userName* |Nome utente |Handle Twitter per l'utente |
| maxResults |Numero massimo di risultati |Numero massimo di utenti da restituire |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
UserDetailsModel: dettagli dell'utente di Twitter

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| FullName |string |Nome dell'utente |
| Percorso |string |Posizione dell'utente |
| ID |integer |ID Twitter dell'utente |
| UserName |string |Nome visualizzato dell'utente |
| FollowersCount |integer |Numero di follower |
| Descrizione |string |Descrizione dell'utente |
| StatusesCount |integer |Conteggio dello stato degli utenti |
| FriendsCount |integer |Numero di amici |
| FavouritesCount |integer |Numero di tweet che l'utente ha contassegnato come favoriti |
| ProfileImageUrl |string |URL dell'immagine del profilo |

### <a name="get-my-followers"></a>Recupera follower personali
Questa operazione recupera l'elenco degli utenti che sono follower. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| maxResults |Numero massimo di risultati |Numero massimo di utenti da restituire |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
UserDetailsModel: dettagli dell'utente di Twitter

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| FullName |string |Nome dell'utente |
| Percorso |string |Posizione dell'utente |
| ID |integer |ID Twitter dell'utente |
| UserName |string |Nome visualizzato dell'utente |
| FollowersCount |integer |Numero di follower |
| Descrizione |string |Descrizione dell'utente |
| StatusesCount |integer |Conteggio dello stato degli utenti |
| FriendsCount |integer |Numero di amici |
| FavouritesCount |integer |Numero di tweet che l'utente ha contassegnato come favoriti |
| ProfileImageUrl |string |URL dell'immagine del profilo |

### <a name="get-following"></a>Recupera following
L'operazione recupera l'elenco delle persone che l'utente specificato segue. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| userName* |Nome utente |Handle Twitter per l'utente |
| maxResults |Numero massimo di risultati |Numero massimo di utenti da restituire |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
UserDetailsModel: dettagli dell'utente di Twitter

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| FullName |string |Nome dell'utente |
| Percorso |string |Posizione dell'utente |
| ID |integer |ID Twitter dell'utente |
| UserName |string |Nome visualizzato dell'utente |
| FollowersCount |integer |Numero di follower |
| Descrizione |string |Descrizione dell'utente |
| StatusesCount |integer |Conteggio dello stato degli utenti |
| FriendsCount |integer |Numero di amici |
| FavouritesCount |integer |Numero di tweet che l'utente ha contassegnato come favoriti |
| ProfileImageUrl |string |URL dell'immagine del profilo |

### <a name="get-my-following"></a>Recupera following personali
Questa operazione recupera l'elenco degli utenti seguiti. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| maxResults |Numero massimo di risultati |Numero massimo di utenti da restituire |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
UserDetailsModel: dettagli dell'utente di Twitter

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| FullName |string |Nome dell'utente |
| Percorso |string |Posizione dell'utente |
| ID |integer |ID Twitter dell'utente |
| UserName |string |Nome visualizzato dell'utente |
| FollowersCount |integer |Numero di follower |
| Descrizione |string |Descrizione dell'utente |
| StatusesCount |integer |Conteggio dello stato degli utenti |
| FriendsCount |integer |Numero di amici |
| FavouritesCount |integer |Numero di tweet che l'utente ha contassegnato come favoriti |
| ProfileImageUrl |string |URL dell'immagine del profilo |

### <a name="get-user"></a>Recupera utente
Questa operazione recupera i dettagli del profilo per un determinato utente, come nome utente, descrizione, conteggio dei follower e altro ancora. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| userName* |Nome utente |Handle Twitter per l'utente |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
UserDetailsModel: dettagli dell'utente di Twitter

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| FullName |string |Nome dell'utente |
| Percorso |string |Posizione dell'utente |
| ID |integer |ID Twitter dell'utente |
| UserName |string |Nome visualizzato dell'utente |
| FollowersCount |integer |Numero di follower |
| Descrizione |string |Descrizione dell'utente |
| StatusesCount |integer |Conteggio dello stato degli utenti |
| FriendsCount |integer |Numero di amici |
| FavouritesCount |integer |Numero di tweet che l'utente ha contassegnato come favoriti |
| ProfileImageUrl |string |URL dell'immagine del profilo |

### <a name="post-a-tweet"></a>Pubblica un tweet
Questa operazione pubblica un nuovo tweet. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| tweetText |Testo del tweet |Testo da pubblicare |
| body |Contenuti multimediali |Contenuto multimediale da pubblicare |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
TweetResponseModel: modello che rappresenta un tweet pubblicato

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| TweetId |string |ID del tweet recuperato |

### <a name="when-a-new-tweet-is-posted"></a>Quando viene visualizzato un nuovo tweet
Questa operazione attiva un flusso quando viene pubblicato un nuovo tweet che corrisponde a una query di ricerca specificata. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| searchQuery* |Testo di ricerca |Termine di ricerca come "happy hour", #haiku, amore O odio |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli output
TriggerBatchResponse[TweetModel]

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

## <a name="http-responses"></a>Risposte HTTP
Le azioni e i trigger riportati sopra possono restituire uno o più dei seguenti codici di stato HTTP: 

| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto. |
| default |Operazione non riuscita. |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


