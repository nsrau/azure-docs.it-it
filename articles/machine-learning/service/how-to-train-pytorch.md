---
title: Eseguire il training e registrare i modelli di PyTorch
titleSuffix: Azure Machine Learning service
description: Questo articolo illustra come eseguire il training e registrare un modello di PyTorch utilizzando il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: 8def58eb003fcc817c21151416744cf391b5f38f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443939"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>Eseguire il training e registrare i modelli di PyTorch su larga scala con servizi di Azure Machine Learning

Questo articolo illustra come eseguire il training e registrare un modello di PyTorch utilizzando il servizio di Azure Machine Learning. Si basa sul [trasferimento del PyTorch esercitazione di apprendimento](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) che crea un classificatore di reti neurali profonde (DNN) per le immagini di tacchini e volatili.

[PyTorch](https://pytorch.org/) è un framework di elaborazione open source comunemente usato per creare le reti neurali profonde (DNN). Con il servizio di Azure Machine Learning, è possibile aumentare rapidamente i processi di training open source con le risorse di calcolo cloud elastico. È inoltre possibile monitorare le esecuzioni di training, i modelli di versione, la distribuzione di modelli e molto altro ancora.

Se si sta sviluppando un modello di PyTorch da zero o si desidera introdurre un modello esistente nel cloud, servizio Azure Machine Learning consentono di compilare modelli pronti per la produzione.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno dei due ambienti:

 - Azure Machine Learning Notebook VM - alcun download o installazione necessaria

    - Completare la [Guida introduttiva di notebook basato su cloud](quickstart-run-cloud-notebook.md) per creare un server notebook dedicato precaricato con il SDK e il repository di esempio.
    - Nella cartella samples nel server notebook, trovare un notebook completato ed espanso passando a questa directory: **how-to-uso-azureml > formazione con deep learning > train-hyperparameter-tune-deploy-with-pytorch** cartella. 
 
 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK per Python](setup-create-workspace.md#sdk)
    - [Creare un file di configurazione dell'area di lavoro](setup-create-workspace.md#write-a-configuration-file)
    - [Scaricare i file di script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    È anche possibile trovare un completate [versione di Notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) di questa Guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione degli iperparametri intelligenti, la distribuzione del modello e i widget di notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

In questa sezione si configura l'esperimento di training per il caricamento di pacchetti python necessari, l'inizializzazione di un'area di lavoro, creazione di un esperimento e caricare i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

In primo luogo, importare le librerie di Python necessari.

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

Il [dell'area di lavoro di Azure Machine Learning servizio](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK, è possibile accedere gli elementi dell'area di lavoro mediante la creazione di un [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) oggetto.

Crea un oggetto dell'area di lavoro dal `config.json` creato nel file il [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento e una cartella per contenere gli script di training. In questo esempio, creare un esperimento denominato "pytorch volatili".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Ottenere i dati

Il set di dati è costituito da circa 120 immagini di training ogni per tacchini e volatili, con 100 immagini di convalida per ogni classe. Si verrà scaricato ed estratto il set di dati come parte del nostro script di training `pytorch_train.py`. Le immagini sono un subset del [set di dati di immagini Open v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Preparazione degli script di training

In questa esercitazione, lo script di training, `pytorch_train.py`, ma è disponibile. In pratica, è possibile eseguire qualsiasi script di training personalizzati, come è ed eseguirlo con il servizio di Azure Machine Learning.

Caricare lo script di training, Pytorch `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Tuttavia, se si desidera usare le funzionalità di metriche e registrazione del servizio di Azure Machine Learning, è necessario aggiungere il codice una piccola quantità all'interno dello script di training. Esempi di metriche di rilevamento sono reperibile `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per il processo di PyTorch eseguire nell'ambito. In questo esempio, creare un cluster di calcolo abilitate per GPU di Azure Machine Learning.

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

Per altre informazioni sulle destinazioni di calcolo, vedere la [What ' s una destinazione di calcolo](concept-compute-target.md) articolo.

## <a name="create-a-pytorch-estimator"></a>Creare un Estimatore PyTorch

Il [estimator PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fornisce un modo semplice per l'avvio di un processo di training PyTorch in una destinazione di calcolo.

Lo strumento di stima di PyTorch viene implementata tramite il tipo generico [ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) (classe), che può essere usato per supportare qualsiasi framework. Per altre informazioni sui training dei modelli utilizzando lo strumento di stima generico, vedere [il training dei modelli con Azure Machine Learning con strumento di stima](how-to-train-ml-models.md)

Se lo script di training deve aggiuntive pip o conda pacchetti da eseguire, è possibile usare i pacchetti installati nell'immagine docker risultante passando i nomi tramite il `pip_packages` e `conda_packages` argomenti.

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

Il [oggetto esecuzione](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia per la cronologia di esecuzione durante l'esecuzione del processo e dopo il completamento.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

L'esecuzione viene eseguita, la chiamata passa attraverso le fasi seguenti:

- **Preparazione**: In base alla stima di PyTorch viene creata un'immagine docker. L'immagine viene caricata in Registro contenitori dell'area di lavoro e memorizzato nella cache per le esecuzioni successive. I log vengono anche trasmessi per la cronologia di esecuzione e possono essere consultati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: Il cluster tenta di aumentare le prestazioni se il cluster Batch per intelligenza artificiale richiede più nodi per l'esecuzione dell'esecuzione rispetto a quelli attualmente disponibili.

- **In esecuzione**: Tutti gli script nella cartella dello script vengono caricati nella destinazione di calcolo, archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e. / cartella dei log vengono inviati nel flusso per la cronologia di esecuzione e può essere usato per monitorare l'esecuzione.

- **Post-elaborazione**: I. / output dell'esecuzione della cartella da copiare la cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver eseguito il training modello, è possibile registrarlo per l'area di lavoro. Registrazione del modello consente di store e la versione dei modelli nell'area di lavoro per semplificare [modello di distribuzione e gestione](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

È anche possibile scaricare una copia locale del modello usando l'oggetto di esecuzione. Nello script di training `pytorch_train.py`, un PyTorch salvare un oggetto mantiene il modello in una cartella locale (computer locale per la destinazione di calcolo). È possibile usare l'oggetto di esecuzione per scaricare una copia.

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

Il [ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) estimator supporta anche l'addestramento distribuito tra i cluster CPU e GPU. È possibile eseguire facilmente i processi distribuiti PyTorch e servizio Azure Machine Learning gestiranno l'orchestrazione per l'utente.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) un open-source, ridurre la framework per l'addestramento distribuito sviluppato da Uberjar. Offre un percorso facilitato per i processi distribuiti PyTorch GPU.

Per utilizzare Horovod, specificare un [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) dell'oggetto per il `distributed_training` parametro nel costruttore PyTorch. Questo parametro assicura che library Horovod viene installato da usare nello script di training.


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
Horovod e le relative dipendenze verranno installate automaticamente, pertanto è possibile importarlo nello script di training `train.py` come indicato di seguito:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Esportare in ONNX

Per ottimizzare l'inferenza con il [ONNX Runtime](concept-onnx.md), convertire il modello di PyTorch training nel formato ONNX. Inferenza o modello di punteggio, è la fase in cui viene usato il modello distribuito per la stima, in genere sui dati di produzione. Vedere le [esercitazione](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) per un esempio.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, viene sottoposto a training e registrato un modello di PyTorch nel servizio di Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione modello.

> [!div class="nextstepaction"]
> [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
