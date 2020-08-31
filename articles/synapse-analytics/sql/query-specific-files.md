---
title: Uso dei metadati dei file nelle query
description: La funzione OPENROWSET fornisce informazioni su file e percorsi per ogni file usato nella query per filtrare o analizzare i dati in base al nome file e/o al percorso della cartella.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 04a308805dd69c4d9245d287a391f1e7fa7d50f2
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055567"
---
# <a name="use-file-metadata-in-queries"></a>Usare i metadati dei file nelle query

SQL su richiesta può indirizzare più file e cartelle, come descritto nell'articolo [cartelle query e più file](query-folders-multiple-csv-files.md) . In questo articolo verrà illustrato come usare le informazioni sui metadati relative a nomi di file e cartelle nelle query.

In alcuni casi, potrebbe essere necessario individuare l'origine del file o della cartella correlata a una riga specifica nel set di risultati.

È possibile usare la funzione `filepath` e `filename` per restituire i nomi file e/o il percorso nel set di risultati. In alternativa, è possibile usarli per filtrare i dati in base al nome file e/o al percorso della cartella. Queste funzioni sono descritte nella sezione relativa alla sintassi della [funzione filename](query-data-storage.md#filename-function) e della [funzione filepath](query-data-storage.md#filepath-function). Di seguito sono riportate brevi descrizioni insieme agli esempi.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel **creare un database** con un'origine dati che fa riferimento all'account di archiviazione. Inizializzare quindi gli oggetti eseguendo uno [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Questo script di installazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni usati in questi esempi.

## <a name="functions"></a>Funzioni

### <a name="filename"></a>Nome file

Questa funzione restituisce il nome del file da cui ha origine la riga.

L'esempio seguente legge i file di dati dei taxi di New York per gli ultimi tre mesi del 2017 e restituisce il numero di corse per ogni file. La parte OPENROWSET della query specifica i file che verranno letti.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

L'esempio seguente mostra come usare *filename()* nella clausola WHERE per filtrare i file da leggere. Accede all'intera cartella nella parte OPENROWSET della query e filtra i file nella clausola WHERE.

I risultati saranno identici a quelli dell'esempio precedente.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

La funzione filepath restituisce un percorso completo o parziale:

- Se viene chiamata senza un parametro, restituisce il percorso completo del file da cui ha origine la riga.
- Se viene chiamata con un parametro, viene restituita una parte del percorso che corrisponde al carattere jolly nella posizione specificata nel parametro. Ad esempio, il valore del parametro 1 restituisce la parte del percorso che corrisponde al primo carattere jolly.

L'esempio seguente legge i file di dati dei taxi di New York per gli ultimi tre mesi del 2017. Restituisce il numero di corse per ogni percorso file. La parte OPENROWSET della query specifica i file che verranno letti.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

L'esempio seguente mostra come usare *filepath()* nella clausola WHERE per filtrare i file da leggere.

È possibile usare i caratteri jolly nella parte OPENROWSET della query e filtrare i file nella clausola WHERE. I risultati saranno identici a quelli dell'esempio precedente.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
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

Nell'articolo successivo si imparerà a [eseguire query su file Parquet](query-parquet-files.md).
