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
ms.date: 02/24/2020
ms.openlocfilehash: c8791e933882832dc7b0037c860a4c4e1e9a54c7
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389036"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Eseguire nuovamente il training di modelli con la finestra di progettazione di Azure Machine Learning (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In questo articolo sulle procedure viene illustrato come usare la finestra di progettazione di Azure Machine Learning per riqualificare un modello di apprendimento automatico. Informazioni su come usare le pipeline pubblicate per automatizzare i flussi di lavoro di Machine Learning per la riqualificazione.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Eseguire il training di un modello di Machine Learning.
> * Creare un parametro della pipeline.
> * Pubblicare la pipeline di formazione.
> * Riqualificare il modello.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, creare un [account gratuito.](https://aka.ms/AMLFree)
* Un'area di lavoro di Azure Machine Learning con lo SKU Enterprise.An Azure Machine Learning workspace with the Enterprise SKU.

In questo articolo si presuppone che si dispone di conoscenze di base per la compilazione di pipeline nella finestra di progettazione. Per un'introduzione guidata alla finestra di progettazione, completare l'[esercitazione](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Pipeline di esempio

La pipeline utilizzata in questo articolo è una versione modificata di quella presente [nell'esempio 3: previsione del reddito](how-to-designer-sample-classification-predict-income.md). Usa il modulo [Importa dati](algorithm-module-reference/import-data.md) anziché il set di dati di esempio per mostrare come eseguire il training di un modello usando i propri dati.

![Screenshot che mostra la pipeline di esempio modificata con una casella che evidenzia il modulo Importa dati](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Eseguire il training di un modello di Machine Learning

Per rieseguire il training di un modello, è necessario un modello iniziale. In questa sezione viene illustrato come eseguire il training di un modello e accedere al modello salvato tramite la finestra di progettazione.

1. Selezionare il modulo **Importa dati.**
1. Nel riquadro delle proprietà specificare un'origine dati.

   ![Screenshot che mostra una configurazione di esempio del modulo Importa dati](./media/how-to-retrain-designer/import-data-settings.png)

   Per questo esempio, i dati vengono archiviati in un datastore di Azure.For this example, the data is stored in an [Azure datastore](how-to-access-data.md). Se non si dispone già di un archivio dati, è possibile crearne uno ora selezionando **Nuovo archivio dati**.

1. Specificare il percorso dei dati. È anche possibile selezionare **Sfoglia percorso** per passare all'archivio dati. 
1. Seleziona **Invia** nella parte superiore dell'area di disegno.
    
   > [!NOTE]
   > Se è già stato impostato un calcolo predefinito per questa bozza della pipeline, la pipeline verrà eseguita automaticamente. In caso contrario, è possibile seguire le istruzioni nel riquadro delle impostazioni per impostarne uno ora.

### <a name="find-your-trained-model"></a>Trova il tuo modello addestrato

La finestra di progettazione salva tutti gli output della pipeline, inclusi i modelli sottoposti a training, nell'account di archiviazione predefinito. Tuttavia, è anche possibile accedere ai modelli sottoposti a training direttamente nella finestra di progettazione:However, you can also access trained models directly in the designer:

1. Attendere il completamento dell'esecuzione della pipeline.
1. Selezionare il modulo **Train Model**.
1. Nel riquadro delle impostazioni, selezionare **Output e registri**.
1. Selezionare l'icona **Visualizza output** e seguire le istruzioni nella finestra popup per trovare il modello sottoposto a training.

![Screenshot che mostra come scaricare il modello sottoposto a training](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Creare un parametro della pipelineCreate a pipeline parameter

Aggiungere parametri della pipeline per impostare dinamicamente le variabili in fase di esecuzione. Per questa pipeline, aggiungere un parametro per il percorso dei dati di training in modo da poter eseguire nuovamente il training del modello in un nuovo set di dati.

1. Selezionare il modulo **Importa dati.**
1. Nel riquadro delle impostazioni, selezionare i puntini di sospensione sopra il campo **Percorso.**
1. Selezionare **Aggiungi al parametro della pipeline**.
1. Specificare un nome di parametro e un valore predefinito.

   > [!NOTE]
   > È possibile esaminare e modificare i parametri della pipeline selezionando l'icona a forma di ingranaggio **Impostazioni** accanto al titolo della bozza della pipeline. 

![Schermata che mostra come creare un parametro della pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Pubblicare una pipeline di formazione

Quando si pubblica una pipeline, viene creato un endpoint della pipeline. Gli endpoint della pipeline consentono di riutilizzare e gestire le pipeline per la ripetibilità e l'automazione. In questo esempio, la pipeline è stata impostata per la riqualificazione.

1. Selezionare **Pubblica** sopra l'area di disegno della finestra di progettazione.
1. Selezionare o creare un endpoint della pipeline.

   > [!NOTE]
   > È possibile pubblicare più pipeline in un singolo endpoint. A ogni pipeline nell'endpoint viene assegnato un numero di versione, che è possibile specificare quando si chiama l'endpoint della pipeline.

1. Selezionare **Pubblica**.

## <a name="retrain-your-model"></a>Riqualificare il modello

Ora che si dispone di una pipeline di training pubblicata, è possibile usarla per rieseguire il training del modello usando nuovi dati. È possibile inviare esecuzioni da un endpoint della pipeline dal portale di Azure o inviarle a livello di codice.

### <a name="submit-runs-by-using-the-designer"></a>Invio di esecuzioni tramite la finestra di progettazioneSubmit runs by using the designer

Utilizzare la procedura seguente per inviare un endpoint della pipeline eseguito dalla finestra di progettazione:Use the following steps to submit a pipeline endpoint run from the designer:

1. Passare alla pagina **Endpoint.**
1. Selezionare la scheda **Endpoint pipeline.**
1. Selezionare l'endpoint della pipeline.
1. Selezionare la scheda **Pipeline pubblicate.**
1. Selezionare la pipeline che si desidera eseguire.
1. Selezionare **Submit**.
1. Nella finestra di dialogo di impostazione è possibile specificare un nuovo valore per il valore del percorso dei dati di input. Questo valore punta al nuovo set di dati.

![Screenshot che mostra come impostare un'esecuzione di pipeline con parametri nella finestra di progettazione](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>L'invio viene eseguito tramite codiceSubmit runs by using code

È possibile trovare l'endpoint REST di una pipeline pubblicata nel riquadro di panoramica. Chiamando l'endpoint, è possibile eseguire nuovamente il training della pipeline pubblicata.

Per effettuare una chiamata REST, è necessaria un'intestazione di autenticazione di tipo portatore OAuth 2.0.To make a REST call, you need an OAuth 2.0 bearer-type authentication header. Per informazioni sull'impostazione dell'autenticazione nell'area di lavoro e sull'esecuzione di una chiamata REST con parametri, vedere Creare una pipeline di Azure Machine Learning per il [punteggio in batch.](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)

## <a name="next-steps"></a>Passaggi successivi

Seguire [l'esercitazione](tutorial-designer-automobile-price-train-score.md) sulla finestra di progettazione per eseguire il training e distribuire un modello di regressione.
