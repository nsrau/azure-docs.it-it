---
title: 'Albero decisionale potenziato multiclasse: riferimento al moduloMulticlass Boosted Decision Tree: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Albero delle decisioni potenziato multiclasse in Azure Machine Learning per creare un classificatore usando i dati con etichetta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920060"
---
# <a name="multiclass-boosted-decision-tree"></a>Albero delle decisioni incrementato a più classi

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di apprendimento automatico basato sull'algoritmo degli alberi delle decisioni potenziato.

Un albero decisionale potenziato è un metodo di apprendimento dell'insieme in cui il secondo albero corregge gli errori del primo albero, il terzo albero corregge gli errori del primo e del secondo albero e così via. Le previsioni si basano sull'insieme di alberi insieme.

## <a name="how-to-configure"></a>Modalità di configurazione 

Questo modulo crea un modello di classificazione non sottoposto a training. Poiché la classificazione è un metodo di apprendimento supervisionato, è necessario un set di *dati con etichetta* che include una colonna label con un valore per tutte le righe.

È possibile eseguire il training di questo tipo di modello utilizzando il modello di [treno](././train-model.md). 

1.  Aggiungere il modulo **Albero decisionale potenziato multiclasse** alla pipeline.

1.  Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**

    + **Parametro singolo**: se si conosce la configurazione del modello, è possibile fornire un set specifico di valori come argomenti.
    
    + **Intervallo parametri**: Selezionare questa opzione se non si è sicuri dei parametri migliori e si desidera eseguire una sweep di parametro. Selezionare un intervallo di valori su cui eseguire l'iterazione e gli [Hyperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) scorrono tutte le possibili combinazioni delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  

1. **Il numero massimo di foglie per albero** limita il numero massimo di nodi terminali (foglie) che possono essere creati in qualsiasi albero.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **Il numero minimo di campioni per nodo foglia** indica il numero di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **La velocità** di apprendimento definisce la dimensione del passo durante l'apprendimento. Immettere un numero compreso tra 0 e 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Numero di alberi costruiti** indica il numero totale di alberi decisionali da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma saranno necessari tempi di training maggiori.

1. Il valore di **cui a /seed casuale** imposta facoltativamente un numero intero non negativo da utilizzare come valore di serie casuale. La specifica di un valore di serie garantisce la riproducibilità tra le esecuzioni con gli stessi dati e parametri.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Se si imposta **la modalità Crea trainer** su **Parametro singolo**, collegare un set di dati con tag e il modulo Train [Model.](./train-model.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
