---
title: Creare set di dati di Azure Machine Learning per accedere ai datiCreate Azure Machine Learning datasets to access data
titleSuffix: Azure Machine Learning
description: Informazioni su come creare set di dati di Azure Machine Learning per accedere ai dati per le esecuzioni dell'esperimento di Machine Learning.Learn how to create Azure Machine Learning datasets to access your data for machine learning experiment runs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: f02046d1e2ee558ca4ea4472a03fddb5d0a6a16f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549317"
---
# <a name="create-azure-machine-learning-datasets"></a>Creare set di dati di Azure Machine LearningCreate Azure Machine Learning datasets

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come creare set di dati di Azure Machine Learning per accedere ai dati per gli esperimenti locali o remoti.

Con i set di dati di Azure Machine Learning è possibile:With Azure Machine Learning datasets, you can:

* Mantenere una singola copia dei dati nell'archivio, a cui fanno riferimento i set di dati.

* Accedere ai dati senza problemi durante il training del modello senza preoccuparsi delle stringhe di connessione o dei percorsi di dati.

* Condividi i dati e collabora con altri utenti.

## <a name="prerequisites"></a>Prerequisiti
' Per creare e utilizzare i set di dati, è necessario:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Area di lavoro di [Azure Machine Learning](how-to-manage-workspace.md).

* [Azure Machine Learning SDK per Python è installato,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)che include il pacchetto azureml-datasets.

> [!NOTE]
> Alcune classi di set di dati hanno dipendenze nel pacchetto [azureml-dataprep.Some](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) dataset classes have dependencies on the azureml-dataprep package. Per gli utenti Linux, queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="compute-size-guidance"></a>Indicazioni sulle dimensioni di calcolo

Quando si crea un set di dati esaminare la potenza di elaborazione del calcolo e le dimensioni dei dati in memoria. La dimensione dei dati nell'archivio non corrisponde alla dimensione dei dati in un frame di dati. Ad esempio, i dati nei file CSV possono espandersi fino a 10 volte in un frame di dati, quindi un file CSV da 1 GB può diventare 10 GB in un frame di dati. 

Il fattore principale è la dimensione del set di dati in memoria, ovvero come frame di dati. È consigliabile che le dimensioni di elaborazione e la potenza di elaborazione contengano 2 volte la dimensione della RAM. Pertanto, se il frame di dati è di 10 GB, si desidera una destinazione di calcolo con 20 GB di RAM per garantire che il frame di dati possa adattarsi comodamente alla memoria ed essere elaborato. Se i dati sono compressi, può espandersi ulteriormente; 20 GB di dati relativamente sparsi memorizzati in formato parquet compresso possono espandersi fino a 800 GB di memoria. Dal momento che i file parquet memorizzano i dati in un formato a colonne, se hai bisogno solo della metà delle colonne, allora devi caricare solo 400 GB di memoria.
 
Se si utilizza Panda, non c'è motivo di avere più di 1 vCPU dal momento che è tutto quello che userà. È possibile eseguire facilmente il parallelize a molte vCPU in una singola istanza/nodo di calcolo di Azure Machine Learning `import pandas as pd` `import modin.pandas as pd`tramite Modin e Dask/Ray e scalare orizzontalmente in un cluster di grandi dimensioni, se necessario, semplicemente passando a . 
 
Se non è possibile ottenere un abbastanza grande virtuale per i dati, sono disponibili due opzioni: utilizzare un framework come Spark o Dask per eseguire l'elaborazione sui dati 'memoria esaurita', vale a dire il frame di dati viene caricato nella partizione RAM per partizione ed elaborato, con il risultato finale viene raccolto alla fine. Se questo è troppo lento, Spark o Dask consentono di scalare orizzontalmente a un cluster che può ancora essere utilizzato in modo interattivo. 

## <a name="dataset-types"></a>Tipi di set di dati

