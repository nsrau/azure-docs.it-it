---
title: Usare il modulo Score Image Model
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Score Image Model in Azure Machine Learning per generare stime usando un modello di immagine con training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fe57a9e8ce9b14f7d1346d819965576770afef3b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324890"
---
# <a name="score-image-model"></a>Score Image Model

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per generare stime usando un modello di immagine sottoposto a training sui dati dell'immagine di input.

## <a name="how-to-configure-score-image-model"></a>Come configurare il modello di immagine del Punteggio

1. Aggiungere il modulo **Score Image Model** alla pipeline.

2. Alleghi un modello di immagine sottoposto a training e un set di dati contenente dati immagine di input. 

    I dati devono essere di tipo ImageDirectory. Per ulteriori informazioni su come ottenere una directory di immagine, vedere il modulo [Convert to Image directory](convert-to-image-directory.md) . Lo schema del set di dati di input deve anche corrispondere generalmente allo schema dei dati utilizzati per il training del modello.

3. Inviare la pipeline.

## <a name="results"></a>Risultati

Dopo aver generato un set di punteggi usando il [modello di immagine del Punteggio](score-image-model.md), per generare un set di metriche usate per la valutazione dell'accuratezza del modello (prestazioni), è possibile connettere questo modulo e il set di dati con punteggio per [valutare il modello](evaluate-model.md). 

### <a name="publish-scores-as-a-web-service"></a>Pubblicare i punteggi come servizio Web

Un uso comune dei punteggi consiste nel restituire l'output come parte di un servizio Web predittivo. Per altre informazioni, vedere [questa esercitazione](../tutorial-designer-automobile-price-deploy.md) su come distribuire un endpoint in tempo reale basato su una pipeline in Azure Machine Learning Designer.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning.