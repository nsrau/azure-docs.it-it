---
title: Eseguire query su file CSV con SQL su richiesta (anteprima)
description: In questo articolo si apprenderà come eseguire una query su singoli file CSV con formati di file diversi usando SQL su richiesta (anteprima).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431592"
---
# <a name="query-csv-files"></a>Eseguire query sui file CSV

In questo articolo si apprenderà come eseguire una query su un singolo file CSV usando SQL su richiesta (anteprima) in Azure sinapsi Analytics. I file CSV possono avere formati diversi: 

- Con e senza una riga di intestazione
- Valori delimitati da virgole e tabulazioni
- Terminazioni riga stile Windows e UNIX
- Valori non delimitati da virgolette e caratteri di escape

Tutte le varianti precedenti verranno descritte di seguito.

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere la parte restante di questo articolo, vedere gli articoli seguenti:

- [Prima configurazione](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Nuova riga stile Windows

Nella query seguente viene illustrato come leggere un file CSV senza una riga di intestazione, con una nuova riga di tipo Windows e colonne delimitate da virgole.

Anteprima file:

![Prime 10 righe del file CSV senza intestazione, nuova riga in stile Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
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

Nella query seguente viene illustrato come leggere un file senza una riga di intestazione, con una nuova riga di tipo UNIX e con colonne delimitate da virgole. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV senza riga di intestazione e con nuova riga di tipo UNIX.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
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

Nella query seguente viene illustrato come leggere un file con una riga di intestazione con una nuova riga di tipo UNIX e colonne delimitate da virgole. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV con riga di intestazione e con nuova riga in stile UNIX.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
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

## <a name="custom-quote-character"></a>Carattere virgolette personalizzate

Nella query seguente viene illustrato come leggere un file con una riga di intestazione, con una nuova riga di tipo UNIX, colonne delimitate da virgole e valori tra virgolette. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV con riga di intestazione e con valori di nuova riga e virgolette di tipo UNIX.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
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
> Questa query restituisce gli stessi risultati se è stato omesso il parametro FIELDQUOTE poiché il valore predefinito per FIELDQUOTE è una virgoletta doppia.

## <a name="escaping-characters"></a>Utilizzo di caratteri di escape

La query seguente mostra come leggere un file con una riga di intestazione, con una nuova riga di tipo UNIX, colonne delimitate da virgole e un carattere di escape usato per il delimitatore di campo (virgola) all'interno dei valori. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV con riga di intestazione e con carattere di escape e nuova riga di tipo UNIX usati per il delimitatore di campo.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
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
    country_name = 'Slov,enia';
```

> [!NOTE]
> Questa query avrà esito negativo se ESCAPECHAR non è specificato perché la virgola in "slov, Enia" verrebbe considerata come delimitatore di campo anziché come parte del nome del paese. "Slov, Enia" verrebbe considerato come due colonne. La riga specifica, quindi, avrà una colonna maggiore di altre righe e una colonna maggiore di quella definita nella clausola WITH.

## <a name="tab-delimited-files"></a>File delimitati da tabulazioni

Nella query seguente viene illustrato come leggere un file con una riga di intestazione con una nuova riga di tipo UNIX e colonne delimitate da tabulazioni. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV con riga di intestazione e con nuovo delimitatore di riga e di tabulazione in stile UNIX.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
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

Fino ad ora è stato specificato lo schema del file CSV usando con ed elencando tutte le colonne. È possibile specificare solo le colonne effettivamente necessarie nella query usando un numero ordinale per ogni colonna necessaria. Si ometteranno anche le colonne di nessun interesse.

La query seguente restituisce il numero di nomi di paese distinti in un file, specificando solo le colonne necessarie:

> [!NOTE]
> Osservare la clausola WITH nella query seguente. si noti che c'è "2" (senza virgolette) alla fine della riga in cui si definisce la colonna *[country_name]* . Significa che la colonna *[country_name]* è la seconda colonna del file. La query ignorerà tutte le colonne del file ad eccezione della seconda.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Passaggi successivi

Negli articoli successivi verrà illustrato come:

- [Esecuzione di query sui file parquet](query-parquet-files.md)
- [Esecuzione di query su cartelle e più file](query-folders-multiple-csv-files.md)
