---
title: Procedure consigliate per l'analisi SQL in Azure sinapsi Analytics (in precedenza SQL DW)
description: Suggerimenti e procedure consigliate per lo sviluppo di soluzioni per l'analisi SQL in Azure sinapsi Analytics (in precedenza SQL DW).
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6a9d7c4ed020f447e9512c7bc2edd0caecf3400d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646182"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Procedure consigliate per l'analisi SQL in Azure sinapsi Analytics (in precedenza SQL DW)

Questo articolo è una raccolta di procedure consigliate che consentono di ottenere prestazioni ottimali dalla distribuzione di [analisi SQL](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) .  Lo scopo di questo articolo è fornire alcune linee guida di base ed evidenziare importanti aree di interesse.  Ogni sezione presenta un concetto e quindi punta a articoli più dettagliati che trattano il concetto in modo più approfondito. La sequenza di argomenti è nell'ordine di importanza. 

## <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire la potenza di calcolo](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Gestire le statistiche
Azure SQL Data Warehouse può essere configurato per rilevare e creare automaticamente statistiche sulle colonne.  I piani di query creati dall'utilità di ottimizzazione sono validi solo per le statistiche disponibili.  È consigliabile abilitare AUTO_CREATE_STATISTICS per i database e mantenere aggiornate le statistiche ogni giorno o dopo ogni carico per garantire che le statistiche sulle colonne utilizzate nelle query siano sempre aggiornate. 

Se si ritiene che l'aggiornamento di tutte le statistiche richieda troppo tempo, è consigliabile provare a usare più selettivamente le colonne che necessitano di aggiornamenti frequenti delle statistiche. Potrebbe ad esempio essere consigliabile aggiornare le colonne di data, in cui potrebbero venire aggiunti nuovi valori ogni giorno. **Per ottenere il massimo vantaggio, è necessario aggiornare le statistiche sulle colonne incluse nei join, sulle colonne utilizzate nella clausola WHERE e sulle colonne presenti in GROUP BY.**

Vedere anche [Gestire le statistiche delle tabelle][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS] e [UPDATE STATISTICS][UPDATE STATISTICS].

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilizzare DMV per monitorare e ottimizzare le query
SQL Analytics include diversi DMV che possono essere usati per monitorare l'esecuzione delle query.  L'articolo relativo al monitoraggio indicato di seguito fornisce istruzioni dettagliate su come esaminare i dati di una query in esecuzione.  Per trovare rapidamente le query in queste DMV, può essere utile usare l'opzione LABEL con le query.

