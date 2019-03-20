---
title: Esempio in memoria del database SQL di Azure | Microsoft Docs
description: Le tecnologie in memoria del database SQL di Azure con esempi OLTP e columnstore.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 2aa98c3958f1dffeb8adbad5e91a11f397d4a9fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005729"
---
# <a name="in-memory-sample"></a>Esempio in memoria

Le tecnologie in memoria del database SQL di Azure consentono di migliorare le prestazioni dell'applicazione e ridurre potenzialmente i costi del database. Utilizzando le tecnologie in memoria nel database SQL di Azure, è possibile ottenere miglioramenti delle prestazioni con diversi carichi di lavoro.

In questo articolo verranno esaminati due esempi che illustrano l'uso di OLTP in memoria e degli indici columnstore nel database SQL di Azure.

Per altre informazioni, vedere:
- [Panoramica e scenari di utilizzo](https://msdn.microsoft.com/library/mt774593.aspx), inclusi riferimenti a casi di studio sui clienti e informazioni introduttive
- [OLTP in memoria](https://msdn.microsoft.com/library/dn133186.aspx)
- [Descrizione degli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- L'elaborazione analitica e transazionale ibrida (HTAP), anche nota come [analisi operativa in tempo reale](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Installare l'esempio di OLTP in memoria

È possibile creare il database AdventureWorksLT di esempio con pochi clic nel [portale di Azure](https://portal.azure.com/). I passaggi descritti in questa sezione illustrano come migliorare il database AdventureWorksLT con oggetti OLTP in memoria e dimostra i vantaggi sulle prestazioni.

Per una dimostrazione più semplice e visivamente più interessante sulle prestazioni di OLTP in memoria, vedere:

- Versione: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Codice sorgente: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Procedura di installazione

1. Nel [portale di Azure](https://portal.azure.com/)creare un database premium o business critical in un server. Impostare **Origine** sul database AdventureWorksLT di esempio. Per istruzioni dettagliate, vedere [Creare il primo database SQL di Azure](sql-database-single-database-get-started.md).

2. Connettersi al database con SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Copiare lo [script Transact-SQL OLTP in memoria](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) negli Appunti. Lo script T-SQL crea gli oggetti in memoria necessari nel database AdventureWorksLT di esempio creato nel passaggio 1.

4. Incollare lo script T-SQL in SSMS.exe, quindi eseguirlo. La clausola `MEMORY_OPTIMIZED = ON` è fondamentale nelle istruzioni CREATE TABLE, Ad esempio: 


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Errore 40536


Se viene visualizzato l'errore 40536 quando si esegue lo script T-SQL, verificare se il database supporta le funzionalità in memoria eseguendo questo script T-SQL:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Se il risultato è **0**, le funzionalità in memoria non sono supportate, mentre **1** indica che sono supportate. Per diagnosticare il problema, verificare che il livello di servizio del database sia Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Informazioni sugli elementi ottimizzati per la memoria creati

**Tabelle**: l'esempio contiene le tabelle ottimizzate per la memoria seguenti:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


È possibile esaminare le tabelle ottimizzate per la memoria tramite **Esplora oggetti** in SSMS. Fare doppio clic su **Tabelle** > **Filtro** > **Impostazioni filtro** > **Con ottimizzazione per la memoria**. Il valore è uguale a 1.


In alternativa, è possibile eseguire una query delle viste del catalogo, ad esempio:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Stored procedure compilate in modo nativo**: è possibile esaminare SalesLT.usp_InsertSalesOrder_inmem usando una query delle viste del catalogo:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Eseguire il carico di lavoro OLTP di esempio

L'unica differenza tra le due *stored procedure* seguenti è che la prima usa versioni delle tabelle ottimizzate per la memoria, mentre la seconda usa tabelle basate su disco tradizionali:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


Questa sezione illustra come usare l'utilità **ostress.exe** per eseguire le due stored procedure in condizioni di sovraccarico. È possibile mettere a confronto i tempi necessari per il completamento dei due test di stress.


Quando si esegue ostress.exe, è consigliabile passare valori di parametri specifici a entrambe:

- Eseguire un numero elevato di connessioni simultanee, usando -n100.
- Ripetere ogni ciclo di connessione centinaia di volte, usando -r500.


È opportuno, tuttavia, iniziare con valori molto più bassi, ad esempio -n10 e -r50, per assicurarsi che tutto funzioni correttamente.


### <a name="script-for-ostressexe"></a>Script per ostress.exe


Questa sezione illustra lo script T-SQL incorporato nella riga di comando ostress.exe. Lo script usa gli elementi creati dallo script T-SQL installato in precedenza.


Lo script riportato di seguito inserisce un ordine di vendita di esempio con cinque voci nelle *tabelle* ottimizzate per la memoria seguenti:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```sql
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
    SET @i = @i + 1;
end
```


Per creare la versione *_ondisk* dello script T-SQL precedente per ostress.exe, occorre sostituire le due occorrenze della sottostringa *_inmem* con *_ondisk*. Queste sostituzioni interessano i nomi delle tabelle e delle stored procedure.


### <a name="install-rml-utilities-and-ostress"></a>Installare le utilità RML e `ostress`


È consigliabile pianificare l'esecuzione di ostress.exe su una macchina virtuale di Azure. Creare una [macchina virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) nella stessa area geografica di Azure in cui risiede il database AdventureWorksLT. È possibile eseguire ostress.exe sul computer portatile.


Installare nella macchina virtuale o nell'host scelto le utilità RML (Replay Markup Language), che includono ostress.exe.

Per altre informazioni, vedere:
- La discussione su ostress.exe nell'articolo relativo ai [database di esempio per OLTP in memoria](https://msdn.microsoft.com/library/mt465764.aspx).
- [Database di esempio per OLTP in memoria](https://msdn.microsoft.com/library/mt465764.aspx).
- [Blog sull'installazione di ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Eseguire prima di tutto il test di stress del carico di lavoro per *_inmem*


Per eseguire la riga di comando ostress.exe è possibile usare una finestra del *prompt dei comandi RML* . I parametri della riga di comando indicano a `ostress` di:

- Eseguire 100 connessioni simultaneamente (-n100).
- Fare in modo che ogni connessione esegua lo script T-SQL 50 volte (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Per eseguire la riga di comando ostress.exe precedente:


1. Reimpostare il contenuto dei dati del database eseguendo questo comando in SSMS per eliminare tutti i dati inseriti da esecuzioni precedenti: 

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Copiare il testo della riga di comando ostress.exe precedente negli Appunti.

3. Sostituire `<placeholders>` per i parametri -S -U -P -d con i valori reali corretti.

4. Eseguire la riga di comando modificata in una finestra dei comandi RML.


#### <a name="result-is-a-duration"></a>Il risultato è un intervallo di tempo


Al termine, `ostress.exe` scrive la durata dell'esecuzione come ultima riga di output nella finestra dei comandi RML. Ad esempio, per un'esecuzione dei test più breve, durata circa 1,5 minuti:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Reimpostare, modificare per l'esecuzione *_ondisk* ed eseguire di nuovo il test


Dopo aver ottenuto il risultato dell'esecuzione *_inmem*, seguire la procedura indicata di seguito per l'esecuzione *_ondisk*:


1. Reimpostare il database eseguendo questo comando in SSMS per eliminare tutti i dati inseriti dall'esecuzione precedente:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Modificare la riga di comando ostress.exe per sostituire tutte le occorrenze di *_inmem* con *_ondisk*.

3. Eseguire ostress.exe per la seconda volta e acquisire il risultato relativo alla durata.

4. Reimpostare nuovamente il database, per eliminare in modo responsabile una potenziale grande quantità di dati di test.


#### <a name="expected-comparison-results"></a>Risultati previsti per il confronto

I test delle funzionalità in memoria hanno mostrato un miglioramento delle prestazioni pari a **9 volte** per questo semplice carico di lavoro, con l'utilità `ostress` in esecuzione in una VM di Azure nella stessa area di Azure del database.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Installare l'esempio di analisi in memoria


In questa sezione vengono messi a confronto i risultati di statistiche e IO quando si usa un indice columnstore rispetto a un indice ad albero B tradizionale.


Per l'analisi in tempo reale in un carico di lavoro OLTP, è spesso preferibile usare un indice columnstore non cluster. Per informazioni dettagliate, vedere [Descrizione degli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparare il test di analisi columnstore


1. Usare il portale di Azure per creare un nuovo database AdventureWorksLT dall'esempio.
   - Usare esattamente questo nome.
   - Scegliere qualsiasi livello di servizio Premium.

2. Copiare [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) negli Appunti.
   - Lo script T-SQL crea gli oggetti in memoria necessari nel database AdventureWorksLT di esempio creato nel passaggio 1.
   - Lo script crea la tabella delle dimensioni e due tabelle dei fatti. Ogni tabella dei fatti viene popolata con 3,5 milioni di righe.
   - Il completamento dello script potrebbe richiedere 15 minuti.

3. Incollare lo script T-SQL in SSMS.exe, quindi eseguirlo. La parola chiave **COLUMNSTORE** è fondamentale in una istruzione **CREATE INDEX**, ad esempio:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Impostare AdventureWorksLT sul livello di compatibilità 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Il livello 130 non è direttamente correlato alle funzionalità in memoria, ma offre in genere prestazioni delle query più veloci rispetto al livello 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Tabelle e indici columnstore fondamentali


- dbo.FactResellerSalesXL_CCI è una tabella contenente un indice *columnstore* cluster, che presenta una compressione avanzata a livello di dati.

- dbo.FactResellerSalesXL_PageCompressed è una tabella contenente un indice cluster equivalente tradizionale, che presenta una compressione solo a livello di *pagina*.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Query fondamentali per il confronto dell'indice columnstore


Sono disponibili [diversi tipi di query T-SQ](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) che è possibile eseguire per migliorare le prestazioni. Nel passaggio 2 nello script T-SQL, prestare attenzione a questa coppia di query. Le due query differiscono per una sola riga:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un indice columnstore cluster si trova nella tabella FactResellerSalesXL\_CCI.

L'estratto dallo script T-SQL riportato di seguito permette di stampare le statistiche per IO e TIME per la query di ogni tabella.


```sql
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
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
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
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

In un database con piano tariffario P2 è possibile raggiungere circa 9X il guadagno sulle prestazioni per la query tramite l'indice columnstore cluster rispetto a un indice tradizionale. Con P15, è possibile prevedere un miglioramento delle prestazioni pari a 57X usando l'indice columnstore.



## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido 1: Tecnologie OLTP in memoria per ottimizzare le prestazioni di T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [Usare OLTP in memoria in un'applicazione esistente del database SQL di Azure.](sql-database-in-memory-oltp-migration.md)

- [Monitoraggio dell'archiviazione OLTP in memoria](sql-database-in-memory-oltp-monitoring.md) per OLTP in memoria.


## <a name="additional-resources"></a>Risorse aggiuntive

#### <a name="deeper-information"></a>Approfondimenti

- [Informazioni in Learn how Quorum doubles key database's workload while lowering DTU by 70% with In-Memory OLTP in SQL Databas](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database) (Il quorum raddoppia il carico di lavoro del database principale riducendo il DTU del 70% con OLTP in memoria nel database SQL)

- [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (Post di blog su OLTP nel database SQL di Azure)

- [Informazioni su OLTP in memoria](https://msdn.microsoft.com/library/dn133186.aspx)

- [Informazioni sugli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Informazioni sulle analisi operative in tempo reale](https://msdn.microsoft.com/library/dn817827.aspx)

- Vedere l'articolo sui [modelli comuni dei carichi di lavoro e considerazioni relative alla migrazione](https://msdn.microsoft.com/library/dn673538.aspx), che descrive modelli di carico di lavoro in cui OLTP in memoria fornisce in genere miglioramenti significativi delle prestazioni

#### <a name="application-design"></a>Progettazione di applicazioni

- [OLTP in memoria (ottimizzazione in memoria)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Usare OLTP in memoria in un'applicazione esistente del database SQL di Azure.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Strumenti

- [Portale di Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
