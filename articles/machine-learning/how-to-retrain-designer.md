---
title: Ripetere il training di modelli con Azure Machine Learning Designer (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come ripetere il training dei modelli con le pipeline pubblicate in Azure Machine Learning Designer (anteprima).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 12/15/2019
ms.openlocfilehash: 734acd712eb954e66a9c0b037d10b7b1fd626c6a
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732162"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Ripetere il training dei modelli con Azure Machine Learning Designer (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa procedura si apprenderà come usare Azure Machine Learning Designer per ripetere il training di un modello di machine learning. Informazioni su come usare le pipeline pubblicate per automatizzare i flussi di lavoro di machine learning per la ripetizione del training.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Eseguire il training di un modello di Machine Learning.
> * Creare un parametro della pipeline.
> * Pubblicare la pipeline di training.
> * Ripetere il training del modello.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).

* Un'area di lavoro Azure Machine Learning con lo SKU Enterprise.

Questa procedura presuppone che l'utente abbia una conoscenza di base della creazione di pipeline nella finestra di progettazione. Per un'introduzione guidata alla finestra di progettazione, completare l'[esercitazione](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Pipeline di esempio

La pipeline usata in questo articolo è una versione modificata di quella rilevata nell' [esempio 3-stima del reddito](how-to-designer-sample-classification-predict-income.md). Usa il modulo [Import Data](algorithm-module-reference/import-data.md) anziché il set di dati di esempio per illustrare come eseguire il training di un modello usando i propri dati.

![Screenshot che mostra la pipeline di esempio modificata con una casella che evidenzia il modulo Import Data](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Eseguire il training di un modello di Machine Learning

Per ripetere il training di un modello, è necessario un modello iniziale. In questa sezione viene illustrato come eseguire il training di un modello e accedere al modello salvato utilizzando la finestra di progettazione.

1. Selezionare il modulo **Import Data** .
1. Nel riquadro Proprietà specificare un'origine dati.

    ![Screenshot che mostra una configurazione di esempio del modulo Import Data](./media/how-to-retrain-designer/import-data-settings.png)

    Per questo esempio, i dati vengono archiviati in un [Archivio](how-to-access-data.md)dati di Azure. Se non si dispone già di un archivio dati, è possibile crearne uno selezionando **nuovo archivio dati**.

1. Specificare il percorso dei dati. È anche possibile selezionare **Esplora percorso** per esplorare visivamente l'archivio dati. 

1. Selezionare **Esegui** nella parte superiore dell'area di disegno.
    
    > [!NOTE]
    > Se è già stato impostato un calcolo predefinito per questa bozza della pipeline, la pipeline verrà eseguita automaticamente. In caso contrario, è possibile seguire le istruzioni nel riquadro impostazioni visualizzato per impostarne uno ora.

### <a name="locate-your-trained-model"></a>Individuare il modello sottoposto a training

La finestra di progettazione Salva tutti gli output della pipeline, inclusi i modelli sottoposti a training, nell'account di archiviazione predefinito. È anche possibile accedere al modello sottoposto a training direttamente nella finestra di progettazione:

1. Attendere il completamento dell'esecuzione della pipeline.

1. Selezionare il modulo **Train Model**.

1. Nel riquadro Impostazioni selezionare **output**.

1. Selezionare **Trained_model** per scaricare il modello.

![Screenshot che illustra come scaricare il modello sottoposto a training](./media/how-to-retrain-designer/download-model.png)

## <a name="create-a-pipeline-parameter"></a>Creare un parametro della pipeline

Aggiungere i parametri della pipeline per impostare in modo dinamico le variabili in fase di esecuzione. Per questa pipeline, aggiungere un parametro per il percorso dei dati di training in modo che sia possibile ripetere il training del modello in un nuovo set di dati.

1. Selezionare il modulo **Import Data** .
1. Nel riquadro Impostazioni selezionare i puntini di sospensione sopra il campo **percorso** .
1. Selezionare **Aggiungi a parametro pipeline**.
1. Specificare un nome di parametro e un valore predefinito.

    > [!NOTE]
    > È possibile esaminare e modificare i parametri della pipeline selezionando l' **icona dell'ingranaggio impostazioni** accanto al titolo della bozza della pipeline. 

![Screenshot che illustra come creare un parametro della pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Pubblicare una pipeline di training

Quando si pubblica una pipeline, viene creato un endpoint della pipeline. Gli endpoint della pipeline consentono di riutilizzare e gestire le pipeline per la ripetibilità e l'automazione. In questo esempio è stata impostata la pipeline per la ripetizione del training.

1. Selezionare **pubblica** sopra l'area di disegno della finestra di progettazione.
1. Selezionare o creare un nuovo endpoint della pipeline.

    > [!NOTE]
    > È possibile pubblicare più pipeline in un singolo endpoint. A ogni pipeline dell'endpoint viene assegnato un numero di versione, che è possibile specificare quando si chiama l'endpoint della pipeline.

1. Selezionare **Pubblica**.

## <a name="retrain-your-model"></a>Ripetere il training del modello

Ora che si dispone di una pipeline di training pubblicata, è possibile usarla per ripetere il training del modello usando nuovi dati. È possibile inviare esecuzioni da un endpoint della pipeline dal portale o da a livello.

### <a name="submit-runs-with-the-designer"></a>Invia esecuzioni con la finestra di progettazione

Usare i passaggi seguenti per inviare un endpoint della pipeline eseguito dalla finestra di progettazione:

1. Passare alla pagina **endpoint** .

1. Selezionare la scheda **endpoint della pipeline** .

1. Selezionare l'endpoint della pipeline.

1. Selezionare la scheda **pipeline pubblicate** .

1. Selezionare la pipeline che si vuole eseguire.

1. Selezionare **Run** (Esegui).

1. Nella finestra di dialogo di installazione è possibile specificare un nuovo valore per il percorso dei dati di input, che punta al nuovo set di dati.

![Screenshot che illustra come configurare un'esecuzione di pipeline con parametri nella finestra di progettazione](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>Invia esecuzioni con codice

Esistono diversi modi per accedere all'endpoint REST a livello a seconda dell'ambiente di sviluppo. È possibile trovare esempi di codice che illustrano come inviare le esecuzioni di pipeline con i parametri nella scheda **consum** della pipeline.

## <a name="next-steps"></a>Passaggi successivi

Seguire l'[esercitazione](tutorial-designer-automobile-price-train-score.md) relativa alla finestra di progettazione per eseguire il training e la distribuzione di un modello di regressione.
