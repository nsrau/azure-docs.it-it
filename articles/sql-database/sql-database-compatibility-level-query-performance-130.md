<properties
	pageTitle="Come valutare il livello di compatibilità | Microsoft Azure"
	description="Passaggi e strumenti per determinare il livello di compatibilità ottimale per il database in Microsoft SQL Server o nel database SQL di Azure"
	services="sql-database"
	documentationCenter=""
	authors="alainlissoir"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.devlang="NA"
	ms.tgt_pltfrm="NA"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="alainl"/>


# Miglioramento delle prestazioni delle query con il livello di compatibilità 130 nel database SQL di Azure


Il database SQL di Azure esegue in modo trasparente centinaia di migliaia di database a vari livelli di compatibilità diversi, mantenendo e garantendo la compatibilità con le versioni precedenti corrispondenti di Microsoft SQL Server per tutti i clienti.

Anche i clienti che aggiornano i database esistenti al livello di compatibilità più recente possono quindi sfruttare i vantaggi delle nuove funzionalità Query Optimizer e Query Processor. Di seguito è riportato un promemoria relativo all'allineamento delle versioni di SQL ai livelli di compatibilità predefiniti.

- 100: in SQL Server 2008 e database SQL di Azure versione 11.
- 110: in SQL Server 2012 e database SQL di Azure versione 11.
- 120: in SQL Server 2014 e database SQL di Azure versione 12.
- 130: in SQL Server 2016 e database SQL di Azure versione 12.


> [AZURE.IMPORTANT] A partire dalla **metà di giugno 2016**, nel database SQL di Azure il livello di compatibilità predefinito sarà 130 anziché 120 per i database **di nuova creazione**.
> 
> I database creati prima della metà di giugno 2016 *non* saranno interessati dalla modifica e manterranno il livello di compatibilità corrente (100, 110 o 120). Il livello di compatibilità non verrà modificato neanche per i database di cui viene eseguita la migrazione dalla versione 11 alla versione 12 del database SQL di Azure.


Questo articolo illustra i vantaggi del livello di compatibilità 130 e come sfruttarli. Vengono discussi i possibili effetti collaterali sulle prestazioni di query per le applicazioni SQL esistenti.


## Informazioni sul livello di compatibilità 130


Per conoscere il livello di compatibilità corrente del database, eseguire prima di tutto l'istruzione Transact-SQL seguente.


```
SELECT compatibility_level
	FROM sys.databases
	WHERE name = '<YOUR DATABASE_NAME>’;
```


Prima del passaggio al livello 130 per i database **di nuova creazione**, questo articolo esamina da vicino la modifica attraverso alcuni esempi di query molto semplici, per valutarne i vantaggi effettivi.

L'elaborazione delle query nei database relazionali può risultare molto complessa e richiedere competenze informatiche e matematiche per comprendere le scelte di progettazione e i comportamenti intrinseci. In questo documento il contenuto è stato semplificato intenzionalmente perché che chiunque abbia una minima competenza tecnica possa comprendere l'impatto della modifica del livello di compatibilità e valutarne i vantaggi per le applicazioni.

Di seguito è riportato un breve riepilogo delle caratteristiche del livello di compatibilità 130. Per altre informazioni, vedere [Livello di compatibilità ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx):

