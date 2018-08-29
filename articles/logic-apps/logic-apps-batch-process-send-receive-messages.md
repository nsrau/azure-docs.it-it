---
title: Elaborare in batch i messaggi come gruppo o raccolta - App per la logica di Azure | Microsoft Docs
description: Inviare e ricevere messaggi in batch nelle App per la logica di Azure
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
manager: jeconnoc
ms.topic: article
ms.date: 08/19/2018
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.openlocfilehash: 5190e5d4191cb4d07b000920dd1be1b53e679350
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143652"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Inviare, ricevere ed elaborare in batch i messaggi nelle app per la logica di Azure

Per inviare ed elaborare messaggi tra loro in modo specifico come gruppi, è possibile creare una soluzione di invio in batch che raccoglie i messaggi in un *batch* fino a quando non vengono soddisfatti i criteri specificati per il rilascio e l'elaborazione dei messaggi in batch. L'invio in batch può ridurre la frequenza con cui l’app per la logica elabora i messaggi. Questo articolo illustra come creare una soluzione batch creando due app per la logica nella stessa sottoscrizione di Azure, area di Azure seguendo questo ordine specifico: 

* L’app per la logica ["che riceve il batch"](#batch-receiver), che accetta e raccoglie i messaggi in un batch fino a quando non vengono soddisfatti i criteri specificati per il rilascio e l'elaborazione dei messaggi.

  Assicurarsi che venga creato prima di tutto il ricevitore del batch in modo che sia possibile selezionare la destinazione di batch in un secondo momento quando si crea il mittente della batch.

* Una o più app per la logica ["mittenti del batch"](#batch-sender), che inviano i messaggi al ricevitore del batch creato in precedenza. 

   È inoltre possibile specificare una chiave univoca, ad esempio un numero cliente, per *partizionare* o suddividere il batch di destinazione in subset logici in base alla chiave. In questo modo, l'app ricevente può raccogliere tutti gli elementi con la stessa chiave ed elaborarli contemporaneamente.

Assicurarsi che il ricevitore del batch e il mittente del batch condividano la stessa sottoscrizione di Azure *e* l’area di Azure. In caso contrario, è possibile selezionare il ricevitore del batch quando si crea il mittente del batch perché non sono visibili tra loro.

## <a name="prerequisites"></a>Prerequisiti

Per seguire questo esempio, è necessario disporre di questi elementi:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Un account di posta elettronica con un [provider di posta elettronica supportato da App per la logica di Azure](../connectors/apis-list.md)

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Per usare Visual Studio anziché il portale di Azure, assicurarsi di [configurare Visual Studio per l'uso di App per la logica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Creare il ricevitore del batch

Prima di poter inviare messaggi a un batch, il batch deve esistere come destinazione in cui si inviano i messaggi. Quindi, innanzitutto, è necessario creare l'app per la logica "ricevente il batch", che inizia con il trigger del **batch**. In questo modo, quando si crea l’app per la logica “mittente del batch” è possibile selezionare l’app per la logica ricevente il batch. Il ricevitore del batch continua a raccogliere i messaggi fino a quando non vengono soddisfatti i criteri specificati per il rilascio e l'elaborazione dei messaggi. Anche se non è necessario che i ricevitori di batch conoscano i mittenti di batch, i mittenti di batch devono conoscere la destinazione in cui inviano i messaggi. 

1. Nel [portale di Azure](https://portal.azure.com) o in Visual Studio creare un'app per la logica con questo nome: "BatchReceiver" 

2. Nella finestra Progettazione app per la logica aggiungere il trigger **batch**, che avvia il flusso di lavoro dell'app per la logica. Nella casella di ricerca digitare "batch" come filtro. Selezionare il trigger: **Messaggi batch**

   ![Aggiungere il trigger "Messaggi batch"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Impostare le proprietà del ricevitore di batch: 

   | Proprietà | DESCRIZIONE | 
   |----------|-------------|
   | **Modalità batch** | - **Inline**: per la definizione di criteri di rilascio all'interno del trigger batch <br>- **Account di integrazione**: per la definizione di più configurazioni di criteri di rilascio tramite un'[account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Con un account di integrazione, è possibile gestire queste configurazioni in un'unica posizione invece che nell'App per la logica separata. | 
   | **Nome batch** | Il nome per il batch, che è "TestBatch" in questo esempio, si applica solo al **Inline** modalità batch |  
   | **Criteri di rilascio** | Si applica solo al **Inline** modalità batch e specifica i criteri da soddisfare prima di elaborare ogni batch: <p>- **Il numero di base del messaggio**: il numero di messaggi da raccogliere nel batch, ad esempio, 10 messaggi <br>- **Basato sulle dimensioni**: le dimensioni massime dei batch in byte, ad esempio, 100 MB <br>- **Basato sulla programmazione**: intervallo e frequenza tra i rilasci di batch, ad esempio, 10 minuti. È anche possibile specificare una data e un'ora di inizio. <br>- **Seleziona tutto**: usare tutti i criteri specificati. | 
   ||| 
   
   Questo esempio seleziona tutti i criteri:

   ![Specificare i dettagli del trigger Batch](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Ora aggiungere una o più azioni che elaborano ogni batch. 

   Per questo esempio, aggiungere un'azione che invii un messaggio di posta elettronica quando viene attivato il trigger batch. 
   Il trigger viene eseguito e invia un messaggio di posta elettronica quando il batch ha 10 messaggi, raggiunge i 10 MB o dopo 10 minuti.

   1. Nel trigger batch scegliere **Nuovo passaggio**.

   2. Nella casella di ricerca immettere "invia messaggio di posta elettronica" come filtro.
   In base al provider di posta elettronica in uso, selezionare un connettore di posta elettronica.
      
      Ad esempio, se hai un account personale, ad esempio @outlook.com o @hotmail.com, selezionare il connettore Outlook.com. 
      Se si dispone di un account Gmail, selezionare il connettore Gmail. 
      Questo esempio usa Outlook per Office 365. 

   3. Selezionare questa azione: **inviare un messaggio di posta elettronica - <*provider di posta elettronica*>**

      Ad esempio: 

      ![Selezionare l'azione "Invia un messaggio di posta elettronica" per il provider di posta elettronica](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Se richiesto, accedere all'account di posta elettronica. 

6. Impostare le proprietà per l'azione aggiunta.

   * Nella casella **A** immettere l'indirizzo di posta elettronica del destinatario. 
   AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica.

   * Nella casella **Oggetto**, quando viene visualizzato l'elenco contenuto dinamico, selezionare il campo **Nome partizione**.

     ![Nell'elenco contenuto dinamico selezionare "Nome partizione"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     In una sezione più avanti si specificherà una chiave di partizione univoca che divide il batch di destinazione in subset logici a cui inviare i messaggi. 
     Ogni set è associato a un numero univoco che viene generato dall'app per la logica mittente del batch. 
     Questa funzionalità consente di usare un unico batch con più subset e di assegnare a ogni subset il nome desiderato.

     > [!IMPORTANT]
     > Una partizione ha un limite di 5.000 messaggi o di 80 MB. Se viene soddisfatta la condizione, l’app per la logica può rilasciare il batch, anche quando non viene soddisfatta la condizione di rilascio.

   * Nella casella **Corpo**, quando viene visualizzato l'elenco contenuto dinamico, selezionare il campo **ID messaggio**. 

     La finestra di progettazione dell’app per la logica aggiunge automaticamente un ciclo “For each” per l'azione di invio di un messaggio di posta elettronica, perché l’azione accetta una matrice come input. 
     Questo ciclo invia un messaggio di posta elettronica per ogni messaggio nel batch. 
     Pertanto, quando il trigger batch è impostato su 10 messaggi, si ottengono 10 messaggi di posta elettronica ogni volta che il trigger viene attivato.

     ![Per la casella "Body" selezionare "ID messaggio"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Salvare l'app per la logica. È stato creato un ricevitore di batch.

    ![Salvare l'app per la logica](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Se si usa Visual Studio, assicurarsi di [distribuire l'app per la logica che riceve il batch in Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). In caso contrario, non è possibile selezionare il ricevitore di batch quando si crea il mittente del batch.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Creare il mittente dei batch

A questo punto creare una o più app per la logica mittenti del batch che inviano messaggi all’app per la logica che riceve il batch. In ogni mittente del batch specificare il ricevitore del batch e il nome del batch, il contenuto del messaggio ed eventuali altre impostazioni. È possibile specificare anche una chiave di partizione univoca per dividere il batch in subset logici per raccogliere gli elementi a cui è assegnata tale chiave. 

* Assicurarsi di aver già [creato il ricevitore del batch](#batch-receiver) in modo che quando si crea il mittente del batch sia possibile selezionare il ricevente del batch esistente come batch di destinazione. Anche se non è necessario che i ricevitori di batch conoscano i mittenti di batch, i mittenti di batch devono conoscere la destinazione in cui inviano i messaggi. 

* Assicurarsi che il ricevitore del batch e il mittente del batch condividano la stessa sottoscrizione di Azure *e* l’area di Azure. In caso contrario, è possibile selezionare il ricevitore del batch quando si crea il mittente del batch perché non sono visibili tra loro.

1. Creare un'altra app per la logica con questo nome: "BatchSender"

   1. Nella casella di ricerca digitare "ricorrenza" come filtro. 
   Selezionare questo trigger: **Ricorrenza - Pianificazione**

      ![Aggiungere il trigger “Ricorrenza - Pianificazione”](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Impostare la frequenza e l'intervallo in modo da eseguire l'app per la logica mittente ogni minuto.

      ![Impostare la frequenza e l'intervallo del trigger di ricorrenza](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Aggiungere una nuova azione per l'invio di messaggi a un batch.

   1. Nel trigger ricorrenza scegliere **Nuovo passaggio**.

   2. Nella casella di ricerca digitare "batch" come filtro. 
   Selezionare l’elenco delle **Azioni**, quindi selezionare questa azione: **Scegliere un flusso di lavoro delle app per la logica con un trigger batch - Inviare un messaggio al batch**

      ![Selezionare “Scegliere un flusso di lavoro delle app per la logica con un trigger batch”](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Selezionare l'app per la logica che riceve il batch creata in precedenza.

      ![Selezionare l'app per la logica "ricevente il batch"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > L'elenco mostra anche tutte le altre app per la logica che dispongono di trigger batch. 
      > 
      > Se si usa Visual Studio e non viene visualizzato nessun ricevitore di batch da selezionare, verificare che il ricevitore di batch sia distribuito in Azure. Se non è distribuito, vedere l'articolo su come [distribuire l'app per la logica ricevitore di batch in Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Selezionare questa azione: **Batch_messages - <*your-batch-receiver*>**

      ![Selezionare questa azione: "Batch_messages - <your-logic-app>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Impostare le proprietà del mittente del batch:

   | Proprietà | DESCRIZIONE | 
   |----------|-------------| 
   | **Nome batch** | Il nome del batch definito dall'app per la logica ricevente, che in questo esempio è "TestBatch" <p>**Importante**: il nome del batch viene convalidato in fase di esecuzione e deve corrispondere al nome specificato dall'app per la logica ricevente. Se si modifica il nome del batch, l'esecuzione del mittente del batch ha esito negativo. | 
   | **Contenuto del messaggio** | Contenuto del messaggio da inviare | 
   ||| 

   Per questo esempio aggiungere questa espressione che inserisce la data e l'ora correnti nel contenuto del messaggio da inviare al batch:

   1. Fare clic all'interno della casella **contenuto messaggio**. 

   2. Quando viene visualizzato l'elenco contenuto dinamico, scegliere **Espressione**. 

   3. Immettere l'espressione `utcnow()` e scegliere **OK**. 

      ![In "Contenuto messaggio" scegliere "Espressione", immettere "utcnow()" e scegliere "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Configurare a questo punto una partizione per il batch. Nell'azione "BatchReceiver" scegliere **Mostra opzioni avanzate** e impostare le proprietà seguenti:

   | Proprietà | DESCRIZIONE | 
   |----------|-------------| 
   | **Nome della partizione** | Una chiave di partizione univoca facoltativa da usare per la suddivisione del batch di destinazione in subset logici e raccogliere i messaggi in base a tale chiave | 
   | **Id del messaggio** | Un identificatore di messaggio facoltativo che rappresenta l'identificatore univoco globale generato (GUID) se non viene specificato | 
   ||| 

   Per questo esempio aggiungere un'espressione nella casella **Nome della partizione** che genera un numero casuale compreso tra uno e cinque. Lasciare la casella **Id del messaggio** vuota.
   
   1. Fare clic all'interno della casella **Nome della partizione** in modo che venga visualizzato l'elenco di contenuti dinamici. 

   2. Nell'elenco di contenuto dinamico scegliere **Espressione**.
   
   3. Immettere l'espressione `rand(1,6)` e scegliere **OK**.

      ![Configurare una partizione per il batch di destinazione](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Questa funzione **rand** genera un numero compreso tra uno e cinque. 
      Si divide pertanto il batch in cinque partizioni numerate, che questa espressione imposta in modo dinamico.

5. Salvare l'app per la logica. L'app per la logica appare ora simile a questo esempio:

   ![Salvare l'app per la logica mittente](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testare le app per la logica

Per testare la soluzione per l'invio in batch, lasciare in esecuzione le app per la logica per alcuni minuti. Si inizierà presto a ricevere messaggi di posta elettronica a gruppi di cinque, tutti con la stessa chiave di partizione.

L'app per la logica mittente del batch viene eseguita ogni minuto, genera un numero casuale compreso tra uno e cinque e lo usa come chiave di partizione per il batch di destinazione a cui vengono inviati i messaggi. Ogni volta che il batch contiene cinque elementi con la stessa chiave di partizione, l'app per la logica ricevente il batch si attiva e invia posta elettronica per ogni messaggio.

> [!IMPORTANT]
> Al termine dei test, assicurarsi di disabilitare l'app per la logica BatchSender per arrestare l'invio di messaggi ed evitare il sovraccarico della casella di posta in arrivo.

## <a name="next-steps"></a>Passaggi successivi

* [Build on logic app definitions by using JSON](../logic-apps/logic-apps-author-definitions.md) (Compilare definizioni di app per la logica on JSON)
* [Compilare un'app senza server in Visual Studio con App per la logica e Funzioni](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Gestione delle eccezioni e registrazione degli errori per le app per la logica](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
