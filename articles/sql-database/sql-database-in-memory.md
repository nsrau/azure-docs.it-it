---
title: Tecnologie in memoria del database SQL di Azure | Documentazione Microsoft
description: Le tecnologie in memoria del database SQL di Azure migliorano notevolmente le prestazioni dei carichi di lavoro transazionali e analitici. Informazioni su come sfruttare i vantaggi di queste tecnologie.
services: sql-database
documentationCenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: 250ef341-90e5-492f-b075-b4750d237c05
ms.service: sql-database
ms.custom: develop databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jodebrui
ms.openlocfilehash: 4cb45551c486263f26947e5684d54b4f2ecc7410
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Ottimizzare le prestazioni tramite le tecnologie in memoria nel database SQL

Tramite le tecnologie in memoria del database SQL di Azure, è possibile migliorare le prestazioni di diversi carichi di lavoro: transazionale (elaborazione transazionale online o OLTP), analitica (elaborazione analitica online o OLAP) e mista (elaborazione ibrida transazione/analitica o HTAP). Grazie a una più efficiente elaborazione delle query e delle transazioni, le tecnologie in memoria aiutano a ridurre i costi. In genere non è necessario aggiornare il piano tariffario del database per migliorare le prestazioni. In alcuni casi infatti le tecnologie in memoria consentono di ridurre il piano tariffario e di osservare al contempo miglioramenti delle prestazioni.

Di seguito sono riportati due esempi che mostrano come la tecnologia OLTP in memoria abbia contribuito a migliorare significativamente le prestazioni.

