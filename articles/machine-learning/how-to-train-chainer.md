---
title: Allenati modelli Chainer di deep learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di formazione PyTorch su scala aziendale usando la classe di stima di Azure Machine Learning Chainer.Learn how to run your PyTorch training scripts at enterprise scale by using the Azure Machine Learning Chainor estimator class.  Lo script di esempio classifica le immagini delle cifre scritte a mano per creare una rete neurale di deep learning usando la libreria Python Chainer in esecuzione su numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536616"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Addestrare e registrare i modelli Chainer su larga scala con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come eseguire gli script di training Chainer su scala aziendale usando la classe di stima di Azure Machine Learning Chainer.In this article, learn how to run your [Chainer](https://chainer.org/) training scripts at enterprise scale by using the Azure Machine Learning [Chainer estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) class. Lo script di training di esempio in questo articolo usa il set di [dati MNIST](http://yann.lecun.com/exdb/mnist/) comune per classificare le cifre scritte a mano usando una rete neurale profonda (DNN) creata utilizzando la libreria Python Chainer in esecuzione su [numpy](https://www.numpy.org/).

Sia che tu stia eseguendo il training di un modello Chainer di deep learning da zero o che tu stia portando un modello esistente nel cloud, puoi usare Azure Machine Learning per scalare orizzontalmente i processi di formazione open source usando le risorse di calcolo del cloud elastico. È possibile creare, distribuire, versione e monitorare modelli di livello di produzione con Azure Machine Learning.You can build, deploy, version, and monitor production-grade models with Azure Machine Learning. 

Ulteriori informazioni sul [deep learning e sull'apprendimento automatico](concept-deep-learning-vs-machine-learning.md).

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno di questi ambienti:Run this code on either of these environments:

- Istanza di calcolo di Azure Machine Learning: non sono necessari download o installazioni

    - Completare [l'esercitazione: Ambiente](tutorial-1st-experiment-sdk-setup.md) di installazione e area di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella di deep learning degli esempi nel server notebook, trovare un blocco appunti e i file completati nella > ml-framework di utilizzo **ml > concatenatore > distribuzione > cartella train-hyperparameter-tune-deploy-with-chainer.**  Il blocco appunti include sezioni espanse che includono l'ottimizzazione intelligente degli iperparametri, la distribuzione dei modelli e i widget per i blocchi appunti.

- Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - Creare un file di [configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - Scaricare il file di script di esempio [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - È anche possibile trovare una versione jupyter Notebook completata di questa guida nella pagina degli esempi di GitHub.You can also find a completed [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) of this guide on GitHub samples page. Il blocco appunti include sezioni espanse che includono l'ottimizzazione intelligente degli iperparametri, la distribuzione dei modelli e i widget per i blocchi appunti.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione configura l'esperimento di training caricando i pacchetti pitone necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

Importare innanzitutto la libreria Python di azureml.core e visualizzare il numero di versione.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

[L'area di lavoro](concept-workspace.md) di Azure Machine Learning è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK è possibile accedere agli [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) elementi dell'area di lavoro creando un oggetto .

Creare un oggetto dell'area di lavoro leggendo il `config.json` file creato nella sezione [Prerequisiti:](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Creare una directory di progetto
Creare una directory che conterrà tutto il codice necessario del computer locale a cui sarà necessario accedere alla risorsa remota. Sono inclusi lo script di training e gli eventuali file aggiuntivi da cui dipende lo script di training.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Preparare uno script di formazione

In questa esercitazione, lo script di training **chainer_mnist.py** è già disponibile per l'utente. In pratica, si dovrebbe essere in grado di prendere qualsiasi script di training personalizzato così com'è ed eseguirlo con Azure ML senza dover modificare il codice.

Per usare le funzionalità di rilevamento e metriche di Azure ML, aggiungere una piccola quantità di codice di Azure ML all'interno dello script di training.  Lo script di training **chainer_mnist.py** mostra come registrare alcune `Run` metriche nell'esecuzione di Azure ML usando l'oggetto all'interno dello script.

Lo script di training fornito usa `datasets.mnist.get_mnist` dati di esempio dalla funzione del concatenatore.  Per i propri dati, potrebbe essere necessario usare passaggi come Caricare set di [dati e script](how-to-train-keras.md#data-upload) per rendere i dati disponibili durante il training.

Copiare lo script di training **chainer_mnist.py** nella directory del progetto.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Creare un esperimento di deep learning

Creare un esperimento. In questo esempio, creare un esperimento denominato "chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Creare o ottenere una destinazione di calcoloCreate or get a compute target

È necessaria una [destinazione di calcolo](concept-compute-target.md) per il training del modello. In questo esempio si usa il calcolo gestito da Azure ML (AmlCompute) per la risorsa di elaborazione del training remoto.

**La creazione di AmlCompute richiede circa 5 minuti.** Se AmlCompute con tale nome è già presente nell'area di lavoro, questo codice ignora il processo di creazione.  

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

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un obiettivo](concept-compute-target.md) di calcolo.

## <a name="create-a-chainer-estimator"></a>Creare uno stimatore Chainer

Lo [stimatore Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) fornisce un modo semplice per avviare i lavori di training Chainer sul tuo obiettivo di calcolo.

Lo stimatore Chainer viene implementato [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) tramite la classe generica, che può essere utilizzata per supportare qualsiasi framework. Per altre informazioni sui modelli di training usando lo stimatore generico, vedere Eseguire il training dei [modelli con Azure Machine Learning usando estimator](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Inviare una corsa

[L'oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Durante l'esecuzione dell'esecuzione, l'esecuzione passa attraverso le seguenti fasi:

- **Preparazione**: Viene creata un'immagine docker in base allo stimatore Chainer. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per esecuzioni successive. I log vengono trasmessi anche alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Scalabilità:** il cluster tenta di aumentare le scalabilità se il cluster Batch AI richiede più nodi per eseguire l'esecuzione di quelli attualmente disponibili.

- **In esecuzione:** tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e della cartella ./logs vengono trasmessi alla cronologia di esecuzione e possono essere utilizzati per monitorare l'esecuzione.

- **Post-elaborazione:** la cartella ./outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training del modello, è possibile salvarlo e registrarlo nell'area di lavoro. La registrazione dei modelli consente di archiviare e versione i modelli nell'area di lavoro per semplificare [la gestione e](concept-model-management-and-deployment.md)la distribuzione dei modelli.


Al termine del training del modello, registrare il modello nell'area di lavoro con il codice seguente.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Il modello appena registrato viene distribuito esattamente come qualsiasi altro modello registrato in Azure Machine Learning, indipendentemente dallo stimatore usato per il training. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla creazione di una destinazione di [calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

È inoltre possibile scaricare una copia locale del modello. Ciò può essere utile per eseguire ulteriori operazioni di convalida del modello in locale. Nello script di `chainer_mnist.py`training, , un oggetto salvatore rende persistente il modello in una cartella locale (locale alla destinazione di calcolo). È possibile utilizzare l'oggetto Run per scaricare una copia dall'archivio dati.

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

In this article, you trained and registered a deep learning, neural network using Chainer on Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del [modello.](how-to-deploy-and-where.md)

* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)

* [Visualizza l'architettura di riferimento per la formazione sul deep learning distribuito in AzureView our reference architecture for distributed deep learning training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
