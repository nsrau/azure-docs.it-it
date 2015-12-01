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
	ms.date="11/20/2015"
	ms.author="jodebrui"/>


# Introduzione alle tecnologie in memoria (anteprima) in database SQL


Le funzionalità in memoria migliorano notevolmente le prestazioni dei carichi di lavoro transazionali e analitici in determinate situazioni.

Questo argomento presenta due dimostrazioni, una relativa a OLTP in memoria e l'altra relativa all'analisi in memoria. Ogni dimostrazione include i passaggi e il codice necessari per eseguire la dimostrazione. È possibile:

- usare il codice per testare le varianti e vedere le differenze nei risultati delle prestazioni; oppure
- leggere il codice per comprendere lo scenario e apprendere come creare e usare gli oggetti in memoria.


#### OLTP in memoria

Funzionalità di [OLTP](#install_oltp_manuallink) (elaborazione delle transazioni online) in memoria:

- Tabelle con ottimizzazione per la memoria.
- Stored procedure compilate in modo nativo.


Una tabella con ottimizzazione per la memoria ha una rappresentazione di sé nella memoria attiva, oltre alla rappresentazione standard su un disco rigido. Le transazioni aziendali sulla tabella vengono eseguite più velocemente perché interagiscono direttamente solo con la rappresentazione nella memoria attiva.

OLTP in memoria permette di ottenere una velocità effettiva fino a 30 volte superiore per le transazioni, in base alle specifiche del carico di lavoro.


Le stored procedure compilate in modo nativo richiedono un numero inferiore di istruzioni in linguaggio macchina durante la fase di esecuzione rispetto alle stored procedure interpretate tradizionali. La compilazione nativa permette di ottenere durate che rappresentano 1/100 della durata interpretata.


#### Analisi in memoria 

Funzionalità di [analisi](#install_analytics_manuallink) in memoria:

- Indici Columnstore


Un indice columnstore migliora le prestazioni dei carichi di lavoro di query con la compressione esotica dei dati.

In altri servizi gli indici columnstore hanno necessariamente l'ottimizzazione per la memoria. Nel database SQL di Azure, tuttavia, un indice columnstore può coesistere nello stesso disco rigido con la tabella tradizionale che indicizza.


#### Analisi in tempo reale

Per l'[analisi in tempo reale](http://msdn.microsoft.com/library/dn817827.aspx) si combinano analisi e OLTP in memoria per ottenere:

- Informazioni commerciali approfondite in tempo reale basate su dati operativi.


#### Disponibilità


Disponibilità generale (GA):

- [Indici Columnstore](http://msdn.microsoft.com/library/dn817827.aspx) *su disco*.


Anteprima:

- OLTP in memoria
- Analisi in memoria con indici columnstore con ottimizzazione per la memoria
- Analisi operativa in tempo reale


Le considerazioni sulle funzionalità in memoria in fase di anteprima sono disponibili [più avanti in questo argomento](#preview_considerations_for_in_memory).


> [AZURE.NOTE]Le funzionalità in fase di anteprima sono disponibili solo per database SQL di Azure di livello [*Premium*](sql-database-service-tiers.md), non per i database di livello di servizio Standard o Basic.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

& nbsp;

## A. Installare l'esempio di OLTP in memoria

È possibile creare il database AdventureWorksLT [V12] di esempio con pochi clic nel [portale di anteprima di Azure](http://portal.azure.com/). I passaggi descritti in questa sezione illustrano come migliorare il database AdventureWorksLT con:

- Tabelle in memoria.
- Una stored procedure compilata in modo nativo.


#### Procedura di installazione

1. Nel [portale di anteprima di Azure](http://portal.azure.com/) creare un database Premium in un server versione 12. Impostare **Origine** sul database AdventureWorksLT [V12] di esempio.
 - Per istruzioni dettagliate, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md).

2. Connettersi al database con SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiare lo [script Transact-SQL OLTP in memoria](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) negli Appunti.
 - Lo script T-SQL crea gli oggetti in memoria necessari nel database AdventureWorksLT di esempio creato nel passaggio 1.

4. Incollare lo script T-SQL in SSMS.exe, quindi eseguirlo.
 - La clausola `MEMORY_OPTIMIZED = ON` è fondamentale nelle istruzioni CREATE TABLE, ad esempio:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
	[SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
	...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### Errore 40536


Se viene visualizzato l'errore 40536 quando si esegue lo script T-SQL, verificare se il database supporta le funzionalità in memoria eseguendo questo script T-SQL:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Se il risultato è **0**, le funzionalità in memoria non sono supportate, mentre 1 indica che sono supportate. Per diagnosticare il problema:

- Verificare che il database sia stato creato dopo l'attivazione della funzionalità OLTP in memoria per l'anteprima.
- Verificare che il livello di servizio del database sia Premium.


#### Informazioni sugli elementi creati con ottimizzazione per la memoria

**Tabelle**: l'esempio contiene le tabelle con ottimizzazione per la memoria seguenti.

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


È possibile esaminare le tabelle con ottimizzazione per la memoria usando **Esplora oggetti** in SSMS come indicato di seguito:

- Fare doppio clic su **Tabelle** > **Filtro** > **Impostazioni filtro** > **Con ottimizzazione per la memoria** uguale a 1.


In alternativa, è possibile eseguire una query delle viste del catalogo, ad esempio:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**Stored procedure compilata in modo nativo**: è possibile esaminare SalesLT.usp\_InsertSalesOrder\_inmem usando una query delle viste del catalogo.


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


& nbsp;

## Eseguire il carico di lavoro OLTP di esempio

L'unica differenza tra le due *stored procedure* seguenti è che la prima usa versioni con ottimizzazione per la memoria delle tabelle, mentre la seconda usa tabelle basate su disco tradizionali:

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**


Questa sezione illustra come usare l'utilità **ostress.exe** per eseguire le due stored procedure in condizioni di sovraccarico. È possibile mettere a confronto i tempi necessari per il completamento dei due test di stress.


Quando si esegue ostress.exe, è consigliabile passare valori di parametri specifici a entrambe:

- Eseguire un numero elevato di connessioni simultanee, usando ad esempio -n100.
- Ripetere ogni ciclo di connessione centinaia di volte, usando ad esempio -r500.


È opportuno, tuttavia, iniziare con valori molto più bassi, ad esempio -n10 e -r50, per assicurarsi che tutto funzioni correttamente.


### Script per ostress.exe


Questa sezione illustra lo script T-SQL incorporato nella riga di comando ostress.exe. Lo script usa gli elementi creati dallo script T-SQL installato in precedenza.


Lo script riportato di seguito inserisce un ordine di vendita di esempio con cinque voci nelle *tabelle* con ottimizzazione per la memoria seguenti:

- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem


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
	
WHILE (@i < 20)
begin;
	EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
		@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
end
```


Per creare la versione \_ondisk dello script T-SQL precedente per ostress.exe, occorre semplicemente sostituire le due occorrenze della sottostringa *\_inmem* con *\_ondisk*. Questa sostituzione interessa i nomi delle tabelle e delle stored procedure.


### Installare le utilità RML e ostress


È consigliabile pianificare l'esecuzione di ostress.exe su una macchina virtuale di Azure. Creare una [macchina virtuale di Azure](http://azure.microsoft.com/documentation/services/virtual-machines/) nella stessa area geografica di Azure in cui risiede il database AdventureWorksLT. È possibile eseguire ostress.exe sul computer portatile.


Installare nella macchina virtuale o nell'host scelto le utilità RML (Replay Markup Language) che includono ostress.exe.

- Vedere la discussione su ostress.exe nell'articolo relativo ai [database di esempio per OLTP in memoria](http://msdn.microsoft.com/library/mt465764.aspx).
 - In alternativa, vedere l'articolo relativo ai [database di esempio per OLTP in memoria](http://msdn.microsoft.com/library/mt465764.aspx).
 - In alternativa, vedere il [blog sull'installazione di ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### Eseguire prima di tutto il test di stress del carico di lavoro per \_inmem


Per eseguire la riga di comando ostress.exe è possibile usare una finestra del *prompt dei comandi RML*. I parametri della riga di comando indicano al comando ostress di:

- Eseguire 100 connessioni simultaneamente (-n100).
- Fare in modo che ogni connessione esegua lo script T-SQL 50 volte (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Per eseguire la riga di comando ostress.exe precedente:


1. Reimpostare il contenuto dei dati del database eseguendo questo comando in SSMS per eliminare tutti i dati inseriti da esecuzioni precedenti: ```
EXECUTE Demo.usp_DemoReset;
```

2. Copiare il testo della riga di comando ostress.exe precedente negli Appunti.

3. Sostituire <placeholders> per i parametri -S -U -P -d con i valori reali corretti.

4. Eseguire la riga di comando modificata in una finestra dei comandi RML.


#### Il risultato è un intervallo di tempo


Al termine, ostress.exe scrive la durata dell'esecuzione come ultima riga di output nella finestra dei comandi RML. Ad esempio, per un'esecuzione dei test più breve, durata circa 1,5 minuti:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### Reimpostare, modificare per l'esecuzione \_ondisk ed eseguire di nuovo il test


Dopo aver ottenuto il risultato dell'esecuzione \_inmem, seguire la procedura indicata di seguito per l'esecuzione \_ondisk:


1. Reimpostare il database eseguendo questo comando in SSMS per eliminare tutti i dati inseriti dall'esecuzione precedente: ```
EXECUTE Demo.usp_DemoReset;
```

2. Modificare la riga di comando ostress.exe per sostituire tutte le occorrenze di *\_inmem* con *\_ondisk*.

3. Eseguire ostress.exe per la seconda volta e acquisire il risultato relativo alla durata.

4. Reimpostare nuovamente il database, per eliminare in modo responsabile una potenziale grande quantità di dati di test.


#### Risultati previsti per il confronto

I test delle funzionalità in memoria hanno mostrato un miglioramento delle prestazioni pari a **9 volte** per questo semplice carico di lavoro, con ostress in esecuzione in una macchina virtuale di Azure nella stessa area di Azure del database.


Le prestazioni possono migliorare ulteriormente quando si aggiunge la conversione alle stored procedure compilate in modo nativo.


## B. Installare l'esempio di analisi in memoria


In questa sezione vengono messi a confronto i risultati di statistiche e IO quando si usa un indice columnstore rispetto a un indice tradizionale.


Gli indici columnstore sono logicamente simili agli indici tradizionali, ma fisicamente sono diversi. Un indice columnstore organizza in modo esotico i dati per comprimerli notevolmente. In questo modo è possibile ottenere notevoli miglioramenti delle prestazioni.


Per l'analisi in tempo reale in un carico di lavoro OLTP, è spesso preferibile usare un indice columnstore non cluster. Per informazioni dettagliate, vedere [Descrizione degli indici columnstore](http://msdn.microsoft.com/library/gg492088.aspx).



### Preparare il test di analisi columnstore


1. Usare il portale di Azure per creare un nuovo database AdventureWorksLT dall'esempio.
 - Usare esattamente questo nome.
 - Scegliere qualsiasi livello di servizio Premium.

2. Copiare [sql\_in memory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) negli Appunti.
 - Lo script T-SQL crea gli oggetti in memoria necessari nel database AdventureWorksLT di esempio creato nel passaggio 1.
 - Lo script crea la tabella delle dimensioni e due tabelle dei fatti. Ogni tabella dei fatti viene popolata con 3,5 milioni di righe.
 - Il completamento dello script potrebbe richiedere 15 minuti.

3. Incollare lo script T-SQL in SSMS.exe, quindi eseguirlo.
 - La parola chiave **COLUMNSTORE** è fondamentale in una istruzione **CREATE INDEX**, ad esempio:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Impostare AdventureWorksLT sul livello di compatibilità 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Il livello 130 non è direttamente correlato alle funzionalità in memoria, ma offre in genere prestazioni delle query più veloci rispetto al livello 120.


#### Tabelle e indici columnstore fondamentali


- dbo.FactResellerSalesXL\_CCI è una tabella contenente un indice **columnstore** cluster, che presenta una compressione avanzata a livello di *dati*.

- dbo.FactResellerSalesXL\_PageCompressed è una tabella contenente un indice cluster equivalente tradizionale, che presenta una compressione solo a livello di *pagina*.


#### Query fondamentali per il confronto dell'indice columnstore


[Qui](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) sono disponibili diversi tipi di query T-SQL che è possibile eseguire per migliorare le prestazioni. Dal passaggio 2, nello script T-SQL è presente una coppia di query di interesse diretto. Le due query differiscono per una sola riga:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un indice columnstore cluster si trova nella tabella FactResellerSalesXL**\_CCI**.

L'estratto dallo script T-SQL riportato di seguito permette di stampare le statistiche per IO e TIME per la query di ogni tabella.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## Considerazioni sull'anteprima per le funzionalità di OLTP in memoria


Le funzionalità di OLTP in memoria nel database SQL di Azure sono [attive per l'anteprima dal 28 ottobre 2015](http://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Durante la fase di anteprima prima della disponibilità generale (GA), OLTP in memoria è supportato solo per:

- Database a un livello di servizio *Premium*.

- Database creati dopo l'attivazione delle funzionalità di OLTP in memoria.
 - Un nuovo database non può supportare OLTP in memoria se viene ripristinato da un database creato prima dell'attivazione delle funzionalità di OLTP in memoria.


In caso di dubbi, è sempre possibile eseguire l'istruzione T-SQL SELECT seguente per verificare se il database supporta OLTP in memoria. Se il risultato è **1**, il database supporta le funzionalità di OLTP in memoria:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Se la query restituisce **1**, OLTP in memoria è supportato in questo database, nonché in eventuali database copiati e ripristinati basati su questo database.


#### Oggetti consentiti solo a livello Premium


Se un database contiene uno dei tipi di oggetti o tipi OLTP in memoria seguenti, il downgrade del livello di servizio del database da Premium a Basic o Standard non è supportato. Per effettuare il downgrade del database, è necessario prima di tutto eliminare gli oggetti seguenti:

- Tabelle con ottimizzazione per la memoria
- Tipi di tabelle con ottimizzazione per la memoria
- Moduli compilati in modo nativo


#### Altre relazioni


- L'uso delle funzionalità OLTP in memoria con i database in pool elastici non è supportato durante l'anteprima, ma potrebbe esserlo in futuro:

- L'uso di OLTP in memoria con SQL Data Warehouse non è supportato.
 - La funzionalità di indice columnstore delle analisi in memoria è supportata in SQL Data Warehouse.

- L'archivio query non acquisisce query all'interno di moduli compilati in modo nativo durante l'anteprima, ma potrebbe farlo in futuro.

- Alcune funzionalità di Transact-SQL non sono supportate con OLTP in memoria. Questo vale per Microsoft SQL Server e per database SQL di Azure. Per informazioni dettagliate, vedere:
 - [Supporto di Transact-SQL per OLTP in memoria](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Costrutti Transact-SQL non supportati da OLTP in memoria](http://msdn.microsoft.com/library/dn246937.aspx)


## Passaggi successivi


- Vedere la pagina relativa all'[uso di OLTP in memoria in un'applicazione esistente del database SQL di Azure](sql-database-in-memory-oltp-migration.md).


## Risorse aggiuntive

#### Approfondimenti

- [Informazioni su OLTP in memoria, valide per Microsoft SQL Server e database SQL di Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [Informazioni sulle analisi operative in tempo reale su MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- White paper su [modelli comuni dei carichi di lavoro e considerazioni relative alla migrazione](http://msdn.microsoft.com/library/dn673538.aspx), che descrive modelli di carico di lavoro in cui OLTP in memoria fornisce in genere miglioramenti significativi delle prestazioni.

#### Progettazione di applicazioni

- [OLTP in memoria (ottimizzazione in memoria)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Usare OLTP in memoria in un'applicazione esistente del database SQL di Azure.](sql-database-in-memory-oltp-migration.md)

#### Strumenti

- [Anteprima di SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), per la versione più recente del mese.

- [Descrizione delle utilità Replay Markup Language (RML) per SQL Server](http://support.microsoft.com/it-IT/kb/944837)

- [Monitoraggio dell'archiviazione in memoria](sql-database-in-memory-oltp-monitoring.md) per OLTP in memoria.

<!---HONumber=AcomDC_1125_2015-->