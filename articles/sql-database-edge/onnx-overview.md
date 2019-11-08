---
title: Machine Learning e intelligenza artificiale con ONNX nell'anteprima Edge del database SQL di Azure | Microsoft Docs
description: Machine Learning nell'anteprima Edge del database SQL di Azure supporta i modelli nel formato Open Neural Network Exchange (ONNX). ONNX è un formato aperto che è possibile usare per interscambiare modelli tra diversi Framework e strumenti di machine learning.
keywords: distribuire Edge database SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822842"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine Learning e intelligenza artificiale con ONNX in SQL database Edge Preview

Machine Learning nell'anteprima Edge del database SQL di Azure supporta i modelli nel formato [Open Neural Network Exchange (ONNX)](https://onnx.ai/) . ONNX è un formato aperto che è possibile usare per interscambiare modelli tra diversi [Framework e strumenti di Machine Learning](https://onnx.ai/supported-tools).

## <a name="overview"></a>Panoramica

Per dedurre modelli di machine learning nel database SQL di Azure Edge, è necessario innanzitutto ottenere un modello. Può trattarsi di un modello già sottoposto a training o di un modello personalizzato di cui è stato eseguito il training con il framework scelto. Edge del database SQL di Azure supporta il formato ONNX ed è necessario convertire il modello in questo formato. Non dovrebbe esserci alcun effetto sull'accuratezza del modello e, una volta ottenuto il modello ONNX, è possibile distribuire il modello nel database SQL di Azure Edge e usare [Il Punteggio nativo con la funzione di stima T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Ottenere modelli ONNX

Esistono diversi modi in cui è possibile ottenere un modello nel formato ONNX:

- [ONNX Model Zoo](https://github.com/onnx/models): contiene molti modelli ONNX con training preliminare per diversi tipi di attività che è possibile scaricare e pronti per l'uso.

- [Esportazione nativa da Framework di training di ml](https://onnx.ai/supported-tools): diversi framework di training supportano la funzionalità di esportazione nativa per ONNX, che consente di salvare il modello sottoposto a training in una versione specifica del formato ONNX, tra cui [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer e Caffe2. Inoltre, i servizi di creazione di modelli come la [funzionalità automatizzata Machine Learning in Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) e [Azure servizio visione artificiale personalizzato](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) forniscono l'esportazione ONNX.

- [Convertire i modelli esistenti](https://github.com/onnx/tutorials#converting-to-onnx-format): per i Framework che non supportano l'esportazione nativa, sono disponibili pacchetti autonomi per la conversione dei modelli nel formato ONNX. Per esempi ed esercitazioni, vedere [conversione in formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format). 

### <a name="supported-frameworks"></a>Framework supportati

I convertitori ONNX consentono di convertire i modelli sottoposti a training da Framework di Machine Learning diversi nel formato ONNX. I convertitori più diffusi includono: 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

Per l'elenco completo dei Framework supportati, vedere [conversione in formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limitazioni

Attualmente, non tutti i modelli ONNX sono supportati dal perimetro del database SQL di Azure. Il supporto è limitato ai modelli con **tipi di dati numerici**:

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [Real e float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Altri tipi numerici possono essere convertiti in tipi supportati usando [cast e Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Gli input del modello devono essere strutturati in modo che ogni input del modello corrisponda a una singola colonna in una tabella. Se ad esempio si usa un dataframe Pandas per eseguire il training di un modello, ogni input deve essere una colonna separata per il modello.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Edge del database SQL tramite portale di Azure](deploy-portal.md)
- [Distribuire un modello ONNX in anteprima Edge del database SQL di Azure](deploy-onnx.md)
