---
title: Creare set di dati per accedere ai dati con azureml-DataSets
titleSuffix: Azure Machine Learning
description: Informazioni su come creare set di DataSet da varie origini e registrare set di impostazioni con l'area di lavoro
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 10/10/2019
ms.openlocfilehash: a558658d7c853560f0939c99dc5dce739d985944
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900707"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Creare e accedere ai set di impostazioni (anteprima) in Azure Machine Learning

In questo articolo si apprenderà come creare set di dati Azure Machine Learning (anteprima) e come accedere ai dati da esperimenti locali o remoti.

Con Azure Machine Learning set di impostazioni è possibile:

* **Conservare una sola copia dei dati nella risorsa di archiviazione a** cui fanno riferimento i set di dati.

* **Accedere facilmente ai dati durante il training del modello** senza doversi preoccupare delle stringhe di connessione o dei percorsi dati.

* **Condividere i dati & collaborare** con altri utenti.

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md)

* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-DataSets.

> [!Note]
> Alcune classi del set di dati (anteprima) presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="dataset-types"></a>Tipi di set di dati

I set di impostazioni sono suddivisi in due tipi in base al modo in cui gli utenti li utilizzano nel training.

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. Questo consente di materializzare i dati in un frame di dati Pandas o Spark. È possibile creare un oggetto `TabularDataset` da file CSV, TSV, parquet, risultati della query SQL e così via. Per un elenco completo, consultare la [documentazione](https://aka.ms/tabulardataset-api-reference).

* [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. Questo consente di scaricare o montare i file nel calcolo. I file possono avere qualsiasi formato, che consente una gamma più ampia di scenari di apprendimento automatico, tra cui l'apprendimento avanzato.

Per ulteriori informazioni sulle modifiche future dell'API, vedere [qui](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Creare set di dati

Creando un set di dati, si crea un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. I dati rimangono nell'attuale posizione, quindi non si incorre in costi aggiuntivi di archiviazione. È possibile creare sia TabularDatasets che filedatasets mediante Python SDK o la pagina di destinazione dell'area di lavoro (anteprima). 

Affinché i dati siano accessibili da parte di Azure Machine Learning, è necessario creare i set di dati da percorsi in [archivi dati di Azure](how-to-access-data.md) o URL Web pubblici.

### <a name="using-the-sdk"></a>Uso dell'SDK

Per creare set di dati da un [archivio dati di Azure](how-to-access-data.md) usando Python SDK:

* Verificare di avere `contributor` o `owner` accedere all'archivio dati di Azure registrato.

* Creare il set di dati facendo riferimento a un percorso nell'archivio dati.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
#### <a name="create-tabulardatasets"></a>Crea TabularDatasets

Usare il metodo [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) sulla classe `TabularDatasetFactory` per leggere i file in formato CSV o TSV e creare un TabularDataset non registrato. Se si esegue la lettura da più file, i risultati verranno aggregati in una rappresentazione tabulare.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Rimasti|Pclass|name|Sesso|Età|SibSp|Parch|Ticket|Tariffe|Abitacolo|Intrapreso
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|1|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|1|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7,9250||S

Usare il metodo [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) sulla classe `TabularDatasetFactory` per leggere dal database SQL di Azure.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

In TabularDatasets è possibile specificare un timestamp da una colonna nei dati oppure i dati del modello di percorso vengono archiviati in per abilitare un tratto della serie temporale, che consente un filtro semplice ed efficiente in base al tempo.

Usare il metodo [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) sulla classe `TabularDataset` per specificare la colonna timestamp e abilitare il filtro in base all'ora. Altri esempi e dettagli sono disponibili [qui](https://aka.ms/azureml-tsd-notebook).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-filedatasets"></a>Crea set di dati

Utilizzare il metodo [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) sulla classe `FileDatasetFactory` per caricare file in qualsiasi formato e creare un oggetto filedataset non registrato.

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="using-the-workspace-landing-page"></a>Uso della pagina di destinazione dell'area di lavoro

Accedere alla pagina di [destinazione dell'area di lavoro](https://ml.azure.com) per creare un set di dati tramite l'esperienza Web. La pagina di destinazione dell'area di lavoro supporta la creazione di TabularDatasets e filedataset.

Nell'animazione seguente viene illustrato come creare un set di dati nella pagina di destinazione dell'area di lavoro.

Per prima cosa, selezionare **set di impostazioni** nella sezione **Asset** del riquadro sinistro. Selezionare quindi **+ Crea set di dati** per scegliere l'origine del set di dati. può provenire da file locali, archivio dati o URL Web pubblici. Selezionare il **tipo di set di dati**: * tabulare o file. Le **Impostazioni e l'anteprima** e i moduli **dello schema** vengono popolati in modo intelligente in base al tipo di file. Selezionare **Avanti** per esaminarli o per configurare ulteriormente il set di dati prima della creazione. Selezionare **fine** per completare la creazione del set di dati.

![Creare un set di dati con l'interfaccia utente](media/how-to-create-register-datasets/create-dataset-ui.gif)

## <a name="register-datasets"></a>Registrare i set di impostazioni

Per completare il processo di creazione, registrare i set di impostazioni con un'area di lavoro.

Usare il metodo [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) per registrare i set di elementi con l'area di lavoro in modo che possano essere condivisi con altri utenti e riutilizzati in diversi esperimenti.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> I set di impostazioni creati tramite la pagina di destinazione dell'area di lavoro vengono registrati automaticamente nell'area di lavoro.

## <a name="version-datasets"></a>Set di impostazioni di versione

È possibile registrare un nuovo set di dati con lo stesso nome creando una nuova versione. La versione del set di dati è un modo per aggiungere segnalibri allo stato dei dati, in modo da poter applicare una versione specifica del set di dati per la sperimentazione o la riproduzione futura. Altre informazioni sulle [versioni del set di dati](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-datasets-in-your-script"></a>Accedere ai set di impostazioni nello script

I set di impostazioni registrati sono accessibili in locale e in remoto nei cluster di calcolo come il Azure Machine Learning calcolo. Per accedere al set di dati registrato tra gli esperimenti, usare il codice seguente per ottenere l'area di lavoro e il set di dati registrato in base al nome. Per impostazione predefinita, il metodo [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) sulla classe `Dataset` restituisce la versione più recente del set di dati registrato con l'area di lavoro.

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

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come eseguire il training con i set di impostazioni](how-to-train-with-datasets.md).
* USA Machine Learning automatizzato per eseguire il [training con TabularDatasets](https://aka.ms/automl-dataset).
* Per altri esempi di training con i set di impostazioni, vedere i [notebook di esempio](https://aka.ms/dataset-tutorial).
