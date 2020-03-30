---
title: Allenati modelli Keras di deep learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training e registrare un modello di classificazione della rete neurale completa di Keras in esecuzione su TensorFlow usando Azure Machine Learning.Learn how to train and register a Keras deep neural network classification model running on TensorFlow using Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269965"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Addestrare e registrare un modello di classificazione Keras con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire il training e registrare un modello di classificazione Keras basato su TensorFlow usando Azure Machine Learning.This article shows you how to train and register a Keras classification model built on TensorFlow using Azure Machine Learning. Utilizza il popolare set di [dati MNIST](http://yann.lecun.com/exdb/mnist/) per classificare le cifre scritte a mano utilizzando una rete neurale profonda (DNN) creata utilizzando la [libreria Keras Python](https://keras.io) in esecuzione sulla parte superiore di [TensorFlow](https://www.tensorflow.org/overview).

Keras è un'API di rete neurale di alto livello in grado di eseguire in cima ad altri framework DNN popolari per semplificare lo sviluppo. Con Azure Machine Learning è possibile scalare rapidamente i processi di formazione usando le risorse di elaborazione cloud elastiche. È inoltre possibile tenere traccia delle esecuzioni di allenamento, dei modelli di versione, distribuire modelli e molto altro ancora.

Sia che tu stia sviluppando un modello Keras da zero o che tu stia portando un modello esistente nel cloud, Azure Machine Learning ti aiuterà a creare modelli pronti per la produzione.

Vedere [l'articolo concettuale](concept-deep-learning-vs-machine-learning.md) per informazioni sulle differenze tra machine learning e deep learning.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno di questi ambienti:Run this code on either of these environments:

- Istanza di calcolo di Azure Machine Learning: non sono necessari download o installazioni

     - Completare [l'esercitazione: Ambiente](tutorial-1st-experiment-sdk-setup.md) di installazione e area di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella degli esempi nel server notebook, trovare un blocco appunti completato ed espanso passando a questa directory: **how-to-use-azureml > training-with-deep-learning > cartella train-hyperparameter-tune-deploy-with-keras.**

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - Creare un file di [configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` di script di esempio e`utils.py`

    È inoltre possibile trovare una [versione completata Di Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) di questa guida nella pagina degli esempi di GitHub.You can also find a completed Jupyter Notebook version of this guide on the GitHub samples page. Il blocco appunti include sezioni espanse che includono l'ottimizzazione intelligente degli iperparametri, la distribuzione dei modelli e i widget per i blocchi appunti.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione configura l'esperimento di training caricando i pacchetti pitone necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

In primo luogo, importare le librerie Python necessarie.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

[L'area di lavoro](concept-workspace.md) di Azure Machine Learning è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK è possibile accedere agli [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) elementi dell'area di lavoro creando un oggetto .

Creare un oggetto `config.json` dell'area di lavoro dal file creato nella [sezione Prerequisiti.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento denominato "keras-mnist" nell'area di lavoro.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Creare un set di dati di fileCreate a file dataset

L'oggetto `FileDataset` fa riferimento a uno o più file nell'archivio dati dell'area di lavoro o negli URL pubblici. I file possono essere di qualsiasi formato e la classe offre la possibilità di scaricarli o montarli nel contesto di calcolo. Creando un oggetto `FileDataset`, si crea un riferimento alla posizione dell'origine dati. Anche le eventuali trasformazioni applicate al set di dati verranno archiviate nel set di dati. I dati rimangono nell'attuale posizione, quindi non si incorre in costi aggiuntivi di archiviazione. Per altre informazioni, vedere la guida alle [procedure](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) del pacchetto `Dataset`.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Utilizzare `register()` il metodo per registrare il set di dati nell'area di lavoro in modo che possano essere condivisi con altri utenti, riutilizzati in vari esperimenti e a cui viene fatto riferimento per nome nello script di training.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per il processo TensorFlow su cui eseguire il processo. In questo esempio creare un cluster di calcolo di Azure Machine Learning abilitato per GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un obiettivo](concept-compute-target.md) di calcolo.

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Creare uno stimatore TensorFlow e importare Keras

Lo [stimatore TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornisce un modo semplice per avviare i lavori di training TensorFlow sulla destinazione di calcolo. Poiché Keras viene eseguito sopra TensorFlow, è possibile utilizzare lo stimatore `pip_packages` TensorFlow e importare la libreria Keras utilizzando l'argomento .

Ottenere innanzitutto i dati dall'archivio dati dell'area di lavoro usando la `Dataset` classe .

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

Lo stimatore TensorFlow viene implementato [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) tramite la classe generica, che può essere utilizzata per supportare qualsiasi framework. Inoltre, creare un `script_params` dizionario che contiene le impostazioni dell'iperparametro DNN. Per altre informazioni sui modelli di training usando lo stimatore generico, vedere Eseguire il training dei [modelli con Azure Machine Learning usando estimator](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Inviare una corsa

[L'oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Durante l'esecuzione dell'esecuzione, l'esecuzione passa attraverso le seguenti fasi:

- **Preparazione**: Viene creata un'immagine docker in base allo stimatore TensorFlow. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per esecuzioni successive. I log vengono trasmessi anche alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Scalabilità:** il cluster tenta di aumentare le scalabilità se il cluster Batch AI richiede più nodi per eseguire l'esecuzione di quelli attualmente disponibili.

- **In esecuzione:** tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e della cartella ./logs vengono trasmessi alla cronologia di esecuzione e possono essere utilizzati per monitorare l'esecuzione.

- **Post-elaborazione:** la cartella ./outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-the-model"></a>Registrare il modello

Dopo aver eseguito il training del modello DNN, è possibile registrarlo nell'area di lavoro. La registrazione dei modelli consente di archiviare e versione i modelli nell'area di lavoro per semplificare [la gestione e](concept-model-management-and-deployment.md)la distribuzione dei modelli.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Il modello appena registrato viene distribuito esattamente come qualsiasi altro modello registrato in Azure Machine Learning, indipendentemente dallo stimatore usato per il training. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla creazione di una destinazione di [calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

È inoltre possibile scaricare una copia locale del modello. Ciò può essere utile per eseguire ulteriori operazioni di convalida del modello in locale. Nello script di `mnist-keras.py`training, , un oggetto salvafattore TensorFlow mantiene il modello in una cartella locale (locale alla destinazione di calcolo). È possibile utilizzare l'oggetto Run per scaricare una copia dall'archivio dati.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Passaggi successivi

In this article, you trained and registered a Keras model on Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

> [!div class="nextstepaction"]
> [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per la formazione sul deep learning distribuito in AzureReference architecture for distributed deep learning training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
