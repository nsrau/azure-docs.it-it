---
title: Riqualificare i modelli usando la finestra di progettazione di Azure Machine Learning (anteprima)Retrain models by using Azure Machine Learning designer (preview)
titleSuffix: Azure Machine Learning
description: Informazioni su come riqualificare i modelli con pipeline pubblicate in Azure Machine Learning Designer (anteprima).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: 721e5414fc4753cd5d58a17fc7ed51ea99868778
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810377"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Eseguire nuovamente il training di modelli con la finestra di progettazione di Azure Machine Learning (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In questo articolo sulle procedure viene illustrato come usare la finestra di progettazione di Azure Machine Learning per riqualificare un modello di apprendimento automatico. Le pipeline pubblicate verranno usate per automatizzare il flusso di lavoro e impostare i parametri per il training del modello sui nuovi dati. 

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Eseguire il training di un modello di Machine Learning.
> * Creare un parametro della pipeline.
> * Pubblicare la pipeline di formazione.
> * Riqualificare il modello con nuovi parametri.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning con Enterprise SKU.
* Un set di dati accessibile alla finestra di progettazione. I possibili valori sono i seguenti:
   * Un set di dati registrato di Azure Machine LearningAn Azure Machine Learning registered dataset
    
     **-o-**
   * Un file di dati archiviato in un archivio dati di Azure Machine Learning.A data file stored in an Azure Machine Learning datastore.
   
Per informazioni sull'accesso ai dati mediante la finestra di progettazione, vedere Come importare dati nella finestra di [progettazione](how-to-designer-import-data.md).

In questo articolo si presuppone inoltre che si dispone di conoscenze di base per la compilazione di pipeline nella finestra di progettazione. Per un'introduzione guidata, completare [l'esercitazione](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Pipeline di esempio

La pipeline utilizzata in questo articolo è una versione modificata [dell'esempio 3: previsione del reddito](samples-designer.md#classification-samples). La pipeline usa il modulo [Importa dati](algorithm-module-reference/import-data.md) anziché il set di dati di esempio per illustrare come eseguire il training dei modelli usando i propri dati.

![Screenshot che mostra la pipeline di esempio modificata con una casella che evidenzia il modulo Importa dati](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Creare un parametro della pipelineCreate a pipeline parameter

Creare parametri della pipeline per impostare dinamicamente le variabili in fase di esecuzione. Per questo esempio, si modificherà il percorso dei dati di training da un valore fisso a un parametro, in modo da poter eseguire nuovamente il training del modello su dati diversi.

1. Selezionare il modulo **Importa dati.**

    > [!NOTE]
    > In questo esempio viene utilizzato il modulo Import Data per accedere ai dati in un archivio dati registrato. Tuttavia, è possibile seguire passaggi simili se si utilizzano modelli di accesso ai dati alternativi.

1. Nel riquadro dei dettagli del modulo, a destra dell'area di disegno, selezionare l'origine dati.

1. Immettere il percorso dei dati. È anche possibile selezionare **Sfoglia percorso** per sfogliare l'albero dei file. 

1. Posizionare il puntatore del mouse sul campo **Percorso** e selezionare i puntini di sospensione sopra il campo **Percorso** visualizzati.

    ![Schermata che mostra come creare un parametro della pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Selezionare **Aggiungi al parametro della pipeline**.

1. Specificare un nome di parametro e un valore predefinito.

   > [!NOTE]
   > È possibile esaminare e modificare i parametri della pipeline selezionando l'icona a forma di ingranaggio **Impostazioni** accanto al titolo della bozza della pipeline. 

1. Selezionare **Salva**.

1. Inviare l'esecuzione della pipeline.

## <a name="find-a-trained-model"></a>Trovare un modello con training

La finestra di progettazione salva tutto l'output della pipeline, inclusi i modelli sottoposti a training, nell'account di archiviazione predefinito dell'area di lavoro. È inoltre possibile accedere ai modelli sottoposti a training direttamente nella finestra di progettazione:You can also access trained models directly in the designer:

1. Attendere il completamento dell'esecuzione della pipeline.
1. Selezionare il modulo **Train Model**.
1. Nel riquadro dei dettagli del modulo, a destra dell'area di disegno, selezionare **Output e log**.
1. È possibile trovare il modello in **Altri output** insieme ai log di esecuzione.
1. In alternativa, selezionare l'icona **Visualizza output.** Da qui, è possibile seguire le istruzioni nella finestra di dialogo per passare direttamente all'archivio dati. 

![Screenshot che mostra come scaricare il modello sottoposto a training](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Pubblicare una pipeline di formazione

Pubblicare una pipeline in un endpoint della pipeline per riutilizzare facilmente le pipeline in futuro. Un endpoint della pipeline crea un endpoint REST per richiamare la pipeline in futuro. In questo esempio, l'endpoint della pipeline consente di riutilizzare la pipeline per eseguire il nuovo training di un modello su dati diversi.

1. Selezionare **Pubblica** sopra l'area di disegno della finestra di progettazione.
1. Selezionare o creare un endpoint della pipeline.

   > [!NOTE]
   > È possibile pubblicare più pipeline in un singolo endpoint. A ogni pipeline in un endpoint specificato viene assegnato un numero di versione, che è possibile specificare quando si chiama l'endpoint della pipeline.

1. Selezionare **Pubblica**.

## <a name="retrain-your-model"></a>Riqualificare il modello

Ora che si dispone di una pipeline di training pubblicata, è possibile usarla per rieseguire il training del modello sui nuovi dati. È possibile inviare esecuzioni da un endpoint della pipeline dall'area di lavoro di Studio o a livello di codice.

### <a name="submit-runs-by-using-the-designer"></a>Invio di esecuzioni tramite la finestra di progettazioneSubmit runs by using the designer

Utilizzare la procedura seguente per inviare un endpoint della pipeline con parametri eseguito dalla finestra di progettazione:Use the following steps to submit a parameterized pipeline endpoint run from the designer:

1. Passare alla pagina Endpoint nell'area di lavoro di Studio.Go to the **Endpoints** page in your studio workspace.
1. Selezionare la scheda **Endpoint pipeline.** Selezionare quindi l'endpoint della pipeline.
1. Selezionare la scheda **Pipeline pubblicate.** Selezionare quindi la versione della pipeline che si desidera eseguire.
1. Selezionare **Submit** (Invia).
1. Nella finestra di dialogo di configurazione, è possibile specificare i valori dei parametri per l'esecuzione. Per questo esempio, aggiornare il percorso dei dati per eseguire il training del modello usando un set di dati non statunitense.

![Screenshot che mostra come impostare un'esecuzione di pipeline con parametri nella finestra di progettazione](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>L'invio viene eseguito tramite codiceSubmit runs by using code

È possibile trovare l'endpoint REST di una pipeline pubblicata nel riquadro di panoramica. Chiamando l'endpoint, è possibile eseguire nuovamente il training della pipeline pubblicata.

Per effettuare una chiamata REST, è necessaria un'intestazione di autenticazione di tipo portatore OAuth 2.0.To make a REST call, you need an OAuth 2.0 bearer-type authentication header. Per informazioni sull'impostazione dell'autenticazione nell'area di lavoro e sull'esecuzione di una chiamata REST con parametri, vedere Creare una pipeline di Azure Machine Learning per il [punteggio in batch.](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare un endpoint della pipeline di training con parametri usando la finestra di progettazione.

Per una procedura dettagliata completa su come è possibile distribuire un modello per eseguire stime, vedere [l'esercitazione](tutorial-designer-automobile-price-train-score.md) sulla progettazione per eseguire il training e distribuire un modello di regressione.
