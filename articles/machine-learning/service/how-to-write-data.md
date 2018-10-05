---
title: Scrivere dati con l'SDK di preparazione dati Azure Machine Learning - Python
description: Informazioni sulla scrittura dei dati con l'SDK di preparazione dati Azure Machine Learning. È possibile scrivere dati in qualsiasi punto di un flusso di dati e nei file in qualunque posizione supportata (file system locale, Archiviazione BLOB di Azure e Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 81344d388fbba0db034b8adb06adab6797ec2ce1
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166745"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Scrivere dati usando l'SDK di preparazione dati Azure Machine Learning
È possibile scrivere dati in qualsiasi punto di un flusso di dati. Queste operazioni di scrittura sono aggiunte come passaggi al flusso di dati risultante e vengono eseguite a ogni esecuzione del flusso di dati. I dati vengono scritti in più file di partizione per consentire operazioni di scrittura in parallelo.

Poiché non esistono limitazioni per il numero di passaggi di scrittura presenti in una pipeline, è molto semplice aggiungere passaggi di scrittura aggiuntivi per ottenere risultati intermedi per la risoluzione dei problemi o per altre pipeline. 

Ogni volta che si esegue un passaggio di scrittura, si verifica un pull completo dei dati nel flusso di dati. Ad esempio, un flusso di dati con tre operazioni di scrittura eseguirà tre volte la lettura e l'elaborazione di tutti i record nel set di dati.

## <a name="supported-data-types-and-location"></a>Tipi di dati supportati e percorso

Sono supportati i seguenti formati di file
-   File delimitati (CSV, TSV e così via)
-   File Parquet

Usando l'[SDK python di preparazione dati Azure Machine Learning](https://aka.ms/data-prep-sdk), è possibile scrivere dati in:
+ un file system locale
+ Archiviazione BLOB di Azure
+ Archiviazione di Azure Data Lake

## <a name="spark-considerations"></a>Considerazioni su Spark
Quando si esegue un flusso di dati in Spark, è necessario eseguire la scrittura in una cartella vuota. Tentativo di eseguire un'operazione di scrittura in una cartella esistente non riuscito. Verificare che la cartella di destinazione sia vuota o usare un percorso di destinazione diverso per ogni esecuzione. In caso contrario, l'operazione di scrittura avrà esito negativo.

## <a name="monitoring-write-operations"></a>Monitoraggio delle operazioni di scrittura
Per comodità, una volta completata un'operazione di scrittura, viene generato un file sentinel denominato SUCCESS. In questo modo, è possibile determinare quando un'operazione di scrittura intermedia è stata completata senza dover attendere il completamento dell'intera pipeline.

## <a name="example-write-code"></a>Esempio codice di scrittura

Per questo esempio, iniziare caricando i dati in un flusso di dati. Questi dati verranno riutilizzati con diversi formati.

