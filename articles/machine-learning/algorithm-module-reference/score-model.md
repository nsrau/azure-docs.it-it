---
title: 'Modello di punteggio: Riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Modello di punteggio in Azure Machine Learning per generare stime usando un modello di classificazione o regressione sottoposto a training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364179"
---
# <a name="score-model-module"></a>Modulo Score Model

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per generare stime usando un modello di classificazione o regressione sottoposto a training.

## <a name="how-to-use"></a>Utilizzo

1. Aggiungere il modulo Modello di **punteggio** alla pipeline.

2. Collegare un modello sottoposto a training e un set di dati contenente nuovi dati di input. 

    I dati devono essere in un formato compatibile con il tipo di modello sottoposto a training in uso. Lo schema del set di dati di input deve in genere corrispondere allo schema dei dati usati per eseguire il training del modello.

3. Inviare la pipeline.

## <a name="results"></a>Risultati

Dopo aver generato una serie di punteggi utilizzando [Score Model](./score-model.md):

+ Per generare un set di metriche utilizzate per valutare l'accuratezza (prestazioni) del modello, è possibile connettere il set di dati con punteggio a [Valuta modello](./evaluate-model.md), 
+ Fare clic con il pulsante destro del mouse sul modulo e selezionare **Visualizza** per visualizzare un esempio dei risultati.
<!-- + To Save the results to a dataset. -->

Il punteggio, o valore stimato, può essere in molti formati diversi, a seconda del modello e dei dati di input:

- Per i modelli di classificazione, Modello di [punteggio](./score-model.md) restituisce un valore stimato per la classe, nonché la probabilità del valore stimato.
- Per i modelli di regressione, [Modello di punteggio](./score-model.md) genera solo il valore numerico stimato.


## <a name="publish-scores-as-a-web-service"></a>Pubblicare punteggi come servizio Web

Un utilizzo comune del punteggio consiste nel restituire l'output come parte di un servizio Web predittivo. Per altre informazioni, vedere [questa esercitazione](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) su come distribuire un endpoint in tempo reale basato su una pipeline in Progettazione Azure Machine Learning.For more information, see this tutorial on how to deploy a real-time endpoint based on a pipeline in Azure Machine Learning designer.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 