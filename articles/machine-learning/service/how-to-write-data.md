---
title: 'Scrittura: preparazione dei dati con Python SDK'
titleSuffix: Azure Machine Learning service
description: Informazioni sulla scrittura dei dati con l'SDK di preparazione dati Azure Machine Learning. È possibile scrivere dati in qualsiasi punto di un flusso di dati e nei file in qualunque posizione supportata (file system locale, Archiviazione BLOB di Azure e Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 92f04d80ea956f3036d7778a5d6de62e53b969ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817364"
---
# <a name="write-and-configure-data-using-azure-machine-learning"></a>Scrivere e configurare i dati usando Azure Machine Learning

In questo articolo descrive diversi metodi per scrivere i dati usando il [Python SDK di Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk) e su come configurare i dati per la sperimentazione con il [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  I dati di output possono essere scritto in qualsiasi punto in un flusso di dati. Operazioni di scrittura vengono aggiunte come passaggi al flusso di dati risultante e questi passaggi vengono eseguiti ogni volta che il flusso di dati viene eseguito. I dati vengono scritti in più file di partizione per consentire operazioni di scrittura in parallelo.

Poiché non esistono limitazioni per il numero di passaggi di scrittura presenti in una pipeline, è molto semplice aggiungere passaggi di scrittura aggiuntivi per ottenere risultati intermedi per la risoluzione dei problemi o per altre pipeline.

Ogni volta che si esegue un passaggio di scrittura, si verifica un pull completo dei dati nel flusso di dati. Ad esempio, un flusso di dati con tre operazioni di scrittura eseguirà tre volte la lettura e l'elaborazione di ogni record nel set di dati.

## <a name="supported-data-types-and-location"></a>Tipi di dati supportati e percorso

Sono supportati i seguenti formati di file
-   File delimitati (CSV, TSV e così via)
-   File Parquet

Usa Azure Machine Learning Data Prep Python SDK, è possibile scrivere i dati per:
+ un file system locale
+ Archiviazione BLOB di Azure
+ Archiviazione di Azure Data Lake

## <a name="spark-considerations"></a>Considerazioni su Spark

Quando si esegue un flusso di dati in Spark, è necessario eseguire la scrittura in una cartella vuota. Tentativo di eseguire un'operazione di scrittura in una cartella esistente non riuscito. Verificare che la cartella di destinazione sia vuota o usare un percorso di destinazione diverso per ogni esecuzione. In caso contrario, l'operazione di scrittura avrà esito negativo.

## <a name="monitoring-write-operations"></a>Monitoraggio delle operazioni di scrittura

Per comodità, una volta completata un'operazione di scrittura, viene generato un file sentinel denominato SUCCESS. In questo modo, è possibile determinare quando un'operazione di scrittura intermedia è stata completata senza dover attendere il completamento dell'intera pipeline.

## <a name="example-write-code"></a>Esempio codice di scrittura

Per questo esempio, avviare il caricamento dei dati in un flusso di dati tramite `auto_read_file()`. Questi dati vengono riutilizzati con diversi formati.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Output di esempio:

| | Colonna1 | Colonna2 | Colonna3 | Colonna4 | Colonna5 | Colonna6 | Colonna7 | Colonna8 | Colonna9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000,0 | 99999,0 | Nessuna | NO | NO | ENRS | NaN | NaN | NaN |   
|1| 10003,0 | 99999,0 | Nessuna | NO | NO | ENSO | NaN | NaN | NaN |   
|2| 10010,0 | 99999,0 | Nessuna | NO | JN | ENJA | 70933,0 | -8667,0 | 90,0 |
|3| 10013,0 | 99999,0 | Nessuna | NO | NO |      | NaN | NaN | NaN |
|4| 10014,0 | 99999,0 | Nessuna | NO | NO | ENSO | 59783,0 | 5350,0 |  500,0|

### <a name="delimited-file-example"></a>Esempio di file delimitato

Il codice seguente usa il [ `write_to_csv()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-csv-directory-path--destinationpath--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) funzione per scrivere dati in un file delimitato da virgole.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Output di esempio:

| | Colonna1 | Colonna2 | Colonna3 | Colonna4 | Colonna5 | Colonna6 | Colonna7 | Colonna8 | Colonna9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000,0 | 99999,0 | ERRORE | NO | NO | ENRS | NaN    | NaN | NaN |   
|1| 10003,0 | 99999,0 | ERRORE | NO | NO | ENSO |    NaN | NaN | NaN |   
|2| 10010,0 | 99999,0 | ERRORE | NO | JN | ENJA |    70933,0 | -8667,0 | 90,0 |
|3| 10013,0 | 99999,0 | ERRORE | NO | NO |     | NaN | NaN | NaN |
|4| 10014,0 | 99999,0 | ERRORE | NO | NO | ENSO |    59783,0 | 5350,0 |  500,0|

Nell'output precedente diversi errori vengono visualizzati nelle colonne numeriche a causa di numeri che non sono stati analizzati correttamente. Durante la scrittura in un file CSV, per impostazione predefinita i valori Null vengono sostituiti dalla stringa "ERRORE".

Aggiungere parametri durante la chiamata dell'operazione di scrittura e specificare una stringa da usare per rappresentare i valori Null.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Il codice precedente produce il seguente output:

| | Colonna1 | Colonna2 | Colonna3 | Colonna4 | Colonna5 | Colonna6 | Colonna7 | Colonna8 | Colonna9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000,0 | 99999,0 | BadData | NO | NO | ENRS | NaN  | NaN | NaN |   
|1| 10003,0 | 99999,0 | BadData | NO | NO | ENSO |  NaN | NaN | NaN |   
|2| 10010,0 | 99999,0 | BadData | NO | JN | ENJA |  70933,0 | -8667,0 | 90,0 |
|3| 10013,0 | 99999,0 | BadData | NO | NO |   | NaN | NaN | NaN |
|4| 10014,0 | 99999,0 | BadData | NO | NO | ENSO |  59783,0 | 5350,0 |  500,0|

### <a name="parquet-file-example"></a>Esempio di file parquet

Simile a `write_to_csv()`, il [ `write_to_parquet()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-parquet-file-path--typing-union--destinationpath--nonetype----none--directory-path--typing-union--destinationpath--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) funzione restituisce un nuovo flusso di dati con un'operazione di scrittura passaggio Parquet che viene eseguito quando il flusso di dati viene eseguito.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Eseguire il flusso di dati per avviare l'operazione di scrittura.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

Il codice precedente produce il seguente output:

|   | Colonna1 | Colonna2 | Colonna3 | Colonna4 | Colonna5 | Colonna6 | Colonna7 | Colonna8 | Colonna9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000,0 | 99999,0 | MiscreantData | NO | NO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003,0 | 99999,0 | MiscreantData | NO | NO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010,0 | 99999,0 | MiscreantData | NO| JN| ENJA|   70933,0|    -8667,0 |90,0|
|3| 10013,0 | 99999,0 | MiscreantData | NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014,0 | 99999,0 | MiscreantData | NO| NO| ENSO|   59783,0|    5350,0| 500,0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Configurare i dati per la formazione automatizzati di machine learning

Passare il file di dati appena scritti in un [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) oggetto in preparazione per la formazione automatizzati di machine learning. 

Esempio di codice seguente viene illustrato come convertire i flussi di dati in un dataframe di Pandas e, successivamente, dividere il set di dati di training e test per la formazione automatizzati di machine learning.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Se non sono necessarie eventuali passaggi di preparazione dei dati intermedi, come illustrato nell'esempio precedente, è possibile passare il flusso di dati direttamente in `AutoMLConfig`.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>Passaggi successivi
* Vedere il SDK [Panoramica](https://aka.ms/data-prep-sdk) per esempi di utilizzo e i modelli di progettazione 
* Vedere l'apprendimento automatico [esercitazione](tutorial-auto-train-models.md) per un esempio di modello di regressione