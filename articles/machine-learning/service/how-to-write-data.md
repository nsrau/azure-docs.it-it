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
ms.openlocfilehash: da5823473b7f69fa0a6c65d5ea7319bfd2e92720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946766"
---
# <a name="write-data-with-the-azure-machine-learning-data-prep-sdk"></a>Scrivere dati con l'SDK di preparazione dati Azure Machine Learning
È possibile scrivere dati in qualsiasi punto di un flusso di dati. Queste operazioni di scrittura sono aggiunte come passaggi al flusso di dati risultante e vengono eseguite a ogni esecuzione del flusso di dati. Poiché non esistono limitazioni per il numero di passaggi di scrittura presenti in una pipeline, è molto semplice eseguire la scrittura dei risultati intermedi a scopo di risoluzione dei problemi o per l'uso in altre pipeline. È importante tenere presente che l'esecuzione di ogni passaggio di scrittura comporta un pull completo dei dati nel flusso di dati. Ad esempio, un flusso di dati con tre operazioni di scrittura eseguirà tre volte la lettura e l'elaborazione di tutti i record nel set di dati.

## <a name="writing-to-files"></a>Scrittura nei file
Con l'[SDK di preparazione dati Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py), è possibile scrivere dati nei file in qualunque posizione supportata (file system locale, Archiviazione BLOB di Azure e Azure Data Lake Storage). I dati vengono scritti in più file di partizione per consentire operazioni di scrittura in parallelo. Una volta completata un'operazione di scrittura, viene generato anche un file sentinel denominato SUCCESS. In questo modo, è possibile determinare quando un'operazione di scrittura intermedia è stata completata senza dover attendere il completamento dell'intera pipeline.

Quando si esegue un flusso di dati in Spark, è necessario eseguire la scrittura in una cartella vuota. Il tentativo di eseguire un'operazione di scrittura in una cartella esistente avrà esito negativo. Verificare che la cartella di destinazione sia vuota o usare un percorso di destinazione diverso per ogni esecuzione. In caso contrario, l'operazione di scrittura avrà esito negativo.

L'SDK di preparazione dati Azure Machine Learning supporta i formati di file seguenti:
-   File delimitati (CSV, TSV e così via)
-   File Parquet

Per questo esempio, iniziare caricando i dati in un flusso di dati. Questi dati verranno riutilizzati con diversi formati.

```

import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

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

## <a name="delimited-files"></a>File delimitati
La riga seguente crea un nuovo flusso di dati con un passaggio di scrittura, ma la scrittura effettiva non è ancora stata eseguita. La scrittura verrà eseguita al momento dell'esecuzione del flusso di dati.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))
```
A questo punto, eseguire il flusso di dati, che esegue l'operazione di scrittura.
```
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
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

I dati scritti contengono diversi errori nelle colonne numeriche a causa di numeri che non sono stati analizzati correttamente. Durante la scrittura in un file CSV, per impostazione predefinita questi valori null vengono sostituiti dalla stringa "ERRORE". È possibile aggiungere parametri durante la chiamata dell'operazione di scrittura e specificare una stringa da usare per rappresentare i valori null.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
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
## <a name="parquet-files"></a>File Parquet

 In modo simile alla funzione `write_to_csv` precedente, `write_to_parquet` restituisce un nuovo flusso di dati con un passaggio Parquet di scrittura che verrà eseguito durante l'esecuzione del flusso di dati.

```
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```
 A questo punto, possiamo eseguire il flusso di dati, che esegue l'operazione di scrittura.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```
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
