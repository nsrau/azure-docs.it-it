---
title: Caricare dati da Azure Data Lake Store a SQL Data Warehouse | Documentazione Microsoft
description: Informazioni su come usare le tabelle esterne PolyBase per caricare dati da Azure Data Lake Store ad Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/25/2017
ms.author: cakarst;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 6f8d220a64e04b7dfa021aacf68dadf0d55393bf
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>Caricare dati da Azure Data Lake Store a SQL Data Warehouse
Questo documento illustra tutti i passaggi necessari per caricare i dati da Azure Data Lake Store (ADLS) a SQL Data Warehouse usando PolyBase.
Anche se è possibile eseguire query ad hoc sui dati archiviati in ADLS usando le tabelle esterne, è consigliabile importare i dati in SQL Data Warehouse.
Tempo stimato: 10 minuti se si hanno i prerequisiti necessari.
In questa esercitazione si apprenderà come:

1. Creare oggetti di database esterno da caricare da Azure Data Lake Store.
2. Connettersi a una directory di Azure Data Lake Store.
3. Caricare i dati in Azure SQL Data Warehouse.

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire questa esercitazione è necessario:

* Applicazione di Azure Active Directory da usare per l'autenticazione da servizio a servizio. Per la creazione, vedere [Autenticazione di Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)

>[!NOTE] 
> Sono necessari ID client, chiave e valore dell'endpoint di token OAuth 2.0 dell'applicazione Active Directory per la connessione ad Azure Data Lake da SQL Data Warehouse. I dettagli su come ottenere questi valori sono disponibili nel collegamento precedente.
>Per la registrazione di app di Azure Active Directory usare l'ID applicazione come ID client.

* SQL Server Management Studio o SQL Server Data Tools. Per il download di SSMS e la connessione, vedere [Query con SSMS](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms)

* Un'istanza di Azure SQL Data Warehouse. Per crearne una, vedere https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision

* Un'istanza di Azure Data Lake Store, con o senza crittografia abilitata. Per crearne una, vedere: https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal




## <a name="configure-the-data-source"></a>Configurare l'origine dati
PolyBase utilizza oggetti esterni T-SQL per definire il percorso e gli attributi dei dati esterni. Gli oggetti esterni vengono archiviati in SQL Data Warehouse e fanno riferimento ai dati archiviati esternamente.


###  <a name="create-a-credential"></a>Creare una credenziale
Per accedere ad Azure Data Lake Store è necessario creare una chiave master del database per crittografare il segreto delle credenziali usato nel passaggio successivo.
È quindi possibile creare una credenziale con ambito database che archivia le credenziali dell'entità servizio impostate in AAD. Per coloro che hanno usato PolyBase per connettersi ai BLOB del servizio di archiviazione di Azure, si noti che la sintassi delle credenziali è diversa.
Per connettersi ad Azure Data Lake Store, **prima** è necessario creare un'applicazione Azure Active Directory, creare una chiave di accesso e concedere all'applicazione l'accesso alla risorsa Azure Data Lake. Le istruzioni per eseguire queste operazioni sono disponibili [qui](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-authenticate-using-active-directory).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

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


### <a name="create-the-external-data-source"></a>Creare un'origine dati esterna.
Usare questo comando [CREATE EXTERNAL DATA SOURCE][CREATE EXTERNAL DATA SOURCE] per archiviare il percorso e il tipo di dati.
È possibile trovare l'URI ADL nel portale di Azure e in www.portal.azure.com.

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
Per importare i dati da ADLS è necessario specificare il formato file esterno. Questo comando ha opzioni specifiche del formato per descrivere i dati.
Di seguito è riportato un esempio di formato file comunemente usato, ovvero un file di testo delimitato da barre verticali.
Esaminare la documentazione di T-SQL per un elenco completo di [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT]

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store all Missing values as NULL

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

