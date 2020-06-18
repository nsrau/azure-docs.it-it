---
title: Eseguire query su file CSV con SQL su richiesta (anteprima)
description: Questo articolo illustra come eseguire una query su singoli file CSV con formati di file diversi usando SQL su richiesta (anteprima).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: f264a62428f919fe23797171926ddf63c585c42b
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234137"
---
# <a name="query-csv-files"></a>Eseguire query su file CSV

Questo articolo illustra come eseguire una query su un singolo file CSV usando SQL su richiesta (anteprima) in Azure Synapse Analytics. I file CSV possono avere formati diversi: 

- Con e senza riga di intestazione
- Valori delimitati da virgole e tabulazioni
- Terminazioni riga in stile Windows e UNIX
- Valori delimitati da virgolette o meno e caratteri di escape

Tutte le varianti precedenti verranno descritte di seguito.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel **creare un database** in cui verranno create le tabelle. Inizializzare quindi gli oggetti eseguendo uno [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Questo script di installazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni usati in questi esempi.

## <a name="windows-style-new-line"></a>Nuova riga in stile Windows

La query seguente mostra come leggere un file CSV senza alcuna riga di intestazione, con nuova riga in stile Windows e colonne delimitate da virgole.

Anteprima file:

![Prime 10 righe del file CSV senza intestazione, nuova riga in stile Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Nuova riga in stile UNIX

La query seguente mostra come leggere un file senza alcuna riga di intestazione, con nuova riga in stile UNIX e colonne delimitate da virgole. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV senza riga di intestazione, con nuova riga in stile UNIX.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Riga di intestazione

La query seguente mostra come leggere un file con una riga di intestazione, con nuova riga in stile UNIX e colonne delimitate da virgole. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV con riga di intestazione e nuova riga in stile UNIX.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Carattere virgolette personalizzato

La query seguente mostra come leggere un file con riga di intestazione, nuova riga in stile UNIX, colonne delimitate da virgole e valori delimitati da virgolette. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV con riga di intestazione, nuova riga in stile UNIX e valori delimitati da virgolette.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Questa query restituisce gli stessi risultati se il parametro FIELDQUOTE è stato omesso, poiché il valore predefinito per FIELDQUOTE è una virgoletta doppia.

## <a name="escaping-characters"></a>Uso di caratteri di escape

La query seguente mostra come leggere un file con riga di intestazione, nuova riga in stile UNIX, colonne delimitate da virgole e l'uso di un carattere di escape per il delimitatore di campo (virgola) all'interno dei valori. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV con riga di intestazione, nuova riga in stile UNIX e carattere di escape usato per il delimitatore di campo.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Questa query avrà esito negativo se il parametro ESCAPECHAR non è specificato perché la virgola in "Slov,enia" verrebbe considerata come delimitatore di campo anziché come parte del nome del Paese/dell'area. "Slov,enia" verrebbe considerato come composto da due colonne. La riga specifica avrà quindi una colonna in più rispetto alle altre righe e una colonna in più rispetto a quelle definite nella clausola WITH.

## <a name="tab-delimited-files"></a>File delimitati da tabulazioni

La query seguente mostra come leggere un file con riga di intestazione, nuova riga in stile UNIX e colonne delimitate da tabulazioni. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV con riga di intestazione, nuova riga in stile UNIX e tabulazioni come delimitatore.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>Restituzione di subset di colonne

Fino ad ora è stato specificato lo schema del file CSV usando il parametro WITH ed elencando tutte le colonne. È possibile specificare solo le colonne effettivamente necessarie nella query usando un numero ordinale per ogni colonna necessaria. Si ometteranno anche le colonne di nessun interesse.

La query seguente restituisce il numero di nomi di Paese/area distinti in un file, specificando solo le colonne necessarie:

> [!NOTE]
> Osservare la clausola WITH nella query seguente. Si noti che è presente "2" (senza virgolette) alla fine della riga in cui si definisce la colonna *[country_name]* . Ciò significa che la colonna *[country_name]* è la seconda colonna del file. La query ignorerà tutte le colonne del file, ad eccezione della seconda.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Passaggi successivi

Negli articoli successivi verrà illustrato come:

- [Eseguire query su file Parquet](query-parquet-files.md)
- [Eseguire query su cartelle e file multipli](query-folders-multiple-csv-files.md)
