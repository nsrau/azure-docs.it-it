<properties
	pageTitle="Introduzione alle query tra database (partizionamento verticale) | Microsoft Azure"	
	description="Informazioni sull'uso della query del database elastico con database con partizionamento verticale."
	services="sql-database"
	documentationCenter=""  
	manager="jhubbard"
	authors="torsteng"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="torsteng" />

# Introduzione alle query tra database (partizionamento verticale) (anteprima)

La query del database elastico (anteprima) per il database SQL di Azure consente di eseguire query T-SQL che si estendono a più database usando un unico punto di connessione. Questo argomento si applica ai [database con partizionamento verticale](sql-database-elastic-query-vertical-partitioning.md).

Al termine, si apprenderà come configurare e usare un database SQL di Azure per eseguire query che coinvolgono più database correlati.

Per altre informazioni sulla funzionalità di query del database elastico, vedere la [panoramica della query del database elastico del database SQL di Azure](sql-database-elastic-query-overview.md).

## Creare i database di esempio

Per iniziare, è necessario creare due database, **Customers** e **Orders**, in server logici uguali o diversi.

Eseguire la query seguente sul database **Orders ** per creare la tabella **OrderInformation** e inserire i dati di esempio.

	CREATE TABLE [dbo].[OrderInformation]( 
		[OrderID] [int] NOT NULL, 
		[CustomerID] [int] NOT NULL 
		) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Quindi eseguire la query seguente sul database **Customers** per creare la tabella **CustomerInformation** e inserire i dati di esempio.

	CREATE TABLE [dbo].[CustomerInformation]( 
		[CustomerID] [int] NOT NULL, 
		[CustomerName] [varchar](50) NULL, 
		[Company] [varchar](50) NULL 
		CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
	) 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## Creare oggetti di database
### Chiave master e credenziali con ambito database

1. Apri SQL Server Management Studio e SQL Server Data Tools in Visual Studio
2. Connettersi al database Orders ed eseguire i comandi T-SQL seguenti:

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
		WITH IDENTITY = '<username>', 
		SECRET = '<password>';  

	Il nome utente e la password devono corrispondere al nome utente e alla password usati per l'accesso al database Customers. L'autenticazione tramite Azure Active Directory con query elastiche non è attualmente supportata.

### DROP EXTERNAL DATA SOURCE
Per creare un'origine dati esterna, eseguire il comando seguente sul database Orders:

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
		(TYPE = RDBMS, 
		LOCATION = '<server_name>.database.windows.net', 
		DATABASE_NAME = 'Customers', 
		CREDENTIAL = ElasticDBQueryCred, 
	) ;

### Tabelle esterne
Creare una tabella esterna nel database Orders che corrisponda alla definizione della tabella CustomerInformation:

	CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
	( [CustomerID] [int] NOT NULL, 
	  [CustomerName] [varchar](50) NOT NULL, 
	  [Company] [varchar](50) NOT NULL) 
	WITH 
	( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## Eseguire una query di esempio elastica database T-SQL

Dopo aver definito l'origine dati esterna e le tabelle esterne, è possibile usare T-SQL per eseguire query nelle tabelle esterne. Eseguire questa query nel database Orders:

	SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
	FROM OrderInformation 
	INNER JOIN CustomerInformation 
	ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## Costi

Attualmente, la funzionalità di query del database elastico è compresa nel costo del database SQL di Azure.

Per informazioni sui prezzi, vedere [Database SQL - Prezzi](/pricing/details/sql-database).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->

<!---HONumber=AcomDC_0713_2016-->