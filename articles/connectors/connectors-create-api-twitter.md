---
title: Informazioni su come usare il connettore Twitter nelle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Twitter con i parametri dell'API REST
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: be8163043535833ce45b3d50939a537406cf8152
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
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


## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/twitterconnector/). 

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)