Esistono due tipi di set di dati, in base al modo in cui gli utenti li utilizzano nel training:There are two dataset types, based on how users consume them in training:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. In questo modo è possibile materializzare i dati in un oggetto dati Pandas o Spark.This provides you with the ability to materialze the data into a Pandas or Spark DataFrame. È possibile `TabularDataset` creare un oggetto da .csv, .tsv, .parquet, file .jsonl e dai risultati della query SQL. Per un elenco completo, vedere [Classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

* La classe [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. Con questo metodo, è possibile scaricare o montare i file per il calcolo come un FileDataset oggetto. I file possono essere in qualsiasi formato, il che consente una più ampia gamma di scenari di apprendimento automatico, incluso il deep learning.

Per altre informazioni sulle modifiche imminenti all'API, vedere Avviso di [modifica dell'API del set di](https://aka.ms/tabular-dataset)dati .

## <a name="create-datasets"></a>Creare set di dati

Creando un set di dati, si crea un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. Poiché i dati rimangono nella posizione esistente, non è previsto alcun costo di archiviazione aggiuntivo. È possibile `TabularDataset` creare `FileDataset` entrambi i set di https://ml.azure.comdati utilizzando Python SDK o at .

Affinché i dati siano accessibili da Azure Machine Learning, i set di dati devono essere creati da percorsi in [archivi dati](how-to-access-data.md) di Azure o URL Web pubblici. 

### <a name="use-the-sdk"></a>Utilizzare l'SDK

Per creare set di dati da un archivio dati di Azure usando Python SDK:To create datasets from an [Azure datastore](how-to-access-data.md) by using the Python SDK:

1. Verificare di `contributor` `owner` avere o di avere accesso all'archivio dati di Azure registrato.

2. Creare il set di dati facendo riferimento a i percorsi nell'archivio dati.
> [!Note]
> È possibile creare un set di dati da più percorsi in più datastore. Non esiste alcun limite rigido al numero di file o alle dimensioni dei dati da cui è possibile creare un set di dati. Tuttavia, per ogni percorso dati, verranno inviate alcune richieste al servizio di archiviazione per verificare se punta a un file o a una cartella. Questo sovraccarico può causare un peggioramento delle prestazioni o un errore. Un set di dati che fa riferimento a una cartella con 1000 file all'interno viene considerato che fa riferimento a un percorso dati. È consigliabile creare un set di dati che fa riferimento a meno di 100 percorsi nei datastore per ottenere prestazioni ottimali.

#### <a name="create-a-tabulardataset"></a>Creare un tabulareCreate a TabularDataset

Utilizzare [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) il metodo `TabularDatasetFactory` sulla classe per leggere i file in formato csv o tsv e per creare un TabularDataset non registrato. Se si sta leggendo da più file, i risultati verranno aggregati in un'unica rappresentazione tabulare. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Per impostazione predefinita, quando si crea un TabularDataset, i tipi di dati di colonna vengono dedotti automaticamente. Se i tipi dedotti non corrispondono alle aspettative, è possibile specificare i tipi di colonna utilizzando il codice seguente. Il `infer_column_type` parametro è applicabile solo ai set di dati creati da file delimitati. Ulteriori [informazioni sui tipi di dati supportati](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)sono disponibili.

> [!IMPORTANT] 
> Se l'archiviazione è protetto da una rete virtuale o da un firewall, è supportata solo la creazione di un set di dati tramite l'SDK. Per creare il set di dati, assicurarsi di includere i parametri `validate=False` e `infer_column_types=False` nel `from_delimited_files()` metodo. In questo modo viene ignorato il controllo di convalida iniziale e viene garantito che è possibile creare il set di dati da questi file protetti. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |Id Passeggero|Survived|Pclasse|Nome|Sesso|Age|SibSp|Perarca|Ticket|Tariffe|Cabina|Intrapreso
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, signor Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, la signora John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599 (informazioni in base al sistema PC 17599)|71.2833|C85|C
2|3|True|3|Heikkinen, signorina. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S


Per creare un set di dati da un frame di dati pandas in memoria, scrivere i dati in un file locale, ad esempio un file csv, e creare il set di dati da tale file. Il codice seguente illustra questo flusso di lavoro.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

Usare [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) il metodo `TabularDatasetFactory` nella classe per leggere dal database SQL di Azure:Use the method on the class to read from Azure SQL Database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

In TabularDatasets è possibile specificare un timestamp da una colonna nei dati o dalla posizione in cui vengono archiviati i dati del modello di percorso per abilitare un tratto di serie temporali. Questa specifica consente di filtrare in modo semplice ed efficiente in base al tempo.

Utilizzare [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) il metodo`TabularDataset` sulla classe per specificare la colonna timestamp e per abilitare il filtro in base all'ora. Per ulteriori informazioni, vedere [Demo dell'API relativa alle serie temporali tabulare con i dati meteo NOAA](https://aka.ms/azureml-tsd-notebook).

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

Utilizzare [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) il metodo `FileDatasetFactory` sulla classe per caricare i file in qualsiasi formato e per creare un FileDataset non registrato. Se l'archiviazione è protetto da una rete `validate =False` virtuale `from_files()` o da un firewall, impostare il parametro nel metodo. In questo modo viene ignorato il passaggio di convalida iniziale e viene garantito che è possibile creare il set di dati da questi file protetti.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Sul Web 
I passaggi e l'animazione seguenti illustrano come https://ml.azure.comcreare un set di dati in Azure Machine Learning Studio.

![Creare un set di dati con l'interfaccia utenteCreate a dataset with the UI](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Per creare un set di dati nello studio:
1. Accedere a https://ml.azure.com.
1. Selezionare **Set di dati** nella sezione **Asset** del riquadro sinistro. 
1. Selezionare **Crea set di dati** per scegliere l'origine del set di dati. Questa origine può essere file locali, un archivio dati o URL pubblici.
1. Selezionare **Tabulare** o **File** per Tipo di set di dati.
1. Selezionare **Avanti** per aprire il modulo **Datastore e selezione file.** In questo modulo è possibile selezionare la posizione in cui mantenere il set di dati dopo la creazione, nonché selezionare i file di dati da utilizzare per il set di dati. 
1. Selezionare **Avanti** per popolare i moduli **Impostazioni e Anteprima** e **Schema;** vengono popolati in modo intelligente in base al tipo di file ed è possibile configurare ulteriormente il set di dati prima della creazione in questi moduli. 
1. Selezionare **Avanti** per esaminare il modulo **Conferma dettagli.** Controllare le selezioni e creare un profilo dati facoltativo per il set di dati. Altre informazioni sulla [profilatura dei dati](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Selezionare **Crea** per completare la creazione del set di dati.

## <a name="register-datasets"></a>Registrare i set di datiRegister datasets

Per completare il processo di creazione, registrare i set di dati con un'area di lavoro. Utilizzare [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) il metodo per registrare i set di dati con l'area di lavoro per condividerli con altri utenti e riutilizzarli in vari esperimenti:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> I set di dati creati tramite Azure Machine Learning Studio vengono registrati automaticamente nell'area di lavoro.

## <a name="create-datasets-with-azure-open-datasets"></a>Creare set di dati con i set di dati aperti di AzureCreate datasets with Azure Open Datasets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) include set di dati pubblici curati che è possibile usare per aggiungere caratteristiche specifiche dello scenario alle soluzioni di Machine Learning e realizzare modelli più accurati. I set di dati includono dati di pubblico dominio relativi a meteo, censimento, festività, sicurezza pubblica e posizione, che consentono di eseguire il training di modelli di Machine Learning e arricchire le soluzioni predittive. I set di dati aperti si trovano nel cloud in Microsoft Azure e sono inclusi sia nell'SDK che nell'interfaccia utente dell'area di lavoro.

### <a name="use-the-sdk"></a>Utilizzare l'SDK

Per creare set di dati con Azure Open Dataset dall'SDK, `pip install azureml-opendatasets`assicurarsi di aver installato il pacchetto con . Ogni set di dati discreto è rappresentato dalla propria classe nell'SDK e alcune classi sono disponibili come `TabularDataset`, `FileDataset`, o entrambe. Vedere la [documentazione](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) di riferimento per un elenco completo delle classi.

È possibile recuperare alcune `TabularDataset` classi `FileDataset`come un o , che consente di manipolare e/ o scaricare i file direttamente. Altre classi possono **only** ottenere un set `get_tabular_dataset()` `get_file_dataset()` di dati solo utilizzando una o funzione. Nell'esempio di codice riportato di seguito vengono illustrati alcuni esempi di questi tipi di classi.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Quando si registra un set di dati creato da Open Datasets, non viene scaricato alcun dato immediatamente, ma i dati saranno accessibili in un secondo momento quando richiesto (durante il training, ad esempio) da una posizione di archiviazione centrale.

### <a name="use-the-ui"></a>Usare l'interfaccia utenteUse the UI

È anche possibile creare dataset dalle classi Open Datasets tramite l'interfaccia utente. Nell'area di lavoro selezionare la scheda **Set di dati** in **Asset**. Nel menu a discesa **Crea set di dati** selezionare Da set di dati **aperti**.

![Aprire il set di dati con l'interfaccia utenteOpen Dataset with the UI](./media/how-to-create-register-datasets/open-datasets-1.png)

Selezionare un set di dati selezionando il relativo riquadro. È possibile filtrare utilizzando la barra di ricerca. Selezionare **Avanti**.

![Scegli set di dati](./media/how-to-create-register-datasets/open-datasets-2.png)

Scegliere un nome in un quale registrare il set di dati e facoltativamente filtrare i dati utilizzando i filtri disponibili. In questo caso, per il set di dati giorni festivi, si filtra il periodo di tempo a un anno e il codice paese solo agli Stati Uniti. Selezionare **Create** (Crea).

![Impostare i parametri del set di dati e creare il set di datiSet dataset params and create dataset](./media/how-to-create-register-datasets/open-datasets-3.png)

Il set di dati è ora disponibile nell'area di lavoro in **Set di dati**. È possibile utilizzarlo nello stesso modo di altri set di dati creati.

## <a name="version-datasets"></a>Set di dati di versione

È possibile registrare un nuovo set di dati con lo stesso nome creando una nuova versione. Una versione del set di dati è un modo per aggiungere un segnalibro allo stato dei dati in modo da poter applicare una versione specifica del set di dati per la sperimentazione o la riproduzione futura. Ulteriori informazioni sulle versioni dei [dataset](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>Accedere ai set di dati nello scriptAccess datasets in your script

I set di dati registrati sono accessibili in locale e in remoto nei cluster di calcolo, ad esempio il calcolo di Azure Machine Learning.Registered datasets are accessible both locally and remotely on compute clusters like the Azure Machine Learning compute. Per accedere al set di dati registrato tra gli esperimenti, usare il codice seguente per accedere all'area di lavoro e al set di dati registrato in base al nome. Per impostazione [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) predefinita, `Dataset` il metodo nella classe restituisce la versione più recente del set di dati registrato nell'area di lavoro.

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

* Informazioni su [come eseguire il training con i set di dati.](how-to-train-with-datasets.md)
* Usare l'apprendimento automatico automatico per eseguire il [training con TabularDatasets](https://aka.ms/automl-dataset).
* Per altri esempi di training sui set di dati, vedere [i blocchi appunti di esempio.](https://aka.ms/dataset-tutorial)
