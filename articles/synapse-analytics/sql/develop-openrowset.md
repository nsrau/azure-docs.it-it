---
title: Come usare OPENROWSET in SQL su richiesta (anteprima)
description: Questo articolo descrive la sintassi di OPENROWSET in SQL su richiesta (anteprima) e illustra come usare gli argomenti.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680500"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Come usare OPENROWSET con SQL su richiesta (anteprima)

La funzione OPENROWSET (BULK) consente di accedere ai file di Archiviazione di Azure. All'interno della risorsa SQL su richiesta (anteprima) il provider bulk per set di righe OPENROWSET è accessibile chiamando la funzione OPENROWSET e specificando l'opzione BULK.  

È possibile fare riferimento alla funzione OPENROWSET nella clausola FROM di una query come se fosse una funzione OPENROWSET per un nome di tabella. Supporta le operazioni in blocco tramite un provider BULK predefinito che consente di leggere i dati da un file e restituirli come set di righe.

OPENROWSET non è attualmente supportata nel pool SQL.

## <a name="syntax"></a>Sintassi

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Argomenti

Sono disponibili due opzioni per i file di input che contengono i dati di destinazione per le query. I valori validi sono:

- 'CSV': include qualsiasi file di testo delimitato con separatori di riga e colonna. Come separatore di campo è possibile usare qualsiasi carattere, ad esempio TSV: FIELDTERMINATOR = tab.

- 'PARQUET': file binario in formato Parquet 

**'unstructured_data_path'**

L'elemento unstructured_data_path che stabilisce un percorso dei dati è strutturato come segue:  
'\<prefix>://\<storage_account_path>/\<storage_path>'
 
 
 Di seguito sono riportati i percorsi di account di archiviazione pertinenti che verranno collegati a una specifica origine dati esterna. 

| Origine dati esterna       | Prefisso | Percorso dell'account di archiviazione                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Archiviazione BLOB di Azure         | https  | \<storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>.dfs.core.windows.net              |
||||

'\<storage_path>'

 Specifica un percorso all'interno dell'archiviazione che punta alla cartella o al file da leggere. Se il percorso punta a un contenitore o a una cartella, tutti i file verranno letti da quel particolare contenitore o cartella. I file nelle sottocartelle non verranno inclusi. 
 
 È possibile usare i caratteri jolly per scegliere più file o cartelle come destinazione. È consentito l'utilizzo di più caratteri jolly non consecutivi.
Di seguito è riportato un esempio che legge tutti i file *CSV* che iniziano con *population* da tutte le cartelle che iniziano con */csv/population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Se si specifica l'elemento unstructured_data_path come cartella, una query di SQL su richiesta recupererà i file da tale cartella. 

> [!NOTE]
> A differenza di Hadoop e PolyBase, SQL su richiesta non restituisce le sottocartelle. Inoltre, a differenza di Hadoop e PloyBase, SQL su richiesta restituisce i file il cui nome inizia con un carattere di sottolineatura (_) o un punto (.).

Nell'esempio seguente, se unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`, una query di SQL su richiesta restituirà le righe di mydata.txt e _hidden.txt. Non restituirà mydata2.txt e mydata3.txt perché si trovano in una sottocartella.

![Dati ricorsivi per tabelle esterne](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

La clausola WITH consente di specificare le colonne da leggere nei file.

- Nel caso di file di dati CSV, per leggere tutte le colonne specificare i relativi nomi e tipi di dati. Se si vuole specificare un sottoinsieme di colonne, selezionare le colonne dai file di dati di origine in base a numeri ordinali. Le colonne verranno associate in base alla designazione dell'ordinale. 

> [!IMPORTANT]
> La clausola WITH è obbligatoria per i file CSV.
- Per i file di dati Parquet, specificare i nomi di colonna che corrispondono a quelli dei file di dati di origine. Le colonne verranno associate per nome. Se la clausola WITH viene omessa, verranno restituite tutte le colonne dei file Parquet.

column_name = nome della colonna di output. Se specificato, questo nome sostituisce il nome della colonna nel file di origine.

column_type = tipo di dati della colonna di output. Verrà eseguita la conversione implicita del tipo di dati.

column_ordinal = numero ordinale della colonna nel file o nei file di origine. Questo argomento viene ignorato per i file Parquet perché il binding viene eseguito per nome. Nell'esempio seguente viene restituita una seconda colonna solo da un file CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Specifica il carattere di terminazione del campo da usare. Il carattere di terminazione del campo predefinito è la virgola (" **,** ").

ROWTERMINATOR ='row_terminator'

Specifica il carattere di terminazione della riga da usare. Il carattere di terminazione della riga predefinito è un carattere di nuova riga, ad esempio \r\n.

ESCAPE_CHAR = 'char'

Specifica il carattere nel file usato per l'escape di se stesso e di tutti i valori dei delimitatori del file. Se seguito da un valore diverso da se stesso o da uno qualsiasi dei valori dei delimitatori, il carattere di escape viene eliminato durante la lettura del valore. 

Il parametro ESCAPE_CHAR verrà applicato indipendentemente dal fatto che FIELDQUOTE sia o meno abilitato. Non verrà usato per l'escape del carattere virgolette singole. L'escape del carattere virgolette singole viene eseguito con le virgolette doppie, in linea con il comportamento dei file CSV di Excel.

FIRSTROW = 'first_row' 

Specifica il numero della prima riga da caricare. Il valore predefinito è 1. Questo valore indica la prima riga nel file di dati specificato. I numeri di riga sono determinati dal conteggio dei caratteri di terminazione. FIRSTROW è in base 1.

FIELDQUOTE = 'field_quote' 

Specifica il carattere da usare come carattere virgolette nel file CSV. Se non specificato, viene usato il carattere virgolette ("). 

## <a name="examples"></a>Esempi

L'esempio seguente restituisce solo due colonne con i numeri ordinali 1 e 4 dai file population*.csv. Poiché i file non includono una riga di intestazione, la lettura inizia dalla prima riga:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```



L'esempio seguente restituisce tutte le colonne della prima riga del set di dati di censimento in formato Parquet, senza specificare nomi di colonna e tipi di dati: 

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere le [guide di avvio rapido](query-data-storage.md) o salvare i risultati della query in Archiviazione di Azure usando [CETAS](develop-tables-cetas.md).
