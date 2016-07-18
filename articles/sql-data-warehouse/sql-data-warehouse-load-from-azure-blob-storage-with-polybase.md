<properties
   pageTitle="Caricare dati dall’archiviazione BLOB di Azure in SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Informazioni su come utilizzare PolyBase per caricare i dati dall'archivio BLOB di Azure in SQL Data Warehouse. Caricare alcune tabelle dai dati pubblici nello schema del Data Warehouse di Contoso Retail."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>


# Caricare dati dall’archiviazione BLOB di Azure in un SQL Data Warehouse (PolyBase)

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Usare i comandi PolyBase e T-SQL per caricare i dati dall'archivio BLOB di Azure in un Data Warehouse di Azure SQL.

In parole brevi, in questa esercitazione vengono caricate due tabelle da un BLOB di Archiviazione di Azure pubblico nello schema del Data Warehouse di Contoso Retail. Per caricare il set di dati completo, eseguire l'esempio [Caricare nel Data Warehouse completo di Contoso Retail][] dal repository di esempi di Microsoft SQL Server.

In questa esercitazione si apprenderà come:

1. Configurare PolyBase per caricare dall'archiviazione BLOB di Azure
2. Caricare dati pubblici nel database
3. Una volta completato il caricamento, effettuare le ottimizzazioni.


## Prima di iniziare
Per eseguire questa esercitazione, è necessario un account di Azure che già dispone di un database SQL Data Warehouse. In caso contrario, vedere [Creare un'istanza di SQL Data Warehouse][].

## 1\. Configurare l'origine dati

PolyBase utilizza oggetti esterni T-SQL per definire il percorso e gli attributi dei dati esterni. Le definizioni degli oggetti esterni vengono archiviate in SQL Data Warehouse. I dati vengano archiviati esternamente.

### 1\.1. Creare una credenziale

**Ignorare questo passaggio** se si desidera caricare i dati pubblici di Contoso. Poiché sono già accessibili a tutti, non è necessario un accesso sicuro ai dati pubblici.

**Non ignorare questo passaggio** se si utilizza questa esercitazione come modello per il caricamento dei dati personali. Per accedere ai dati tramite una credenziale, utilizzare lo script seguente per creare una credenziale con ambito di database, quindi utilizzarla quando si definisce il percorso dell'origine dati.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Andare al passaggio 2.

### 1\.2. Creare un'origine dati esterna.

Usare questo comando [CREATE EXTERNAL DATA SOURCE][] per archiviare il percorso e il tipo di dati.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] Se si sceglie di rendere pubblici i contenitori di archiviazione BLOB di Azure, tenere presente che i costi per l’uscita dei dati dal data center verranno addebitati al proprietario dei dati.

## 2\. Configurare il formato dei dati