- Sfruttando la tecnologia OLTP in memoria [Quorum Business Solutions è riuscita a raddoppiare il carico di lavoro migliorando i valori DTU del 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU significa *unità di velocità effettiva database* e include una misurazione del consumo di risorse.
- Il video seguente illustra un miglioramento significativo nell'uso delle risorse con un carico di lavoro di esempio: [In-Memory OLTP in Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (Video su OLTP in memoria nel database SQL di Azure).
    - Per altre informazioni, vedere il post di blog: [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (Post di blog su OLTP nel database SQL di Azure)

Le tecnologie in memoria sono disponibili in tutti i database nel livello Premium, inclusi i database nei pool elastici Premium.

Il video seguente spiega il potenziale miglioramento delle prestazioni con le tecnologie in memoria nel database SQL di Azure. Tenere presente che il miglioramento delle prestazioni dipende sempre da numerosi fattori, tra cui la natura del carico di lavoro e dei dati, il modello di accesso del database e così via.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Il database SQL di Azure dispone delle seguenti tecnologie in memoria:

- *OLTP in memoria*: aumenta la velocità effettiva e riduce la latenza per l'elaborazione delle transazioni. Gli scenari che beneficiano dell'OLTP in memoria sono: elaborazione transazionale ad alta velocità di elaborazione, come l'inserimento di dati commerciali e da videogiochi, da eventi o dispositivi IoT, il caching, il caricamento di dati, le tabelle temporanee e gli scenari con variabili di tabella.
- Gli *indici columnstore cluster* riducono fino a 10 volte il footprint della memoria e migliorano le prestazioni delle query di reporting e analisi. È possibile usare gli indici con tabelle dei fatti nei data mart per inserire più dati nel database e migliorare le prestazioni. Gli indici possono anche essere usati con i dati cronologici nel database operativo per archiviare ed essere in grado di eseguire una query su una quantità di dati 10 volte superiore.
- Gli *indici columnstore non cluster* per HTAP consentono di ottenere in tempo reale informazioni approfondite sull'azienda eseguendo una query direttamente sul database operativo, senza la necessità di eseguire un processo ETL dispendioso e attendere che il data warehouse venga popolato. Gli indici columnstore non cluster consentono l'esecuzione molto rapida delle query di analisi nei database OLTP, riducendo l'impatto sul carico di lavoro operativo.
- È possibile anche disporre di una tabella ottimizzata per la memoria con un indice columnstore. Tale combinazione consente di elaborare le transazioni e *al contempo* di eseguire le query di analisi sugli stessi dati in tempi estremamente rapidi.

Gli indici columnstore e OLTP in memoria fanno parte di SQL Server rispettivamente dal 2012 e dal 2014. Il database SQL di Azure e SQL Server condividono la stessa implementazione delle tecnologie in memoria. In futuro, le nuove funzionalità per queste tecnologie verranno integrate prima nel database SQL di Azure e poi in SQL Server.

Questo argomento descrive gli aspetti di OLTP in memoria e degli indici columnstore specifici del database SQL di Azure e include alcuni esempi:
- Viene analizzato l'impatto di queste tecnologie sulla memoria e i limiti sulle dimensioni dei dati.
- Verrà illustrato come gestire lo spostamento dei database che sfruttano queste tecnologie tra i diversi piani tariffari.
- Verranno esaminati due esempi che illustrano l'uso di OLTP in memoria e degli indici columnstore nel database SQL di Azure.

Per altre informazioni, vedere le risorse seguenti.

Approfondimento sulle tecnologie:

- [Panoramica e scenari di utilizzo](https://msdn.microsoft.com/library/mt774593.aspx), inclusi riferimenti a casi di studio sui clienti e informazioni introduttive
- [OLTP in memoria](http://msdn.microsoft.com/library/dn133186.aspx)
- [Descrizione degli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- L'elaborazione analitica e transazionale ibrida (HTAP), anche nota come [analisi operativa in tempo reale](https://msdn.microsoft.com/library/dn817827.aspx)

Nozioni di base sull'OLTP in memoria: [Avvio rapido 1: Tecnologie OLTP in memoria per ottimizzare le prestazioni di T-SQL](http://msdn.microsoft.com/library/mt694156.aspx), un altro articolo introduttivo

Video di approfondimento sulle tecnologie:

- [OLTP in memoria nel database SQL di Azure](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB), che contiene una demo dei vantaggi in termini di prestazioni e i passaggi per riprodurre tali risultati manualmente
- [Video OLTP in memoria: Che cos'è e come/quando usarlo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Indice ColumnStore: Video sull'analisi in memoria da Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Dimensioni di archiviazione e dati

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limite su dimensioni dei dati e archiviazione per OLTP in memoria

OLTP in memoria include tabelle con ottimizzazione per la memoria, che vengono usate per archiviare i dati utente. Queste tabelle devono rientrare nella memoria. Poiché la memoria è gestita direttamente nel servizio del database SQL, esiste il concetto di quota per i dati utente. Questo concetto è definito *archiviazione di OLTP in memoria*.

Ogni piano tariffario relativo a database autonomi e pool elastici supportati include una certa quantità di spazio di archiviazione OLTP in memoria. Al momento della redazione di questo articolo, è disponibile un gigabyte di spazio di archiviazione per ogni 125 unità di transazione di database (DTU) o unità di transazione di database elastico (eDTU).

L'articolo sui [Livelli di servizio del database SQL](sql-database-service-tiers.md) contiene l'elenco ufficiale dello spazio di archiviazione OLTP in memoria disponibile per ogni piano tariffario di database autonomi e pool elastici supportati.

Gli elementi seguenti rientrano nel limite di archiviazione di OLTP in memoria:

- Righe di dati utente attive nelle tabelle con ottimizzazione per la memoria e variabili di tabella. Si noti che le versioni precedenti della riga non vengono conteggiate nel limite.
- Indici nelle tabelle con ottimizzazione per la memoria.
- Costi operativi delle operazioni ALTER TABLE.

Se si raggiunge il limite, si riceve un errore di superamento della quota e non sarà più possibile inserire o aggiornare dati. Per risolvere il problema, eliminare i dati o aumentare il piano tariffario del database o del pool.

Per dettagli sul monitoraggio dell'uso dello spazio di archiviazione OLTP in memoria e sulla configurazione degli avvisi al raggiungimento del limite, vedere [Monitorare l'archiviazione in memoria](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Informazioni sui pool elastici

Con i pool elastici, lo spazio di archiviazione OLTP in memoria è condiviso tra tutti i database nel pool. Ne consegue che l'utilizzo in un database può potenzialmente influire sugli altri database. Esistono due metodi per la risoluzione di questo problema:

- Configurare Max-eDTU per i database con numero di eDTU inferiore rispetto al numero di eDTU per l'intero pool. Ciò limita l'uso dello spazio di archiviazione OLTP in memoria in qualsiasi database del pool alla dimensione corrispondente al numero di eDTU.
- Configurare Min-eDTU su un valore maggiore di 0. In questo modo si garantisce che ogni database nel pool abbia a disposizione la quantità di spazio di archiviazione OLTP in memoria corrispondente al valore Min-eDTU configurato.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Dimensioni dei dati e archiviazione per gli indici columnstore

Gli indici columnstore non devono essere contenuti nella memoria. L'unico limite alla dimensione degli indici è quindi la dimensione complessiva massima del database, descritta nell'articolo [Livelli di servizio del database SQL](sql-database-service-tiers.md).

Quando si usano gli indici columnstore cluster, viene impiegata la compressione a colonne per l'archiviazione delle tabelle di base. Ciò può ridurre notevolmente il footprint di archiviazione dei dati utente, ovvero è possibile inserire più dati nel database. Usando la [compressione a colonne dell'archivio](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression), è possibile inserire una quantità ancora maggiore di dati. La quantità di compressione che è possibile ottenere dipende dalla natura dei dati, ma generalmente si aggira intorno a 10 volte (10X) la compressione tradizionale.

Ad esempio, se si dispone di un database con dimensioni massime di 1 terabyte (TB) e si raggiunge una compressione 10X tramite columnstore, nel database è possibile inserire un totale di 10 TB di dati utente.

Quando si usano indici columnstore non cluster, la tabella di base è ancora archiviata nel formato rowstore tradizionale. Pertanto, il risparmio di archiviazione non è paragonabile a quello ottenuto con gli indici columnstore cluster. Tuttavia, se si sostituisce un numero di indici non in cluster tradizionali con un indice columnstore singolo, è sempre possibile riscontrare un risparmio complessivo nel footprint della memoria per la tabella.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Spostamento dei database tra i piani tariffari tramite tecnologie in memoria

Passando a un piano tariffario superiore, ad esempio da Standard a Premium, non si corre mai il rischio di incompatibilità o altri problemi. Il passaggio implica semplicemente un aumento di funzionalità e risorse.

Tuttavia, eseguire il downgrade del piano tariffario può avere un impatto negativo sul database. Questo impatto è particolarmente evidente quando si effettua il downgrade da Premium a Standard o Basic nei casi in cui il database contenga oggetti di OLTP In memoria. Le tabelle ottimizzate per la memoria e gli indici columnstore non sono disponibili dopo il downgrade, anche se dovessero rimanere visibili. Le stesse considerazioni si applicano quando si effettua il downgrade del piano tariffario di un pool elastico o quando si esegue lo spostamento dei database con tecnologie in memoria in un pool elastico Standard o Basic.

### <a name="in-memory-oltp"></a>OLTP in memoria

*Downgrade a Basic/Standard*: OLTP in memoria non è supportato nei database del piano Standard o Basic. Non è possibile spostare un database che contiene oggetti OLTP in memoria al piano tariffario Standard o Basic.

Prima di eseguire il downgrade del database al livello Standard o Basic, rimuovere tutti i tipi di tabella e le tabelle con ottimizzazione per la memoria, nonché tutti i moduli T-SQL compilati in modo nativo.

Esiste un modo a livello di codice per capire se un determinato database supporta OLTP in memoria. È possibile eseguire la query Transact-SQL seguente:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Se la query restituisce **1**, OLTP in memoria è supportato nel database.


*Downgrade a un livello Premium inferiore*: i dati nelle tabelle con ottimizzazione per la memoria devono essere contenuti nell'archiviazione OLTP in memoria associata al piano tariffario del database o disponibile nel pool elastico. Se si tenta di eseguire il downgrade del piano tariffario o di spostare il database in un pool che non dispone di sufficiente spazio di archiviazione OLTP in memoria, l'operazione avrà esito negativo.

### <a name="columnstore-indexes"></a>Indici Columnstore

*Downgrade a Basic o Standard*: gli indici columnstore sono supportati solo nel piano tariffario Premium e non nei livelli Standard o Basic. Quando si effettua il downgrade del database al piano Standard o Basic, l'indice columnstore non è più disponibile. Il sistema conserva l'indice columnstore, ma non lo usa mai. Se in seguito si torna al piano Premium, l'indice columnstore torna subito disponibile all'uso.

Se dispone di un indice columnstore **cluster**, l'intera tabella non sarà più disponibile dopo il downgrade del livello. Pertanto è consigliabile eliminare tutti gli indici columnstore *cluster* prima di effettuare il downgrade del database al di sotto del livello Premium.

*Downgrade a un livello Premium inferiore*: il downgrade avrà esito positivo se l'intero database rientra nelle dimensioni massime dei database relative al piano tariffario di destinazione o all'archiviazione disponibile nel pool elastico. Non è previsto alcun impatto specifico dagli indici columnstore.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Installare l'esempio di OLTP in memoria

È possibile creare il database AdventureWorksLT di esempio con pochi clic nel [portale di Azure](https://portal.azure.com/). I passaggi descritti in questa sezione illustrano come migliorare il database AdventureWorksLT con oggetti OLTP in memoria e dimostra i vantaggi sulle prestazioni.

Per una dimostrazione più semplice e visivamente più interessante sulle prestazioni di OLTP in memoria, vedere:

- Versione: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Codice sorgente: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Procedura di installazione

1. Nel [portale di Azure](https://portal.azure.com/)creare un database Premium in un server. Impostare **Origine** sul database AdventureWorksLT di esempio. Per istruzioni dettagliate, vedere [Creare il primo database SQL di Azure](sql-database-get-started-portal.md).

2. Connettersi al database con SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiare lo [script Transact-SQL OLTP in memoria](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) negli Appunti. Lo script T-SQL crea gli oggetti in memoria necessari nel database AdventureWorksLT di esempio creato nel passaggio 1.

4. Incollare lo script T-SQL in SSMS.exe, quindi eseguirlo. La clausola `MEMORY_OPTIMIZED = ON` è fondamentale nelle istruzioni CREATE TABLE, ad esempio:


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


Se il risultato è **0**, le funzionalità in memoria non sono supportate, mentre **1** indica che sono supportate. Per diagnosticare il problema, verificare che il livello di servizio del database sia Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Informazioni sugli elementi creati con ottimizzazione per la memoria

**Tabelle**: l'esempio contiene le tabelle con ottimizzazione per la memoria seguenti.

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


È possibile esaminare le tabelle con ottimizzazione per la memoria tramite **Esplora oggetti** in SSMS. Fare doppio clic su **Tabelle** > **Filtro** > **Impostazioni filtro** > **Con ottimizzazione per la memoria**. Il valore è uguale a 1.


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

### <a name="run-the-sample-oltp-workload"></a>Eseguire il carico di lavoro OLTP di esempio

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


Per creare la versione *_ondisk* dello script T-SQL precedente per ostress.exe, occorre sostituire le due occorrenze della sottostringa *_inmem* con *_ondisk*. Queste sostituzioni interessano i nomi delle tabelle e delle stored procedure.


### <a name="install-rml-utilities-and-ostress"></a>Installare le utilità RML e ostress


È consigliabile pianificare l'esecuzione di ostress.exe su una macchina virtuale di Azure. Creare una [macchina virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) nella stessa area geografica di Azure in cui risiede il database AdventureWorksLT. È possibile eseguire ostress.exe sul computer portatile.


Installare nella macchina virtuale o nell'host scelto le utilità RML (Replay Markup Language), che includono ostress.exe.

Per altre informazioni, vedere:
- La discussione su ostress.exe nell'articolo relativo ai [database di esempio per OLTP in memoria](http://msdn.microsoft.com/library/mt465764.aspx).
- [Database di esempio per OLTP in memoria](http://msdn.microsoft.com/library/mt465764.aspx).
- [Blog sull'installazione di ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



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

    ``` tsql
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

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Installare l'esempio di analisi in memoria


In questa sezione vengono messi a confronto i risultati di statistiche e IO quando si usa un indice columnstore rispetto a un indice ad albero B tradizionale.


Per l'analisi in tempo reale in un carico di lavoro OLTP, è spesso preferibile usare un indice columnstore non cluster. Per informazioni dettagliate, vedere [Descrizione degli indici columnstore](http://msdn.microsoft.com/library/gg492088.aspx).



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

- [Avvio rapido 1: Tecnologie OLTP in memoria per ottimizzare le prestazioni di T-SQL](http://msdn.microsoft.com/library/mt694156.aspx)

- [Usare OLTP in memoria in un'applicazione esistente del database SQL di Azure.](sql-database-in-memory-oltp-migration.md)

- [Monitoraggio dell'archiviazione OLTP in memoria](sql-database-in-memory-oltp-monitoring.md) per OLTP in memoria.


## <a name="additional-resources"></a>Risorse aggiuntive

#### <a name="deeper-information"></a>Approfondimenti

- [Informazioni in Learn how Quorum doubles key database's workload while lowering DTU by 70% with In-Memory OLTP in SQL Databas](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database) (Il quorum raddoppia il carico di lavoro del database principale riducendo il DTU del 70% con OLTP in memoria nel database SQL)

- [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (Post di blog su OLTP nel database SQL di Azure)

- [Informazioni su OLTP in memoria](http://msdn.microsoft.com/library/dn133186.aspx)

- [Informazioni sugli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Informazioni sulle analisi operative in tempo reale](http://msdn.microsoft.com/library/dn817827.aspx)

- Vedere l'articolo sui [modelli comuni dei carichi di lavoro e considerazioni relative alla migrazione](http://msdn.microsoft.com/library/dn673538.aspx), che descrive modelli di carico di lavoro in cui OLTP in memoria fornisce in genere miglioramenti significativi delle prestazioni

#### <a name="application-design"></a>Progettazione di applicazioni

- [OLTP in memoria (ottimizzazione in memoria)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Usare OLTP in memoria in un'applicazione esistente del database SQL di Azure.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Strumenti

- [Portale di Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
