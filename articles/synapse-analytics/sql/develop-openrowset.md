---
title: Come usare OPENROWSET nel pool SQL serverless (anteprima)
description: Questo articolo descrive la sintassi di OPENROWSET nel pool SQL serverless (anteprima) e illustra come usare gli argomenti.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 20003a91726e5ccee7f73d85b7c9a9389801e0ad
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701756"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Creare e usare OPENROWSET con il pool SQL serverless (anteprima) in Azure Synapse Analytics

La funzione `OPENROWSET(BULK...)` consente di accedere ai file di Archiviazione di Azure. La funzione `OPENROWSET` legge il contenuto di un'origine dati remota, ad esempio un file, e lo restituisce come set di righe. All'interno della risorsa pool SQL serverless (anteprima) il provider bulk per set di righe OPENROWSET è accessibile chiamando la funzione OPENROWSET e specificando l'opzione BULK.  

È possibile fare riferimento alla funzione `OPENROWSET` nella clausola `FROM` di una query come se fosse un nome di tabella `OPENROWSET`. Supporta le operazioni in blocco tramite un provider BULK predefinito che consente di leggere i dati da un file e restituirli come set di righe.

## <a name="data-source"></a>Origine dati

La funzione OPENROWSET in Synapse SQL legge il contenuto dei file da un'origine dati. L'origine dati è un account di archiviazione di Azure a cui è possibile fare riferimento esplicitamente nella funzione `OPENROWSET` o che può essere dedotta dinamicamente dall'URL dei file da leggere.
La funzione `OPENROWSET` può facoltativamente contenere un parametro `DATA_SOURCE` per specificare l'origine dati che contiene i file.
- È possibile usare `OPENROWSET` senza `DATA_SOURCE` per leggere direttamente il contenuto dei file dal percorso URL specificato come opzione `BULK`:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Si tratta di un modo semplice e rapido per leggere il contenuto dei file senza preconfigurazione. Questa opzione consente di usare l'opzione di autenticazione di base per accedere all'archiviazione (pass-through di Azure AD per gli account di accesso di Azure AD e token di firma di accesso condiviso per gli account di accesso di SQL). 

- È possibile usare `OPENROWSET` con `DATA_SOURCE` per accedere ai file nell'account di archiviazione specificato:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Questa opzione consente di configurare la posizione dell'account di archiviazione nell'origine dati e di specificare il metodo di autenticazione da usare per accedere all'archiviazione. 
    
    > [!IMPORTANT]
    > `OPENROWSET` senza `DATA_SOURCE` fornisce un metodo semplice e rapido per accedere ai file di archiviazione, ma offre opzioni di autenticazione limitate. Ad esempio, le entità di Azure AD possono accedere ai file solo usando la rispettiva [identità di Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity) e non possono accedere ai file disponibili pubblicamente. Se sono necessarie opzioni di autenticazione più avanzate, usare l'opzione `DATA_SOURCE` e definire le credenziali da usare per accedere all'archiviazione.


## <a name="security"></a>Sicurezza

Per usare la funzione `OPENROWSET`, un utente di database deve avere l'autorizzazione `ADMINISTER BULK OPERATIONS`.

L'amministratore dell'archiviazione deve anche consentire a un utente di accedere ai file fornendo un token di firma di accesso condiviso valido o consentendo all'entità di Azure AD di accedere ai file di archiviazione. Per altre informazioni sul controllo di accesso all'archiviazione, vedere [questo articolo](develop-storage-files-storage-access-control.md).

