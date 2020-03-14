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
ms.date: 02/24/2020
ms.openlocfilehash: 264b169eefde18880f50feae2554aa3ca7037b1f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368163"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Ripetere il training dei modelli con Azure Machine Learning Designer (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Questo articolo illustra come usare Azure Machine Learning Designer per ripetere il training di un modello di machine learning. Informazioni su come usare le pipeline pubblicate per automatizzare i flussi di lavoro di machine learning per la ripetizione del training.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Eseguire il training di un modello di Machine Learning.
> * Creare un parametro della pipeline.
> * Pubblicare la pipeline di training.
> * Ripetere il training del modello.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).
* Un'area di lavoro Azure Machine Learning con lo SKU Enterprise.

Questo articolo presuppone che l'utente abbia una conoscenza di base della creazione di pipeline nella finestra di progettazione. Per un'introduzione guidata alla finestra di progettazione, completare l'[esercitazione](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Pipeline di esempio

La pipeline utilizzata in questo articolo è una versione modificata di quella disponibile nell' [esempio 3: stima del reddito](how-to-designer-sample-classification-predict-income.md). Usa il modulo [Import Data](algorithm-module-reference/import-data.md) anziché il set di dati di esempio per illustrare come eseguire il training di un modello usando i propri dati.

![Screenshot che mostra la pipeline di esempio modificata con una casella che evidenzia il modulo Import Data](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Eseguire il training di un modello di Machine Learning

Per ripetere il training di un modello, è necessario un modello iniziale. In questa sezione viene illustrato come eseguire il training di un modello e accedere al modello salvato utilizzando la finestra di progettazione.

1. Selezionare il modulo **Import Data** .
1. Nel riquadro Proprietà specificare un'origine dati.

   ![Screenshot che mostra una configurazione di esempio del modulo Import Data](./media/how-to-retrain-designer/import-data-settings.png)

   Per questo esempio, i dati vengono archiviati in un [Archivio](how-to-access-data.md)dati di Azure. Se non si dispone già di un archivio dati, è possibile crearne uno selezionando **nuovo archivio dati**.

1. Specificare il percorso dei dati. È anche possibile selezionare **Esplora percorso** per passare all'archivio dati. 
1. Selezionare **Esegui** nella parte superiore dell'area di disegno.
    
   > [!NOTE]
   > Se è già stato impostato un calcolo predefinito per questa bozza della pipeline, la pipeline verrà eseguita automaticamente. In caso contrario, è possibile seguire le istruzioni nel riquadro impostazioni per impostarne uno ora.

### <a name="find-your-trained-model"></a>Trovare il modello sottoposto a training

La finestra di progettazione Salva tutti gli output della pipeline, inclusi i modelli sottoposti a training, nell'account di archiviazione predefinito. È anche possibile accedere al modello sottoposto a training direttamente nella finestra di progettazione:

1. Attendere il completamento dell'esecuzione della pipeline.
1. Selezionare il modulo **Train Model**.
1. Nel riquadro Impostazioni selezionare **output + log**.
1. Selezionare l'icona **Visualizza output** e seguire le istruzioni nella finestra popup per trovare il modello sottoposto a training.

![Screenshot che illustra come scaricare il modello sottoposto a training](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Creare un parametro della pipeline

Aggiungere i parametri della pipeline per impostare in modo dinamico le variabili in fase di esecuzione. Per questa pipeline, aggiungere un parametro per il percorso dei dati di training in modo che sia possibile ripetere il training del modello in un nuovo set di dati.

1. Selezionare il modulo **Import Data** .
1. Nel riquadro Impostazioni selezionare i puntini di sospensione sopra il campo **percorso** .
1. Selezionare **Aggiungi a parametro pipeline**.
1. Specificare un nome di parametro e un valore predefinito.

   > [!NOTE]
   > È possibile esaminare e modificare i parametri della pipeline selezionando l'icona dell'ingranaggio **Impostazioni** accanto al titolo della bozza della pipeline. 

![Screenshot che illustra come creare un parametro della pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Pubblicare una pipeline di training

Quando si pubblica una pipeline, viene creato un endpoint della pipeline. Gli endpoint della pipeline consentono di riutilizzare e gestire le pipeline per la ripetibilità e l'automazione. In questo esempio è stata configurata la pipeline per la ripetizione del training.

1. Selezionare **pubblica** sopra l'area di disegno della finestra di progettazione.
1. Selezionare o creare un endpoint della pipeline.

   > [!NOTE]
   > È possibile pubblicare più pipeline in un singolo endpoint. A ogni pipeline dell'endpoint viene assegnato un numero di versione, che è possibile specificare quando si chiama l'endpoint della pipeline.

1. Selezionare **Pubblica**.

## <a name="retrain-your-model"></a>Ripetere il training del modello

Ora che si dispone di una pipeline di training pubblicata, è possibile usarla per ripetere il training del modello usando nuovi dati. È possibile inviare le esecuzioni da un endpoint della pipeline dal portale di Azure o inviarle a livello di codice.

### <a name="submit-runs-by-using-the-designer"></a>Inviare le esecuzioni tramite la finestra di progettazione

Usare i passaggi seguenti per inviare un endpoint della pipeline eseguito dalla finestra di progettazione:

1. Passare alla pagina **endpoint** .
1. Selezionare la scheda **endpoint della pipeline** .
1. Selezionare l'endpoint della pipeline.
1. Selezionare la scheda **pipeline pubblicate** .
1. Selezionare la pipeline che si desidera eseguire.
1. Selezionare **Submit**.
1. Nella finestra di dialogo del programma di installazione è possibile specificare un nuovo valore per il valore del percorso dei dati di input. Questo valore punta al nuovo set di dati.

![Screenshot che illustra come configurare un'esecuzione di pipeline con parametri nella finestra di progettazione](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Inviare esecuzioni tramite codice

È possibile trovare l'endpoint REST di una pipeline pubblicata nel pannello panoramica. Chiamando l'endpoint, è possibile ripetere il training della pipeline pubblicata.

Per eseguire una chiamata REST, è necessaria un'intestazione di autenticazione del tipo di porta OAuth 2,0. Per informazioni sulla configurazione dell'autenticazione per l'area di lavoro e sulla creazione di una chiamata REST con parametri, vedere [creare una pipeline di Azure Machine Learning per l'assegnazione dei punteggi di batch](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Passaggi successivi

Seguire l' [esercitazione di progettazione](tutorial-designer-automobile-price-train-score.md) per eseguire il training e distribuire un modello di regressione.
