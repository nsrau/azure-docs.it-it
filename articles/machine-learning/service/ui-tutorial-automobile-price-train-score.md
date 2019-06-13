---
title: "Esercitazione: Stimare il prezzo delle automobili con l'interfaccia visiva grafica"
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire il training, assegnare punteggi e distribuire un modello di Machine Learning usando moduli tramite un'interfaccia visiva grafica con trascinamento della selezione. Questa esercitazione è la prima parte di una serie in due parti su come stimare i prezzi delle automobili con la regressione lineare.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: e37e99323c92adad0b9e897af8c276a8ac153371
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515640"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Esercitazione: Stimare il prezzo delle automobili con l'interfaccia visiva grafica

In questa esercitazione verrà esaminato in modo approfondito lo sviluppo di una soluzione di analisi predittiva nell'interfaccia visiva grafica del servizio Azure Machine Learning. Al termine dell'esercitazione si avrà una soluzione che consente di stimare il prezzo di qualsiasi automobile in base alle specifiche tecniche inviate.

Questa esercitazione [continua dalla guida di avvio rapido](ui-quickstart-run-experiment.md) ed è la **prima parte di una serie in due parti**. Tuttavia, non è necessario completare la guida di avvio rapido prima di iniziare.

Nella prima parte della serie di esercitazioni si è appreso come:

> [!div class="checklist"]
> * Importare e pulire i dati (gli stessi passaggi della guida di avvio rapido)
> * Eseguire il training di un modello di Machine Learning
> * Assegnare punteggi e valutare un modello

Nella [seconda parte](ui-tutorial-automobile-price-deploy.md) della serie di esercitazioni si apprenderà come distribuire il modello predittivo come servizio Web di Azure.

> [!NOTE]
> Una versione completa dell'esercitazione è disponibile come esperimento di esempio.
> Nella pagina Experiments (Esperimenti) passare a **Add New (Aggiungi nuovo)**  > **Sample 1 - Regression: Automobile Price Prediction(Basic)**


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Se è già disponibile un'area di lavoro del servizio Azure Machine Learning, passare alla [sezione successiva](#open-the-visual-interface-webpage). Altrimenti crearne una adesso.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Aprire la pagina Web dell'interfaccia visiva grafica

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  

