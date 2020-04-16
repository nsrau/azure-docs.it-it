---
title: Eseguire query su file CSV tramite SQL su richiesta (anteprima)Query CSV files using SQL on-demand (preview)
description: In questo articolo verrà illustrato come eseguire query su singoli file CSV con formati di file diversi utilizzando SQL on-demand (anteprima).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431592"
---
# <a name="query-csv-files"></a>Interrogare i file CSV

In this article, you'll learn how to query a single CSV file using SQL on-demand (preview) in Azure Synapse Analytics. I file CSV possono avere formati diversi: 

- Con e senza una riga di intestazione
- Valori delimitati da virgole e tabulazioni
- Finali delle righe di stile Windows e Unix
- Valori non racchiusi tra virgolette e caratteri di caratteri di scappamento

Tutte le variazioni di cui sopra saranno trattate di seguito.

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere il resto di questo articolo, leggere i seguenti articoli:

- [Configurazione al primo tempo](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Nuova riga stile Windows

La query seguente mostra come leggere un file CSV senza una riga di intestazione, con una nuova riga in stile Windows e colonne delimitate da virgole.

Anteprima file:

![Le prime 10 righe del file CSV senza intestazione, la nuova riga in stile Windows.](./media/query-single-csv-file/population.png)

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

## <a name="unix-style-new-line"></a>Nuova linea in stile Unix

La query seguente mostra come leggere un file senza una riga di intestazione, con una nuova riga in stile Unix e colonne delimitate da virgole. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Le prime 10 righe del file CSV senza riga di intestazione e con la nuova riga Unix-Style.](./media/query-single-csv-file/population-unix.png)

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

Nella query seguente viene illustrato come leggere un file di lettura con una riga di intestazione, con una nuova riga in stile Unix e colonne delimitate da virgole. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Le prime 10 righe del file CSV con riga di intestazione e con la nuova riga Unix-Style.](./media/query-single-csv-file/population-unix-hdr.png)

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

## <a name="custom-quote-character"></a>Carattere di citazione personalizzato

Nella query seguente viene illustrato come leggere un file con una riga di intestazione, con una nuova riga in stile Unix, colonne delimitate da virgole e valori tra virgolette. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Prime 10 righe del file CSV con riga di intestazione e con la nuova riga Unix-Style e i valori tra virgolette.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

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
> Questa query restituirebbe gli stessi risultati se omettessi il parametro FIELDQUOTE perché il valore predefinito per FIELDQUOTE è un'virgoletta doppia.

## <a name="escaping-characters"></a>Utilizzo di caratteri di escape

La query seguente mostra come leggere un file con una riga di intestazione, con una nuova riga di tipo Unix, colonne delimitate da virgole e un carattere di escape utilizzato per il delimitatore di campo (virgola) all'interno di valori. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Le prime 10 righe del file CSV con riga di intestazione e con Unix-Style nuova riga e escape char utilizzato per il delimitatore di campo.](./media/query-single-csv-file/population-unix-hdr-escape.png)

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
> Questa query avrebbe esito negativo se ESCAPECHAR non è specificato poiché la virgola in "Slov,enia" verrebbe considerata come delimitatore di campo anziché parte del nome del paese. "Slov,enia" verrebbe considerato come due colonne. Pertanto, la riga specifica avrebbe una colonna più delle altre righe e una colonna più di quanto definito nella clausola WITH.

## <a name="tab-delimited-files"></a>File delimitati da tabulazioni

Nella query seguente viene illustrato come leggere un file con una riga di intestazione, con una nuova riga in stile Unix e colonne delimitate da tabulazioni. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Le prime 10 righe del file CSV con riga di intestazione e con Unix-Style nuova riga e delimitatore di tabulazione.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

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

Finora, hai specificato lo schema del file CSV usando WITH ed elencando tutte le colonne. È possibile specificare solo le colonne effettivamente necessarie nella query utilizzando un numero ordinale per ogni colonna necessaria. Ometterai anche le colonne di nessun interesse.

La query seguente restituisce il numero di nomi di paesi distinti in un file, specificando solo le colonne necessarie:

> [!NOTE]
> Osservare la clausola WITH nella query seguente e notare che c'è "2" (senza virgolette) alla fine della riga in cui si definisce la colonna *[country_name].* Significa che la colonna *[country_name]* è la seconda colonna del file. La query ignorerà tutte le colonne del file ad eccezione della seconda.

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

I prossimi articoli ti mostreranno come:

- [Esecuzione di query sui file Parquet](query-parquet-files.md)
- [Esecuzione di query su cartelle e più file](query-folders-multiple-csv-files.md)
