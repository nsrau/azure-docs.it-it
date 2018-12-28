---
title: Test in batch
titleSuffix: Azure Cognitive Services
description: Questa esercitazione illustra come usare un test in batch per individuare i problemi di previsione delle espressioni nell'app e correggerli.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 06981972dbdb95b8597bab5028c2d86e0594caf3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106040"
---
# <a name="tutorial-2-batch-test-data-sets"></a>Esercitazione 2: Eseguire test in batch dei set di dati

Questa esercitazione illustra come usare un test in batch per individuare i problemi di previsione delle espressioni nell'app e correggerli.  

L'esecuzione di test in batch consente di convalidare lo stato del modello attivo e sottoposto a training con un set noto di espressioni ed entità etichettate. Nel file batch in formato JSON aggiungere le espressioni e impostare le etichette di entità che è necessario prevedere all'interno dell'espressione. 

Requisiti per il test in batch:

* Numero massimo di 1000 espressioni per ogni test. 
* Assenza di duplicati. 
* Tipi di entità consentiti: solo entità apprese in modo automatico semplici, gerarchiche (solo elemento padre) e composite. Il test in batch è utile solo per le finalità e le entità apprese in modo automatico.

Quando si usa un'app diversa rispetto a quella di questa esercitazione, *non* usare le espressioni di esempio già aggiunte a una finalità. 

**In questa esercitazione si apprenderà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usare l'app di esercitazione esistente
> * Creare un file di test in batch 
> * Eseguire un test in batch
> * Esaminare i risultati del test
> * Correggere gli errori 
> * Eseguire nuovamente il test in batch

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente

Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `batchtest`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL. 

4. Eseguire il training dell'app.

## <a name="batch-file"></a>File di batch

1. Creare `HumanResources-jobs-batch.json` in un editor di testo o [scaricarlo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json). 

2. Nel file batch in formato JSON aggiungere espressioni con la **finalità** che si desidera prevedere nel test. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Eseguire il batch

1. Selezionare **Test** nella barra di spostamento in alto. 

