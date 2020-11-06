---
title: Train Pytorch Model
titleSuffix: Azure Machine Learning
description: Usare il modulo Train Pytorch Models in Azure Machine Learning Designer per eseguire il training dei modelli da zero oppure ottimizzare i modelli esistenti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2d88069f33995bdbe9dd479afe9a4e72ab9939b6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420666"
---
# <a name="train-pytorch-model"></a>Train Pytorch Model

Questo articolo descrive come usare il modulo **Train Pytorch Model** in Azure Machine Learning Designer per eseguire il training di modelli Pytorch come DenseNet. Il training viene eseguito dopo aver definito un modello e avere impostato i relativi parametri e sono necessari dati con etichetta. 

## <a name="how-to-use-train-pytorch-model"></a>Come usare Train Pytorch Model 

1. Aggiungere il modulo [DenseNet](densenet.md) o [Resnet](resnet.md) alla bozza della pipeline nella finestra di progettazione.

2. Aggiungere il modulo **Train Pytorch Model** alla pipeline. È possibile trovare questo modulo nella categoria **training del modello** . Espandere **Train** , quindi trascinare il modulo **Train Pytorch Model** nella pipeline.

   > [!NOTE]
   > Il modulo **Train Pytorch Model** è migliore per l'esecuzione in una **GPU** di tipo calcolo per set di dati di grandi dimensioni. in caso contrario, la pipeline non riesce È possibile selezionare calcolo per un modulo specifico nel riquadro destro del modulo impostando **usa altra destinazione di calcolo**.

3.  Nell'input di sinistra alleghi un modello non sottoposto a training. Alleghi il set di dati di training e il set di dati di convalida all'input centrale e destro del **modello Train Pytorch**.

    Per il modello non sottoposto a training, deve essere un modello pytorch, ad esempio DenseNet; in caso contrario, verrà generata un'eccezione ' InvalidModelDirectoryError '.

    Per DataSet, il set di dati di training deve essere una directory di immagini con etichetta. Vedere la pagina relativa alla **conversione di una directory di immagini** per ottenere una directory di immagini con etichetta. Se non è etichettato, verrà generata un'eccezione ' NotLabeledDatasetError '.

    Il set di dati di training e il set di dati di convalida hanno le stesse categorie di etichette; in caso contrario, verrà generato un InvalidDatasetError

4.  Per **epoche** , specificare il numero di epoche di cui si vuole eseguire il training. L'intero set di dati verrà iterato in ogni Epoch, per impostazione predefinita 5.

5.  Per le **dimensioni del batch** , specificare il numero di istanze di cui eseguire il training in un batch, per impostazione predefinita è 16.

6.  Per la **velocità di apprendimento** specificare un valore per la velocità di *apprendimento*. I valori della velocità di apprendimento controllano le dimensioni del passaggio usato in Optimizer come SGD ogni volta che il modello viene testato e corretto.

    Rendendo la percentuale più piccola, si testa il modello più spesso, con il rischio che si possa rimanere bloccati in un plateau locale. Estendendo le dimensioni del passaggio, è possibile rendere più veloce la convergenza con il rischio di oltrepassare il valore minimo true. per impostazione predefinita 0,001.

7.  Per il valore di **inizializzazione casuale** , digitare facoltativamente un valore intero da utilizzare come valore di inizializzazione. L'uso di un valore di inizializzazione è consigliato se si vuole garantire la riproducibilità dell'esperimento tra le esecuzioni.

8.  Per **pazienza** , specificare il numero di epoche da arrestare prima di eseguire il training se la perdita di convalida non diminuisce consecutivamente. per impostazione predefinita 3.

9.  Inviare la pipeline. Se il set di dati ha dimensioni maggiori, è consigliabile un po' di tempo e le risorse di calcolo GPU sono consigliate.

## <a name="results"></a>Risultati

Al termine dell'esecuzione della pipeline, per usare il modello per il punteggio, connettere il [modello Train Pytorch](train-pytorch-model.md) al modello di [immagine del Punteggio](score-image-model.md)per stimare i valori per i nuovi esempi di input.

## <a name="technical-notes"></a>Note tecniche
###  <a name="expected-inputs"></a>Input previsti  

| Nome               | Type                    | Description                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| Untrained model    | UntrainedModelDirectory | Modello non sottoposto a training, richiede pytorch         |
| Dataset di training   | ImageDirectory          | Dataset di training                         |
| Set di dati di convalida | ImageDirectory          | Set di dati di convalida per la valutazione ogni Epoch |

###  <a name="module-parameters"></a>Parametri del modulo  

| Nome          | Range            | Type    | Predefinito | Descrizione                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Periodi        | >0               | Integer | 5       | Seleziona la colonna contenente l'etichetta o la colonna del risultato |
| Dimensioni dei batch    | >0               | Integer | 16      | Numero di istanze di cui eseguire il training in un batch   |
| Velocità di apprendimento | >=double.Epsilon | Float   | 0,001   | Velocità di apprendimento iniziale per l'ottimizzatore di valori descent con sfumatura stocastica |
| Random seed   | Qualsiasi              | Integer | 1       | Valore di inizializzazione per il generatore di numeri casuali usato dal modello. |
| Pazienza      | >0               | Integer | 3       | Il numero di epoche per il training anticipato   |

###  <a name="outputs"></a>Output  

| Nome          | Type           | Description   |
| ------------- | -------------- | ------------- |
| Trained model | ModelDirectory | Trained model |

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 