I dati vengono archiviati in file di testo nell'archiviazione BLOB di Azure e ogni campo è separato con un delimitatore. Eseguire questo comando [CREATE EXTERNAL FILE FORMAT][] per specificare il formato dei dati nei file di testo. I dati di Contoso sono delimitati da barre verticali e non sono compressi.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,	FORMAT_OPTIONS	(   FIELD_TERMINATOR = '|'
					,	STRING_DELIMITER = ''
					,	DATE_FORMAT		 = 'yyyy-MM-dd HH:mm:ss.fff'
					,	USE_TYPE_DEFAULT = FALSE 
					)
);
``` 

## 3\. Creare le tabelle esterne.

Ora che sono stati specificati l’origine dei dati e il formato dei file, si è pronti per creare le tabelle esterne.

### 3\.1. Creare uno schema per i dati. 

Per creare un percorso in cui archiviare i dati di Contoso nel database, creare uno schema.

```sql
CREATE SCHEMA [asb]
GO
```

### 3\.2. Creare le tabelle esterne. 

Eseguire questo script per creare le tabelle esterne DimProduct e FactOnlineSales. Tutto quello che stiamo facendo qui è definire i nomi delle colonne e i tipi di dati e associarli al percorso e al formato dei file dell’archiviazione BLOB di Azure. La definizione viene archiviata in SQL Data Warehouse e i dati sono ancora nel BLOB di Archiviazione di Azure.

Il parametro **LOCATION** corrisponde alla cartella sotto la cartella radice nel BLOB di Archiviazione di Azure. Ogni tabella è in una cartella diversa.


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
	[ProductKey] [int] NOT NULL,
	[ProductLabel] [nvarchar](255) NULL,
	[ProductName] [nvarchar](500) NULL,
	[ProductDescription] [nvarchar](400) NULL,
	[ProductSubcategoryKey] [int] NULL,
	[Manufacturer] [nvarchar](50) NULL,
	[BrandName] [nvarchar](50) NULL,
	[ClassID] [nvarchar](10) NULL,
	[ClassName] [nvarchar](20) NULL,
	[StyleID] [nvarchar](10) NULL,
	[StyleName] [nvarchar](20) NULL,
	[ColorID] [nvarchar](10) NULL,
	[ColorName] [nvarchar](20) NOT NULL,
	[Size] [nvarchar](50) NULL,
	[SizeRange] [nvarchar](50) NULL,
	[SizeUnitMeasureID] [nvarchar](20) NULL,
	[Weight] [float] NULL,
	[WeightUnitMeasureID] [nvarchar](20) NULL,
	[UnitOfMeasureID] [nvarchar](10) NULL,
	[UnitOfMeasureName] [nvarchar](40) NULL,
	[StockTypeID] [nvarchar](10) NULL,
	[StockTypeName] [nvarchar](40) NULL,
	[UnitCost] [money] NULL,
	[UnitPrice] [money] NULL,
	[AvailableForSaleDate] [datetime] NULL,
	[StopSaleDate] [datetime] NULL,
	[Status] [nvarchar](7) NULL,
	[ImageURL] [nvarchar](150) NULL,
	[ProductURL] [nvarchar](150) NULL,
	[ETLLoadID] [int] NULL,
	[LoadDate] [datetime] NULL,
	[UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
	[OnlineSalesKey] [int]  NOT NULL,
	[DateKey] [datetime] NOT NULL,
	[StoreKey] [int] NOT NULL,
	[ProductKey] [int] NOT NULL,
	[PromotionKey] [int] NOT NULL,
	[CurrencyKey] [int] NOT NULL,
	[CustomerKey] [int] NOT NULL,
	[SalesOrderNumber] [nvarchar](20) NOT NULL,
	[SalesOrderLineNumber] [int] NULL,
	[SalesQuantity] [int] NOT NULL,
	[SalesAmount] [money] NOT NULL,
	[ReturnQuantity] [int] NOT NULL,
	[ReturnAmount] [money] NULL,
	[DiscountQuantity] [int] NULL,
	[DiscountAmount] [money] NULL,
	[TotalCost] [money] NOT NULL,
	[UnitCost] [money] NULL,
	[UnitPrice] [money] NULL,
	[ETLLoadID] [int] NULL,
	[LoadDate] [datetime] NULL,
	[UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## 4\. Caricare i dati
È possibile accedere ai dati esterni in diversi modi. Si può eseguire una query sui dati direttamente dalla tabella esterna, caricare i dati in nuove tabelle di database o aggiungere dati esterni alle tabelle dei database esistenti.


### 4\.1. Crea un nuovo schema

CTAS crea una nuova tabella contenente i dati. Innanzitutto, creare uno schema per i dati di Contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### 4\.2. Caricare i dati in nuove tabelle

Per caricare i dati dall'archiviazione BLOB di Azure e salvarli in una tabella all'interno del database, utilizzare l'istruzione [CREATE TABLE AS SELECT][] (CTAS) T-SQL. Il caricamento con CTAS sfrutta le tabelle esterne fortemente tipizzate appena create. Per caricare i dati in nuove tabelle, usare una sola istruzione [CTAS][] per ogni tabella.

CTAS crea una nuova tabella e la popola con i risultati di un'istruzione SELECT. CTAS definisce la nuova tabella in modo che abbia le stesse colonne e gli stessi tipi di dati dei risultati dell'istruzione SELECT. Se si selezionano tutte le colonne da una tabella esterna, la nuova tabella sarà una replica delle colonne e dei tipi di dati della tabella esterna.

In questo esempio, creiamo sia la dimensione sia la tabella dei fatti come hash di tabelle distribuite.


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### 4\.3. Monitorare l'avanzamento del caricamento

È possibile monitorare l’avanzamento del caricamento con la vista a gestione dinamica (DMV) `[sys].[dm_pdw_exec_requests]`.

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.label = 'CTAS : Load [cso].[DimProduct]             '
      OR r.label = 'CTAS : Load [cso].[FactOnlineSales]        '
;
```

