---
title: Eseguire query su file Parquet con SQL su richiesta (anteprima)
description: In questo articolo si apprenderà come eseguire query su file Parquet con SQL su richiesta (anteprima).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e9731b869b20c7d8cfc3b1e234711c818a2b7422
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744244"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Eseguire query su file Parquet con SQL su richiesta (anteprima) in Azure Synapse Analytics

In questo articolo si apprenderà come scrivere una query con SQL su richiesta (anteprima) che legga file Parquet.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel **creare un database** con un'origine dati che faccia riferimento all'account di archiviazione [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Inizializzare quindi gli oggetti eseguendo uno [script di configurazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Questo script di configurazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni usati in questi esempi.

## <a name="dataset"></a>Set di dati

In questo esempio viene usato il set di dati di [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). È possibile eseguire query sui file Parquet nello stesso modo in cui si [leggono i file CSV](query-parquet-files.md). L'unica differenza è che il parametro `FILEFORMAT` deve essere impostato su `PARQUET`. Gli esempi in questo articolo illustrano le specifiche relative alla lettura di file Parquet.

## <a name="query-set-of-parquet-files"></a>Set di query di file Parquet

Quando si eseguono query su file Parquet, è possibile specificare solo le colonne di interesse.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Inferenza automatica dello schema

Quando si leggono file Parquet, non è necessario usare la clausola OPENROWSET WITH. I nomi di colonna e i tipi di dati vengono letti automaticamente dai file Parquet.

L'esempio seguente illustra le funzionalità di inferenza automatica dello schema per i file Parquet. L'esempio restituisce il numero di righe per settembre 2017 senza specificare uno schema.

> [!NOTE]
> Quando si leggono file Parquet, non è necessario specificare colonne nella clausola OPENROWSET WITH. In questo caso, il Servizio Query di SQL su richiesta usa i metadati del file Parquet e associa le colonne in base al nome.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Eseguire query su dati partizionati

Il set di dati usato in questo esempio è diviso (partizionato) in sottocartelle separate. È possibile indicare come destinazione partizioni specifiche usando la funzione filepath. Questo esempio illustra gli importi delle tariffe per anno, mese e payment_type per i primi tre mesi del 2017.

> [!NOTE]
> La query SQL su richiesta è compatibile con lo schema di partizione Hive/Hadoop.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Mapping dei tipi

I file Parquet contengono descrizioni dei tipi per ogni colonna. La tabella seguente descrive il mapping dei tipi Parquet ai tipi SQL nativi.

| Tipo Parquet | Tipo logico Parquet (annotazione) | Tipo di dati SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
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
| BINARY |JSON |varchar(max) \*(regole di confronto UTF8) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar(max), serializzato in formato standardizzato |
| INT32 |INT(8, true) |SMALLINT |
| INT32 |INT(16, true) |SMALLINT |
| INT32 |INT(32, true) |INT |
| INT32 |INT(8, false) |TINYINT |
| INT32 |INT(16, false) |INT |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |Data |
| INT32 |DECIMAL |decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TIME (MICROS/NANOS) |time |
|INT64 |TIMESTAMP (MILLIS/MICROS/NANOS) |datetime2 |
|[Tipo complesso](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |INSERZIONE |varchar(max), serializzato in JSON |
|[Tipo complesso](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(max), serializzato in JSON |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come [eseguire query su tipi nidificati Parquet](query-parquet-nested-types.md), passare al prossimo articolo.
