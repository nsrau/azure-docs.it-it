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
ms.date: 11/04/2019
ms.openlocfilehash: 4771db21a0ea5e841dbe12c8ce915b3833a8a30d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692317"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine Learning e intelligenza artificiale con ONNX in SQL database Edge Preview

Machine Learning nell'anteprima Edge del database SQL di Azure supporta i modelli nel formato [Open Neural Network Exchange (ONNX)](https://onnx.ai/) . ONNX è un formato aperto che è possibile usare per interscambiare modelli tra diversi [Framework e strumenti di Machine Learning](https://onnx.ai/supported-tools).

## <a name="supported-tool-kits"></a>Kit di strumenti supportati

ONNXMLTools consente di convertire i modelli da diversi kit di strumenti di Machine Learning in un modello ONNX. Attualmente, per i tipi di dati numerici e gli input a colonna singola, sono supportati i kit di strumenti seguenti:

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (sperimentale)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>Ottenere modelli ONNX

Esistono diversi modi in cui è possibile ottenere un modello nel formato ONNX:

- [ONNX Model Zoo](https://github.com/onnx/models): contiene diversi modelli ONNX con training preliminare per diversi tipi di attività. È possibile scaricare e usare le versioni supportate da Windows Machine Learning.

- [Esportazione nativa da Framework di training di Machine Learning](https://onnx.ai/supported-tools): diversi framework di training supportano la funzionalità di esportazione nativa per ONNX, che consente di salvare il modello sottoposto a training in una versione specifica del formato ONNX. Ad esempio, Chainer, Caffee2 e PyTorch. Inoltre, i servizi come [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning-service/) e [Azure visione personalizzata](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) forniscono anche l'esportazione ONNX nativa.

  - Per informazioni su come eseguire il training e l'esportazione di un modello ONNX nel cloud usando Visione personalizzata, vedere [esercitazione: usare un modello ONNX da visione personalizzata con Windows ml (anteprima)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml).

- [Convertire i modelli esistenti usando WinMLTools](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools): questo pacchetto python consente la conversione dei modelli da diversi formati di Framework di training a ONNX. È possibile specificare la versione di ONNX in cui si desidera convertire il modello, a seconda delle build di Windows di destinazione dell'applicazione. Se non si ha familiarità con Python, è possibile usare il [Dashboard basato sull'interfaccia utente di Windows Machine Learning](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) per convertire i modelli.

> [!IMPORTANT]
> Non tutte le versioni di ONNX sono supportate dal perimetro del database SQL di Azure. Attualmente è supportata solo la stima dei tipi di dati numerici tramite il modello ONNX.

Dopo aver creato un modello ONNX, è possibile distribuire il modello in Edge del database SQL di Azure. È quindi possibile usare [Il Punteggio nativo con la funzione stima T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="limitations"></a>Limitazioni

Attualmente, questo supporto è limitato ai modelli con **tipi di dati numerici**:

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [Real e float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Altri tipi numerici possono essere convertiti in tipi supportati tramite cast e CONVERT [cast e Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Gli input del modello devono essere strutturati in modo che ogni input del modello corrisponda a una singola colonna in una tabella. Se ad esempio si usa un dataframe Pandas per eseguire il training di un modello, ogni input deve essere una colonna separata per il modello.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Edge del database SQL tramite portale di Azure](deploy-portal.md)
- [Distribuire un modello ONNX in anteprima Edge del database SQL di Azure](deploy-onnx.md)
