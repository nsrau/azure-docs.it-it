---
title: Creare e registrare i set di dati con l'area di lavoro
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
ms.date: 05/02/19
ms.openlocfilehash: 65a861c647c2dc92e416fa356075821aa5060042
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205029"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Creare e registrare i set di dati di Azure Machine Learning (anteprima)

In questo articolo descrive i flussi di lavoro di Azure Machine Learning per creare e registrare i set di dati e come accedervi per il riutilizzo in esperimenti locali e remoti.

Azure Machine Learning i set di dati (anteprima) rendono più semplice accedere e usare i dati. Set di dati di gestire i dati in diversi scenari, ad esempio, la creazione di pipeline e di training del modello. Usando il [SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), è possibile lavorare con i dati in formati comuni, accedere all'archiviazione sottostante, esplorare e preparare i dati, gestire il ciclo di vita delle definizioni di set di dati diversi e tra le due set di dati usati formazione e nell'ambiente di produzione.

## <a name="prerequisites"></a>Prerequisiti

Per creare e registrare i set di dati è necessario:

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro del servizio Azure Machine Learning. Visualizzare [creare un'area di lavoro del servizio di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Azure Machine Learning SDK per Python. Per installare o aggiornare la versione più recente del SDK, vedere [installare o aggiornare il SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="create-datasets-from-local-files"></a>Creare set di dati da file locali

Caricare i file dal computer locale, specificando il percorso di file o una cartella con il [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) dal metodo di `Dataset` classe.  Questo metodo esegue i passaggi seguenti senza richiedere di specificare il tipo di file o analisi di argomenti:

* L'inferenza e il delimitatore di impostazione.
* Ignora i record vuoti nella parte superiore del file.
* L'inferenza e l'impostazione della riga di intestazione.
* Deduzione e la conversione di tipi di dati di colonna.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

In alternativa, usare le funzioni specifiche del file per controllare in modo esplicito l'analisi del file app. Supporta attualmente il SDK Datasets [delimitati](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-), [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-), [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-), [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), e [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) formati di file.

## <a name="create-datasets-from-azure-datastores"></a>Creare i set di dati da archivi dati di Azure

Per creare i set di dati da un archivio dati di Azure, assicurarsi di:

* Verificare di che avere accesso all'archivio dati di Azure registrati proprietario o collaboratore.

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

||ID|Case Number|Data|Blocco|IUCR|Tipo principale|DESCRIZIONE|Descrizione della posizione|Arresto|Nazionale|...|Corsia|Area della community|Codice FBI|Coordinata X|Coordinata Y|Year|Aggiornato il|Latitude|Longitudine|Località|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PROCEDURE CONSIGLIATE INGANNEVOLI|FURTO DELL'IDENTITÀ FINANZIARIA SU $ 300|OTHER|FALSE|FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|FURTO| DALLA CREAZIONE|RESIDENZA|FALSE|FALSE|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PROCEDURE CONSIGLIATE INGANNEVOLI|FURTO DELL'IDENTITÀ FINANZIARIA $300 E IN|RESIDENZA|FALSE|FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|PROCEDURE CONSIGLIATE INGANNEVOLI|FALSA|RESIDENZA|FALSE|FALSE|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|FURTO|DALLA CREAZIONE|ISTITUTO DI ISTRUZIONE, PUBBLICA, CREAZIONE|FALSE|FALSE|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>Registrare i set di dati con area di lavoro

Usare la [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metodo per registrare i set di dati nell'area di lavoro per la condivisione e riutilizzo all'interno dell'organizzazione e tra diverse esperimenti.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',

                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> L'impostazione del parametro predefinito per `register()` è `exist_ok = False`. Se si prova a registrare un set di dati con lo stesso nome senza modificare questa impostazione verrà generato un errore.

Il `register()` metodo viene restituito il set di dati già registrata con l'impostazione dei parametri, `exist_ok = True`.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
```

Usare `list()` per visualizzare tutti i set di dati registrati nell'area di lavoro.

```Python
Dataset.list(workspace_name)
```

Il codice precedente produce quanto segue:

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>Set di dati di accesso nell'area di lavoro

I set di dati registrati siano accessibili e utilizzabili in locale, in remoto e nei cluster di calcolo, ad esempio le risorse di calcolo di Azure Machine Learning. Per riutilizzare il set di dati registrati in esperimenti e ambienti di calcolo, usare il codice seguente per ottenere l'area di lavoro e set di dati registrati in base al nome.

```Python
workspace = Workspace.from_config()

dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare e preparare i set di dati](how-to-explore-prepare-data.md).
* [Gestire il ciclo di vita delle definizioni di set di dati](how-to-manage-dataset-definitions.md).
* Per un esempio dell'uso di set di dati, vedere la [notebook di esempio](https://aka.ms/dataset-tutorial).
