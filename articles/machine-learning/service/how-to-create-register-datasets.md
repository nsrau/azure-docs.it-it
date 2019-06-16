---
title: Creare set di dati per accedere ai dati con Azure ml-set di dati
titleSuffix: Azure Machine Learning service
description: Informazioni su come creare set di dati da diverse origini e registrare i set di dati con l'area di lavoro
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: b4c22caae86e20b8379db2b7feffb1ca82001239
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753164"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Creare e accedere ai set di dati (anteprima) in Azure Machine Learning

In questo articolo si apprenderà come creare set di dati di Azure Machine Learning (anteprima) e come accedere ai dati da esperimenti locali e remoti.

Con i set di dati gestiti, è possibile: 
* **Accedere facilmente ai dati durante il training del modello** senza la riconnessione in archivi sottostanti

* **Garantire la coerenza dei dati e la riproducibilità** attraverso lo stesso puntatore esperimenti: notebook, automatizzati di Machine Learning, pipeline, l'interfaccia visiva

* **Condividere i dati e collaborano** con altri utenti

* **Esplorare i dati** & Gestione ciclo di vita delle versioni e gli snapshot di dati

* **Confrontare i dati** nel training nell'ambiente di produzione


## <a name="prerequisites"></a>Prerequisiti

Per creare e lavorare con i set di dati, è necessario:

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un [dell'area di lavoro del servizio Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* Il [Azure Machine Learning SDK per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-set di dati.

> [!Note]
> Alcune classi di set di dati (anteprima) hanno dipendenze le [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) pacchetto (GA). Per gli utenti di Linux, queste classi sono supportate solo nelle distribuzioni seguenti:  Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="data-formats"></a>Formati di dati

È possibile creare un set di dati di Azure Machine Learning dai dati seguenti:
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Database SQL di Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Utilizzo di Azure Data Lake. 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Creare set di dati 

È possibile interagire con i set di dati con il pacchetto azureml-set di dati nel [SDK Python di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e in particolare [il `Dataset` classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py).

### <a name="create-from-local-files"></a>Il nome di file locali

Caricare i file dal computer locale, specificando il percorso di file o una cartella con il [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) dal metodo di `Dataset` classe.  Questo metodo esegue i passaggi seguenti senza richiedere di specificare il tipo di file o analisi di argomenti:

* L'inferenza e il delimitatore di impostazione.
* Ignora i record vuoti nella parte superiore del file.
* L'inferenza e l'impostazione della riga di intestazione.
* Deduzione e la conversione di tipi di dati di colonna.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

In alternativa, usare le funzioni specifiche del file per controllare in modo esplicito l'analisi del file app. 


### <a name="create-from-azure-datastores"></a>Creare da archivi dati di Azure

Per creare i set di dati da un archivio dati di Azure:

* Verificare di aver `contributor` o `owner` accesso all'archivio dati di Azure registrati.

* Importa i [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) e [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) e `Dataset` pacchetti dal SDK.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 Il `get()` metodo recupera un archivio dati esistente nell'area di lavoro.

```
dstore = Datastore.get(workspace, datastore_name)
```

Usare il `from_delimited_files()` metodo per leggere i file delimitati e creare un set di dati non registrato.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Registrare i set di dati

Per completare il processo di creazione, registrare i set di dati con area di lavoro:

Usare la [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metodo per registrare i set di dati nell'area di lavoro in modo che possono essere condivisi con altri utenti e riusati in diverse esperimenti.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Se `exist_ok = False` (impostazione predefinita), e si tenta di registrare un set di dati con lo stesso nome di un altro, si verifica un errore. Impostare su `True` sovrascrivere esistente.

## <a name="access-data-in-datasets"></a>Accedere ai dati in set di dati

I set di dati registrati siano accessibili e utilizzabili in locale, in remoto e nei cluster di calcolo, ad esempio le risorse di calcolo di Azure Machine Learning. Per riutilizzare il set di dati registrati in esperimenti e ambienti di calcolo, usare il codice seguente per ottenere l'area di lavoro e set di dati registrati in base al nome.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare e preparare i set di dati](how-to-explore-prepare-data.md).
* [Gestire il ciclo di vita delle definizioni di set di dati](how-to-manage-dataset-definitions.md).
* Per un esempio dell'uso di set di dati, vedere la [notebook di esempio](https://aka.ms/dataset-tutorial).
