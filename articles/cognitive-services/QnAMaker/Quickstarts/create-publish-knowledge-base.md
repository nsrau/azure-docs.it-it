---
title: 'Avvio rapido: Eseguire la creazione, il training e la pubblicazione di una knowledge base - QnA Maker'
description: È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. Questo articolo offre un esempio della creazione di una knowledge base di QnA Maker da una semplice pagina Web di domande frequenti, per rispondere alle domande di QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: f1712a51fcd35c348f6177b670a7686898a0c78b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346139"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Avvio rapido: Creare, eseguire il training e pubblicare la knowledge base QnA Maker

È possibile creare una knowledge base di QnA Maker (KB) a partire dal proprio contenuto, come le domande frequenti o i manuali del prodotto. Questo articolo offre un esempio della creazione di una knowledge base di QnA Maker da una semplice pagina Web di domande frequenti, per rispondere alle domande di QnA Maker.

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare.
> * Una [risorsa](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) QnA Maker creata nel portale di Azure. Prendere nota dei valori di ID di Azure Active Directory, sottoscrizione e nome della risorsa di QnA selezionati durante la creazione della risorsa.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Creare la prima knowledge base di QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

1. Accedere al portale [QnAMaker.ai](https://QnAMaker.ai) con le credenziali di Azure.

2. Nel portale di QnA Maker selezionare **Creare una knowledge base**.

3. Nella pagina **Crea** ignorare il **Passaggio 1** se la risorsa QnA Maker è già presente.

    Se la risorsa non è ancora stata creata, selezionare **Creare un servizio QnA**. Si verrà reindirizzati al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) per configurare un servizio QnA Maker nella sottoscrizione. Prendere nota dei valori di ID di Azure Active Directory, sottoscrizione e nome della risorsa di QnA selezionati durante la creazione della risorsa.

    Al termine della creazione della risorsa nel portale di Azure, tornare al portale di QnA Maker, aggiornare la pagina del browser e continuare con il **Passaggio 2**.

4. Nel **Passaggio 2** selezionare l'istanza di Active Directory, la sottoscrizione, il servizio (risorsa) e la lingua per tutte le knowledge base create nel servizio.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="Screenshot della selezione di una knowledge base per il servizio QnA Maker":::

5. Nel **Passaggio 3** assegnare alla knowledge base il nome **My Sample QnA KB**.

6. Nel **Passaggio 4** configurare le impostazioni in base alla tabella seguente:

    |Impostazione|valore|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files** (Abilita estrazione a più turni da URL e file PDF o DOCX)|Selezionato|
    |**Multi-turn default text** (Testo predefinito per più turni)| Selezionare un'opzione|
    |**+ Add URL** (+ Aggiungi URL)|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Selezionare **Professional**|

7. Nel **Passaggio 5** selezionare **Create your KB** (Crea la KB).

    Il processo di estrazione impiega alcuni minuti per leggere il documento e identificare le domande e risposte.

    Al termine della creazione della knowledge base, verrà visualizzata la pagina **Knowledge base**. È possibile modificare il contenuto della knowledge base in questa pagina.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

1. Accedere al portale [QnAMaker.ai](https://QnAMaker.ai) con le credenziali di Azure.

2. Nel portale di QnA Maker selezionare **Creare una knowledge base**.

3. Nella pagina **Crea** ignorare il **Passaggio 1** se la risorsa QnA Maker è già presente.

    Se la risorsa non è ancora stata creata, selezionare **Creare un servizio QnA**. Si verrà reindirizzati al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) per configurare un servizio QnA Maker nella sottoscrizione. Prendere nota dei valori di ID di Azure Active Directory, sottoscrizione e nome della risorsa di QnA selezionati durante la creazione della risorsa.

    Al termine della creazione della risorsa nel portale di Azure, tornare al portale di QnA Maker, aggiornare la pagina del browser e continuare con il **Passaggio 2**.

