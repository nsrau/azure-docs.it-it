---
title: Eseguire il debug del modello
titleSuffix: Azure Machine Learning Studio
description: Come eseguire il debug di errori generati dai moduli dal training e dalla classificazione del modello in Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 9c505262030e5b5aa13b8d221cf1e39c4a9c7833
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751122"
---
# <a name="debug-your-model-in-azure-machine-learning-studio"></a>Debug del modello in Azure Machine Learning Studio

Quando si esegue un modello, è possibile riscontrare gli errori seguenti:

* il modulo [Train Model][train-model] genera un errore 
* il modulo [Score Model][score-model] genera risultati non corretti 

Questo articolo spiega le cause potenziali di questi errori.


## <a name="train-model-module-produces-an-error"></a>Il modulo Train Model genera un errore

![Immagine1](./media/debug-models/train_model-1.png)

Il modulo [Training del modello][train-model] prevede i due valori di input seguenti:

1. Il tipo di modello di Machine Learning della raccolta di modelli forniti da Azure Machine Learning Studio.
2. I dati di training con una colonna Etichetta specifica che indica la variabile da stimare (le altre colonne vengono considerate funzionalità).

Questo modulo può generare un errore nei casi seguenti:

1. La colonna Etichetta è stata specificata in modo errato. Questo può succedere se è selezionata più di una colonna come Etichetta oppure se è selezionato un indice di colonna non corretto. Ad esempio, il secondo caso è applicabile se è stato usato un indice di colonna pari a 30 con un set di dati di input dotato di 25 colonne soltanto.

2. Il set di dati non contiene nessuna colonna per le caratteristiche. Se ad esempio il set di dati di input dispone solo di una colonna, contrassegnata come colonna Etichetta, non sono presenti caratteristiche con cui creare il modello. In questo caso, il modulo [Train Model][train-model] genera un errore.

3. Il set di dati di input (caratteristiche o etichetta) contiene Infinity come valore.

## <a name="score-model-module-produces-incorrect-results"></a>Il modulo Classificazione del modello genera risultati non corretti

![Immagine2](./media/debug-models/train_test-2.png)

In un tipico esperimento di training/testing per l'apprendimento supervisionato, il modulo [Divisione dei dati][split] separa il set di dati originale in due parti: una usata per eseguire il training del modello e l'altra riservata alla classificazione delle prestazioni del modello. Il modello sottoposto a training viene quindi usato per calcolare il punteggio dei dati di test, dopo il quale vengono valutati i risultati per determinare la precisione del modello.

Il modulo [Score Model][score-model] richiede due input:

1. Output del modello sottoposto a training dal modulo [Training modello][train-model].
2. Un set di dati per la classificazione diverso da quello usato per il training del modello.

Può accadere che, nonostante il buon esito dell'esperimento, il modulo [Classificazione modello][score-model] produca risultati non corretti. Questo problema può essere dovuto a diversi scenari:

1. Se l'etichetta specificata è categorica e un modello di regressione è sottoposto a training sui dati, può essere prodotto un output errato dal modulo [Score Model][score-model]. Questo si verifica perché la regressione richiede una variabile di risposta continua. In questo caso sarebbe più opportuno usare un modello di classificazione. 

2. Analogamente, se un modello di classificazione è sottoposto a training su un set di dati con numeri a virgola mobile nella colonna Etichetta, tale modello può produrre risultati indesiderati. Questo si verifica perché la classificazione richiede una variabile di risposta discreta che ammette solo valori all'interno di un set di classi finito e di dimensioni ridotte.

3. Se il set di dati non contiene tutte le caratteristiche usate per eseguire il training del modello, il modulo [Score Model][score-model] genera un errore.

4. Il modulo [Score Model][score-model] non genera alcun output corrispondente a una riga nel set di dati di punteggio contenente un valore mancante o infinito per una delle proprie caratteristiche.

5. Il modulo [Score Model][score-model] può generare output identici per tutte le righe nel set di dati di punteggio. Ciò potrebbe verificarsi, ad esempio, quando si tenta di eseguire una classificazione usando insiemi di decisioni se il numero minimo di esempi per nodo foglia viene scelto per superare il numero di esempi di training disponibili.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

