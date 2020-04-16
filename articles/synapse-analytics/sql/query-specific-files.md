---
title: Utilizzo dei metadati dei file nelle query
description: La funzione OPENROWSET fornisce informazioni su file e percorso su ogni file utilizzato nella query per filtrare o analizzare i dati in base al nome e/o al percorso della cartella.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431553"
---
# <a name="using-file-metadata-in-queries"></a>Utilizzo dei metadati dei file nelle query

Il servizio query su richiesta SQL è in grado di indirizzare più file e cartelle, come descritto nell'articolo [Query folders and multiple files (Informazioni](query-folders-multiple-csv-files.md) in lingua inglese). In questo articolo viene illustrato come utilizzare le informazioni sui metadati relative ai nomi di file e cartelle nelle query.

A volte, potrebbe essere necessario sapere quale origine di file o cartelle è correlata a una riga specifica nel set di risultati.

È possibile `filepath` utilizzare `filename` function e restituire i nomi dei file e/o il percorso nel set di risultati. In alternativa, è possibile utilizzarli per filtrare i dati in base al nome e/o al percorso della cartella. Queste funzioni sono descritte nella sezione relativa alla sintassi [filename function](develop-storage-files-overview.md#filename-function) e [filepath function](develop-storage-files-overview.md#filepath-function). Qui di seguito troverete brevi descrizioni lungo i campioni.

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere il resto di questo articolo, esaminare i prerequisiti seguenti:

- [Configurazione al primo tempo](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="functions"></a>Funzioni

### <a name="filename"></a>Nome file

Questa funzione restituisce il nome del file da cui ha origine la riga.

L'esempio seguente legge i file di dati di NYC Yellow Taxi per gli ultimi tre mesi del 2017 e restituisce il numero di corse per file. La parte OPENROWSET della query specifica quali file verranno letti.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

L'esempio seguente mostra come *utilizzare filename()* nella clausola WHERE per filtrare i file da leggere. Accede all'intera cartella nella parte OPENROWSET della query e filtra i file nella clausola WHERE.

I risultati saranno gli stessi dell'esempio precedente.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

La funzione filepath restituisce un percorso completo o parziale:The filepath function returns a full or partial path:

- Quando viene chiamato senza un parametro, restituisce il percorso completo del file da cui ha origine la riga.
- Quando viene chiamato con un parametro, restituisce parte del percorso che corrisponde al carattere jolly nella posizione specificata nel parametro. Ad esempio, il valore del parametro 1 restituirebbe parte del percorso che corrisponde al primo carattere jolly.

L'esempio seguente legge i file di dati di NYC Yellow Taxi per gli ultimi tre mesi del 2017. Restituisce il numero di corse per percorso di file. La parte OPENROWSET della query specifica quali file verranno letti.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

L'esempio seguente mostra come *utilizzare filepath()* nella clausola WHERE per filtrare i file da leggere.

È possibile utilizzare i caratteri jolly nella parte OPENROWSET della query e filtrare i file nella clausola WHERE. I risultati saranno gli stessi dell'esempio precedente.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Passaggi successivi

Nel prossimo articolo, imparerai come eseguire una query sui [file di parquet.](query-parquet-files.md)
