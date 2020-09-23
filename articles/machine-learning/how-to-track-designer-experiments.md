---
title: Metriche di log nella finestra di progettazione
titleSuffix: Azure Machine Learning
description: Monitorare gli esperimenti di progettazione di Azure ML. Abilitare la registrazione usando il modulo Execute Python script e visualizzare i risultati registrati in studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: bffbf32cf5faa936a00444f1f39facaf226b8ef2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885976"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Abilitare la registrazione nelle pipeline di Azure Machine Learning Designer


Questo articolo illustra come aggiungere codice di registrazione alle pipeline di progettazione. Si apprenderà anche come visualizzare i log usando il portale Web di Azure Machine Learning Studio.

Per altre informazioni sulla registrazione delle metriche con l'esperienza di creazione dell'SDK, vedere [monitorare le esecuzioni e le metriche dell'esperimento di Azure ml](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>Abilitare la registrazione con script Execute Python

Usare il modulo __Execute Python script__ per abilitare la registrazione nelle pipeline della finestra di progettazione. Sebbene sia possibile registrare qualsiasi valore con questo flusso di lavoro, è particolarmente utile registrare le metriche dal modulo __Evaluate Model__ per tenere traccia delle prestazioni del modello tra le esecuzioni.

L'esempio seguente illustra come registrare l'errore quadratico medio di due modelli sottoposti a training usando i moduli Evaluate Model ed Execute Python script.

1. Connettere un modulo __Execute Python script__ all'output del modulo __Evaluate Model__ .

    ![Connettere un modulo Execute Python Script al modulo Evaluate Model](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Incollare il codice seguente nell'editor di codice per l' __esecuzione di script Python__ per registrare l'errore assoluto medio per il modello sottoposto a training. È possibile usare un modello simile per registrare qualsiasi altro valore nella finestra di progettazione:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.

        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        run.parent.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])

        # Log right output port result of Evaluate Model.
        run.parent.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])
    
        return dataframe1,
    ```
    
Questo codice USA Azure Machine Learning Python SDK per registrare i valori. USA Run. get_context () per ottenere il contesto dell'esecuzione corrente. Registra quindi i valori nel contesto con il metodo Run. Parent. log (). USA `parent` per registrare i valori nell'esecuzione della pipeline padre anziché nell'esecuzione del modulo.

Per altre informazioni su come usare Python SDK per registrare i valori, vedere [abilitare la registrazione nelle esecuzioni di training di Azure ml](how-to-track-experiments.md).

## <a name="view-logs"></a>Visualizzare i log

Al termine dell'esecuzione della pipeline, è possibile visualizzare il *Mean_Absolute_Error* nella pagina esperimenti.

1. Passare alla sezione **esperimenti** .
1. Selezionare l'esperimento.
1. Selezionare l'esecuzione nell'esperimento che si vuole visualizzare.
1. Selezionare **Metriche**.

    ![Visualizzare le metriche di esecuzione in studio](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare i log nella finestra di progettazione. Per i passaggi successivi, vedere gli articoli correlati:


* Per informazioni su come risolvere i problemi relativi alle pipeline di progettazione, vedere [Debug & risolvere i problemi delle pipeline di ml](how-to-debug-pipelines.md#azure-machine-learning-designer).
* Per informazioni su come usare Python SDK per registrare le metriche nell'esperienza di creazione dell'SDK, vedere [abilitare la registrazione nelle esecuzioni di training di Azure ml](how-to-track-experiments.md).
