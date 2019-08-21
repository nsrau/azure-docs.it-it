---
title: Test batch-LUIS
titleSuffix: Azure Cognitive Services
description: Questa esercitazione illustra come usare un test in batch per individuare i problemi di previsione delle espressioni nell'app e corregerli.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 45520d39c822c734e3fc725bca3375e93983a118
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637463"
---
# <a name="tutorial-batch-test-data-sets"></a>Esercitazione: Eseguire test in batch dei set di dati

Questa esercitazione illustra come usare un test in batch per individuare i problemi di previsione delle espressioni nell'app e corregerli.  

L'esecuzione di test in batch consente di convalidare lo stato del modello attivo e sottoposto a training con un set noto di espressioni ed entità etichettate. Nel file batch in formato JSON aggiungere le espressioni e impostare le etichette di entità che è necessario prevedere all'interno dell'espressione. 

Requisiti per il test in batch:

* Numero massimo di 1000 espressioni per ogni test. 
* Assenza di duplicati. 
* Tipi di entità consentiti: solo le entità apprese dal computer di semplici e composite. Il test in batch è utile solo per le finalità e le entità apprese in modo automatico.

Quando si usa un'app diversa rispetto a quella di questa esercitazione, *non* usare le espressioni di esempio già aggiunte a una finalità. 

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importare l'app di esempio
> * Creare un file di test in batch 
> * Eseguire un test in batch
> * Esaminare i risultati del test
> * Correggi errori 
> * Eseguire nuovamente il test in batch

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importare l'app di esempio

Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Seguire questa procedura:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `batchtest`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL. 

4. Eseguire il training dell'app.

## <a name="batch-file"></a>File di batch

1. Creare `HumanResources-jobs-batch.json` in un editor di testo o [scaricarlo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json). 

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

    ![Seleziona file](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Selezionare il pulsante **Run** (Esegui). 

7. Selezionare **See results** (Visualizza risultati).

8. Esaminare i risultati nel grafico e nella legenda.

    [ ![Screenshot dell'app LUIS con i risultati del test in batch](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Esaminare i risultati del batch

Il grafico relativo al batch visualizza quattro quadranti d risultati. Nella parte destra del grafico è presente un filtro. Il filtro contiene Intent ed entità. Quando si seleziona una [sezione del grafico](luis-concept-batch-test.md#batch-test-results) oppure un punto nel grafico, le espressioni associate vengono visualizzate sotto il grafico. 

Quando si passa il mouse sul grafico, la rotellina del mouse consente di ingrandire o ridurre la visualizzazione del grafico. Ciò è utile quando sono presenti molti punti nel grafico strettamente raggruppati. 

Il grafico è diviso in quattro quadranti, con due sezioni visualizzate in rosso. **Queste sono le sezioni a cui prestare attenzione**. 

### <a name="getjobinformation-test-results"></a>Risultati del test GetJobInformation

I risultati del test **GetJobInformation** visualizzati nel filtro mostrano che due delle quattro stime hanno avuto esito positivo. Selezionare il nome **false negative** nel quadrante in basso a sinistra per visualizzare le espressioni sotto il grafico. 

Utilizzare la tastiera, CTRL + E, per passare alla visualizzazione etichetta per visualizzare il testo esatto dell'espressione utente. 

L'espressione `Is there a database position open in Los Colinas?` è contrassegnata come _GetJobInformation_ , ma il modello corrente ha stimato l'espressione come _ApplyForJob_. 

Ci sono quasi tre volte il numero di esempi per **ApplyForJob** rispetto a **GetJobInformation**. Questa uniformità degli enunciati di esempio pondera il vantaggio di **ApplyForJob** Intent, causando una stima errata. 

Si noti che entrambe le finalità hanno lo stesso numero di errori. Una stima non corretta in una finalità influisce anche sull'altra. Entrambe presentano errori perché le espressioni sono state stimate in modo non corretto per una finalità e anche per l'altra. 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>Come correggere l'app

L'obiettivo di questa sezione è quello di avere tutte le espressioni stimate in modo corretto per **GetJobInformation** tramite la correzione dell'app. 

Una correzione apparentemente rapida potrebbe essere quella di aggiungere queste espressioni di file batch alla finalità corretta. Questo non è ciò che si vuole fare. Si desidera che LUIS stimi in modo corretto tali espressioni senza aggiungerle come esempi. 

Può essere opportuno anche rimuovere le espressioni da **ApplyForJob** fino a quando la quantità di espressioni equivale a quella di **GetJobInformation**. In questo modo si potrebbero correggere i risultati del test, ma si impedisce a LUIS di stimare in modo preciso la finalità la volta successiva. 

La correzione consiste nell'aggiungere più espressioni a **GetJobInformation**. Ricordarsi di variare la lunghezza dell'espressione, la scelta di parole e la disposizione di parole, pur continuando a individuare le informazioni sul processo, _non_ applicando il processo.

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

1. Selezionare il pulsante con i puntini di sospensione (***...***) a destra del nome del batch e selezionare **Esegui**. Attendere fino a quando il test in batch non viene completato. Si noti che il pulsante **See results** (Visualizza risultati) è ora di colore verde. Ciò significa che l'intero batch è stato eseguito correttamente.

1. Selezionare **See results** (Visualizza risultati). Per tutte le finalità, le icone a sinistra del nome devono essere verdi. 

## <a name="create-batch-file-with-entities"></a>Creare file di batch con entità 

Per verificare le entità in un test in batch, le entità devono essere etichettate nel file batch JSON. 

La variazione delle entità per il numero di parole totali ([token](luis-glossary.md#token)) può influire sulla qualità della stima. Verificare che i dati di training specificati per la finalità con espressioni etichettate includano una vasta gamma di lunghezze di entità. 

Quando si scrivono i file di batch e se ne esegue il test la prima volta, è consigliabile iniziare con poche espressioni ed entità di cui è noto il funzionamento corretto, nonché con quelle che si ritene essere stimate in modo non corretto. Ciò consente di concentrarsi sulle aree del problema rapidamente. Dopo aver eseguito il test delle finalità **GetJobInformation** e **ApplyForJob** con nomi dell'entità Job diversi, che non erano stati stimati, il file di test in batch è stato sviluppato per verificare se fosse presente un problema di stima per determinati valori dell'entità **Job**. 

Il valore di un'entità **Job**, indicato nelle espressioni di test, è composto in genere da una o due parole, con alcuni esempi di più parole. Se la _propria_ app relativa alle risorse umane è composta in genere da nomi di lavoro costituiti da molte parole, le espressioni di esempio etichettate con l'entità **Job** in questa app non funzionano in modo corretto.

1. Creare `HumanResources-entities-batch.json` in un editor di testo come [VSCode](https://code.visualstudio.com/) o [scaricarlo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json).

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

Il grafico viene aperto con tutte le finalità stimate correttamente. Scorrere verso il basso nel filtro sul lato destro per individuare le stime dell'entità con errori. 

1. Selezionare l'entità **Job** nel filtro.

    ![Stime delle entità di errore nel filtro](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

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

