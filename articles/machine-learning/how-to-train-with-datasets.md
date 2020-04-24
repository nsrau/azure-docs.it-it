---
title: Eseguire il training con azureml-DataSets
titleSuffix: Azure Machine Learning
description: Informazioni su come usare i set di impostazioni nel training
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
# <a name="train-with-datasets-in-azure-machine-learning"></a>Eseguire il training con set di impostazioni in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo vengono illustrate le due modalità di utilizzo [Azure Machine Learning set](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) di dati in un esperimento remoto viene eseguito senza doversi preoccupare delle stringhe di connessione o dei percorsi di dati.

- Opzione 1: se si dispone di dati strutturati, creare un TabularDataset e usarlo direttamente nello script di training.

- Opzione 2: se si dispone di dati non strutturati, creare un file filedataset e montare o scaricare file in un computer remoto per il training.

Azure Machine Learning set di impostazioni forniscono un'integrazione perfetta con Azure Machine Learning prodotti di formazione come [scriptrun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py), iperguida e [Azure Machine Learning pipeline](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Prerequisiti

Per creare ed eseguire il training con i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-DataSets.

> [!Note]
> Alcune classi del set di dati presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Opzione 1: usare i set di impostazioni direttamente negli script di training

In questo esempio si crea un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) e lo si usa come input diretto all'oggetto per `estimator` il training. 

### <a name="create-a-tabulardataset"></a>Creare un TabularDataset

Il codice seguente crea un TabularDataset non registrato da un URL Web. È anche possibile creare set di dati da file o percorsi locali negli archivi dati. Altre informazioni su [come creare set di impostazioni](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Accedere al set di dati di input nello script di training

Gli oggetti TabularDataset offrono la possibilità di caricare i dati in un frame di dati Pandas o Spark in modo che sia possibile lavorare con le librerie di formazione e preparazione dei dati note. Per sfruttare questa funzionalità, è possibile passare un TabularDataset come input nella configurazione di training e quindi recuperarlo nello script.

A tale scopo, accedere al set di dati di [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) input tramite l'oggetto nello script di training [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) e usare il metodo. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Configurare lo strumento di stima

Viene usato un oggetto [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) per inviare l'esecuzione dell'esperimento. Azure Machine Learning dispone di estimatori preconfigurati per i Framework di apprendimento automatico comuni, oltre a uno strumento di stima generico.

Questo codice crea un oggetto estimatore generico `est`,, che specifica

* Directory di script per gli script. Tutti i file in questa directory vengono caricati nei nodi del cluster per l'esecuzione.
* Script di training, *train_titanic. py*.
* Set di dati di input per `titanic`il training. `as_named_input()`è necessario per fare riferimento al set di dati di input in base al nome assegnato nello script di training. 
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


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Opzione 2: montare i file in una destinazione di calcolo remota

Se si desidera rendere disponibili i file di dati nella destinazione di calcolo per il training, utilizzare [filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) per montare o scaricare i file a cui fa riferimento.

### <a name="mount-vs-download"></a>Montaggio rispetto a download

Il montaggio o il download di file di qualsiasi formato sono supportati per i set di dati creati dall'archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e database di Azure per PostgreSQL. 

Quando si monta un set di dati, si collegano i file a cui fa riferimento il set di dati a una directory (punto di montaggio) e lo si rende disponibile nella destinazione di calcolo. Il montaggio è supportato per i calcoli basati su Linux, tra cui Azure Machine Learning calcolo, macchine virtuali e HDInsight. Quando si scarica un set di dati, tutti i file a cui fa riferimento il set di dati verranno scaricati nella destinazione di calcolo. Il download è supportato per tutti i tipi di calcolo. 

Se lo script elabora tutti i file a cui fa riferimento il set di dati e il disco di calcolo può adattarsi al set di dati completo, è consigliabile scaricare il download per evitare il sovraccarico del flusso dei dati dai servizi di archiviazione. Se le dimensioni dei dati superano le dimensioni del disco di calcolo, il download non è possibile. Per questo scenario, è consigliabile montare poiché solo i file di dati usati dallo script vengono caricati al momento dell'elaborazione.

Il codice seguente viene montato `dataset` sulla directory Temp in`mounted_path`

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

Nell'esempio seguente viene creato un oggetto filedataset non registrato da URL Web. Altre informazioni su [come creare set di impostazioni](https://aka.ms/azureml/howto/createdatasets) da altre origini.

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

### <a name="configure-the-estimator"></a>Configurare lo strumento di stima

Oltre a passare il set di `inputs` dati tramite il parametro nello strumento di stima, è anche possibile `script_params` passare il set di dati tramite e ottenere il percorso dati (punto di montaggio) nello script di training tramite argomenti. In questo modo, è possibile evitare che lo script di training sia indipendente da azureml-SDK. In altre parole, sarà possibile usare lo stesso script di training per il debug locale e il training remoto in qualsiasi piattaforma cloud.

Un oggetto [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) Estimator viene usato per inviare l'esecuzione per gli esperimenti Scikit-learn. Scopri di più sul training con [SKlearn Estimator](how-to-train-scikit-learn.md).

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

### <a name="retrieve-the-data-in-your-training-script"></a>Recuperare i dati nello script di training

Dopo aver inviato l'esecuzione, i file di dati a `mnist` cui fa riferimento il set di dati verranno montati nella destinazione di calcolo. Nel codice seguente viene illustrato come recuperare i dati nello script.

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

I [notebook del set di dati](https://aka.ms/dataset-tutorial) dimostrano ed espandono i concetti in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il [training automatico dei modelli di Machine Learning](how-to-auto-train-remote.md) con TabularDatasets

* [Training di modelli di classificazione delle immagini](https://aka.ms/filedataset-samplenotebook) con filedataset

* [Eseguire il training con i set di impostazioni usando le pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
