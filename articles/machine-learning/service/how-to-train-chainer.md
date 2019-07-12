---
title: Eseguire il training e registrare i modelli di Chainer
titleSuffix: Azure Machine Learning service
description: Questo articolo illustra come eseguire il training e registrare un modello di Chainer utilizzando il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 8ecefccbdf5f02652e935858b6ae8fb4cdfde640
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840033"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Eseguire il training e registrare i modelli di Chainer su larga scala con servizi di Azure Machine Learning

Questo articolo illustra come eseguire il training e registrare un modello di Chainer utilizzando il servizio di Azure Machine Learning. Usa il diffuso [set di dati MNIST](http://yann.lecun.com/exdb/mnist/) classificare cifre scritte a mano usando una rete neurale profonde (DNN) compilata usando la [libreria Python di Chainer](https://Chainer.org) in esecuzione in cima [numpy](https://www.numpy.org/).

Chainer viene una reti neurali di alto livello API in grado di eseguire in primo piano di altri framework DNN più diffusi per semplificare lo sviluppo. Con il servizio di Azure Machine Learning, è possibile aumentare rapidamente i processi di training usando risorse di calcolo cloud elastico. È inoltre possibile monitorare le esecuzioni di training, i modelli di versione, la distribuzione di modelli e molto altro ancora.

Se si sta sviluppando un modello di Chainer da zero o si desidera introdurre un modello esistente nel cloud, servizio Azure Machine Learning consentono di compilare modelli pronti per la produzione.

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno dei due ambienti:

- Azure Machine Learning Notebook VM - alcun download o installazione necessaria

    - Completare la [Guida introduttiva di notebook basato su cloud](quickstart-run-cloud-notebook.md) per creare un server notebook dedicato precaricato con il SDK e il repository di esempio.
    - Nella cartella samples nel server notebook, individuare un notebook completato e i file nei **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** cartella.  Il notebook include sezioni espanse che coprono l'ottimizzazione degli iperparametri intelligenti, la distribuzione del modello e i widget di notebook.

- Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK per Python](setup-create-workspace.md#sdk)
    - [Creare un file di configurazione dell'area di lavoro](setup-create-workspace.md#write-a-configuration-file)
    - Scaricare il file di script di esempio [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - È anche possibile trovare un completate [versione di Notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) di questa Guida nella pagina di esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione degli iperparametri intelligenti, la distribuzione del modello e i widget di notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

In questa sezione si configura l'esperimento di training per il caricamento di pacchetti python necessari, l'inizializzazione di un'area di lavoro, creazione di un esperimento e caricare i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

Prima di tutto importare azureml.core visualizzazione ad della libreria di Python il numero di versione.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

Il [dell'area di lavoro di Azure Machine Learning servizio](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK, è possibile accedere gli elementi dell'area di lavoro mediante la creazione di un [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) oggetto.

Crea un oggetto dell'area di lavoro dal `config.json` creato nel file il [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Creare una directory del progetto
Creare una directory che conterrà tutto il codice necessario dal computer locale che è necessario accedere alla risorsa remota. Sono inclusi script di training e di eventuali file aggiuntivi, che lo script di training dipende.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Preparare lo script di training

In questa esercitazione, lo script di training **chainer_mnist.py** viene già fornito automaticamente. In pratica, deve essere in grado di sfruttare tutti gli script di formazione personalizzati ed eseguirlo con Azure Machine Learning senza dover modificare il codice.

Per usare il rilevamento e le funzionalità di metriche di Azure Machine Learning, è necessario aggiungere una piccola quantità di codice di Azure Machine Learning all'interno dello script di training.  Script di training **chainer_mnist.py** Mostra come registrare alcune metriche per l'esecuzione di Azure ML. A tale scopo, è accedere a Azure ML `Run` oggetto all'interno dello script.

Copiare lo script di training **chainer_mnist.py** nella directory del progetto.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento e una cartella per contenere gli script di training. In questo esempio, creare un esperimento denominato "chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Creare o ottenere una destinazione di calcolo

È necessario un [destinazione di calcolo](concept-compute-target.md) per il training del modello. In questa esercitazione si userà il calcolo di Azure Machine Learning gestiti (AmlCompute) per la risorsa di calcolo remoto corsi di formazione.

**La creazione di AmlCompute richiede circa 5 minuti**. Se il AmlCompute con lo stesso nome è già nell'area di lavoro, questo codice verrà eseguito il processo di creazione.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Per altre informazioni sulle destinazioni di calcolo, vedere la [What ' s una destinazione di calcolo](concept-compute-target.md) articolo.

## <a name="create-a-chainer-estimator"></a>Creare un Estimatore Chainer

Il [estimator Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) fornisce un modo semplice per l'avvio di processi di training Chainer nella destinazione di calcolo.

Lo strumento di stima Chainer viene implementata tramite il tipo generico [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) (classe), che può essere usato per supportare qualsiasi framework. Per altre informazioni sui training dei modelli utilizzando lo strumento di stima generico, vedere [il training dei modelli con Azure Machine Learning con strumento di stima](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Invia un'esecuzione

Il [oggetto esecuzione](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia per la cronologia di esecuzione durante l'esecuzione del processo e dopo il completamento.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

L'esecuzione viene eseguita, la chiamata passa attraverso le fasi seguenti:

- **Preparazione**: Viene creata un'immagine docker in base alla stima di Chainer. L'immagine viene caricata in Registro contenitori dell'area di lavoro e memorizzato nella cache per le esecuzioni successive. I log vengono anche trasmessi per la cronologia di esecuzione e possono essere consultati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: Il cluster tenta di aumentare le prestazioni se il cluster Batch per intelligenza artificiale richiede più nodi per l'esecuzione dell'esecuzione rispetto a quelli attualmente disponibili.

- **In esecuzione**: Tutti gli script nella cartella dello script vengono caricati nella destinazione di calcolo, archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e. / cartella dei log vengono inviati nel flusso per la cronologia di esecuzione e può essere usato per monitorare l'esecuzione.

- **Post-elaborazione**: I. / output dell'esecuzione della cartella da copiare la cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training modello, è possibile salvare e registrarlo per l'area di lavoro. Registrazione del modello consente di store e la versione dei modelli nell'area di lavoro per semplificare [modello di distribuzione e gestione](concept-model-management-and-deployment.md).

Aggiungere il codice seguente allo script, corsi di formazione **chainer_mnist.py**, salvare il modello. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

Registrare il modello nell'area di lavoro con il codice seguente.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è eseguito il training di un modello di Chainer nel servizio di Azure Machine Learning. 

* Per informazioni su come distribuire un modello, passare al nostro [distribuzione del modello](how-to-deploy-and-where.md) articolo.

* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
