---
title: Eseguire query sui file parquet usando un pool SQL senza server (anteprima)
description: Questo articolo illustra come eseguire query su file parquet usando un pool SQL senza server (anteprima).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3559b3724d14be6aade07c4884190afce30c0715
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93306863"
---
# <a name="query-parquet-files-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Eseguire query sui file parquet usando un pool SQL senza server (anteprima) in Azure sinapsi Analytics

In questo articolo verrà illustrato come scrivere una query usando un pool SQL senza server (anteprima) che leggerà i file parquet.

## <a name="quickstart-example"></a>Esempio di Guida introduttiva

`OPENROWSET` la funzione consente di leggere il contenuto del file parquet fornendo l'URL del file.

### <a name="read-parquet-file"></a>Leggi file parquet

Il modo più semplice per visualizzare il contenuto del `PARQUET` file consiste nel fornire l'URL del file per la `OPENROWSET` funzione e specificare parquet `FORMAT` . Se il file è disponibile pubblicamente o se l'identità del Azure AD può accedere a questo file, dovrebbe essere possibile visualizzare il contenuto del file usando la query come quella illustrata nell'esempio seguente:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Assicurarsi di accedere a questo file. Se il file è protetto con la chiave SAS o con identità Azure personalizzata, è necessario configurare le [credenziali a livello di server per l'accesso SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

### <a name="data-source-usage"></a>Utilizzo dell'origine dati

Nell'esempio precedente viene usato il percorso completo del file. In alternativa, è possibile creare un'origine dati esterna con il percorso che punta alla cartella radice dell'archiviazione e usare tale origine dati e il percorso relativo del file nella `OPENROWSET` funzione:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Se un'origine dati è protetta con la chiave SAS o l'identità personalizzata, è possibile configurare l' [origine dati con le credenziali con ambito database](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Specifica in modo esplicito lo schema

`OPENROWSET` consente di specificare in modo esplicito le colonne che si desidera leggere dalla clausola using del file `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

Nelle sezioni seguenti è possibile vedere come eseguire query su diversi tipi di file PARQUET.

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
> Quando si leggono file Parquet, non è necessario specificare colonne nella clausola OPENROWSET WITH. In tal caso, il servizio query del pool SQL senza server utilizzerà i metadati nel file parquet e legherà le colonne in base al nome.

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
> La query del pool SQL senza server è compatibile con lo schema di partizionamento hive/Hadoop.

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

Per il mapping dei tipi parquet al mapping dei tipi di controllo di tipo nativo SQL [per parquet](develop-openrowset.md#type-mapping-for-parquet).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come [eseguire query su tipi nidificati Parquet](query-parquet-nested-types.md), passare al prossimo articolo.