```python
import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

Output di esempio:
|   |  Colonna1 |    Colonna2 | Colonna3 | Colonna4  |Colonna5   | Colonna6 | Colonna7 | Colonna8 | Colonna9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000,0 |   99999,0 |   Nessuna|       NO|     NO  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003,0 |   99999,0 |   Nessuna|       NO|     NO  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010,0|    99999,0|    Nessuna|   NO| JN| ENJA|   70933,0|    -8667,0 |90,0|
|3| 10013,0|    99999,0|    Nessuna|   NO| NO| |   NaN|    NaN|    NaN|
|4| 10014,0|    99999,0|    Nessuna|   NO| NO| ENSO|   59783,0|    5350,0| 500,0|
|5| 10015,0|    99999,0|    Nessuna|   NO| NO| ENBL|   61383,0|    5867,0| 3270,0|
|6| 10016,0 |99999,0|   Nessuna|   NO| NO|     |64850,0|   11233,0|    140,0|
|7| 10017,0|    99999,0|    Nessuna|   NO| NO| ENFR|   59933,0|    2417,0| 480,0|
|8| 10020,0|    99999,0|    Nessuna|   NO| SV|     |80050,0|   16250,0|    80,0|
|9| 10030,0|    99999,0|    Nessuna|   NO| SV|     |77000,0|   15500,0|    120,0|

### <a name="delimited-file-example"></a>Esempio di file delimitato

In questa sezione, è possibile vedere un esempio di come usare la `write_to_csv` funzione per scrivere con un file delimitato.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Output di esempio:
|   |  Colonna1 |    Colonna2 | Colonna3 | Colonna4  |Colonna5   | Colonna6 | Colonna7 | Colonna8 | Colonna9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000,0 |   99999,0 |   ERRORE |       NO|     NO  |   ENRS    |ERRORE    |   ERRORE |   ERRORE|    
|   1|      10003,0 |   99999,0 |   ERRORE |       NO|     NO  |   ENSO|       ERRORE|        ERRORE |ERRORE|   
|   2|  10010,0|    99999,0|    ERRORE |   NO| JN| ENJA|   70933,0|    -8667,0 |90,0|
|3| 10013,0|    99999,0|    ERRORE |   NO| NO| |   ERRORE|    ERRORE|    ERRORE|
|4| 10014,0|    99999,0|    ERRORE |   NO| NO| ENSO|   59783,0|    5350,0| 500,0|
|5| 10015,0|    99999,0|    ERRORE |   NO| NO| ENBL|   61383,0|    5867,0| 3270,0|
|6| 10016,0 |99999,0|   ERRORE |   NO| NO|     |64850,0|   11233,0|    140,0|
|7| 10017,0|    99999,0|    ERRORE |   NO| NO| ENFR|   59933,0|    2417,0| 480,0|
|8| 10020,0|    99999,0|    ERRORE |   NO| SV|     |80050,0|   16250,0|    80,0|
|9| 10030,0|    99999,0|    ERRORE |   NO| SV|     |77000,0|   15500,0|    120,0|

Nell'output precedente è possibile vedere che diversi errori vengono visualizzati nelle colonne numeriche a causa di numeri che non sono stati analizzati correttamente. Durante la scrittura in un file CSV, per impostazione predefinita questi valori null vengono sostituiti dalla stringa "ERRORE". 

È possibile aggiungere parametri durante la chiamata dell'operazione di scrittura e specificare una stringa da usare per rappresentare i valori null. Ad esempio: 

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Il codice precedente produce il seguente output:
|   |  Colonna1 |    Colonna2 | Colonna3 | Colonna4  |Colonna5   | Colonna6 | Colonna7 | Colonna8 | Colonna9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000,0 |   99999,0 |   BadData |       NO|     NO  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003,0 |   99999,0 |   BadData |       NO|     NO  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010,0|    99999,0|    BadData |   NO| JN| ENJA|   70933,0|    -8667,0 |90,0|
|3| 10013,0|    99999,0|    BadData |   NO| NO| |   BadData|    BadData|    BadData|
|4| 10014,0|    99999,0|    BadData |   NO| NO| ENSO|   59783,0|    5350,0| 500,0|
|5| 10015,0|    99999,0|    BadData |   NO| NO| ENBL|   61383,0|    5867,0| 3270,0|
|6| 10016,0 |99999,0|   BadData |   NO| NO|     |64850,0|   11233,0|    140,0|
|7| 10017,0|    99999,0|    BadData |   NO| NO| ENFR|   59933,0|    2417,0| 480,0|
|8| 10020,0|    99999,0|    BadData |   NO| SV|     |80050,0|   16250,0|    80,0|
|9| 10030,0|    99999,0|    BadData |   NO| SV|     |77000,0|   15500,0|    120,0|


### <a name="parquet-file-example"></a>Esempio di file parquet

In modo simile a `write_to_csv`, la `write_to_parquet` funzione restituisce un nuovo flusso di dati con un passaggio Parquet di scrittura che viene eseguito durante l'esecuzione del flusso di dati.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Successivamente, è possibile eseguire il flusso di dati per avviare l'operazione di scrittura.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

Il codice precedente produce il seguente output:
|   |  Colonna1 |    Colonna2 | Colonna3 | Colonna4  |Colonna5   | Colonna6 | Colonna7 | Colonna8 | Colonna9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000,0 |   99999,0 |   MiscreantData |       NO|     NO  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003,0 |   99999,0 |   MiscreantData |       NO|     NO  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010,0|    99999,0|    MiscreantData |   NO| JN| ENJA|   70933,0|    -8667,0 |90,0|
|3| 10013,0|    99999,0|    MiscreantData |   NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014,0|    99999,0|    MiscreantData |   NO| NO| ENSO|   59783,0|    5350,0| 500,0|
|5| 10015,0|    99999,0|    MiscreantData |   NO| NO| ENBL|   61383,0|    5867,0| 3270,0|
|6| 10016,0 |99999,0|   MiscreantData |   NO| NO|     |64850,0|   11233,0|    140,0|
|7| 10017,0|    99999,0|    MiscreantData |   NO| NO| ENFR|   59933,0|    2417,0| 480,0|
|8| 10020,0|    99999,0|    MiscreantData |   NO| SV|     |80050,0|   16250,0|    80,0|
|9| 10030,0|    99999,0|    MiscreantData |   NO| SV|     |77000,0|   15500,0|    120,0|
