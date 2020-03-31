---
title: 'Regressione foresta decisionale: riferimento al moduloDecision Forest Regression: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Perceptron media a due classi in Azure Machine Learning per creare un modello di apprendimento automatico basato sull'algoritmo di percettore medio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920774"
---
# <a name="two-class-averaged-perceptron-module"></a>Modulo perceptrona media a due classi

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di apprendimento automatico basato sull'algoritmo di percettrone media.  
  
Questo algoritmo di classificazione è un metodo di apprendimento supervisionato e richiede un set di *dati con tag,* che include una colonna label. È possibile eseguire il training del modello fornendo il modello e il set di dati con tag come input per il training [del modello](./train-model.md). Il modello sottoposto a training può quindi essere utilizzato per stimare i valori per i nuovi esempi di input.  

### <a name="about-averaged-perceptron-models"></a>Informazioni sui modelli medi di percettori

Il *metodo percettore medio* è una versione iniziale e semplice di una rete neurale. In questo approccio, gli input sono classificati in diversi output possibili in base a una funzione lineare e quindi combinati con un set di pesi derivati dal vettore di funzionalità, da cui il nome "perceptron".

I modelli più semplici di percettrone sono adatti per l'apprendimento di modelli separabili in modo lineare, mentre le reti neurali, specialmente quelle profonde, consentono di modellare limiti di classe più complessi. I percettroni sono tuttavia più rapidi e poiché elaborano i casi in serie, possono essere usati con il training continuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Come configurare percettore medio a due classi

1.  Aggiungere il modulo **Perceptron media a due** classi alla pipeline.  

2.  Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**  
  
    -   **Parametro singolo**: Se si sa come si desidera configurare il modello, fornire un set specifico di valori come argomenti.

    -   **Intervallo parametri**: Selezionare questa opzione se non si è sicuri dei parametri migliori e si desidera eseguire una sweep di parametro. Selezionare un intervallo di valori su cui eseguire l'iterazione e gli [Hyperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) scorrono tutte le possibili combinazioni delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  
  
3.  Per **Tasso di apprendimento**, specificare un valore per la velocità di *apprendimento*. I valori della velocità di apprendimento controllano le dimensioni del passo utilizzato nella discesa del gradiente stocastico ogni volta che il modello viene testato e corretto.
  
     Riducendo la velocità, si testa il modello più spesso, con il rischio di rimanere bloccati in un altopiano locale. Estendendo le dimensioni del passaggio, è possibile rendere più veloce la convergenza con il rischio di oltrepassare il valore minimo true.
  
4.  Per **Numero massimo di iterazioni**, digitare il numero di volte in cui si desidera che l'algoritmo esamini i dati di training.  
  
     L'interruzione anticipata spesso fornisce una migliore generalizzazione. L'aumento del numero di iterazioni migliora l'adattamento, con il rischio di overfitting.
  
5.  Per **Valore di errore di tipo casuale**, digitare facoltativamente un valore intero da utilizzare come valore di serie. L'uso di un valore di secondo valore è consigliato se si desidera garantire la riproducibilità della pipeline tra le esecuzioni.  
  
1.  Collegare un set di dati di training e uno dei moduli di training:Connect a training dataset, and one of the training modules:
  
    -   Se si imposta **La modalità Crea trainer** su **Parametro singolo**, utilizzare il modulo Modello di [treno.](train-model.md)




## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 