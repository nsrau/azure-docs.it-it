---
title: 'Regressione della foresta delle decisioni: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Two-Class averaged perceptron nel servizio Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo perceptron medio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f560923b0a5457ac5fd03c7f76fc4315c6ca08e8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128431"
---
# <a name="two-class-averaged-perceptron-module"></a>Modulo perceptron medio a due classi

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo perceptron medio.  
  
Questo algoritmo di classificazione è un metodo di apprendimento supervisionato e richiede un *set di dati con tag*, che include una colonna di etichetta. Per eseguire il training del modello, è possibile fornire il modello e il set di dati con tag come input per il [training del modello](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input.  

### <a name="about-averaged-perceptron-models"></a>Informazioni sui modelli perceptron medi

Il *Metodo perceptron mediato* è una versione iniziale e semplice di una rete neurale. In questo approccio, gli input sono classificati in diversi output possibili basati su una funzione lineare e quindi combinati con un set di pesi derivati dal vettore di funzionalità, di conseguenza il nome "perceptron".

I modelli perceptron più semplici sono adatti per apprendere modelli separabili in modo lineare, mentre le reti neurali, in particolare le reti neurali profonde, possono modellare limiti di classe più complessi. Tuttavia, perceptron sono più veloci e, dal momento che elaborano i case in modo seriale, perceptron può essere usato con il training continuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Come configurare la media con due classi perceptron

1.  Aggiungere il modulo **Two-Class averaged perceptron** all'esperimento.  

2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: Se si conosce il modo in cui si desidera configurare il modello, fornire un set di valori specifico come argomenti.
  
3.  Per la **velocità di apprendimento**specificare un valore per la velocità di *apprendimento*. I valori della velocità di apprendimento controllano le dimensioni del passaggio usato nella discesa della sfumatura stocastica ogni volta che il modello viene testato e corretto.
  
     Rendendo la percentuale più piccola, si testa il modello più spesso, con il rischio che si possa rimanere bloccati in un plateau locale. Rendendo il passaggio più grande, è possibile convergere più velocemente, a rischio di sovrascattare il valore true minime.
  
4.  Per il **numero massimo di iterazioni**, digitare il numero di volte in cui si desidera che l'algoritmo esamini i dati di training.  
  
     L'arresto anticipato spesso offre una maggiore generalizzazione. L'aumento del numero di iterazioni migliora l'adattamento, a rischio di overfitting.
  
5.  Per il valore di **inizializzazione numerico casuale**, digitare facoltativamente un valore intero da utilizzare come valore di inizializzazione. L'uso di un valore di inizializzazione è consigliato se si vuole garantire la riproducibilità dell'esperimento tra le esecuzioni.  
  
1.  Connettere un set di dati di training e uno dei moduli di training:
  
    -   Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](train-model.md) .

## <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare un riepilogo dei parametri del modello, insieme ai pesi delle funzionalità appresi dal training, fare clic con il pulsante destro del mouse sull'output di [Train Model](./train-model.md).


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 