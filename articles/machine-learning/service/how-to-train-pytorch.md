---
title: Training di modelli PyTorch per Deep Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di training di PyTorch su scala aziendale usando la classe PyTorch Estimator di Azure Machine Learning.  Gli script di esempio classificano le immagini Chicken e Turkey per creare una rete neurale Deep Learning basata sull'esercitazione per il trasferimento di PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: a18949799b948b962a5b24ffdc7c735fc1380808
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931083"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Esegui il training di modelli di Deep Learning Pytorch su larga scala con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire gli script di training di [PyTorch](https://pytorch.org/) su scala aziendale usando la classe [PyTorch estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) di Azure Machine Learning.  

Gli script di esempio in questo articolo vengono usati per classificare le immagini di pollo e tacchino per creare una rete neurale di apprendimento avanzato basata sull' [esercitazione](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)per il trasferimento dell'apprendimento di PyTorch. 

Sia che si stia eseguendo il training di un modello PyTorch di apprendimento avanzato da zero o si stia portando un modello esistente nel cloud, è possibile usare Azure Machine Learning per scalare i processi di training open source con risorse di calcolo elastiche del cloud. Con Azure Machine Learning è possibile compilare, distribuire, eseguire la versione e monitorare i modelli a livello di produzione. 

Scopri di più sull'apprendimento avanzato [rispetto a Machine Learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:

 - Azure Machine Learning macchina virtuale Notebook-nessun download o installazione necessaria

    - Completare l' [esercitazione: configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella Samples Deep learning nel server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > Training-with-Deep-learning > Train-iperparameter-Tune-deploy-with-pytorch** Folder. 
 
 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    È anche possibile trovare una [versione di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) completata di questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione intelligente dei parametri, la distribuzione di modelli e i widget del notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="import-packages"></a>Importare i pacchetti

Prima di tutto, importare le librerie Python necessarie.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un oggetto [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Creare un oggetto area di lavoro dal file `config.json` creato nella [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Creare un esperimento di apprendimento approfondito

Creare un esperimento e una cartella che contengano gli script di training. In questo esempio, creare un esperimento denominato "pytorch-Birds".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Ottenere i dati

Il set di dati è costituito da circa 120 di immagini di training per tacchini e polling, con immagini di convalida 100 per ogni classe. Il set di dati viene scaricato ed estratto come parte dello script di training `pytorch_train.py`. Le immagini sono un subset del [set di dati V5 Open images V5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Preparare gli script di training

In questa esercitazione, lo script di training, `pytorch_train.py`, è già disponibile. In pratica, è possibile usare qualsiasi script di training personalizzato, così come è, ed eseguirlo con Azure Machine Learning.

Caricare lo script di training di Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Tuttavia, se si vuole usare Azure Machine Learning funzionalità di rilevamento e metrica, è necessario aggiungere un codice di piccola entità all'interno dello script di training. Esempi di rilevamento delle metriche sono disponibili in `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per l'esecuzione del processo PyTorch. In questo esempio, creare un cluster di calcolo Azure Machine Learning abilitato per GPU.

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

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un calcolo di destinazione](concept-compute-target.md) .

## <a name="create-a-pytorch-estimator"></a>Creare un PyTorch Estimator

[PyTorch Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fornisce un modo semplice per avviare un processo di formazione PyTorch su una destinazione di calcolo.

Lo strumento di stima PyTorch viene implementato tramite la classe generica [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , che può essere usata per supportare qualsiasi Framework. Per altre informazioni sui modelli di training con lo strumento di stima generico, vedere eseguire il training dei [modelli con Azure Machine Learning usando Estimator](how-to-train-ml-models.md)

Se lo script di training richiede l'esecuzione di pacchetti PIP o conda aggiuntivi, è possibile fare in modo che i pacchetti siano installati nell'immagine Docker risultante passando i relativi nomi tramite gli argomenti `pip_packages` e `conda_packages`.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Invia un'esecuzione

L' [oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione**: viene creata un'immagine Docker in base allo strumento di stima PyTorch. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione rispetto al momento disponibile.

- **Running**: tutti gli script nella cartella di script vengono caricati nella destinazione di calcolo, vengono montati o copiati gli archivi dati e viene eseguita la entry_script. Gli output da stdout e la cartella./logs vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione**: la cartella./Outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver eseguito il training del modello, è possibile registrarlo nell'area di lavoro. Con la registrazione del modello è possibile archiviare e deversionere i modelli nell'area di lavoro per semplificare la [gestione e la distribuzione del modello](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Il modello appena registrato viene distribuito esattamente come qualsiasi altro modello registrato in Azure Machine Learning, indipendentemente dallo strumento di stima utilizzato per il training. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla [creazione di una destinazione di calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

È inoltre possibile scaricare una copia locale del modello utilizzando l'oggetto Esegui. Nel `pytorch_train.py`script di training, un oggetto Salva PyTorch rende permanente il modello in una cartella locale (locale nella destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Training distribuito

Il [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) Estimator supporta anche il training distribuito nei cluster CPU e GPU. È possibile eseguire facilmente processi PyTorch distribuiti e Azure Machine Learning gestirà l'orchestrazione.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) è un framework open source di riduzione per la formazione distribuita sviluppata da uber. Offre un percorso facile ai processi PyTorch GPU distribuiti.

Per usare Horovod, specificare un oggetto [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) per il parametro `distributed_training` nel costruttore PyTorch. Questo parametro assicura che la libreria Horovod sia installata per l'uso nello script di training.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod e le relative dipendenze verranno installate per l'utente, in modo che sia possibile importarlo nello script di training `train.py` come indicato di seguito:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Esporta in ONNX

Per ottimizzare l'inferenza con il [Runtime ONNX](concept-onnx.md), convertire il modello PyTorch sottoposto a training nel formato ONNX. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione. Per un esempio, vedere l' [esercitazione](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il training e la registrazione di una rete neurale di apprendimento avanzato con PyTorch in Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

> [!div class="nextstepaction"]
> [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per il training di Deep learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
