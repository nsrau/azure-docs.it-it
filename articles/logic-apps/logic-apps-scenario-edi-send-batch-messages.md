---
title: Elaborare in batch messaggi EDI come gruppo o raccolta - App per la logica di Azure | Microsoft Docs
description: Inviare messaggi EDI per l'elaborazione batch nelle app per la logica
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: d6d3a7111f3a5e49e32eba8ca4f09d692538cb87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60428027"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Inviare i messaggi EDI in batch ai partner commerciali con le App per la logica di Azure

In scenari Business-to-Business (B2B) i partner spesso scambiano messaggi in gruppi o in *batch*. Quando si crea una soluzione di invio in batch con le App per la logica, è possibile inviare messaggi a partner commerciali ed elaborare tali messaggi in batch. Questo articolo illustra come è possibile creare batch di messaggi EDI di processo, usando X12 ad esempio, creando un'app per la logica "mittente del batch" e un'app per la logica “che riceve il batch". 

L'invio in batch di messaggi X12 funziona come l'invio in batch di altri messaggi. Si usa un trigger di batch che raccoglie i messaggi in un batch e un'azione di batch che invia messaggi al batch. Inoltre, l'invio in batch X12 include un passaggio di codifica X12 prima che i messaggi siano inviati al partner commerciale o a un altro destinatario. Per ulteriori informazioni sui trigger e sulle azioni di batch, vedere [Messaggi dei processi batch](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

Questo articolo illustra come creare una soluzione batch creando due app per la logica nella stessa sottoscrizione di Azure, area di Azure seguendo questo ordine specifico:

* Un’app per la logica ["che riceve il batch"](#receiver), che accetta e raccoglie i messaggi in un batch fino a quando non vengono soddisfatti i criteri specificati per il rilascio e l'elaborazione dei messaggi. In questo scenario, il ricevitore del batch codifica anche i messaggi nel batch usando il contratto X12 specificato o le identità dei partner.

  Assicurarsi che venga creato prima di tutto il ricevitore del batch in modo che sia possibile selezionare la destinazione di batch in un secondo momento quando si crea il mittente del batch.

* Un’app per la logica ["mittente del batch"](#sender), che invia i messaggi al ricevitore del batch creato in precedenza. 

Assicurarsi che il ricevitore del batch e il mittente del batch condividano la stessa sottoscrizione di Azure *e* l’area di Azure. In caso contrario, è possibile selezionare il ricevitore del batch quando si crea il mittente del batch perché non sono visibili tra loro.

## <a name="prerequisites"></a>Prerequisiti

Per seguire questo esempio, è necessario disporre di questi elementi:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) esistente che è associato alla sottoscrizione di Azure ed è collegato alle App per la logica

* Almeno due [partners](../logic-apps/logic-apps-enterprise-integration-partners.md) esistenti nell'account di integrazione. Ogni partner deve usare il qualificatore X12 (Standard Carrier Alpha Code) come identità di business nelle proprietà del partner.

* Un [contratto X12](../logic-apps/logic-apps-enterprise-integration-x12.md) esistente nell'account di integrazione

* Per usare Visual Studio anziché il portale di Azure, assicurarsi di [configurare Visual Studio per l'uso di App per la logica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Creare il ricevitore del batch X12

Prima di poter inviare messaggi a un batch, il batch deve esistere come destinazione in cui si inviano i messaggi. Quindi, innanzitutto, è necessario creare l'app per la logica "ricevente il batch", che inizia con il trigger del **batch**. In questo modo, quando si crea l’app per la logica “mittente del batch” è possibile selezionare l’app per la logica ricevente il batch. Il ricevitore del batch continua a raccogliere i messaggi fino a quando non vengono soddisfatti i criteri specificati per il rilascio e l'elaborazione dei messaggi. Anche se non è necessario che i ricevitori di batch conoscano i mittenti di batch, i mittenti di batch devono conoscere la destinazione in cui inviano i messaggi. 

Per questo ricevitore di batch, è necessario specificare la modalità di batch, il nome del batch, i criteri di rilascio, il contratto X12 e altre impostazioni. 

1. Nel [portale di Azure](https://portal.azure.com) o in Visual Studio creare un'app per la logica con questo nome: "BatchX12Messages"

2. [Collegare l'app per la logica a un account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. Nella finestra Progettazione app per la logica aggiungere il trigger **batch**, che avvia il flusso di lavoro dell'app per la logica. Nella casella di ricerca digitare "batch" come filtro. Selezionare questo trigger: **Messaggi batch**

   ![Aggiungere il trigger Batch](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Impostare le proprietà del ricevitore di batch: 

   | Proprietà | Value | Note | 
   |----------|-------|-------|
   | **Modalità batch** | Inline |  |  
   | **Nome batch** | TestBatch | Disponibile solo con modalità batch **Inline** | 
   | **Criteri di rilascio** | In base al numero di messaggi, In base alla pianificazione | Disponibile solo con modalità batch **Inline** | 
   | **Numero di messaggi** | 10 | Disponibile solo con i criteri di rilascio **In base al numero di messaggi** | 
   | **Interval** | 10 | Disponibile solo con i criteri di rilascio **In base alla pianificazione** | 
   | **Frequenza** | minuto | Disponibile solo con i criteri di rilascio **In base alla pianificazione** | 
   ||| 

   ![Specificare i dettagli del trigger di batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > In questo esempio non viene configurata una partizione per il batch, pertanto ogni batch usa la stessa chiave di partizione. Per altre informazioni sulle partizioni, vedere [Messaggi dei processi batch](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Oar aggiungere un'azione che consenta di codificare ogni batch: 

   1. Nel trigger batch scegliere **Nuovo passaggio**.

   2. Nella casella di ricerca immettere "X batch X12" come filtro e selezionare l'azione (qualsiasi versione): **Codifica in batch <*versione*>-X12** 

      ![Selezionare l'azione di codifica batch X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Se la connessione all’account di integrazione non è stata eseguita in precedenza, creare la connessione. Specificare un nome per la connessione, selezionare l'account di integrazione desiderato e scegliere **Crea**.

      ![Creare una connessione tra codificatore di batch e account di integrazione](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Impostare queste proprietà per l'azione di codificatore batch:

      | Proprietà | DESCRIZIONE |
      |----------|-------------|
      | **Nome del contratto X12** | Aprire l'elenco e selezionare il contratto esistente. <p>Se l'elenco è vuoto, verificare di aver [collegato l'app per la logica all'account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) che dispone del contratto desiderato. | 
      | **BatchName** | Fare clic all'interno di questa casella e quando viene visualizzato l'elenco di contenuto dinamico, selezionare il token **Nome batch**. | 
      | **PartitionName** | Fare clic all'interno di questa casella e quando viene visualizzato l'elenco di contenuto dinamico, selezionare il token **Nome partizione**. | 
      | **Items** | Chiudere la finestra dettagli elemento e quindi fare clic all'interno di questa casella. Quando viene visualizzato l'elenco di contenuto dinamico, selezionare il token **Elementi in batch**. | 
      ||| 

      ![Dettagli dell'azione di codifica batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Per la casella **elementi**:

      ![Elementi dell'azione di codifica batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Salvare l'app per la logica. 

7. Se si usa Visual Studio, assicurarsi di [distribuire l'app per la logica che riceve il batch in Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). In caso contrario, non è possibile selezionare il ricevitore di batch quando si crea il mittente del batch.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

Per assicurarsi che il ricevitore di batch funzioni nel modo previsto, è possibile aggiungere un'azione HTTP per scopi di test e inviare un messaggio in batch al [servizio contenitore richieste](https://requestbin.fullcontact.com/). 

1. Sotto l’azione di codifica X12, scegliere **Nuovo passaggio**. 

2. Nella casella di ricerca, immettere "HTTP" come filtro. Selezionare questa azione: **HTTP - HTTP**
    
   ![Selezionare l'azione HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Impostare le proprietà per l'azione HTTP:

   | Proprietà | DESCRIZIONE | 
   |----------|-------------|
   | **Metodo** | Selezionare **POST** dall'elenco. | 
   | **Uri** | Generare un URI per il contenitore delle richieste e immetterlo. | 
   | **Corpo** | Fare clic all'interno di questa casella e dopo aver aperto l'elenco di contenuto dinamico, selezionare il token **corpo**, che viene visualizzato nella sezione **Codifica in batch in base al nome di contratto**. <p>Se il token **Corpo** non è visualizzato, selezionare **Vedi altri** accanto a **Codifica in batch in base al nome del contratto**. | 
   ||| 

   ![Fornire i dettagli dell'azione HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Salvare l'app per la logica. 

   L'app per la logica avrà un aspetto simile all'esempio seguente: 

   ![Salvare l'app per la logica ricevente il batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Creare il mittente del batch X12

A questo punto creare una o più app per la logica che inviano messaggi all’app per la logica che riceve il batch. In ogni mittente del batch specificare l’app per la logica che riceve il batch, il nome del batch, il contenuto del messaggio ed eventuali altre impostazioni. È possibile specificare anche una chiave di partizione univoca per dividere il batch in subset che raccolgano i messaggi a cui è assegnata tale chiave. 

* Assicurarsi di aver già [creato il ricevitore del batch](#receiver) in modo che quando si crea il mittente del batch sia possibile selezionare il ricevente del batch esistente come batch di destinazione. Anche se non è necessario che i ricevitori di batch conoscano i mittenti di batch, i mittenti di batch devono conoscere la destinazione in cui inviano i messaggi. 

* Assicurarsi che il ricevitore del batch e il mittente del batch condividano la stessa sottoscrizione di Azure *e* l’area di Azure. In caso contrario, è possibile selezionare il ricevitore del batch quando si crea il mittente del batch perché non sono visibili tra loro.

1. Creare un'altra app per la logica con questo nome: "SendX12MessagesToBatch" 

2. Nella casella di ricerca, digitare "in caso di richiesta http" come filtro. Selezionare questo trigger: **Quando viene ricevuta una richiesta HTTP** 
   
   ![Aggiungere il trigger Request](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Aggiungere un’azione per l'invio di messaggi a un batch.

   1. Sotto l'azione di richiesta HTTP, scegliere **Nuovo passaggio**.

   2. Nella casella di ricerca digitare "batch" come filtro. 
   Selezionare l'elenco **Azioni** e quindi questa azione: **Scegliere un flusso di lavoro delle app per la logica con un trigger batch - Invia messaggi al batch**

      ![Selezionare “Scegliere un flusso di lavoro delle app per la logica con un trigger batch”](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Ora selezionare l'app per la logica "BatchX12Messages" creata in precedenza.

      ![Selezionare l'app per la logica "ricevente il batch"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Selezionare questa azione: **Batch_messages - <*ricevitore-batch*>**

      ![Selezionare l'azione "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Impostare le proprietà del mittente del batch.

   | Proprietà | DESCRIZIONE | 
   |----------|-------------| 
   | **Nome batch** | Il nome del batch definito dall'app per la logica ricevente, che in questo esempio è "TestBatch" <p>**Importante**: il nome del batch viene convalidato in fase di esecuzione e deve corrispondere al nome specificato dall'app per la logica ricevente. Se si modifica il nome del batch, l'esecuzione del mittente del batch ha esito negativo. | 
   | **Contenuto del messaggio** | Il contenuto del messaggio da inviare, ovvero il token **corpo** in questo esempio | 
   ||| 
   
   ![Impostare le proprietà del batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Salvare l'app per la logica. 

   L'app per la logica mittente del batch avrà un aspetto simile all'esempio seguente:

   ![Salvare l'app per la logica mittente del batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testare le app per la logica

Per testare la soluzione di invio in batch, inserire i messaggi X12 nell'app per la logica mittente del batch da [Postman](https://www.getpostman.com/postman) o da uno strumento simile. Presto si dovrebbe iniziare a ricevere i messaggi X12 nel contenitore di richieste, o ogni dieci minuti o in batch da dieci, il tutto con la stessa chiave di partizione.

## <a name="next-steps"></a>Passaggi successivi

* [Elaborare messaggi come batch](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
