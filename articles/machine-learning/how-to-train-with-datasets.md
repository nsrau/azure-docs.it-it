---
title: Eseguire il training con azureml-DataSets
titleSuffix: Azure Machine Learning
description: Informazioni su come usare i set di impostazioni nel training
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/20/2020
ms.custom: tracking-python
ms.openlocfilehash: a9b9faed111e6126bfdb30e4237a988afd947823
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560133"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Eseguire il training con set di impostazioni in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come usare i set di impostazioni di [Azure Machine Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) negli esperimenti di training.  È possibile usare i set di dati nella destinazione di calcolo locale o remota senza preoccuparsi di stringhe di connessione o percorsi di dati.

Azure Machine Learning set di impostazioni forniscono un'integrazione perfetta con Azure Machine Learning prodotti di formazione come [scriptrun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py), iperguida e [Azure Machine Learning pipeline](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Prerequisiti

Per creare ed eseguire il training con i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-DataSets.

> [!Note]
> Alcune classi del set di dati presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="access-and-explore-input-datasets"></a>Accedere ed esplorare i set di dati di input

È possibile accedere a un TabularDataset esistente dallo script di training di un esperimento nell'area di lavoro e caricare il set di dati in un frame di dati Pandas per un'ulteriore esplorazione nell'ambiente locale.

Il codice seguente usa il [`get_context()`]() metodo nella [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) classe per accedere al TabularDataset di input esistente, `titanic` , nello script di training. USA quindi il [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metodo per caricare il set di dati in un frame di dati Pandas per eseguire ulteriori operazioni di esplorazione e preparazione dei dati prima del training.

> [!Note]
> Se l'origine dati originale contiene NaN, stringhe vuote o valori vuoti, quando si usa il to_pandas_dataframe (), questi valori vengono sostituiti come valore *null* . 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

Se è necessario caricare i dati preparati in un nuovo set di dati da un frame di dati Pandas in memoria, scrivere i dati in un file locale, ad esempio un parquet, e creare un nuovo set di dati da tale file. È anche possibile creare set di dati da file o percorsi locali negli archivi dati. Altre informazioni su [come creare set di impostazioni](how-to-create-register-datasets.md).

## <a name="use-datasets-directly-in-training-scripts"></a>Usare i set di impostazioni direttamente negli script di training

Se i dati strutturati non sono ancora registrati come set di dati, creare un TabularDataset e usarlo direttamente nello script di training per l'esperimento locale o remoto.

In questo esempio si crea un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) non registrato e lo si usa come input diretto all' `estimator` oggetto per il training. Se si vuole riusare questo TabularDataset con altri esperimenti nell'area di lavoro, vedere [come registrare i set di impostazioni nell'area di lavoro](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Creare un TabularDataset

Il codice seguente crea un TabularDataset non registrato da un URL Web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Gli oggetti TabularDataset offrono la possibilità di caricare i dati nel TabularDataset in un frame di dati Pandas o Spark, in modo da poter lavorare con le librerie di preparazione e formazione dei dati familiari senza dover uscire dal notebook. Per sfruttare questa funzionalità, vedere [accedere ed esplorare i set di dati di input](#access-and-explore-input-datasets).

### <a name="configure-the-estimator"></a>Configurare lo strumento di stima

Viene usato un oggetto [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) per inviare l'esecuzione dell'esperimento. Azure Machine Learning dispone di estimatori preconfigurati per i Framework di apprendimento automatico comuni, oltre a uno strumento di stima generico.

Questo codice crea un oggetto estimatore generico, `est` , che specifica

* Directory di script per gli script. Tutti i file in questa directory vengono caricati nei nodi del cluster per l'esecuzione.
* Script di training, *train_titanic. py*.
* Set di dati di input per il training `titanic_ds` . `as_named_input()`è necessario per fare riferimento al set di dati di input in base al nome assegnato `titanic` nello script di training. 
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

## <a name="mount-files-to-remote-compute-targets"></a>Montare i file nelle destinazioni di calcolo Remote

Se si dispone di dati non strutturati, creare un [filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) e montare o scaricare i file di dati per renderli disponibili per la destinazione di calcolo remota per il training. Informazioni su quando usare [Mount vs. download](#mount-vs-download) per gli esperimenti di training remoto. 

Nell'esempio seguente viene creato un oggetto filedataset e il set di dati viene montato nella destinazione di calcolo passandolo come argomento nell'estimatore per il training. 

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

È consigliabile passare il set di dati come argomento durante il montaggio. Oltre a passare il set di `inputs` dati tramite il parametro nello strumento di stima, è anche possibile passare il set `script_params` di dati tramite e ottenere il percorso dati (punto di montaggio) nello script di training tramite argomenti. In questo modo, sarà possibile usare lo stesso script di training per il debug locale e il training remoto in qualsiasi piattaforma cloud.

Un oggetto [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) Estimator viene usato per inviare l'esecuzione per gli esperimenti Scikit-learn. Dopo aver inviato l'esecuzione, i file di dati a cui fa riferimento il `mnist` set di dati verranno montati nella destinazione di calcolo. Scopri di più sul training con [SKlearn Estimator](how-to-train-scikit-learn.md).

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

Nel codice seguente viene illustrato come recuperare i dati nello script.

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


## <a name="mount-vs-download"></a>Installare Visual Studio download

Il montaggio o il download di file di qualsiasi formato sono supportati per i set di dati creati dall'archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e database di Azure per PostgreSQL. 

Quando si monta un set di dati, si collegano i file a cui fa riferimento il set di dati a una directory (punto di montaggio) e lo si rende disponibile nella destinazione di calcolo. Il montaggio è supportato per i calcoli basati su Linux, tra cui Azure Machine Learning calcolo, macchine virtuali e HDInsight. 

Quando si scarica un set di dati, tutti i file a cui fa riferimento il set di dati verranno scaricati nella destinazione di calcolo. Il download è supportato per tutti i tipi di calcolo. 

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

## <a name="notebook-examples"></a>Esempi di notebook

I [notebook del set di dati](https://aka.ms/dataset-tutorial) dimostrano ed espandono i concetti in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il [training automatico dei modelli di Machine Learning](how-to-auto-train-remote.md) con TabularDatasets.

* Eseguire il [training dei modelli di classificazione delle immagini](https://aka.ms/filedataset-samplenotebook) con i set di dati.

* Eseguire il [training con i set di impostazioni usando le pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).
