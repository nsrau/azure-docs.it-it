---
title: Creare, eseguire il training e pubblicare la knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. La knowledge base di QnA Maker in questo esempio viene creata da una semplice pagina Web di domande frequenti per rispondere alle domande sul recupero chiavi di BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: cf6b0cd99254d5b034687598c588ce5ef13dbd53
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272853"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Creare, eseguire il training e pubblicare la knowledge base QnA Maker

È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. La knowledge base di QnA Maker in questo esempio viene creata da una semplice pagina Web di domande frequenti per rispondere alle domande sul recupero chiavi di BitLocker.

## <a name="prerequisite"></a>Prerequisito

> [!div class="checklist"]
> * Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-qna-maker-knowledge-base"></a>Creare una knowledge base di QnA Maker

1. Accedere al portale di [QnAMaker.ai](https://QnAMaker.ai) con le credenziali di Azure.

1. Nel portale di QnA Maker, selezionare **Creare una knowledge base**.

   ![Creare una nuova knowledge base](../media/qna-maker-create-kb.png)

1. Nella pagina **Crea**, nel passaggio 1, selezionare **creare un servizio QnA**. Si verrà reindirizzati al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) per configurare un servizio QnA Maker nella sottoscrizione. Se il portale di Azure va in timeout, selezionare **Riprova** nel sito. Dopo la connessione, viene visualizzata la dashboard di Azure.

1. Dopo aver creato correttamente un nuovo servizio QnA Maker in Azure, tornare a qnamaker.ai/create. Selezionare il servizio QnA dagli elenchi a discesa nel passaggio 2. Se è stato creato un nuovo servizio di domande e risposte, assicurarsi di riaggiornare la pagina.

   ![Selezionare una knowledge base del servizio QnA](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Nel passaggio 3, denominare la knowledge base **Mia KB di domande e risposte di esempio**.

1. Per aggiungere contenuto alla knowledge base, selezionare tre tipi di origini dati. Nel passaggio 4, sotto **Popola la KB**, aggiungere l'URL [domande frequenti sul ripristino di BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) nella casella **URL**.

   ![Selezionare una knowledge base del servizio QnA](../media/qnamaker-quickstart-kb/add-datasources.png)

1. Nel passaggio 5, selezionare **Crea la KB**.

1. Durante la creazione della knowledge base, viene visualizzata una finestra popup. Il processo di estrazione richiede alcuni minuti per leggere la pagina HTML e identificare le domande e risposte.

1. Dopo aver creato la knowledge base, verrà visualizzata la pagina **Knowledge base**. È possibile modificare il contenuto della knowledge base in questa pagina.

## <a name="edit-the-knowledge-base"></a>Modificare la knowledge base

1. Nella sezione **Modifica** del portale QnA Maker selezionare **Add QnA pair** (Aggiungi coppia domanda/risposta) per aggiungere una nuova riga alla tabella della knowledge base. Sotto **domanda**, immettere **Ciao.** Sotto **risposta**, immettere **Salve. Ponimi domande di BitLocker.**

    ![Aggiungere una coppia domanda/risposta](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. In alto a destra, selezionare **Save and train** (Salva ed esegui il training) per salvare le modifiche e il training del modello di QnA Maker. Le modifiche non vengono mantenute a meno che non vengano salvate.

## <a name="test-the-knowledge-base"></a>Testare la knowledge base

1. In alto a destra del portale QnA Maker, selezionare **Esegui test** per verificare che le modifiche apportate abbiano avuto effetto. Immettere `hi there` nella casella e premere INVIO. Verrà visualizzata la risposta che è stata creata come risposta.

1. Selezionare **Ispeziona** per esaminare la risposta in modo più dettagliato. La finestra di test viene utilizzata per testare le modifiche alla knowledge base prima che vengano pubblicate.

    ![Pannello Test](../media/qnamaker-quickstart-kb/inspect-panel.png)

1. Selezionare nuovamente **Esegui test** per comprimere il pop-up **Esegui test**.

## <a name="publish-the-knowledge-base"></a>Pubblicare la knowledge base

Quando si pubblica una knowledge base, il contenuto di domande e risposte della knowledge base passa dall'indice di test a un indice di produzione in Ricerca di Azure.

![Pubblicare l'indice di test di produzione](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Nel menu accanto a **Modifica** del portale QnA Maker, selezionare **Pubblica**. Quindi, per confermare, selezionare **Pubblica** nella pagina.

1. Il servizio QnA Maker viene pubblicato correttamente. Utilizza l'endpoint nell’applicazione o nel codice bot.

    ![Pubblica](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Creazione di un bot

La creazione di un bot è sempre disponibile dopo la pubblicazione, dalla pagina **Pubblica**. 

* È possibile creare rapidamente diversi bot, che indirzzano tutti alla stessa knowledge base per aree o piani tariffari differenti per i singoli bot. 
* Se si vuole solo un bot per la knowledge base, usare il collegamento **View all your bots on the Azure portal** (Visualizza tutti i bot nel portale di Azure) per visualizzare un elenco dei bot correnti. 
* Quando si apportano modifiche alla knowledge base e si ripubblica, non è necessario eseguire ulteriori operazioni con il bot. È già configurato per interagire con la knowledge base e funziona con tutte le modifiche successive alla knowledge base. Ogni volta che si pubblica una knowledge base, tutti i bot connessi vengono aggiornati automaticamente

1. Nella pagina **Pubblicazione** del portale di QnA Maker, selezionare **Creare il bot**. Questo pulsante viene visualizzato solo dopo che è stata pubblicata la knowledge base.

    ![Nel portale di QnA Maker, passare alla pagina Pubblicazione e pubblicare la knowledge base. Selezionare Creare il bot.](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Viene visualizzata una nuova scheda del browser per il portale di Azure, con la pagina di creazione del servizio Azure Bot. Configurare il servizio Azure Bot. Per altre informazioni su queste impostazioni di configurazione, vedere l'esercitazione [Creare un bot QnA con il servizio Azure Bot v4](../tutorials/create-qna-bot.md).
    
    * Non modificare le impostazioni seguenti nel portale di Azure durante la creazione del bot. Sono già popolate per la knowledge base esistente: 
        * Chiave di autenticazione QnA
        * Piano di servizio app/Località
        * Archiviazione di Azure
    * Il bot e QnA Maker possono condividere il _piano_ di servizio dell'app Web, ma non possono condividere l'app Web. Ciò significa che il **nome app** deve essere diverso dal nome dell'app che è stato usato alla creazione del servizio QnA Maker. 


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una knowledge base](../How-To/create-knowledge-base.md)
