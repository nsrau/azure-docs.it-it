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
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 82c9aa961221b582bb16438f30a0584232164393
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65915110"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Eseguire il training di modelli di TensorFlow e Keras con il servizio di Azure Machine Learning

È possibile eseguire con facilità processi di training TensorFlow in calcolo di Azure usando il [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) classe Estimatore nel SDK di Azure Machine Learning. Il `TensorFlow` estimator indirizza il servizio di Azure Machine Learning per eseguire il processo in un contenitore TensorFlow abilitata per il training di Deep Neural Network (DNN).

Il `TensorFlow` Estimatore fornisce inoltre un livello di astrazione rispetto all'esecuzione, il che significa che è possibile configurare facilmente le esecuzioni con parametri in destinazioni di calcolo diverse senza modificare gli script di training.

## <a name="get-started"></a>Attività iniziali

Poiché il `TensorFlow` estimator classe è simile alla base [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), è consigliabile leggere prima la [articolo sulle procedure di base Estimator](how-to-train-ml-models.md) comprendere i concetti generali.

Per iniziare a usare servizio Azure Machine Learning [completare la Guida introduttiva](quickstart-run-cloud-notebook.md). Al termine, sarà necessario un [dell'area di lavoro di Azure Machine Learning](concept-workspace.md) e tutti i nostri [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml) inclusi quelli per reti neurali profonde training Keras con TensorFlow.

## <a name="single-node-training"></a>Training su nodo singolo

Per eseguire un processo TensorFlow, creare un'istanza di un [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) dell'oggetto e inviarlo come esperimento.

Il codice seguente crea un'istanza di un Estimatore TensorFlow e lo invia come esperimento. Script di training `train.py` verrà eseguita usando i parametri dello script specificato. Il processo verrà eseguito su un supporto per GPU [destinazione di calcolo](how-to-set-up-training-targets.md)e scikit-informazioni su verrà installato come una dipendenza per `train.py`.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Training distribuito

Il [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator supporta anche l'addestramento distribuito tra i cluster CPU e GPU. È possibile eseguire facilmente i processi TensorFlow distribuiti e servizio Azure Machine Learning gestiranno l'infrastruttura e orchestrazione per l'utente.

Servizio Azure Machine Learning supporta due metodi di addestramento distribuito in TensorFlow:

* [Basato su MPI](https://www.open-mpi.org/) distribuite training usando il [Horovod](https://github.com/uber/horovod) framework
* Nativi [TensorFlow distribuiti](https://www.tensorflow.org/deploy/distributed) usando il metodo server parametro

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) è un framework open source per l'addestramento distribuito sviluppato da Uberjar. Offre un percorso facilitato per i processi GPU TensorFlow distribuiti.

L'esempio seguente viene eseguito un processo di training distribuita usando Horovod con due ruoli di lavoro distribuiti in due nodi.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod e le relative dipendenze vengono installati per utente, pertanto è possibile importarlo nello script di training.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Server dei parametri

È anche possibile eseguire [TensorFlow distribuito nativo](https://www.tensorflow.org/deploy/distributed), che usa il modello del server dei parametri. In questo modo si esegue il training in un cluster di server dei parametri e ruoli di lavoro. I ruoli di lavoro calcolano i gradienti durante il training, mentre i server dei parametri aggregano i gradienti.

L'esempio seguente viene eseguito un processo di training distribuita usando il metodo server del parametro con quattro ruoli di lavoro distribuiti in due nodi.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Nota su `TF_CONFIG`

È necessario anche gli indirizzi di rete e porte del cluster per il [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), in modo che Azure Machine Learning imposta il `TF_CONFIG` variabile di ambiente per l'utente.

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

Per il livello elevato di TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow analizzerà ciò `TF_CONFIG` variabile e il cluster di compilazione specifiche per l'utente.

Per core di livello inferiore di TensorFlow API per il training, analizzare il `TF_CONFIG` variabile e compilazione di `tf.train.ClusterSpec` del codice di training. In [questo esempio](https://aka.ms/aml-notebook-tf-ps) si eseguirebbe questa operazione nel **proprio script di training** come segue:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Supporto per Keras

[Keras](https://keras.io/) è un'API Python DNN in più diffusi, ad alto livello che supporta CNTK, TensorFlow e Theano come back-end. Se si usa TensorFlow come back-end, l'aggiunta di Keras è semplice quanto incluso un `pip_package` parametro del costruttore.

L'esempio seguente crea una [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator e lo invia come esperimento. Lo strumento di stima esegue lo script di training Keras `keras_train.py`. Il processo verrà eseguito su un supporto per gpu [destinazione di calcolo](how-to-set-up-training-targets.md) con Keras installati come dipendenza tramite pip.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>Esportare in ONNX

Per ottenere inferenza ottimizzato con la [ONNX Runtime](concept-onnx.md), è possibile convertire il modello di training TensorFlow nel formato ONNX. Vedere l'[esempio](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Esempi

È possibile trovare esempi di codice funzionante per le esecuzioni a nodo singolo e distribuite TensorFlow usando vari Framework sul [accedere alla pagina GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>Passaggi successivi

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
