---
title: 'Regressione della foresta delle decisioni: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Two-Class averaged perceptron in Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo perceptron medio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 53e40726a5745263ee2b3cb4ada8671bf65da963
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137672"
---
# <a name="two-class-averaged-perceptron-module"></a>Modulo perceptron medio a due classi

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo perceptron medio.  
  
Questo algoritmo di classificazione è un metodo di apprendimento supervisionato e richiede un *set di dati con tag*, che include una colonna di etichetta. Per eseguire il training del modello, è possibile fornire il modello e il set di dati con tag come input per il [training del modello](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input.  

### <a name="about-averaged-perceptron-models"></a>Informazioni sui modelli perceptron medi

Il *Metodo perceptron mediato* è una versione iniziale e semplice di una rete neurale. In questo approccio, gli input sono classificati in diversi output possibili basati su una funzione lineare e quindi combinati con un set di pesi derivati dal vettore di funzionalità, di conseguenza il nome "perceptron".

I modelli più semplici di percettrone sono adatti per l'apprendimento di modelli separabili in modo lineare, mentre le reti neurali, specialmente quelle profonde, consentono di modellare limiti di classe più complessi. I percettroni sono tuttavia più rapidi e poiché elaborano i casi in serie, possono essere usati con il training continuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Come configurare la media con due classi perceptron

1.  Aggiungere il modulo **Two-Class averaged perceptron** alla pipeline.  

2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: se si sa come si desidera configurare il modello, fornire un set di valori specifico come argomenti.

    -   **Intervallo parametri**: selezionare questa opzione se non si è certi dei parametri migliori e si vuole eseguire uno sweep di parametri. Selezionare un intervallo di valori di cui eseguire l'iterazione e l' [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) esegue l'iterazione su tutte le combinazioni possibili delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  
  
3.  Per la **velocità di apprendimento**specificare un valore per la velocità di *apprendimento*. I valori della velocità di apprendimento controllano le dimensioni del passaggio usato nella discesa della sfumatura stocastica ogni volta che il modello viene testato e corretto.
  
     Rendendo la percentuale più piccola, si testa il modello più spesso, con il rischio che si possa rimanere bloccati in un plateau locale. Estendendo le dimensioni del passaggio, è possibile rendere più veloce la convergenza con il rischio di oltrepassare il valore minimo true.
  
4.  Per il **numero massimo di iterazioni**, digitare il numero di volte in cui si desidera che l'algoritmo esamini i dati di training.  
  
     L'interruzione anticipata spesso fornisce una migliore generalizzazione. L'aumento del numero di iterazioni migliora l'adattamento, con il rischio di overfitting.
  
5.  Per il valore di **inizializzazione numerico casuale**, digitare facoltativamente un valore intero da utilizzare come valore di inizializzazione. L'uso di un valore di inizializzazione è consigliato se si vuole garantire la riproducibilità della pipeline tra le esecuzioni.  
  
1.  Connettere un set di dati di training ed eseguire il training del modello:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](train-model.md) .  
  
    + Se si imposta la **modalità di creazione dell'allenatore** sull'intervallo di **parametri**, connettere un set di dati con tag ed eseguire il training del modello usando gli [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se si passa un intervallo di parametri a [Training Model](train-model.md), viene utilizzato solo il valore predefinito nell'elenco di parametri singoli.  
    > 
    > Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.  
    > 
    > Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.




## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 