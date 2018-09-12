---
title: Usare un test in batch per migliorare le previsioni LUIS | Microsoft Docs
titleSuffix: Azure
description: Caricare un test in batch, esaminare i risultati e migliorare le previsioni LUIS tramite modifiche.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 5abaeaee87d54e82df29e75b89c83522b8746730
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158246"
---
# <a name="improve-app-with-batch-test"></a>Migliorare l'app con test in batch

Questa esercitazione illustra come usare un test in batch per individuare i problemi di previsione delle espressioni.  

In questa esercitazione si apprenderà come:

<!-- green checkmark -->
> [!div class="checklist"]
* Creare un file di test in batch 
* Eseguire un test in batch
* Esaminare i risultati del test
* Correggere gli errori 
* Eseguire nuovamente il test in batch

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Se non si dispone dell'app relativa alle risorse umane dell'esercitazione per [esaminare le espressioni endpoint](luis-tutorial-review-endpoint-utterances.md) tutorial, [importare](luis-how-to-start-new-app.md#import-new-app) il file JSON in una nuova app nel sito Web di [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-review-HumanResources.json).

Se si vuole mantenere l'app relativa alle risorse umane originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `batchtest`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

Eseguire il training dell'app.

## <a name="purpose-of-batch-testing"></a>Scopo del test in batch

L'esecuzione di test in batch consente di convalidare lo stato del modello attivo e sottoposto a training con un set noto di espressioni ed entità etichettate. Nel file batch in formato JSON aggiungere le espressioni e impostare le etichette di entità che è necessario prevedere all'interno dell'espressione. 

<!--The recommended test strategy for LUIS uses three separate sets of data: example utterances provided to the model, batch test utterances, and endpoint utterances. --> Quando si usa un'app diversa rispetto a quella di questa esercitazione, verificare che *non* si usino le espressioni di esempio già aggiunte a una finalità. Per verificare le espressioni di test in batch rispetto alle espressioni di esempio, [esportare](luis-how-to-start-new-app.md#export-app) l'app. Confrontare le espressioni di esempio dell'app alle espressioni di test in batch. 

Requisiti per il test in batch:

* Numero massimo di 1000 espressioni per ogni test. 
* Assenza di duplicati. 
* Tipi di entità consentiti: solo entità apprese in modo automatico semplici, gerarchiche (solo elemento padre) e composite. Il test in batch è utile solo per le finalità e le entità apprese in modo automatico.

## <a name="create-a-batch-file-with-utterances"></a>Creare un file batch con espressioni

1. Creare `HumanResources-jobs-batch.json` in un editor di testo come [VSCode](https://code.visualstudio.com/). 

2. Nel file batch in formato JSON aggiungere espressioni con la **finalità** che si desidera prevedere nel test. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorial-batch-testing/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Eseguire il batch

1. Selezionare **Test** nella barra di spostamento in alto. 

2. Selezionare **Batch testing panel** (Pannello test in batch) nel pannello di destra. 

    [ ![Screenshot dell'app LUIS con il pannello di test in batch evidenziato](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Selezionare **Import dataset** (Importa set di dati).

    [ ![Screenshot dell'app LUIS con l'opzione di importazione di set di dati evidenziata](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Scegliere il percorso del file system del file `HumanResources-jobs-batch.json`.

5. Assegnare un nome al set di dati `intents only` e selezionare **Done** (Fatto).

    ![Selezionare il file](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Selezionare il pulsante **Run** (Esegui). Attendere fino a quando il test non viene completato.

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

## <a name="fix-the-app-based-on-batch-results"></a>Correggere l'app in base ai risultati del batch

L'obiettivo di questa sezione è quello di avere tutte le espressioni stimate in modo corretto per **GetJobInformation** tramite la correzione dell'app. 

Una correzione apparentemente rapida potrebbe essere quella di aggiungere queste espressioni di file batch alla finalità corretta. Questo risultato non è tuttavia quello desiderato. Si desidera che LUIS stimi in modo corretto tali espressioni senza aggiungerle come esempi. 

Può essere opportuno anche rimuovere le espressioni da **ApplyForJob** fino a quando la quantità di espressioni equivale a quella di **GetJobInformation**. In questo modo si potrebbero correggere i risultati del test, ma si impedisce a LUIS di stimare in modo preciso la finalità la volta successiva. 

La prima soluzione consiste nell'aggiungere più espressioni a **GetJobInformation**. La seconda soluzione consiste nel ridurre il peso di parole come `resume` e `apply` rispetto alla finalità **ApplyForJob**. 

### <a name="add-more-utterances-to-getjobinformation"></a>Aggiungere altre espressioni a **GetJobInformation**

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

## <a name="verify-the-fix-worked"></a>Verificare il funzionamento delle correzioni

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

1. Creare `HumanResources-entities-batch.json` in un editor di testo come [VSCode](https://code.visualstudio.com/). In alternativa, scaricare [il file](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorial-batch-testing/HumanResources-entities-batch.json) dal repository Github LUIS-Samples.


2. Nel file batch in formato JSON aggiungere una matrice di oggetti che includono espressioni con la **finalità** che si vuole stimare nel test nonché i percorsi di tutte le entità nell'espressione. Poiché un'entità è basata su token, verificare di iniziare e terminare ogni entità con un carattere. Non iniziare o terminare l'espressione con uno spazio. Ciò può causare un errore durante l'importazione del file batch.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorial-batch-testing/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


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

## <a name="what-has-this-tutorial-accomplished"></a>Risultati dell'esercitazione

L'accuratezza della stima dell'app è aumentata grazie alla ricerca di errori nel batch e alla correzione del modello. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sui criteri](luis-tutorial-pattern.md)

