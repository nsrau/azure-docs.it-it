---
title: Eseguire query sui file CSV usando un pool SQL senza server (anteprima)
description: In questo articolo si apprenderà come eseguire una query su singoli file CSV con formati di file diversi usando un pool SQL senza server (anteprima).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 9faff6589466c7cbe78a11c283139acb72bce4bb
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685648"
---
# <a name="query-csv-files"></a>Eseguire query su file CSV

In questo articolo si apprenderà come eseguire una query su un singolo file CSV usando un pool SQL senza server (anteprima) in Azure sinapsi Analytics. I file CSV possono avere formati diversi: 

- Con e senza riga di intestazione
- Valori delimitati da virgole e tabulazioni
- Terminazioni riga in stile Windows e UNIX
- Valori delimitati da virgolette o meno e caratteri di escape

Tutte le varianti precedenti verranno descritte di seguito.

## <a name="quickstart-example"></a>Esempio di Guida introduttiva

`OPENROWSET` la funzione consente di leggere il contenuto del file CSV fornendo l'URL del file.

### <a name="read-a-csv-file"></a>Leggi un file CSV

Il modo più semplice per visualizzare il contenuto del `CSV` file consiste nel fornire l'URL del file per la `OPENROWSET` funzione, specificare csv `FORMAT` e 2,0 `PARSER_VERSION` . Se il file è disponibile pubblicamente o se l'identità del Azure AD può accedere a questo file, dovrebbe essere possibile visualizzare il contenuto del file usando la query come quella illustrata nell'esempio seguente:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

Consente `firstrow` di ignorare la prima riga del file CSV che rappresenta l'intestazione in questo caso. Verificare che sia possibile accedere a questo file. Se il file è protetto con la chiave SAS o l'identità personalizzata, è necessario configurare le [credenziali a livello di server per l'accesso SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Se il file CSV contiene caratteri UTF-8, assicurarsi di utilizzare le regole di confronto del database UTF-8, ad esempio `Latin1_General_100_CI_AS_SC_UTF8` .
> Mancata corrispondenza tra la codifica del testo nel file e le regole di confronto potrebbero causare errori di conversione imprevisti.
> È possibile modificare facilmente le regole di confronto predefinite del database corrente usando l'istruzione T-SQL seguente: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>Utilizzo dell'origine dati

Nell'esempio precedente viene usato il percorso completo del file. In alternativa, è possibile creare un'origine dati esterna con il percorso che punta alla cartella radice dell'archiviazione:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Una volta creata un'origine dati, è possibile utilizzare l'origine dati e il percorso relativo del file nella `OPENROWSET` funzione:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Se un'origine dati è protetta con la chiave SAS o l'identità personalizzata, è possibile configurare l' [origine dati con le credenziali con ambito database](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Specifica in modo esplicito lo schema

`OPENROWSET` consente di specificare in modo esplicito le colonne che si desidera leggere dalla clausola using del file `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

I numeri dopo un tipo di dati nella `WITH` clausola rappresentano l'indice di colonna nel file CSV.

> [!IMPORTANT]
> Se il file CSV contiene caratteri UTF-8, assicurarsi di explicilty specificando alcune regole di confronto UTF-8, ad esempio `Latin1_General_100_CI_AS_SC_UTF8` per tutte le colonne nella `WITH` clausola o impostare alcune regole di confronto UTF-8 a livello di database.
> Mancata corrispondenza tra la codifica del testo nel file e le regole di confronto potrebbero causare errori di conversione imprevisti.
> È possibile modificare facilmente le regole di confronto predefinite del database corrente usando l'istruzione T-SQL seguente: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> È possibile impostare facilmente le regole di confronto sui tipi Colum usando la definizione seguente: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8`

Nelle sezioni seguenti è possibile vedere come eseguire query su diversi tipi di file CSV.

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

## <a name="escape-characters"></a>Caratteri di escape

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

### <a name="escape-quoting-characters"></a>Caratteri di escape per virgolette

Nella query seguente viene illustrato come leggere un file con una riga di intestazione, con una nuova riga di tipo UNIX, colonne delimitate da virgole e un carattere di virgolette doppie con caratteri di escape all'interno dei valori. Si noti la posizione diversa del file rispetto agli altri esempi.

Anteprima file:

![Nella query seguente viene illustrato come leggere un file con una riga di intestazione, con una nuova riga di tipo UNIX, colonne delimitate da virgole e un carattere di virgolette doppie con caratteri di escape all'interno dei valori.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
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
    country_name = 'Slovenia';
```

> [!NOTE]
> Il carattere di virgolette deve essere impostato come escape con un altro carattere di virgolette. Il carattere di virgolette può essere visualizzato all'interno del valore della colonna solo se il valore è incapsulato con caratteri di virgolette.

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

## <a name="return-a-subset-of-columns"></a>Restituisce un subset di colonne

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
