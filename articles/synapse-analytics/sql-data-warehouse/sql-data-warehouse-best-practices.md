---
title: Procedure consigliate per il pool di Synapse SQL in Azure Synapse Analytics (in precedenza SQL Data Warehouse)
description: Raccomandazioni e procedure consigliate per lo sviluppo di soluzioni per il pool SQL in Azure Synapse Analytics (in precedenza SQL Data Warehouse).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: ce1121a4a006e4208c76193a38262d4309e67584
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834442"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Procedure consigliate per il pool di Synapse SQL in Azure Synapse Analytics (in precedenza SQL Data Warehouse)

Questo articolo contiene una raccolta di procedure consigliate che consentono di ottenere prestazioni ottimali dalla distribuzione dei [pool SQL](sql-data-warehouse-overview-what-is.md).  Lo scopo di questo articolo è fornire materiali sussidiari di base ed evidenziare importanti aree di interesse.  

## <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire la potenza di calcolo](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Gestire le statistiche

Il pool SQL può essere configurato per rilevare e creare automaticamente statistiche sulle colonne.  La qualità dei piani di query creati dall'utilità di ottimizzazione dipende dalla qualità delle statistiche disponibili.  

È consigliabile abilitare AUTO_CREATE_STATISTICS per i database e mantenere aggiornate le statistiche ogni giorno o dopo ogni carico per garantire che le statistiche sulle colonne usate nelle query siano sempre aggiornate.

Se si ritiene che l'aggiornamento di tutte le statistiche richieda troppo tempo, è consigliabile provare a scegliere in modo più selettivo le colonne che richiedono aggiornamenti delle statistiche frequenti. Potrebbe ad esempio essere consigliabile aggiornare le colonne di data, in cui potrebbero venire aggiunti nuovi valori ogni giorno.

> [!TIP]
> Il massimo vantaggio è offerto dalle statistiche aggiornate su colonne usate nei join, colonne usate nella clausola WHERE e colonne presenti in GROUP BY.

