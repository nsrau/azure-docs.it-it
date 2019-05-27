---
title: Definizione del set di dati e controllo delle versioni con Azure ml-set di dati
titleSuffix: Azure Machine Learning service
description: Informazioni su come aggiornare le definizioni dei set di dati e gestire il ciclo di vita delle definizioni
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: e58ce156deaaad259ea7b74521bcf9b79afbd183
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146212"
---
# <a name="update-and-manage-the-lifecycle-of-dataset-definitions"></a>Aggiornare e gestire il ciclo di vita delle definizioni di set di dati

Informazioni su come aggiornare e gestire definizioni di set di dati con i set di dati di Azure Machine Learning (anteprima).

Le definizioni di set di dati sono le soluzioni consente di preparare i set di dati, che includono entrambi il riferimento per i dati di origine e la procedura di trasformazione eseguita. Un set di dati può avere numerose definizioni e ogni definizione ha il proprio ciclo di vita.

Lo scenario seguente è riportato un esempio dell'uso di definizioni di set di dati:

1. Si compila una pipeline di machine learning che usa la definizione del set di dati corrente.
1. Modifiche dei dati sottostante. sono stati aggiunti nuovi attributi.
1. Si crea una nuova versione della definizione di che consente di aggiungere le trasformazioni aggiuntive per gestire i nuovi attributi.

In questo esempio, la pipeline esistente continua a usare la versione della definizione originale. La nuova versione di definizione è utilizzabile per nuovi scenari, ad esempio un modello di training o la creazione di una pipeline.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di una sottoscrizione di Azure e un'area di lavoro per registrare il set di dati per gestire il ciclo di vita delle definizioni di set di dati.

Il file di esempio usato negli esempi in questo documento è disponibile all'indirizzo [ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv).

## <a name="update-dataset-definitions"></a>Aggiornare le definizioni dei set di dati

Verrà innanzitutto creato e registrare un set di dati con l'area di lavoro.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

La prima definizione di set di dati (`version_id` = 1) viene creato quando viene creato il set di dati. Ogni volta che si aggiorna la definizione di set di dati, un nuovo version_id verrà assegnato alla definizione più recente.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Arresto</th>
      <th>Latitude</th>
      <th>Longitudine</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Questo aggiornamento della definizione non cancellerà precedenti definizioni di set di dati. È comunque possibile accedere e utilizzare le definizioni precedenti.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Case Number</th>
      <th>Data</th>
      <th>Blocco</th>
      <th>IUCR</th>
      <th>Tipo principale</th>
      <th>DESCRIZIONE</th>
      <th>Descrizione della posizione</th>
      <th>Arresto</th>
      <th>Nazionale</th>
      <th>...</th>
      <th>Corsia</th>
      <th>Area della community</th>
      <th>Codice FBI</th>
      <th>Coordinata X</th>
      <th>Coordinata Y</th>
      <th>Year</th>
      <th>Aggiornato il</th>
      <th>Latitude</th>
      <th>Longitudine</th>
      <th>Località</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>PROCEDURE CONSIGLIATE INGANNEVOLI</td>
      <td>FURTO DELL'IDENTITÀ FINANZIARIA SU $ 300</td>
      <td>OTHER</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX S MARSHFIELD AVE</td>
      <td>890</td>
      <td>FURTO</td>
      <td>DALLA CREAZIONE</td>
      <td>RESIDENZA</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX S SACRAMENTO AVE</td>
      <td>1154</td>
      <td>PROCEDURE CONSIGLIATE INGANNEVOLI</td>
      <td>FURTO DELL'IDENTITÀ FINANZIARIA $300 E IN</td>
      <td>RESIDENZA</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX S PRAIRIE AVE</td>
      <td>1120</td>
      <td>PROCEDURE CONSIGLIATE INGANNEVOLI</td>
      <td>FALSA</td>
      <td>RESIDENZA</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>FURTO</td>
      <td>DALLA CREAZIONE</td>
      <td>ISTITUTO DI ISTRUZIONE, PUBBLICA, CREAZIONE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Come si può notare dal risultato, la prima versione della definizione di set di dati mantiene ancora tutte le colonne dai dati crime indipendentemente dall'aggiornamento successivo. Ad esempio, se hai un apprendimento del modello attualmente che la prima versione del set di dati consente di utilizzare `Date` colonne dal set di dati come una delle funzionalità, non si verificherà un errore con l'aggiornamento delle definizioni più recente, mantenendo solo `ID`, `Arrest`, `Latitude`, `Longitude` colonne dai dati crime.

## <a name="how-to-access-dataset-definitions"></a>Come accedere alle definizioni dei set di dati

Per ottenere un elenco di tutte le definizioni, usare `get_definitions()`. Per ottenere una versione specifica di una definizione, usare `get_definition()`. Nell'esempio seguente viene illustrato il recupero di un elenco di tutte le definizioni e quindi recuperando la versione 1:

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

L'output di `get_definitions()` è simile all'esempio seguente:

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

Dopo avere recuperato una definizione, è possibile usarlo con modelli di machine learning o in una pipeline di machine learning.

## <a name="manage-lifecycle-of-dataset-definitions"></a>Gestione del ciclo di vita delle definizioni di set di dati

È possibile gestire il ciclo di vita di ogni definizione di set di dati in modo indipendente. Esistono tre fasi del ciclo di vita: attivo, deprecate o archiviato.

### <a name="active"></a>Attive

Quando viene creata una nuova definizione di set di dati, è attiva per impostazione predefinita. 

### <a name="deprecate"></a>Deprecare

Le definizioni di set di dati possano deprecate quando utilizzo non è più consigliato ed è disponibile una sostituzione. Quando viene usata una definizione di set di dati deprecata in pipeline di machine learning, ottiene ha restituito un messaggio di avviso, ma non verrà bloccata l'esecuzione.

Quando la deprecazione di una definizione di set di dati, specificare l'ID del set di dati e l'ID della versione definizione set di dati per la definizione di sostituzione. Queste informazioni sono memorizzate e utilizzate nel messaggio di avviso quando si prova a usare una definizione di set di dati deprecata.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>Archivia

Le definizioni di set di dati possono essere archiviate quando le definizioni non sono considerate da usare per qualsiasi motivo (ad esempio, i dati sottostanti non saranno più disponibili). Quando viene usata una definizione di set di dati archiviata in pipeline di machine learning, con l'errore verrà bloccata l'esecuzione.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>Riattivare

È possibile riattivare facilmente qualsiasi definizione di set di dati deprecati o archived.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'utilizzo dei set di dati, vedere [crea e registra il set di dati di Azure Machine Learning](how-to-create-register-datasets.md).

Per un esempio dell'uso di set di dati, vedere la [notebook di esempio](https://aka.ms/dataset-tutorial).
