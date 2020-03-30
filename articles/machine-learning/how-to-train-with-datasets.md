---
title: Eseguire il training con set di dati di azuremlTrain with azureml-datasets
titleSuffix: Azure Machine Learning
description: Informazioni su come usare i set di dati nel training
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283498"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Eseguire il training con i set di dati in Azure Machine LearningTrain with datasets in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo vengono [appresi](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) i due modi per usare i set di dati di Azure Machine Learning in un esperimento remoto che il training viene eseguito senza preoccuparsi delle stringhe di connessione o dei percorsi dei dati.

- Opzione 1: se si dispone di dati strutturati, creare un TabularDataset e utilizzarlo direttamente nello script di training.

- Opzione 2: se si dispone di dati non strutturati, creare un FileDataset e montare o scaricare i file in un calcolo remoto per il training.

I set di dati di Azure Machine Learning offrono una perfetta integrazione con i prodotti di formazione di Azure Machine Learning come le pipeline [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py), [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) e [Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Prerequisiti

Per creare ed eseguire il training con i set di dati, è necessario:To create and train with datasets, you need:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Area di lavoro di [Azure Machine Learning](how-to-manage-workspace.md).

* [Azure Machine Learning SDK per Python è installato,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)che include il pacchetto azureml-datasets.

> [!Note]
> Alcune classi Dataset hanno dipendenze dal pacchetto [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Per gli utenti Linux, queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Opzione 1: Usare i set di dati direttamente negli script di trainingOption 1: Use datasets directly in training scripts

In questo esempio si crea un [tabulare TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) `estimator` e lo si usa come input diretto all'oggetto per il training. 

### <a name="create-a-tabulardataset"></a>Creare un tabulareCreate a TabularDataset

Il codice seguente crea un TabularDataset non registrato da un URL Web.The following code creates an unregistered TabularDataset from a web url. È inoltre possibile creare set di dati da file o percorsi locali negli archivi dati. Ulteriori informazioni su [come creare set](https://aka.ms/azureml/howto/createdatasets)di dati .

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Accedere al set di dati di input nello script di trainingAccess the input dataset in your training script

Gli oggetti TabularDataset consentono di caricare i dati in un panda o in un frame di dati spark in modo da poter lavorare con le raccolte di training e di preparazione dei dati familiari. Per sfruttare questa funzionalità, è possibile passare un TabularDataset come input nella configurazione di training e quindi recuperarlo nello script.

A tale scopo, accedere al [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) set di dati di [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) input tramite l'oggetto nello script di training e usare il metodo . 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Configurare lo stimatore

Un oggetto [stimatore](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) viene utilizzato per inviare l'esecuzione dell'esperimento. Azure Machine Learning ha stimatori preconfigurati per framework di apprendimento automatico comuni, nonché uno stimatore generico.

Questo codice crea un oggetto `est`stimatore generico, , che specifica

* Una directory di script per gli script. Tutti i file in questa directory vengono caricati nei nodi del cluster per l'esecuzione.
* Lo script di addestramento, *train_titanic.py*.
* Set di dati `titanic`di input per il training, . `as_named_input()`è necessario in modo che il set di dati di input possa essere fatto riferimento dal nome assegnato nello script di training. 
* Destinazione di calcolo per l'esperimento.
* Definizione dell'ambiente per l'esperimento.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Opzione 2: Montare i file in una destinazione di calcolo remotaOption 2: Mount files to a remote compute target

Se si desidera rendere disponibili i file di dati nella destinazione di calcolo per il training, usare [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) per montare o scaricare i file a cui fa riferimento.

### <a name="mount-vs-download"></a>Montaggio e download

Il montaggio o il download di file di qualsiasi formato sono supportati per i set di dati creati da Archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Database SQL di Azure e Database di Azure per PostgreSQL. 

Quando si monta un set di dati, si collegano i file a cui fa riferimento il dataset a una directory (punto di montaggio) e lo si rende disponibile nella destinazione di calcolo. Mounting is supported for Linux-based computes, including Azure Machine Learning Compute, virtual machines, and HDInsight. Quando si scarica un set di dati, tutti i file a cui fa riferimento il set di dati verranno scaricati nella destinazione di calcolo. Il download è supportato per tutti i tipi di calcolo. 

Se lo script elabora tutti i file a cui fa riferimento il set di dati e il disco di calcolo può adattarsi all'intero set di dati, è consigliabile scaricare per evitare l'overhead dei dati di streaming dai servizi di archiviazione. Se le dimensioni dei dati superano le dimensioni del disco di calcolo, il download non è possibile. Per questo scenario, è consigliabile montare poiché solo i file di dati utilizzati dallo script vengono caricati al momento dell'elaborazione.

Il codice seguente `dataset` viene montato nella directory temp`mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

### <a name="create-a-filedataset"></a>Creare un oggetto FileDataset

Nell'esempio seguente viene creato un oggetto FileDataset non registrato dagli URL Web. Altre informazioni su [come creare set di dati](https://aka.ms/azureml/howto/createdatasets) da altre origini.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>Configurare lo stimatore

Oltre a passare il `inputs` set di dati tramite il parametro `script_params` nello stimatore, è anche possibile passare il set di dati e ottenere il percorso dati (punto di montaggio) nello script di training tramite argomenti. In questo modo, è possibile mantenere lo script di training indipendente da azureml-sdk. In altre parole, sarà possibile utilizzare lo stesso script di formazione per il debug locale e la formazione remota su qualsiasi piattaforma cloud.

Un [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) oggetto stimatore viene utilizzato per inviare la corsa per gli esperimenti scikit-learn. Scopri di più sull'allenamento con lo [stimatore SKlearn](how-to-train-scikit-learn.md).

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Recuperare i dati nello script di trainingRetrieve the data in your training script

Dopo aver inviato l'esecuzione, `mnist` i file di dati a cui fa riferimento il set di dati verranno montati nella destinazione di calcolo. Nel codice seguente viene illustrato come recuperare i dati nello script.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>Esempi di notebook

Gli [appunti del set di dati](https://aka.ms/dataset-tutorial) illustrano ed espandono i concetti illustrati in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

* [Adauto treno modelli](how-to-auto-train-remote.md) di apprendimento automatico con TabularDatasets Auto train machine learning models with TabularDatasets

* [Eseguire il training dei modelli](https://aka.ms/filedataset-samplenotebook) di classificazione delle immagini con FileDatasetsTrain image classification models with FileDatasets

* [Eseguire il training con i set di dati usando le pipelineTrain with datasets using pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
