---
title: Python
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Python nella finestra di progettazione Azure Machine Learning per trasformare i dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: b0b0bb5eefde9e744b1f30109d60ded91d3b44e8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228684"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Eseguire codice Python in Azure Machine Learning Designer

Questo articolo illustra come usare il modulo [Execute Python script](algorithm-module-reference/execute-python-script.md) per aggiungere la logica personalizzata a Azure Machine Learning Designer. Nella procedura seguente si usa la libreria Pandas per eseguire semplici operazioni di progettazione delle funzionalità.

È possibile usare l'editor di codice incorporato per aggiungere rapidamente logica Python semplice. Se si vuole aggiungere codice più complesso o caricare altre librerie Python, è necessario usare il metodo del file zip.

L'ambiente di esecuzione predefinito usa la distribuzione Anacondas di Python. Per un elenco completo dei pacchetti preinstallati, vedere la pagina di riferimento per il [modulo Execute Python script](algorithm-module-reference/execute-python-script.md) .

![Esegui mappa di input Python](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Eseguire Python scritto nella finestra di progettazione

### <a name="add-the-execute-python-script-module"></a>Aggiungere il modulo Execute Python script

1. Trovare il modulo **Execute Python script (Esegui script Python** ) nella tavolozza di progettazione. Si trova nella sezione **Python Language (linguaggio Python** ).

1. Trascinare e rilasciare il modulo nell'area di disegno della pipeline.

### <a name="connect-input-datasets"></a>Connetti set di dati di input

Questo articolo usa il set di dati di esempio, **auto price data (RAW)** . 

1. Trascinare e rilasciare il set di dati nell'area di disegno della pipeline.

1. Connettere la porta di output del set di dati alla porta di input superiore sinistra del modulo **Execute Python script** . La finestra di progettazione espone l'input come parametro allo script del punto di ingresso.
    
    La porta di input corretta è riservata alle librerie Python compresse.

    ![Connetti set di impostazioni](media/how-to-designer-python/connect-dataset.png)
        

1. Prendere nota della porta di input usata. La finestra di progettazione assegna la porta di input sinistra alla variabile `dataset1` e la porta di input intermedia per `dataset2`. 

I moduli di input sono facoltativi perché è possibile generare o importare dati direttamente nel modulo **Execute Python script** .

### <a name="write-your-python-code"></a>Scrivere il codice Python

La finestra di progettazione fornisce uno script iniziale del punto di ingresso che consente di modificare e immettere il proprio codice Python. 

In questo esempio si usano i Panda per combinare due colonne trovate nel set di dati dell'automobile, **Prezzo** e **potenza**, per creare una nuova colonna, **dollaro per potenza**. Questo articolo rappresenta quanto si paga per ogni potenza, che può essere una funzionalità utile per decidere se un'auto è una buona soluzione per il denaro. 

1. Selezionare il modulo **Execute Python script** .

1. Nel riquadro visualizzato a destra dell'area di disegno, selezionare la casella di testo **script Python** .

1. Copiare e incollare il codice seguente nella casella di testo.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    La pipeline dovrebbe avere un aspetto simile all'immagine seguente:
    
    ![Esegui pipeline Python](media/how-to-designer-python/execute-python-pipeline.png)

    Lo script del punto di ingresso deve contenere la funzione `azureml_main`. Sono disponibili due parametri di funzione che eseguono il mapping alle due porte di input per il modulo **Execute Python script** .

    Il valore restituito deve essere un dataframe Pandas. È possibile restituire fino a due dataframe come output del modulo.
    
1. Eseguire la pipeline.

A questo punto, si dispone di un set di dati con la nuova funzionalità **dollars/HP**, che può essere utile per il training di un'auto Recommender. Questo è un esempio di estrazione della funzionalità e riduzione della dimensionalità. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [importare i dati](how-to-designer-import-data.md) in Azure Machine Learning Designer.