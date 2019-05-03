---
title: 'Regressione tramite foresta delle decisioni: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Two-Class Averaged Perceptron nel servizio Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo di percettrone.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029236"
---
# <a name="two-class-averaged-perceptron-module"></a>Modulo Two-Class Averaged Perceptron

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo di percettrone.  
  
Questo algoritmo di classificazione è un metodo di apprendimento supervisionato e richiede un *set di dati con tag*, che include una colonna di etichetta. È possibile eseguirne il training, fornendo come input per il modello e il set di dati con tag [Train Model](./train-model.md). Il modello con training può quindi utilizzabile per stimare i valori per i nuovi esempi di input.  

### <a name="about-averaged-perceptron-models"></a>Informazioni sui modelli averaged perceptron

Il *calcolata la media metodo perceptron* è una prima versione semplice di una rete neurale. Questo approccio, gli input sono classificati in diversi output possibili basati su una funzione lineare e quindi combinati con un set di medie derivate dal vettore, pertanto il nome "percettrone".

I modelli più semplici di percettrone sono adatti per l'apprendimento di modelli separabili in modo lineare, mentre le reti neurali (in particolare le reti neurali profonde) può modellare limiti di classe più complessi. Tuttavia, i percettroni sono più veloci, e poiché elaborano i casi in serie, i percettroni sono utilizzabili con training continuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Jak nakonfigurovat Two-Class Averaged Perceptron

1.  Aggiungere il **Two-Class Averaged Perceptron** modulo nell'esperimento.  

2.  Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.  
  
    -   **Singolo parametro**: Se si conosce la modalità con cui si desidera configurare il modello, definire un set specifico di valori come argomenti.
  
3.  Per la **velocità di apprendimento**, specificare un valore per il *velocità di apprendimento*. La velocità di apprendimento i valori di controllare le dimensioni del passaggio che viene usato nei valori descent con sfumatura stocastico ogni volta che il modello viene testato e corretto.
  
     Impostando la frequenza più piccoli, il modello viene testato più spesso, con il rischio che potrebbero rimanere bloccati in a livello locale. Per rendere il passaggio più grande, possono più veloce la convergenza con il rischio di oltrepassare il valore minimo true.
  
4.  Per la **numero massimo di iterazioni**, digitare il numero di volte in cui si desidera che l'algoritmo per esaminare i dati di training.  
  
     L'interruzione anticipata spesso fornisce una migliore generalizzazione. L'aumento del numero di iterazioni migliora l'adattamento, con il rischio di overfitting.
  
5.  Per la **seed numeri casuali**, facoltativamente, digitare un valore intero da utilizzare come valore di inizializzazione. Uso di un valore di inizializzazione è consigliato se si desidera garantire la riproducibilità dell'esperimento in esecuzione.  
  
1.  Connettere un set di dati di training e uno dei moduli di formazione:
  
    -   Se si imposta **modalità di creazione trainer** al **singolo parametro**, usare il [Train Model](train-model.md) modulo.

## <a name="results"></a>Risultati

Dopo aver completata la formazione:

+ Per visualizzare un riepilogo dei parametri del modello, con i pesi delle funzionalità appreso dal training, fare doppio clic sull'output del [Train Model](./train-model.md).


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 