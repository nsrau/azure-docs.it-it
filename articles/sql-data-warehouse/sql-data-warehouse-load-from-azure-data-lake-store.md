---
title: 'Esercitazione: caricare dati da Azure Data Lake Store ad Azure SQL Data Warehouse | Microsoft Docs'
description: Usare tabelle PolyBase esterne per caricare dati da Azure Data Lake Store ad Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: c6030d1951c22dddfe6df01225c63cf503a370ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="load-data-from-azure-data-lake-store-to-sql-data-warehouse"></a>Caricare dati da Azure Data Lake Store ad SQL Data Warehouse
Usare tabelle PolyBase esterne per caricare dati da Azure Data Lake Store ad Azure SQL Data Warehouse. Sebbene sia possibile eseguire query ad hoc sui dati archiviati in ADLS, è consigliabile importare i dati in SQL Data Warehouse per prestazioni ottimali.

> [!div class="checklist"]
> * Creare gli oggetti di database necessari da caricare da Azure Data Lake Store.
> * Connettersi a una directory di Azure Data Lake Store.
> * Caricare i dati in Azure SQL Data Warehouse.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare
Prima di iniziare questa esercitazione, scaricare e installare la versione più recente di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Per eseguire questa esercitazione è necessario:

* Applicazione di Azure Active Directory da usare per l'autenticazione da servizio a servizio. Per la creazione, vedere [Autenticazione di Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Sono necessari ID client, chiave e valore dell'endpoint di token OAuth 2.0 dell'applicazione Active Directory per la connessione ad Azure Data Lake da SQL Data Warehouse. I dettagli su come ottenere questi valori sono disponibili nel collegamento precedente. Per la registrazione di app di Azure Active Directory usare l'ID applicazione come ID client.
> 

* Un Azure SQL Data Warehouse. Vedere [Creare un Azure SQL Data Warehouse ed eseguirvi una query](create-data-warehouse-portal.md).

* Un Azure Data Lake Store. Vedere [Introduzione ad Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Creare una credenziale
Per accedere ad Azure Data Lake Store è necessario creare una chiave master del database per crittografare il segreto delle credenziali usato nel passaggio successivo. È quindi possibile creare una credenziale con ambito database che archivia le credenziali dell'entità servizio impostate in AAD. Per coloro che hanno usato PolyBase per connettersi ai BLOB del servizio di archiviazione di Azure, si noti che la sintassi delle credenziali è diversa.

Per connettersi ad Azure Data Lake Store, **prima** è necessario creare un'applicazione Azure Active Directory, creare una chiave di accesso e concedere all'applicazione l'accesso alla risorsa Azure Data Lake. Per istruzioni, vedere [Effettuare l'autenticazione in Azure Data Lake Store tramite Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Creare un'origine dati esterna.
Usare il comando [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) per archiviare il percorso dei dati. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```

## <a name="configure-data-format"></a>Configurare il formato dei dati
Per importare i dati da ADLS è necessario specificare il formato file esterno. Questo oggetto definisce la modalità di scrittura dei dati in ADLS.
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
-- LOCATION: Folder under the ADLS root folder.
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
,   DATA_SOURCE = AzureDataLakeStore
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

 Azure Data Lake Store usa il controllo degli accessi in base al ruolo per controllare l'accesso ai dati. Questo significa che l'entità servizio deve avere autorizzazioni di lettura per le directory definite nel parametro location e per i figli della directory e dei file finali. Questo comportamento consente a PolyBase di autenticare e caricare i dati. 

## <a name="load-the-data"></a>Caricare i dati
Per caricare i dati da Azure Data Lake Store usare l'istruzione [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse). 

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
In questa esercitazione sono state create tabelle esterne per definire la struttura dei dati archiviati in Azure Data Lake Store e quindi è stata usata l'istruzione PolyBase CREATE TABLE AS SELECT per caricare dati nel data warehouse. 

Sono state eseguite queste operazioni:
> [!div class="checklist"]
> * Creazione degli oggetti di database necessari da caricare da Azure Data Lake Store.
> * Connessione a una directory di Azure Data Lake Store.
> * Caricamento dei dati in Azure SQL Data Warehouse.
> 

Il caricamento dei dati è il primo passaggio per lo sviluppo di una soluzione di data warehouse con SQL Data Warehouse. Vedere le risorse di sviluppo.

> [!div class="nextstepaction"]
>[Informazioni sullo sviluppo di tabelle in SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




