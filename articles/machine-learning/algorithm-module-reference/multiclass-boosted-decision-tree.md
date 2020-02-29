---
title: 'Albero delle decisioni con boosting multiclasse: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di albero delle decisioni con boosting multiclasse in Azure Machine Learning per creare un classificatore usando i dati con etichetta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920060"
---
# <a name="multiclass-boosted-decision-tree"></a>Albero delle decisioni incrementato a più classi

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo degli alberi delle decisioni con boosting.

Un albero delle decisioni con boosting è un metodo di apprendimento dell'insieme in cui il secondo albero corregge gli errori del primo albero, il terzo albero corregge gli errori del primo e del secondo albero e così via. Le stime si basano sull'insieme di alberi.

## <a name="how-to-configure"></a>Modalità di configurazione 

Questo modulo crea un modello di classificazione non sottoposto a training. Poiché la classificazione è un metodo di apprendimento supervisionato, è necessario un *set di dati con etichetta* che include una colonna Label con un valore per tutte le righe.

Per eseguire il training di questo tipo di modello, è possibile usare il [modello Train](././train-model.md). 

1.  Aggiungere il modulo di **albero delle decisioni con boosting multiclasse** alla pipeline.

1.  Specificare come si desidera eseguire il training del modello impostando l'opzione **crea modalità trainer** .

    + **Singolo parametro**: se si sa come si desidera configurare il modello, è possibile fornire un set di valori specifico come argomenti.
    
    + **Intervallo parametri**: selezionare questa opzione se non si è certi dei parametri migliori e si vuole eseguire uno sweep di parametri. Selezionare un intervallo di valori di cui eseguire l'iterazione e l' [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) esegue l'iterazione su tutte le combinazioni possibili delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  

1. Il **numero massimo di foglie per albero** limita il numero massimo di nodi terminali (foglie) che possono essere creati in qualsiasi albero.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. Il **numero minimo di campioni per nodo foglia** indica il numero di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. La **velocità di apprendimento** definisce le dimensioni del passaggio durante l'apprendimento. Immettere un numero compreso tra 0 e 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. Il **numero di alberi costruiti** indica il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma saranno necessari tempi di training maggiori.

1. Il valore di **inizializzazione casuale dei numeri** imposta facoltativamente un numero intero non negativo da utilizzare come valore di inizializzazione casuale. La specifica di un valore di inizializzazione garantisce la riproducibilità tra esecuzioni con gli stessi dati e parametri.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](./train-model.md) .

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