- L'operazione INSERT di un'istruzione INSERT-SELECT può essere multithread o avere un piano parallelo. In precedenza l'operazione era a thread singolo.
- Le tabelle con ottimizzazione per la memoria e le query sulle variabili di tabella ora possono avere piani paralleli. In precedenza l'operazione era a thread singolo.
- Le statistiche per la tabella con ottimizzazione per la memoria ora possono essere campionate e vengono aggiornate automaticamente. Per altre informazioni, vedere [Novità (Motore di database): OLTP in memoria](https://msdn.microsoft.com/library/bb510411.aspx#InMemory).
- Modifiche alla modalità batch rispetto alla modalità riga con gli indici columnstore:
  - L'ordinamento in una tabella con un indice columnstore viene ora eseguito in modalità batch.
  - Le aggregazioni finestra ora vengono eseguite in modalità batch, ad esempio con istruzioni TSQL LAG/LEAD.
  - Le query sulle tabelle columnstore con più clausole distinte operano in modalità batch.
  - Anche le query in esecuzione con DOP=1 o con un piano seriale vengono eseguite in modalità batch.
- I miglioramenti relativi alla stima di cardinalità sono legati effettivamente al livello di compatibilità 120, ma se si esegue un livello di compatibilità inferiore, ovvero 100 o 110, il passaggio al livello di compatibilità 130 porterà anche questi vantaggi, utili per le prestazioni di query delle applicazioni.


## Esercitazione sul livello di compatibilità 130


Preparare, prima di tutto, alcune tabelle, indici e dati casuali creati per provare alcune di queste nuove funzionalità. Gli esempi di script TSQL possono essere eseguiti in SQL Server 2016 o nel database SQL di Azure. Tuttavia, quando si crea un database SQL di Azure, assicurarsi di scegliere come minimo un database P2. Sono necessari almeno due core per consentire il multithreading e poter trarre vantaggio da queste funzionalità.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
	(EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
	(Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
	(c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
	(‘Blue’, RAND() * 100000, RAND() * 100000),
	(‘Yellow’, RAND() * 100000, RAND() * 100000),
	(‘Red’, RAND() * 100000, RAND() * 100000),
	(‘Green’, RAND() * 100000, RAND() * 100000),
	(‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


Vengono ora prese in esame alcune delle funzionalità di elaborazione query offerte dal livello di compatibilità 130.


## INSERT in parallelo


Le istruzioni TSQL seguenti permettono di eseguire l'operazione INSERT con i livelli di compatibilità 120 e 130. Questi eseguono l'operazione INSERT rispettivamente in un modello a thread singolo (120) e in un modello multithread (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
	SELECT C1, COUNT(C2) * 10 * RAND()
		FROM T_source
		GROUP BY C1
	OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
	SELECT C1, COUNT(C2) * 10 * RAND()
		FROM T_source
		GROUP BY C1
	OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


Richiedendo il piano di query effettivo ed esaminandone la rappresentazione grafica o il contenuto XML è possibile determinare la funzione di stima di cardinalità usata. La visualizzazione affiancata della figura 1 mostra chiaramente che l'esecuzione dell'operazione INSERT sull'indice columnstore passa da seriale, nel livello 120, a parallela, nel livello 130. Si noti anche la modifica dell'icona iteratore nel piano 130, con due frecce parallele a indicare il fatto che ora l'esecuzione dell'iteratore è effettivamente parallela. Per il completamento di operazioni INSERT di grandi dimensioni, l'esecuzione parallela, collegata al numero di core disponibili per il database, offre prestazioni migliori e può risultare fino a un 100 volte più veloce, a seconda delle situazioni.


*Figura 1: L'operazione INSERT passa da seriale a parallela con il livello di compatibilità 130.*


![Figura 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## Modalità batch seriale


Analogamente, il passaggio al livello di compatibilità 130 durante l'elaborazione di righe di dati consente l'elaborazione in modalità batch. In primo luogo, le operazioni in modalità batch sono disponibili solo in presenza di un indice columnstore. In secondo luogo, un batch rappresenta in genere circa 900 righe e usa una logica del codice ottimizzata per una CPU multicore, ha una maggiore velocità effettiva della memoria e sfrutta direttamente i dati compressi dell'indice columnstore quando è possibile. In queste condizioni SQL Server 2016 può elaborare circa 900 righe contemporaneamente, anziché 1 riga alla volta. Il costo generale dell'operazione viene quindi condiviso dall'intero batch, con conseguente riduzione del costo complessivo per riga. Questa quantità condivisa di operazioni, unita alla compressione dell'indice columnstore, riduce sostanzialmente la latenza legata a un'operazione SELECT in modalità batch. Per altre informazioni sull'indice columnstore e la modalità batch, vedere [Descrizione degli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx).


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
	FROM T_target
	GROUP BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
	FROM T_target
	GROUP BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Come mostra la visualizzazione affiancata dei piani di query nella figura 2, anche la modalità di elaborazione è cambiata con il livello di compatibilità. Di conseguenza, quando si eseguono query in entrambi i livelli di compatibilità si vede come la maggior parte del tempo di elaborazione sia dedicata alla modalità riga (86%), rispetto alla modalità batch (14%) usata per elaborare due batch. Aumentando le dimensioni del set di dati, aumentano anche i vantaggi.


*Figura 2: L'operazione SELECT passa da seriale alla modalità batch con il livello di compatibilità 130.*


![Figura 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## Modalità batch per l'esecuzione dell'ordinamento


Quanto appena visto si applica anche a un'operazione di ordinamento. La transizione dalla modalità riga nel livello di compatibilità 120 alla modalità batch nel livello di compatibilità 130 migliora le prestazioni dell'operazione SORT, per gli stessi motivi.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
	FROM T_target
	GROUP BY C1
	ORDER BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
	FROM T_target
	GROUP BY C1
	ORDER BY C1
	OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


La visualizzazione affiancata nella figura 3 mostra che l'operazione di ordinamento in modalità riga rappresenta l'81% del costo, mentre la modalità batch rappresenta solo il 19% del costo. Rispettivamente, l'81% e il 56% dell'operazione di ordinamento.


*Figura 3: L'operazione SORT passa dalla modalità riga alla modalità batch con il livello di compatibilità 130.*


![Figura 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Naturalmente, questi esempi contengono solo decine di migliaia di righe, che non sono nulla rispetto alla quantità di dati disponibili nella maggior parte delle istanze di SQL Server di oggi. Su milioni di righe, questo può tradursi in tempi di esecuzione inferiori di diversi minuti ogni giorno, in base alla natura del carico di lavoro.


## Miglioramenti apportati alla stima di cardinalità (CE)


La nuova funzionalità di stima di cardinalità è stata introdotta con SQL Server 2014 ed è inclusa in tutti i database in esecuzione a un livello di compatibilità 120 o superiore. La stima di cardinalità è sostanzialmente la logica usata per determinare il modo in cui SQL Server deve eseguire una query in base al relativo costo stimato. La stima viene calcolata usando informazioni basate sulle statistiche associate agli oggetti coinvolti nella query. In generale, le funzioni di stima di cardinalità sono costituite da stime dei conteggi delle righe, informazioni sulla distribuzione dei valori, conteggi dei valori distinct e conteggi duplicati contenuti in tabelle e oggetti a cui viene fatto riferimento nella query. Un errore in queste stime può portare a operazioni di I/O sul disco non necessarie a causa di concessioni di memoria insufficienti, con conseguenti eventi di spill di TempDB, o alla scelta dell'esecuzione di un piano seriale anziché di un piano parallelo, per fare alcuni esempi. In conclusione, stime non corrette possono portare a una riduzione del livello di prestazioni complessivo dell'esecuzione di query. Stime corrette e più accurate, d'altro canto, migliorano notevolmente l'esecuzione delle query.

Come accennato in precedenza, le stime e l'ottimizzazione delle query sono un argomento complesso. Per informazioni più approfondite sui piani di query e sulla stima di cardinalità, vedere il documento relativo all'[ottimizzazione dei piani di query con la funzionalità di stima di cardinalità di SQL Server 2014](https://msdn.microsoft.com/library/dn673537.aspx).


## Stima di cardinalità corrente


Per determinare la stima di cardinalità in cui vengono eseguite le query è possibile usare gli esempi di query seguenti. Si noti che il primo esempio viene eseguito con il livello di compatibilità 110 e implica l'uso delle funzioni di stima di cardinalità precedenti.


```
-- Old CE

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
	FROM [dbo].[T_target]
	WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Al termine dell'esecuzione, fare clic sul collegamento XML ed esaminare le proprietà del primo iteratore, come illustrato di seguito. Si noti che il nome della proprietà CardinalityEstimationModelVersion attualmente è impostato su 70. Questo non significa che il livello di compatibilità del database sia impostato su SQL Server versione 7.0. Come si può vedere nelle istruzioni TSQL precedenti, è impostato su 110. Il valore 70 rappresenta semplicemente la funzionalità di stima di cardinalità legacy disponibile a partire da SQL Server 7.0, che non ha subito modifiche importanti fino a SQL Server 2014 e al relativo livello di compatibilità 120.


*Figura 4: CardinalityEstimationModelVersion impostato su 70 quando si usa un livello di compatibilità 110 o inferiore.*


![Figura 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


In alternativa, è possibile modificare il livello di compatibilità in 130 e disabilitare l'uso della nuova funzione di stima di cardinalità impostando LEGACY\_CARDINALITY\_ESTIMATION su ON con l'istruzione [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx). In termini di funzione di stima di cardinalità questo equivale a usare il livello 110, pur facendo uso del livello di compatibilità più recente per l'elaborazione delle query. Così facendo, è possibile sfruttare le nuove funzionalità di elaborazione query incluse nel livello di compatibilità più recente, ovvero la modalità batch, ma poter fare uso all'occorrenza della funzione di stima di cardinalità precedente.


```
-- Old CE

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
	FROM [dbo].[T_target]
	WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Il semplice passaggio al livello di compatibilità 120 o 130 abilita la nuova funzionalità di stima di cardinalità. In tal caso, il valore predefinito di CardinalityEstimationModelVersion sarà impostato su 120 o 130, come mostrato nella figura seguente.


```
-- New CE

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
	FROM [dbo].[T_target]
	WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figura 5: CardinalityEstimationModelVersion impostato su 130 quando si usa un livello di compatibilità 130.*


![Figura 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## Osservazione delle differenze nella stima di cardinalità


A questo punto, provare a eseguire una query leggermente più complessa che preveda un INNER JOIN con una clausola WHERE e alcuni predicati. Esaminare prima di tutto la stima del conteggio delle righe ottenuta dalla funzione di stima di cardinalità precedente.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
	FROM
		           [dbo].[T_source] S
		INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
	WHERE
		S.[Color] = ‘Red’  AND
		S.[c2] > 2000  AND
		T.[c2] > 2000
	OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


L'esecuzione di questa query restituisce in effetti 200.704 righe, mentre la stima delle righe prodotta dalla funzione di stima di cardinalità precedente indica 194.284 righe. Come detto in precedenza, i risultati di conteggio delle righe variano anche in base alla frequenza con cui sono stati eseguiti gli esempi precedenti, che a ogni esecuzione popolano le tabelle di esempio. Anche i predicati della query hanno un effetto sulla stima effettiva, insieme alla forma della tabella, al contenuto dei dati e al modo in cui i dati sono effettivamente correlati tra loro.


*Figura 6: La stima del conteggio delle righe è 194.284. 6.000 righe di differenza rispetto alle 200.704 righe effettive.*


![Figura 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


Eseguire ora la stessa query con la nuova funzione di stima di cardinalità.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
	SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
	SCOPED CONFIGURATION
	SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
	FROM
		           [dbo].[T_source] S
		INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
	WHERE
		S.[Color] = ‘Red’  AND
		S.[c2] > 2000  AND
		T.[c2] > 2000
	OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Esaminando la figura seguente, si nota che la stima delle righe è ora 202.877. Molto più vicina al conteggio effettivo e superiore alla stima di cardinalità precedente.

*Figura 7: La stima del conteggio delle righe è ora 202.877 anziché 194.284.*


![Figura 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


Il risultato effettivo è di 200.704 righe. Tale risultato dipende però dalla frequenza con cui sono state eseguite le query degli esempi precedenti. È importante sottolineare anche che dato l'uso dell'istruzione RAND() in TSQL, i valori effettivi restituiti possono variare da un'esecuzione all'altra. In questo particolare esempio, quindi, con una stima del numero di righe pari a 202.877, la nuova stima di cardinalità si avvicina di più al risultato effettivo di 200.704, rispetto alle 194.284 della funzionalità precedente. Infine, se si modificano i predicati della clausola WHERE in uguaglianze anziché usare, ad esempio, il simbolo ">", la differenza tra le stime della nuova funzione di cardinalità e di quella precedente può aumentare ancora di più, a seconda del numero di corrispondenze che è possibile ottenere.

In questo caso, naturalmente, una differenza di circa 6000 righe rispetto al conteggio effettivo non rappresenta una grande quantità di dati, in alcune situazioni. Nella realtà si tratta spesso di milioni di righe su numerose tabelle e di query più complesse. In alcuni casi le stime possono sbagliare di milioni di righe e questo può portare a scegliere un piano di esecuzione sbagliato o a richiedere concessioni di memoria insufficienti, con conseguenti eventi di spill di TempDB e aumento delle operazioni di I/O.

Se possibile, provare a fare questo confronto con le query e i set di dati più usati, per scoprire le differenze tra le vecchie e le nuove stime. Alcune potrebbero discostarsi ancora di più dai valori effettivi, mentre altre potrebbero avvicinarsi di più al conteggio delle righe effettivamente restituito nei set di risultati. Tutto dipende dalla forma delle query, dalle caratteristiche del database SQL di Azure, dalla natura e dalle dimensioni dei set di dati e dalle relative statistiche disponibili. Se l'istanza di database SQL di Azure è stata appena creata, Query Optimizer dovrà compilare le informazioni da zero anziché riutilizzare le statistiche relative alle esecuzioni precedenti della query. Le stime, quindi, dipendono molto dal contesto sono quasi specifiche dei singoli server e delle situazioni relative alle applicazioni. Questo è un aspetto importante da tenere presente.


## Alcune considerazioni importanti


Anche se la maggior parte dei carichi di lavoro può trarre vantaggio dal livello di compatibilità 130, prima di adottare questo livello di compatibilità per l'ambiente di produzione occorre valutare le tre opzioni disponibili:

1. Passare al livello di compatibilità 130 e osservare i risultati. Se si nota un peggioramento, è sufficiente impostare di nuovo il livello di compatibilità sul valore originale. In alternativa, è possibile lasciare il livello impostato su 130 ma ripristinare la modalità legacy della stima di cardinalità, come descritto in precedenza. Quest'ultima operazione potrebbe già risolvere il problema.
2. Eseguire test approfonditi sulle applicazioni esistenti con un carico di produzione simile, ottimizzare e convalidare le prestazioni prima di passare all'ambiente di produzione. In caso di problemi, come per l'opzione precedente è possibile tornare al livello di compatibilità originale o semplicemente ripristinare la modalità legacy della stima di cardinalità.
3. Usare Archivio query. Quest'ultima opzione rappresenta il modo più recente di risolvere il problema ed è anche l'opzione consigliata. Per semplificare l'analisi delle query al di sotto del livello di compatibilità 120 rispetto al livello 130, la soluzione più efficace è sicuramente Archivio query. Archivio query è disponibile con la versione più recente del database SQL di Azure V12 ed è progettato per la risoluzione dei problemi di prestazioni delle query. Archivio query agisce nel database come una scatola nera, che registra dati e raccoglie e presenta informazioni cronologiche dettagliate su tutte le query. Questo semplifica notevolmente le analisi sulle prestazioni riducendo il tempo necessario per diagnosticare e risolvere i problemi. Per altre informazioni, vedere il post di blog relativo ad [Archivio query, la scatola nera del database](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).


A livello generale, se è già disponibile un set di database in esecuzione al livello di compatibilità 120 o inferiore e si prevede di impostare alcuni di essi sul livello 130, o se il carico di lavoro effettua automaticamente il provisioning di nuovi database che verranno presto impostati su 130 come valore predefinito, prendere in considerazione quanto segue:

- Prima di passare al nuovo livello di compatibilità nell'ambiente di produzione, abilitare Archivio query. Per altre informazioni, vedere l'articolo relativo alla [modifica della modalità di compatibilità del database e uso di Archivio query](https://msdn.microsoft.com/library/bb895281.aspx).
- Successivamente, testare tutti i carichi di lavoro critici con query e dati rappresentativi di un ambiente di produzione e confrontare le prestazioni registrate e segnalate da Archivio query. Se si verifica un peggioramento è possibile usare Archivio query per identificare le query peggiorate e usare l'opzione di Archivio query per l'uso forzato del piano, anche detta associazione del piano. In tal caso, si può lasciare il livello di compatibilità impostato su 130 e usare il piano di query precedente come indicato da Archivio query.
- Se si vuole sfruttare le nuove funzionalità del database SQL di Azure, che esegue SQL Server 2016, ma si preferisce evitare le modifiche introdotte con il livello di compatibilità 130, come ultima soluzione è possibile forzare il ripristino del livello di compatibilità al livello appropriato per il carico di lavoro tramite un'istruzione ALTER DATABASE. Tenere presente, però, che l'opzione di Archivio query per l'uso forzato del piano rimane comunque la soluzione migliore. Questo perché usare un livello di compatibilità diverso da 130 significa sostanzialmente rimanere al livello di funzionalità di una versione precedente di SQL Server.
- Se sono presenti applicazioni multi-tenant in più database, potrebbe essere necessario aggiornare la logica di provisioning dei database per garantire la coerenza del livello di compatibilità in tutti i database di cui è stato eseguito il provisioning più di recente o meno. Le prestazioni del carico di lavoro dell'applicazione potrebbero risentire del fatto che alcuni database vengono eseguiti a livelli di compatibilità diversi. La coerenza del livello di compatibilità in tutti i database potrebbe quindi essere necessaria per fornire la stessa esperienza a tutti i clienti. Si noti che la coerenza non è obbligatoria. Dipende unicamente dall'effetto del livello di compatibilità sull'applicazione.
- Riguardo alla stima di cardinalità, come per la modifica del livello di compatibilità, prima di procedere nell'ambiente di produzione è consigliabile testare il carico di lavoro di produzione con le nuove condizioni, per determinare se l'applicazione trae vantaggio dai miglioramenti alla stima di cardinalità.


## Conclusione


L'uso del database SQL di Azure per sfruttare i vantaggi dei miglioramenti di SQL Server 2016 può migliorare notevolmente l'esecuzione delle query. È un dato di fatto. Naturalmente, come per qualsiasi nuova funzionalità è necessaria un'attenta valutazione per determinare le condizioni esatte in cui il carico di lavoro del database funziona al meglio. L'esperienza dimostra che, al livello di compatibilità 130, la maggior parte dei carichi di lavoro viene eseguita almeno in modo trasparente, sfruttando le nuove funzioni di elaborazione delle query e la nuova stima di cardinalità. Ciò detto, esistono sempre alcune eccezioni ed è importante eseguire le opportune verifiche per quantificare il vantaggio effettivo di tali miglioramenti. Anche in questo caso Archivio query può rivelarsi una risorsa molto utile.

Con l'evoluzione di SQL Azure, si arriverà probabilmente a un livello di compatibilità 140. Al momento opportuno verranno trattate anche le nuove caratteristiche del futuro livello di compatibilità 140, come si è fatto brevemente in questo articolo per il livello di compatibilità 130.

Per ora è importante sottolineare ancora una volta che, a partire da giugno 2016, il livello di compatibilità predefinito nel database SQL di Azure per i database di nuova creazione passerà da 120 a 130. Tenere presente questa informazione.


## Riferimenti


- [Novità (Motore di database)](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Post di blog: Archivio query, la scatola nera del database, di Borko Novakovic, 8 giugno 2016](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [Livello di compatibilità ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx)

- [Livello di compatibilità 130 per il database SQL di Azure versione 12](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- [Ottimizzazione dei piani di query con la funzionalità di stima di cardinalità di SQL Server 2014](https://msdn.microsoft.com/library/dn673537.aspx)

- [Descrizione degli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Post di blog: Miglioramento delle prestazioni delle query con il livello di compatibilità 130 nel database SQL di Azure, di Alain Lissoir, 6 maggio 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->

<!---HONumber=AcomDC_0810_2016-->