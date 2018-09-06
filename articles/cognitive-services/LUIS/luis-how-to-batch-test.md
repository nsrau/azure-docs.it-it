---
title: Eseguire test in batch dell'app LUIS - Azure | Microsoft Docs
description: Eseguire il test in batch dell'app LUIS (Language Understanding) per trovare le espressioni con finalità ed entità errate.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: 07c38aa5f66fc8c38392c5407449ec6911e80d47
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888627"
---
# <a name="batch-testing"></a>Test in batch
 Il test in batch è un test completo sul modello già sottoposto a training per misurarne le prestazioni in LUIS. 

<a name="batch-testing"></a>
## <a name="import-a-dataset-file-for-batch-testing"></a>Importare un file di set di dati per il test in batch

1. Selezionare **Test** nella barra in alto, quindi selezionare il pannello **Batch testing** (Test in batch).

    ![Collegamento Batch Testing (Test in batch)](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Selezionare **Import dataset** (Importa set di dati). Verrà visualizzata la finestra di dialogo **Import new dataset** (Importa nuovo set di dati). Selezionare **Choose File** (Scegli file) e cercare il file JSON con il formato [JSON](luis-concept-batch-test.md#batch-file-format) corretto che *non contiene più di 1.000* espressioni da testare.

    ![Importare il file di set di dati](./media/luis-how-to-batch-test/batchtest-importset.png)

    Gli errori di importazione vengono segnalati in una barra di notifica rossa nella parte superiore del browser. Quando un'importazione presenta degli errori, non viene creato alcun set di dati. Per ulteriori informazioni, vedere[Errori comuni](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. Nel campo **Dataset Name** (Nome set di dati) immettere un nome per il file di set di dati. Il file del set di dati include una **matrice di espressioni**, incluse la *finalità etichettata* e le *entità*. Rivedere la sintassi del [file batch di esempio](luis-concept-batch-test.md#batch-file-format). 

4. Selezionare **Operazione completata**. Il file di set di dati viene aggiunto.

## <a name="run-rename-export-or-delete-dataset"></a>Eseguire, rinominare, esportare o eliminare il set di dati
Per eseguire, rinominare, esportare o eliminare il set di dati, usare il pulsante dei puntini di sospensione (***...*** ) alla fine della riga del set di dati.

![Azioni del set di dati](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Eseguire un test in batch sull'app già sottoposta a training

Per eseguire il test, selezionare il nome del set di dati. Al termine del test, questa riga visualizza il risultato del test del set di dati.

![Risultato del test in batch](./media/luis-how-to-batch-test/run-test.png)

Il set di dati scaricabile è lo stesso file caricato per il test in batch.

|Stato|Significato|
|--|--|
|![Icona cerchio verde test completato](./media/luis-how-to-batch-test/batch-test-result-green.png)|Tutte le espressioni sono corrette.|
|![Icona x rossa test non riuscito](./media/luis-how-to-batch-test/batch-test-result-red.png)|Almeno una finalità di un'espressione non corrisponde alla stima.|
|![Icona Ready to test (Pronti per il test)](./media/luis-how-to-batch-test/batch-test-result-blue.png)|È possibile procedere con il test.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>
## <a name="view-batch-test-results"></a>Visualizzare i risultati del test in batch 
Per esaminare i risultati del test in batch, selezionare **See results** (Visualizza risultati).

![Risultati del test in batch](./media/luis-how-to-batch-test/run-test-results.png)

<!-- Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. Green points indicate correct prediction, and red ones indicate incorrect prediction. The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.-->


<a name="filter-chart-results-by-intent-or-entity"></a>  
## <a name="filter-chart-results"></a>Filtrare i risultati del grafico

Per filtrare il grafico in base a una finalità o entità specifica, selezionare la finalità o l'entità nel pannello filtro sulla destra. I punti dati e la relativa distribuzione vengono aggiornati nel grafico in base alla selezione. 
 
![Risultato del test in batch visualizzato](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Visualizzare i dati dell'espressione single-point
Nel grafico passare il mouse su un punto dati per visualizzare il punteggio di certezza della relativa stima. Selezionare un punto dati per recuperare l'espressione corrispondente nell'elenco nella parte inferiore della pagina. 

![Espressione selezionata](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="view-section-data"></a>Visualizzare i dati sezione
Nel grafico a quattro sezioni selezionare il nome della sezione, ad esempio **False Positive** (Falso positivo) in alto a destra del grafico. Sotto il grafico, tutte le espressioni in quella sezione vengono visualizzate sotto il grafico in un elenco. 

![Espressioni selezionate per sezione](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

In questa immagine precedente l'espressione `switch on` viene etichettata con la finalità TurnAllOn, ma ha ricevuto la stima della finalità None (Nessuna). Indica che la finalità TurnAllOn necessita di più espressioni di esempio per elaborare la stima prevista. 

Le due sezioni del grafico in rosso indicano le espressioni che non corrispondevano alla stima prevista. Indicano le espressioni per le quali LUIS necessita di ulteriore training. 

Le due sezioni del grafico in verde corrispondevano alla stima prevista.

## <a name="next-steps"></a>Passaggi successivi

Se il test indica che l'app LUIS non riconosce le finalità e le entità corrette, è possibile migliorare le prestazioni dell'app LUIS etichettando più espressioni o aggiungendo funzionalità. 

* [Etichettare le espressioni suggerite con LUIS](luis-how-to-review-endoint-utt.md) 
* [Usare le funzionalità per migliorare le prestazioni dell'app LUIS](luis-how-to-add-features.md) 
* [Comprendere il test in batch con questa esercitazione](luis-tutorial-batch-testing.md)
* [Informazioni sui concetti del test in batch](luis-concept-batch-test.md).
