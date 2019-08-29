---
title: 'Modello di Punteggio: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Score Model nel servizio Azure Machine Learning per generare stime usando un modello di classificazione o regressione con training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 43a398b091b282da6ede06796250cda17117dc18
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128510"
---
# <a name="score-model-module"></a>Modulo Score Model

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per generare stime usando un modello di classificazione o regressione con training.

## <a name="how-to-use"></a>Utilizzo

1. Aggiungere il modulo **Score Model (Punteggio modello** ) all'esperimento.

2. Alleghi un modello sottoposto a training e un DataSet contenente i nuovi dati di input. 

    I dati devono essere in un formato compatibile con il tipo di modello sottoposto a training in uso. Lo schema del set di dati di input deve anche corrispondere generalmente allo schema dei dati utilizzati per il training del modello.

3. Eseguire l'esperimento.

## <a name="results"></a>Risultati

Dopo aver generato un set di punteggi utilizzando [Score Model](./score-model.md):

+ Per generare un set di metriche utilizzate per la valutazione dell'accuratezza del modello (prestazioni).  è possibile connettere il set di dati con punteggio per [valutare il modello](./evaluate-model.md), 
+ Fare clic con il pulsante destro del mouse sul modulo e selezionare **Visualizza** per visualizzare un esempio dei risultati.
+ Salvare i risultati in un set di dati.

Il Punteggio, o valore stimato, può essere in molti formati diversi, a seconda del modello e dei dati di input:

- Per i modelli di classificazione, [Score Model](./score-model.md) restituisce un valore stimato per la classe, nonché la probabilità del valore stimato.
- Per i modelli di regressione, [Score Model](./score-model.md) genera solo il valore numerico stimato.
- Per i modelli di classificazione delle immagini, il punteggio potrebbe essere la classe dell'oggetto nell'immagine o un valore booleano che indica se è stata trovata una funzionalità specifica.

## <a name="publish-scores-as-a-web-service"></a>Pubblicare i punteggi come servizio Web

Un uso comune dei punteggi consiste nel restituire l'output come parte di un servizio Web predittivo. Per altre informazioni, vedere questa esercitazione su come creare un servizio Web basato su un esperimento in Azure Machine Learning:


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 