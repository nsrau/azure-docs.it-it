---
title: Eseguire query su cartelle e più file usando SQL su richiesta (anteprima)
description: SQL su richiesta (anteprima) supporta la lettura di più file/cartelle utilizzando caratteri jolly, che sono simili ai caratteri jolly utilizzati nel sistema operativo Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 6c61bd420121800ade48de88cbcaadf37343262d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207632"
---
# <a name="query-folders-and-multiple-files"></a>Eseguire query su cartelle e più file  

In questo articolo si apprenderà come scrivere una query usando SQL su richiesta (anteprima) in Azure Synapse Analytics.

SQL su richiesta supporta la lettura di più file/cartelle utilizzando caratteri jolly, che sono simili ai caratteri jolly utilizzati nel sistema operativo Windows. Tuttavia, è presente una maggiore flessibilità perché sono consentiti più caratteri jolly.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel **creare un database** in cui verranno eseguite le query. Inizializzare quindi gli oggetti eseguendo uno [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Questo script di configurazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni usati in questi esempi.

Per seguire le query di esempio, si userà la cartella *CSV/taxi* . Contiene i dati di NYC Taxi-yellow taxi trip da luglio 2016 a giugno 2018. I file in *formato CSV/taxi* sono denominati dopo l'anno e il mese usando il modello seguente: yellow_tripdata_ <year> - <month> . csv

## <a name="read-all-files-in-folder"></a>Leggi tutti i file nella cartella
    
Nell'esempio seguente vengono letti tutti i file di dati di NYC yellow taxi dalla cartella *CSV/taxi* e viene restituito il numero totale di passeggeri e corse all'anno. Viene inoltre illustrato l'utilizzo delle funzioni di aggregazione.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tutti i file a cui si accede con l'unico OPENROWSET devono avere la stessa struttura, ad esempio il numero di colonne e i relativi tipi di dati.

### <a name="read-subset-of-files-in-folder"></a>Leggi subset di file nella cartella

L'esempio seguente legge i file di dati dei taxi gialli 2017 NYC dalla cartella *CSV/taxi* usando un carattere jolly e restituisce l'importo totale della tariffa per ogni tipo di pagamento.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Tutti i file a cui si accede con l'unico OPENROWSET devono avere la stessa struttura, ad esempio il numero di colonne e i relativi tipi di dati.

## <a name="read-folders"></a>Leggi cartelle

Il percorso fornito a OPENROWSET può anche essere un percorso di una cartella. Le sezioni seguenti includono questi tipi di query.

### <a name="read-all-files-from-specific-folder"></a>Leggi tutti i file da una cartella specifica

È possibile leggere tutti i file in una cartella usando il carattere jolly a livello di file, come illustrato in [leggere tutti i file nella cartella](#read-all-files-in-folder). Tuttavia, esiste un modo per eseguire una query su una cartella e utilizzare tutti i file all'interno di tale cartella.

Se il percorso fornito in OPENROWSET punta a una cartella, tutti i file in tale cartella verranno usati come origine per la query. Nella query seguente vengono letti tutti i file nella cartella *CSV/taxi* .

> [!NOTE]
> Si noti l'esistenza di/alla fine del percorso nella query seguente. Indica una cartella. Se il valore di/viene omesso, la query verrà destinata a un file denominato *taxi* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> Tutti i file a cui si accede con l'unico OPENROWSET devono avere la stessa struttura, ad esempio il numero di colonne e i relativi tipi di dati.

### <a name="read-all-files-from-multiple-folders"></a>Leggi tutti i file da più cartelle

È possibile leggere i file da più cartelle usando un carattere jolly. Con la query seguente vengono letti tutti i file di tutte le cartelle presenti nella cartella *CSV* i cui nomi iniziano con *t* e terminano con *i*.

> [!NOTE]
> Si noti l'esistenza di/alla fine del percorso nella query seguente. Indica una cartella. Se l'oggetto/viene omesso, la query verrà destinata ai file denominati *t &ast; i* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> Tutti i file a cui si accede con l'unico OPENROWSET devono avere la stessa struttura, ad esempio il numero di colonne e i relativi tipi di dati.

Poiché è presente una sola cartella che soddisfa i criteri, il risultato della query corrisponde a [quello di tutti i file nella cartella](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Più caratteri jolly

È possibile usare più caratteri jolly a livelli di percorso diversi. È ad esempio possibile arricchire la query precedente per leggere i file solo con 2017 dati, da tutte le cartelle i cui nomi iniziano con *t* e terminano con *i*.

> [!NOTE]
> Si noti l'esistenza di/alla fine del percorso nella query seguente. Indica una cartella. Se l'oggetto/viene omesso, la query verrà destinata ai file denominati *t &ast; i* .
> È previsto un limite massimo di 10 caratteri jolly per ogni query.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> Tutti i file a cui si accede con l'unico OPENROWSET devono avere la stessa struttura, ad esempio il numero di colonne e i relativi tipi di dati.

Poiché è presente una sola cartella che soddisfa i criteri, il risultato della query è uguale a quello [di Read subset di file nella cartella](#read-subset-of-files-in-folder) e di [leggere tutti i file da una cartella specifica](#read-all-files-from-specific-folder). Gli scenari di utilizzo dei caratteri jolly più complessi sono descritti in [file parquet di query](query-parquet-files.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sono disponibili nell'articolo in [file specifici di query](query-specific-files.md) .
