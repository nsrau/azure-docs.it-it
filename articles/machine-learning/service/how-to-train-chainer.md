---
title: Eseguire il training di modelli di chaining Deep Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di training di PyTorch su scala aziendale usando la classe di stimatore chainer di Azure Machine Learning.  Lo script di esempio classifica le immagini di cifre scritte a mano per creare una rete neurale di apprendimento avanzato usando la libreria Python del Chainer in esecuzione in numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: bfe44e552618dfbee13e8c85ef424f52d4b7ae5f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814983"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Esegui il training e la registrazione dei modelli di Chainer su larga scala con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire gli script di training del [Chainer](https://chainer.org/) su scala aziendale usando la classe di [stimatore chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) di Azure Machine Learning. Lo script di training di esempio in questo articolo usa il popolare [set di dati MNIST](http://yann.lecun.com/exdb/mnist/) per classificare le cifre scritte a mano usando una rete neurale profonda (DNN) creata usando la libreria Python del Chainer in esecuzione su [numpy](https://www.numpy.org/).

Sia che si stia eseguendo il training di un modello di chaining di Deep Learning da zero o che si stia introducendo un modello esistente nel cloud, è possibile usare Azure Machine Learning per scalare i processi di training open source con risorse di calcolo elastiche del cloud. Con Azure Machine Learning è possibile compilare, distribuire, eseguire la versione e monitorare i modelli a livello di produzione. 

Scopri di più sull'apprendimento avanzato [rispetto a Machine Learning](concept-deep-learning-vs-machine-learning.md).

Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:

- Azure Machine Learning macchina virtuale Notebook-nessun download o installazione necessaria

    - Completare l' [esercitazione: configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella Samples Deep learning nel server notebook trovare un notebook e i file completati nella cartella **How-to-use-azureml > ml-frameworks > chainer > deployment > Train-iperparameter-Tune-deploy-with-Chainer** .  Il notebook include sezioni espanse che coprono l'ottimizzazione intelligente dei parametri, la distribuzione di modelli e i widget del notebook.

- Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - Scaricare il file di script di esempio [chainer_mnist. py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py).
     - È anche possibile trovare una [versione di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) completata di questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione intelligente dei parametri, la distribuzione di modelli e i widget del notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

Importare prima di tutto la libreria Python azureml. core e visualizzare il numero di versione.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un oggetto [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Creare un oggetto dell'area di lavoro leggendo il file `config.json` creato nella [sezione Prerequisiti](#prerequisites):

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Creare una directory di progetto
Creare una directory che conterrà tutto il codice necessario dal computer locale a cui sarà necessario accedere nella risorsa remota. Sono inclusi gli script di training e gli eventuali file aggiuntivi da cui dipende lo script di training.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Preparare lo script di training

In questa esercitazione, lo script di training **chainer_mnist. py** è già disponibile. In pratica, si dovrebbe essere in grado di eseguire qualsiasi script di training personalizzato ed eseguirlo con Azure ML senza dover modificare il codice.

Per usare le funzionalità di rilevamento e metrica di Azure ML, aggiungere una piccola quantità di codice di Azure ML all'interno dello script di training.  Lo script di training **chainer_mnist. py** Mostra come registrare alcune metriche nell'esecuzione di Azure ml usando l'oggetto `Run` all'interno dello script.

Lo script di training fornito usa i dati di esempio della funzione Chainer `datasets.mnist.get_mnist`.  Per i dati personali, potrebbe essere necessario usare passaggi come il caricamento di set di dati [e script](how-to-train-keras.md#data-upload) per rendere disponibili i dati durante il training.

Copiare lo script di training **chainer_mnist. py** nella directory del progetto.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Creare un esperimento di apprendimento approfondito

Creare un esperimento. In questo esempio, creare un esperimento denominato "Chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Creare o ottenere una destinazione di calcolo

È necessaria una [destinazione di calcolo](concept-compute-target.md) per il training del modello. In questo esempio si usa il calcolo gestito di Azure ML (AmlCompute) per la risorsa di calcolo di training remoto.

**La creazione di AmlCompute richiede circa 5 minuti**. Se il AmlCompute con lo stesso nome è già presente nell'area di lavoro, questo codice ignora il processo di creazione.  

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

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un calcolo di destinazione](concept-compute-target.md) .

## <a name="create-a-chainer-estimator"></a>Creare uno strumento di stima del Chainer

Lo strumento di [stima del Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) fornisce un modo semplice per avviare i processi di training del Chainer nella destinazione di calcolo.

Lo strumento di stima del chainer viene implementato tramite la classe generica [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , che può essere utilizzata per supportare qualsiasi Framework. Per altre informazioni sui modelli di training con lo strumento di stima generico, vedere eseguire il training dei [modelli con Azure Machine Learning usando Estimator](how-to-train-ml-models.md)

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

L' [oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione**: viene creata un'immagine Docker in base allo strumento di stima del Chainer. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione rispetto al momento disponibile.

- **Running**: tutti gli script nella cartella di script vengono caricati nella destinazione di calcolo, vengono montati o copiati gli archivi dati e viene eseguita la entry_script. Gli output da stdout e la cartella./logs vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione**: la cartella./Outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training del modello, è possibile salvarlo e registrarlo nell'area di lavoro. Con la registrazione del modello è possibile archiviare e deversionere i modelli nell'area di lavoro per semplificare la [gestione e la distribuzione del modello](concept-model-management-and-deployment.md).


Al termine del training del modello, registrare il modello nell'area di lavoro con il codice seguente.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Se viene visualizzato un messaggio di errore che indica che il modello non è stato trovato, assegnarlo un minuto e riprovare.  Talvolta si verifica un lieve ritardo tra la fine dell'esecuzione del training e la disponibilità del modello nella directory degli output.

È anche possibile scaricare una copia locale del modello. Questa operazione può essere utile per eseguire altre operazioni di convalida del modello localmente. Nello script di training, `chainer_mnist.py`, un oggetto Saver Salva in modo permanente il modello in una cartella locale (locale nella destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia dall'archivio dati.

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

In questo articolo è stato eseguito il training e la registrazione di una rete neurale con formazione approfondita usando Chainer su Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla [distribuzione del modello](how-to-deploy-and-where.md) .

* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)

* [Visualizza l'architettura di riferimento per il training di Deep learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
