---
title: Come usare OPENROWSET in SQL su richiesta (anteprima)
description: Questo articolo descrive la sintassi di OPENROWSET in SQL su richiesta (anteprima) e illustra come usare gli argomenti.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 3861b981a1083b44e9cc522a01c50cf24f281e91
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702040"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Come usare OPENROWSET con SQL su richiesta (anteprima)

La funzione `OPENROWSET(BULK...)` consente di accedere ai file di Archiviazione di Azure. La funzione `OPENROWSET` legge il contenuto di un'origine dati remota, ad esempio un file, e lo restituisce come set di righe. All'interno della risorsa SQL su richiesta (anteprima) il provider bulk per set di righe OPENROWSET è accessibile chiamando la funzione OPENROWSET e specificando l'opzione BULK.  

È possibile fare riferimento alla funzione `OPENROWSET` nella clausola `FROM` di una query come se fosse un nome di tabella `OPENROWSET`. Supporta le operazioni in blocco tramite un provider BULK predefinito che consente di leggere i dati da un file e restituirli come set di righe.

## <a name="data-source"></a>Origine dati

La funzione OPENROWSET in Synapse SQL legge il contenuto dei file da un'origine dati. L'origine dati è un account di archiviazione di Azure a cui è possibile fare riferimento esplicitamente nella funzione `OPENROWSET` o che può essere dedotta dinamicamente dall'URL dei file da leggere.
La funzione `OPENROWSET` può facoltativamente contenere un parametro `DATA_SOURCE` per specificare l'origine dati che contiene i file.
- È possibile usare `OPENROWSET` senza `DATA_SOURCE` per leggere direttamente il contenuto dei file dal percorso URL specificato come opzione `BULK`:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://storage..../container/folder/*.parquet',
                    TYPE = 'PARQUET') AS file
    ```

Si tratta di un modo semplice e rapido per leggere il contenuto dei file senza preconfigurazione. Questa opzione consente di usare l'opzione di autenticazione di base per accedere all'archiviazione (pass-through di Azure AD per gli account di accesso di Azure AD e token di firma di accesso condiviso per gli account di accesso di SQL). 

- È possibile usare `OPENROWSET` con `DATA_SOURCE` per accedere ai file nell'account di archiviazione specificato:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    TYPE = 'PARQUET') AS file
    ```


    Questa opzione consente di configurare la posizione dell'account di archiviazione nell'origine dati e di specificare il metodo di autenticazione da usare per accedere all'archiviazione. 
    
    > [!IMPORTANT]
    > `OPENROWSET` senza `DATA_SOURCE` fornisce un metodo semplice e rapido per accedere ai file di archiviazione, ma offre opzioni di autenticazione limitate. Ad esempio, l'entità di Azure AD può accedere ai file solo usando l'[identità di Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#force-azure-ad-pass-through) e non può accedere ai file disponibili pubblicamente. Se sono necessarie opzioni di autenticazione più avanzate, usare l'opzione `DATA_SOURCE` e definire le credenziali da usare per accedere all'archiviazione.


## <a name="security"></a>Sicurezza

Per usare la funzione `OPENROWSET`, un utente di database deve avere l'autorizzazione `ADMINISTER BULK OPERATIONS`.

L'amministratore dell'archiviazione deve anche consentire a un utente di accedere ai file fornendo un token di firma di accesso condiviso valido o consentendo all'entità di Azure AD di accedere ai file di archiviazione. Per altre informazioni sul controllo di accesso all'archiviazione, vedere [questo articolo](develop-storage-files-storage-access-control.md).

`OPENROWSET` usa le regole seguenti per determinare la modalità di autenticazione per l'archiviazione:
- In `OPENROWSET` senza `DATA_SOURCE` il meccanismo di autenticazione dipende dal tipo di chiamante.
  - Gli account di accesso di Azure AD possono accedere ai file solo usando la propria [identità di Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) se Archiviazione di Azure consente all'utente di Azure AD di accedere ai file sottostanti (ad esempio, se il chiamante ha l'autorizzazione di lettura per l'archiviazione) e se si [abilita l'autenticazione pass-through di Azure AD](develop-storage-files-storage-access-control.md#force-azure-ad-pass-through) nel servizio Synapse SQL.
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
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>Argomenti

Sono disponibili due opzioni per i file di input che contengono i dati di destinazione per le query. I valori validi sono:

- 'CSV': include qualsiasi file di testo delimitato con separatori di riga e colonna. Come separatore di campo è possibile usare qualsiasi carattere, ad esempio TSV: FIELDTERMINATOR = tab.

- 'PARQUET': file binario in formato Parquet 

**'unstructured_data_path'**

L'elemento unstructured_data_path che stabilisce un percorso dei dati può essere un percorso assoluto o relativo:
- Il percorso assoluto nel formato '\<prefix >://\<storage_account_path >/\<storage_path >' consente a un utente di leggere direttamente i file.
- Il percorso relativo nel formato '<storage_path>' deve essere usato con il parametro `DATA_SOURCE` e descrive il modello di file all'interno del percorso <storage_account_path> definito in `EXTERNAL DATA SOURCE`. 

 Di seguito sono riportati i valori <storage account path> appropriati per collegarsi a una specifica origine dati esterni. 

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
    >
    
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

Specifica il carattere di terminazione della riga da usare. Se il carattere di terminazione della riga non è specificato, ne verrà usato uno predefinito. I caratteri di terminazione predefiniti per PARSER_VERSION = '1.0' sono \r\n, \n e \r. I caratteri di terminazione predefiniti per PARSER_VERSION ='2.0' sono \r\n e \n.

ESCAPE_CHAR = 'char'

Specifica il carattere nel file usato per l'escape di se stesso e di tutti i valori dei delimitatori del file. Se seguito da un valore diverso da se stesso o da uno qualsiasi dei valori dei delimitatori, il carattere di escape viene eliminato durante la lettura del valore. 

Il parametro ESCAPE_CHAR verrà applicato indipendentemente dal fatto che FIELDQUOTE sia o meno abilitato. Non verrà usato per l'escape del carattere virgolette singole. L'escape del carattere virgolette singole viene eseguito con le virgolette doppie, in linea con il comportamento dei file CSV di Excel.

FIRSTROW = 'first_row' 

Specifica il numero della prima riga da caricare. Il valore predefinito è 1. Questo valore indica la prima riga nel file di dati specificato. I numeri di riga sono determinati dal conteggio dei caratteri di terminazione. FIRSTROW è in base 1.

FIELDQUOTE = 'field_quote' 

Specifica il carattere da usare come carattere virgolette nel file CSV. Se non specificato, viene usato il carattere virgolette ("). 

DATA_COMPRESSION = 'data_compression_method'

Specifica il metodo di compressione. È supportato il metodo di compressione seguente:

- org.apache.hadoop.io.compress.GzipCodec

PARSER_VERSION = 'parser_version'

Specifica la versione del parser da usare per la lettura dei file. Le versioni del parser CSV attualmente supportate sono 1.0 e 2.0:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

La versione 1.0 del parser CSV è l'impostazione predefinita ed è completa di tutte le funzionalità, mentre la versione 2.0 è progettata per le prestazioni e non supporta tutte le opzioni e le codifiche. 

Specifiche del parser CSV versione 2.0:

- Non sono supportati tutti i tipi di dati.
- Il limite massimo delle dimensioni di riga è 8 MB.
- Le opzioni seguenti non sono supportate: DATA_COMPRESSION.
- La stringa vuota racchiusa tra virgolette ("") viene interpretata come stringa vuota.

## <a name="examples"></a>Esempi

L'esempio seguente restituisce solo due colonne con i numeri ordinali 1 e 4 dai file population*.csv. Poiché i file non includono una riga di intestazione, la lettura inizia dalla prima riga:

```sql
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
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

Se si riceve un messaggio di errore indicante che non è possibile elencare i file, è necessario abilitare l'accesso all'archiviazione pubblica in Synapse SQL su richiesta:
- Se si usa un account di accesso di SQL, è necessario [creare credenziali con ambito server che consentano l'accesso all'archiviazione pubblica](develop-storage-files-storage-access-control.md#examples).
- Se si usa un'entità di Azure AD per accedere all'archiviazione pubblica, è necessario[creare credenziali con ambito server che consentano l'accesso all'archiviazione pubblica](develop-storage-files-storage-access-control.md#examples) e disabilitare l'[autenticazione pass-through di Azure AD](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere la [guida di avvio rapido sull'esecuzione di query sui dati nell'archiviazione](query-data-storage.md) per informazioni su come usare 'OPENROWSET per leggere i file in formato formati di file [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md) e [JSON](query-json-files.md). È anche possibile acquisire informazioni su come salvare i risultati della query in Archiviazione di Azure con [CETAS](develop-tables-cetas.md).
