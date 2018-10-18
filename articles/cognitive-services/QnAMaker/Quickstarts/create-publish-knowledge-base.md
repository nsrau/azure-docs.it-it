---
title: 'Guida introduttiva: creazione di una Knowledge Base - QnA Maker'
titleSuffix: Azure Cognitive Services
description: È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. La Knowledge Base di QnA Maker in questo esempio viene creata da una semplice pagina Web di domande frequenti per rispondere alle domande sul recupero chiavi di BitLocker.
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: 99f9259c9fa73a9ba4842b2684cde8a44f73145a
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389667"
---
# <a name="quickstart-create-train-and-publish-your-knowledge-base"></a>Guida introduttiva: Creare, eseguire il training e pubblicare la knowledge base

È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. La Knowledge Base di QnA Maker in questo esempio viene creata da una semplice pagina Web di domande frequenti per rispondere alle domande sul recupero chiavi di BitLocker.

## <a name="prerequisite"></a>Prerequisito

> [!div class="checklist"]
> * Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-qna-maker-knowledge-base"></a>Creare una knowledge base di QnA Maker

1. Accedere a QnA Maker con le credenziali di Azure.

2. Nel sito Web di QnA Maker, selezionare **creare una knowledge base**.

   ![Creare una nuova knowledge base](../media/qna-maker-create-kb.png)

3. Nella pagina **Crea**, nel passaggio 1, selezionare **creare un servizio QnA**. Si verrà reindirizzati al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) per configurare un servizio QnA Maker nella sottoscrizione. Se il portale di Azure va in timeout, selezionare **Riprova** nel sito. Dopo la connessione, viene visualizzata la dashboard di Azure.

4. Dopo aver creato correttamente un nuovo servizio QnA Maker in Azure, tornare a qnamaker.ai/create. Selezionare il servizio QnA dagli elenchi a discesa nel passaggio 2. Se è stato creato un nuovo servizio di domande e risposte, assicurarsi di riaggiornare la pagina.

   ![Selezionare una knowledge base del servizio QnA](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. Nel passaggio 3, denominare la KB **Mia KB di domande e risposte di esempio**.

6. Per aggiungere contenuto alla Knowledge Base, selezionare tre tipi di origini dati. Nel passaggio 4, sotto **Popola la KB**, aggiungere l'URL [domande frequenti sul ripristino di BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) nella casella **URL**.

   ![Selezionare una knowledge base del servizio QnA](../media/qnamaker-quickstart-kb/add-datasources.png)

7. Nel passaggio 5, selezionare **Crea la KB**.

8. Durante la creazione della Knowledge Base, viene visualizzata una finestra popup. Il processo di estrazione richiede alcuni minuti per leggere la pagina HTML e identificare le domande e risposte.

9. Dopo aver creato la Knowledge Base, verrà visualizzata la pagina **Knowledge base**. È possibile modificare il contenuto della Knowledge base in questa pagina.

10. In alto a destra, selezionare **Add QnA pair** (Aggiungi coppia domanda/risposta) per aggiungere una nuova riga nella sezione **editoriale** della Knowledge Base. Sotto **domanda**, immettere **Ciao.** Sotto **risposta**, immettere **Salve. Ponimi domande di bitlocker.**

   ![Aggiungere una coppia domanda/risposta](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. In alto a destra, selezionare **Save and train** (Salva ed esegui il training) per salvare le modifiche e il training del modello di QnA Maker. Le modifiche non vengono mantenute a meno che non vengano salvate.

   ![Save and train (Salva ed esegui training)](../media/qnamaker-quickstart-kb/add-qna-pair2.png)

12. In alto a destra, selezionare **Esegui test** per verificare che le modifiche apportate abbiano avuto effetto. Immettere **Ciao a tutti** nella casella e premere INVIO. Verrà visualizzata la risposta che è stata creata come risposta.

13. Selezionare **Ispeziona** per esaminare la risposta in modo più dettagliato. La finestra di test viene utilizzata per testare le modifiche alla Knowledge Base prima che vengano pubblicate.

   ![Pannello Test](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Selezionare nuovamente **Esegui test** per comprimere il pop-up **Esegui test**.

15. Nel menu accanto a **Modifica**, selezionare **Pubblica**. Quindi, per confermare, selezionare **Pubblica** nella pagina.

16. Il servizio QnA Maker viene pubblicato correttamente. Utilizza l'endpoint nell’applicazione o nel codice bot.

   ![Pubblica](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una knowledge base](../How-To/create-knowledge-base.md)
