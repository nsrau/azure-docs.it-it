---
title: 'Regressione della foresta delle decisioni: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Two-Class averaged perceptron in Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo perceptron medio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 62e2168c674693ded31bd94206e2e13c82c67b68
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546385"
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
  
3.  Per la **velocità di apprendimento**specificare un valore per la velocità di *apprendimento*. I valori della velocità di apprendimento controllano le dimensioni del passaggio usato nella discesa della sfumatura stocastica ogni volta che il modello viene testato e corretto.
  
     Rendendo la percentuale più piccola, si testa il modello più spesso, con il rischio che si possa rimanere bloccati in un plateau locale. Estendendo le dimensioni del passaggio, è possibile rendere più veloce la convergenza con il rischio di oltrepassare il valore minimo true.
  
4.  Per il **numero massimo di iterazioni**, digitare il numero di volte in cui si desidera che l'algoritmo esamini i dati di training.  
  
     L'interruzione anticipata spesso fornisce una migliore generalizzazione. L'aumento del numero di iterazioni migliora l'adattamento, con il rischio di overfitting.
  
5.  Per il valore di **inizializzazione numerico casuale**, digitare facoltativamente un valore intero da utilizzare come valore di inizializzazione. L'uso di un valore di inizializzazione è consigliato se si vuole garantire la riproducibilità della pipeline tra le esecuzioni.  
  
1.  Connettere un set di dati di training e uno dei moduli di training:
  
    -   Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](train-model.md) .




## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 