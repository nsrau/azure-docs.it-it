---
title: Eseguire il training con azureml-DataSets
titleSuffix: Azure Machine Learning
description: Informazioni su come rendere i dati disponibili per il calcolo locale o remoto per il training del modello ML con Azure Machine Learning set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 263509ce7d348e51bf4a2a1d7ad83fb5dfdb5e29
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489438"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Eseguire il training con set di impostazioni in Azure Machine Learning


Questo articolo illustra come usare i set di impostazioni di [Azure Machine Learning](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) negli esperimenti di training.  È possibile usare i set di dati nella destinazione di calcolo locale o remota senza preoccuparsi di stringhe di connessione o percorsi di dati.

Azure Machine Learning set di impostazioni forniscono un'integrazione perfetta con Azure Machine Learning [funzionalità di training](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py) come [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py), iperguida e [pipeline di Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Prerequisiti

Per creare ed eseguire il training con i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* [SDK Azure Machine Learning per Python installato](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0), che include il pacchetto azureml-DataSets.

> [!Note]
> Alcune classi del set di dati presentano dipendenze dal pacchetto [azureml-dataprep](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) . Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="use-datasets-directly-in-training-scripts"></a>Usare i set di impostazioni direttamente negli script di training

Se i dati strutturati non sono ancora registrati come set di dati, creare un TabularDataset e usarlo direttamente nello script di training per l'esperimento locale o remoto.

In questo esempio si crea un [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) non registrato e lo si specifica come argomento dello script nell'oggetto ScriptRunConfig per il training. Se si vuole riusare questo TabularDataset con altri esperimenti nell'area di lavoro, vedere [come registrare i set di impostazioni nell'area di lavoro](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Creare un TabularDataset

Il codice seguente crea un TabularDataset non registrato da un URL Web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Gli oggetti TabularDataset offrono la possibilità di caricare i dati nel TabularDataset in un frame di dati Pandas o Spark, in modo da poter lavorare con le librerie di preparazione e formazione dei dati familiari senza dover uscire dal notebook.

### <a name="access-dataset-in-training-script"></a>Accedere al set di dati nello script di training

Il codice seguente configura un argomento dello script `--input-data` che verrà specificato quando si configura l'esecuzione del training (vedere la sezione successiva). Quando il set di dati tabulare viene passato come valore dell'argomento, Azure ML lo risolverà in ID del set di dati, che sarà quindi possibile usare per accedere al set di dati nello script di training (senza impostare come hardcoded il nome o l'ID del set di dati nello script). USA quindi il [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metodo per caricare il set di dati in un frame di dati Pandas per eseguire ulteriori operazioni di esplorazione e preparazione dei dati prima del training.

> [!Note]
> Se l'origine dati originale contiene NaN, stringhe vuote o valori vuoti, quando si usa `to_pandas_dataframe()` , questi valori vengono sostituiti come valore *null* .

Se è necessario caricare i dati preparati in un nuovo set di dati da un dataframe Pandas in memoria, scrivere i dati in un file locale, ad esempio un parquet, e creare un nuovo set di dati da tale file. È anche possibile creare set di dati da file o percorsi locali negli archivi dati. Altre informazioni su [come creare set di impostazioni](how-to-create-register-datasets.md).

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Configurare l'esecuzione del training
Un oggetto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun?preserve-view=true&view=azure-ml-py) viene usato per configurare e inviare l'esecuzione del training.

Questo codice crea un oggetto ScriptRunConfig, `src` , che specifica

* Directory di script per gli script. Tutti i file in questa directory vengono caricati nei nodi del cluster per l'esecuzione.
* Script di training, *train_titanic. py*.
* Set di dati di input per il training, `titanic_ds` , come argomento dello script. Azure ML lo risolverà nell'ID corrispondente del set di dati quando viene passato allo script.
* Destinazione di calcolo per l'esecuzione.
* Ambiente per l'esecuzione.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-dataset', titanic_ds],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Montare i file nelle destinazioni di calcolo Remote

