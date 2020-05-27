---
title: Machine Learning e intelligenza artificiale con ONNX in SQL Edge di Azure (anteprima)
description: Machine Learning in SQL Edge di Azure (anteprima) supporta i modelli nel formato Open Neural Network Exchange (ONNX). ONNX è un formato aperto che è possibile usare per l'interscambio di modelli tra diversi framework e strumenti di Machine Learning.
keywords: Distribuire SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 12c1294a804c2063d405c5ec08440865283d51d3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594590"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge-preview"></a>Machine Learning e intelligenza artificiale con ONNX in SQL Edge (anteprima)

Machine Learning in SQL Edge di Azure (anteprima) supporta i modelli nel formato [Open Neural Network Exchange (ONNX)](https://onnx.ai/). ONNX è un formato aperto che è possibile usare per l'interscambio di modelli tra diversi [framework e strumenti di Machine Learning](https://onnx.ai/supported-tools).

## <a name="overview"></a>Panoramica

Per dedurre i modelli di Machine Learning in SQL Edge di Azure, è necessario innanzitutto ottenere un modello. Può trattarsi di un modello già sottoposto a training o di un modello personalizzato di cui è stato eseguito il training con il framework scelto. SQL Edge di Azure supporta il formato ONNX ed è necessario convertire il modello in questo formato. La conversione non dovrebbe avere alcun effetto sull'accuratezza del modello e, una volta disponibile, è possibile distribuire il modello ONNX in SQL Edge di Azure e usare l'[assegnazione dei punteggi nativa con la funzione T-SQL PREDICT](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Ottenere modelli ONNX

Per ottenere un modello nel formato ONNX:

- **Servizi di creazione dei modelli**: servizi quali la [funzionalità di Machine Learning automatizzato in Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) e il [servizio Visione personalizzata di Azure](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) supportano direttamente l'esportazione del modello sottoposto a training nel formato ONNX.

- [**Convertire e/o esportare modelli esistenti**](https://github.com/onnx/tutorials#converting-to-onnx-format): diversi framework di training (ad esempio [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer e Caffe2) supportano la funzionalità di esportazione nativa in formato ONNX, che consente di salvare il modello sottoposto a training in una versione specifica del formato ONNX. Per i framework che non supportano l'esportazione nativa, sono disponibili pacchetti autonomi installabili del convertitore ONNX che consentono di convertire nel formato ONNX i modelli sottoposti a training di framework di Machine Learning diversi.

     **Framework supportati**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Per l'elenco completo dei framework e degli esempi supportati, vedere [Conversione in formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limitazioni

Attualmente, in SQL Edge di Azure non sono supportati tutti i modelli ONNX. Il supporto è limitato ai modelli con **tipi di dati numerici**:

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real e float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Per convertire altri tipi numerici in tipi supportati, è possibile usare [CAST e CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Gli input del modello devono essere strutturati in modo che ognuno corrisponda a una singola colonna in una tabella. Se ad esempio si usa un dataframe Pandas per eseguire il training di un modello, ogni input deve essere una colonna separata per il modello.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire SQL Edge tramite il portale di Azure](deploy-portal.md)
- [Distribuire un modello ONNX in SQL Edge di Azure (anteprima)](deploy-onnx.md)
