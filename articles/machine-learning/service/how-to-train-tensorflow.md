---
title: Eseguire il training di modelli con TensorFlow
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire il training su nodo singolo e distribuito di modelli di TensorFlow con Estimator di TensorFlow
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d15d3ed115009ad1395a85d36e833d85197d4d19
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094109"
---
# <a name="train-tensorflow-models-with-azure-machine-learning-service"></a>Eseguire il training di modelli TensorFlow con il servizio Azure Machine Learning

Per il training con reti neurali avanzate (DNN) tramite TensorFlow, Azure Machine Learning fornisce una classe `TensorFlow` personalizzata di `Estimator`. Estimator di `TensorFlow` di Azure SDK (da non confondere con la classe [`tf.estimator.Estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator)) consente di inviare facilmente processi di training TensorFlow per l'esecuzione su nodo singolo e distribuita nelle risorse di calcolo di Azure.

## <a name="single-node-training"></a>Training su nodo singolo
Il training con Estimator di `TensorFlow` è simile all'uso di [ `Estimator` di base](how-to-train-ml-models.md). È quindi consigliabile leggere prima l'articolo sulle procedure e assicurarsi di avere compreso i concetti introdotti in tale articolo.
  
Per eseguire un processo TensorFlow, creare un'istanza di un oggetto `TensorFlow`. È necessario avere già creato l'oggetto [destinazione di calcolo](how-to-set-up-training-targets.md#amlcompute) `compute_target`.

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'],
                    use_gpu=True)
```

Qui si specificano i parametri seguenti al costruttore TensorFlow:

Parametro | DESCRIZIONE
--|--
`source_directory` | Directory locale contenente tutto il codice necessario per il processo di training. Questa cartella viene copiata dal computer locale nelle risorse di calcolo remote
`script_params` | Dizionario che specifica gli argomenti della riga di comando per lo script di training `entry_script`, sotto forma di coppie <argomento riga di comando, valore>
`compute_target` | Destinazione di calcolo remota su cui verrà eseguito lo script di training, in questo caso un cluster di ambiente di calcolo di Azure Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute))
`entry_script` | Percorso file (relativo a `source_directory`) dello script di training da eseguire nelle risorse di calcolo remote. Questo file e gli eventuali file aggiuntivi da cui dipende, devono trovarsi in questa cartella
`conda_packages` | Elenco dei pacchetti Python da installare tramite Conda, necessari per lo script di training. In questo caso lo script di training usa `sklearn` per il caricamento dei dati, quindi specificare questo pacchetto da installare.  Il costruttore ha un altro parametro chiamato `pip_packages` che è possibile usare per gli eventuali pacchetti pip necessari
`use_gpu` | Impostare questo flag su `True` per sfruttare la GPU per il training. L'impostazione predefinita è `False`.

Poiché si usa Estimator di TensorFlow, il contenitore usato per il training includerà per impostazione predefinita il pacchetto di TensorFlow e le relative dipendenze necessarie per il training su CPU e GPU.

Inviare quindi il processo TensorFlow:
```Python
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Training distribuito
Estimator di TensorFlow consente anche di eseguire il training di modelli su larga scala tra cluster di CPU e GPU di macchine virtuali di Azure. È possibile eseguire facilmente il training distribuito di TensorFlow con alcune chiamate API, mentre Azure Machine Learning gestisce dietro le quinte tutta l'infrastruttura e l'orchestrazione necessarie per questi carichi di lavoro.

Azure Machine Learning supporta due modalità di training distribuito in TensorFlow:
* Training distribuito basato su MPI con il framework [Horovod](https://github.com/uber/horovod)
* [TensorFlow distribuito](https://www.tensorflow.org/deploy/distributed) nativo tramite il server dei parametri

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) è un framework open source ring-allreduce per il training distribuito, sviluppato da Uber.

Per eseguire TensorFlow distribuito usando il framework Horovod, creare l'oggetto TensorFlow come indicato di seguito:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

Il codice precedente espone i nuovi parametri seguenti al costruttore di TensorFlow:

Parametro | DESCRIZIONE | Predefinito
--|--|--
`node_count` | Numero di nodi da usare per il processo di training. | `1`
`process_count_per_node` | Numero di processi (o "ruoli di lavoro") da eseguire in ogni nodo.|`1`
`distributed_backend` | Back-end per l'avvio del training distribuito, offerto da Estimator tramite MPI. Se si desidera eseguire il training parallelo o distribuito (ad esempio `node_count`> 1 o `process_count_per_node`> 1 o entrambi) con MPI (e Horovod), impostare `distributed_backend='mpi'`. L'implementazione di MPI usata da Azure Machine Learning è [Open MPI](https://www.open-mpi.org/). | `None`

Nell'esempio precedente verrà eseguito il training distribuito con due ruoli di lavoro, uno per ogni nodo.

Horovod e le relative dipendenze verranno installati automaticamente e pertanto sarà sufficiente importarli nello script di training `train.py` come indicato di seguito:

```Python
import tensorflow as tf
import horovod
```

Inviare infine il processo TensorFlow:
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Server dei parametri
È anche possibile eseguire [TensorFlow distribuito nativo](https://www.tensorflow.org/deploy/distributed), che usa il modello del server dei parametri. In questo modo si esegue il training in un cluster di server dei parametri e ruoli di lavoro. I ruoli di lavoro calcolano i gradienti durante il training, mentre i server dei parametri aggregano i gradienti.

Costruire l'oggetto TensorFlow:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

Prestare attenzione ai parametri seguenti per il costruttore di TensorFlow nel codice precedente:

Parametro | DESCRIZIONE | Predefinito
--|--|--
`worker_count` | Numero di ruoli di lavoro. | `1`
`parameter_server_count` | Numero di server dei parametri. | `1`
`distributed_backend` | Back-end da usare per il training distribuito. Per eseguire il training distribuito tramite server dei parametri, impostare `distributed_backend='ps'` | `None`

#### <a name="note-on-tfconfig"></a>Nota su `TF_CONFIG`
È necessario avere anche gli indirizzi di rete e le porte del cluster per [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), per consentire ad Azure Machine Learning di impostare la variabile di ambiente `TF_CONFIG`.

La variabile di ambiente `TF_CONFIG` è una stringa JSON. Di seguito è riportato un esempio della variabile per un server dei parametri:
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Se si usa l'API di alto livello [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) di TensorFlow, TensorFlow analizzerà questa variabile `TF_CONFIG` e creerà le specifiche del cluster automaticamente. 

Se si usano invece le API core di livello più basso di TensorFlow per il training, è necessario analizzare la variabile `TF_CONFIG` e creare `tf.train.ClusterSpec` manualmente nel codice di training. In [questo esempio](https://aka.ms/aml-notebook-tf-ps) si eseguirebbe questa operazione nel **proprio script di training** come segue:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

Dopo aver terminato la scrittura dello script di training e la creazione dell'oggetto di TensorFlow, è possibile inviare il processo di training:
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Esempi

Per i notebook relativi a Deep Learning distribuito, vedere:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
