---
title: Eseguire il training di modelli con PyTorch
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire il training su nodo singolo e distribuito di modelli di PyTorch con Estimator di PyTorch
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 11819730e05e425066e1f060769e14d5290f877d
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851971"
---
# <a name="train-pytorch-models-with-azure-machine-learning-service"></a>Eseguire il training di modelli di PyTorch con il servizio Azure Machine Learning

Per il training di reti neurali profonde (DNN) mediante PyTorch, Azure Machine Learning fornisce una classe personalizzata [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) della classe di `Estimator`. Estimator di `PyTorch` di Azure SDK consente di inviare facilmente processi di training PyTorch per l'esecuzione su nodo singolo e distribuita nelle risorse di calcolo di Azure.

## <a name="single-node-training"></a>Training su nodo singolo
Il training con Estimator di `PyTorch` è simile all'uso di [ `Estimator` di base](how-to-train-ml-models.md). È quindi consigliabile leggere prima l'articolo sulle procedure e assicurarsi di avere compreso i concetti introdotti in tale articolo.
  
Per eseguire un processo PyTorch, creare un'istanza di un oggetto `PyTorch`. È necessario avere già creato l'oggetto [destinazione di calcolo](how-to-set-up-training-targets.md#amlcompute) `compute_target` e l'oggetto [datastore](how-to-access-data.md) `ds`.

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

Qui si specificano i parametri seguenti al costruttore PyTorch:

Parametro | Descrizione
--|--
`source_directory` |  Directory locale contenente tutto il codice necessario per il processo di training. Questa cartella viene copiata dal computer locale nelle risorse di calcolo remote
`script_params` |  Dizionario che specifica gli argomenti della riga di comando allo script di training `entry_script`, sotto forma di < argomento della riga di comando, valore > coppie.  Per specificare un flag dettagliato nella `script_params`, usare `<command-line argument, "">`.
`compute_target` |  Destinazione di calcolo remota su cui verrà eseguito lo script di training, in questo caso un cluster di ambiente di calcolo di Azure Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute))
`entry_script` |  Percorso file (relativo a `source_directory`) dello script di training da eseguire nelle risorse di calcolo remote. Questo file e gli eventuali file aggiuntivi da cui dipende, devono trovarsi in questa cartella
`conda_packages` |  Elenco dei pacchetti Python da installare tramite Conda, necessari per lo script di training. Il costruttore ha un altro parametro chiamato `pip_packages` che è possibile usare per gli eventuali pacchetti pip necessari
`use_gpu` |  Impostare questo flag su `True` per sfruttare la GPU per il training. L'impostazione predefinita è `False`

Poiché si usa Estimator di `PyTorch`, il contenitore usato per il training includerà il pacchetto di PyTorch e le relative dipendenze necessarie per il training su CPU e GPU.

Quindi, inviare il processo di PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Training distribuito
Estimator di `PyTorch` consente anche di eseguire il training di modelli su larga scala tra cluster di CPU e GPU di macchine virtuali di Azure. È possibile eseguire facilmente il training distribuito di PyTorch con alcune chiamate API, mentre Azure Machine Learning gestisce dietro le quinte tutta l'infrastruttura e l'orchestrazione necessarie per questi carichi di lavoro.

Azure Machine Learning attualmente supporta l'addestramento distribuito basato su MPI di PyTorch usando il framework Horovod.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) è un framework open source ring-allreduce per il training distribuito, sviluppato da Uber.

Per eseguire PyTorch distribuito usando il framework Horovod, creare l'oggetto PyTorch come indicato di seguito:

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

Questo codice espone i nuovi parametri seguenti al costruttore di PyTorch:

Parametro | Descrizione | Predefinito
--|--|--
`node_count` |  Numero di nodi da usare per il processo di training. | `1`
`process_count_per_node` |  Numero di processi (o "ruoli di lavoro") da eseguire in ogni nodo. | `1`
`distributed_backend` |  Back-end per l'avvio del training distribuito, offerto da Estimator tramite MPI.  Per eseguire il training parallelo o distribuito (ad esempio `node_count`> 1 o `process_count_per_node`> 1 o entrambi) con MPI (e Horovod), impostare `distributed_backend='mpi'`. L'implementazione di MPI usata da Azure Machine Learning è [Open MPI](https://www.open-mpi.org/). | `None`

Nell'esempio precedente verrà eseguito il training distribuito con due ruoli di lavoro, uno per ogni nodo.

Horovod e le relative dipendenze verranno installati automaticamente e pertanto sarà sufficiente importarli nello script di training `train.py` come indicato di seguito:
```Python
import torch
import horovod
```

Infine, inviare il processo di PyTorch distribuito:
```Python
run = exp.submit(pt_est)
```

## <a name="export-to-onnx"></a>Esportare in ONNX

Per ottimizzare l'inferenza con il [ONNX Runtime](concept-onnx.md), convertire il modello di PyTorch training nel formato ONNX. Inferenza o modello di punteggio, è la fase in cui viene usato il modello distribuito per la stima, in genere sui dati di produzione. Vedere le [esercitazione](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) per un esempio.

## <a name="examples"></a>Esempi

Per i notebook relativi a Deep Learning distribuito, vedere:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
