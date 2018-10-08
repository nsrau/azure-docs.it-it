---
title: Eseguire il training di modelli di PyTorch con Azure Machine Learning
description: Informazioni su come eseguire il training su nodo singolo e distribuito di modelli di PyTorch con Estimator di PyTorch
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e569b63f676fb750bcbab88dda6cda39156d41f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977033"
---
# <a name="how-to-train-pytorch-models"></a>Come eseguire il training di modelli di PyTorch

Per il training con reti neurali avanzate (DNN) mediante PyTorch, Azure Machine Learning fornisce una classe Estimator personalizzata per PyTorch. Estimator di PyTorch di Azure SDK consente di inviare facilmente processi di training PyTorch per l'esecuzione su nodo singolo e distribuita nelle risorse di calcolo di Azure.

## <a name="single-node-training"></a>Training su nodo singolo
Il training con Estimator di PyTorch è simile all'uso di [Estimator di base](how-to-train-ml-models.md), quindi si consiglia di leggere prima l'articolo sulle procedure e assicurarsi di avere compreso i concetti introdotti.
  
Per eseguire un processo PyTorch, creare un'istanza di un oggetto `PyTorch`. È necessario avere già creato l'oggetto [destinazione di calcolo](how-to-set-up-training-targets.md#batch) `compute_target` e l'oggetto [datastore](how-to-access-data.md) `ds`.

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
* `source_directory`: la directory locale che contiene tutto il codice necessario per il processo di training. Questa cartella viene copiata dal computer locale nelle risorse di calcolo remote
* `script_params`: un dizionario che specifica gli argomenti della riga di comando allo script di training `entry_script`, sotto forma di coppie <argomento riga di comando, valore>
* `compute_target`: le risorse di calcolo remote in cui verrà eseguito lo script di training, in questo caso un cluster [Batch per intelligenza artificiale](how-to-set-up-training-targets.md#batch)
* `entry_script`: il percorso file (relativo a `source_directory`) dello script di training da eseguire sulle risorse di calcolo remote. Questo file e gli eventuali file aggiuntivi da cui dipende, devono trovarsi in questa cartella
* `conda_packages`: l'elenco dei pacchetti Python da installare tramite Conda, necessari per lo script di training.
Il costruttore ha un altro parametro chiamato `pip_packages` che è possibile usare per gli eventuali pacchetti pip necessari
* `use_gpu`: impostare questo flag su `True` per sfruttare la GPU per il training. L'impostazione predefinita è `False`

Poiché si usa Estimator di PyTorch, il contenitore usato per il training includerà per impostazione predefinita il pacchetto di PyTorch e le relative dipendenze necessarie per il training su CPU e GPU.

Quindi, inviare il processo di PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Training distribuito
Estimator di PyTorch consente anche di eseguire il training di modelli su larga scala tra cluster di CPU e GPU di macchine virtuali di Azure. È possibile eseguire facilmente il training distribuito di PyTorch con alcune chiamate API, mentre Azure Machine Learning gestisce dietro le quinte tutta l'infrastruttura e l'orchestrazione necessarie per questi carichi di lavoro.

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

Il codice precedente espone i nuovi parametri seguenti al costruttore di PyTorch:
* `node_count`: il numero di nodi da usare per il processo di training. Il valore predefinito di questo argomento è `1`
* `process_count_per_node`: il numero di processi (o "ruoli di lavoro") da eseguire in ogni nodo. Il valore predefinito di questo argomento è `1`
* `distributed_backend`: il back-end per l'avvio del training distribuito, offerto da Estimator tramite MPI. Il valore predefinito di questo argomento è `None`. Se si desidera eseguire il training parallelo o distribuito (ad esempio `node_count`> 1 o `process_count_per_node`> 1 o entrambi) con MPI (e Horovod), impostare `distributed_backend='mpi'`. L'implementazione di MPI usata da Azure Machine Learning è [Open MPI](https://www.open-mpi.org/).

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

## <a name="examples"></a>Esempi
Per un'esercitazione sul training di PyTorch su nodo singolo, vedere:
* `training/01.train-tune-deploy-pytorch/01.train-tune-deploy-pytorch.ipynb`

Per un'esercitazione su PyTorch distribuito con Horovod, vedere:
* `training/02.distributed-pytorch-with-horovod/02.distributed-pytorch-with-horovod.ipynb`

Ottenere questi notebook:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