`OPENROWSET` usa le regole seguenti per determinare la modalità di autenticazione per l'archiviazione:
- In `OPENROWSET` senza `DATA_SOURCE` il meccanismo di autenticazione dipende dal tipo di chiamante.
  - Qualsiasi utente può usare `OPENROWSET` senza `DATA_SOURCE` per leggere i file disponibili pubblicamente in Archiviazione di Azure.
  - Gli account di accesso di Azure AD possono accedere ai file protetti usando la rispettiva [identità di Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) se Archiviazione di Azure consente all'utente di Azure AD di accedere ai file sottostanti, ad esempio se il chiamante ha l'autorizzazione `Storage Reader` in Archiviazione di Azure.
  - Gli account di accesso di SQL possono anche usare `OPENROWSET` senza `DATA_SOURCE` per accedere ai file disponibili pubblicamente, ai file protetti tramite token di firma di accesso condiviso o all'identità gestita dell'area di lavoro di Synapse. È necessario [creare credenziali con ambito server](develop-storage-files-storage-access-control.md#examples) per consentire l'accesso ai file di archiviazione. 
- In `OPENROWSET` con `DATA_SOURCE` il meccanismo di autenticazione è definito nelle credenziali con ambito database assegnate all'origine dati a cui viene fatto riferimento. Questa opzione consente di accedere all'archiviazione disponibile pubblicamente o di accedere all'archiviazione tramite token di firma di accesso condiviso, identità gestita dell'area di lavoro oppure [identità di Azure AD del chiamante](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (se il chiamante è un'entità di Azure AD). Se `DATA_SOURCE` fa riferimento all'archiviazione di Azure non pubblica, sarà necessario [creare credenziali con ambito database](develop-storage-files-storage-access-control.md#examples) e farvi riferimento in `DATA SOURCE` per consentire l'accesso ai file di archiviazione.

Il chiamante deve avere l'autorizzazione `REFERENCES` nelle credenziali per usarle per l'autenticazione per l'archiviazione.

## <a name="syntax"></a>Sintassi

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
```

## <a name="arguments"></a>Argomenti

Sono disponibili due opzioni per i file di input che contengono i dati di destinazione per le query. I valori validi sono:

- 'CSV': include qualsiasi file di testo delimitato con separatori di riga e colonna. Come separatore di campo è possibile usare qualsiasi carattere, ad esempio TSV: FIELDTERMINATOR = tab.

- 'PARQUET': file binario in formato Parquet 

**'unstructured_data_path'**

L'elemento unstructured_data_path che stabilisce un percorso dei dati può essere un percorso assoluto o relativo:
- Il percorso assoluto nel formato '\<prefix>://\<storage_account_path>/\<storage_path>' consente a un utente di leggere direttamente i file.
- Il percorso relativo nel formato '<storage_path>' deve essere usato con il parametro `DATA_SOURCE` e descrive il modello di file all'interno del percorso <storage_account_path> definito in `EXTERNAL DATA SOURCE`. 

Di seguito sono riportati i valori <storage account path> appropriati per collegarsi a una specifica origine dati esterni. 

| Origine dati esterna       | Prefisso | Percorso dell'account di archiviazione                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Archiviazione BLOB di Azure         | http[s]  | \<storage_account>.blob.core.windows.net/path/file   |
| Archiviazione BLOB di Azure         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net /path/file   |
| Azure Data Lake Store Gen2 | aufs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Specifica un percorso all'interno dell'archiviazione che punta alla cartella o al file da leggere. Se il percorso punta a un contenitore o a una cartella, tutti i file verranno letti da quel particolare contenitore o cartella. I file nelle sottocartelle non verranno inclusi. 

È possibile usare i caratteri jolly per scegliere più file o cartelle come destinazione. È consentito l'utilizzo di più caratteri jolly non consecutivi.
Di seguito è riportato un esempio che legge tutti i file *CSV* che iniziano con *population* da tutte le cartelle che iniziano con */csv/population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Se si specifica l'elemento unstructured_data_path come cartella, una query del pool SQL serverless recupererà i file da tale cartella. 

È possibile impostare il pool SQL serverless in modo da attraversare le cartelle specificando /* alla fine del percorso, come nell'esempio seguente: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> A differenza di Hadoop e PolyBase, il pool SQL serverless non restituisce le sottocartelle, a meno che non si specifichi /** alla fine del percorso. Inoltre, a differenza di Hadoop e PolyBase, il pool SQL serverless restituisce i file il cui nome inizia con un carattere di sottolineatura (_) o un punto (.).

Nell'esempio seguente, se unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`, una query del pool SQL serverless restituirà le righe di mydata.txt e _hidden.txt. Non restituirà mydata2.txt e mydata3.txt perché si trovano in una sottocartella.

![Dati ricorsivi per tabelle esterne](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

La clausola WITH consente di specificare le colonne da leggere nei file.

- Nel caso di file di dati CSV, per leggere tutte le colonne specificare i relativi nomi e tipi di dati. Se si vuole specificare un sottoinsieme di colonne, selezionare le colonne dai file di dati di origine in base a numeri ordinali. Le colonne verranno associate in base alla designazione dell'ordinale. 
    > [!TIP]
    > È possibile omettere la clausola WITH anche per i file CSV. I tipi di dati verranno dedotti automaticamente dal contenuto del file. È possibile usare l'argomento HEADER_ROW per specificare l'esistenza della riga di intestazione, da cui verranno letti i nomi delle colonne con distinzione tra maiuscole e minuscole. Per informazioni dettagliate, vedere [Individuazione automatica dello schema](#automatic-schema-discovery).
    
- Per i file di dati Parquet, specificare i nomi di colonna che corrispondono a quelli dei file di dati di origine. Le colonne saranno associate per nome e con distinzione tra maiuscole e minuscole. Se la clausola WITH viene omessa, verranno restituite tutte le colonne dei file Parquet.
    > [!IMPORTANT]
    > I nomi delle colonne nei file Parquet fanno distinzione tra maiuscole e minuscole. Se si specifica il nome della colonna con una combinazione di maiuscole e minuscole diversa rispetto al file Parquet, per tale colonna verranno restituiti i valori NULL.


column_name = nome della colonna di output. Se specificato, questo nome sostituisce il nome della colonna nel file di origine e il nome della colonna specificato nel percorso JSON, se presente. Se json_path non è specificato, viene aggiunto automaticamente come '$.column_name'. Per verificare il comportamento, controllare l'argomento json_path.

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

json_path = [espressione di percorso JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server?view=sql-server-ver15) per una colonna o una proprietà annidata. La [modalità percorso](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server?view=sql-server-ver15#PATHMODE) predefinita è lax.

> [!NOTE]
> In modalità strict la query non riesce generando un errore se il percorso specificato non esiste. In modalità lax la query riesce e l'espressione del percorso JSON restituirà NULL.

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Specifica il carattere di terminazione del campo da usare. Il carattere di terminazione del campo predefinito è la virgola (" **,** ").

ROWTERMINATOR ='row_terminator'

Specifica il carattere di terminazione della riga da usare. Se il carattere di terminazione della riga non è specificato, ne verrà usato uno predefinito. I caratteri di terminazione predefiniti per PARSER_VERSION = '1.0' sono \r\n, \n e \r. I caratteri di terminazione predefiniti per PARSER_VERSION ='2.0' sono \r\n e \n.

ESCAPE_CHAR = 'char'

Specifica il carattere nel file usato per l'escape di se stesso e di tutti i valori dei delimitatori del file. Se seguito da un valore diverso da se stesso o da uno qualsiasi dei valori dei delimitatori, il carattere di escape viene eliminato durante la lettura del valore. 

Il parametro ESCAPE_CHAR verrà applicato indipendentemente dal fatto che FIELDQUOTE sia o meno abilitato. Non verrà usato per l'escape del carattere virgolette singole. Il carattere di virgolette deve essere impostato come escape con un altro carattere di virgolette. Il carattere di virgolette può essere visualizzato all'interno del valore della colonna solo se il valore è incapsulato con caratteri di virgolette.

FIRSTROW = 'first_row' 

Specifica il numero della prima riga da caricare. Il valore predefinito è 1 e indica la prima riga nel file di dati specificato. I numeri di riga sono determinati dal conteggio dei caratteri di terminazione. FIRSTROW è in base 1.

FIELDQUOTE = 'field_quote' 

Specifica il carattere da usare come carattere virgolette nel file CSV. Se non specificato, viene usato il carattere virgolette ("). 

DATA_COMPRESSION = 'data_compression_method'

Specifica il metodo di compressione. Supportato solo in PARSER_VERSION='1.0'. È supportato il metodo di compressione seguente:

- GZIP

PARSER_VERSION = 'parser_version'

Specifica la versione del parser da usare per la lettura dei file. Le versioni del parser CSV attualmente supportate sono 1.0 e 2.0:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

La versione 1.0 del parser CSV è l'impostazione predefinita ed è completa di tutte le funzionalità, mentre la versione 2.0 è progettata per le prestazioni e non supporta tutte le opzioni e le codifiche. 

Specifiche del parser CSV versione 1.0:

- Le opzioni seguenti non sono supportate: HEADER_ROW.

Specifiche del parser CSV versione 2.0:

- Non sono supportati tutti i tipi di dati.
- Il limite massimo delle dimensioni di riga è 8 MB.
- Le opzioni seguenti non sono supportate: DATA_COMPRESSION.
- La stringa vuota racchiusa tra virgolette ("") viene interpretata come stringa vuota.

HEADER_ROW = { TRUE | FALSE }

Specifica se il file CSV contiene o meno una riga di intestazione. L'impostazione predefinita è FALSE. Supportato solo in PARSER_VERSION='2.0'. Se TRUE, i nomi delle colonne verranno letti dalla prima riga in base all'argomento FIRSTROW.

DATAFILETYPE = { 'char' | 'widechar' }

Specifica la codifica: char viene usato per UTF8, widechar per i file UTF16.

## <a name="fast-delimited-text-parsing"></a>Analisi rapida del testo delimitato

È possibile usare due versioni del parser di testo delimitato. La versione 1.0 del parser CSV è l'impostazione predefinita ed è completa di tutte le funzionalità, mentre la versione 2.0 è progettata per le prestazioni. Le prestazioni più elevate del parser 2.0 sono attribuibili a tecniche di analisi avanzate e al multi-threading. La differenza di velocità sarà maggiore con l'aumento delle dimensioni dei file.

## <a name="automatic-schema-discovery"></a>Individuazione automatica dello schema

È possibile eseguire facilmente query su file CSV e Parquet senza conoscere o specificare lo schema omettendo la clausola WITH. I nomi delle colonne e i tipi di dati verranno dedotti dai file.

I file Parquet contengono metadati di colonna che verranno letti. I mapping dei tipi sono disponibili in [Mapping dei tipi per Parquet](#type-mapping-for-parquet). Per alcuni esempi, vedere [Leggere i file Parquet senza specificare lo schema](#read-parquet-files-without-specifying-schema).

Per i file CSV, i nomi delle colonne possono essere letti dalla riga di intestazione. È possibile specificare se la riga di intestazione esiste o meno con l'argomento HEADER_ROW. Se HEADER_ROW = FALSE, verranno usati nomi di colonne generici: C1, C2 e così via fino a Cn, in cui n è il numero di colonne del file. I tipi di dati verranno dedotti dalle prime 100 righe di dati. Per alcuni esempi, vedere [Leggere i file CSV senza specificare lo schema](#read-csv-files-without-specifying-schema).

> [!IMPORTANT]
> In alcuni casi non è possibile dedurre il tipo di dati appropriato a causa della mancanza di informazioni e verrà quindi usato un tipo di dati più grande. Questo comporta un sovraccarico delle prestazioni ed è particolarmente importante per le colonne di caratteri che verranno dedotte come varchar (8000). Per prestazioni ottimali, [controllare i tipi di dati dedotti](best-practices-sql-on-demand.md#check-inferred-data-types) e [usare i tipi di dati appropriati](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Mapping dei tipi per Parquet

I file Parquet contengono descrizioni dei tipi per ogni colonna. La tabella seguente descrive il mapping dei tipi Parquet ai tipi SQL nativi.

| Tipo Parquet | Tipo logico Parquet (annotazione) | Tipo di dati SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | real |
| INT32 | | INT |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |BINARY |
| BINARY |UTF8 |varchar \*(regole di confronto UTF8) |
| BINARY |STRING |varchar \*(regole di confronto UTF8) |
| BINARY |ENUM|varchar \*(regole di confronto UTF8) |
| FIXED_LEN_BYTE_ARRAY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(8000) \*(regole di confronto UTF8) |
| BINARY |BSON | Non supportato |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL | Non supportato |
| INT32 |INT(8, true) |SMALLINT |
| INT32 |INT(16, true) |SMALLINT |
| INT32 |INT(32, true) |INT |
| INT32 |INT(8, false) |TINYINT |
| INT32 |INT(16, false) |INT |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |Data |
| INT32 |DECIMAL |decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TIME (MICROS) |time - TIME(NANOS) non è supportato |
|INT64 |TIMESTAMP (MILLIS / MICROS) |datetime2 - TIMESTAMP(NANOS) non è supportato |
|[Tipo complesso](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |INSERZIONE |varchar(8000), serializzato in JSON |
|[Tipo complesso](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(8000), serializzato in JSON |

## <a name="examples"></a>Esempi

### <a name="read-csv-files-without-specifying-schema"></a>Leggere i file CSV senza specificare lo schema

Nell'esempio seguente viene letto un file CSV che contiene una riga di intestazione, senza specificare i nomi delle colonne e i tipi di dati: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

Nell'esempio seguente viene letto un file CSV che non contiene una riga di intestazione, senza specificare i nomi delle colonne e i tipi di dati: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Leggere i file Parquet senza specificare lo schema

L'esempio seguente restituisce tutte le colonne della prima riga del set di dati di censimento in formato Parquet, senza specificare nomi di colonna e tipi di dati: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>Leggere specifiche colonne dal file CSV

L'esempio seguente restituisce solo due colonne con i numeri ordinali 1 e 4 dai file population*.csv. Poiché i file non includono una riga di intestazione, la lettura inizia dalla prima riga:

```sql
SELECT 
    * 
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

### <a name="read-specific-columns-from-parquet-file"></a>Leggere specifiche colonne dal file Parquet

L'esempio seguente restituisce solo due colonne della prima riga dal set di dati di censimento in formato Parquet: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>Specificare le colonne usando percorsi JSON

L'esempio seguente mostra come usare le [espressioni di percorso JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server?view=sql-server-ver15) in una clausola WITH e illustra la differenza tra le modalità di percorso strict e lax: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere l[argomento di avvio rapido sull'esecuzione di query sui dati nell'archiviazione](query-data-storage.md) per informazioni su come usare `OPENROWSET` per leggere i file in formati di file [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md) e [JSON](query-json-files.md). Per informazioni su come ottenere prestazioni ottimali, vedere le [procedure consigliate](best-practices-sql-on-demand.md). È anche possibile acquisire informazioni su come salvare i risultati della query in Archiviazione di Azure con [CETAS](develop-tables-cetas.md).
