---
title: Tecnologie in memoria di SQL | Documentazione Microsoft
description: Le tecnologie SQL in memoria migliorano notevolmente le prestazioni dei carichi di lavoro transazionali e analitici. Informazioni su come sfruttare i vantaggi di queste tecnologie.
services: sql-database
documentationCenter: 
authors: jodebrui
manager: jhubbard
editor: 
ms.assetid: 250ef341-90e5-492f-b075-b4750d237c05
ms.service: sql-database
ms.custom: db development; monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: jodebrui
translationtype: Human Translation
ms.sourcegitcommit: 0179a926dcee9b225edc4e7cc1c7af675a537ea4
ms.openlocfilehash: a0dbf879a7d37235c54947c59d4cda2d5bbe6feb

---

# <a name="optimize-performance-using-in-memory-technologies-in-sql-database"></a>Ottimizzare le prestazioni tramite le tecnologie in memoria nel database SQL

Le tecnologie in memoria del database SQL di Azure consentono di ottenere miglioramenti delle prestazioni con carichi di lavoro transazionali (OLTP), di analisi (OLAP), nonché misti (HTAP), riducendo allo stesso tempo i costi. A seconda del carico di lavoro, le tecnologie possono consentire di ottenere fino a 30 volte il miglioramento delle prestazioni di elaborazione delle transazioni e fino a 100 volte il miglioramento delle prestazioni delle query analitiche, rispetto agli indici e alle tabelle tradizionali. Grazie alla maggiore efficienza di query ed elaborazione delle transazioni, le tecnologie in memoria consentono anche di ridurre i costi: in genere non è necessario aggiornare il piano tariffario del database per migliorare le prestazioni e in alcuni casi è anche possibile ridurre il piano tariffario continuando a riscontrare un miglioramento delle prestazioni grazie alle tecnologie in memoria. 

Le tecnologie in memoria sono disponibili in tutti i database nel livello Premium, inclusi i database nei pool elastici Premium. 

Il video seguente spiega il potenziale miglioramento delle prestazioni con le tecnologie in memoria nel database SQL di Azure. Tenere presente che il miglioramento delle prestazioni dipende sempre da numerosi fattori, tra cui la natura di carico di lavoro e dei dati, il modello di accesso del database e così via.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Database-In-Memory-Technologies/player]
> 
> 

Il database SQL di Azure dispone delle seguenti tecnologie in memoria:

- *OLTP in memoria*: aumenta la velocità effettiva e riduce la latenza per l'elaborazione delle transazioni. Gli scenari che beneficiano di OLTP In memoria sono: elaborazione transazionale ad alta velocità di elaborazione, come l'inserimento di dati commerciali e di videogiochi, da eventi o dispositivi IoT, il caching, il caricamento di dati, le tabelle temporanee e gli scenari con variabili di tabella.
- *Indici Columnstore nel cluster*: riducono fino a 10 volte il footprint della memoria e migliorano le prestazioni delle query di reporting e analisi. Usare gli indici con tabelle dei fatti nei data mart per inserire più dati nel database e migliorare le prestazioni. Usare gli indici con i dati cronologici nel database operativo per archiviare ed essere in grado di eseguire una query su una quantità di dati 10 volte superiore.
- *Indici Columnstore non nel cluster* per l'elaborazione analitica e transazionale ibrida (HTAP): consente di ottenere informazioni approfondite dell'azienda in tempo reale eseguendo una query direttamente sul database operativo, senza la necessità di eseguire un processo ETL dispendioso e attendere che il data warehouse venga popolato. Gli indici Columnstore non nel cluster consentono l'esecuzione molto rapida delle query di analisi nei database OLTP, riducendo l'impatto sul carico di lavoro operativo.
- È anche possibile combinare OLTP in memoria e Columnstore per avere una tabella con ottimizzazione per la memoria con indice columnstore, che consente di eseguire molto rapidamente l'elaborazione di transazioni e query di analisi notevolmente rapide sugli stessi dati.

