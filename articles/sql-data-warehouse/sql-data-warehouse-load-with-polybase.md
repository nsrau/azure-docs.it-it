<properties
   pageTitle="Esercitazione su PolyBase in SQL Data Warehouse | Microsoft Azure"
   description="Informazioni su PolyBase e sul relativo uso per scenari di data warehousing."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/09/2015"
   ms.author="sahajs;barbkess"/>


# Caricare dati con PolyBase
La tecnologia PolyBase consente di eseguire query e join di dati da più origini usando comandi Transact-SQL.

Con PolyBase, è possibile eseguire query sui dati memorizzati nell'archiviazione BLOB di Azure e caricare i dati nel database di SQL Data Warehouse con i passaggi seguenti:

- Creare la chiave master e le credenziali del database.
- Creare oggetti PolyBase: origine dati esterna, formato di file esterno e tabella esterna. 
- Eseguire query sui dati memorizzati nell'archiviazione BLOB di Azure.
- Caricare dati dall'archiviazione BLOB di Azure in SQL Data Warehouse.


## Prerequisiti
Per eseguire questa esercitazione, è necessario:

- Account di archiviazione di Azure
- Dati memorizzati nell'archiviazione BLOB di Azure come file con testo delimitato

Creare prima gli oggetti richiesti da PolyBase per la connessione ai dati nell'archiviazione BLOB di Azure e l'esecuzione di query su di essi.

## Creare la chiave master del database
Connettersi al database master nel server per creare una chiave master del database. Questa chiave viene usata per crittografare il segreto della credenziale nel passaggio successivo.

```
-- Creating master key
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo';
```

Argomento di riferimento: [CREATE MASTER KEY (Transact-SQL)][].

## Creare una credenziale con ambito di database
Per accedere all'archiviazione BLOB di Azure, è necessario creare una credenziale con ambito di database che archivia informazioni di autenticazione per l'account di archiviazione Azure. Connettersi al database del data warehouse e creare una credenziale con ambito di database per ogni account di archiviazione Azure a cui si vuole accedere. Specificare un nome di identità e la chiave dell'account di archiviazione Azure come segreto. Il nome dell'identità non influenza l'autenticazione per Archiviazione di Azure.

```
-- Creating credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe', 
	Secret = 'myazurestoragekey==';
```

Argomento di riferimento: [CREATE CREDENTIAL (Transact-SQL)][].

Quando si esegue la rotazione delle chiavi dell'account di archiviazione di Azure, è necessario eliminare le credenziali e ricrearle con la nuova chiave come segreto.

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret;
```

Argomento di riferimento: [DROP CREDENTIAL (Transact-SQL)][].


## Creare un'origine dati esterna
L'origine dati esterna è un oggetto di database che archivia il percorso dei dati di archiviazione BLOB di Azure e le informazioni di accesso. È necessario definire un'origine dati esterna per ogni contenitore di archiviazione di Azure a cui si vuole accedere.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH (
	TYPE = HADOOP, 
       LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path’,
      CREDENTIAL = ASBSecret
);
```

Argomento di riferimento: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

## Creare un formato di file esterno
Il formato di file esterno è un oggetto di database che specifica il formato dei dati esterni. In questo esempio i dati sono stati decompressi in un file di testo e i campi sono separati dal carattere barra verticale ('|').

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH (
	FORMAT_TYPE = DELIMITEDTEXT, 
	FORMAT_OPTIONS (
		FIELD_TERMINATOR ='|', 
		USE_TYPE_DEFAULT = TRUE
	)
);
```

PolyBase supporta dati compressi e non compressi nei formati di file con testo delimitato, Hive RCFILE e HIVE ORC.

Argomento di riferimento: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

## Creare una tabella esterna

La definizione della tabella esterna è simile alla definizione di una tabella relazionale. La differenza principale è costituita dal percorso e dal formato dei dati. La definizione della tabella esterna è archiviata nel database di SQL Data Warehouse. I dati sono archiviati nel percorso specificato dall'origine dati.

L'opzione LOCATION specifica il percorso dei dati dalla radice dell'origine dati. In questo esempio i dati si trovano in 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/'.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] (
    [SensorKey] int NOT NULL, 
    [CustomerKey] int NOT NULL, 
    [GeographyKey] int NULL, 
    [Speed] float NOT NULL, 
    [YearMeasured] int NOT NULL
)
WITH (LOCATION='/Demo/',
      DATA_SOURCE = azure_storage,
      FILE_FORMAT = text_file_format,      
);
```

> [AZURE.NOTE]Si noti che per il momento non è possibile creare statistiche su una tabella esterna.

Tutti i file per la stessa tabella devono trovarsi all'interno della stessa cartella logica nell'archiviazione BLOB di Azure. Come procedura consigliata, suddividere i dati di Archiviazione di Azure in file di non oltre 1 GB quando possibile per l'elaborazione parallela con SQL Data Warehouse.

Argomento di riferimento: [CREATE EXTERNAL TABLE (Transact-SQL)][].

Gli oggetti appena creati vengono archiviati nel database di SQL Data Warehouse. È possibile visualizzarli in Esplora oggetti di SQL Server Data Tools (SSDT).


## Eseguire query sui dati di archiviazione BLOB di Azure
Le query su tabelle esterne usano semplicemente il nome della tabella come se fosse una tabella relazionale.

Si tratta di una query ad hoc che esegue il join dei dati dei clienti delle assicurazioni archiviati in SQL Data Warehouse con i dati di un sensore per automobili archiviati nei BLOB di Archiviazione di Azure. Il risultato mostra gli automobilisti che guidano più velocemente rispetto ad altri.

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
    [Insured_Customers].[FirstName],
    [Insured_Customers].[LastName],
    [Insured_Customers].[YearlyIncome],
    [CarSensor_Data].[Speed]
FROM [dbo].[Insured_Customers] INNER JOIN [ext].[CarSensor_Data]
ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] desc;
```

## Caricare dati dall'archiviazione BLOB di Azure
Questo esempio carica i dati dall'archiviazione BLOB di Azure nel database di SQL Data Warehouse.

Archiviando i dati direttamente viene eliminato il tempo di trasferimento dei dati per le query. L'archiviazione dei dati con un indice columnstore migliora le prestazioni delle query di analisi fino a 10 volte.

Questo esempio usa l'istruzione CREATE TABLE AS SELECT per caricare i dati. La nuova tabella eredita le colonne indicate nella query. Eredita i tipi di dati di tali colonne dalla definizione della tabella esterna.

CREATE TABLE AS SELECT è un'istruzione Transact-SQL a prestazioni elevate che sostituisce l'istruzione INSERT... SELECT. È stata sviluppata in origine per il motore di elaborazione a elevato parallelismo (MPP) nel sistema di piattaforma di analisi ed è ora inclusa in SQL Data Warehouse.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH (
	CLUSTERED COLUMNSTORE INDEX
	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
	)
AS SELECT * from [ext].[CarSensor_Data];
```

Vedere [CREATE TABLE AS SELECT (Transact-SQL)][].


## Limitazioni
Il caricamento con PolyBase supporta solo lo stile di codifica UTF-8. Per altri stili di codifica, ad esempio UTF-16, è possibile usare utilità bcp, SSIS o Data factory di Azure per caricare dati nel database di SQL Data Warehouse.

## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere la [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[panoramica sullo sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/it-it/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/it-it/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/it-it/library/ms189450.aspx

<!---HONumber=July15_HO1-->