1. Nell'area di lavoro selezionare **Interfaccia visiva grafica**.  Quindi selezionare **Avvia interfaccia visiva grafica**.  

    ![Screenshot del portale di Azure che mostra come accedere all'interfaccia visiva grafica da un'area di lavoro del servizio Azure Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    La pagina Web dell'interfaccia si apre in una nuova finestra del browser.  

## <a name="import-and-clean-your-data"></a>Importare e pulire i dati

La prima operazione da eseguire è la pulizia dei dati. Se è stata completata la guida di avvio rapido, è possibile riutilizzare l'esperimento di preparazione dei dati. In caso contrario, passare alla sezione successiva e [iniziare da un nuovo esperimento](#start-from-a-new-experiment).

### <a name="reuse-the-quickstart-experiment"></a>Riutilizzare l'esperimento della guida di avvio rapido

1. Aprire l'esperimento della guida di avvio rapido.

1. Selezionare **Save As** (Salva con nome) nella parte inferiore della finestra.

1. Assegnare un nuovo nome nella finestra di dialogo popup che viene visualizzata.

    ![Screenshot che mostra come rinominare un esperimento in "Tutorial - Predict Automobile Price"](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. L'esperimento avrà ora un aspetto analogo al seguente:

    ![Screenshot dello stato previsto dell'esperimento. Il set di dati delle automobili si connette al modulo Select Columns (Selezione colonne) che si connette a Clean Missing Data (Pulizia dei dati mancanti)](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

Se è stato riutilizzato l'esperimento della guida di avvio rapido, passare alla sezione successiva e iniziare il [training del modello](#train-the-model).

### <a name="start-from-a-new-experiment"></a>Iniziare da un nuovo esperimento

Se la guida di avvio rapido non è stata completata, seguire questi passaggi per creare rapidamente un nuovo esperimento che importa e pulisce il set di dati delle automobili.

1. Per creare un nuovo esperimento, selezionare **+NEW** (NUOVO) nella parte inferiore della finestra dell'interfaccia visiva grafica.

1. Selezionare **Experiments** >  **Blank Experiment** (Esperimenti > Esperimento vuoto).

1. Selezionare il nome predefinito dell'esperimento, **"Experiment Created on**" (Esperimento creato in data) nella parte superiore dell'area di disegno e cambiarlo con un nome significativo, ad esempio **Automobile price prediction**. Il nome non deve essere univoco.

1. A sinistra dell'area di disegno dell'esperimento è presente una tavolozza di set di dati e moduli. Per trovare i moduli, usare la casella di ricerca nella parte superiore della tavolozza. Digitare **automobile** nella casella di ricerca per trovare il set di dati denominato **Automobile price data (Raw)** . Trascinare il set di dati nell'area di disegno dell'esperimento.

    ![Screenshot che mostra come trovare il set di dati dei prezzi delle automobili](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    Dopo aver recuperato i dati, è possibile aggiungere un modulo che rimuove completamente la colonna **normalized-losses**. Quindi, aggiungere un altro modulo che rimuove qualsiasi riga con dati mancanti.

1. Digitare **select columns** nella casella di ricerca per trovare il modulo **Select Columns in Dataset** (Selezione colonne in set di dati). Trascinare il set di dati nel canvas dell'esperimento. Questo modulo consente di selezionare le colonne di dati da includere o escludere nel modello.

1. Connettere la porta di output del set di dati **Automobile price data (Raw)** (Dati non elaborati sui prezzi delle automobili) alla porta di input del modulo Select Columns in Dataset (Seleziona colonne in set di dati).

    ![Gif animata che mostra come connettere il modulo Automobile Price Data al modulo Select Columns](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Selezionare il modulo Select the Select Columns in Dataset e quindi selezionare **Launch column selector** (Avvia selettore colonna) nel riquadro **Properties** (Proprietà).

   1. A sinistra selezionare **With rules** (Con regole).

   1. Accanto a **Begin With** (Inizia con) selezionare **All columns** (Tutte le colonne). Queste regole indicano a **Select Columns in Dataset** di analizzare tutte le colonne, ad eccezione di quelle che verranno escluse.

   1. Negli elenchi a discesa selezionare **Exclude** (Escludi) e **column names** (nomi di colonne), quindi fare digitare **normalized-lossess** nella casella di testo.

   1. Selezionare il pulsante OK per chiudere il selettore di colonne nell'angolo in basso a destra.

     Il riquadro delle proprietà del modulo **Select Columns in Dataset** indica ora che verranno analizzate tutte le colonne del set di dati, a eccezione di **normalized-losses**.

1. Aggiungere un commento al modulo **Select Columns in Dataset** e immettere il commento "Exclude normalized losses". In tal modo sarà possibile individuare subito l'operazione eseguita dal modulo nell'esperimento.

    ![Screenshot della configurazione corretta del modulo Select Columns](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. Digitare **Clean** (Pulizia) nella casella di ricerca per trovare il modulo **Clean Missing Data** (Pulizia dei dati mancanti). Trascinare il modulo **Clean Missing Data** nell'area di disegno dell'esperimento e connetterlo al modulo **Select Columns in Dataset**.

1. Nel riquadro delle **proprietà** selezionare **Remove entire row** (Rimuovi riga intera) in **modalità Cleaning** (Pulizia). Queste opzioni indicano al modulo **Clean Missing Data** di pulire i dati rimuovendo le righe con valori mancanti. Fare doppio clic sul modulo e digitare il commento "Rimuovi righe valori mancanti".

![Screenshot della configurazione corretta del modulo Clean Missing Data](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

## <a name="train-the-model"></a>Eseguire il training del modello

Una volta preparati i dati, è possibile creare un modello predittivo. Per eseguire il training del modello si useranno i propri dati. Quindi si testerà il modello per verificare la precisione con cui è in grado di stimare i prezzi.

**Classificazione** e **regressione** sono due tipi di algoritmi di Machine Learning sottoposti a supervisione. La **classificazione** stima una risposta da un set di categorie definito, ad esempio un colore (rosso, blu o verde). La **regressione** viene usata per stimare un numero.

Poiché si vuole stimare il prezzo, ovvero un numero, è possibile usare un algoritmo di regressione. Per questo esempio si userà un modello di regressione lineare.

Eseguire il training del modello assegnando un set di dati che include il prezzo. Il modello analizza i dati e cerca le correlazioni tra le caratteristiche di un'automobile e il relativo prezzo. Testare quindi il modello assegnando un set di caratteristiche per le automobili con cui si ha familiarità e verificando la precisione con cui il modello stima il prezzo noto.

Usare i dati per il training e il test del modello dividendoli in set di dati separati.

1. Digitare **split data** nella casella di ricerca per trovare il modulo **Split Data** (Divisione dei dati) a sinistra del modulo **Clean Missing Data**.

1. Selezionare il modulo **Split Data** appena connesso. Nel riquadro delle proprietà impostare su 0,7 l'opzione Fraction of rows in the first ouptut dataset (Frazione di righe nel primo set di dati di output). In questo modo per il training del modello verrà usato il 70% dei dati, mentre il restante 30% verrà usato per i test.

    ![Screenshot della configurazione corretta del riquadro di proprietà In "Split Data" il valore di "Split Rows" (Dividi righe) dovrà essere 0,7 e i valori di Randomized split (Divisione casuale) 0 e False.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Fare doppio clic su **Split Data** e digitare il commento "Split the dataset into training set(0.7) and test set(0.3)"

1. Per selezionare l'algoritmo di apprendimento, cancellare la casella di ricerca della tavolozza dei moduli.

1. Espandere **Machine Learning** e quindi **Initialize Model** (Inizializza modello). Verranno visualizzate diverse categorie di moduli che possono essere usate per inizializzare gli algoritmi di Machine Learning.

1. Per questo esperimento, selezionare il modulo **Regression** > **Linear Regression** (Regressione - Regressione lineare) e trascinarlo nell'area di disegno.

    ![Screenshot della configurazione corretta del riquadro di proprietà In "Split Data" il valore di "Split Rows" (Dividi righe) dovrà essere 0,7 e i valori di Randomized split (Divisione casuale) 0 e False.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Trovare e trascinare il modulo **Train Model** (Training modello) nell'area di disegno dell'esperimento. Connettere l'output del modulo Linear Regression all'input sinistro del modulo Train Model e l'output dei dati di training (porta sinistra) del modulo **Split Data** all'input destro del modulo **Train Model**.

    ![Screenshot della configurazione corretta del modulo Train Model Il modulo Linear Regression si connette alla porta sinistra e il modulo Split Data alla porta destra del modulo Train Model](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Selezionare il modulo **Train Model**. Nel riquadro delle proprietà selezionare Launch column selector (Avvia selettore colonna) e quindi digitare **price** accanto a **Include column names** (Includi nomi colonne). Il prezzo è il valore che si intende stimare con il modello.

    ![Screenshot della corretta configurazione del modulo Column selector With rules > Include column names > "price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    L'esperimento dovrebbe risultare simile al seguente.
    ![Screenshot della configurazione corretta dell'esperimento dopo l'aggiunta del modulo Train Model](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Eseguire l'esperimento di training

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Assegnare punteggi e valutare il modello

Dopo aver eseguito il training del modello usando il 70% dei dati, è possibile usarlo per assegnare punteggi al restante 30% e verificarne il funzionamento.

1. Digitare **score model** nella casella di ricerca per trovare il modulo **Score Model** (Punteggio del modello) e trascinarlo nell'area di disegno dell'esperimento. Connettere l'output del modulo **Train Model** alla porta di input sinistra del modulo **Score Model**. Connettere l'output dei dati di test (porta destra) del modulo **Split Data** alla porta di input destra di **Score Model**.

1. Digitare **evaluate** nella casella di ricerca per trovare il modulo **Evaluate Model** (Valutazione del modello) e trascinarlo nel canvas dell'esperimento. Connettere l'output del modulo **Score Model** all'input sinistro di **Evaluate Model**. L'esperimento dovrebbe avere un aspetto simile al seguente:

    ![Screenshot della configurazione finale corretta dell'esperimento](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Eseguire l'esperimento con la stessa destinazione di calcolo usata in precedenza.

1. Visualizzare l'output del modulo **Score Model** selezionando la porta di output di **Score Model** e quindi **Visualize** (Visualizza). L'output mostra i valori stimati per il prezzo e i valori noti dai dati di test.

    ![Screenshot della visualizzazione di output con la colonna "Scored Label" evidenziata](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Per visualizzare l'output del modulo Evaluate Model, selezionare la porta di output e quindi Visualize.

    ![Screenshot dei risultati della valutazione per l'esperimento finale.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Per il modello vengono visualizzate le seguenti statistiche:

* **Errore assoluto medio** (MAE): la media degli errori assoluti (un errore è la differenza tra il valore stimato e quello effettivo).
* **Radice dell'errore quadratico medio** (RMSE): Radice quadrata della media degli errori quadratici delle stime effettuate sul set di dati di test.
* **Errore assoluto relativo**: Media degli errori assoluti relativamente alla differenza assoluta tra i valori effettivi e la media di tutti i valori effettivi.
* **Errore quadratico relativo**: Media degli errori quadratici relativamente alla differenza quadratica tra i valori effettivi e la media di tutti i valori effettivi.
* **Coefficiente di determinazione**: Noto anche come valore quadratico R, è una metrica statistica che indica l'esattezza del modello rispetto ai dati.

Per ogni statistica di errore, sono preferibili i valori più piccoli. Un valore più piccolo indica che le stime sono più vicine ai valori effettivi. Per il coefficiente di determinazione, più il valore si avvicina a uno (1,0) più le stime sono precise.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Gestire esperimenti nell'area di lavoro del servizio Azure Machine Learning

Gli esperimenti creati nell'interfaccia visiva grafica possono essere gestiti nell'area di lavoro del servizio Azure Machine Learning. Usare l'area di lavoro per visualizzare informazioni più dettagliate, come le esecuzioni dei singoli esperimenti, i log di diagnostica, i grafici dell'esecuzione e altro ancora.

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  

1. Nell'area di lavoro selezionare **Experiments** (Esperimenti). Quindi selezionare l'esperimento creato.

    ![Screenshot che mostra come passare agli esperimenti nel portale di Azure](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    In questa pagina viene visualizzata una panoramica dell'esperimento e delle ultime esecuzioni.

    ![Screenshot che mostra una panoramica delle statistiche dell'esperimento nel portale di Azure](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Selezionare un numero per visualizzare altri dettagli su una specifica esecuzione.

    ![Screenshot del report dettagliato sulle esecuzioni](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Il report sulle esecuzioni viene aggiornato in tempo reale. Se è stato usato un modulo **Execute Python Script** (Esecuzione di script Python), è possibile specificare di immettere i log dello script nella scheda **Logs**.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte dell'esercitazione sono stati completati questi passaggi:

* Riutilizzare l'esperimento creato nella guida di avvio rapido
* Preparare i dati
* Eseguire il training del modello
* Assegnare punteggi e valutare il modello

Nella seconda parte si è appreso come distribuire il modello come servizio Web di Azure.

> [!div class="nextstepaction"]
> [Continuare a distribuire modelli](ui-tutorial-automobile-price-deploy.md)
