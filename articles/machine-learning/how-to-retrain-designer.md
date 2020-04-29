---
title: Ripetere il training dei modelli con Azure Machine Learning Designer (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come ripetere il training dei modelli con le pipeline pubblicate in Azure Machine Learning Designer (anteprima).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: 721e5414fc4753cd5d58a17fc7ed51ea99868778
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80810377"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Eseguire nuovamente il training di modelli con la finestra di progettazione di Azure Machine Learning (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Questo articolo illustra come usare Azure Machine Learning Designer per ripetere il training di un modello di machine learning. Si utilizzeranno le pipeline pubblicate per automatizzare il flusso di lavoro e impostare i parametri per eseguire il training del modello sui nuovi dati. 

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Eseguire il training di un modello di Machine Learning.
> * Creare un parametro della pipeline.
> * Pubblicare la pipeline di training.
> * Ripetere il training del modello con nuovi parametri.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning con Enterprise SKU.
* Set di dati accessibile alla finestra di progettazione. I possibili valori sono i seguenti:
   * Un set di dati registrato Azure Machine Learning
    
     **o**
   * Un file di dati archiviato in un archivio dati Azure Machine Learning.
   
Per informazioni sull'accesso ai dati tramite la finestra [di progettazione, vedere come importare dati nella finestra di progettazione](how-to-designer-import-data.md).

Questo articolo presuppone inoltre che l'utente abbia una conoscenza di base della creazione di pipeline nella finestra di progettazione. Per un'introduzione guidata, completare l' [esercitazione](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Pipeline di esempio

La pipeline usata in questo articolo è una versione modificata di [Sample 3: stima del reddito](samples-designer.md#classification-samples). La pipeline usa il modulo [Import Data](algorithm-module-reference/import-data.md) anziché il set di dati di esempio per illustrare come eseguire il training dei modelli usando i propri dati.

![Screenshot che mostra la pipeline di esempio modificata con una casella che evidenzia il modulo Import Data](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Creare un parametro della pipeline

Creare parametri della pipeline per impostare dinamicamente le variabili in fase di esecuzione. Per questo esempio, il percorso dei dati di training verrà modificato da un valore fisso a un parametro, in modo che sia possibile ripetere il training del modello su dati diversi.

1. Selezionare il modulo **Import Data** .

    > [!NOTE]
    > In questo esempio viene usato il modulo Import data per accedere ai dati in un archivio dati registrato. Tuttavia, se si usano modelli di accesso ai dati alternativi, è possibile seguire una procedura simile.

1. Nel riquadro dei dettagli del modulo a destra dell'area di disegno selezionare l'origine dati.

1. Immettere il percorso dei dati. È anche possibile selezionare **Esplora percorso** per esplorare l'albero dei file. 

1. MouseOver il campo **percorso** e selezionare i puntini di sospensione sopra il campo **percorso** visualizzato.

    ![Screenshot che illustra come creare un parametro della pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selezionare **Aggiungi a parametro pipeline**.

1. Specificare un nome di parametro e un valore predefinito.

   > [!NOTE]
   > È possibile esaminare e modificare i parametri della pipeline selezionando l'icona dell'ingranaggio **Impostazioni** accanto al titolo della bozza della pipeline. 

1. Selezionare **Salva**.

1. Inviare l'esecuzione della pipeline.

## <a name="find-a-trained-model"></a>Trovare un modello sottoposto a training

La finestra di progettazione Salva tutti gli output della pipeline, inclusi i modelli sottoposti a training, nell'account di archiviazione predefinito. È anche possibile accedere ai modelli sottoposti a training direttamente nella finestra di progettazione:

1. Attendere il completamento dell'esecuzione della pipeline.
1. Selezionare il modulo **Train Model**.
1. Nel riquadro dei dettagli del modulo a destra dell'area di disegno selezionare **output + log**.
1. È possibile trovare il modello in **altri output** insieme ai log di esecuzione.
1. In alternativa, selezionare l'icona **Visualizza output** . Da qui è possibile seguire le istruzioni nella finestra di dialogo per passare direttamente all'archivio dati. 

![Screenshot che illustra come scaricare il modello sottoposto a training](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Pubblicare una pipeline di training

Pubblicare una pipeline in un endpoint della pipeline per riutilizzare facilmente le pipeline in futuro. Un endpoint della pipeline crea un endpoint REST per richiamare la pipeline in futuro. In questo esempio, l'endpoint della pipeline consente di riutilizzare la pipeline per ripetere il training di un modello su dati diversi.

1. Selezionare **pubblica** sopra l'area di disegno della finestra di progettazione.
1. Selezionare o creare un endpoint della pipeline.

   > [!NOTE]
   > È possibile pubblicare più pipeline in un singolo endpoint. A ogni pipeline di un determinato endpoint viene assegnato un numero di versione, che è possibile specificare quando si chiama l'endpoint della pipeline.

1. Selezionare **Pubblica**.

## <a name="retrain-your-model"></a>Ripetere il training del modello

Ora che si dispone di una pipeline di training pubblicata, è possibile usarla per ripetere il training del modello sui nuovi dati. È possibile inviare le esecuzioni da un endpoint della pipeline dall'area di lavoro di studio o a livello di codice.

### <a name="submit-runs-by-using-the-designer"></a>Inviare le esecuzioni tramite la finestra di progettazione

Usare i passaggi seguenti per inviare un endpoint della pipeline con parametri eseguito dalla finestra di progettazione:

1. Passare alla pagina **endpoint** nell'area di lavoro di studio.
1. Selezionare la scheda **endpoint della pipeline** . Selezionare quindi l'endpoint della pipeline.
1. Selezionare la scheda **pipeline pubblicate** . Selezionare quindi la versione della pipeline che si desidera eseguire.
1. Selezionare **Submit** (Invia).
1. Nella finestra di dialogo imposta è possibile specificare i valori dei parametri per l'esecuzione. Per questo esempio, aggiornare il percorso dei dati per eseguire il training del modello usando un set di dati diverso da US.

![Screenshot che illustra come configurare un'esecuzione di pipeline con parametri nella finestra di progettazione](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Inviare esecuzioni tramite codice

È possibile trovare l'endpoint REST di una pipeline pubblicata nel pannello panoramica. Chiamando l'endpoint, è possibile ripetere il training della pipeline pubblicata.

Per eseguire una chiamata REST, è necessaria un'intestazione di autenticazione del tipo di porta OAuth 2,0. Per informazioni sulla configurazione dell'autenticazione per l'area di lavoro e sulla creazione di una chiamata REST con parametri, vedere [creare una pipeline di Azure Machine Learning per l'assegnazione dei punteggi di batch](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare un endpoint della pipeline di training con parametri usando la finestra di progettazione.

Per una procedura dettagliata completa su come distribuire un modello per eseguire stime, vedere l'esercitazione di [progettazione](tutorial-designer-automobile-price-train-score.md) per eseguire il training e la distribuzione di un modello di regressione.
