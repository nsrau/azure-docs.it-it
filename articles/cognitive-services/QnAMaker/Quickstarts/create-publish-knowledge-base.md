---
title: Eseguire la creazione, il training e la pubblicazione di una knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. La knowledge base di QnA Maker in questo esempio viene creata da una semplice pagina Web di domande frequenti per rispondere alle domande sul recupero chiavi di BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/10/2019
ms.author: diberry
ms.openlocfilehash: 609d71898d8db027f1cfee9e1b73039367ec94f4
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693368"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Creare, eseguire il training e pubblicare la knowledge base QnA Maker

È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. Questo articolo offre un esempio della creazione di una knowledge base di QnA Maker da una semplice pagina Web di domande frequenti, per rispondere a domande sul recupero delle chiavi di BitLocker.

## <a name="prerequisite"></a>Prerequisito

> [!div class="checklist"]
> * Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-qna-maker-knowledge-base"></a>Creare una knowledge base di QnA Maker

1. Accedere al portale [QnAMaker.ai](https://QnAMaker.ai) con le credenziali di Azure.

1. Nel portale di QnA Maker, selezionare **Creare una knowledge base**.

   ![Screenshot del portale di QnA Maker](../media/qna-maker-create-kb.png)

1. Nella pagina **Crea**, nel passaggio 1, selezionare **creare un servizio QnA**. Si verrà reindirizzati al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) per configurare un servizio QnA Maker nella sottoscrizione. Se il portale di Azure va in timeout, selezionare **Riprova** nel sito. Dopo la connessione, viene visualizzata la dashboard di Azure.

1. Dopo aver creato correttamente un nuovo servizio QnA Maker in Azure, tornare a qnamaker.ai/create. Nel passaggio 2 selezionare il servizio QnA Maker negli elenchi a discesa. Se è stato creato un nuovo servizio QnA Maker, assicurarsi di aggiornare la pagina.

   ![Screenshot della selezione di una knowledge base per il servizio QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Nel passaggio 3, denominare la knowledge base **Mia KB di domande e risposte di esempio**.

1. Per aggiungere contenuto alla knowledge base, selezionare tre tipi di origini dati. Nel passaggio 4, sotto **Popola la KB**, aggiungere l'URL [domande frequenti sul ripristino di BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) nella casella **URL**.

   ![Screenshot dell'aggiunta di origini dati](../media/qnamaker-quickstart-kb/add-datasources.png)

1. Nel passaggio 5, selezionare **Crea la KB**.

1. Mentre QnA Maker crea la knowledge base, viene visualizzata una finestra popup. Il processo di estrazione richiede alcuni minuti per leggere la pagina HTML e identificare le domande e risposte.

1. Al termine della creazione della knowledge base, verrà visualizzata la pagina **Knowledge base**. È possibile modificare il contenuto della knowledge base in questa pagina.

## <a name="edit-the-knowledge-base"></a>Modificare la knowledge base

1. Nella sezione **Edit** (Modifica) del portale di QnA Maker selezionare **Add QnA pair** (Aggiungi coppia domanda/risposta) per aggiungere una nuova riga alla knowledge base. Sotto **domanda**, immettere **Ciao.** Sotto **risposta**, immettere **Salve. Ponimi domande di BitLocker.**

    ![Screenshot del portale di QnA Maker](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. In alto a destra, selezionare **Save and train** (Salva ed esegui il training) per salvare le modifiche e il training del modello di QnA Maker. Le modifiche non vengono mantenute a meno che non vengano salvate.

## <a name="test-the-knowledge-base"></a>Testare la knowledge base

1. In alto a destra del portale QnA Maker, selezionare **Esegui test** per verificare che le modifiche apportate abbiano avuto effetto. Immettere `hi there` nella casella e premere INVIO. Verrà visualizzata la risposta che è stata creata come risposta.

1. Selezionare **Ispeziona** per esaminare la risposta in modo più dettagliato. La finestra di test viene utilizzata per testare le modifiche alla knowledge base prima che vengano pubblicate.

    ![Screenshot del pannello di test](../media/qnamaker-quickstart-kb/inspect.png)

1. Selezionare nuovamente **Esegui test** per comprimere il pop-up **Esegui test**.

## <a name="publish-the-knowledge-base"></a>Pubblicare la knowledge base

Quando si pubblica una knowledge base, il contenuto di domande e risposte della knowledge base passa dall'indice di test a un indice di produzione in Ricerca di Azure.

![Screenshot dello spostamento del contenuto della knowledge base](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Nel menu accanto a **Modifica** del portale QnA Maker, selezionare **Pubblica**. Quindi, per confermare, selezionare **Pubblica** nella pagina.

1. Il servizio QnA Maker viene pubblicato correttamente. Utilizza l'endpoint nell’applicazione o nel codice bot.

    ![Screenshot del completamento della pubblicazione](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Creazione di un bot

Dopo la pubblicazione è possibile creare un bot dalla pagina **Publish** (Pubblica): 

* È possibile creare rapidamente diversi bot, che indirzzano tutti alla stessa knowledge base per aree o piani tariffari differenti per i singoli bot. 
* Se si vuole solo un bot per la knowledge base, usare il collegamento **View all your bots on the Azure portal** (Visualizza tutti i bot nel portale di Azure) per visualizzare un elenco dei bot correnti. 

Quando si apportano modifiche alla knowledge base e si ripete la pubblicazione, non sono necessarie ulteriori azioni con il bot. È già configurato per interagire con la knowledge base e funziona con tutte le successive modifiche a essa apportate. Ogni volta che si pubblica una knowledge base, tutti i bot connessi vengono aggiornati automaticamente.

1. Nella pagina **Pubblicazione** del portale di QnA Maker, selezionare **Creare il bot**. Questo pulsante viene visualizzato solo dopo la pubblicazione della knowledge base.

    ![Schermata della creazione di un bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Viene visualizzata una nuova scheda del browser per il portale di Azure, con la pagina di creazione del servizio Azure Bot. Configurare il servizio Azure Bot. Per altre informazioni su queste impostazioni di configurazione, vedere [Creare un bot di domande e risposte con il servizio Azure Bot v4](../tutorials/create-qna-bot.md).
    
    * Durante la creazione del bot non modificare le impostazioni seguenti nel portale di Azure. Sono già popolate per la knowledge base esistente: 
        * Chiave di autenticazione QnA
        * Piano di servizio app e località
        * Archiviazione di Azure
    * Il bot e QnA Maker possono condividere il piano di servizio dell'app Web, ma non l'app Web. Ciò significa che il **nome app** deve essere diverso dal nome dell'app che è stato usato alla creazione del servizio QnA Maker. 


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una knowledge base](../How-To/create-knowledge-base.md)
