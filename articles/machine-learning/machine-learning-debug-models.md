---
title: Eseguire il debug del modello in Azure Machine Learning | Documentazione Microsoft
description: Viene illustrato come eseguire il debug del modello in Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: bradsev;garye
translationtype: Human Translation
ms.sourcegitcommit: 8c2fb72de830a699c96f3593e9f07a871614267b
ms.openlocfilehash: 58e6cc28a624b0d873e3eabeba971cc8e909267a


---
# <a name="debug-your-model-in-azure-machine-learning"></a>Debug del modello in Azure Machine Learning
Questo articolo illustra come eseguire il debug dei modelli in Microsoft Azure Machine Learning. In particolare, fornisce i potenziali motivi per i quali possono verificarsi i due errori indicati di seguito durante l'esecuzione di un modello:

* il modulo [Train Model][train-model] genera un errore 
* il modulo [Score Model][score-model] genera risultati non corretti 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>Il modulo Train Model genera un errore
![Immagine1](./media/machine-learning-debug-models/train_model-1.png)

Il modulo [Train Model][train-model] prevede i due valori di input seguenti:

1. Il tipo di modello di regressione o classificazione dalla raccolta di modelli forniti da Azure Machine Learning
2. I dati di training con una colonna Etichetta specificata. La colonna Etichetta specifica la variabile da stimare. Il resto delle colonne incluse rappresentano le caratteristiche.

Questo modulo genera un errore nei casi seguenti:

1. La colonna Etichetta è specificata in modo errato perché è selezionata più di una colonna come etichetta oppure perché è selezionato un indice di colonna non corretto. Ad esempio, il secondo caso è applicabile se è stato usato un indice di colonna pari a 30 con un set di dati di input dotato di 25 colonne soltanto.
2. Il set di dati non contiene nessuna colonna per le caratteristiche. Se ad esempio il set di dati di input dispone solo di una colonna, contrassegnata come colonna Etichetta, non sono presenti caratteristiche con cui creare il modello. In questo caso, il modulo [Train Model][train-model] genera un errore.
3. Il set di dati di input (caratteristiche o etichetta) contiene Infinity come valore.

## <a name="score-model-module-does-not-produce-correct-results"></a>Il modulo Score Model non produce risultati corretti
![Immagine2](./media/machine-learning-debug-models/train_test-2.png)

In un tipico grafico di training/testing per l'apprendimento sorvegliato, il modulo [Split Data][split] separa il set di dati originale in due parti: una usata per eseguire il training del modello e l'altra riservata alla classificazione delle prestazioni del modello sottoposto a training sui dati. Il modello sottoposto a training viene quindi usato per calcolare il punteggio dei dati di test, dopo il quale vengono valutati i risultati per determinare la precisione del modello.

Il modulo [Score Model][score-model] richiede due input:

1. Un output del modello sottoposto a training dal modulo [Train Model][train-model]
2. Un set di dati diverso da quello usato per il training del modello

Può accadere che, nonostante il buon esito dell'esperimento, il modulo [Score Model][score-model] produca risultati non corretti. Ciò può essere dovuto a diversi scenari:

1. Se l'etichetta specificata è categorica e un modello di regressione è sottoposto a training sui dati, può essere prodotto un output errato dal modulo [Score Model][score-model]. Questo si verifica perché la regressione richiede una variabile di risposta continua. In questo caso sarebbe più opportuno usare un modello di classificazione. 
2. Analogamente, se un modello di classificazione è sottoposto a training su un set di dati con numeri a virgola mobile nella colonna Etichetta, tale modello può produrre risultati indesiderati. Questo si verifica perché la classificazione richiede una variabile di risposta discreta che ammette solo valori all'interno di un set di classi finito e solitamente di dimensioni ridotte.
3. Se il set di dati non contiene tutte le caratteristiche usate per eseguire il training del modello, il modulo [Score Model][score-model] genera un errore.
4. Il modulo [Score Model][score-model] non genera alcun output corrispondente a una riga nel set di dati di punteggio contenente un valore mancante o infinito per una delle proprie caratteristiche.
5. Il modulo [Score Model][score-model] può generare output identici per tutte le righe nel set di dati di punteggio. Ciò potrebbe verificarsi, ad esempio, quando si tenta di eseguire una classificazione usando insiemi di decisioni se il numero minimo di esempi per nodo foglia viene scelto per superare il numero di esempi di training disponibili.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/




<!--HONumber=Dec16_HO2-->