## 5\. Ottimizzare la compressione columnstore

Per impostazione predefinita, SQL Data Warehouse archivia le tabelle come un indice columnstore cluster. Al termine di un caricamento, alcune delle righe di dati potrebbero non essere compresse nel columnstore. Esiste una serie di motivi per cui questo può verificarsi. Per altre informazioni, vedere [Indicizzazione di tabelle in SQL Data Warehouse][].

Per ottimizzare le prestazioni delle query e la compressione columnstore dopo un'operazione di caricamento, ricompilare la tabella per forzare l'indice columnstore per comprimere tutte le righe.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Per altre informazioni sulla gestione degli indici columnstore, vedere [Indicizzazione di tabelle in SQL Data Warehouse][].

## 6\. Ottimizzare le statistiche

È consigliabile creare statistiche a colonna singola subito dopo un caricamento. Sono disponibili alcune opzioni per le statistiche. Ad esempio, se si creano statistiche a colonna singola su ogni colonna, ricompilare tutte le statistiche potrebbe richiedere molto tempo. Se si sa che alcune colonne non si aggiungeranno ai predicati di query, è possibile ignorare la creazione delle statistiche su tali colonne.

Per creare statistiche a colonna singola su ogni colonna di ogni tabella, è possibile usare l'esempio di codice di stored procedure `prc_sqldw_create_stats` disponibile in [Managing statistics on tables in SQL Data Warehouse][] (Gestione delle statistiche nelle tabelle in SQL Data Warehouse).

L'esempio seguente è un buon punto di partenza per la creazione delle statistiche. Qui vengono create statistiche a colonna singola su ogni colonna nella tabella della dimensione e su ogni colonna di join nelle tabelle dei fatti. È sempre possibile aggiungere in un secondo momento statistiche a colonna singola o a più colonne per altre colonne delle tabelle dei fatti.


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## Obiettivo raggiunto

I dati pubblici sono stati caricati correttamente in Azure SQL Data Warehouse. Ottimo lavoro.

Ora è possibile iniziare a eseguire le query sulle tabelle prendendo spunto dalle query seguenti:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

Ora è possibile usare SQL Data Warehouse per l'esplorazione dei dati.

## Passaggi successivi
Per caricare i dati completi del data warehouse di Contoso Retail, usare lo script disponibile nei suggerimenti per lo sviluppo e vedere [Decisioni di progettazione e tecniche di codifica per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Creare un'istanza di SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Decisioni di progettazione e tecniche di codifica per SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Indicizzazione di tabelle in SQL Data Warehouse]: ./sql-data-warehouse-tables-index.md
[Managing statistics on tables in SQL Data Warehouse]: ./sql-data-warehouse-tables-statistics.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[label]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/it-IT/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/it-IT/library/dn935026.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Caricare nel Data Warehouse completo di Contoso Retail]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md

<!---HONumber=AcomDC_0706_2016-->