---
title: Aggiungere il connettore Yammer alle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Yammer con i parametri dell&quot;API REST
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: c69f7af9f9427eb295481657eaefcdad76b48ccb


---
# <a name="get-started-with-the-yammer-connector"></a>Introduzione al connettore Yammer
Connettersi a Yammer per accedere alle conversazioni della rete dell'organizzazione.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica.
> 
> 

Con Yammer è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Yammer. 
* Usare i trigger quando c'è un nuovo messaggio in un gruppo o un feed che si sta seguendo.
* Usare le azioni per pubblicare un messaggio, recuperare tutti i messaggi e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene visualizzato un nuovo messaggio, è possibile inviare un messaggio di posta elettronica tramite Office 365.

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Yammer include i trigger e le azioni seguenti. 

| Trigger | Azioni |
| --- | --- |
| <ul><li>Quando viene pubblicato un nuovo messaggio in un gruppo</li><li>Quando viene pubblicato un nuovo messaggio nel feed Seguiti</li></ul> |<ul><li>Ottieni tutti i messaggi</li><li>Ottiene i messaggi in un gruppo</li><li>Ottiene i messaggi dal feed Seguiti</li><li>Pubblicare messaggio</li><li>Quando viene pubblicato un nuovo messaggio in un gruppo</li><li>Quando viene pubblicato un nuovo messaggio nel feed Seguiti</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML. 

## <a name="create-a-connection-to-yammer"></a>Creare una connessione a Yammer
Per usare il connettore Yammer, creare prima una **connessione**, quindi specificare i dettagli di queste proprietà: 

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Specificare le credenziali di Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

## <a name="yammer-rest-api-reference"></a>Informazioni di riferimento sull'API REST di Yammer
Questa documentazione è relativa alla versione 1.0

### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Ottenere tutti i messaggi pubblici nella rete Yammer dell'utente connesso
Corrisponde a "Tutte" le conversazioni nell'interfaccia Web di Yammer.  
```GET: /messages.json```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| older_then |integer |no |query |nessuno |Restituisce i messaggi meno recenti rispetto all'ID del messaggio specificato come stringa numerica. Risulta utile per l'impaginazione dei messaggi. Ad esempio, se si stanno attualmente visualizzando 20 messaggi e il meno recente è il numero 2912, è possibile aggiungere "?older_than=2912" alla richiesta per ottenere i 20 messaggi precedenti a quelli visualizzati. |
| newer_then |integer |no |query |nessuno |Restituisce i messaggi più recenti rispetto all'ID del messaggio specificato come stringa numerica. Risulta utile per il polling dei nuovi messaggi. Se si cercano i messaggi e il messaggio più recente restituito è il numero 3516, è possibile eseguire una richiesta con il parametro "?newer_than=3516" per garantire che non si ottengono copie duplicate dei messaggi già presenti nella pagina. |
| limit |integer |no |query |nessuno |Restituisce solo il numero specificato di messaggi. |
| page |integer |no |query |nessuno |Recuperare la pagina specificata. Se i dati restituiti sono maggiori del limite, è possibile usare questo campo per accedere alle pagine successive |

### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 408 |Timeout richiesta |
| 429 |Troppe richieste |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| 503 |Servizio Yammer non disponibile |
| 504 |Timeout gateway |
| default |Operazione non riuscita. |

### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Pubblica un messaggio in un gruppo o nel feed generale dell'organizzazione
Se viene fornito l'ID del gruppo, il messaggio verrà inviato al gruppo specificato altrimenti verrà pubblicato nel feed generale dell'organizzazione.    
```POST: /messages.json``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| input | |Sì |body |nessuno |Pubblicare la richiesta del messaggio |

### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 201 |Data di creazione |

## <a name="object-definitions"></a>Definizioni oggetto
#### <a name="message-yammer-message"></a>Message: messaggio di Yammer
| Nome | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |integer |no |
| content_excerpt |string |no |
| sender_id |integer |no |
| replied_to_id |integer |no |
| created_at |string |no |
| network_id |integer |no |
| message_type |stringa |no |
| sender_type |string |no |
| URL |string |no |
| web_url |string |no |
| group_id |integer |no |
| body |non definito |no |
| thread_id |integer |no |
| direct_message |boolean |no |
| client_type |stringa |no |
| client_url |string |no |
| Lingua |string |no |
| notified_user_ids |array |no |
| privacy |string |no |
| liked_by |non definito |no |
| system_message |boolean |no |

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: indica una richiesta di pubblicazione per il connettore Yammer per la pubblicazione in Yammer
| Nome | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| body |string |sì |
| group_id |integer |no |
| replied_to_id |integer |no |
| direct_to_id |integer |no |
| broadcast |boolean |no |
| topic1 |string |no |
| topic2 |string |no |
| topic3 |string |no |
| topic4 |string |no |
| topic5 |string |no |
| topic6 |string |no |
| topic7 |string |no |
| topic8 |string |no |
| topic9 |string |no |
| topic10 |string |no |
| topic11 |string |no |
| topic12 |string |no |
| topic13 |string |no |
| topic14 |string |no |
| topic15 |string |no |
| topic16 |string |no |
| topic17 |string |no |
| topic18 |string |no |
| topic19 |string |no |
| topic20 |string |no |

#### <a name="messagelist-list-of-messages"></a>MessageList: elenco di messaggi
| Nome | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| messages |array |no |

#### <a name="messagebody-message-body"></a>MessageBody: corpo del messaggio
| Nome | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| parsed |string |no |
| plain |string |no |
| rich |string |no |

#### <a name="likedby-liked-by"></a>LikedBy: utenti che hanno espresso gradimento
| Nome | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| count |integer |no |
| names |array |no |

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: utenti che hanno espresso gradimento
| Nome | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| type |string |no |
| id |integer |no |
| full_name |string |no |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png



<!--HONumber=Feb17_HO3-->