4. Nel **Passaggio 2** selezionare l'istanza di Active Directory, la sottoscrizione, il servizio (risorsa) e la lingua per tutte le knowledge base create nel servizio.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/connect-your-knowledge-base.png" alt-text="Screenshot relativo alla selezione di un'anteprima gestita della knowledge base del servizio QnA Maker":::

5. Se nel **Passaggio 2** si sta creando la prima knowledge base per il servizio, sarà possibile definire un'impostazione della lingua specifica per ogni knowledge base. Dopo aver definito l'impostazione della lingua per la prima knowledge base, non sarà possibile modificare le impostazioni per il servizio in un secondo momento.

6. Nel  **Passaggio 3** assegnare alla knowledge base il nome  **My Sample QnA KB**. 

7. Nel **Passaggio 4** configurare le impostazioni in base alla tabella seguente:

    |Impostazione|valore|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files** (Abilita estrazione a più turni da URL e file PDF o DOCX)|Selezionato|
    |**Multi-turn default text** (Testo predefinito per più turni)| Selezionare un'opzione|
    |**+ Add URL** (+ Aggiungi URL)|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Selezionare **Professional**|

8. Nel **Passaggio 5** selezionare **Create your KB** (Crea la KB).

    Il processo di estrazione impiega alcuni minuti per leggere il documento e identificare le domande e risposte.

    Al termine della creazione della knowledge base, verrà visualizzata la pagina **Knowledge base**. È possibile modificare il contenuto della knowledge base in questa pagina.

---

## <a name="add-a-new-question-and-answer-set"></a>Aggiungere un nuovo set di domande e risposte

1. Nella pagina **Edit** (Modifica) del portale di QnA Maker selezionare **+ Add QnA pair** (Aggiungi coppia domanda/risposta) sulla barra degli strumenti contestuale.
1. Aggiungere la domanda seguente:

    `How many Azure services are used by a knowledge base?`

1. Aggiungere la risposta formattata con _Markdown_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="Aggiungere la domanda come testo e la risposta formattata con Markdown.":::

    Il simbolo di markdown, `*`, viene usato per i punti elenco. `\n` viene usato per una nuova riga.

    La pagina **Edit** (Modifica) mostra il markdown. Quando successivamente si userà il pannello **Test**, si vedrà il markdown visualizzato correttamente.

## <a name="save-and-train"></a>Save and train (Salva ed esegui training)

In alto a destra selezionare **Save and train** (Salva ed esegui il training) per salvare le modifiche e il training di QnA Maker. Le modifiche non vengono mantenute a meno che non vengano salvate.

## <a name="test-the-knowledge-base"></a>Testare la knowledge base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

1. In alto a destra del portale QnA Maker, selezionare **Esegui test** per verificare che le modifiche apportate abbiano avuto effetto.
2. Immettere una query utente di esempio nella casella di testo.

    `How many Azure services are used by a knowledge base?`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png" alt-text="Immettere una query utente di esempio nella casella di testo.":::

3. Selezionare **Ispeziona** per esaminare la risposta in modo più dettagliato. La finestra di test viene usata per testare le modifiche alla knowledge base prima della pubblicazione.

4. Selezionare nuovamente **Test** per chiudere il pannello **Test**.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

1. In alto a destra del portale QnA Maker, selezionare **Esegui test** per verificare che le modifiche apportate abbiano avuto effetto.
2. Immettere una query utente di esempio nella casella di testo.

    `whats the size of the touchscreen`

3. Se si abilita la funzionalità MRC per la knowledge base, selezionando l'opzione **Display short answer** (Visualizza risposta breve), verrà visualizzata anche una risposta precisa, se disponibile, unitamente al passaggio della risposta nel riquadro del test. 

    ![Riquadro del test abilitato per la modalità gestita](../media/conversational-context/test-pane-with-managed.png)
    

