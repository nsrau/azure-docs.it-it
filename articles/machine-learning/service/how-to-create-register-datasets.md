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
ms.date: 05/21/2019
ms.openlocfilehash: c5b423fca3e0ec116fceefb6867189f4f8413b96
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856081"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Creare e accedere ai set di impostazioni (anteprima) in Azure Machine Learning

In questo articolo si apprenderà come creare set di dati Azure Machine Learning (anteprima) e come accedere ai dati da esperimenti locali o remoti.

Con Azure Machine Learning set di impostazioni è possibile: 

* **Mantieni una sola copia dei dati nello spazio di archiviazione a** cui fanno riferimento i set di dati

* **Analizzare i dati** tramite l'analisi esplorativa dei dati 

* **Accedere facilmente ai dati durante il training del modello** senza doversi preoccupare della stringa di connessione o del percorso dati

* **Condividere i dati & collaborare** con altri utenti

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un' [area di lavoro del servizio Azure Machine Learning](how-to-manage-workspace.md)

* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-DataSets.

> [!Note]
> Alcune classi del set di dati (anteprima) presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) (GA). Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti:  Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="data-formats"></a>Formati di dati

È possibile creare un set di dati Azure Machine Learning dai formati seguenti:
+ [delimited](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none--partition-format-none-)
+ [json](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false--partition-format-none-)
+ [Excel](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true--partition-format-none-)
+ [Parquet](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false--partition-format-none-)
+ [dataframe Pandas](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [SQL query](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>Creare set di dati 

Creando un set di dati, si crea un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. I dati rimangono nella posizione esistente, pertanto non è previsto alcun costo aggiuntivo per l'archiviazione.

### <a name="create-from-local-files"></a>Crea da file locali

Caricare i file dal computer locale specificando il percorso del file o della cartella [`auto_read_files()`](/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false--partition-format-none-) con il metodo `Dataset` della classe.  Questo metodo esegue i passaggi seguenti senza richiedere di specificare il tipo di file o gli argomenti di analisi:

* Inferenza e impostazione del delimitatore.
* I record vuoti verranno ignorati all'inizio del file.
* Deduzione e impostazione della riga di intestazione.
* Deduzione e conversione di tipi di dati di colonna.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

In alternativa, usare le funzioni specifiche del file per controllare in modo esplicito l'analisi del file. 


### <a name="create-from-azure-datastores"></a>Crea da archivi dati di Azure

Per creare set di dati da un [archivio dati di Azure](how-to-access-data.md):

* Verificare di avere `contributor` o `owner` accedere all'archivio dati di Azure registrato.

* Creare il set di dati facendo riferimento a un percorso nell'archivio dati 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

Usare il metodo `from_delimited_files()` per leggere i file delimitati da un [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) e creare un set di dati non registrato.

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Registrare i set di impostazioni

Per completare il processo di creazione, registrare i set di impostazioni con l'area di lavoro:

Usare il [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metodo per registrare i set di elementi nell'area di lavoro in modo che possano essere condivisi con altri utenti e riutilizzati in diversi esperimenti.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Se `exist_ok = False` (impostazione predefinita) e si tenta di registrare un set di dati con lo stesso nome di un altro, si verifica un errore. Impostare su `True` per sovrascrivere esistente.

## <a name="access-data-in-datasets"></a>Accedere ai dati nei set di dati

I set di impostazioni registrati sono accessibili in locale e in remoto nei cluster di calcolo come il Azure Machine Learning calcolo. Per accedere al set di dati registrato tra gli esperimenti, usare il codice seguente per ottenere l'area di lavoro e il set di dati registrato in base al nome.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare e preparare i set di impostazioni](how-to-explore-prepare-data.md).
* Per un esempio dell'uso dei set di impostazioni, vedere i [notebook di esempio](https://aka.ms/dataset-tutorial).
