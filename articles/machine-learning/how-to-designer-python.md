---
title: Esegui script Python nella finestra di progettazione (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Python nella finestra di progettazione Azure Machine Learning (anteprima) per trasformare i dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.custom: designer, tracking-python
ms.openlocfilehash: 61086bf8c215a09e457e931ffed157559b339a9b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206799"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Eseguire codice Python in Azure Machine Learning Designer

Questo articolo illustra come usare il modulo [Execute Python Script](algorithm-module-reference/execute-python-script.md) per aggiungere la logica personalizzata alla finestra di progettazione di Azure Machine Learning. Nella procedura seguente si usa la libreria Pandas per eseguire semplici operazioni di progettazione di funzionalità.

È possibile usare l'editor di codice incorporato per aggiungere rapidamente logica Python semplice. Se si vuole aggiungere codice più complesso o caricare altre librerie Python, è necessario usare il metodo del file zip.

L'ambiente di esecuzione predefinito usa la distribuzione Anacondas di Python. Per un elenco completo dei pacchetti preinstallati, vedere la pagina di [riferimento del modulo Execute Python Script](algorithm-module-reference/execute-python-script.md).

![Mappa di input di Execute Python](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Execute Python scritto nella finestra di progettazione

### <a name="add-the-execute-python-script-module"></a>Aggiungere il modulo Execute Python Script

1. Trovare il modulo **Execute Python Script** nella tavolozza della finestra di progettazione. Si trova nella sezione **Linguaggio Python**.

1. Trascinare e rilasciare il modulo nel canvas della pipeline.

### <a name="connect-input-datasets"></a>Connettere set di dati di input

In questo articolo viene usato il set di dati di esempio **Automobile price data (Raw)** . 

1. Trascinare e rilasciare il set di dati nel canvas della pipeline.

1. Connettere la porta di output del set di dati alla porta di input superiore sinistra del modulo **Execute Python Script**. La finestra di progettazione espone l'input come parametro allo script del punto di ingresso.
    
    La porta di input corretta è riservata alle librerie Python compresse.

    ![Connettere i set di dati](media/how-to-designer-python/connect-dataset.png)
        

1. Prendere nota della porta di input usata. La finestra di progettazione assegna la porta di input sinistra alla variabile `dataset1` e la porta di input intermedia a `dataset2`. 

I moduli di input sono facoltativi perché è possibile generare o importare dati direttamente nel modulo **Execute Python Script**.

### <a name="write-your-python-code"></a>Scrivere codice Python

La finestra di progettazione fornisce uno script iniziale che è possibile modificare per immettere il proprio codice Python. 

In questo esempio vengono usati Pandas per combinare due colonne trovate nel set di dati dell'automobile, **Prezzo** e **Potenza**, in modo da creare una nuova colonna, **Dollari per potenza**. Questo articolo rappresenta quanto si paga per ogni cavallo, una funzionalità potenzialmente utile per decidere se un'automobile ha un buon rapporto qualità/prezzo. 

1. Selezionare il modulo **Execute Python Script**.

1. Nel riquadro che viene visualizzato a destra del canvas selezionare la casella di testo **Script Python**.

1. Copiare e incollare il codice seguente nella casella di testo.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    La pipeline dovrebbe essere simile all'immagine seguente:
    
    ![Pipeline di Execute Python](media/how-to-designer-python/execute-python-pipeline.png)

    Lo script del punto di ingresso deve contenere la funzione `azureml_main`. Sono disponibili due parametri di funzione che eseguono il mapping alle due porte di input per il modulo **Execute Python Script**.

    Il valore restituito deve essere un dataframe Pandas. È possibile restituire fino a due dataframe come output del modulo.
    
1. Inviare la pipeline.

A questo punto, si dispone di un set di dati con la nuova funzionalità **Dollari/Potenza**, potenzialmente utile per il training di un esperto di automobili. Si tratta di un esempio di estrazione della funzionalità e di riduzione della dimensionalità. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [importare i propri dati](how-to-designer-import-data.md) nella finestra di progettazione Azure Machine Learning.
