---
title: Allenati modelli PyTorch deep learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di formazione PyTorch su scala aziendale usando la classe di stima Di torchatura di Azure Machine Learning.Learn how to run your PyTorch training scripts at enterprise scale using Azure Machine Learning's PyTorch estimator class.  Gli script di esempio classificano le immagini di pollo e tacchino per creare una rete neurale di deep learning basata sul tutorial di apprendimento di trasferimento di PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834867"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Addestrare i modelli di deep learning Pytorch su larga scala con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come eseguire gli script di training PyTorch su scala aziendale usando la classe di stima PyTorch di Azure Machine Learning.In this article, learn how to run your [PyTorch](https://pytorch.org/) training scripts at enterprise scale using Azure Machine Learning's [PyTorch estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) class.  

Gli script di esempio in questo articolo vengono usati per classificare le immagini di pollo e tacchino per creare una rete neurale di deep learning basata [sull'esercitazione](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)sull'apprendimento del trasferimento di PyTorch. 

Sia che tu stia eseguendo il training di un modello PyTorch di deep learning da zero o che tu stia portando un modello esistente nel cloud, puoi usare Azure Machine Learning per scalare orizzontalmente i processi di formazione open source usando le risorse di calcolo cloud elastiche. È possibile creare, distribuire, versione e monitorare modelli di livello di produzione con Azure Machine Learning.You can build, deploy, version, and monitor production-grade models with Azure Machine Learning. 

Ulteriori informazioni sul [deep learning e sull'apprendimento automatico](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno di questi ambienti:Run this code on either of these environments:

- Istanza di calcolo di Azure Machine Learning: non sono necessari download o installazioni

    - Completare [l'esercitazione: Ambiente](tutorial-1st-experiment-sdk-setup.md) di installazione e area di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella di deep learning degli esempi nel server notebook, trovare un blocco appunti completato ed espanso passando a questa directory: **how-to-use-azureml > training-with-deep-learning > cartella train-hyperparameter-tune-deploy-with-pytorch.** 
 
 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - Creare un file di [configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - Scaricare i file di script di [esempioDownload the sample script files](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    È inoltre possibile trovare una [versione completata Di Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) di questa guida nella pagina degli esempi di GitHub.You can also find a completed Jupyter Notebook version of this guide on the GitHub samples page. Il blocco appunti include sezioni espanse che includono l'ottimizzazione intelligente degli iperparametri, la distribuzione dei modelli e i widget per i blocchi appunti.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione configura l'esperimento di training caricando i pacchetti pitone necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

In primo luogo, importare le librerie Python necessarie.

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

[L'area di lavoro](concept-workspace.md) di Azure Machine Learning è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK è possibile accedere agli [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) elementi dell'area di lavoro creando un oggetto .

Creare un oggetto `config.json` dell'area di lavoro dal file creato nella [sezione Prerequisiti.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Creare un esperimento di deep learning

Creare un esperimento e una cartella per contenere gli script di formazione. In questo esempio, creare un esperimento chiamato "pytorch-birds".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Ottenere i dati

Il set di dati è costituito da circa 120 immagini di training ciascuna per tacchini e polli, con 100 immagini di convalida per ogni classe. Il set di dati verrà scaricato ed `pytorch_train.py`estratto come parte dello script di training. Le immagini sono un sottoinsieme del set di dati [Open Images v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Preparare gli script di formazione

In questa esercitazione viene `pytorch_train.py`già fornito lo script di training , . In practice, you can take any custom training script, as is, and run it with Azure Machine Learning.

Caricare lo script di `pytorch_train.py`allenamento Pytorch, .

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Tuttavia, se si desidera usare le funzionalità di rilevamento e metriche di Azure Machine Learning, sarà necessario aggiungere un codice di piccola quantità all'interno dello script di training. Esempi di monitoraggio delle `pytorch_train.py`metriche sono disponibili in .

## <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per il processo PyTorch in cui eseguire. In questo esempio creare un cluster di calcolo di Azure Machine Learning abilitato per GPU.

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

## <a name="create-a-pytorch-estimator"></a>Creare uno stimatore di PitorchCreate a PyTorch estimator

Lo [stimatore PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fornisce un modo semplice per avviare un processo di formazione PyTorch su una destinazione di calcolo.

Lo stimatore PyTorch viene implementato [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) tramite la classe generica, che può essere utilizzata per supportare qualsiasi framework. Per altre informazioni sui modelli di training usando lo stimatore generico, vedere Eseguire il training dei [modelli con Azure Machine Learning usando estimator](how-to-train-ml-models.md)

Se lo script di training richiede pacchetti pip o conda aggiuntivi per l'esecuzione, è `pip_packages` possibile `conda_packages` fare in modo che i pacchetti siano installati nell'immagine docker risultante passando i relativi nomi attraverso gli argomenti e .

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

Per ulteriori informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per la formazione e](how-to-use-environments.md)la distribuzione .

## <a name="submit-a-run"></a>Inviare una corsa

[L'oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Durante l'esecuzione dell'esecuzione, l'esecuzione passa attraverso le seguenti fasi:

- **Preparazione**: Viene creata un'immagine docker in base allo stimatore PyTorch. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per esecuzioni successive. I log vengono trasmessi anche alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Scalabilità:** il cluster tenta di aumentare le scalabilità se il cluster Batch AI richiede più nodi per eseguire l'esecuzione di quelli attualmente disponibili.

- **In esecuzione:** tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e della cartella ./logs vengono trasmessi alla cronologia di esecuzione e possono essere utilizzati per monitorare l'esecuzione.

- **Post-elaborazione:** la cartella ./outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver eseguito il training del modello, è possibile registrarlo nell'area di lavoro. La registrazione dei modelli consente di archiviare e versione i modelli nell'area di lavoro per semplificare [la gestione e](concept-model-management-and-deployment.md)la distribuzione dei modelli.

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Il modello appena registrato viene distribuito esattamente come qualsiasi altro modello registrato in Azure Machine Learning, indipendentemente dallo stimatore usato per il training. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla creazione di una destinazione di [calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

È inoltre possibile scaricare una copia locale del modello utilizzando l'oggetto Run. Nello script `pytorch_train.py`di training , un oggetto di salvataggio PyTorch rende persistente il modello in una cartella locale (locale alla destinazione di calcolo). È possibile utilizzare l'oggetto Run per scaricare una copia.

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

Lo [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) stimatore supporta anche il training distribuito tra i cluster CPU e GPU. È possibile eseguire facilmente processi di seto-1/ distribuita e Azure Machine Learning gestirà l'orchestrazione per l'utente.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) è un quadro open source, tutti riducono la formazione distribuita sviluppata da Uber. Offre un percorso semplice per i processi GPU PyTorch distribuiti.

Per utilizzare Horovod, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) specificare `distributed_training` un oggetto per il parametro nel costruttore PyTorch. Questo parametro assicura che la libreria Horovod sia installata per l'utilizzo nello script di training.


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
Horovod e le relative dipendenze verranno installati per l'utente, in modo da poterlo importare nello script `train.py` di training come segue:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Esporta in ONNX

Per ottimizzare l'inferenza con [ONNX Runtime](concept-onnx.md), convertire il modello PyTorch sottoposto a training nel formato ONNX. L'inferenza, o punteggio del modello, è la fase in cui il modello distribuito viene usato per la stima, in genere nei dati di produzione. Vedere [l'esercitazione](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) per un esempio.

## <a name="next-steps"></a>Passaggi successivi

In this article, you trained and registered a deep learning, neural network using PyTorch on Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

> [!div class="nextstepaction"]
> [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per la formazione sul deep learning distribuito in AzureReference architecture for distributed deep learning training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

