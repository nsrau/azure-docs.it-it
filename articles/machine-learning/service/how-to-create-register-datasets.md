---
title: Creare set di dati per accedere ai dati con azureml-DataSets
titleSuffix: Azure Machine Learning service
description: Informazioni su come creare set di DataSet da varie origini e registrare set di impostazioni con l'area di lavoro
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: 2ce64df5eeb8aa44ef714d6b465b7f2e1819635d
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259285"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Creare e accedere ai set di impostazioni (anteprima) in Azure Machine Learning

In questo articolo si apprenderà come creare set di dati Azure Machine Learning (anteprima) e come accedere ai dati da esperimenti locali o remoti.

Con Azure Machine Learning set di impostazioni è possibile: 

* **Conservare una sola copia dei dati nella risorsa di archiviazione a** cui fanno riferimento i set di dati. 

* **Accedere facilmente ai dati durante il training del modello** senza doversi preoccupare delle stringhe di connessione o dei percorsi dati.

* **Condividere i dati & collaborare** con altri utenti.

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un' [area di lavoro del servizio Azure Machine Learning](how-to-manage-workspace.md)

* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-DataSets.

> [!Note]
> Alcune classi del set di dati (anteprima) presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti:  Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="dataset-types"></a>Tipi di set di dati

I set di impostazioni sono suddivisi in vari tipi in base al modo in cui gli utenti li utilizzano nel training. Elenco dei tipi di set di dati:
* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. Questo consente di materializzare i dati in un frame di dati Pandas. Un `TabularDataset` oggetto può essere creato da file CSV, TSV, parquet, risultati della query SQL e così via. Per un elenco completo, consultare la [documentazione](https://aka.ms/tabulardataset-api-reference).
* [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. Questo consente di scaricare o montare i file nel calcolo. I file possono avere qualsiasi formato, che consente una gamma più ampia di scenari di apprendimento automatico, tra cui l'apprendimento avanzato.

Per ulteriori informazioni sulle modifiche future dell'API, vedere [qui](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Creare set di dati 

Creando un set di dati, si crea un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. I dati rimangono nella posizione esistente, pertanto non è previsto alcun costo aggiuntivo per l'archiviazione.

Affinché i dati siano accessibili da parte del servizio Azure Machine Learning, è necessario creare i set di dati da percorsi in [archivi dati di Azure](how-to-access-data.md) o URL Web pubblici.

Per creare set di dati da un [archivio dati di Azure](how-to-access-data.md):

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
### <a name="create-tabulardatasets"></a>Crea TabularDatasets

Usare il `from_delimited_files()` metodo sulla `TabularDatasetFactory` classe per leggere i file in formato CSV o TSV e creare un TabularDataset non registrato. Se si esegue la lettura da più file, i risultati verranno aggregati in una rappresentazione tabulare.

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

| |PassengerId|Rimasti|Pclass|Name|Sesso|Tempo di risoluzione|SibSp|Parch|Ticket|Tariffe|Abitacolo|Intrapreso
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||D
1|2|1|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|1|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7,9250||D

### <a name="create-filedatasets"></a>Crea set di dati
Utilizzare il `from_files()` metodo sulla `FileDatasetFactory` classe per caricare file in qualsiasi formato e creare un oggetto filedataset non registrato.

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
## <a name="register-datasets"></a>Registrare i set di impostazioni

Per completare il processo di creazione, registrare i set di impostazioni con l'area di lavoro:

Usare il `register()` metodo per registrare i set di elementi nell'area di lavoro in modo che possano essere condivisi con altri utenti e riutilizzati in diversi esperimenti.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>Set di impostazioni di versione

È possibile registrare un nuovo set di dati con lo stesso nome creando una nuova versione. La versione del set di dati è un modo per aggiungere segnalibri allo stato dei dati, in modo da poter applicare una versione specifica del set di dati per la sperimentazione o la riproduzione futura. Scenari tipici da considerare per il controllo delle versioni: 
* Quando sono disponibili nuovi dati per la ripetizione del training.
* Quando si applicano approcci diversi per la preparazione dei dati o la progettazione di funzionalità.

```Python
# create a TabularDataset from new Titanic training data
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


## <a name="access-your-data-during-training"></a>Accedi ai dati durante il training

I set di impostazioni registrati sono accessibili in locale e in remoto nei cluster di calcolo come il Azure Machine Learning calcolo. Per accedere al set di dati registrato tra gli esperimenti, usare il codice seguente per ottenere l'area di lavoro e il set di dati registrato in base al nome. Per [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) impostazione predefinita, `Dataset` il metodo della classe restituisce la versione più recente del set di dati registrato con l'area di lavoro.

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

* USA Machine Learning automatizzato per eseguire il [training con TabularDatasets](https://aka.ms/automl-dataset).
* Per altri esempi di training con i set di impostazioni, vedere i [notebook di esempio](https://aka.ms/dataset-tutorial).
