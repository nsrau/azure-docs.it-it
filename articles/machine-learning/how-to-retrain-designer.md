---
title: Ripetere il training dei modelli utilizzando Azure Machine Learning Designer
titleSuffix: Azure Machine Learning
description: Informazioni su come ripetere il training dei modelli di Machine Learning con le pipeline pubblicate in Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: d8ef4d9f768d6fdcf976c9317d1abec3d4533824
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554802"
---
# <a name="retrain-models-with-azure-machine-learning-designer"></a>Ripetere il training dei modelli con Azure Machine Learning Designer


Questo articolo illustra come usare la finestra di progettazione di Azure Machine Learning per ripetere il training di un modello di Machine Learning. Verranno usate le pipeline pubblicate per automatizzare il flusso di lavoro e impostare i parametri per eseguire il training del modello con i nuovi dati. 

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Eseguire il training di un modello di Machine Learning.
> * Creare un parametro della pipeline.
> * Pubblicare la pipeline di training.
> * Ripetere il training del modello con nuovi parametri.

## <a name="prerequisites"></a>Prerequisiti

* Area di lavoro di Azure Machine Learning
* Completare la parte 1 di questa serie di procedure, [trasformare i dati nella finestra di progettazione](how-to-designer-transform-data.md)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

Questo articolo presuppone inoltre che l'utente abbia una certa conoscenza della creazione di pipeline nella finestra di progettazione. Per un'introduzione guidata, eseguire l'[esercitazione](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Pipeline di esempio

La pipeline usata in questo articolo è una versione modificata di una stima del [reddito](samples-designer.md#classification) della pipeline di esempio nella Home page della finestra di progettazione. La pipeline usa il modulo [Import Data](algorithm-module-reference/import-data.md) (Importare dati) anziché il set di dati di esempio per illustrare come eseguire il training dei modelli con i propri dati.

![Screenshot che mostra la pipeline di esempio modificata con un riquadro che evidenzia il modulo Import Data (Importare dati)](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Creare un parametro della pipeline

Creare i parametri della pipeline per impostare dinamicamente le variabili durante il runtime. Per questo esempio, il percorso dei dati di training verrà modificato da un valore fisso a un parametro in modo che sia possibile ripetere il training del modello con dati diversi.

1. Selezionare il modulo **Import Data** (Importare dati).

    > [!NOTE]
    > Questo esempio usa il modulo Import Data (Importare Dati) per accedere ai dati in un archivio dati registrato. Tuttavia, se si usano modelli di accesso ai dati alternativi, è possibile seguire una procedura simile.

1. Nel riquadro dei dettagli del modulo a destra del canvas selezionare l'origine dati.

1. Immettere il percorso dei dati. È anche possibile selezionare **Browse path** (Sfoglia percorso) per esplorare l'albero dei file. 

1. Passare il mouse sul campo **Path** (Percorso) e selezionare i tre punti visualizzati sopra il campo **Path** (Percorso).

    ![Screenshot che illustra come creare un parametro della pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selezionare **Add to pipeline parameter** (Aggiungi a parametro pipeline).

1. Specificare un nome di parametro e un valore predefinito.

   > [!NOTE]
   > È possibile esaminare e modificare i parametri della pipeline selezionando l'icona a forma di ingranaggio **Settings** (Impostazioni) accanto al titolo della bozza della pipeline. 

1. Selezionare **Salva**.

1. Avviare l'esecuzione della pipeline.

## <a name="find-a-trained-model"></a>Trovare un modello di cui è stato eseguito il training

La finestra di progettazione salva tutti gli output della pipeline, inclusi i modelli di cui è stato eseguito il training, nell'account di archiviazione predefinito dell'area di lavoro. È anche possibile accedere ai modelli di cui è stato eseguito il training direttamente nella finestra di progettazione:

1. Attendere il completamento dell'esecuzione della pipeline.
1. Selezionare il modulo **Train Model**.
1. Nel riquadro dei dettagli del modulo a destra del canvas selezionare **Outputs + logs** (Output + log).
1. È possibile trovare il modello in **Other outputs** (Altri output) insieme ai log di esecuzione.
1. In alternativa, selezionare l'icona **View output** (Visualizza output). Da qui è possibile seguire le istruzioni nella finestra di dialogo per passare direttamente all'archivio dati. 

> [!div class="mx-imgBorder"]
> ![Schermata che illustra come scaricare il modello di cui è stato eseguito il training](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Pubblicare una pipeline di training

Pubblicare una pipeline in un endpoint della pipeline per riutilizzare facilmente le pipeline in futuro. Un endpoint della pipeline crea un endpoint REST per richiamare la pipeline in futuro. In questo esempio, l'endpoint della pipeline consente di riutilizzare la pipeline per ripetere il training di un modello con dati diversi.

1. Selezionare **Publish** (Pubblica) sopra il canvas della finestra di progettazione.
1. Selezionare o creare un endpoint della pipeline.

   > [!NOTE]
   > È possibile pubblicare più pipeline in un singolo endpoint. A ogni pipeline di un determinato endpoint viene assegnato un numero di versione che è possibile specificare quando si chiama l'endpoint della pipeline.

1. Selezionare **Pubblica**.

## <a name="retrain-your-model"></a>Ripetere il training del modello

Ora che è disponibile una pipeline di training pubblicata, è possibile usarla per ripetere il training del modello con i nuovi dati. È possibile avviare le esecuzioni da un endpoint della pipeline dall'area di lavoro di Studio o a livello di codice.

### <a name="submit-runs-by-using-the-studio-portal"></a>Inviare le esecuzioni tramite il portale di studio

Usare la procedura seguente per inviare un endpoint della pipeline con parametri eseguito dal portale di studio:

1. Passare alla pagina **Endpoints** (Endpoint) nell'area di lavoro di Studio.
1. Selezionare la scheda **Pipeline endpoints** (Endpoint pipeline). Quindi selezionare l'endpoint della pipeline.
1. Selezionare la scheda **Published pipelines** (Pipeline pubblicate). Quindi selezionare la versione della pipeline che si vuole eseguire.
1. Selezionare **Submit** (Invia).
1. Nella finestra di dialogo di impostazione è possibile specificare i valori dei parametri per l'esecuzione. Per questo esempio, aggiornare il percorso dei dati per eseguire il training del modello usando un set di dati diverso da US.

![Screenshot che illustra come configurare l'esecuzione di una pipeline con parametri nella finestra di progettazione](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Avviare le esecuzioni usando il codice

L'endpoint REST di una pipeline pubblicata è disponibile nel pannello di panoramica. Chiamando l'endpoint è possibile ripetere il training della pipeline pubblicata.

Per eseguire una chiamata REST è necessaria un'intestazione di autenticazione di tipo bearer token OAuth 2.0. Per informazioni sulla configurazione dell'autenticazione nell'area di lavoro e sull'esecuzione di una chiamata REST con parametri, vedere [Creare una pipeline di Azure Machine Learning per l'assegnazione di punteggi batch](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare un endpoint della pipeline di training con parametri usando la finestra di progettazione.

Per la procedura dettagliata completa per distribuire un modello per eseguire stime, vedere l'[esercitazione con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md) per eseguire il training e la distribuzione di un modello di regressione.
