---
title: Eseguire query su file parquet con SQL su richiesta (anteprima)
description: Questo articolo illustra come eseguire query su file parquet usando SQL su richiesta (anteprima).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431696"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Eseguire query su file parquet usando SQL su richiesta (anteprima) in Azure sinapsi Analytics

In questo articolo verrà illustrato come scrivere una query usando SQL su richiesta (anteprima) che leggerà i file parquet.

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere il resto di questo articolo, vedere gli articoli seguenti:

- [Prima configurazione](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Set di dati

È possibile eseguire query su file parquet nello stesso modo in cui si leggono i file CSV. L'unica differenza è che il parametro FileFormat deve essere impostato su PARQUET. Gli esempi in questo articolo illustrano le specifiche relative alla lettura di file parquet.

> [!NOTE]
> Non è necessario specificare le colonne nella clausola OPENROWSET WITH durante la lettura dei file parquet. SQL su richiesta utilizzerà i metadati nel file parquet e legherà le colonne in base al nome.

Per le query di esempio, si userà la cartella *parquet/taxi* . Contiene i dati relativi ai taxi di NYC Taxi-yellow Trip registrati dal 2016 luglio. al 2018 giugno.

I dati vengono partizionati per anno e mese e la struttura di cartelle è la seguente:

- anno = 2016
  - mese = 6
  - ...
  - mese = 12
- anno = 2017
  - mese = 1
  - ...
  - mese = 12
- anno = 2018
  - mese = 1
  - ...
  - mese = 6

## <a name="query-set-of-parquet-files"></a>Set di query di file parquet

È possibile specificare solo le colonne di interesse quando si eseguono query sui file parquet.

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

Non è necessario usare la clausola OPENROWSET WITH durante la lettura dei file parquet. I nomi di colonna e i tipi di dati vengono letti automaticamente da file parquet.

Nell'esempio seguente vengono illustrate le funzionalità di inferenza automatica dello schema per i file parquet. Restituisce il numero di righe di settembre 2017 senza specificare uno schema.

> [!NOTE]
> Non è necessario specificare le colonne nella clausola OPENROWSET WITH durante la lettura dei file parquet. In tal caso, il servizio query SQL su richiesta utilizzerà i metadati nel file parquet e legherà le colonne in base al nome.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Eseguire query sui dati partizionati

Il set di dati fornito in questo esempio viene diviso (partizionato) in sottocartelle separate. È possibile fare riferimento a partizioni specifiche usando la funzione FilePath. Questo esempio Mostra gli importi delle tariffe per anno, mese e payment_type per i primi tre mesi di 2017.

> [!NOTE]
> La query SQL su richiesta è compatibile con lo schema di partizionamento hive/Hadoop.

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

I file parquet contengono descrizioni dei tipi per ogni colonna. La tabella seguente descrive come viene eseguito il mapping dei tipi parquet ai tipi SQL nativi.

| Tipo parquet | Tipo logico parquet (annotazione) | Tipo di dati SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARIO/BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | real |
| INT32 | | INT |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |BINARY |
| BINARY |UTF8 |varchar \*(regole di confronto UTF8) |
| BINARY |STRING |varchar \*(regole di confronto UTF8) |
| BINARY |ENUM|varchar \*(regole di confronto UTF8) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar (max) \*(regole di confronto UTF8) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar (max), serializzato in formato standardizzato |
| INT32 |INT (8, true) |SMALLINT |
| INT32 |INT (16, true) |SMALLINT |
| INT32 |INT (32, true) |INT |
| INT32 |INT (8, false) |TINYINT |
| INT32 |INT (16, false) |INT |
| INT32 |INT (32, false) |bigint |
| INT32 |DATE |Data |
| INT32 |DECIMAL |decimal |
| INT32 |TEMPO (MILLIS)|time |
| INT64 |INT (64, true) |bigint |
| INT64 |INT (64, false) |Decimal (20, 0) |
| INT64 |DECIMAL |decimal |
| INT64 |TEMPO (MICROS/NANO) |time |
|INT64 |TIMESTAMP (MILLIS/MICROS/NANOS) |datetime2 |
|[Tipo complesso](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |INSERZIONE |varchar (max), serializzato in JSON |
|[Tipo complesso](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar (max), serializzato in JSON |

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come [eseguire una query sui tipi annidati parquet](query-parquet-nested-types.md).