2. Selezionare **Batch testing panel** (Pannello test in batch) nel pannello di destra. 

    [ ![Screenshot dell'app LUIS con il pannello di test in batch evidenziato](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Selezionare **Import dataset** (Importa set di dati).

    [ ![Screenshot dell'app LUIS con l'opzione di importazione di set di dati evidenziata](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Scegliere il percorso del file `HumanResources-jobs-batch.json`.

5. Assegnare un nome al set di dati `intents only` e selezionare **Done** (Fatto).

    ![Selezionare il file](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Selezionare il pulsante **Run** (Esegui). 

7. Selezionare **See results** (Visualizza risultati).

8. Esaminare i risultati nel grafico e nella legenda.

    [ ![Screenshot dell'app LUIS con i risultati del test in batch](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Esaminare i risultati del batch

Il grafico relativo al batch visualizza quattro quadranti d risultati. Nella parte destra del grafico è presente un filtro. L'impostazione predefinita del filtro è sulla prima finalità dell'elenco. Il filtro contiene tutte le finalità e solo le entità semplici, gerarchiche (solo elemento padre) e composite. Quando si seleziona una [sezione del grafico](luis-concept-batch-test.md#batch-test-results) oppure un punto nel grafico, le espressioni associate vengono visualizzate sotto il grafico. 

Quando si passa il mouse sul grafico, la rotellina del mouse consente di ingrandire o ridurre la visualizzazione del grafico. Ciò è utile quando sono presenti molti punti nel grafico strettamente raggruppati. 

Il grafico è diviso in quattro quadranti, con due sezioni visualizzate in rosso. **Queste sono le sezioni a cui prestare attenzione**. 

### <a name="getjobinformation-test-results"></a>Risultati del test GetJobInformation

I risultati del test **GetJobInformation** visualizzati nel filtro mostrano che due delle quattro stime hanno avuto esito positivo. Selezionare il nome **False positive** (Falso positivo) sopra il quadrante superiore destro per visualizzare le espressioni sotto il grafico. 

![Espressioni di test in batch LUIS](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Perché sono presenti due espressioni previste come **ApplyForJob** anziché come finalità **GetJobInformation** corretta? Le due finalità sono strettamente correlate in termini di scelta e di disposizione delle parole. Esistono anche esempi per **ApplyForJob** tre volte più numerosi rispetto a **GetJobInformation**. Questo disuguaglianza di espressioni di esempio pesa in favore della finalità **ApplyForJob**. 

Si noti che entrambe le finalità hanno lo stesso numero di errori. Una stima non corretta in una finalità influisce anche sull'altra. Entrambe presentano errori perché le espressioni sono state stimate in modo non corretto per una finalità e anche per l'altra. 

![Errori di filtro di test in batch LUIS](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

Le espressioni corrispondenti nella parte superiore della sezione **False positive** (Falso positivo) sono `Can I apply for any database jobs with this resume?` e `Can I apply for any database jobs with this resume?`. Per la prima espressione, la parola `resume` è stata usata solo in **ApplyForJob**. Per la seconda espressione, la parola `apply` è stata usata solo per la finalità **ApplyForJob**.

## <a name="fix-the-app"></a>Correggere l'app

L'obiettivo di questa sezione è quello di avere tutte le espressioni stimate in modo corretto per **GetJobInformation** tramite la correzione dell'app. 

Una correzione apparentemente rapida potrebbe essere quella di aggiungere queste espressioni di file batch alla finalità corretta. Questo risultato non è tuttavia quello desiderato. Si desidera che LUIS stimi in modo corretto tali espressioni senza aggiungerle come esempi. 

Può essere opportuno anche rimuovere le espressioni da **ApplyForJob** fino a quando la quantità di espressioni equivale a quella di **GetJobInformation**. In questo modo si potrebbero correggere i risultati del test, ma si impedisce a LUIS di stimare in modo preciso la finalità la volta successiva. 

La prima soluzione consiste nell'aggiungere più espressioni a **GetJobInformation**. La seconda soluzione consiste nel ridurre il peso di parole come `resume` e `apply` rispetto alla finalità **ApplyForJob**. 

### <a name="add-more-utterances"></a>Aggiungere altre espressioni

1. Chiudere il pannello di test in batch selezionando il pulsante **Test** nel pannello di navigazione superiore. 

2. Selezionare **GetJobInformation** nell'elenco di finalità. 

3. Aggiungere altre espressioni modificate in lunghezza e scelta e disposizione delle parole, assicurandosi di includere i termini `resume`, `c.v.` e `apply`:

    |Espressioni di esempio per la finalità **GetJobInformation**|
    |--|
    |Il nuovo lavoro in magazzino per uno stocker richiede l'invio di un curriculum?|
    |Dove si trovano i lavori manuali oggi?|
    |Ho sentito che era disponibile un lavoro di codifica in campo medico che richiede un curriculum.|
    |Vorrei che gli universitari in cerca di lavoro scrivessero il proprio curriculum. |
    |Ecco il mio curriculum, sono alla ricerca di un nuovo posto nelle università che preveda l'uso del computer.|
    |Quali posizioni sono disponibili nel settore dell'assistenza familiare?|
    |È disponibile un posto di stagista presso il giornale?|
    |Il mio curriculum dimostra che sono esperto nell'analisi di approvvigionamento, budget e perdite di denaro. È disponibile un posto per questo tipo di lavoro?|
    |Dove si trovano i lavori nel settore della perforazione oggi?|
    |Ho lavorato 8 anni come conducente EMS. Sono disponibili nuovi lavori?|
    |I nuovi lavori in campo alimentare richiedono una domanda?|
    |Quanti nuovi lavori nel settore cantieristico sono disponibili?|
    |È disponibile un nuovo posto nel settore delle Risorse Umane per le relazioni e le negoziazioni con la manodopera?|
    |Ho un master nel settore della conservazione dei beni culturali. Sono disponibili nuove posizioni?|
    |Sono disponibili lavori per babysitter per bambini di 13 anni in città oggi?|

    Non assegnare un'etichetta all'entità **Job** nelle espressioni. Questa sezione dell'esercitazione è incentrata solo stima delle finalità.

4. Eseguire il training dell'app selezionando **Train** (Training) nel riquadro di spostamento in alto a destra.

## <a name="verify-the-new-model"></a>Verificare il nuovo modello

Per verificare che le espressioni del test in batch siano stimate correttamente per la finalità, eseguire nuovamente il test in batch.

1. Selezionare **Test** nella barra di spostamento in alto. Se i risultati del batch sono ancora aperti, selezionare **Back to list** (Torna a elenco).  

2. Selezionare i puntini di sospensione (***...*** ) a destra del nome del batch e selezionare **Run Dataset** (Esegui set di dati). Attendere fino a quando il test in batch non viene completato. Si noti che il pulsante **See results** (Visualizza risultati) è ora di colore verde. Ciò significa che l'intero batch è stato eseguito correttamente.

3. Selezionare **See results** (Visualizza risultati). Per tutte le finalità, le icone a sinistra del nome devono essere verdi. 

    ![Schermata di LUIS con il pulsante di risultati batch evidenziato](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)

## <a name="create-batch-file-with-entities"></a>Creare file di batch con entità 

Per verificare le entità in un test in batch, le entità devono essere etichettate nel file batch JSON. Vengono usate solo le entità apprese in modo automatico, ovvero di tipo semplice, gerarchico, (solo elemento padre) e composite. Non aggiungere entità non apprese in modo automatico perché si trovano sempre tramite espressioni regolari o corrispondenze di testo esplicite.

La variazione delle entità per il numero di parole totali ([token](luis-glossary.md#token)) può influire sulla qualità della stima. Verificare che i dati di training specificati per la finalità con espressioni etichettate includano una vasta gamma di lunghezze di entità. 

Quando si scrivono i file di batch e se ne esegue il test la prima volta, è consigliabile iniziare con poche espressioni ed entità di cui è noto il funzionamento corretto, nonché con quelle che si ritene essere stimate in modo non corretto. Ciò consente di concentrarsi sulle aree del problema rapidamente. Dopo aver eseguito il test delle finalità **GetJobInformation** e **ApplyForJob** con nomi dell'entità Job diversi, che non erano stati stimati, il file di test in batch è stato sviluppato per verificare se fosse presente un problema di stima per determinati valori dell'entità **Job**. 

Il valore di un'entità **Job**, indicato nelle espressioni di test, è composto in genere da una o due parole, con alcuni esempi di più parole. Se la _propria_ app relativa alle risorse umane è composta in genere da nomi di lavoro costituiti da molte parole, le espressioni di esempio etichettate con l'entità **Job** in questa app non funzionano in modo corretto.

1. Creare `HumanResources-entities-batch.json` in un editor di testo come [VSCode](https://code.visualstudio.com/) o [scaricarlo](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json).


2. Nel file batch in formato JSON aggiungere una matrice di oggetti che includono espressioni con la **finalità** che si vuole stimare nel test nonché i percorsi di tutte le entità nell'espressione. Poiché un'entità è basata su token, verificare di iniziare e terminare ogni entità con un carattere. Non iniziare o terminare l'espressione con uno spazio. Ciò può causare un errore durante l'importazione del file batch.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Eseguire il batch con le entità

1. Selezionare **Test** nella barra di spostamento in alto. 

2. Selezionare **Batch testing panel** (Pannello test in batch) nel pannello di destra. 

3. Selezionare **Import dataset** (Importa set di dati).

4. Scegliere il percorso del file system del file `HumanResources-entities-batch.json`.

5. Assegnare un nome al set di dati `entities` e selezionare **Done** (Fatto).

6. Selezionare il pulsante **Run** (Esegui). Attendere fino a quando il test non viene completato.

7. Selezionare **See results** (Visualizza risultati).

## <a name="review-entity-batch-results"></a>Esaminare i risultati del batch di entità

Il grafico viene aperto con tutte le finalità stimate correttamente. Scorrere verso il basso nel filtro a destra per trovare le stime di entità con errori. 

1. Selezionare l'entità **Job** nel filtro.

    ![Stime di entità con errori nel filtro](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    Il grafico viene modificato per visualizzare le stime di entità. 

2. Selezionare **False Negative** (Falso negativo) nel quadrante in basso a sinistra del grafico. Usare quindi la combinazione di tasti CTRL + E per passare alla visualizzazione token. 

    [ ![Visualizzazione token delle stime di entità](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    L'analisi delle espressioni sotto il grafico rivela un errore di coerenza quando il nome dell'entità Job include `SQL`. L'analisi delle espressioni di esempio e dell'elenco di frasi dell'entità Job indica che SQL viene usato solo una volta e solo come parte di un nome di lavoro maggiore, `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Correggere l'app in base ai risultati batch dell'entità

La correzione dell'app richiede che LUIS determini in modo corretto le variazioni dei lavori per SQL. Sono disponibili diverse opzioni per la correzione. 

* Aggiungere in modo esplicito più espressioni di esempio che usano SQL e assegnare un'etichetta a tali parole come entità Job. 
* Aggiungere in modo esplicito più lavori per SQL all'elenco di frasi

Queste attività vengono lasciate all'utente.

L'aggiunta di un [criterio](luis-concept-patterns.md) prima che l'entità venga stimata correttamente non permette di risolvere il problema. Ciò avviene perché il criterio non corrisponde fino a quando non vengono rilevate tutte le entità nel criterio stesso. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

L'esercitazione si è servita di un test di batch per individuare i problemi con il modello attuale. Il modello è stato corretto e testato nuovamente con il file batch per verificare che la modifica sia avvenuta correttamente.

> [!div class="nextstepaction"]
> [Informazioni sui criteri](luis-tutorial-pattern.md)

