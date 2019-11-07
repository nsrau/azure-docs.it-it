---
title: Eseguire il debug del modello
titleSuffix: ML Studio (classic) Azure
description: Come eseguire il debug degli errori prodotti dai moduli Train Model e Score Model in Azure Machine Learning Studio (classico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 0ab7f041da63731706742547a53df47462bc584a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619432"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Eseguire il debug del modello in Azure Machine Learning Studio (classico)

Quando si esegue un modello, è possibile riscontrare gli errori seguenti:

* il modulo [Train Model][train-model] genera un errore 
* il modulo [Score Model][score-model] genera risultati non corretti 

Questo articolo spiega le cause potenziali di questi errori.


## <a name="train-model-module-produces-an-error"></a>Il modulo Train Model genera un errore

![Immagine1](./media/debug-models/train_model-1.png)

Il modulo [Train Model][train-model] prevede due input:

1. Tipo di modello di Machine Learning dalla raccolta di modelli forniti da Azure Machine Learning Studio (classico).
2. I dati di training con una colonna Etichetta specifica che indica la variabile da stimare (le altre colonne vengono considerate funzionalità).

Questo modulo può generare un errore nei casi seguenti:

1. La colonna Etichetta è stata specificata in modo errato. Questo può succedere se è selezionata più di una colonna come Etichetta oppure se è selezionato un indice di colonna non corretto. Ad esempio, il secondo caso è applicabile se è stato usato un indice di colonna pari a 30 con un set di dati di input dotato di 25 colonne soltanto.

2. Il set di dati non contiene nessuna colonna per le caratteristiche. Se ad esempio il set di dati di input dispone solo di una colonna, contrassegnata come colonna Etichetta, non sono presenti caratteristiche con cui creare il modello. In questo caso, il modulo [Train Model][train-model] genera un errore.

3. Il set di dati di input (caratteristiche o etichetta) contiene Infinity come valore.

## <a name="score-model-module-produces-incorrect-results"></a>Il modulo Classificazione del modello genera risultati non corretti

![Immagine2](./media/debug-models/train_test-2.png)

In un tipico esperimento di training/testing per l'apprendimento supervisionato, il modulo [Split data][split] divide il set di dati originale in due parti: una parte viene utilizzata per eseguire il training del modello e una parte viene utilizzata per assegnare un punteggio alle prestazioni del modello sottoposto a training. Il modello sottoposto a training viene quindi usato per calcolare il punteggio dei dati di test, dopo il quale vengono valutati i risultati per determinare la precisione del modello.

Il modulo [Score Model][score-model] richiede due input:

1. Un output del modello sottoposto a training dal modulo [Train Model][train-model] .
2. Un set di dati per la classificazione diverso da quello usato per il training del modello.

È possibile che anche se l'esperimento ha esito positivo, il modulo [Score Model][score-model] genera risultati non corretti. Questo problema può essere dovuto a diversi scenari:

1. Se l'etichetta specificata è categorica e viene eseguito il training di un modello di regressione sui dati, viene generato un output errato dal modulo [Score Model][score-model] . Questo si verifica perché la regressione richiede una variabile di risposta continua. In questo caso sarebbe più opportuno usare un modello di classificazione. 

2. Analogamente, se un modello di classificazione è sottoposto a training su un set di dati con numeri a virgola mobile nella colonna Etichetta, tale modello può produrre risultati indesiderati. Questo si verifica perché la classificazione richiede una variabile di risposta discreta che ammette solo valori all'interno di un set di classi finito e di dimensioni ridotte.

3. Se il set di dati di assegnazione dei punteggi non contiene tutte le funzionalità usate per eseguire il training del modello, il [modello di Punteggio][score-model] genera un errore.

4. Se una riga nel set di dati di assegnazione dei punteggi contiene un valore mancante o un valore infinito per le sue funzionalità, il [modello di Punteggio][score-model] non produce alcun output corrispondente a tale riga.

5. Il [modello score][score-model] può produrre output identici per tutte le righe nel set di dati di assegnazione dei punteggi. Ciò potrebbe verificarsi, ad esempio, quando si tenta di eseguire una classificazione usando insiemi di decisioni se il numero minimo di esempi per nodo foglia viene scelto per superare il numero di esempi di training disponibili.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

