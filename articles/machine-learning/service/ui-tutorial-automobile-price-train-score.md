---
title: "Esercitazione: Stimare il prezzo delle automobili con l'interfaccia visiva grafica"
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training, assegnare punteggi e distribuire un modello di Machine Learning usando moduli tramite un'interfaccia visiva grafica con trascinamento della selezione. Questa esercitazione è la prima parte di una serie in due parti su come stimare i prezzi delle automobili con la regressione lineare.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 08/16/2019
ms.openlocfilehash: 11c65c217ef6c150c47f387f7f80070488a8df89
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996769"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Esercitazione: Stimare il prezzo delle automobili con l'interfaccia visiva grafica

In questa esercitazione in due parti si apprenderà come usare l'interfaccia visiva grafica di Azure Machine Learning per sviluppare e distribuire una soluzione di analisi predittiva che stima il prezzo di qualsiasi automobile. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Nella prima parte verrà configurato l'ambiente, quindi i set di dati e i moduli di analisi in verranno trascinati in un'area di disegno interattiva e collegati tra loro per creare un esperimento. 

Nella prima parte dell'esercitazione si è apprenderà come:

> [!div class="checklist"]
> * Creare un nuovo esperimento
> * Importa dati
> * Preparazione dei dati
> * Eseguire il training di un modello di Machine Learning
> * Valutare un modello di Machine Learning

Nella [seconda parte](ui-tutorial-automobile-price-deploy.md) dell'esercitazione si apprenderà come distribuire il modello predittivo come servizio Web di Azure in modo da poterlo usare per stimare il prezzo di qualsiasi automobile in base alle specifiche tecniche inviate. 

Una versione completa dell'esercitazione è disponibile come esperimento di esempio.

Per trovarla, nella pagina **Experiments** (Esperimenti) selezionare **Add New** (Aggiungi nuovo) e quindi selezionare l'esperimento **Sample 1 - Regression: Automobile Price Prediction(Basic)** .

## <a name="create-a-new-experiment"></a>Creare un nuovo esperimento

Per creare un esperimento di interfaccia visiva grafica è necessaria prima di tutto un'area di lavoro del servizio Azure Machine Learning. In questa sezione viene descritto come creare entrambe queste risorse.

### <a name="create-a-new-workspace"></a>Creazione di una nuova area di lavoro

Se è già disponibile un'area di lavoro di Azure Machine Learning, passare alla sezione successiva.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-an-experiment"></a>Creare un esperimento

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com/).

