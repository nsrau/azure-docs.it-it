---
title: Confronta & riprodurre i dati nel corso del tempo con gli snapshot
titleSuffix: Azure Machine Learning service
description: Informazioni su come confrontare i dati nel tempo e garantire la riproducibilità con set di dati snapshot
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/02/2019
ms.openlocfilehash: 51d0dcfc543834e9a8725d11fa82b566a5132a6b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205008"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Confrontare i dati e garantire la riproducibilità gli snapshot (anteprima)

In questo articolo descrive come creare e gestire gli snapshot delle [set di dati di Azure Machine Learning](how-to-create-register-datasets.md) (set di dati) in modo che è possibile acquisire o confrontare i dati nel corso del tempo. I set di dati rendono più semplice accedere e usare i dati nel cloud in vari scenari.

**Set di dati snapshot** archiviare un profilo (statistiche summary) dei dati al momento della creazione. È possibile scegliere anche archiviare una copia dei dati nello snapshot per permetterne la riproduzione.

>[!Important]
> Gli snapshot comportano costi di archiviazione. L'archiviazione di una copia dei dati nello snapshot richiede anche più spazio di archiviazione. Uso [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) quando non sono più necessari.

## <a name="when-to-use-snapshots"></a>Quando usare gli snapshot

Esistono tre utilizzi principali per gli snapshot:

+ **Convalida del modello**: Confrontare il profilo dei dati di snapshot differenti tra le esecuzioni di training o con dati di produzione.

+ **Modellare la riproducibilità**: Riprodurre i risultati tramite la chiamata di uno snapshot che include i dati durante il training.

+ **Tenere traccia dei dati nel corso del tempo**: Vedere come il set di dati si è evoluta da [confronto tra i profili](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>Prerequisiti

Per creare gli snapshot di set di dati, è necessario Azure Machine Learning Dataset registrati. Se non hai uno, vedere [set di dati crea e registra](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Creazione di set di dati snapshot

Per creare uno snapshot di un set di dati, usare [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) dal SDK di Azure Machine Learning.

Per impostazione predefinita, lo snapshot consente di archiviare il profilo (statistiche summary) dei dati con la versione più recente [definizione del set di dati](how-to-manage-dataset-definitions.md) applicato. Una definizione di set di dati contiene un record di tutti i passaggi di trasformazione definita per i dati. È un ottimo modo per il funzionamento di preparazione dei dati riproducibile.

Facoltativamente, è possibile anche includere una copia dei dati nello snapshot aggiungendo `create_data_snapshot = True`.  Questi dati possono essere utili per permetterne la riproduzione.

Questo esempio viene usato [campionare i dati crime](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) e un set di dati denominato `dataset_crime` creato usando l'articolo ["registratore di cassa e Crea set di dati"](how-to-create-register-datasets.md).

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

Poiché gli snapshot vengono creati in modo asincrono, usare il [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) metodo per monitorare il processo.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Output:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Uso [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) quando non sono più necessari.

## <a name="find-snapshots"></a>Trovare gli snapshot

Per recuperare uno snapshot esistente, usare [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-).

Per ottenere un elenco degli snapshot di un determinato set di dati salvato, utilizzare [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--).

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Ottenere dati e i profili da snapshot

Ogni snapshot che genera l'errore di un profilo del set di dati, che include le statistiche di riepilogo e offre la possibilità di salvare una copia dei dati.

### <a name="get-the-data-profile"></a>Ottenere il profilo dei dati

Uso [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) per accedere al profilo dei dati.  Per confrontare i dati di training e di produzione, ad esempio, è possibile usare questo profilo.

```Python
snapshot.get_profile()
```

||Type|Min|Max|Conteggio|Totale mancanti|Totale non mancanti|Percentuale mancanti|Totale errori|Totale vuoti|Quantile 0,1%|Quantile 1%|Quantile 5%|Quantile 25%|Quantile 50%|Quantile 75%|Quantile 95%|Quantile 99%|Quantile 99,9%|Media|Deviazione standard|Variance|Asimmetria|Curtosi
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e + 07|1.05259e+07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e+08|-0.495701|-1.02814
Case Number|FieldType.STRING|HZ239907|HZ278872|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Data|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Blocco|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10,0|0.0|10,0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Tipo principale|FieldType.STRING|PROCEDURE CONSIGLIATE INGANNEVOLI|FURTO|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
DESCRIZIONE|FieldType.STRING|CONTROLLO FITTIZIO|SU 500 DOLLARI|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Descrizione della posizione|FieldType.STRING||ISTITUTO DI ISTRUZIONE, PUBBLICA, CREAZIONE|10,0|0.0|10,0|0.0|0.0|1.0||||||||||||||
Arresto|FieldType.BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Nazionale|FieldType.BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10,0|0.0|10,0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Distretto|FieldType.INTEGER|5|24|10,0|0.0|10,0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Corsia|FieldType.INTEGER|1|48|10,0|0.0|10,0|0.0|0.0|0.0|1|5|1|9|22,5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Area della community|FieldType.INTEGER|4|77|10,0|0.0|10,0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>Ottenere i dati dallo snapshot

Per ottenere una copia dei dati salvati in uno snapshot di set di dati, generare un DataFrame di pandas con i [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) (metodo).

Questo metodo ha esito negativo se non è stata richiesta una copia dei dati durante la creazione di snapshot.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|Case Number|Data|Blocco|IUCR|Tipo principale|DESCRIZIONE|Descrizione della posizione|Arresto|Nazionale|...|Corsia|Area della community|Codice FBI|Coordinata X|Coordinata Y|Year|Aggiornato il|Latitude|Longitudine|Località
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|PROCEDURE CONSIGLIATE INGANNEVOLI|FURTO DELL'IDENTITÀ FINANZIARIA SU $ 300|OTHER|False|False|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|890|FURTO|DALLA CREAZIONE|RESIDENZA|False|False|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|1154|PROCEDURE CONSIGLIATE INGANNEVOLI|FURTO DELL'IDENTITÀ FINANZIARIA $300 E IN|RESIDENZA|False|False|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>Passaggi successivi

* Vedere il SDK [Panoramica](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) per esempi di utilizzo e i modelli di progettazione.

* Per un esempio di utilizzo di set di dati snapshot, vedere la [notebook di esempio](https://aka.ms/dataset-tutorial).
