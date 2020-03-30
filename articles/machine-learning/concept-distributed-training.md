---
title: Che cos'è la formazione distribuita?
titleSuffix: Azure Machine Learning
description: Informazioni sulla formazione distribuita e su come supporta l'archiviazione di Azure Machine Learning.Learn about distributed training and how Azure Machine Learning supports it.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385545"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Formazione distribuita con Azure Machine LearningDistributed training with Azure Machine Learning

In questo articolo vengono fornite informazioni sulla formazione distribuita e su come Azure Machine Learning lo supporta per i modelli di deep learning. 

Nel training distribuito il carico di lavoro per eseguire il training di un modello viene suddiviso e condiviso tra più miniprocessori, denominati nodi di lavoro. Questi nodi di lavoro lavorano in parallelo per velocizzare la formazione del modello. Il training distribuito può essere usato per i modelli ML tradizionali, ma è più adatto per attività di calcolo e tempo intensivo, come il [deep learning](concept-deep-learning-vs-machine-learning.md) per il training di reti neurali profonde.

## <a name="deep-learning-and-distributed-training"></a>Apprendimento approfondito e formazione distribuita 

Esistono due tipi principali di training distribuito: [parallelismo](#data-parallelism) dei dati e [parallelismo](#model-parallelism)del modello . Per la formazione distribuita sui modelli di deep learning, Azure Machine Learning SDK in Python supporta le integrazioni con framework più diffusi, PyTorch e TensorFlow.For distributed training on deep learning models, the [Azure Machine Learning SDK in Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) supports integrations with popular frameworks, PyTorch and TensorFlow. Entrambi i framework utilizzano il parallelismo dei dati per la formazione distribuita e possono sfruttare [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) per ottimizzare le velocità di calcolo. 

* [Formazione distribuita con PyTorchDistributed training with PyTorch](how-to-train-pytorch.md#distributed-training)

* [Formazione distribuita con TensorFlow](how-to-train-tensorflow.md#distributed-training)

Per i modelli di Machine Learning che non richiedono formazione distribuita, vedere Eseguire il training di [modelli con Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) per i diversi modi per eseguire il training dei modelli usando Python SDK.

## <a name="data-parallelism"></a>Parallelismo dei dati

Il parallelismo dei dati è il più semplice da implementare dei due approcci di formazione distribuiti ed è sufficiente per la maggior parte dei casi d'uso.

In questo approccio, i dati sono suddivisi in partizioni, in cui il numero di partizioni è uguale al numero totale di nodi disponibili, nel cluster di calcolo. Il modello viene copiato in ognuno di questi nodi di lavoro e ogni lavoratore opera sul proprio subset di dati. Tenere presente che ogni nodo deve avere la capacità di supportare il modello sottoposto a training, ovvero il modello deve essere interamente adattato a ogni nodo.

Ogni nodo calcola in modo indipendente gli errori tra le stime per i campioni di training e gli output con etichetta. A sua volta, ogni nodo aggiorna il proprio modello in base agli errori e deve comunicare tutte le modifiche agli altri nodi per aggiornare i modelli corrispondenti. Ciò significa che i nodi di lavoro devono sincronizzare i parametri del modello, o gradienti, alla fine del calcolo batch per garantire che il training di un modello coerente. 

## <a name="model-parallelism"></a>Parallelismo del modello

Nel parallelismo del modello, noto anche come parallelismo di rete, il modello viene segmentato in parti diverse che possono essere eseguite contemporaneamente in nodi diversi e ognuno di essi verrà eseguito sugli stessi dati. La scalabilità di questo metodo dipende dal grado di parallelizzazione delle attività dell'algoritmo ed è più complesso da implementare rispetto al parallelismo dei dati. 

Nel parallelismo del modello, i nodi di lavoro devono solo sincronizzare i parametri condivisi, in genere una volta per ogni passaggio di propagazione in avanti o indietro. Inoltre, i modelli più grandi non sono un problema poiché ogni nodo opera su una sottosezione del modello sugli stessi dati di training.

## <a name="next-steps"></a>Passaggi successivi

* Scopri come [configurare](how-to-set-up-training-targets.md) gli ambienti di formazione con Python SDK.
* Per un esempio tecnico, vedere lo [scenario dell'architettura](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)di riferimento .
* [Allena i modelli ML con TensorFlow](how-to-train-tensorflow.md).
* [Addestra i modelli ML con PyTorch.](how-to-train-pytorch.md) 