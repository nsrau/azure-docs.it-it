---
title: Esercitazione caricare i dati da Azure Data Lake Storage
description: Usare le tabelle esterne di base per caricare i dati da Azure Data Lake Storage in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 08/08/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 522cb9b75d5c0db270f8ba4a65850e35a2e8c4fd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685685"
---
# <a name="load-data-from-azure-data-lake-storage-to-sql-data-warehouse"></a>Caricare i dati da Azure Data Lake Storage a SQL Data Warehouse
Usare le tabelle esterne di base per caricare i dati da Azure Data Lake Storage in Azure SQL Data Warehouse. Sebbene sia possibile eseguire query ad hoc sui dati archiviati in Data Lake Storage, è consigliabile importare i dati nel SQL Data Warehouse per ottenere prestazioni ottimali.

> [!div class="checklist"]
> * Creare gli oggetti di database necessari per il caricamento da Data Lake Storage.
> * Connettersi a una directory Data Lake Storage.
> * Caricare i dati in Azure SQL Data Warehouse.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare
Prima di iniziare questa esercitazione, scaricare e installare la versione più recente di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Per eseguire questa esercitazione è necessario:

* Applicazione di Azure Active Directory da usare per l'autenticazione da servizio a servizio. Per la creazione, vedere [Autenticazione di Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

* Un Azure SQL Data Warehouse. Vedere [Creare un Azure SQL Data Warehouse ed eseguirvi una query](create-data-warehouse-portal.md).

* Un account Data Lake Storage. Vedere iniziare [a usare Azure Data Lake storage](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Creare una credenziale
Per accedere all'account di Data Lake Storage, sarà necessario creare una chiave master del database per crittografare il segreto delle credenziali usato nel passaggio successivo. Si creeranno quindi le credenziali con ambito database. Quando si esegue l'autenticazione usando entità servizio, le credenziali con ambito database archiviano le credenziali dell'entità servizio impostate in AAD. È anche possibile usare la chiave dell'account di archiviazione nelle credenziali con ambito database per Gen2. 

Per connettersi a Data Lake Storage usando entità servizio, è **prima** necessario creare un'applicazione Azure Active Directory, creare una chiave di accesso e concedere all'applicazione l'accesso all'account data Lake storage. Per istruzioni, vedere [eseguire l'autenticazione a Azure Data Lake storage usando Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principals:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Creare un'origine dati esterna.
Usare il comando [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) per archiviare il percorso dei dati. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Configurare il formato dei dati
Per importare i dati da Data Lake Storage, è necessario specificare il formato di file esterno. Questo oggetto definisce il modo in cui i file vengono scritti in Data Lake Storage.
Per un elenco completo, vedere la documentazione T-SQL di [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Creare le tabelle esterne
Ora che sono stati specificati l’origine dei dati e il formato dei file, si è pronti per creare le tabelle esterne. Le tabelle esterne rappresentano la modalità di interazione con i dati esterni. Il parametro location può specificare un file o una directory. Se specifica una directory, verranno caricati tutti i file all'interno della directory.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Considerazioni sulle tabelle esterne
La creazione di una tabella esterna è semplice, ma esistono alcuni aspetti da considerare.

Le tabelle esterne sono fortemente tipizzate. Ciò significa che ogni riga di dati inserita deve soddisfare la definizione dello schema tabella.
Il caricamento di una riga non corrispondente alla definizione dello schema verrà rifiutato.

Le opzioni REJECT_TYPE e REJECT_VALUE permettono di definire il numero di righe o la percentuale dei dati che dovranno essere presenti nella tabella finale. Se durante il caricamento viene raggiunto il valore rifiutato, il caricamento avrà esito negativo. La causa più comune del rifiuto delle righe è una mancata corrispondenza con la definizione dello schema. Se ad esempio a una colonna viene erroneamente assegnato lo schema di int quando i dati nel file sono in formato stringa, il caricamento di tutte le righe avrà esito negativo.

Data Lake Storage Gen1 usa il controllo degli accessi in base al ruolo per controllare l'accesso ai dati. Questo significa che l'entità servizio deve avere autorizzazioni di lettura per le directory definite nel parametro location e per i figli della directory e dei file finali. Questo comportamento consente a PolyBase di autenticare e caricare i dati. 

## <a name="load-the-data"></a>Caricare i dati
Per caricare dati da Data Lake Storage utilizzare l'istruzione [create table As Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) . 

CTAS crea una nuova tabella e la popola con i risultati di un'istruzione SELECT. CTAS definisce la nuova tabella in modo che abbia le stesse colonne e gli stessi tipi di dati dei risultati dell'istruzione SELECT. Se si selezionano tutte le colonne da una tabella esterna, la nuova tabella sarà una replica delle colonne e dei tipi di dati della tabella esterna.

In questo esempio viene creata una tabella con distribuzione hash chiamata DimProduct dalla tabella esterna DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Ottimizzare la compressione columnstore
Per impostazione predefinita, SQL Data Warehouse archivia le tabelle come un indice columnstore cluster. Al termine di un caricamento, alcune delle righe di dati potrebbero non essere compresse nel columnstore.  Esiste una serie di motivi per cui questo può verificarsi. Per altre informazioni, vedere l'articolo [Gestire gli indici columnstore](sql-data-warehouse-tables-index.md).

Per ottimizzare le prestazioni delle query e la compressione columnstore dopo un'operazione di caricamento, ricompilare la tabella per forzare l'indice columnstore per comprimere tutte le righe.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Ottimizzare le statistiche
È consigliabile creare statistiche a colonna singola subito dopo un caricamento. Sono disponibili alcune opzioni per le statistiche. Ad esempio, se si creano statistiche a colonna singola su ogni colonna, ricompilare tutte le statistiche potrebbe richiedere molto tempo. Se si sa che alcune colonne non si aggiungeranno ai predicati di query, è possibile ignorare la creazione delle statistiche su tali colonne.

Per creare statistiche a colonna singola su ogni colonna di ogni tabella, è possibile usare l'esempio di codice di stored procedure `prc_sqldw_create_stats` riportato nell'articolo relativo alle [statistiche](sql-data-warehouse-tables-statistics.md).

L'esempio seguente è un buon punto di partenza per la creazione delle statistiche. Qui vengono create statistiche a colonna singola su ogni colonna nella tabella della dimensione e su ogni colonna di join nelle tabelle dei fatti. È sempre possibile aggiungere in un secondo momento statistiche a colonna singola o a più colonne per altre colonne delle tabelle dei fatti.

## <a name="achievement-unlocked"></a>Obiettivo raggiunto
I dati sono stati caricati correttamente in Azure SQL Data Warehouse. Ottimo lavoro.

## <a name="next-steps"></a>Passaggi successivi 
In questa esercitazione sono state create tabelle esterne per definire la struttura dei dati archiviati in Data Lake Storage Gen1 ed è quindi stata usata l'istruzione PolyBase CREATE TABLE AS SELECT per caricare i dati nel data warehouse. 

Sono state eseguite queste operazioni:
> [!div class="checklist"]
> * Creazione degli oggetti di database necessari per il caricamento da Data Lake Storage Gen1.
> * Connessione a una directory Data Lake Storage Gen1.
> * Caricamento dei dati in Azure SQL Data Warehouse.
>

Il caricamento dei dati è il primo passaggio per lo sviluppo di una soluzione di data warehouse con SQL Data Warehouse. Vedere le risorse di sviluppo.

> [!div class="nextstepaction"]
> [Informazioni sullo sviluppo di tabelle in SQL Data Warehouse](sql-data-warehouse-tables-overview.md)
