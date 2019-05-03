---
title: Il training dei modelli Keras con TensorFlow
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire a nodo singolo e distribuito training dei modelli di TensorFlow e Keras con il businesss TensorFlow e Keras
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: cedd45d4142633e48d0d9dd41870f57c16d860c8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023832"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Eseguire il training di modelli di TensorFlow e Keras con il servizio di Azure Machine Learning

Per il training con reti neurali avanzate (DNN) tramite TensorFlow, Azure Machine Learning fornisce una classe `TensorFlow` personalizzata di `Estimator`. il SDK di Azure [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator (non per essere conflated con la [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) classe) consente di inviare con facilità processi di training TensorFlow per le esecuzioni a nodo singolo e distribuiti in Azure risorse di calcolo,

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
                    conda_packages=['scikit-learn'], # in case you need scikit-learn in train.py
                    use_gpu=True)
```

Qui si specificano i parametri seguenti al costruttore TensorFlow:

Parametro | DESCRIZIONE
--|--
`source_directory` | Directory locale contenente tutto il codice necessario per il processo di training. Questa cartella viene copiata dal computer locale nelle risorse di calcolo remote
`script_params` | Dizionario che specifica gli argomenti della riga di comando allo script di training `entry_script`, sotto forma di < argomento della riga di comando, valore > coppie.  Per specificare un flag dettagliato nella `script_params`, usare `<command-line argument, "">`.
`compute_target` | Destinazione di calcolo remota su cui verrà eseguito lo script di training, in questo caso un cluster di ambiente di calcolo di Azure Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute))
`entry_script` | Percorso file (relativo a `source_directory`) dello script di training da eseguire nelle risorse di calcolo remote. Questo file e gli eventuali file aggiuntivi da cui dipende, devono trovarsi in questa cartella
`conda_packages` | Elenco dei pacchetti Python da installare tramite Conda, necessari per lo script di training. In questo caso lo script di training usa `sklearn` per il caricamento dei dati, quindi specificare questo pacchetto da installare.  Il costruttore ha un altro parametro chiamato `pip_packages` che è possibile usare per gli eventuali pacchetti pip necessari
`use_gpu` | Impostare questo flag su `True` per sfruttare la GPU per il training. L'impostazione predefinita è `False`.

Poiché si usa Estimator di TensorFlow, il contenitore usato per il training includerà per impostazione predefinita il pacchetto di TensorFlow e le relative dipendenze necessarie per il training su CPU e GPU.

Inviare quindi il processo TensorFlow:
```Python
run = exp.submit(tf_est)
```

## <a name="keras-support"></a>Supporto per Keras
[Keras](https://keras.io/) è un'API comune ad alto livello Python di rete neurale profonda che supporta CNTK, TensorFlow o Theano come back-end. Se si usa TensorFlow come back-end, è possibile utilizzare lo strumento di stima TensFlow facilmente per addestrare un modello di Keras. Di seguito è riportato un esempio di un Estimatore TensorFlow con Keras aggiunti:

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py',
                       pip_packages=['keras'], # just add keras through pip
                       use_gpu=True)
```
Questo costruttore estimator TensorFlow indica al servizio di Azure Machine Learning per installare Keras tramite pip in modo che l'ambiente di esecuzione. E il `keras_train.py` quindi possibile importare l'API Keras per addestrare un modello di Keras. Per un esempio completo, esplorare [questo notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb).

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
`distributed_backend` | Back-end per l'avvio del training distribuito, offerto da Estimator tramite MPI. Se si desidera eseguire il training parallelo o distribuita (ad esempio, `node_count`> 1 o `process_count_per_node`> 1 o entrambi) con MPI e Horovod, impostare `distributed_backend='mpi'`. L'implementazione di MPI usata da Azure Machine Learning è [Open MPI](https://www.open-mpi.org/). | `None`

Nell'esempio precedente verrà eseguito il training distribuito con due ruoli di lavoro, uno per ogni nodo.

Horovod e le relative dipendenze verranno installate automaticamente, pertanto è possibile importarlo nello script di training `train.py` come indicato di seguito:

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

Se si usa un livello elevato di TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow analizzerà ciò `TF_CONFIG` variabile e il cluster di compilazione specifiche per l'utente. 

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

## <a name="export-to-onnx"></a>Esportare in ONNX

Per ottenere inferenza ottimizzato con la [ONNX Runtime](concept-onnx.md), è possibile convertire il modello di training TensorFlow nel formato ONNX. Vedere l'[esempio](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Esempi

Esaminare varie [notebook su apprendimento avanzato distribuito su Github](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