Vedere anche [Gestire le statistiche delle tabelle](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilizzare DMV per monitorare e ottimizzare le query

I pool SQL dispongono di diversi DMV che possono essere usati per monitorare l'esecuzione di query.  L'articolo "Monitoraggio del carico di lavoro mediante DMV" fornisce istruzioni dettagliate su come esaminare i dati di una query in esecuzione.  

Per trovare rapidamente le query in queste DMV, può essere utile usare l'opzione LABEL con le query.

Vedere anche [Monitoraggio del carico di lavoro mediante DMV](sql-data-warehouse-manage-monitor.md), [LABEL](sql-data-warehouse-develop-label.md), [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ottimizzare le prestazioni delle query con nuovi miglioramenti del prodotto

- [Ottimizzazione delle prestazioni con viste materializzate](performance-tuning-materialized-views.md)
- [Ottimizzazione delle prestazioni con indice columnstore cluster ordinato](performance-tuning-ordered-cci.md)
- [Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Raggruppare le istruzioni INSERT in batch

Una singola operazione di caricamento in una tabella di piccole dimensioni con un'istruzione INSERT o anche un ricaricamento periodico di una ricerca può essere appropriato per soddisfare le esigenze nel caso di un'istruzione come `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  

Se tuttavia è necessario caricare migliaia o milioni di righe nel corso della giornata, singole operazioni INSERT potrebbero non essere adeguate.  Sviluppare invece i processi in modo che scrivano in un file, mentre un altro processo subentra periodicamente e carica il file.

Vedere anche [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilizzare PolyBase per caricare ed esportare rapidamente i dati

Il pool SQL supporta il caricamento e l'esportazione dei dati attraverso diversi strumenti, tra cui Azure Data Factory, PolyBase e BCP.  Per piccole quantità di dati in cui le prestazioni non sono fondamentali, qualsiasi strumento può essere adeguato per soddisfare le esigenze.  Quando tuttavia si caricano o si esportano grandi volumi di dati oppure sono necessarie prestazioni elevate, PolyBase è la scelta migliore.  

PolyBase è progettato per sfruttare l'architettura MPP (Massively Parallel Processing, elaborazione parallela massiva) e consente di caricare ed esportare grandi volumi di dati più velocemente di qualsiasi altro strumento.  Le operazioni di caricamento di PolyBase possono essere eseguite usando CTAS o INSERT INTO.  

> [!TIP]
> L'uso di CTAS riduce al minimo la registrazione delle transazioni e rappresenta il modo più rapido per caricare i dati.

Azure Data Factory supporta anche il caricamento PolyBase e può raggiungere prestazioni simili a quelle di CTAS.  PolyBase supporta una vasta gamma di formati di file, inclusi i file GZIP.  
  
> [!NOTE]
> Per aumentare al massimo la velocità effettiva quando si usano file di testo con estensione gzip, suddividere i file in 60 o più file per ottimizzare il parallelismo del caricamento.  Per una velocità effettiva totale maggiore, prendere in considerazione il caricamento simultaneo dei dati.

Vedere anche [Caricare i dati](design-elt-data-loading.md), [Guida per l'uso di PolyBase](guidance-for-loading-data.md), [Strategie e criteri di caricamento di pool SQL](https://blogs.msdn.microsoft.com/sqlcat/20../../), [Caricare dati con Azure Data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), [Spostare dati con Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md), [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e [Create Table As Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Caricare ed eseguire query su tabelle esterne

PolyBase, tecnologia nota anche per le tabelle esterne, può rappresentare il modo più rapido per caricare i dati, ma non il migliore per eseguire query. Le tabelle Polybase attualmente supportano solo file BLOB di Azure e l'archivio Azure Data Lake. Questi file non sono supportati da alcuna risorsa di calcolo.  

Di conseguenza, il pool SQL non può ripartire il lavoro e pertanto deve leggere l'intero file, caricandolo su tempdb per leggere i dati.  Pertanto, in presenza di numerose query relative a questi dati, è consigliabile caricare i dati una volta ed eseguire le query tramite la tabella locale.

Vedere anche [Guida per l'uso di PolyBase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.  In questo modo, gli utenti possono iniziare a creare le tabelle in modo semplice senza dover decidere come devono essere distribuite.  Le tabelle round robin possono funzionare bene per alcuni carichi di lavoro, ma spesso la selezione di una colonna di distribuzione offre prestazioni notevolmente migliori.  

L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  

Se, ad esempio, sono presenti una tabella degli ordini, distribuita in base a id_ordine, e una tabella delle transazioni, anch'essa distribuita in base a id_ordine, quando si uniscono in join le due tabelle in base a id_ordine, la query diventa una query pass-through, il che significa che si eliminano le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.  

> [!TIP]
> Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  

Vedere i collegamenti seguenti per informazioni più dettagliate su come la selezione di una colonna di distribuzione possa migliorare le prestazioni e su come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLE.

Vedere anche [Panoramica sulle tabelle](sql-data-warehouse-tables-overview.md), [Distribuzione delle tabelle](sql-data-warehouse-tables-distribute.md), [Selezione della distribuzione delle tabelle](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni

Anche se il partizionamento dei dati può essere efficace per la gestione dei dati tramite il cambio di partizione o l'ottimizzazione delle analisi con l'eliminazione delle partizioni, un numero eccessivo di partizioni può rallentare le query.  Spesso una strategia di partizionamento che prevede una granularità molto elevata, che potrebbe funzionare bene in SQL Server, potrebbe non essere ideale in pool SQL.  

Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe.  Tenere presente che in background il pool SQL partiziona automaticamente i dati in 60 database, quindi se si crea una tabella con 100 partizioni, in realtà si formano 6000 partizioni.  

Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  Prendere in considerazione una granularità inferiore rispetto a quella considerata appropriata in SQL Server.  Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [Partizionamento delle tabelle](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback.  Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE.  

Se, ad esempio, è presente un'istruzione INSERT che si prevede richieda 1 ora, se possibile suddividerla in quattro parti, ognuna delle quali verrà eseguita in 15 minuti.  Sfruttare i casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, in modo da ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  Ad esempio, invece di eseguire un'istruzione DELETE per eliminare tutte le righe in una tabella in cui data_ordine corrisponde a ottobre 2001, è possibile creare una partizione mensile dei dati e quindi scambiare la partizione con i dati con una partizione vuota di un'altra tabella (vedere gli esempi relativi ad [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)).  

Per le tabelle non partizionate, prendere in considerazione l'uso di CTAS per scrivere i dati da mantenere in una tabella, invece di usare DELETE.  Se un'operazione CTAS richiede la stessa quantità di tempo, è molto più sicura da eseguire perché prevede una registrazione delle transazioni minima e può essere annullata rapidamente, se necessario.

Vedere anche [Informazioni sulle transazioni](sql-data-warehouse-develop-transactions.md), [Ottimizzazione delle transazioni](sql-data-warehouse-develop-best-practices-transactions.md), [Partizionamento delle tabelle](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e [Create Table As Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Ridurre le dimensioni dei risultati delle query

Questo passaggio contribuisce a evitare problemi lato client causati da numerosi risultati delle query.  È possibile modificare la query in modo da ridurre il numero di righe restituite. Alcuni strumenti per la generazione di query consentono di aggiungere la sintassi "top N" a ogni query.  È anche possibile usare la sintassi CETAS sui risultati delle query per creare una tabella temporanea e poi usare l'esportazione PolyBase per l'elaborazione di livello inferiore.

## <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce il codice DDL, per migliorare le prestazioni di query usare il più piccolo tipo di dati in grado di supportare i dati.  Questo approccio è particolarmente importante per le colonne CHAR e VARCHAR.  

Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Panoramica sulle tabelle](sql-data-warehouse-tables-overview.md), [Tipi di dati per le tabelle](sql-data-warehouse-tables-data-types.md) e [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilizzare tabelle heap temporanee per i dati temporanei

Quando si inseriscono temporaneamente i dati, una tabella heap potrebbe rendere più veloce il processo complessivo.  Se si caricano dati solo per la gestione temporanea, prima di eseguire ulteriori trasformazioni, il caricamento della tabella nella tabella heap sarà molto più rapido del caricamento dei dati in una tabella columnstore cluster.  

Inoltre, il caricamento dei dati in una tabella temporanea sarà molto più veloce del caricamento di una tabella in un archivio permanente.  Le tabelle temporanee iniziano con "#" e sono accessibili solo dalla sessione in cui sono state create, quindi potrebbero non funzionare in alcuni scenari.

Le tabelle heap sono definite nella clausola WITH di CREATE TABLE.  Se si usa una tabella temporanea, ricordarsi di creare le statistiche anche su tale tabella temporanea.

Vedere anche [Tabelle temporanee](sql-data-warehouse-tables-temporary.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore in cluster rappresentano uno dei modi più efficienti per archiviare i dati nel pool SQL.  Per impostazione predefinita, le tabelle nel pool SQL vengono create come tabelle columnstore in cluster.  Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  

Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere la sezione [Possibili cause di una qualità scadente dell'indice columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) nell'articolo [Indicizzazione delle tabelle](sql-data-warehouse-tables-index.md).  

Poiché l'elevata qualità dei segmenti columnstore è importante, è consigliabile usare ID utente della classe di risorse media o grande per caricare i dati. L'uso di [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non effettuano il push dei dati in un segmento columnstore compresso fino a quando non sono presenti più di 1 milione di righe per tabella e ogni tabella del pool SQL è suddivisa in 60 tabelle, come regola generale le tabelle columnstore non risultano vantaggiose per una query a meno che non contengano più di 60 milioni di righe.  Per le tabelle con meno di 60 milioni di righe, l'uso di un indice columnstore potrebbe non essere utile,  ma potrebbe anche non avere effetti negativi.  

Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella è divisa in 100 partizioni, per poter sfruttare i vantaggi di un archivio colonne cluster deve contenere almeno 6 miliardi di righe, ovvero 60 distribuzioni *100 partizioni* 1 milione di righe.  

Se la tabella non contiene 6 miliardi di righe come in questo esempio, ridurre il numero di partizioni o prendere in considerazione l'utilizzo di una tabella heap.  Potrebbe anche essere utile fare delle prove per verificare se è possibile ottenere prestazioni migliori con una tabella heap con indici secondari al posto di una tabella columnstore.

> [!TIP]
> Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche gli argomenti sull'[indicizzazione di tabelle](sql-data-warehouse-tables-index.md), la [guida agli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e la [ricompilazione degli indici columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilizzare una classe di risorse più grande per migliorare le prestazioni delle query

Il pool SQL usa i gruppi di risorse come modo per allocare memoria alle query.  Per impostazione predefinita, tutti gli utenti vengono assegnati alla classe di risorse piccola, che garantisce 100 MB di memoria per distribuzione.  Poiché ci sono sempre 60 distribuzioni e a ogni distribuzione viene assegnato un minimo di 100 MB, l'allocazione di memoria totale a livello di sistema è di 6.000 MB o poco meno di 6 GB.  

Alcune query, ad esempio i join di grandi dimensioni oppure le operazioni di caricamento in tabelle columnstore cluster, risultano avvantaggiate da allocazioni di una quantità maggiore di memoria.  Alcune query, come le operazioni di sola analisi, non offrono alcun vantaggio.  Tuttavia, l'uso di classi di risorse più grandi riduce la concorrenza, quindi sarà necessario tenere in considerazione questo impatto prima di spostare tutti gli utenti in una classe di risorse di grandi dimensioni.

Vedere anche [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usare una classe di risorse più piccola per aumentare la concorrenza

Se si nota che le query utente sembrano avere un lungo ritardo, è possibile che gli utenti siano in esecuzione in classi di risorse più grandi e stiano usando molti slot di concorrenza, con il conseguente inserimento in coda di altre query.  Per vedere se le query degli utenti vengono inserite in coda, eseguire `SELECT * FROM sys.dm_pdw_waits` per verificare se vengono restituite righe.

Vedere anche [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md), [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Altre risorse

Vedere anche l'articolo sulla [risoluzione dei problemi](sql-data-warehouse-troubleshoot.md) per informazioni sui problemi più comuni e le relative soluzioni.

Se questo articolo non contiene le informazioni necessarie, provare a usare la funzione "Search for docs" (Cerca documenti) sul lato sinistro della pagina per cercare in tutti i documenti relativi ad Azure Synapse.  La [pagina delle domande di Domande e risposte Microsoft per Azure Synapse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) è un posto in cui è possibile inviare domande ad altri utenti e al gruppo di prodotti Azure Synapse. Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  

Se si preferisce porre domande in Stack Overflow, è disponibile anche un [forum di Stack Overflow in Azure Synapse](https://stackoverflow.com/questions/tagged/azure-sqldw).

Per inviare richieste di funzionalità, usare la pagina [Feedback su Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse).  Le richieste aggiunte o i voti per altre richieste sono utili per definire le priorità per le funzionalità.