Coloumnstore e OLTP In memoria fanno parte di SQL Server rispettivamente dal 2012 dal 2014. Il database SQL di Azure e SQL Server condividono la stessa implementazione di tecnologie in memoria e, in futuro, verranno rilasciate nuove funzionalità per queste tecnologie inizialmente nel database SQL di Azure, prima di essere estese alla prossima versione di SQL Server. 

Questo argomento descrive gli aspetti di OLTP in memoria e degli indici Columnstore specifici del database SQL di Azure con esempi. Verrà innanzitutto analizzato l'impatto di queste tecnologie sulla memoria e i limiti sulle dimensioni dei dati. In un secondo momento si scoprirà come gestire lo spostamento dei database, che consente di sfruttare queste tecnologie tra i diversi piani tariffari. Infine, verranno esaminati due esempi che illustrano l'uso di OLTP in memoria e Columnstore nel database SQL di Azure.

Ai pecorsi seguenti è possibile reperire informazioni approfondite sulle technologie:

- [OLTP in memoria](http://msdn.microsoft.com/library/dn133186.aspx)
- [Descrizione degli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- L'elaborazione analitica e transazionale ibrida, anche nota come [analisi operativa in tempo reale](https://msdn.microsoft.com/library/dn817827.aspx)

Le nozioni di base su OLTP in memoria sono disponibili qui:

- [avvio rapido 1: Tecnologie OLTP in memoria per ottimizzare le prestazioni di T-SQL](http://msdn.microsoft.com/library/mt694156.aspx) .

Video di approfondimento sulle tecnologie:

- [Video OLTP in memoria: Che cos'è e come/quando usarlo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Indice ColumnStore: Video sull'analisi in memoria, ad esempio gli indici columnstore, da Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Dimensioni di archiviazione e dati

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limite su dimensioni dei dati e archiviazione per OLTP in memoria

OLTP in memoria include tabelle con ottimizzazione per la memoria, che vengono usate per archiviare i dati utente. Queste tabelle devono rientrare nella memoria. Dal momento che la memoria viene gestita direttamente nel servizio del database SQL, è necessario un concetto di quota per i dati utente, detto *Archiviazione OLTP in memoria*.

Ogni piano tariffario di database autonomi supportati e ogni piano tariffario per pool elastici include una certa quantità di spazio di archiviazione OLTP in memoria. Al momento della redazione, viene offerto un gigabyte di spazio di archiviazione ogni 125 DTU o eDTU.

[Livelli di servizio del database SQL per database singoli e pool di database elastici](sql-database-service-tiers.md) contiene l'elenco ufficiale dell'archiviazione OLTP in memoria disponibile per ogni piano tariffario di database autonomi e pool elastici supportati.

Gli elementi seguenti rientrano nel limite di archiviazione di OLTP in memoria:

- Righe di dati utente attive nelle tabelle con ottimizzazione per la memoria e variabili di tabella. Si noti che le versioni precedenti della riga non vengono conteggiate nel limite.
- Indici nelle tabelle con ottimizzazione per la memoria.
- Costi operativi delle operazioni ALTER TABLE.

Se si raggiunge il limite, si riceve un errore di superamento della quota e non sarà più possibile inserire o aggiornare dati. Per risolvere il problema, eliminare i dati o aumentare il piano tariffario del database o del pool.


Per dettagli sul monitoraggio dell'uso dell'archiviazione OLTP in memoria e sulla configurazione degli avvisi al raggiungimento del limite, vedere:

- [Monitorare l'archiviazione in memoria](sql-database-in-memory-oltp-monitoring.md)

#### <a name="note-about-elastic-pools"></a>Nota sui pool elastici

Con i pool elastici, l'archiviazione OLTP in memoria è condivisa tra tutti i database nel pool, quindi l'uso in un database può potenzialmente influire sugli altri database. Esistono due metodi per la risoluzione di questo problema:

- Configurare Max-eDTU per i database con numero di eDTU inferiore rispetto al numero di eDTU per l'intero pool. Ciò limita l'uso dell'archiviazione OLTP in memoria nei database del pool alla dimensione corrispondente al numero di eDTU.
- Configurare Min-eDTU su un numero maggiore di 0. In questo modo si garantisce che ogni database nel pool disponga della quantità di spazio di archiviazione OLTP in memoria corrispondente al valore Min-eDTU configurato.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Dimensioni dei dati e archiviazione per gli indici Columnstore

Gli indici ColumnStore non devono essere contenuti nella memoria. L'unico limite alla dimensione degli indici è quindi la dimensione complessiva massima del database, descritta nell'articolo [Livelli di servizio del database SQL per database singoli e pool di database elastici](sql-database-service-tiers.md).

Quando si usano gli indici Columnstore nel cluster, viene impiegata la compressione a colonne per l'archiviazione delle tabelle di base. Ciò può ridurre notevolmente il footprint di archiviazione dei dati utente, ovvero è possibile inserire più dati nel database. Usando la [compressione a colonne dell'archivio](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression), è possibile inserire una quantità ancora maggiore di dati. La quantità di compressione che è possibile ottenere dipende dalla natura dei dati, ma generalmente si aggira intorno a 10 volte (10X) rispetto alla compressione tradizionale.

Ad esempio, se si dispone di un database con dimensioni massime di 1 terabyte (TB) e si raggiunge una compressione 10X tramite columnstore, nel database è possibile inserire un totale di 10 TB di dati utente.

Quando si usano gli indici Columnstore non in cluster, la tabella di base viene comunque archiviata in formato rowstore tradizionale, pertanto il risparmio di memoria non è pari a quello ottenuto con gli indici Columnstore nel cluster. Tuttavia, se si sostituisce un numero di indici non in cluster tradizionali con un indice columnstore singolo, è sempre possibile riscontrare un risparmio complessivo nel footprint della memoria per la tabella.

## <a name="moving-databases-using-in-memory-technologies-between-pricing-tiers"></a>Spostamento dei database tra i piani tariffari tramite tecnologie in memoria

L'upgrade del piano tariffario per un database che usa tecnologie in memoria non implica considerazioni speciali, poiché i piani superiori offrono sempre più funzionalità e risorse. Il downgrade del piano tariffario può avere implicazioni per il database, soprattutto quando si passa da Premium a Standard o Basic e quando si sposta un database usando OLTP in memoria a un livello Premium inferiore. Le stesse considerazioni si applicano quando si esegue il downgrade del piano tariffario di un pool elastico o quando si esegue lo spostamento dei database con tecnologie in memoria in un pool elastico Standard o Basic.

### <a name="in-memory-oltp"></a>OLTP in memoria

*Downgrade al piano Basic o Standard.* OLTP in memoria non è supportato nei database del piano Standard o Basic. Non è possibile spostare un database che dispone di oggetti OLTP in memoria al piano Standard o Basic.

- Prima di eseguire il downgrade del database al livello Standard o Basic, rimuovere tutti i tipi di tabella e le tabelle con ottimizzazione per la memoria, nonché tutti i moduli T-SQL compilati in modo nativo.

Esiste un modo a livello di codice per capire se un determinato database supporta OLTP in memoria. È possibile eseguire la query Transact-SQL seguente:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Se la query restituisce **1**, OLTP in memoria è supportato nel database.


*Downgrade a un piano Premium inferiore.* I dati nelle tabelle con ottimizzazione per la memoria devono essere contenuti nell'archiviazione OLTP in memoria associata al piano tariffario del database o disponibile nel pool elastico. Se si tenta di eseguire il downgrade del piano tariffario o spostare il database in un pool che non dispone di sufficiente spazio di archiviazione OLTP in memoria, l'operazione avrà esito negativo.

### <a name="columnstore-indexes"></a>Indici Columnstore

*Downgrade al piano Basic o Standard.* Gli indici Columnstore non sono supportati nei database del piano Standard o Basic. Se si esegue il downgrade di un database verso il piano Standard o Basic, gli indici columnstore non saranno più disponibili. Se si usa un indice columnstore nel cluster, l'intera tabella non sarà più disponibile.

- Prima di eseguire il downgrade del database al piano Standard o Basic, eliminare tutti gli indici columnstore nel cluster.

*Downgrade a un piano Premium inferiore.* Il downgrade avrà esito positivo se l'intero database rientra nelle dimensioni massime dei database realtive al piano tariffario di destinazione o all'archiviazione disponibile nel pool elastico. Non è previsto alcun impatto specifico dagli indici Columnstore.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>R. Installare l'esempio di OLTP in memoria

È possibile creare il database AdventureWorksLT [V12] di esempio con pochi clic nel [portale di Azure](https://portal.azure.com/). I passaggi descritti in questa sezione illustrano come migliorare il database AdventureWorksLT con oggetti OLTP in memoria e dimostra i vantaggi sulle prestazioni.

Una dimostrazione più semplice e visivamente più interessante sulle prestazioni di OLTP in memoria è disponibile qui:

- Versione: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Codice sorgente: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Procedura di installazione

1. Nel [portale di Azure](https://portal.azure.com/)creare un database Premium in un server versione 12. Impostare **Origine** sul database AdventureWorksLT [V12] di esempio.
 - Per istruzioni dettagliate, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md).

2. Connettersi al database con SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiare lo [script Transact-SQL OLTP in memoria](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) negli Appunti.
 - Lo script T-SQL crea gli oggetti in memoria necessari nel database AdventureWorksLT di esempio creato nel passaggio 1.

4. Incollare lo script T-SQL in SSMS.exe, quindi eseguirlo.
 - La clausola `MEMORY_OPTIMIZED = ON` è fondamentale nelle istruzioni CREATE TABLE, ad esempio:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Errore 40536


Se viene visualizzato l'errore 40536 quando si esegue lo script T-SQL, verificare se il database supporta le funzionalità in memoria eseguendo questo script T-SQL:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Se il risultato è **0** , le funzionalità in memoria non sono supportate, mentre 1 indica che sono supportate. Per diagnosticare il problema:

- Verificare che il livello di servizio del database sia Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Informazioni sugli elementi creati con ottimizzazione per la memoria

**Tabelle**: l'esempio contiene le tabelle con ottimizzazione per la memoria seguenti.

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


È possibile esaminare le tabelle con ottimizzazione per la memoria tramite **Esplora oggetti** in SSMS come indicato di seguito:

- Fare doppio clic su **Tabelle** > **Filtro** > **Impostazioni filtro** > **Con ottimizzazione per la memoria** uguale a 1.


In alternativa, è possibile eseguire una query delle viste del catalogo, ad esempio:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Stored procedure compilata in modo nativo**: è possibile esaminare SalesLT.usp_InsertSalesOrder_inmem usando una query delle viste del catalogo.


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>Eseguire il carico di lavoro OLTP di esempio

L'unica differenza tra le due *stored procedure* seguenti è che la prima usa versioni con ottimizzazione per la memoria delle tabelle, mentre la seconda usa tabelle basate su disco tradizionali:

- SalesLT**.**usp_InsertSalesOrder**_inmem**
- SalesLT**.**usp_InsertSalesOrder**_ondisk**


Questa sezione illustra come usare l'utilità **ostress.exe** per eseguire le due stored procedure in condizioni di sovraccarico. È possibile mettere a confronto i tempi necessari per il completamento dei due test di stress.


Quando si esegue ostress.exe, è consigliabile passare valori di parametri specifici a entrambe:

- Eseguire un numero elevato di connessioni simultanee, usando -n100.
- Ripetere ogni ciclo di connessione centinaia di volte, usando -r500.


È opportuno, tuttavia, iniziare con valori molto più bassi, ad esempio -n10 e -r50, per assicurarsi che tutto funzioni correttamente.


### <a name="script-for-ostressexe"></a>Script per ostress.exe


Questa sezione illustra lo script T-SQL incorporato nella riga di comando ostress.exe. Lo script usa gli elementi creati dallo script T-SQL installato in precedenza.


Lo script riportato di seguito inserisce un ordine di vendita di esempio con cinque voci nelle *tabelle*con ottimizzazione per la memoria seguenti:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


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
    SET @i = @i + 1;
end
```


Per creare la versione *_ondisk* dello script T-SQL precedente per ostress.exe, occorre semplicemente sostituire le due occorrenze della sottostringa *_inmem* con *_ondisk*. Questa sostituzione interessa i nomi delle tabelle e delle stored procedure.


### <a name="install-rml-utilities-and-ostress"></a>Installare le utilità RML e ostress


È consigliabile pianificare l'esecuzione di ostress.exe su una macchina virtuale di Azure. Creare una [macchina virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) nella stessa area geografica di Azure in cui risiede il database AdventureWorksLT. È possibile eseguire ostress.exe sul computer portatile.


Installare nella VM o nell'host scelto le utilità RML (Replay Markup Language) che includono ostress.exe.

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



### <a name="run-the-inmem-stress-workload-first"></a>Eseguire prima di tutto il test di stress del carico di lavoro per *_inmem*


Per eseguire la riga di comando ostress.exe è possibile usare una finestra del *prompt dei comandi RML* . I parametri della riga di comando indicano al comando ostress di:

- Eseguire 100 connessioni simultaneamente (-n100).
- Fare in modo che ogni connessione esegua lo script T-SQL 50 volte (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Per eseguire la riga di comando ostress.exe precedente:


1. Reimpostare il contenuto dei dati del database eseguendo questo comando in SSMS per eliminare tutti i dati inseriti da esecuzioni precedenti: 
```
EXECUTE Demo.usp_DemoReset;
```

2. Copiare il testo della riga di comando ostress.exe precedente negli Appunti.

3. Sostituire `<placeholders>` per i parametri -S -U -P -d con i valori reali corretti.

4. Eseguire la riga di comando modificata in una finestra dei comandi RML.


#### <a name="result-is-a-duration"></a>Il risultato è un intervallo di tempo


Al termine, ostress.exe scrive la durata dell'esecuzione come ultima riga di output nella finestra dei comandi RML. Ad esempio, per un'esecuzione dei test più breve, durata circa 1,5 minuti:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Reimpostare, modificare per l'esecuzione *_ondisk* ed eseguire di nuovo il test


Dopo aver ottenuto il risultato dell'esecuzione *_inmem*, seguire la procedura indicata di seguito per l'esecuzione *_ondisk*:


1. Reimpostare il database eseguendo questo comando in SSMS per eliminare tutti i dati inseriti dall'esecuzione precedente:
```
EXECUTE Demo.usp_DemoReset;
```

2. Modificare la riga di comando ostress.exe per sostituire tutte le occorrenze di *_inmem* con *_ondisk*.

3. Eseguire ostress.exe per la seconda volta e acquisire il risultato relativo alla durata.

4. Reimpostare nuovamente il database, per eliminare in modo responsabile una potenziale grande quantità di dati di test.


#### <a name="expected-comparison-results"></a>Risultati previsti per il confronto

I test delle funzionalità in memoria hanno mostrato un miglioramento delle prestazioni pari a **9 volte** per questo semplice carico di lavoro, con l'utilità ostress in esecuzione in una VM di Azure nella stessa area di Azure del database.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B. Installare l'esempio di analisi in memoria


In questa sezione vengono messi a confronto i risultati di statistiche e IO quando si usa un indice Columnstore rispetto a un indice ad albero B tradizionale.


Per l'analisi in tempo reale in un carico di lavoro OLTP, è spesso preferibile usare un indice columnstore non cluster. Per informazioni dettagliate, vedere [Descrizione degli indici columnstore](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparare il test di analisi columnstore


1. Usare il portale di Azure per creare un nuovo database AdventureWorksLT dall'esempio.
 - Usare esattamente questo nome.
 - Scegliere qualsiasi livello di servizio Premium.

2. Copiare [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) negli Appunti.
 - Lo script T-SQL crea gli oggetti in memoria necessari nel database AdventureWorksLT di esempio creato nel passaggio 1.
 - Lo script crea la tabella delle dimensioni e due tabelle dei fatti. Ogni tabella dei fatti viene popolata con 3,5 milioni di righe.
 - Il completamento dello script potrebbe richiedere 15 minuti.

3. Incollare lo script T-SQL in SSMS.exe, quindi eseguirlo.
 - La parola chiave **COLUMNSTORE** è fondamentale in una istruzione **CREATE INDEX**, ad esempio:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Impostare AdventureWorksLT sul livello di compatibilità 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Il livello 130 non è direttamente correlato alle funzionalità in memoria, ma offre in genere prestazioni delle query più veloci rispetto al livello 120.


#### <a name="crucial-tables-and-columnstore-indexes"></a>Tabelle e indici columnstore fondamentali


- dbo.FactResellerSalesXL_CCI è una tabella contenente un indice **Columnstore** con cluster, che presenta una compressione avanzata a livello di *dati*.

- dbo.FactResellerSalesXL_PageCompressed è una tabella contenente un indice cluster equivalente tradizionale, che presenta una compressione solo a livello di *pagina*.


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>Query fondamentali per il confronto dell'indice columnstore


[Qui](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) sono disponibili diversi tipi di query T-SQL che è possibile eseguire per migliorare le prestazioni. Dal passaggio 2, nello script T-SQL è presente una coppia di query di interesse diretto. Le due query differiscono per una sola riga:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un indice columnstore cluster si trova nella tabella FactResellerSalesXL\_CCI.

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
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

In un database con piano tariffario P2 è possibile raggiungere circa 9X il guadagno sulle prestazioni per la query tramite l'indice columnstore nel cluster rispetto a un indice tradizionale. Con P15 è possibile raggiungere circa 57X il guadagno sulle prestazioni tramite columnstore.



## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido 1: Tecnologie OLTP in memoria per ottimizzare le prestazioni di T-SQL](http://msdn.microsoft.com/library/mt694156.aspx)

- [Usare OLTP in memoria in un'applicazione esistente del database SQL di Azure.](sql-database-in-memory-oltp-migration.md)

- [Monitoraggio dell'archiviazione OLTP in memoria](sql-database-in-memory-oltp-monitoring.md) per OLTP in memoria.


## <a name="additional-resources"></a>Risorse aggiuntive

#### <a name="deeper-information"></a>Approfondimenti

- [Informazioni in Learn how Quorum doubles key database's workload while lowering DTU by 70% with In-Memory OLTP in SQL Databas](https://customers.microsoft.com/en-US/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database) (Il quorum raddoppia il carico di lavoro del database principale riducendo il DTU del 70% con OLTP in memoria nel database SQL)

- [Informazioni su OLTP in memoria](http://msdn.microsoft.com/library/dn133186.aspx)

- [Informazioni sugli indici Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Informazioni sulle analisi operative in tempo reale](http://msdn.microsoft.com/library/dn817827.aspx)

- White paper su [modelli comuni dei carichi di lavoro e considerazioni relative alla migrazione](http://msdn.microsoft.com/library/dn673538.aspx), che descrive modelli di carico di lavoro in cui OLTP in memoria fornisce in genere miglioramenti significativi delle prestazioni.

#### <a name="application-design"></a>Progettazione di applicazioni

- [OLTP in memoria (ottimizzazione in memoria)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Usare OLTP in memoria in un'applicazione esistente del database SQL di Azure.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Strumenti

- [portale di Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)



<!--HONumber=Nov16_HO4-->


