---
title: Elaborare in batch i messaggi come gruppo o raccolta - App per la logica di Azure | Microsoft Docs
description: Inviare e ricevere messaggi per l'elaborazione in batch nelle app per la logica
keywords: batch, processo batch
author: jonfancey
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: de519084a4f172ad984c78727123835eeb9deaef
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>Inviare, ricevere ed elaborare in batch i messaggi nelle app per la logica

Per elaborare i messaggi in gruppi, è possibile inviare elementi di dati o messaggi a un *batch* e quindi elaborarli come batch. Questo approccio è utile quando si desidera che gli elementi di dati vengano raggruppati secondo una modalità specifica ed elaborati insieme. 

È possibile creare app per la logica che ricevono gli elementi in batch usando il trigger **Batch**. È possibile creare quindi app per la logica che inviano elementi a un batch usando l'azione **Batch**.

Questo argomento illustra come compilare una soluzione di invio in batch mediante l'esecuzione di queste attività: 

* [Creare un'app per la logica che riceve e raccoglie gli elementi in batch](#batch-receiver). Nell'app per la logica "ricevente il batch" si specificano il nome del batch e i criteri di rilascio da soddisfare perché l'app ricevente rilasci ed elabori gli elementi. 

* [Creare un'app per la logica che invia elementi a un batch](#batch-sender). Nell'app per la logica "mittente del batch" si specifica dove inviare gli elementi. La destinazione degli elementi deve essere un'app per la logica ricevente esistente. È inoltre possibile specificare una chiave univoca, ad esempio un numero cliente, per "partizionare" o suddividere il batch di destinazione in subset in base alla chiave. Tutti gli elementi con questa chiave verranno così raccolti ed elaborati insieme. 

## <a name="requirements"></a>Requisiti

Per seguire questo esempio, è necessario disporre di questi elementi:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, è possibile [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Un account di posta elettronica con un [provider di posta elettronica supportato da App per la logica di Azure](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>Creare app per la logica che ricevono messaggi in batch

Prima di poter inviare messaggi a un batch, è necessario creare un'app per la logica "ricevente il batch" con il trigger **Batch**. Sarà possibile in questo modo selezionare questa app ricevente quando si creerà l'app per la logica mittente. Nell'app ricevente occorre specificare il nome del batch, i criteri di rilascio e altre impostazioni. 

Nelle app per la logica mittenti è necessario specificare dove inviare gli elementi, mentre in quelle riceventi non è necessario aggiungere informazioni sulle app mittenti.

1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica con questo nome: "BatchReceiver" 

2. Nella finestra Progettazione app per la logica aggiungere il trigger **batch**, che avvia il flusso di lavoro dell'app per la logica. Nella casella di ricerca digitare "batch" come filtro. Selezionare il trigger: **Batch - Messaggi batch**

   ![Aggiungere il trigger Batch](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Specificare un nome per il batch e specificare i criteri per il rilascio del batch, ad esempio:

   * **Nome batch**: il nome usato per identificare il batch, in questo esempio "TestBatch".
   * **Criteri di rilascio**: criteri di rilascio del batch, che possono essere basati sul numero di messaggi, su una pianificazione o su entrambi.
   
     ![Specificare i dettagli del trigger Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Numero messaggi**: il numero di messaggi da inserire in un batch prima del rilascio per l'elaborazione, in questo esempio "5".

     ![Specificare i dettagli del trigger Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Pianificazione**: pianificazione del rilascio del batch per l'elaborazione, che corrisponde a ogni 5 minuti in questo esempio.

     ![Specificare i dettagli del trigger Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. Aggiungere un'altra azione che invia un messaggio di posta elettronica quando viene attivato il trigger Batch. Ogni volta che il batch contiene cinque elementi o sono trascorsi cinque minuti, l'app per la logica invia un messaggio di posta elettronica.

   1. Nel trigger batch scegliere **+ Nuovo passaggio** > **Aggiungi un'azione**.

   2. Nella casella di ricerca digitare "email" come filtro.
   In base al provider di posta elettronica in uso, selezionare un connettore di posta elettronica.
   
      Se si dispone, ad esempio, di un account aziendale o dell'istituto di istruzione, selezionare il connettore Office 365 Outlook. 
      Se si dispone di un account Gmail, selezionare il connettore Gmail.

   3. Selezionare questa azione per il connettore: **{*provider di posta elettronica*} - Invia un messaggio di posta elettronica**

      Ad esempio: 

      ![Selezionare l'azione "Invia un messaggio di posta elettronica" per il provider di posta elettronica](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. Se non è stata creata prima una connessione per il provider di posta elettronica, immettere al prompt le credenziali per l'autenticazione. Leggere altre informazioni sull'[autenticazione delle credenziali di posta elettronica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

6. Impostare le proprietà per l'azione appena aggiunta.

   * Nella casella **A** immettere l'indirizzo di posta elettronica del destinatario. 
   AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica.

   * Nella casella **Oggetto**, quando viene visualizzato l'elenco **Contenuto dinamico**, selezionare il campo **Nome partizione**.

     ![Nell'elenco "Contenuto dinamico" selezionare "Nome partizione"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     In una sezione più avanti si specificherà una chiave di partizione univoca che divide il batch di destinazione in set logici a cui inviare i messaggi. 
     Ogni set è associato a un numero univoco che viene generato dall'app per la logica mittente. 
     Questa funzionalità consente di usare un unico batch con più subset e di assegnare a ogni subset il nome desiderato.

   * Nella casella **Corpo**, quando viene visualizzato l'elenco **Contenuto dinamico**, selezionare il campo **ID messaggio**.

     ![Per la casella "Body" selezionare "ID messaggio"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     Poiché l'input per l'azione di invio del messaggio di posta elettronica è una matrice, la finestra di progettazione aggiunge automaticamente un ciclo **For each** per l'azione **Invia un messaggio di posta elettronica**. 
     Questo ciclo esegue l'azione interna su ogni elemento nel batch. 
     Con il trigger batch impostato su cinque elementi, si ottengono quindi cinque messaggi di posta elettronica ogni volta che viene attivato il trigger.

7.  Ora che è stata creata un'app per la logica ricevente, occorre salvarla.

    ![Salvare l'app per la logica](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Una partizione ha un limite di 5.000 messaggi o di 80 MB. Se una delle condizioni è soddisfatta, il batch potrebbe essere rilasciato, anche se non è soddisfatta la condizione definita dall'utente.


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>Creare app per la logica che inviano messaggi a un batch

Creare a questo punto una o più app per la logica che inviano elementi al batch definito dall'app per la logica ricevente. Nell'app mittente specificare il nome del batch e dell'app per la logica ricevente, il contenuto del messaggio ed eventuali altre impostazioni. È possibile specificare anche una chiave di partizione univoca per dividere il batch in subset che raccolgano gli elementi a cui è assegnata tale chiave.

Nelle app per la logica mittenti è necessario specificare dove inviare gli elementi, mentre in quelle riceventi non è necessario aggiungere informazioni sulle app mittenti.

1. Creare un'altra app per la logica con questo nome: "BatchSender"

   1. Nella casella di ricerca digitare "ricorrenza" come filtro. 
   Selezionare il trigger **Pianificazione - Ricorrenza**

      ![Aggiungere il trigger "Pianificazione - Ricorrenza"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. Impostare la frequenza e l'intervallo in modo da eseguire l'app per la logica mittente ogni minuto.

      ![Impostare la frequenza e l'intervallo del trigger di ricorrenza](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. Aggiungere un nuovo passaggio per l'invio di messaggi a un batch.

   1. Nel trigger ricorrenza scegliere **+ Nuovo passaggio** > **Aggiungi un'azione**.

   2. Nella casella di ricerca digitare "batch" come filtro. 

   3. Selezionare l'azione **Invia messaggi al batch - Scegliere un flusso di lavoro delle app per la logica con un trigger batch**

      ![Selezionare "Invia messaggi al batch"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. Selezionare a questo punto l'app per la logica "BatchReceiver" creata in precedenza, che ora viene visualizzata sotto forma di azione.

      ![Selezionare l'app per la logica "ricevente il batch"](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > L'elenco mostra anche tutte le altre app per la logica che dispongono di trigger batch.

3. Impostare le proprietà del batch.

   * **Nome batch**: il nome del batch definito dall'app per la logica ricevente, che in questo esempio è "TestBatch" e viene convalidato in fase di esecuzione.

     > [!IMPORTANT]
     > Assicurarsi di non modificare il nome del batch in quanto deve corrispondere al nome batch specificato dall'app per la logica ricevente.
     > Se si modifica il nome del batch, l'esecuzione dell'app per la logica mittente ha esito negativo.

   * **Contenuto messaggio**: il contenuto del messaggio che si desidera inviare. 
   Per questo esempio aggiungere questa espressione che inserisce la data e l'ora correnti nel contenuto del messaggio da inviare al batch:

     1. Quando viene visualizzato l'elenco **Contenuto dinamico**, scegliere **Espressione**. 
     2. Immettere l'espressione **utcnow()** e scegliere **OK**. 

        ![In "Contenuto messaggio" scegliere "Espressione". Immettere "utcnow()".](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. Configurare a questo punto una partizione per il batch. Nell'azione "BatchReceiver" scegliere **Mostra opzioni avanzate**.

   * **Nome partizione**: una chiave di partizione univoca facoltativa da usare per la suddivisione del batch di destinazione. Per questo esempio aggiungere un'espressione che genera un numero casuale compreso tra uno e cinque.
   
     1. Quando viene visualizzato l'elenco **Contenuto dinamico**, scegliere **Espressione**.
     2. Immettere l'espressione **rand(1,6)**

        ![Configurare una partizione per il batch di destinazione](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        Questa funzione **rand** genera un numero compreso tra uno e cinque. 
        Si divide pertanto il batch in cinque partizioni numerate, che questa espressione imposta in modo dinamico.

   * **ID messaggio**: un identificatore di messaggio facoltativo e GUID generato quando vuoto. 
   Per questo esempio lasciare vuota questa casella.

5. Salvare l'app per la logica. L'app per la logica appare ora simile a questo esempio:

   ![Salvare l'app per la logica mittente](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>Testare le app per la logica

Per testare la soluzione per l'invio in batch, lasciare in esecuzione le app per la logica per alcuni minuti. Si inizierà presto a ricevere messaggi di posta elettronica a gruppi di cinque, tutti con la stessa chiave di partizione.

L'app per la logica BatchSender viene eseguita ogni minuto, genera un numero casuale compreso tra uno e cinque e lo usa come chiave di partizione per il batch di destinazione a cui vengono inviati i messaggi. Ogni volta che il batch contiene cinque elementi con la stessa chiave di partizione, l'app per la logica BatchReceiver si attiva e invia posta elettronica per ogni messaggio.

> [!IMPORTANT]
> Al termine dei test, assicurarsi di disabilitare l'app per la logica BatchSender per arrestare l'invio di messaggi ed evitare il sovraccarico della casella di posta in arrivo.

## <a name="next-steps"></a>Passaggi successivi

* [Build on logic app definitions by using JSON](../logic-apps/logic-apps-author-definitions.md) (Compilare definizioni di app per la logica on JSON)
* [Compilare un'app senza server in Visual Studio con App per la logica e Funzioni](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Gestione delle eccezioni e registrazione degli errori per le app per la logica](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