4. Selezionare Ispeziona per esaminare la risposta in modo più dettagliato. La finestra di test viene usata per testare le modifiche alla knowledge base prima della pubblicazione. 
5. Selezionare nuovamente **Test** per chiudere il pannello **Test**.
---

## <a name="publish-the-knowledge-base"></a>Pubblicare la knowledge base

Quando si pubblica una knowledge base, il relativo contenuto viene spostato dall'indice `test` a un indice `prod` in Ricerca di Azure.

![Screenshot dello spostamento del contenuto della knowledge base](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Nel portale di QnA Maker selezionare **Publish** (Pubblica). Quindi, per confermare, selezionare **Pubblica** nella pagina.

    Il servizio QnA Maker viene pubblicato correttamente. Utilizza l'endpoint nell’applicazione o nel codice bot.

    ![Screenshot del completamento della pubblicazione](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Creare un bot

Dopo la pubblicazione è possibile creare un bot dalla pagina **Publish** (Pubblica):

* È possibile creare rapidamente diversi bot, che indirzzano tutti alla stessa knowledge base per aree o piani tariffari differenti per i singoli bot.
* Se si vuole solo un bot per la knowledge base, usare il collegamento **View all your bots on the Azure portal** (Visualizza tutti i bot nel portale di Azure) per visualizzare un elenco dei bot correnti.

Quando si apportano modifiche alla knowledge base e si ripete la pubblicazione, non sono necessarie ulteriori azioni con il bot. È già configurato per interagire con la knowledge base e funziona con tutte le successive modifiche a essa apportate. Ogni volta che si pubblica una knowledge base, tutti i bot connessi vengono aggiornati automaticamente.

1. Nella pagina **Pubblicazione** del portale di QnA Maker, selezionare **Creare il bot**. Questo pulsante viene visualizzato solo dopo la pubblicazione della knowledge base.

    ![Schermata della creazione di un bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Viene visualizzata una nuova scheda del browser per il portale di Azure, con la pagina di creazione del servizio Azure Bot. Configurare il servizio Azure Bot. Il bot e QnA Maker possono condividere il piano di servizio dell'app Web, ma non l'app Web. Ciò significa che il **nome app** del bot deve essere diverso da quello del servizio QnA Maker.

    * **Cosa fare**
        * Cambiare l'handle del bot, se non è univoco.
        * Selezionare il linguaggio dell'SDK. Una volta creato il bot, è possibile scaricare il codice nell'ambiente di sviluppo locale e continuare il processo di sviluppo.
    * **Cosa non fare**
        * Cambiare le impostazioni seguenti nel portale di Azure quando si crea il bot. Sono già popolate per la knowledge base esistente:
           * Chiave di autenticazione QnA
           * Piano di servizio app e località


1. Dopo aver creato il bot, aprire la risorsa **servizio Bot**.
1. In **Bot Management** (Gestione bot) selezionare **Test in Web Chat** (Testa nella chat Web).
1. Quando viene visualizzato il prompt della chat **Type your message** (Digitare il messaggio), immettere:

    `Azure services?`

    Il chatbot risponde con una risposta della knowledge base.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Immettere una query utente nella chat Web di test.":::

## <a name="what-did-you-accomplish"></a>Cosa si è ottenuto?

È stata creata una nuova knowledge base in cui sono stati aggiunti un URL pubblico e una coppia personalizzata di domande e risposte, quindi la knowledge base è stata sottoposta a training, testata e pubblicata.

Dopo aver pubblicato la knowledge base, è stato creato un bot, che è stato testato.

Questa procedura è stata completata in pochi minuti senza la necessità di scrivere codice o pulire il contenuto.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si continua con l'argomento di avvio rapido successivo, eliminare le risorse QnA Maker e Bot Framework nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere domande con i metadati](add-question-metadata-portal.md)

Per altre informazioni:

* [Formato Markdown nelle risposte](../reference-markdown-format.md)
* [Origini dati](../index.yml) di QnA Maker.