## <a name="create-the-external-tables"></a>Creare le tabelle esterne.
Ora che sono stati specificati l’origine dei dati e il formato dei file, si è pronti per creare le tabelle esterne. Le tabelle esterne rappresentano la modalità di interazione con i dati esterni. PolyBase usa l'attraversamento ricorsivo delle directory per leggere tutti i file in tutte le sottodirectory della directory specificata nel parametro del percorso. L'esempio seguente mostra come creare l'oggetto. È necessario personalizzare l'istruzione perché funzioni con i dati presenti in ADLS.

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

Il caricamento dei dati con PolyBase è fortemente tipizzato. Ciò significa che ogni riga di dati inserita deve soddisfare la definizione dello schema tabella.
Il caricamento di una riga non corrispondente alla definizione dello schema verrà rifiutato.

Le opzioni REJECT_TYPE e REJECT_VALUE permettono di definire il numero di righe o la percentuale dei dati che dovranno essere presenti nella tabella finale.
Se durante il caricamento viene raggiunto il valore rifiutato, il caricamento avrà esito negativo. La causa più comune del rifiuto delle righe è una mancata corrispondenza con la definizione dello schema.
Se ad esempio a una colonna viene erroneamente assegnato lo schema di int quando i dati nel file sono in formato stringa, il caricamento di tutte le righe avrà esito negativo.

Il percorso specifica la directory di livello più alto dalla quale leggere i dati.
In questo caso, se sono presenti sottodirectory in /DimProduct/ PolyBase importerà tutti i dati all'interno delle sottodirectory.

## <a name="load-the-data"></a>Caricare i dati
Per caricare i dati da Azure Data Lake Store usare l'istruzione [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]. Il caricamento con CTAS usa la tabella esterna fortemente tipizzata creata.

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
Per impostazione predefinita, SQL Data Warehouse archivia le tabelle come un indice columnstore cluster. Al termine di un caricamento, alcune delle righe di dati potrebbero non essere compresse nel columnstore.  Esiste una serie di motivi per cui questo può verificarsi. Per altre informazioni, vedere l'articolo su come [gestire gli indici columnstore][manage columnstore indexes].

Per ottimizzare le prestazioni delle query e la compressione columnstore dopo un'operazione di caricamento, ricompilare la tabella per forzare l'indice columnstore per comprimere tutte le righe.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

Per altre informazioni sulla gestione di tali indici, vedere l'articolo su come [gestire gli indici columnstore][manage columnstore indexes].

## <a name="optimize-statistics"></a>Ottimizzare le statistiche
È consigliabile creare statistiche a colonna singola subito dopo un caricamento. Sono disponibili alcune opzioni per le statistiche. Ad esempio, se si creano statistiche a colonna singola su ogni colonna, ricompilare tutte le statistiche potrebbe richiedere molto tempo. Se si sa che alcune colonne non si aggiungeranno ai predicati di query, è possibile ignorare la creazione delle statistiche su tali colonne.

Per creare statistiche a colonna singola su ogni colonna di ogni tabella, è possibile usare l'esempio di codice di stored procedure `prc_sqldw_create_stats` riportato nell'articolo relativo alle [statistiche][statistics].

L'esempio seguente è un buon punto di partenza per la creazione delle statistiche. Qui vengono create statistiche a colonna singola su ogni colonna nella tabella della dimensione e su ogni colonna di join nelle tabelle dei fatti. È sempre possibile aggiungere in un secondo momento statistiche a colonna singola o a più colonne per altre colonne delle tabelle dei fatti.


## <a name="achievement-unlocked"></a>Obiettivo raggiunto
I dati sono stati caricati correttamente in Azure SQL Data Warehouse. Ottimo lavoro.

##<a name="next-steps"></a>Passaggi successivi
Il caricamento dei dati è il primo passaggio per lo sviluppo di una soluzione di data warehouse con SQL Data Warehouse. Vedere le risorse di sviluppo in [Tabelle](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview) e [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops.md).


<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md

