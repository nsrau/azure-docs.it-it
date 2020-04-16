---
title: Eseguire query sui file di parquet tramite SQL su richiesta (anteprima)Query Parquet files using SQL on-demand (preview)
description: In questo articolo verrà illustrato come eseguire query sui file di parquet utilizzando SQL su richiesta (anteprima).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431696"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Eseguire query sui file di parquet usando SQL su richiesta (anteprima) in Azure Synapse Analytics

In questo articolo verrà illustrato come scrivere una query utilizzando SQL su richiesta (anteprima) che leggerà i file di parquet.

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere il resto di questo articolo, leggere i seguenti articoli:

- [Configurazione al primo tempo](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Set di dati

È possibile eseguire una query sui file Parquet nello stesso modo in cui si leggono i file CSV. L'unica differenza è che il parametro FILEFORMAT deve essere impostato su PARQUET. Esempi in questo articolo mostrano le specifiche di lettura dei file di parquet.

> [!NOTE]
> Non è necessario specificare colonne nella clausola OPENROWSET WITH durante la lettura dei file di parquet. SQL su richiesta utilizzerà i metadati nel file Parquet e le colonne di associazione in base al nome.

Si utilizzerà la cartella *parquet/taxi* per le query di esempio. Contiene i dati di NYC Taxi - Yellow Taxi Trip Records di luglio 2016. giugno 2018.

I dati vengono partizionati per anno e mese e la struttura delle cartelle è la seguente:

- Anno 2016
  - Mese n. 6
  - ...
  - Mese 12
- Anno 2017
  - Mese n. 1
  - ...
  - Mese 12
- Anno 2018
  - Mese n. 1
  - ...
  - Mese n. 6

## <a name="query-set-of-parquet-files"></a>Set di query di file di parquet

È possibile specificare solo le colonne di interesse quando si esegue una query sui file di parquet.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Inferenza automatica dello schema

Non è necessario utilizzare la clausola OPENROWSET WITH durante la lettura dei file di parquet. I nomi di colonna e i tipi di dati vengono letti automaticamente dai file Parquet.

L'esempio seguente mostra le funzionalità di inferenza automatica dello schema per i file Diquet. Restituisce il numero di righe a settembre 2017 senza specificare uno schema.

> [!NOTE]
> Non è necessario specificare colonne nella clausola OPENROWSET WITH durante la lettura dei file Diquet. In tal caso, il servizio query SQL su richiesta utilizzerà i metadati nel file di parquet e associa le colonne in base al nome.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Eseguire query su dati partizionatiQuery partitioned data

Il set di dati fornito in questo esempio è suddiviso in sottocartelle separate. È possibile scegliere come destinazione partizioni specifiche utilizzando la funzione filepath. Questo esempio mostra gli importi delle tariffe per anno, mese e payment_type per i primi tre mesi del 2017.

> [!NOTE]
> La query su richiesta SQL è compatibile con lo schema di partizionamento Hive/Hadoop.The SQL on-demand Query is compatible with Hive/Hadoop partitioning scheme.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Mapping dei tipi

I file di parquet contengono descrizioni dei tipi per ogni colonna. Nella tabella seguente viene descritto come i tipi di parquet vengono mappati ai tipi nativi SQL.

| Tipo di parquet | Parquet tipo logico (annotazione) | Tipo di dati SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY / BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | real |
| INT32 (inT32) | | INT |
| INT64 | | bigint |
| INT96 (inT96) | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |BINARY |
| BINARY |UTF8 |varchar \*(regole di confronto UTF8) |
| BINARY |STRING |varchar \*(regole di confronto UTF8) |
| BINARY |Enum|varchar \*(regole di confronto UTF8) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(max) \*(regole di confronto UTF8) |
| BINARY |BION |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar(max), serializzato in formato standardizzato |
| INT32 (inT32) |INT(8, vero) |SMALLINT |
| INT32 (inT32) |INT(16, vero) |SMALLINT |
| INT32 (inT32) |INT(32, vero) |INT |
| INT32 (inT32) |INT(8, falso) |TINYINT |
| INT32 (inT32) |INT(16, falso) |INT |
| INT32 (inT32) |INT(32, falso) |bigint |
| INT32 (inT32) |DATE |Data |
| INT32 (inT32) |DECIMAL |decimal |
| INT32 (inT32) |TEMPO (MILLIS)|time |
| INT64 |INT(64, vero) |bigint |
| INT64 |INT(64, falso ) |decimale(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TEMPO (MICROS / NANOS) |time |
|INT64 |TIMESTAMP (MILLIS / MICROS / NANOS) |datetime2 |
|[Tipo complesso](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |INSERZIONE |varchar(max), serializzato in JSON |
|[Tipo complesso](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(max), serializzato in JSON |

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come eseguire query sui [tipi nidificati](query-parquet-nested-types.md).
