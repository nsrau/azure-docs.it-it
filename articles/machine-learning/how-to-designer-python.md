---
title: Python
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Python nella finestra di progettazione di Azure Machine Learning per trasformare i dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455792"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Eseguire il codice Python nella finestra di progettazione di Azure Machine LearningExecute Python code in Azure Machine Learning designer

In questo articolo viene illustrato come usare il modulo Esegui script Python per aggiungere logica personalizzata alla finestra di progettazione di Azure Machine Learning.In this article, you learn how to use the [Execute Python Script](algorithm-module-reference/execute-python-script.md) module to add custom logic to Azure Machine Learning designer. Nelle seguenti operazioni, si utilizza la libreria Pandas per eseguire semplici operazioni di progettazione delle funzionalità.

È possibile utilizzare l'editor di codice incorporato per aggiungere rapidamente logica Python semplice. Se si desidera aggiungere codice più complesso o caricare librerie Python aggiuntive, è necessario utilizzare il metodo del file zip.

The default execution environment uses the Anacondas distribution of Python. Per un elenco completo dei pacchetti preinstallati, vedere la pagina di riferimento del modulo Execute Python Script.For a complete list of pre-installed [packages,](algorithm-module-reference/execute-python-script.md) see the Execute Python Script module reference page.

![Eseguire la mappa di input Python](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Eseguire Python scritto nella finestra di progettazioneExecute Python written in the designer

### <a name="add-the-execute-python-script-module"></a>Aggiungere il modulo Esegui script Python

1. Individuare il modulo **Execute Python Script** nella tavolozza di progettazione. Può essere trovato nella sezione **Python Language.**

1. Trascinare il modulo nell'area di disegno della pipeline.

### <a name="connect-input-datasets"></a>Connettere i set di dati di inputConnect input datasets

In questo articolo viene utilizzato il dataset di esempio Automobile **price data (Raw)**. 

1. Trascinare il set di dati nell'area di disegno della pipeline.

1. Connettere la porta di output del set di dati alla porta di input in alto a sinistra del modulo **Esegui script Python.** La finestra di progettazione espone l'input come parametro allo script del punto di ingresso.
    
    La porta di input corretta è riservata alle librerie pitone compresse.

    ![Connettere i set di datiConnect datasets](media/how-to-designer-python/connect-dataset.png)
        

1. Prendere nota della porta di input utilizzata. La finestra di progettazione assegna `dataset1` la porta di `dataset2`input sinistra alla variabile e la porta di input centrale a . 

I moduli di input sono facoltativi poiché è possibile generare o importare dati direttamente nel modulo **Execute Python Script.**

### <a name="write-your-python-code"></a>Scrivi il tuo codice Python

La finestra di progettazione fornisce uno script del punto di ingresso iniziale per modificare e immettere il proprio codice Python. 

In questo esempio si utilizzaPas per combinare due colonne presenti nel set di dati dell'automobile **Price** and **Horsepower**per creare una nuova colonna, **Dollars per horse**. Questa colonna rappresenta quanto si paga per ogni potenza, che potrebbe essere una caratteristica utile per decidere se una macchina è un buon affare per i soldi. 

1. Selezionare il modulo **Esegui script Python.**

1. Nel riquadro visualizzato a destra dell'area di disegno, selezionare la casella di testo **dello script Python.**

1. Copiare e incollare il codice seguente nella casella di testo.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    La pipeline dovrebbe avere l'immagine seguente:Your pipeline should look the following image:
    
    ![Eseguire la pipeline PythonExecute Python pipeline](media/how-to-designer-python/execute-python-pipeline.png)

    Lo script del punto `azureml_main`di ingresso deve contenere la funzione . Esistono due parametri di funzione che eseguono il mapping alle due porte di input per il modulo **Execute Python Script.**

    Il valore restituito deve essere un frame di dati Pandas. È possibile restituire fino a due frame di dati come output del modulo.
    
1. Inviare la pipeline.

A questo punto, si dispone di un set di dati con la nuova funzionalità **Dollars/HP**, che potrebbe essere utile per la formazione di un auto consigliatore. Questo è un esempio di estrazione delle funzionalità e riduzione della dimensionalità. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [importare i propri dati](how-to-designer-import-data.md) nella finestra di progettazione di Azure Machine Learning.Learn how to import your own data in Azure Machine Learning designer.