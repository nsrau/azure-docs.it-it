---
title: Apprendimento automatico e AI con ONNX in Azure SQL Database Edge Preview Documenti Microsoft
description: L'apprendimento automatico in Azure SQL Database Edge Preview supporta i modelli nel formato Open Neural Network Exchange (ONNX). ONNX è un formato aperto che è possibile utilizzare per scambiare modelli tra vari framework e strumenti di apprendimento automatico.
keywords: distribuire il perimetro del database sql
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366264"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine learning e AI con ONNX in SQL Database Edge Preview

L'apprendimento automatico in Azure SQL Database Edge Preview supporta i modelli nel formato [Open Neural Network Exchange (ONNX).](https://onnx.ai/) ONNX è un formato aperto che è possibile utilizzare per scambiare modelli tra vari framework e strumenti di [apprendimento automatico.](https://onnx.ai/supported-tools)

## <a name="overview"></a>Panoramica

Per dedurre i modelli di Apprendimento automatico in Azure SQL Database Edge, è innanzitutto necessario ottenere un modello. Può trattarsi di un modello pre-addestrato o di un modello personalizzato sottoposto a training con il framework desiderato. Azure SQL Database Edge supporta il formato ONNX ed è necessario convertire il modello in questo formato. Non dovrebbe esserci alcun impatto sull'accuratezza del modello e, una volta ottenuto il modello ONNX, è possibile distribuire il modello in Edge del database SQL di Azure e usare il [punteggio nativo con la funzione T-SQL PREDICT](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Ottenere modelli ONNX

Per ottenere un modello nel formato ONNX:

- **Servizi di creazione di**modelli: servizi come la [funzionalità automatizzata](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) di Machine Learning in Azure Machine Learning e [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) supportano l'esportazione diretta del modello sottoposto a training nel formato ONNX.

- [**Conversione e/o esportazione di modelli esistenti:**](https://github.com/onnx/tutorials#converting-to-onnx-format)diversi framework di training (ad esempio [PyTorch,](https://pytorch.org/docs/stable/onnx.html)Chainer e Caffe2) supportano la funzionalità di esportazione nativa in ONNX, che consente di salvare il modello sottoposto a training in una versione specifica del formato ONNX. Per i framework che non supportano l'esportazione nativa, sono disponibili pacchetti installabili ONNX Converter autonomi che consentono di convertire i modelli sottoposti a training da framework di Machine Learning diversi al formato ONNX.

     **Framework supportati**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-imparare](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Per l'elenco completo dei framework e degli esempi supportati, vedere Conversione in [formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limitazioni

Attualmente, non tutti i modelli ONNX sono supportati da Azure SQL Database Edge. Il supporto è limitato ai modelli con **tipi di dati numerici:**

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [reale e galleggiante](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Altri tipi numerici possono essere convertiti in tipi supportati utilizzando [CAST e CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Gli input del modello devono essere strutturati in modo che ogni input al modello corrisponda a una singola colonna in una tabella. Ad esempio, se si usa un frame di dati pandas per eseguire il training di un modello, ogni input deve essere una colonna separata per il modello.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Edge del database SQL tramite il portale di AzureDeploy SQL Database Edge through Azure portal](deploy-portal.md)
- [Distribuire un modello ONNX in Azure SQL Database Edge PreviewDeploy an ONNX model on Azure SQL Database Edge Preview](deploy-onnx.md)