1. Nell'area di lavoro selezionare **Interfaccia visiva grafica**. Quindi selezionare **Avvia interfaccia visiva grafica**. 

    ![Screenshot del portale di Azure che mostra come accedere all'interfaccia visiva grafica da un'area di lavoro del servizio Azure Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

1. Per creare un nuovo esperimento, selezionare **+NEW** (NUOVO) nella parte inferiore della finestra dell'interfaccia visiva grafica.

1. Selezionare **Blank Experiment** (Esperimento vuoto).

1. Selezionare il nome predefinito dell'esperimento, **"Experiment Created on**" (Esperimento creato in data) nella parte superiore dell'area di disegno e cambiarlo con un nome significativo, ad esempio **"Automobile price prediction"** . Il nome non deve essere univoco.

## <a name="import-data"></a>Importa dati

Machine Learning dipende dai dati. In questa interfaccia sono disponibili diversi set di dati di esempio con cui sperimentare. Per questa esercitazione verrà usato il set di dati di esempio **Automobile price data (Raw)** . 

1. A sinistra dell'area di disegno dell'esperimento è presente una tavolozza di set di dati e moduli. Selezionare **Saved Datasets** (Set di dati salvati) e quindi **Samples** (Esempi) per visualizzare i set di dati di esempio disponibili.

1. Selezionare il set di dati **Automobile price data (raw)** e trascinarlo nell'area di disegno.

   ![Trascinare i dati nell'area di disegno](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. Selezionare le colonne di dati da usare. Digitare **Select** nella casella di ricerca nella parte superiore del pannello per trovare il modulo **Select Columns in Dataset** (Seleziona colonne nel set di dati).

1. Fare clic e trascinare il modulo **Select Columns in Dataset** nell'area di disegno. Rilasciare il modulo sotto il modulo del set di dati.

1. Connettere il set di dati aggiunto in precedenza al modulo **Select Columns in Dataset** facendo clic e trascinando. Trascinare il mouse dalla porta di output del set di dati, ovvero il piccolo cerchio nella parte inferiore del set di dati nell'area di disegno, fino alla porta di input del modulo **Select Columns in Dataset**, ovvero il piccolo cerchio nella parte superiore del modulo.

    > [!TIP]
    > Viene creato un flusso di dati tramite l'esperimento quando si connette la porta di output di un modulo a una porta di input di un altro.
    >

    ![Connettere i moduli](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    Il punto esclamativo rosso indica che le proprietà del modulo non sono ancora state impostate.

1. Selezionare il modulo **Select Columns in Dataset** (Seleziona colonne nel set di dati).

1. Nel riquadro **Properties** (Proprietà) a destra dell'area di disegno selezionare **Edit columns** (Modifica colonne).

    Nella finestra di dialogo **Select columns** (Seleziona colonne) selezionare **ALL COLUMNS** (TUTTE LE COLONNE) e includere **tutte le funzionalità**. La finestra di dialogo dovrebbe essere simile alla seguente:

     ![column-selector](./media/ui-tutorial-automobile-price-train-score/select-all.gif)

1. Nell'angolo in basso a destra fare clic su **OK** per chiudere il selettore di colonne.

### <a name="run-the-experiment"></a>Eseguire l'esperimento

In qualsiasi momento fare clic sulla porta di output di un set di dati o di un modulo per visualizzare l'aspetto dei dati in tale punto specifico del flusso di dati. Se l'opzione **Visualize** (Visualizza) è disabilitata, è prima necessario eseguire l'esperimento.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Quando la destinazione di calcolo è disponibile, viene eseguito l'esperimento. Al termine dell'esecuzione, viene visualizzato un segno di spunta verde su ogni modulo.


### <a name="visualize-the-data"></a>Visualizzare i dati

Ora che è stato eseguito l'esperimento iniziale, è possibile visualizzare i dati per comprendere meglio il set di dati disponibile.

1. Selezionare la porta di output nella parte inferiore del modulo **Select Columns in Dataset** (Seleziona colonne nel set di dati) e quindi selezionare **Visualize** (Visualizza).

1. Fare clic su colonne diverse nella finestra dei dati per visualizzare le informazioni relative alle singole colonne.

    In questo set di dati ogni riga rappresenta un'automobile e le variabili associate a ogni automobile sono rappresentate da colonne. In questo set di dati sono presenti 205 righe e 26 colonne.

    Ogni volta che si fa clic su una colonna di dati, sulla sinistra vengono visualizzate le informazioni sulle **statistiche** e l'immagine della **visualizzazione** di tale colonna.

    [![Visualizzare in anteprima i dati](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)](./media/ui-tutorial-automobile-price-train-score/preview-data.gif#lightbox)

1. Fare clic su ogni colonna per ottenere altre informazioni sul set di dati e stabilire se queste colonne saranno utili per stimare il prezzo di un'automobile.

## <a name="prepare-data"></a>Preparazione dei dati

In genere, prima di poter analizzare un set di dati è necessario pre-elaborarlo. Durante la visualizzazione del set di dati si potrebbe aver notato che mancano alcuni valori. Per consentire al modello di analizzare correttamente i dati, è necessario eseguire la pulizia di questi valori mancanti. Verranno rimosse le righe con i valori mancanti. Inoltre, la colonna **normalized-losses** ha molti valori mancanti, pertanto verrà esclusa completamente dal modello.

> [!TIP]
> La pulizia dei valori mancanti dai dati di input è un prerequisito all'uso della maggior parte dei moduli.

### <a name="remove-column"></a>Rimuovere la colonna

Rimuovere prima completamente la colonna **normalized-losses**.

1. Selezionare il modulo **Select Columns in Dataset** (Seleziona colonne nel set di dati).

1. Nel riquadro **Properties** (Proprietà) a destra dell'area di disegno selezionare **Edit columns** (Modifica colonne).

    * Lasciare selezionate **With rules** (Con regole) e **ALL COLUMNS** (TUTTE LE COLONNE).

    * Negli elenchi a discesa selezionare **Escludi** e **nomi colonna**, quindi fare clic all'interno della casella di testo. Digitare **normalized-losses**.

    * Nell'angolo in basso a destra fare clic su **OK** per chiudere il selettore di colonne.

    ![Escludere una colonna](./media/ui-tutorial-automobile-price-train-score/exclude-column.gif)
        
    Il riquadro delle proprietà del modulo Select Columns in Dataset (Seleziona colonne in set di dati) indica che verranno analizzate tutte le colonne del set di dati ad eccezione di **normalized-losses**.
        
    Il riquadro delle proprietà mostra che la colonna **normalized-losses** è stata esclusa.

1. Fare doppio clic sul modulo **Select Columns in Dataset** (Seleziona colonne in set di dati) e immettere il commento "Escludi perdite normalizzate". 
    
    Dopo aver digitato il commento, fare clic all'esterno del modulo. Viene visualizzata una freccia verso il basso per indicare che il modulo contiene un commento.

1. Fare clic sulla freccia verso il basso per visualizzare il commento.

    Nel modulo è ora visualizzata una freccia verso l'alto per nascondere il commento.
        
    ![Commenti](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Pulire i dati mancanti

Quando si esegue il training di un modello, occorre fare qualcosa in merito ai dati mancanti. In questo caso si aggiungerà un modulo per rimuovere eventuali righe in cui mancano dei dati.

1. Digitare **Clean** (Pulizia) nella casella di ricerca per trovare il modulo **Clean Missing Data** (Pulisci dati mancanti).

1. Trascinare il modulo **Clean Missing Data** (Pulisci dati mancanti) nell'area di disegno dell'esperimento e connetterlo al modulo **Select Columns in Dataset** (Seleziona colonne in set di dati). 

1. Nel riquadro delle proprietà selezionare **Remove entire row** (Rimuovi riga intera) in **Cleaning mode** (Modalità pulizia).

1. Fare doppio clic sul modulo e digitare il commento "Rimuovi righe valori mancanti".

    L'esperimento avrà ora un aspetto analogo al seguente:
    
    ![select-column](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="train-a-machine-learning-model"></a>Eseguire il training di un modello di Machine Learning

Una volta preparati i dati, è possibile creare un modello predittivo. Per eseguire il training del modello si useranno i propri dati. Quindi si testerà il modello per verificare la precisione con cui è in grado di stimare i prezzi.

### <a name="select-an-algorithm"></a>Selezionare un algoritmo

**Classificazione** e **regressione** sono due tipi di algoritmi di Machine Learning sottoposti a supervisione. La **classificazione** stima una risposta da un set di categorie definito, ad esempio un colore (rosso, blu o verde). La **regressione** viene usata per stimare un numero.

Poiché si vuole stimare il prezzo, ovvero un numero, è possibile usare un algoritmo di regressione. Per questo esempio si userà un modello di regressione lineare.

### <a name="split-the-data"></a>Dividere i dati

Usare i dati per il training e il test del modello dividendoli in set di dati separati.

1. Digitare **split data** nella casella di ricerca per trovare il modulo **Split Data** (Divisione dei dati) a sinistra del modulo **Clean Missing Data**.

1. Selezionare il modulo **Split Data**. Nel riquadro delle proprietà impostare su 0,7 l'opzione Fraction of rows in the first ouptut dataset (Frazione di righe nel primo set di dati di output). In questo modo per il training del modello verrà usato il 70% dei dati, mentre il restante 30% verrà usato per i test.

1. Fare doppio clic su **Split Data** e digitare il commento "Split the dataset into training set(0.7) and test set(0.3)"

### <a name="train-the-model"></a>Eseguire il training del modello

Eseguire il training del modello assegnando un set di dati che include il prezzo. Il modello analizza i dati e cerca le correlazioni tra le caratteristiche di un'automobile e il relativo prezzo.

1. Per selezionare l'algoritmo di apprendimento, cancellare la casella di ricerca della tavolozza dei moduli.

1. Espandere **Machine Learning** e quindi **Initialize Model** (Inizializza modello). Verranno visualizzate diverse categorie di moduli che possono essere usate per inizializzare gli algoritmi di Machine Learning.

1. Per questo esperimento, selezionare il modulo **Regression** > **Linear Regression** (Regressione - Regressione lineare) e trascinarlo nell'area di disegno.

1. Trovare e trascinare il modulo **Train Model** (Training modello) nell'area di disegno dell'esperimento. Connettere l'output del modulo Linear Regression all'input sinistro del modulo Train Model e l'output dei dati di training (porta sinistra) del modulo **Split Data** all'input destro del modulo **Train Model**.

    ![Screenshot della configurazione corretta del modulo Train Model Il modulo Linear Regression si connette alla porta sinistra e il modulo Split Data alla porta destra del modulo Train Model](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Selezionare il modulo **Train Model**. Nel riquadro delle proprietà selezionare Launch column selector (Avvia selettore colonna) e quindi digitare **price** accanto a **Include column names** (Includi nomi colonne). Il prezzo è il valore che si intende stimare con il modello.

    ![Screenshot della corretta configurazione del modulo Column selector With rules > Include column names > "price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    L'esperimento dovrebbe ora risultare simile al seguente:

    ![Screenshot della configurazione corretta dell'esperimento dopo l'aggiunta del modulo Train Model](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Valutare un modello di Machine Learning

Dopo aver eseguito il training del modello usando il 70% dei dati, è possibile usarlo per assegnare punteggi al restante 30% e verificarne il funzionamento.

1. Digitare **score model** nella casella di ricerca per trovare il modulo **Score Model** (Punteggio del modello) e trascinarlo nell'area di disegno dell'esperimento. Connettere l'output del modulo **Train Model** alla porta di input sinistra del modulo **Score Model**. Connettere l'output dei dati di test (porta destra) del modulo **Split Data** alla porta di input destra di **Score Model**.

1. Digitare **evaluate** nella casella di ricerca per trovare il modulo **Evaluate Model** (Valutazione del modello) e trascinarlo nel canvas dell'esperimento. Connettere l'output del modulo **Score Model** all'input sinistro di **Evaluate Model**. L'esperimento dovrebbe avere un aspetto simile al seguente:

    ![Screenshot della configurazione finale corretta dell'esperimento](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Eseguire l'esperimento usando la risorsa di calcolo creata in precedenza.

1. Visualizzare l'output del modulo **Score Model** selezionando la porta di output di **Score Model** e quindi **Visualize** (Visualizza). L'output mostra i valori stimati per il prezzo e i valori noti dai dati di test.

    ![Screenshot della visualizzazione di output con la colonna "Scored Label" evidenziata](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Per visualizzare l'output del modulo **Evaluate Model**, selezionare la porta di output e quindi **Visualize**.

    ![Screenshot dei risultati della valutazione per l'esperimento finale.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Per il modello vengono visualizzate le seguenti statistiche:

* **Errore assoluto medio** (MAE): la media degli errori assoluti (un errore è la differenza tra il valore stimato e quello effettivo).
* **Radice dell'errore quadratico medio** (RMSE): Radice quadrata della media degli errori quadratici delle stime effettuate sul set di dati di test.
* **Errore assoluto relativo**: Media degli errori assoluti relativamente alla differenza assoluta tra i valori effettivi e la media di tutti i valori effettivi.
* **Errore quadratico relativo**: Media degli errori quadratici relativamente alla differenza quadratica tra i valori effettivi e la media di tutti i valori effettivi.
* **Coefficiente di determinazione**: Noto anche come valore quadratico R, è una metrica statistica che indica l'esattezza del modello rispetto ai dati.

Per ogni statistica di errore, sono preferibili i valori più piccoli. Un valore più piccolo indica che le stime sono più vicine ai valori effettivi. Per il coefficiente di determinazione, più il valore si avvicina a uno (1,0) più le stime sono precise.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte dell'esercitazione sono stati completati questi passaggi:

* Creare un esperimento
* Preparare i dati
* Eseguire il training del modello
* Assegnare punteggi e valutare il modello

Nella seconda parte si è appreso come distribuire il modello come servizio Web di Azure.

> [!div class="nextstepaction"]
> [Continuare a distribuire modelli](ui-tutorial-automobile-price-deploy.md)
