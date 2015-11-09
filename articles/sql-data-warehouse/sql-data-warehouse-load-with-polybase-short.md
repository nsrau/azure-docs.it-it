<properties
   pageTitle="Caricare dati con l'esercitazione PolyBase | Microsoft Azure"
   description="Informazioni su come usare PolyBase per caricare dati in SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahajs"
   manager="jhubbard"
   editor="sahajs"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="sahajs;barbkess"/>


# Caricare dati con PolyBase

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-load-with-polybase-short.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

Questa esercitazione descrive come caricare dati in SQL Data Warehouse di Azure tramite PolyBase.


## Prerequisiti
Per eseguire questa esercitazione, è necessario:

- Un database di SQL Data Warehouse
- Account di archiviazione di Azure


## Passaggio 1: Creare un file di dati di origine
Aprire il Blocco note e copiare le righe di dati seguenti in un nuovo file. Salvare il file nella directory temporanea locale, C:\\Temp\\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```


## Passaggio 2: Eseguire la migrazione dei dati nell'archivio BLOB di Azure

- Scaricare la [versione più recente di AzCopy][].
- Aprire una finestra di comando e passare alla directory di installazione di AzCopy sul computer contenente il file AzCopy.exe. Per impostazione predefinita, la directory di installazione è %ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy\\ su un computer Windows a 64 bit.
- Eseguire il comando seguente per caricare il file. Specificare la chiave dell'account di archiviazione di Azure per /DestKey.

```
.\AzCopy.exe /Source:C:\Temp\ /Dest:https://pbdemostorage.blob.core.windows.net/datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
```

Per altre informazioni su AzCopy, vedere [Introduzione all'utilità della riga di comando AzCopy][].


## Passaggio 3: Creare tabelle esterne

Quindi, è necessario creare le tabelle esterne in SQL Data Warehouse per fare riferimento ai dati nell'archivio BLOB di Azure. Per creare una tabella esterna, seguire questa procedura:

- [Creare chiave master][]\: per crittografare il segreto della credenziale con ambito database.
- [Creare credenziale con ambito database]\: per specificare le informazioni di autenticazione per l'account di archiviazione di Azure.
- [Creare origine dati esterna]\: per specificare il percorso dell'archivio BLOB di Azure.
- [Creare formato file esterno]\: per specificare il layout dei dati.
- [Creare tabella esterna]\: per fare riferimento ai dati di archiviazione di Azure.



```

-- A: Create Master Key
-- Required to encrypt the credential secret in the next step.
CREATE MASTER KEY;


-- B: Create Database Scoped Credential
-- Provide the Azure storage account key. The identity name does not affect authentication to Azure storage.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH IDENTITY = 'user', 
SECRET = '<azure_storage_account_key>';


-- C: Create External Data Source
-- Specify location and credential to access your Azure blob storage.
CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (	
		TYPE = Hadoop, 
		LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
		CREDENTIAL = AzureStorageCredential
); 


-- D: Create External File format 
-- Specify the layout of data stored in Azure storage blobs. 
CREATE EXTERNAL FILE FORMAT TextFile 
WITH (FORMAT_TYPE = DelimitedText, 
FORMAT_OPTIONS (FIELD_TERMINATOR = ','));


-- E: Create External Table
-- To reference data stored in Azure blob storage.
CREATE EXTERNAL TABLE dbo.DimDate2External (
	DateId INT NOT NULL, 
	CalendarQuarter TINYINT NOT NULL, 
	FiscalQuarter TINYINT NOT NULL
)
WITH (
		LOCATION='datedimension/', 
		DATA_SOURCE=AzureStorage, 
		FILE_FORMAT=TextFile
);


-- Run a query on external table to confirm that the Azure Storage data can be referenced.
SELECT count(*) FROM dbo.DimDate2External;

```



## Passaggio 4: Caricare i dati in SQL Data Warehouse

- Per caricare i dati in una nuova tabella, eseguire l'istruzione CREATE TABLE AS SELECT. La nuova tabella eredita le colonne indicate nella query. Eredita i tipi di dati di tali colonne dalla definizione della tabella esterna. 
- Per caricare i dati in una tabella esistente, usare l'istruzione INSERT...SELECT.  


```

-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
		DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * 
FROM   [dbo].[DimDate2External];

```
Vedere [CREATE TABLE AS SELECT (Transact-SQL)][].


Per altre informazioni su PolyBase, fare riferimento all'[esercitazione PolyBase in SQL Data Warehouse][].


<!--Article references-->
[esercitazione PolyBase in SQL Data Warehouse]: sql-data-warehouse-load-with-polybase.md


<!-- External Links -->
[versione più recente di AzCopy]: http://aka.ms/downloadazcopy
[Introduzione all'utilità della riga di comando AzCopy]: https://azure.microsoft.com/documentation/articles/storage-use-azcopy/

[Creare origine dati esterna]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[Creare formato file esterno]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[Creare tabella esterna]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[Creare chiave master]: https://msdn.microsoft.com/it-IT/library/ms174382.aspx
[Creare credenziale con ambito database]: https://msdn.microsoft.com/it-IT/library/mt270260.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx

<!---HONumber=Nov15_HO1-->