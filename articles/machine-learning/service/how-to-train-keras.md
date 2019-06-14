---
title: Eseguire il training e di registrare modelli Keras in esecuzione in TensorFlow
titleSuffix: Azure Machine Learning service
description: Questo articolo illustra come preparare e registrare un modello di Keras in esecuzione su TensorFlow usando il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: bd2552cdfde19995413f4665f04c41c295304d50
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082599"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>Eseguire il training e di registrare modelli Keras su larga scala con il servizio di Azure Machine Learning

Questo articolo illustra come preparare e registrare un modello di Keras basato su TensorFlow usando il servizio di Azure Machine Learning. Usa il diffuso [set di dati MNIST](http://yann.lecun.com/exdb/mnist/) classificare cifre scritte a mano usando una rete neurale profonde (DNN) compilata usando la [libreria Keras Python](https://keras.io) in esecuzione in cima [TensorFlow](https://www.tensorflow.org/overview) .

Keras è un'API in grado di eseguire parte superiore di altri framework DNN più diffusi per semplificare lo sviluppo di una rete neurale ad alto livello. Con il servizio di Azure Machine Learning, è possibile aumentare rapidamente i processi di training usando risorse di calcolo cloud elastico. È inoltre possibile monitorare le esecuzioni di training, i modelli di versione, la distribuzione di modelli e molto altro ancora.

Se si sta sviluppando un modello di Keras da zero o si desidera introdurre un modello esistente nel cloud, servizio Azure Machine Learning consentono di compilare modelli pronti per la produzione.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).
- [Installare Azure Machine Learning SDK per Python](setup-create-workspace.md#sdk)
- [Creare un file di configurazione dell'area di lavoro](setup-create-workspace.md#write-a-configuration-file)
- [Scaricare i file di script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` e `utils.py`

È anche possibile trovare un completate [versione di Notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) di questa Guida nella pagina di esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione degli iperparametri intelligenti, la distribuzione del modello e i widget di notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

In questa sezione si configura l'esperimento di training per il caricamento di pacchetti python necessari, l'inizializzazione di un'area di lavoro, creazione di un esperimento e caricare i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

In primo luogo, importare le librerie di Python necessari.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

Il [dell'area di lavoro di Azure Machine Learning servizio](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK, è possibile accedere gli elementi dell'area di lavoro mediante la creazione di un [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) oggetto.

Crea un oggetto dell'area di lavoro dal `config.json` creato nel file il [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento e una cartella per contenere gli script di training. In questo esempio, creare un esperimento denominato "keras-mnist".

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Caricare set di dati e gli script

Il [datastore](how-to-access-data.md) è una posizione in cui i dati possono essere archiviati e a cui accede il montaggio o copiare i dati nella destinazione di calcolo. Ogni area di lavoro fornisce un archivio dati predefinito. Caricare i dati e script di training per l'archivio dati in modo che sono facilmente accessibili durante il training.

1. Scaricare localmente il set di dati MNIST.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Caricare il set di dati MNIST nell'archivio dati predefinito.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Caricare lo script di training, Keras `keras_mnist.py`e il file di helper, `utils.py`.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="get-the-default-compute-target"></a>Ottenere la destinazione di calcolo predefinito

Ogni area di lavoro viene fornito con due simboli, impostazione predefinita le destinazioni di calcolo: una destinazione di calcolo basate su gpu e una destinazione di calcolo basate sulla cpu. Le destinazioni di calcolo predefinite hanno impostata su 0, ovvero che non vengono allocate finché non si utilizza la scalabilità automatica. Vinci in questo esempio, usare la destinazione di calcolo GPU predefinita.

```Python
compute_target = ws.get_default_compute_target(type="GPU")
```

Per altre informazioni sulle destinazioni di calcolo, vedere la [What ' s una destinazione di calcolo](concept-compute-target.md) articolo.

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Creare un Estimatore TensorFlow e importare Keras

Il [estimator TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornisce un modo semplice per l'avvio di processi di training TensorFlow nella destinazione di calcolo. Poiché Keras viene eseguita su TensorFlow, è possibile usare lo strumento di stima TensorFlow e importare la libreria Keras usando il `pip_packages` argomento.

Lo strumento di stima TensorFlow viene implementata tramite il tipo generico [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) (classe), che può essere usato per supportare qualsiasi framework. Per altre informazioni sui training dei modelli utilizzando lo strumento di stima generico, vedere [il training dei modelli con Azure Machine Learning con strumento di stima](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
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

## <a name="submit-a-run"></a>Invia un'esecuzione

Il [oggetto esecuzione](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia per la cronologia di esecuzione durante l'esecuzione del processo e dopo il completamento.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

L'esecuzione viene eseguita, la chiamata passa attraverso le fasi seguenti:

- **Preparazione**: In base alla stima di TensorFlow viene creata un'immagine docker. L'immagine viene caricata in Registro contenitori dell'area di lavoro e memorizzato nella cache per le esecuzioni successive. I log vengono anche trasmessi per la cronologia di esecuzione e possono essere consultati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: Il cluster tenta di aumentare le prestazioni se il cluster Batch per intelligenza artificiale richiede più nodi per l'esecuzione dell'esecuzione rispetto a quelli attualmente disponibili.

- **In esecuzione**: Tutti gli script nella cartella dello script vengono caricati nella destinazione di calcolo, archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e. / cartella dei log vengono inviati nel flusso per la cronologia di esecuzione e può essere usato per monitorare l'esecuzione.

- **Post-elaborazione**: I. / output dell'esecuzione della cartella da copiare la cronologia di esecuzione.

## <a name="register-the-model"></a>Registrare il modello

Dopo aver eseguito il training modello, è possibile registrarlo per l'area di lavoro. Registrazione del modello consente di store e la versione dei modelli nell'area di lavoro per semplificare [modello di distribuzione e gestione](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

È anche possibile scaricare una copia locale del modello. Ciò può essere utile per eseguire il lavoro di convalida aggiuntive del modello in locale. Nello script di training, `mnist-keras.py`, un oggetto di screen saver TensorFlow viene mantenuto il modello in una cartella locale (computer locale per la destinazione di calcolo). È possibile usare l'oggetto di esecuzione per scaricare una copia dall'archivio dati.

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

In questo articolo, viene sottoposto a training e registrato un modello di Keras nel servizio di Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione modello.

> [!div class="nextstepaction"]
> [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
