---
title: Creare set di dati Azure Machine Learning per accedere ai dati
titleSuffix: Azure Machine Learning
description: Informazioni su come creare set di dati di Azure Machine Learning per accedere ai dati per le esecuzioni dell'esperimento di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 50728363ffd02e189b4bc0dacebd684a7f13091e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74030703"
---
# <a name="create-azure-machine-learning-datasets"></a>Crea set di impostazioni Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come creare set di dati di Azure Machine Learning per accedere ai dati per gli esperimenti locali o remoti.

Con Azure Machine Learning set di impostazioni è possibile:

* Conservare una sola copia dei dati nella risorsa di archiviazione, a cui fanno riferimento i set di dati.

* Accesso facile ai dati durante il training del modello senza doversi preoccupare di stringhe di connessione o percorsi di dati.

* Condividere i dati e collaborare con altri utenti.

## <a name="prerequisites"></a>prerequisiti

Per creare e usare i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non si ha un account, creare un account gratuito prima di iniziare. Prova la [versione gratuita o a pagamento del Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-DataSets.

> [!NOTE]
> Alcune classi del set di dati presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="dataset-types"></a>Tipi di set di dati

Esistono due tipi di set di dati, in base al modo in cui gli utenti li utilizzano nel training:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. Questo consente di materializzare i dati in un frame di dati Pandas o Spark. È possibile creare un oggetto `TabularDataset` da file CSV, TSV e parquet e dai risultati della query SQL. Per un elenco completo, vedere la [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

* La classe [filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. Con questo metodo è possibile scaricare o montare i file nel calcolo come oggetto filedataset. I file possono essere in qualsiasi formato, consentendo una gamma più ampia di scenari di apprendimento automatico, tra cui l'apprendimento avanzato.

Per altre informazioni sulle modifiche future dell'API, vedere [avviso di modifica dell'API del set di dati](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Creare i set di dati

Creando un set di dati, si crea un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. Poiché i dati rimangono nella posizione esistente, non viene addebitato alcun costo aggiuntivo per l'archiviazione. È possibile creare set di dati sia `TabularDataset` che `FileDataset` tramite Python SDK o la pagina di destinazione dell'area di lavoro (anteprima).

Affinché i dati siano accessibili da parte di Azure Machine Learning, è necessario creare i set di dati da percorsi in [archivi dati di Azure](how-to-access-data.md) o URL Web pubblici.

### <a name="use-the-sdk"></a>Usare l'SDK

Per creare set di dati da un [archivio dati di Azure](how-to-access-data.md) usando Python SDK:

1. Verificare di avere `contributor` o `owner` accedere all'archivio dati di Azure registrato.

1. Creare il set di dati facendo riferimento a un percorso nell'archivio dati:

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

#### <a name="create-a-tabulardataset"></a>Creare un TabularDataset

È possibile creare TabularDatasets tramite l'SDK o usando Azure Machine Learning Studio. È possibile specificare un timestamp da una colonna nei dati o dal modello di percorso in cui sono archiviati i dati per abilitare un tratto della serie temporale. Questa specifica consente un filtro semplice ed efficiente in base al tempo.

Usare il metodo [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) sulla classe `TabularDatasetFactory` per leggere i file in formato CSV o TSV e per creare un TabularDataset non registrato. Se si esegue la lettura da più file, i risultati verranno aggregati in una rappresentazione tabulare.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Per impostazione predefinita, quando si crea un TabularDataset, i tipi di dati della colonna vengono dedotti automaticamente. Se i tipi dedotti non corrispondono alle aspettative, è possibile specificare i tipi di colonna usando il codice seguente. Sono inoltre [disponibili altre informazioni sui tipi di dati supportati](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Rimasti|Pclass|Nome|Sesso|Age|SibSp|Parch|Ticket|Tariffe|Abitacolo|Intrapreso
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|true|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|true|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7,9250||S

Usare il metodo [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) sulla classe `TabularDatasetFactory` per leggere dal database SQL di Azure:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

In TabularDatasets è possibile specificare un timestamp da una colonna nei dati o da qualsiasi posizione in cui vengono archiviati i dati del modello di percorso per abilitare un tratto della serie temporale. Questa specifica consente un filtro semplice ed efficiente in base al tempo.

Usare il metodo [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) sulla classe`TabularDataset` per specificare la colonna timestamp e per abilitare il filtro in base all'ora. Per altre informazioni, vedere la [Demo sull'API relativa alle serie temporali tabulari con i dati meteo NOAA](https://aka.ms/azureml-tsd-notebook).

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

#### <a name="create-a-filedataset"></a>Creare un oggetto FileDataset

Utilizzare il metodo [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) sulla classe `FileDatasetFactory` per caricare file in qualsiasi formato e per creare un oggetto filedataset non registrato:

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [
                  (datastore, 'animals')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Nel Web 
I passaggi e l'animazione seguenti illustrano come creare un set di dati in Azure Machine Learning Studio, https://ml.azure.com.

![Creare un set di dati con l'interfaccia utente](media/how-to-create-register-datasets/create-dataset-ui.gif)

Per creare un set di dati in studio:
1. Accedere al https://ml.azure.com.
1. Selezionare **set di impostazioni** nella sezione **Asset** del riquadro sinistro. 
1. Selezionare **Crea set di dati** per scegliere l'origine del set di dati. Questa origine può essere costituita da file locali, da un archivio dati o da URL pubblici.
1. Selezionare **tabulare** o **file** per tipo di set di dati.
1. Selezionare **Avanti** per esaminare i moduli **Impostazioni e anteprima**, **schema** e **Conferma dettagli** ; vengono popolate in modo intelligente in base al tipo di file. Usare questi moduli per verificare le selezioni e per configurare ulteriormente il set di dati prima della creazione.  
1. Selezionare **Crea** per completare la creazione del set di dati.

## <a name="register-datasets"></a>Registrare i set di impostazioni

Per completare il processo di creazione, registrare i set di impostazioni con un'area di lavoro. Usare il metodo [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) per registrare i set di elementi con l'area di lavoro per condividerli con altri utenti e riutilizzarli in diversi esperimenti:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> I set di impostazioni creati tramite Azure Machine Learning Studio vengono registrati automaticamente nell'area di lavoro.

## <a name="create-datasets-with-azure-open-datasets"></a>Creare set di impostazioni con i set di impostazioni di Azure Open

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) include set di dati pubblici curati che è possibile usare per aggiungere caratteristiche specifiche dello scenario alle soluzioni di Machine Learning e realizzare modelli più accurati. I set di dati includono dati di pubblico dominio relativi a meteo, censimento, festività, sicurezza pubblica e posizione, che consentono di eseguire il training di modelli di Machine Learning e arricchire le soluzioni predittive. I set di impostazioni aperti si trovano nel cloud in Microsoft Azure e sono inclusi sia nell'SDK che nell'interfaccia utente dell'area di lavoro.

### <a name="use-the-sdk"></a>Usare l'SDK

Per creare set di impostazioni con Azure Open DataSets dall'SDK, verificare che il pacchetto sia stato installato con `pip install azureml-opendatasets`. Ogni set di dati discreto è rappresentato dalla propria classe nell'SDK e alcune classi sono disponibili come `TabularDataset`, `FileDataset`o entrambi. Per un elenco completo delle classi, vedere la [documentazione di riferimento](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

La maggior parte delle classi ereditano da e restituiscono un'istanza di `TabularDataset`. Esempi di queste classi sono `PublicHolidays`, `BostonSafety`e `UsPopulationZip`. Per creare un `TabularDataset` da questi tipi di classi, usare il costruttore senza argomenti. Quando si registra un set di dati creato da set di dati aperti, non vengono scaricati immediatamente dati, ma i dati saranno accessibili in un secondo momento, quando richiesto (durante il training, ad esempio) da una posizione di archiviazione centrale. 

```python
from azureml.opendatasets import UsPopulationZip

tabular_dataset = UsPopulationZip()
tabular_dataset = tabular_dataset.register(workspace=workspace, name="pop data", description="US population data by zip code")
```

È possibile recuperare determinate classi come un `TabularDataset` o `FileDataset`, che consente di modificare e/o scaricare direttamente i file. Altre classi possono ottenere un set di dati solo usando le funzioni `get_tabular_dataset()` o `get_file_dataset()`. Nell'esempio di codice seguente vengono illustrati alcuni esempi di questi tipi di classi:

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

### <a name="use-the-ui"></a>Usare l'interfaccia utente

È anche possibile creare set di impostazioni da classi Open DataSets tramite l'interfaccia utente. Nell'area di lavoro selezionare la scheda **set** di **elementi in asset**. Nel menu a discesa **Crea set di dati** selezionare **da set di dati aperti**.

![Aprire il set di dati con l'interfaccia utente](media/how-to-create-register-datasets/open-datasets-1.png)

Selezionare un set di dati selezionando il relativo riquadro. È possibile filtrare usando la barra di ricerca. Selezionare **Avanti**.

![Scegliere il set di dati](media/how-to-create-register-datasets/open-datasets-2.png)

Scegliere un nome con cui registrare il set di dati e, facoltativamente, filtrare i dati usando i filtri disponibili. In questo caso, per il set di dati Public Holidays è possibile filtrare il periodo di tempo a un anno e il codice paese solo negli Stati Uniti. Selezionare **Create**.

![Imposta parametri set di dati e crea set di dati](media/how-to-create-register-datasets/open-datasets-3.png)

Il set di dati è ora disponibile nell'area di lavoro in **set**di dati. È possibile usarlo nello stesso modo degli altri set di impostazioni creati.

## <a name="version-datasets"></a>Set di impostazioni di versione

È possibile registrare un nuovo set di dati con lo stesso nome creando una nuova versione. Una versione del set di dati è un modo per aggiungere un segnalibro allo stato dei dati in modo che sia possibile applicare una versione specifica del set di dati per la sperimentazione o la riproduzione futura. Altre informazioni sulle [versioni del set di dati](how-to-version-track-datasets.md).
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

I set di impostazioni registrati sono accessibili sia localmente che in remoto nei cluster di calcolo, ad esempio il calcolo Azure Machine Learning. Per accedere al set di dati registrato tra gli esperimenti, usare il codice seguente per accedere all'area di lavoro e al set di dati registrato in base al nome. Per impostazione predefinita, il metodo [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) sulla classe `Dataset` restituisce la versione più recente del set di dati registrato con l'area di lavoro.

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
* Per altri esempi di training del set di dati, vedere i [notebook di esempio](https://aka.ms/dataset-tutorial).