Se si dispone di dati non strutturati, creare un [filedataset](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py) e montare o scaricare i file di dati per renderli disponibili per la destinazione di calcolo remota per il training. Informazioni su quando usare [Mount vs. download](#mount-vs-download) per gli esperimenti di training remoto. 

Nell'esempio seguente viene creato un oggetto filedataset e il set di dati viene montato nella destinazione di calcolo passandolo come argomento allo script di training. 

> [!Note]
> Se si usa un'immagine di base Docker personalizzata, è necessario installare fuse tramite `apt-get install -y fuse` come dipendenza per il funzionamento del montaggio del set di dati. Informazioni su come [creare un'immagine di compilazione personalizzata](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Creare un oggetto FileDataset

Nell'esempio seguente viene creato un oggetto filedataset non registrato da URL Web. Altre informazioni su [come creare set di impostazioni](how-to-create-register-datasets.md) da altre origini.

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

### <a name="configure-the-training-run"></a>Configurare l'esecuzione del training
È consigliabile passare il set di dati come argomento durante il montaggio tramite il `arguments` parametro del `ScriptRunConfig` costruttore. In questo modo si otterrà il percorso dei dati (punto di montaggio) nello script di training tramite argomenti. In questo modo, sarà possibile usare lo stesso script di training per il debug locale e il training remoto in qualsiasi piattaforma cloud.

Nell'esempio seguente viene creato un ScriptRunConfig che passa il filedataset tramite `arguments` . Dopo aver inviato l'esecuzione, i file di dati a cui fa riferimento il set `mnist_ds` di dati verranno montati nella destinazione di calcolo.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
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

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
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

Quando si **monta** un set di dati, si collegano i file a cui fa riferimento il set di dati a una directory (punto di montaggio) e lo si rende disponibile nella destinazione di calcolo. Il montaggio è supportato per i calcoli basati su Linux, tra cui Azure Machine Learning calcolo, macchine virtuali e HDInsight. 

Quando si **Scarica** un set di dati, tutti i file a cui fa riferimento il set di dati verranno scaricati nella destinazione di calcolo. Il download è supportato per tutti i tipi di calcolo. 

Se lo script elabora tutti i file a cui fa riferimento il set di dati e il disco di calcolo può adattarsi al set di dati completo, è consigliabile scaricare il download per evitare il sovraccarico del flusso dei dati dai servizi di archiviazione. Se le dimensioni dei dati superano le dimensioni del disco di calcolo, il download non è possibile. Per questo scenario, è consigliabile montare poiché solo i file di dati usati dallo script vengono caricati al momento dell'elaborazione.

Il codice seguente viene montato `dataset` sulla directory Temp in `mounted_path`

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

## <a name="directly-access-datasets-in-your-script"></a>Accedere direttamente ai set di impostazioni nello script

I set di impostazioni registrati sono accessibili sia localmente che in remoto nei cluster di calcolo, ad esempio il calcolo Azure Machine Learning. Per accedere al set di dati registrato tra gli esperimenti, usare il codice seguente per accedere all'area di lavoro e al set di dati registrato in base al nome. Per impostazione predefinita, il [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) metodo della `Dataset` classe restituisce la versione più recente del set di dati registrato con l'area di lavoro.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>Accesso al codice sorgente durante il training

Archiviazione BLOB di Azure offre velocità effettive più elevate rispetto a una condivisione file di Azure e la scalabilità per avviare in parallelo un numero elevato di processi. Per questo motivo, è consigliabile configurare le esecuzioni in modo da usare l'archiviazione BLOB per il trasferimento di file di codice sorgente.

L'esempio di codice seguente specifica nella configurazione di esecuzione quale archivio dati BLOB usare per i trasferimenti del codice sorgente.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Esempi di notebook

+ I [notebook del set di dati](https://aka.ms/dataset-tutorial) dimostrano ed espandono i concetti in questo articolo.
+ Vedere How to [parametize DataSets nelle pipeline ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il [training automatico dei modelli di Machine Learning](how-to-auto-train-remote.md) con TabularDatasets.

* Eseguire il [training dei modelli di classificazione delle immagini](https://aka.ms/filedataset-samplenotebook) con i set di dati.

* Eseguire il [training con i set di impostazioni usando le pipeline](how-to-create-your-first-pipeline.md).
