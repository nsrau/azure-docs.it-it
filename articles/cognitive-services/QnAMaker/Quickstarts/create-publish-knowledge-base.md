---
title: 'Guida introduttiva: Eseguire la creazione, il training e la pubblicazione di una knowledge base - QnA Maker'
titleSuffix: Azure Cognitive Services
description: È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. La knowledge base di QnA Maker in questo esempio viene creata da una semplice pagina Web di domande frequenti per rispondere alle domande sul recupero chiavi di BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 8ce35156cd772487c4787f2f5fb99feb1225826f
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794195"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Guida introduttiva: Creare, eseguire il training e pubblicare la knowledge base QnA Maker

È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. Questo articolo offre un esempio della creazione di una knowledge base di QnA Maker da una semplice pagina Web di domande frequenti, per rispondere a domande sul recupero delle chiavi di BitLocker.

Includere una personalità per chit chat in modo da rendere la propria knowledge base più accattivante per gli utenti.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Prerequisito

> [!div class="checklist"]
> * Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-new-qna-maker-knowledge-base"></a>Creare una nuova knowledge base di QnA Maker

1. Accedere al portale [QnAMaker.ai](https://QnAMaker.ai) con le credenziali di Azure.

1. Nel portale di QnA Maker, selezionare **Creare una knowledge base**.

1. Nella pagina **Create** (Crea) selezionare **Create a QnA service** (Crea un servizio QnA). Si verrà reindirizzati al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) per configurare un servizio QnA Maker nella sottoscrizione. 

1. Nel portale di QnA Maker selezionare il servizio QnA Maker negli elenchi a discesa. Se è stato creato un nuovo servizio QnA Maker, assicurarsi di aggiornare la pagina.

   ![Screenshot della selezione di una knowledge base per il servizio QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Assegnare alla knowledge base il nome **My Sample QnA KB**.

1. Aggiungere un documento di Word di esempio come URL: 

    `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`

1. Selezionare `+ Add URL`.

1. Aggiungere **chiacchiere _professionali_** alla knowledge base. 

1. Selezionare **Crea la KB**.

    Il processo di estrazione impiega alcuni minuti per leggere il documento e identificare le domande e risposte.

    Al termine della creazione della knowledge base, verrà visualizzata la pagina **Knowledge base**. È possibile modificare il contenuto della knowledge base in questa pagina.

## <a name="add-a-new-question-and-answer-set"></a>Aggiungere un nuovo set di domande e risposte

1. Nella pagina **Edit** (Modifica) del portale di QnA Maker selezionare **Add QnA pair** (Aggiungi coppia domanda/risposta).
1. Aggiungere la domanda seguente: 

    `How many Azure services are used by a knowledge base?`

1. Aggiungere la risposta formattata con _Markdown_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Aggiungere la domanda come testo e la risposta formattata con Markdown.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Il simbolo di markdown, `*`, viene usato per i punti elenco. `\n` viene usato per una nuova riga.  

    La pagina **Edit** (Modifica) mostra il markdown. Quando successivamente si userà il pannello **Test**, si vedrà il markdown visualizzato correttamente. 

## <a name="save-and-train"></a>Save and train (Salva ed esegui training)

In alto a destra, selezionare **Save and train** (Salva ed esegui il training) per salvare le modifiche e il training del modello di QnA Maker. Le modifiche non vengono mantenute a meno che non vengano salvate.

## <a name="test-the-knowledge-base"></a>Testare la knowledge base

1. In alto a destra del portale QnA Maker, selezionare **Esegui test** per verificare che le modifiche apportate abbiano avuto effetto. 
1. Immettere una query utente di esempio nella casella di testo. 

    `How many Azure services are used by a knowledge base?`  

    ![ Immettere una query utente di esempio nella casella di testo. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Selezionare **Ispeziona** per esaminare la risposta in modo più dettagliato. La finestra di test viene usata per testare le modifiche alla knowledge base prima della pubblicazione.

1. Selezionare nuovamente **Test** per chiudere il pannello **Test**.

## <a name="publish-the-knowledge-base"></a>Pubblicare la knowledge base

Quando si pubblica una knowledge base, il suo contenuto viene spostato dall'indice `test` a un indice `prod` in Ricerca di Azure.

![Screenshot dello spostamento del contenuto della knowledge base](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Nel portale di QnA Maker selezionare **Publish** (Pubblica). Quindi, per confermare, selezionare **Pubblica** nella pagina.

    Il servizio QnA Maker viene pubblicato correttamente. Utilizza l'endpoint nell’applicazione o nel codice bot.

    ![Screenshot del completamento della pubblicazione](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Creazione di un bot

Dopo la pubblicazione è possibile creare un bot dalla pagina **Publish** (Pubblica): 

* È possibile creare rapidamente diversi bot, che indirzzano tutti alla stessa knowledge base per aree o piani tariffari differenti per i singoli bot. 
* Se si vuole solo un bot per la knowledge base, usare il collegamento **View all your bots on the Azure portal** (Visualizza tutti i bot nel portale di Azure) per visualizzare un elenco dei bot correnti. 

Quando si apportano modifiche alla knowledge base e si ripete la pubblicazione, non sono necessarie ulteriori azioni con il bot. È già configurato per interagire con la knowledge base e funziona con tutte le successive modifiche a essa apportate. Ogni volta che si pubblica una knowledge base, tutti i bot connessi vengono aggiornati automaticamente.

1. Nella pagina **Pubblicazione** del portale di QnA Maker, selezionare **Creare il bot**. Questo pulsante viene visualizzato solo dopo la pubblicazione della knowledge base.

    ![Schermata della creazione di un bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Viene visualizzata una nuova scheda del browser per il portale di Azure, con la pagina di creazione del servizio Azure Bot. Configurare il servizio Azure Bot. 
    
    * Durante la creazione del bot non modificare le impostazioni seguenti nel portale di Azure. Sono già popolate per la knowledge base esistente: 
        * Chiave di autenticazione QnA
        * Piano di servizio app e località
    * Il bot e QnA Maker possono condividere il piano di servizio dell'app Web, ma non l'app Web. Ciò significa che il **nome app** del bot deve essere diverso da quello del servizio QnA Maker. 

1. Dopo aver creato il bot, aprire la risorsa **servizio Bot**. 
1. In **Bot Management** (Gestione bot) selezionare **Test in Web Chat** (Testa nella chat Web).
1. Quando viene visualizzato il prompt della chat **Type your message** (Digitare il messaggio), immettere:

    `Azure services?`

    Il chatbot risponde con una risposta della knowledge base. 

    ![Immettere una query utente nella chat Web di test.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Pulire le risorse di QnA Maker e Bot Framework nel portale di Azure. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni:

* [Formato Markdown nelle risposte](../concepts/data-sources-supported.md)
* [Test di Markdown](../concepts/data-sources-supported.md#testing-your-markdown)
* [Origini dati](../Concepts/data-sources-supported.md) di QnA Maker. 
* [Impostazioni di configurazione delle risorse bot](../tutorials/create-qna-bot.md).

> [!div class="nextstepaction"]
> [Aggiungere domande con i metadati](add-question-metadata-portal.md)

