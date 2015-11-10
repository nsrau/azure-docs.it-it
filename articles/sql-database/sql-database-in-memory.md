<properties
	pageTitle="Introduzione a SQL in memoria | Microsoft Azure"
	description="Le tecnologie SQL in memoria migliorano notevolmente le prestazioni dei carichi di lavoro transazionali e analitici. Informazioni su come sfruttare i vantaggi di queste tecnologie."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Introduzione alle tecnologie in memoria (anteprima)


Le tecnologie SQL in memoria migliorano notevolmente le prestazioni dei carichi di lavoro transazionali e analitici. Con OLTP in memoria è possibile ottenere una velocità effettiva fino a 30 volte superiore per le transazioni e con le analisi in memoria si possono ottenere prestazioni delle query fino a 100 volte migliori, in base al carico di lavoro. Con le analisi in tempo reale è possibile combinare queste tecnologie per ottenere le informazioni aziendali in tempo reale basate su dati operativi.

Le analisi in memoria sono disponibili a livello generale nel database di Azure. OLTP in memoria e analisi dei dati operativi in tempo reale sono in anteprima, per i database SQL Azure Premium.


## Introduzione

Per provare a usare OLTP in memoria per carichi di lavoro transazionali:


- [Installare l'esempio di OLTP in memoria](#install-the-in-memory-oltp-sample).
- [Usare OLTP in memoria in un'applicazione esistente del database SQL di Azure.](sql-database-in-memory-oltp-migration.md)
- [Monitorare l'archiviazione in memoria](sql-database-in-memory-oltp-monitoring.md).


Provare a usare le analisi in memoria per i carichi di lavoro analitici:

- [Installare l'esempio di analisi in memoria](#install-the-in-memory-analytics-sample).
- Altre informazioni sugli [Indici Columnstore](https://msdn.microsoft.com/library/gg492088.aspx) su MSDN.


## Installare l'esempio di OLTP in memoria

È possibile creare il database AdventureWorksLT [V12] di esempio con pochi clic nel portale di anteprima di Azure. I passaggi seguenti illustrano come arricchire il database AdventureWorksLT con tabelle e stored procedure compilate in modalità nativa per illustrare gli oggetti OLTP in memoria.


1. Nel [portale di anteprima di Azure](https://portal.azure.com/) creare un database Premium in un server versione 12. Impostare Origine sul database AdventureWorksLT[V12] di esempio.
 - Per istruzioni dettagliate per questo passaggio, vedere [questo articolo](sql-database-get-started.md).

2. Connettersi al database con [SQL Server Management Studio (SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) o con un'utilità analoga.

3. Copiare lo [script OLTP in memoria](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) negli Appunti.
 - Lo script crea gli oggetti in memoria necessari nel database AdventureWorksLT di esempio creati nel passaggio 1.

4. Incollare lo script Transact-SQL in SSMS.exe, quindi eseguire lo script.



&nbsp;

L'esempio contiene le tabelle con ottimizzazione per la memoria seguenti:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

Esaminare le tabelle con ottimizzazione per la memoria tramite Esplora oggetti oppure tramite le query delle viste del catalogo.

Esempio:


```
		SELECT name, object_id, type, type_desc, is_memory_optimized, durability, durability_desc
		FROM sys.tables
	WHERE is_memory_optimized=1;
```


Analogamente, è possibile esaminare la stored procedure compilata in modalità nativa SalesLT.usp\_InsertSalesOrder\_inmem tramite Esplora oggetti o tramite query delle viste del catalogo.

Esempio:


```
		SELECT object_name(object_id), object_id, definition, uses_native_compilation
		FROM sys.sql_modules
	WHERE uses_native_compilation=1;
```


## Eseguire il carico di lavoro di esempio

Usare le stored procedure SalesLT.usp\_InsertSalesOrder\_inmem e SalesLT.usp\_InsertSalesOrder\_ondisk per confrontare le prestazioni di inserimento per le tabelle con ottimizzazione per la memoria rispetto alle tabelle basate su disco.

È consigliabile eseguire il carico di lavoro usando alcune connessioni client concorrenti da un'applicazione che si trova nella stessa area di Azure del database di esempio.

### Inserimento di ordini di vendita di esempio

Lo script seguente inserisce un ordine di vendita di esempio con cinque voci nelle tabelle con ottimizzazione per la memoria SalesLT.SalesOrderHeader\_inmem e SalesLT.SalesOrderDetail\_inmem:


```
		DECLARE
			@i int = 0,
			@od SalesLT.SalesOrderDetailType_inmem,
			@SalesOrderID int,
			@DueDate datetime2 = sysdatetime(),
			@CustomerID int = rand() * 8000,
			@BillToAddressID int = rand() * 10000,
			@ShipToAddressID int = rand() * 10000;

		INSERT INTO @od
		SELECT OrderQty, ProductID
		FROM Demo.DemoSalesOrderDetailSeed
		WHERE OrderID= cast((rand()*60) as int);

EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
			@CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


### Esecuzione del test di stress del carico di lavoro di esempio

Per eseguire un test di stress del carico di lavoro di esempio, creare una [Macchina virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) nella stessa area del database di esempio. Usare lo strumento da riga di comando ostress per eseguire il carico di lavoro. Le istruzioni per l'[installazione e l'esecuzione di ostress](https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx) sono disponibili su MSDN.

Quando viene eseguito dal prompt dei comandi RML, il comando seguente inserisce un milione di ordini di vendita, ognuno con cinque voci, in tabelle con ottimizzazione per la memoria, usando 100 connessioni concorrenti:


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		 -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*  
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); while (@i <
		 20) begin; EXEC SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
		 @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1 end"
```


Assicurarsi di sostituire <servername> con il nome del server, <database> con il nome del database e <login> e <password> con le informazioni di accesso.

Per confrontare le prestazioni di inserimento delle tabelle con ottimizzazione per la memoria con tabelle tradizionali basate su disco, usare il comando seguente per inserire lo stesso milione di ordini di vendita in tabelle basate su disco:


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		-d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_ondisk,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() *
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed with (snapshot) WHERE OrderID= cast((rand()*60) as
		int); while (@i < 20) begin; EXEC SalesLT.usp_InsertSalesOrder_ondisk
		@SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID,
		@od; set @i += 1 end"
```


I test hanno mostrato un miglioramento di circa nove volte delle prestazioni per le tabelle con ottimizzazione per la memoria rispetto alle tabelle basate su disco per questo carico di lavoro, con ostress in esecuzione in una macchina virtuale nella stessa area di Azure del database.

Dopo l'esecuzione di ogni test, assicurarsi di reimpostare l'esempio, per evitare di esaurire lo spazio di archiviazione in memoria. Eseguire l'istruzione T-SQL seguente nel database. Una singola esecuzione di test che inserisce un milione di ordini di vendita ha come risultato >500 MB di dati nelle tabelle con ottimizzazione per la memoria.


```
EXECUTE Demo.usp_DemoReset;
```


## Installare l'esempio di analisi in memoria

**Prima di tutto** creare un nuovo database SQL di Azure.

- Scegliere qualsiasi Premium Edition (Premium è necessario per Columnstore)
- Assicurarsi di creare il database dall'esempio.

- Per semplicità, denominare il database AdventureworksLT.



**Successivamente**, connettersi al database SQL di Azure mediante [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) anteprima di settembre 2015 o versioni successive.


- Eseguire lo script di installazione [sql\_in-memory\_analytics\_sample](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql).


- Creare lo schema necessario per le tabelle delle dimensioni e dei fatti eseguendo lo script. Lo script creerà due tabelle dei fatti, ognuna con circa 3,5 milioni di righe.


- FactResellerSales\_CCI, che include una tabella Columnstore.


- FactResellerSalesXL\_PageCompressed, ovvero una tabella con albero B equivalente, compressa per la pagina. **Nota:** l'esecuzione di questo script e la generazione dei dati possono richiedere fino a 15 minuti.


**Eseguire** tutte le query demo nel file [clustered\_columnstore\_sample\_queries.sql](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) ed esaminare la funzionalità.

## Altre informazioni su OLTP in memoria

[OLTP in memoria (ottimizzazione in memoria)](https://msdn.microsoft.com/library/dn133186.aspx)

Il [white paper sui modelli comuni dei carichi di lavoro e sulle considerazioni relative alla migrazione](https://msdn.microsoft.com/library/dn673538.aspx) descrive modelli di carico di lavoro in cui OLTP in memoria fornisce in genere miglioramenti significativi delle prestazioni.

## Considerazioni di anteprima

OLTP in memoria è supportato **solo** per database Premium Edition.

OLTP in memoria è supportato solo nei database appena creati. Non è supportato nei database creati in base a un database esistente tramite la funzionalità Copia o Ripristina. Quando il nuovo database è disponibile, sarà possibile copiarlo o ripristinarlo, mantenendo al tempo stesso la funzionalità completa di OLTP in memoria.

Per verificare se OLTP in memoria è supportato in un database specifico, eseguire la query seguente:


```
SELECT DATABASEPROPERTYEX(DB_NAME(), 'IsXTPSupported');
```


Se la query restituisce **1**, OLTP in memoria è supportato in questo database, oltre a eventuali database copiati e ripristinati basati su questo database.

Se un database contiene uno dei tipi di oggetti seguenti, la modifica del livello di servizio del database su Basic o Standard non è supportata. Per effettuare il downgrade del database, è prima di tutto necessario eliminare gli oggetti.

- Tabelle con ottimizzazione per la memoria
- Tipi di tabelle con ottimizzazione per la memoria
- I moduli compilati in modalità nativa che usano OLTP in memoria con database in pool elastici non sono supportati.

L'uso di OLTP in memoria con SQL Data Warehouse non è supportato.

Query Store non acquisisce query in moduli compilati in modalità nativa.

Alcune funzionalità di Transact-SQL non sono supportate con OLTP in memoria. Per informazioni dettagliate, vedere [Supporto di Transact-SQL per OLTP in memoria](https://msdn.microsoft.com/library/dn133180.aspx).

## Strumenti supportati

[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) anteprima di settembre 2015 o versioni successive.

[SQL Server Data Tools Preview (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) anteprima di settembre 2015 o versioni successive.

## Risorse aggiuntive

[Informazioni su OLTP in memoria su MSDN](https://msdn.microsoft.com/library/dn133186.aspx)

[Informazioni sulle analisi In memoria (Columnstore) su MSDN](https://msdn.microsoft.com/library/gg492088.aspx)

[Informazioni sulle analisi operative in tempo reale su MSDN](https://msdn.microsoft.com/library/dn817827.aspx)

Il [white paper sui modelli comuni di carichi di lavoro e sulle considerazioni relative alla migrazione](https://msdn.microsoft.com/library/dn673538.aspx) descrive modelli di carico di lavoro in cui OLTP in memoria fornisce in genere miglioramenti significativi delle prestazioni.

## Passaggi successivi

Vedere [Usare OLTP in memoria in un'applicazione SQL di Azure esistente.](sql-database-in-memory-oltp-migration.md)

[Monitorare l'archiviazione in memoria ](sql-database-in-memory-oltp-monitoring.md) per OLTP in memoria.

<!---HONumber=Nov15_HO2-->