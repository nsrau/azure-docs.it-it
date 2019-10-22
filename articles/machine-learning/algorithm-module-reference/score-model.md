---
title: 'Modello di Punteggio: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Score Model nel servizio Azure Machine Learning per generare stime usando un modello di classificazione o regressione con training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f046b41d02ac392d003a9ecd15f25b43e5449cd0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692664"
---
# <a name="score-model-module"></a>Modulo Score Model

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per generare stime usando un modello di classificazione o regressione con training.

## <a name="how-to-use"></a>Utilizzo

1. Aggiungere il modulo **Score Model (Punteggio modello** ) alla pipeline.

2. Alleghi un modello sottoposto a training e un DataSet contenente i nuovi dati di input. 

    I dati devono essere in un formato compatibile con il tipo di modello sottoposto a training in uso. Lo schema del set di dati di input deve anche corrispondere generalmente allo schema dei dati utilizzati per il training del modello.

3. Eseguire la pipeline.

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

Un uso comune dei punteggi consiste nel restituire l'output come parte di un servizio Web predittivo. Per ulteriori informazioni, vedere questa esercitazione su come creare un servizio Web basato su una pipeline in Azure Machine Learning:


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 