Vedere anche [Monitoraggio del carico di lavoro mediante DMV][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ottimizzare le prestazioni delle query con nuovi miglioramenti del prodotto
- [Ottimizzazione delle prestazioni con viste materializzate](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Ottimizzazione delle prestazioni con indice columnstore cluster ordinato](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>Raggruppare le istruzioni INSERT in batch
Una singola operazione di caricamento in una tabella di piccole dimensioni con un'istruzione INSERT o anche un ricaricamento periodico di una ricerca può essere appropriato per soddisfare le esigenze nel caso di un'istruzione come `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Se tuttavia è necessario caricare migliaia o milioni di righe nel corso della giornata, singole operazioni INSERT potrebbero non essere adeguate.  Sviluppare invece i processi in modo che scrivano in un file, mentre un altro processo subentra periodicamente e carica il file.

Vedere anche [INSERT (Transact-SQL)][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilizzare PolyBase per caricare ed esportare rapidamente i dati

 SQL Analytics supporta il caricamento e l'esportazione di dati tramite diversi strumenti, tra cui Azure Data Factory, polibase e BCP.  Per piccole quantità di dati in cui le prestazioni non sono fondamentali, qualsiasi strumento può essere adeguato per soddisfare le esigenze.  Quando tuttavia si caricano o si esportano grandi volumi di dati oppure sono necessarie prestazioni elevate, PolyBase è la scelta migliore.  
 
 La funzione polibase è progettata per sfruttare l'architettura MPP (Massive Parallel Processing) e caricherà ed esporterà le grandezze di dati più velocemente rispetto a qualsiasi altro strumento.  Le operazioni di caricamento di PolyBase possono essere eseguite usando CTAS o INSERT INTO.  **L'uso di CTAS riduce al minimo la registrazione delle transazioni e rappresenta il modo più rapido per caricare i dati.** 
 
  Azure Data Factory supporta anche il caricamento PolyBase e può raggiungere prestazioni simili a quelle di CTAS.  PolyBase supporta una vasta gamma di formati di file, inclusi i file GZIP.  **Per ottimizzare la velocità effettiva quando si usano file di testo gzip, suddividere i file in 60 o più file per ottimizzare il parallelismo del carico.**  Per una velocità effettiva totale maggiore, prendere in considerazione il caricamento simultaneo dei dati.

Vedere anche [caricare i dati][Load data], [Guida per l'uso di polibase][Guide for using PolyBase], [modelli e strategie di caricamento del pool SQL][Azure SQL Data Warehouse loading patterns and strategies], [caricare dati con Azure Data Factory][Load Data with Azure Data Factory], [spostare dati con Azure Data Factory][Move data with Azure Data Factory], [creare un formato di file esterno][CREATE EXTERNAL FILE FORMAT], [creare tabella come Select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Caricare ed eseguire query su tabelle esterne
PolyBase, tecnologia nota anche per le tabelle esterne, può rappresentare il modo più rapido per caricare i dati, ma non il migliore per eseguire query. Le tabelle di base attualmente supportano solo file BLOB di Azure e archiviazione Azure Data Lake. Questi file non sono supportati da alcuna risorsa di calcolo.  

Di conseguenza, SQL Analytics non può eseguire l'offload di questo lavoro ed è quindi necessario leggere l'intero file caricando il file in tempdb per leggere i dati.  Pertanto, in presenza di numerose query relative a questi dati, è consigliabile caricare i dati una volta ed eseguire le query tramite la tabella locale.

Vedere anche [Guida per l'uso di PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni
Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.  In questo modo, gli utenti possono iniziare a creare le tabelle in modo semplice senza dover decidere come devono essere distribuite.  Le tabelle round robin possono funzionare bene per alcuni carichi di lavoro, ma spesso la selezione di una colonna di distribuzione offre prestazioni notevolmente migliori.  L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  

Se, ad esempio, sono presenti una tabella degli ordini, distribuita in base a id_ordine, e una tabella delle transazioni, anch'essa distribuita in base a id_ordine, quando si uniscono in join le due tabelle in base a id_ordine, la query diventa una query pass-through, il che significa che si eliminano le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.  

Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  Per ulteriori informazioni su come selezionare una colonna di distribuzione, vedere i collegamenti seguenti per ottenere un miglioramento delle prestazioni e la definizione di una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLE.

Vedere anche [Panoramica sulle tabelle][Table overview], [Distribuzione delle tabelle][Table distribution], il post di blog relativo alla [scelta della modalità di distribuzione delle tabelle][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni
Sebbene i dati di partizionamento possano essere efficaci per gestire i dati tramite il cambio della partizione o l'ottimizzazione delle analisi tramite l'eliminazione della partizione, la presenza di un numero eccessivo di partizioni può rallentare le query.  Spesso si tratta di una strategia di partizionamento ad alta granularità, che può funzionare correttamente in SQL Server potrebbe non funzionare correttamente in analisi SQL.  

Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe.  Tenere presente che dietro le quinte SQL Analytics esegue il partizionamento dei dati in un database di 60, pertanto se si crea una tabella con 100 partizioni, ciò comporta in realtà 6000 partizioni.  

Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  Prendere in considerazione una granularità inferiore rispetto a quella considerata appropriata in SQL Server.  Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [Partizionamento delle tabelle][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni
Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback.  Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE.  

Se, ad esempio, si dispone di un inserimento che prevede un'ora, se possibile, suddividere l'inserimento in quattro parti, ognuna delle quali verrà eseguita in 15 minuti.  Sfruttare i casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, in modo da ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  Ad esempio, invece di eseguire un'istruzione DELETE per eliminare tutte le righe in una tabella in cui data_ordine corrisponde a ottobre 2001, è possibile creare una partizione mensile dei dati e quindi scambiare la partizione con i dati con una partizione vuota di un'altra tabella (vedere gli esempi relativi ad ALTER TABLE).  

Per le tabelle non partizionate, è consigliabile usare un CTAS per scrivere i dati da tenere in una tabella anziché usare DELETE.  Se un CTAS richiede la stessa quantità di tempo, è un'operazione molto più sicura da eseguire perché ha una registrazione minima delle transazioni e può essere annullata rapidamente, se necessario.

Vedere anche [Informazioni sulle transazioni][Understanding transactions], [Ottimizzazione delle transazioni][Optimizing transactions], [Partizionamento delle tabelle][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)].

## <a name="reduce-query-result-sizes"></a>Ridurre le dimensioni dei risultati delle query  
Questo passaggio consente di evitare problemi sul lato client causati da risultati di query di grandi dimensioni.  È possibile modificare la query per ridurre il numero di righe restituite. Alcuni strumenti per la generazione di query consentono di aggiungere la sintassi "Top N" a ogni query.  È anche possibile CETAS il risultato della query in una tabella temporanea e quindi usare l'esportazione di base per l'elaborazione di livello inferiore.

## <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili
Quando si definisce la DDL, l'utilizzo del tipo di dati più piccolo che supporterà i dati migliorerà le prestazioni di esecuzione delle query.  Questo aspetto è particolarmente importante per le colonne CHAR e VARCHAR.  Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Panoramica sulle tabelle][Table overview], [Tipi di dati per le tabelle][Table data types], [CREATE TABLE][CREATE TABLE].

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilizzare tabelle heap temporanee per i dati temporanei
Quando si esegue temporaneamente il pianerottolo dei dati, si potrebbe notare che l'uso di una tabella heap renderà più veloce il processo complessivo.  Se si caricano i dati solo per inserirli temporaneamente prima di eseguire altre trasformazioni, il caricamento della tabella in una tabella heap sarà molto più rapido del caricamento dei dati in una tabella columnstore cluster.  

Inoltre, il caricamento dei dati in una tabella temporanea sarà molto più veloce del caricamento di una tabella in un archivio permanente.  Le tabelle temporanee iniziano con "#" e sono accessibili solo dalla sessione che lo ha creato, quindi possono funzionare solo in scenari limitati.   Le tabelle heap sono definite nella clausola WITH di CREATE TABLE.  Se si usa una tabella temporanea, ricordarsi di creare le statistiche anche su tale tabella temporanea.

Vedere anche [Tabelle temporanee][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster
Gli indici columnstore cluster rappresentano uno dei modi più efficienti per archiviare i dati in analisi SQL.  Per impostazione predefinita, le tabelle in SQL Analytics vengono create come cluster columnstore.  Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  

Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere la sezione [Possibili cause di una qualità scadente dell'indice columnstore][Causes of poor columnstore index quality] nell'articolo [Indicizzazione delle tabelle][Table indexes].  

Poiché i segmenti columnstore di alta qualità sono importanti, è consigliabile usare gli ID utente presenti nella classe di risorse media o large per il caricamento dei dati. L'uso di [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non eseguono il push dei dati in un segmento columnstore compresso fino a quando non sono presenti più di 1 milione righe per tabella e ogni tabella di analisi SQL viene partizionata in 60 tabelle, come regola empirica, le tabelle columnstore non trarranno vantaggio da una query a meno che non sia la tabella contiene più di 60 milioni righe.  Per le tabelle con meno di 60 milioni di righe, l'uso di un indice columnstore potrebbe non essere utile,  ma potrebbe anche non avere effetti negativi.  

Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella ha 100 partizioni, dovrà contenere almeno 6 miliardi di righe per trarre vantaggio da un archivio colonne cluster (60 distribuzioni * 100 partizioni * 1 milione di righe).  

Se la tabella non contiene 6 miliardi di righe come in questo esempio, ridurre il numero di partizioni o prendere in considerazione l'utilizzo di una tabella heap.  Potrebbe anche essere utile fare delle prove per verificare se è possibile ottenere prestazioni migliori con una tabella heap con indici secondari al posto di una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche gli argomenti sull'[indicizzazione di tabelle][Table indexes], la [guida agli indici columnstore][Columnstore indexes guide] e la [ricompilazione degli indici columnstore][Rebuilding columnstore indexes].

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilizzare una classe di risorse più grande per migliorare le prestazioni delle query
SQL Analytics usa i gruppi di risorse come metodo per allocare memoria alle query.  Tutti gli utenti vengono assegnati alla classe Small Resource, che garantisce 100 MB di memoria per ogni distribuzione.  Poiché ci sono sempre 60 distribuzioni e a ogni distribuzione viene assegnato un minimo di 100 MB, l'allocazione di memoria totale a livello di sistema è di 6.000 MB o poco meno di 6 GB.  

Alcune query, ad esempio i join di grandi dimensioni oppure le operazioni di caricamento in tabelle columnstore cluster, risultano avvantaggiate da allocazioni di una quantità maggiore di memoria.  Alcune query, come le operazioni di sola analisi, non traggono alcun vantaggio.  Sul lato inverso, l'uso di classi di risorse più grandi riduce la concorrenza, quindi è opportuno prendere in considerazione questo effetto prima di spostare tutti gli utenti in una classe di risorse di grandi dimensioni.

Vedere anche [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usare una classe di risorse più piccola per aumentare la concorrenza
Se si nota che le query utente sembrano avere un ritardo prolungato, è possibile che gli utenti vengano eseguiti in classi di risorse più grandi e che consumino molti slot di concorrenza, causando l'accodamento di altre query.  Per vedere se le query degli utenti vengono inserite in coda, eseguire `SELECT * FROM sys.dm_pdw_waits` per verificare se vengono restituite righe.

Vedere anche [classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md), [sys. dm _pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Altre risorse:
Vedere anche l'articolo sulla [risoluzione dei problemi][Troubleshooting] per informazioni sui problemi più comuni e le relative soluzioni.

Se non è stato trovato ciò che si sta cercando in questo articolo, provare a usare "Cerca documenti" sul lato sinistro della pagina per cercare tutti i documenti di Azure sinapsi.  Il [Forum sulle sinapsi di Azure][Azure SQL Data Warehouse MSDN Forum] è una posizione in cui è possibile porre domande ad altri utenti e al gruppo di prodotti sinapsi di Azure. 

Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  Se si preferisce porre domande in Stack Overflow, è anche presente un forum di [stack overflow sinapsi di Azure][Azure SQL Data Warehouse Stack Overflow Forum].

Infine, usare la pagina dei [commenti e suggerimenti sulle sinapsi di Azure][Azure SQL Data Warehouse Feedback] per eseguire richieste di funzionalità.  Le richieste aggiunte o i voti per altre richieste sono utili per definire le priorità per le funzionalità.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
