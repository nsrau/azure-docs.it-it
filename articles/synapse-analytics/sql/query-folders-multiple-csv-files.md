---
title: Eseguire query su cartelle e più file CSV tramite SQL su richiesta (anteprima)Query folders and multiple CSV files using SQL on-demand (preview)
description: SQL on-demand (anteprima) supporta la lettura di più file/cartelle utilizzando caratteri jolly, che sono simili ai caratteri jolly utilizzati nel sistema operativo Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 070574f0eec40c27eb05cec3bf0a84282b85050f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431800"
---
# <a name="query-folders-and-multiple-csv-files"></a>Eseguire query su cartelle e più file CSV  

In this article, you'll learn how to write a query using SQL on-demand (preview) in Azure Synapse Analytics.

SQL su richiesta supporta la lettura di più file/cartelle utilizzando caratteri jolly, che sono simili ai caratteri jolly utilizzati nel sistema operativo Windows. Tuttavia, è presente una maggiore flessibilità poiché sono consentiti più caratteri jolly.

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere il resto di questo articolo, assicurarsi di leggere gli articoli elencati di seguito:

- [Configurazione al primo tempo](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Leggere più file nella cartella

Si utilizzerà la cartella *csv/taxi* per seguire le query di esempio. Contiene i dati di NYC Taxi - Yellow Taxi Trip Records da luglio 2016 a giugno 2018.

I file in *csv/taxi* prendono il nome dall'anno e dal mese:

- yellow_tripdata_2016.07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

Ogni file ha la seguente struttura:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Leggi tutti i file nella cartella
    
L'esempio seguente legge tutti i file di dati di NYC Yellow Taxi dalla cartella *csv/taxi* e restituisce il numero totale di passeggeri e corse all'anno. Mostra anche l'utilizzo delle funzioni di aggregazione.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
           pickup_location_id INT,'
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tutti i file a cui si accede con il singolo OPENROWSET devono avere la stessa struttura (ad esempio, il numero di colonne e i relativi tipi di dati).

### <a name="read-subset-of-files-in-folder"></a>Leggi sottoinsieme di file nella cartella

L'esempio seguente legge i file di dati di NYC Yellow Taxi 2017 dalla cartella *csv/taxi* utilizzando un carattere jolly e restituisce l'importo totale della tariffa per tipo di pagamento.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Tutti i file a cui si accede con il singolo OPENROWSET devono avere la stessa struttura (ad esempio, il numero di colonne e i relativi tipi di dati).

## <a name="read-folders"></a>Leggere le cartelle

Il percorso fornito a OPENROWSET può anche essere un percorso a una cartella. Le sezioni seguenti includono questi tipi di query.

### <a name="read-all-files-from-specific-folder"></a>Leggere tutti i file da una cartella specifica

È possibile leggere tutti i file in una cartella utilizzando il carattere jolly a livello di file, come mostrato in [Leggi tutti i file nella cartella](#read-all-files-in-folder). Ma, c'è un modo per eseguire una query su una cartella e consumare tutti i file all'interno di tale cartella.

Se il percorso specificato in OPENROWSET punta a una cartella, tutti i file in tale cartella verranno utilizzati come origine per la query. La seguente query leggerà tutti i file nella cartella *csv/taxi.*

> [!NOTE]
> Si noti l'esistenza di / alla fine del percorso nella query seguente. Indica una cartella. Se il / viene omesso, la query sarà destinata a un file denominato *taxi.*

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tutti i file a cui si accede con il singolo OPENROWSET devono avere la stessa struttura (ad esempio, il numero di colonne e i relativi tipi di dati).

### <a name="read-all-files-from-multiple-folders"></a>Leggere tutti i file da più cartelle

È possibile leggere i file da più cartelle utilizzando un carattere jolly. La seguente query leggerà tutti i file da tutte le cartelle che si trovano nella cartella *csv* che hanno nomi che iniziano con *t* e terminano con *i*.

> [!NOTE]
> Si noti l'esistenza di / alla fine del percorso nella query seguente. Indica una cartella. Se il / viene omesso, la query sarà destinato ai file *denominati t&ast;i* invece.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tutti i file a cui si accede con il singolo OPENROWSET devono avere la stessa struttura (ad esempio, il numero di colonne e i relativi tipi di dati).

Poiché si dispone di una sola cartella che corrisponde ai criteri, il risultato della query corrisponde a [Leggi tutti i file nella cartella](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Più caratteri jolly

È possibile utilizzare più caratteri jolly su livelli di percorso diversi. Ad esempio, è possibile arricchire la query precedente per leggere i file solo con dati 2017, da tutte le cartelle i cui nomi iniziano con *t* e terminano con *i*.

> [!NOTE]
> Si noti l'esistenza di / alla fine del percorso nella query seguente. Indica una cartella. Se il / viene omesso, la query sarà destinato ai file *denominati t&ast;i* invece.
> Esiste un limite massimo di 10 caratteri jolly per query.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tutti i file a cui si accede con il singolo OPENROWSET devono avere la stessa struttura (ad esempio, il numero di colonne e i relativi tipi di dati).

Poiché è presente una sola cartella che corrisponde ai criteri, il risultato della query corrisponde a [Leggi sottoinsieme di file nella cartella](#read-subset-of-files-in-folder) e Leggi tutti i file da una cartella [specifica.](#read-all-files-from-specific-folder) Scenari di utilizzo di caratteri jolly più complessi sono descritti in [File di parquet](query-parquet-files.md)di query .

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sono disponibili nell'articolo File specifici della [query.](query-specific-files.md)
