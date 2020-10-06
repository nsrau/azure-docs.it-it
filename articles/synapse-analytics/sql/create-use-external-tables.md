---
title: Creare e usare tabelle esterne in SQL su richiesta (anteprima)
description: Questa sezione illustra come creare e usare tabelle esterne in SQL su richiesta (anteprima).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 182deba959144f6a3992bb41243f29023bad5e5c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289328"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Creare e usare tabelle esterne in SQL su richiesta (anteprima) con Azure Synapse Analytics

Questa sezione illustra come creare e usare [tabelle esterne](develop-tables-external-tables.md) in SQL su richiesta (anteprima). Le tabelle esterne sono utili se si vuole controllare l'accesso ai dati esterni in SQL su richiesta e se si vogliono usare strumenti, come Power BI, in combinazione con SQL su richiesta. Le tabelle esterne possono accedere a due tipi di archiviazione:
- Archiviazione pubblica, che consente agli utenti di accedere ai file archiviati pubblicamente.
- Archiviazione protetta, in cui gli utenti accedono ai file di archiviazione usando le credenziali SAS, l'identità Azure AD o l'identità gestita dell'area di lavoro Synapse.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel creare un database in cui verranno create le tabelle. Inizializzare quindi gli oggetti eseguendo uno [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Tale script creerà gli oggetti seguenti usati in questo esempio:
- CREDENZIALI IN AMBITO DATABASE `sqlondemand` che consentono l'accesso all'account di archiviazione di Azure `https://sqlondemandstorage.blob.core.windows.net` protetto da SAS.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- ORIGINE DATI ESTERNA `sqlondemanddemo` che fa riferimento all'account di archiviazione demo protetto con chiave SAS e ORIGINE DATI ESTERNA `YellowTaxi` che fa riferimento all'account di archiviazione di Azure disponibile pubblicamente in `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/`.

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- Formati di file `QuotedCSVWithHeaderFormat` e `ParquetFormat` che descrivono i tipi di file CSV e Parquet.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

Le query in questo articolo vengono eseguite nel database di esempio e usano tali oggetti. 

## <a name="create-an-external-table-on-protected-data"></a>Creare una tabella esterna su dati protetti

È possibile creare tabelle esterne che accedono ai dati in un account di archiviazione di Azure che consente l'accesso agli utenti con identità Azure AD o chiave SAS. È possibile creare tabelle esterne nello stesso modo in cui si creano le normali tabelle esterne di SQL Server. 

La query seguente crea una tabella esterna che legge il file *population.csv* dall'account di archiviazione di Azure SynapseSQL demo a cui viene fatto riferimento tramite l'origine dati `sqlondemanddemo` e che viene protetto con credenziali in ambito database denominate `sqlondemand`. 

Le credenziali dell'origine dati e in ambito database vengono create nello [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql).

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Creare una tabella esterna su dati pubblici

È possibile creare tabelle esterne che leggono i dati dai file posizionati nell'archiviazione di Azure disponibile pubblicamente. Questo [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) crea l'origine dati esterna e la definizione del formato di file Parquet usate nella query seguente:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Usare una tabella esterna

È possibile usare [tabelle esterne](develop-tables-external-tables.md) nelle query nello stesso modo in cui si usano nelle query di SQL Server.

Nella query seguente viene illustrato l'uso della tabella esterna *population* creata nella sezione precedente. La query restituisce i nomi dei paesi/aree geografiche con la popolazione del 2019 in ordine decrescente.

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come archiviare i risultati di una query nell'archiviazione, vedere l'articolo [Archiviare i risultati della query nell'archiviazione](../sql/create-external-table-as-select.md).
