---
title: Elaborare in batch messaggi EDI come gruppo o raccolta - App per la logica di Azure | Microsoft Docs
description: Inviare messaggi EDI per l'elaborazione batch nelle app per la logica
keywords: batch, elaborare in batch, codificare in batch
author: divswa
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
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 837cb0d9595da5b5bd4f01fb4576f75e98ab8912
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2017
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Inviare messaggi X12 in batch a partner commerciali

In scenari Business-to-Business (B2B) i partner spesso scambiano messaggi in gruppi o batch. Per inviare messaggi in gruppi o batch a partner commerciali, è possibile creare un batch con più elementi e quindi usare l'azione batch X12 per elaborare gli elementi come batch.


L'invio in batch per messaggi X12, come per altri messaggi, usa un trigger e un'azione batch. Anche per X12, il batch viene sottoposto a un passaggio di codifica X12 prima di arrivare al partner o a qualsiasi altra destinazione. Per altre informazioni sui trigger e sulle azioni batch, vedere [Elaborazione batch di messaggi](logic-apps-batch-process-send-receive-messages.md).

Questo argomento mostra come elaborare messaggi X12 come batch tramite l'esecuzione di queste attività:
* [Creare un'app per la logica che riceve elementi e crea un batch](#receiver). L'app per la logica "destinataria" esegue queste azioni:
 
   * Specifica il nome del batch e i criteri di rilascio da soddisfare prima di rilasciare gli elementi come batch.

   * Elabora o codifica gli elementi nel batch usando l'accordo X12 specificato o le identità dei partner.

* [Creare un'app per la logica che invia elementi a un batch](#sender). L'app per la logica "mittente" specifica la destinazione a cui inviare gli elementi per l'invio in batch, che deve essere un'app per la logica destinataria esistente.


## <a name="prerequisites"></a>prerequisiti

Per seguire questo esempio, è necessario disporre di questi elementi:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, è possibile [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure.

* Almeno due [partner](logic-apps-enterprise-integration-partners.md) definiti nell'account di integrazione. Assicurarsi che ogni partner usi il qualificatore X12 (Standard Carrier Alpha Code) nelle proprietà del partner come identità di business.

* Un [contratto X12](logic-apps-enterprise-integration-x12.md) già definito nell'account di integrazione.

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Creare un'app per la logica che riceve messaggi X12 e crea un batch

Prima di poter inviare messaggi a un batch, è necessario creare un'app per la logica "destinataria" con il trigger **Batch**. Sarà possibile in questo modo selezionare questa app ricevente quando si creerà l'app per la logica mittente. Per l'app per la logica destinataria, è necessario specificare il nome del batch, i criteri di rilascio, l'accordo X12 e altre impostazioni. 


1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica con questo nome: "BatchX12Messages".

2. Nella finestra Progettazione app per la logica aggiungere il trigger **batch**, che avvia il flusso di lavoro dell'app per la logica. Nella casella di ricerca digitare "batch" come filtro. Selezionare il trigger: **Batch - Messaggi batch**

   ![Aggiungere il trigger Batch](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. Specificare un nome per il batch e specificare i criteri per il rilascio del batch, ad esempio:

   * **Nome batch**: il nome usato per identificare il batch, in questo esempio "TestBatch".

   * **Criteri di rilascio**: criteri di rilascio del batch, che possono essere basati sul numero di messaggi, su una pianificazione o su entrambi.
   
     ![Specificare i dettagli del trigger Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Numero messaggi**: il numero di messaggi da inserire in un batch prima del rilascio per l'elaborazione, in questo esempio "5".

     ![Specificare i dettagli del trigger Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Pianificazione**: pianificazione del rilascio del batch per l'elaborazione, che corrisponde a ogni 10 minuti in questo esempio.

     ![Specificare i dettagli del trigger Batch](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. Aggiungere un'altra azione per la codifica dei messaggi raggruppati o in batch e la creazione di un messaggio in batch X12. 

   a. Scegliere **+ Nuovo passaggio** > **Aggiungi un'azione**.

   b. Nella casella di ricerca immettere "batch X12" come filtro e selezionare un'azione per **X12 - Codifica in batch**. Come per il connettore di codifica X12, esistono molte varianti per l'azione di codifica in batch. È possibile selezionarne una qualsiasi.

   ![Selezionare l'azione di codifica batch X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. Impostare le proprietà per l'azione appena aggiunta.

   * Nella casella **Nome dell'accordo X12** selezionare l'accordo nell'elenco a discesa. Se l'elenco è vuoto, assicurarsi di aver creato una connessione all'account di integrazione.

   * Nella casella **BatchName** selezionare il campo **Nome batch** nell'elenco del contenuto dinamico.
   
   * Nella casella **PartitionName** selezionare il campo **Nome partizione** nell'elenco del contenuto dinamico.

   * Nella casella **Items** selezionare il campo **Elementi in batch** nell'elenco del contenuto dinamico.

   ![Dettagli dell'azione di codifica batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. Per scopi di test, aggiungere un'azione HTTP per l'invio del messaggio in batch al [servizio Request Bin](https://requestbin.fullcontact.com/). 

   1. Immettere "HTTP" come filtro nella casella di ricerca. Selezionare l'azione **HTTP - HTTP**
    
      ![Selezionare l'azione HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. Nell'elenco **Metodo** selezionare **POST**. Per la casella **URI** generare un URI per il contenitore delle richieste e immetterlo. Nella casella **Corpo**, quando viene visualizzato l'elenco dinamico, selezionare il campo **Corpo** nella sezione **Codifica in batch in base al nome dell'accordo**. Se il campo **Corpo** non è visualizzato, scegliere **Vedi altri** accanto a **Codifica in batch in base al nome dell'accordo**.

      ![Fornire i dettagli dell'azione HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  Ora che è stata creata un'app per la logica destinataria, è necessario salvarla.

    ![Salvare l'app per la logica](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Una partizione ha un limite di 5.000 messaggi o di 80 MB. Se una delle condizioni è soddisfatta, il batch potrebbe essere rilasciato, anche se non è soddisfatta la condizione definita dall'utente.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Creare un'app per la logica che invia messaggi X12 a un batch

Creare a questo punto una o più app per la logica che inviano elementi al batch definito dall'app per la logica ricevente. Nell'app mittente specificare il nome del batch e dell'app per la logica ricevente, il contenuto del messaggio ed eventuali altre impostazioni. È possibile specificare anche una chiave di partizione univoca per dividere il batch in subset che raccolgano gli elementi a cui è assegnata tale chiave.

Le app per la logica mittenti devono saper identificare dove inviare gli elementi, mentre le app per la logica destinatarie non devono conoscere i mittenti.


1. Creare un'altra app per la logica con questo nome: "X12MessageSender". Aggiungere questo trigger all'app per la logica: **Richiesta/Risposta - Richiesta** 
   
   ![Aggiungere il trigger Request](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. Aggiungere un nuovo passaggio per l'invio di messaggi a un batch.

   1. Scegliere **+ Nuovo passaggio** > **Aggiungi un'azione**.

   2. Nella casella di ricerca digitare "batch" come filtro. 

3. Selezionare l'azione **Invia messaggi al batch - Scegliere un flusso di lavoro delle app per la logica con un trigger batch**

   ![Selezionare "Invia messaggi al batch"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. Selezionare l'app per la logica "BatchX12Messages" creata in precedenza, visualizzata ora come azione.

   ![Selezionare l'app per la logica "ricevente il batch"](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > L'elenco mostra anche tutte le altre app per la logica che dispongono di trigger batch.

5. Impostare le proprietà del batch.

   * **Nome batch**: il nome del batch definito dall'app per la logica ricevente, che in questo esempio è "TestBatch" e viene convalidato in fase di esecuzione.

     > [!IMPORTANT]
     > Assicurarsi di non modificare il nome del batch in quanto deve corrispondere al nome batch specificato dall'app per la logica ricevente.
     > Se si modifica il nome del batch, l'esecuzione dell'app per la logica mittente ha esito negativo.

   * **Contenuto del messaggio**: contenuto del messaggio che si vuole inviare al batch
   
   ![Impostare le proprietà del batch](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. Salvare l'app per la logica. L'app per la logica appare ora simile a questo esempio:

   ![Salvare l'app per la logica mittente](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Testare le app per la logica

Per testare la soluzione di invio in batch, inserire i messaggi X12 nell'app per la logica mittente da [Postman](https://www.getpostman.com/postman) o uno strumento simile. Presto si dovrebbe iniziare a ottenere i messaggi X12, come batch di cinque elementi o ogni dieci minuti, nel contenitore delle richieste, il tutto con la stessa chiave di partizione.

## <a name="next-steps"></a>Passaggi successivi

* [Elaborare messaggi come batch](logic-apps-batch-process-send-receive-messages.md) 
* [Compilare un'app senza server in Visual Studio con App per la logica e Funzioni](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Gestione delle eccezioni e registrazione degli errori per le app per la logica](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
