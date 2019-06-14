---
title: 'Score Model: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Score Model nel servizio Azure Machine Learning per generare stime usando un modello di regressione o classificazione sottoposto a training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029266"
---
# <a name="score-model-module"></a>Modulo Score Model

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per generare stime usando un modello di classificazione o regressione con training.

## <a name="how-to-use"></a>Utilizzo

1. Aggiungere il **Score Model** modulo nell'esperimento.

2. Collegare un modello con training e set di dati contenente nuovi dati di input. 

    I dati devono essere in un formato compatibile con il tipo di modello con Training in uso. Lo schema del set di dati di input deve corrispondere anche a livello generale lo schema dei dati utilizzati per il training del modello.

3. Eseguire l'esperimento.

## <a name="results"></a>Risultati

Dopo aver generato un set di punteggi usando [Score Model](./score-model.md):

+ Per generare un set di metriche da usare per valutare l'accuratezza del modello (prestazioni).  è possibile connettere il set di dati con punteggio [Evaluate Model](./evaluate-model.md), 
+ Il modulo e scegliere **Visualize** per vedere un esempio dei risultati.
+ Salvare i risultati in un set di dati.

Il punteggio, o valore stimato, può essere in molti formati diversi, a seconda del modello e i dati di input:

- Per i modelli di classificazione [Score Model](./score-model.md) restituisce un valore stimato per la classe, nonché la probabilità del valore stimato.
- Modelli di regressione [Score Model](./score-model.md) genera solo il valore numerico previsto.
- Per i modelli di classificazione di immagini, il punteggio potrebbe essere la classe dell'oggetto nell'immagine o un valore booleano che indica se è stata trovata una determinata funzionalità.

## <a name="publish-scores-as-a-web-service"></a>Pubblicare i punteggi come servizio web

Un uso comune di assegnazione dei punteggi è per restituire l'output come parte di un servizio web predittivo. Per altre informazioni, vedere l'esercitazione su come creare un servizio web basato su un esperimento in Azure Machine Learning:


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 