---
title: 'Esercitazione: Test in batch per individuare i problemi - LUIS'
description: Questa esercitazione illustra come usare i test in batch per verificare la qualità dell'app LUIS (Language Understanding).
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78250531"
---
# <a name="tutorial-batch-test-data-sets"></a>Esercitazione: Eseguire test in batch dei set di dati

Questa esercitazione illustra come usare i test in batch per verificare la qualità dell'app LUIS (Language Understanding).

L'esecuzione di test in batch consente di convalidare lo stato del modello attivo e sottoposto a training con un set noto di espressioni ed entità etichettate. Nel file batch in formato JSON aggiungere le espressioni e impostare le etichette di entità che è necessario prevedere all'interno dell'espressione.

Requisiti per il test in batch:

* Numero massimo di 1000 espressioni per ogni test.
* Assenza di duplicati.
* Tipi di entità consentiti: solo entità di Machine Learning.

Quando si usa un'app diversa rispetto a quella di questa esercitazione, *non* usare le espressioni di esempio già aggiunte all'app.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importare l'app di esempio
> * Creare un file di test in batch
> * Eseguire un test in batch
> * Esaminare i risultati del test

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importare l'app di esempio

Importare un'app che accetta un ordine di pizza, ad esempio `1 pepperoni pizza on thin crust`.

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Usare il [portale LUIS (anteprima)](https://preview.luis.ai/) importare il file JSON in una nuova app e assegnare all'app il nome `Pizza app`.

1. Selezionare **Esegui il training** sulla barra di spostamento per eseguire il training dell'app.

## <a name="what-should-the-batch-file-utterances-include"></a>Che cosa devono includere le espressioni del file batch

Il file batch deve includere espressioni con entità di Machine Learning di livello superiore etichettate con la posizione iniziale e finale. Le espressioni non devono far parte degli esempi già presenti nell'app. Devono essere espressioni da prevedere positivamente per finalità ed entità.

È possibile separare i test per finalità e/o entità oppure avere tutti i test (fino a 1000 espressioni) nello stesso file.

## <a name="batch-file"></a>File di batch

Il codice JSON di esempio include un'espressione con un'entità etichettata per illustrare l'aspetto di un file di test. Nei propri test è necessario avere più espressioni con finalità corrette ed entità etichettate di Machine Learning.

1. Creare `pizza-with-machine-learned-entity-test.json` in un editor di testo o [scaricarlo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true).

2. Nel file batch in formato JSON aggiungere un'espressione con la **finalità** che si vuole prevedere nel test.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Eseguire il batch

1. Selezionare **Test** nella barra di spostamento in alto.

2. Selezionare **Batch testing panel** (Pannello test in batch) nel pannello di destra.

3. Selezionare **Import dataset** (Importa set di dati).

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'app LUIS con l'opzione di importazione di set di dati evidenziata](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Scegliere il percorso del file `pizza-with-machine-learned-entity-test.json`.

5. Assegnare un nome al set di dati `pizza test` e selezionare **Done** (Fatto).

    > [!div class="mx-imgBorder"]
    > ![Selezionare il file](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Selezionare il pulsante **Run** (Esegui).

7. Selezionare **See results** (Visualizza risultati).

8. Esaminare i risultati nel grafico e nella legenda.

## <a name="review-batch-results-for-intents"></a>Esaminare i risultati del batch per le finalità

I risultati del test mostrano graficamente il modo in cui le espressioni di test sono state previste rispetto alla versione attiva.

Il grafico relativo al batch visualizza quattro quadranti d risultati. Nella parte destra del grafico è presente un filtro. Il filtro contiene finalità ed entità. Quando si seleziona una [sezione del grafico](luis-concept-batch-test.md#batch-test-results) oppure un punto nel grafico, le espressioni associate vengono visualizzate sotto il grafico.

Quando si passa il mouse sul grafico, la rotellina del mouse consente di ingrandire o ridurre la visualizzazione del grafico. Ciò è utile quando sono presenti molti punti nel grafico strettamente raggruppati.

Il grafico è diviso in quattro quadranti, con due sezioni visualizzate in rosso.

1. Selezionare la finalità **ModifyOrder** nell'elenco dei filtri.

    > [!div class="mx-imgBorder"]
    > ![Selezionare la finalità ModifyOrder nell'elenco dei filtri](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    L'espressione è stimata come **Vero positivo**, il che significa che corrisponde correttamente alla previsione positiva elencata nel file batch.

    > [!div class="mx-imgBorder"]
    > ![Espressione correttamente corrispondente alla previsione positiva](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    I segni di spunta verdi nell'elenco dei filtri indicano anche l'esito positivo del test per ogni finalità. Tutte le altre finalità sono elencate con un punteggio positivo 1/1 perché l'espressione è stata testata rispetto a ogni finalità, come test negativo per le finalità non elencate nel test batch.

1. Selezionare la finalità **Confirmation**. Questa finalità non è elencata nel test batch, pertanto si tratta di un test negativo dell'espressione presente nel test.

    > [!div class="mx-imgBorder"]
    > ![Espressione prevista correttamente come negativa per la finalità non elencata nel file batch](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Il test negativo ha avuto esito positivo, come indicato dal testo verde nel filtro e nella griglia.

## <a name="review-batch-test-results-for-entities"></a>Esaminare i risultati del test batch per le entità

L'entità ModifyOrder, come entità computer con sottoentità, mostra se l'entità di livello superiore corrisponde e visualizza come vengono previste le sottoentità.

1. Selezionare l'entità **ModifyOrder** nell'elenco dei filtri e quindi selezionare il cerchio nella griglia.

1. Sotto il grafico viene visualizzata la previsione dell'entità. La visualizzazione include linee continue per le previsioni che corrispondono alle aspettative e linee punteggiate per quelle che non corrispondono.

    > [!div class="mx-imgBorder"]
    > ![Elemento padre dell'entità previsto correttamente nel file batch](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Ricerca di errori con un test batch

Questa esercitazione ha illustrato come eseguire un test e interpretare i risultati. Non ha descritto la filosofia di test né fornito informazioni su come rispondere ai test non superati.

* Assicurarsi di includere nel test sia le espressioni positive che quelle negative, incluse le espressioni che possono essere previste per una finalità diversa ma correlata.
* Per le espressioni non riuscite, eseguire le attività seguenti, quindi ripetere i test:
    * Esaminare gli esempi correnti per finalità ed entità, verificare se le espressioni di esempio della versione attiva sono corrette sia per l'etichettatura di finalità che di entità.
    * Aggiungere funzionalità che consentono all'app di prevedere finalità ed entità
    * Aggiungere più espressioni di esempio positive
    * Esaminare l'equilibrio delle espressioni di esempio tra finalità

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Passaggio successivo

In questa esercitazione è stato usato un test batch per convalidare il modello corrente.

> [!div class="nextstepaction"]
> [Informazioni sui criteri](luis-tutorial-pattern.md)

