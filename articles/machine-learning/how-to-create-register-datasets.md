---
title: Creare set di dati Azure Machine Learning per accedere ai dati
titleSuffix: Azure Machine Learning
description: Informazioni su come creare set di dati di Azure Machine Learning per accedere ai dati per le esecuzioni dell'esperimento di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 51e31977a5f31777252920f9cd18bf069bad6d1e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507196"
---
# <a name="create-azure-machine-learning-datasets"></a>Creare set di dati di Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come creare set di dati di Azure Machine Learning per accedere ai dati per gli esperimenti locali o remoti. Per informazioni sul modo in cui i set di dati si adattano al flusso di lavoro di accesso ai dati complessivo di Azure Machine Learning, vedere l'articolo relativo ai [dati](concept-data.md#data-workflow) di

Creando un set di dati, si crea un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. Poiché i dati rimangono nella posizione esistente, non viene addebitato alcun costo aggiuntivo per l'archiviazione e l'integrità delle origini dati non è rischiosa. Inoltre, i set di impostazioni vengono valutati in modo differito, che facilitano le prestazioni del flusso È possibile creare set di dati da archivi dati, URL pubblici e set di dati [aperti di Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Con Azure Machine Learning set di impostazioni è possibile:

* Conservare una sola copia dei dati nella risorsa di archiviazione, a cui fanno riferimento i set di dati.

* Accesso facile ai dati durante il training del modello senza doversi preoccupare di stringhe di connessione o percorsi di dati. [Altre informazioni su come eseguire il training con i set di impostazioni](how-to-train-with-datasets.md).

* Condividere i dati e collaborare con altri utenti.

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-DataSets.

    * Creare un' [istanza di calcolo di Azure Machine Learning](concept-compute-instance.md#managing-a-compute-instance), ovvero un ambiente di sviluppo completamente configurato e gestito che includa notebook integrati e SDK già installato.

    **OR**

    * Usare il notebook di Jupyter e installare l'SDK manualmente con [queste istruzioni](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Alcune classi del set di dati presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , che è compatibile solo con Python a 64 bit. Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) e CentOS (7).

## <a name="compute-size-guidance"></a>Linee guida per le dimensioni di calcolo

Quando si crea un set di dati, verificare la potenza di elaborazione del calcolo e le dimensioni dei dati in memoria. Le dimensioni dei dati nell'archiviazione non corrispondono alle dimensioni dei dati in un frame di dati. Ad esempio, i dati nei file CSV possono espandersi fino a 10 volte in un frame di dati, quindi un file CSV da 1 GB può diventare 10 GB in un frame di dati. 

Se i dati sono compressi, possono espandersi ulteriormente; 20 GB di dati relativamente sparsi archiviati in formato parquet compresso possono espandersi fino a circa 800 GB di memoria. Poiché i file parquet archiviano i dati in un formato a colonne, se è necessaria solo la metà delle colonne, è sufficiente caricare circa 400 GB di memoria.

[Altre informazioni sull'ottimizzazione dell'elaborazione dei dati in Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Tipi di set di dati

Esistono due tipi di set di dati, in base al modo in cui gli utenti li utilizzano nel training; Filedatasets e TabularDatasets. Entrambi i tipi possono essere usati nei flussi di lavoro di training Azure Machine Learning che coinvolgono, estimatori, AutoML, iperguida e pipeline. 

### <a name="filedataset"></a>Filedataset

Un [filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. Se i dati sono già stati puliti e sono pronti per l'uso negli esperimenti di training, è possibile [scaricare o montare](how-to-train-with-datasets.md#mount-vs-download) i file nel calcolo come oggetto filedataset. 

Si consiglia di usare i filedataset per i flussi di lavoro di Machine Learning, perché i file di origine possono essere in qualsiasi formato, consentendo una gamma più ampia di scenari di apprendimento automatico, tra cui l'apprendimento avanzato.

Creare un filedataset con [Python SDK](#create-a-filedataset) o con [Azure Machine Learning Studio](#create-datasets-in-the-studio)

### <a name="tabulardataset"></a>TabularDataset

Un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. Questo consente di materializzare i dati in un frame di dati Pandas o Spark, in modo da poter usare le librerie di preparazione e formazione dei dati familiari senza dover uscire dal notebook. È possibile creare un `TabularDataset` oggetto da file con estensione CSV, TSV, parquet e JSON e dai [Risultati della query SQL](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Con TabularDatasets è possibile specificare un timestamp da una colonna nei dati o da qualsiasi posizione in cui vengono archiviati i dati del modello di percorso per abilitare un tratto della serie temporale. Questa specifica consente un filtro semplice ed efficiente in base al tempo. Per un esempio, vedere la [demo dell'API relativa alle serie temporali tabulari con i dati meteo NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Creare una TabularDataset con [Python SDK](#create-a-tabulardataset) o [Azure Machine Learning Studio](#create-datasets-in-the-studio).

>[!NOTE]
> I flussi di lavoro AutoML generati tramite il Azure Machine Learning Studio supportano attualmente solo TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Accedere ai set di impostazioni in una rete virtuale

Se l'area di lavoro si trova in una rete virtuale, è necessario configurare il set di dati per ignorare la convalida. Per altre informazioni su come usare gli archivi dati e i set di dati in una rete virtuale, vedere [isolamento rete durante il training & inferenza con reti virtuali private](how-to-enable-virtual-network.md#use-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-via-the-sdk"></a>Creare set di impostazioni tramite l'SDK

 Affinché i dati siano accessibili da parte di Azure Machine Learning, è necessario creare i set di dati da percorsi in [archivi dati di Azure](how-to-access-data.md) o URL Web pubblici. 

Per creare set di dati da un [archivio dati di Azure](how-to-access-data.md) con Python SDK:

1. Verificare `contributor` di avere o `owner` accedere all'archivio dati di Azure registrato.

2. Creare il set di dati facendo riferimento a percorsi nell'archivio dati. È possibile creare un set di dati da più percorsi in più archivi dati. Non esiste un limite fisso per il numero di file o le dimensioni dei dati da cui è possibile creare un set di dati. 

> [!Note]
> Per ogni percorso dati, verranno inviate alcune richieste al servizio di archiviazione per verificare se punta a un file o a una cartella. Questo sovraccarico può causare un peggioramento delle prestazioni o degli errori. Un set di dati che fa riferimento a una cartella con 1000 di file in viene considerato come riferimento a un percorso dati. Per ottenere prestazioni ottimali, è consigliabile creare un set di dati che fa riferimento a meno di 100 percorsi negli archivi dati.

### <a name="create-a-filedataset"></a>Creare un oggetto FileDataset

Utilizzare il [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) Metodo sulla `FileDatasetFactory` classe per caricare file in qualsiasi formato e per creare un oggetto filedataset non registrato. 

Se lo spazio di archiviazione è dietro una rete virtuale o un firewall, impostare il parametro `validate=False` nel `from_files()` metodo. In questo modo viene ignorato il passaggio di convalida iniziale e si garantisce che sia possibile creare il set di dati da questi file protetti. Altre informazioni su come usare gli [archivi dati e i set di dati in una rete virtuale](how-to-enable-virtual-network.md#use-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="create-a-tabulardataset"></a>Creare un TabularDataset

Usare il [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) Metodo sulla `TabularDatasetFactory` classe per leggere i file in formato CSV o TSV e per creare un TabularDataset non registrato. Se si esegue la lettura da più file, i risultati verranno aggregati in una rappresentazione tabulare. 

Se lo spazio di archiviazione è dietro una rete virtuale o un firewall, impostare il parametro `validate=False` nel `from_delimited_files()` metodo. In questo modo viene ignorato il passaggio di convalida iniziale e si garantisce che sia possibile creare il set di dati da questi file protetti. Altre informazioni su come usare gli [archivi dati e i set di dati in una rete virtuale](how-to-enable-virtual-network.md#use-datastores-and-datasets).

Il codice seguente ottiene l'area di lavoro esistente e l'archivio dati desiderato in base al nome. Quindi passa l'archivio dati e i percorsi dei file al `path` parametro per creare un nuovo TabularDataset `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Per impostazione predefinita, quando si crea un TabularDataset, i tipi di dati della colonna vengono dedotti automaticamente. Se i tipi dedotti non corrispondono alle aspettative, è possibile specificare i tipi di colonna usando il codice seguente. Il parametro `infer_column_type` è applicabile solo per i set di dati creati da file delimitati. [Altre informazioni sui tipi di dati supportati](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Indice|PassengerId|Survived|Pclass|Nome|Sesso|Età|SibSp|Parch|Ticket|Tariffe|Abitacolo|Intrapreso
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Vero|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7,9250||S

### <a name="create-a-dataset-from-pandas-dataframe"></a>Creare un set di dati da Pandas dataframe

Per creare un TabularDataset da un frame di dati Pandas in memoria, scrivere i dati in un file locale, ad esempio un volume CSV, e creare il set di dati da tale file. Il codice seguente illustra questo flusso di lavoro.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud

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

## <a name="register-datasets"></a>Registrare i set di impostazioni

Per completare il processo di creazione, registrare i set di impostazioni con un'area di lavoro. Usare il [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) metodo per registrare i set di elementi con l'area di lavoro per condividerli con altri utenti e riutilizzarli tra gli esperimenti nell'area di lavoro:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

<a name="datasets-ui"></a>
## <a name="create-datasets-in-the-studio"></a>Creare set di impostazioni in studio
I passaggi e l'animazione seguenti illustrano come creare un set di dati in [Azure Machine Learning Studio](https://ml.azure.com).

> [!Note]
> I set di impostazioni creati tramite Azure Machine Learning Studio vengono registrati automaticamente nell'area di lavoro.

![Creare un set di dati con l'interfaccia utente](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Per creare un set di dati in studio:
1. Accedere a https://ml.azure.com .
1. Selezionare **set di impostazioni** nella sezione **Asset** del riquadro sinistro. 
1. Selezionare **Crea set di dati** per scegliere l'origine del set di dati. Questa origine può essere costituita da file locali, da un archivio dati o da URL pubblici.
1. Selezionare **tabulare** o **file** per tipo di set di dati.
1. Selezionare **Avanti** per aprire il modulo **archivio dati e selezione file** . In questo modulo è possibile selezionare la posizione in cui salvare il set di dati dopo la creazione, nonché selezionare i file di dati da usare per il set di dati. 
    1. Abilitare Ignora convalida se i dati si trova in una rete virtuale. Scopri di più sull' [isolamento e sulla privacy della rete virtuale](how-to-enable-virtual-network.md#machine-learning-studio).
1. Selezionare **Avanti** per popolare i moduli **Impostazioni e anteprima** e **schema** ; vengono popolate in modo intelligente in base al tipo di file ed è possibile configurare ulteriormente il set di dati prima della creazione in questi moduli. 
1. Selezionare **Avanti** per esaminare il modulo **Conferma dettagli** . Controllare le selezioni e creare un profilo dati facoltativo per il set di dati. Altre informazioni sulla [profilatura dei dati](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Selezionare **Crea** per completare la creazione del set di dati.

## <a name="create-datasets-with-azure-open-datasets"></a>Creare set di impostazioni con i set di impostazioni di Azure Open

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) include set di dati pubblici curati che è possibile usare per aggiungere caratteristiche specifiche dello scenario alle soluzioni di Machine Learning e realizzare modelli più accurati. I set di dati includono dati di pubblico dominio relativi a meteo, censimento, festività, sicurezza pubblica e posizione, che consentono di eseguire il training di modelli di Machine Learning e arricchire le soluzioni predittive. I set di impostazioni aperti si trovano nel cloud in Microsoft Azure e sono inclusi sia nell'SDK che in studio.

Informazioni su come creare [set di impostazioni di Azure Machine Learning da Azure Open DataSets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Eseguire il training con set di dati

Usare i set di impostazioni negli esperimenti di machine learning per il training dei modelli ML. [Altre informazioni su come eseguire il training con DataSet](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>Set di impostazioni di versione

È possibile registrare un nuovo set di dati con lo stesso nome creando una nuova versione. Una versione del set di dati è un modo per aggiungere un segnalibro allo stato dei dati in modo che sia possibile applicare una versione specifica del set di dati per la sperimentazione o la riproduzione futura. Altre informazioni sulle [versioni del set di dati](how-to-version-track-datasets.md).
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

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come eseguire il training con i set di impostazioni](how-to-train-with-datasets.md).
* USA Machine Learning automatizzato per eseguire il [training con TabularDatasets](https://aka.ms/automl-dataset).
* Per altri esempi di training del set di dati, vedere i [notebook di esempio](https://aka.ms/dataset-tutorial).
