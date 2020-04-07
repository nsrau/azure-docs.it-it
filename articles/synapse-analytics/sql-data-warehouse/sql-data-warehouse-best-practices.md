---
title: Procedure consigliate per il pool SQL Synapse in Azure Synapse Analytics (in precedenza SQL DW)
description: Suggerimenti e procedure consigliate per lo sviluppo di soluzioni per il pool SQL in Azure Synapse Analytics (in precedenza SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4462bf0fc2057922340eb01cb8c786dbc63ce290
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745347"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Procedure consigliate per il pool SQL Synapse in Azure Synapse Analytics (in precedenza SQL DW)

Questo articolo è una raccolta di procedure consigliate che consentono di ottenere prestazioni ottimali dalla distribuzione del [pool SQL.](sql-data-warehouse-overview-what-is.md)  Lo scopo di questo articolo è quello di darvi alcune indicazioni di base ed evidenziare importanti aree di messa a fuoco.  

## <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire la potenza di calcolo](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Gestire le statistiche

Il pool SQL può essere configurato per rilevare e creare automaticamente statistiche sulle colonne.  I piani di query creati dall'ottimizzatore sono buoni solo quanto le statistiche disponibili.  

È consigliabile abilitare AUTO_CREATE_STATISTICS per i database e mantenere aggiornate le statistiche ogni giorno o dopo ogni caricamento per garantire che le statistiche sulle colonne utilizzate nelle query siano sempre aggiornate.

Se si ritiene che l'aggiornamento di tutte le statistiche sia troppo lungo, è consigliabile provare a essere più selettivi sulle colonne che richiedono aggiornamenti frequenti delle statistiche. Potrebbe ad esempio essere consigliabile aggiornare le colonne di data, in cui potrebbero venire aggiunti nuovi valori ogni giorno.

> [!TIP]
> Si otterrà il massimo vantaggio grazie all'aggiornamento delle statistiche sulle colonne coinvolte nei join, nelle colonne utilizzate nella clausola WHERE e nelle colonne trovate in GROUP BY.

Vedere anche [Gestire le statistiche delle tabelle](sql-data-warehouse-tables-statistics.md), CREATE [STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilizzare DMV per monitorare e ottimizzare le query

Il pool SQL include diverse DMV che possono essere utilizzate per monitorare l'esecuzione delle query.  L'articolo Monitora il carico di lavoro usando DMV descrive le istruzioni dettagliate su come esaminare i dettagli di una query in esecuzione.  

Per trovare rapidamente le query in queste DMV, può essere utile usare l'opzione LABEL con le query.

Vedere anche [Monitorare il carico di lavoro utilizzando DMVs](sql-data-warehouse-manage-monitor.md), [LABEL](sql-data-warehouse-develop-label.md), [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ottimizzare le prestazioni delle query con nuovi miglioramenti del prodotto

- [Ottimizzazione delle prestazioni con viste materializzate](performance-tuning-materialized-views.md)
- [Ottimizzazione delle prestazioni con indice columnstore cluster ordinato](performance-tuning-ordered-cci.md)
- [Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Raggruppare le istruzioni INSERT in batch

Un carico una tantera su una piccola tabella con un'istruzione INSERT o anche un ricaricamento `INSERT INTO MyLookup VALUES (1, 'Type 1')`periodico di una ricerca può funzionare bene per le proprie esigenze con un'istruzione come .  

Se tuttavia è necessario caricare migliaia o milioni di righe nel corso della giornata, singole operazioni INSERT potrebbero non essere adeguate.  Sviluppare invece i processi in modo che scrivano in un file, mentre un altro processo subentra periodicamente e carica il file.

Vedere anche [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilizzare PolyBase per caricare ed esportare rapidamente i dati

Il pool SQL supporta il caricamento e l'esportazione di dati tramite diversi strumenti, tra cui Azure Data Factory, PolyBase e BCP.  Per piccole quantità di dati in cui le prestazioni non sono fondamentali, qualsiasi strumento può essere adeguato per soddisfare le esigenze.  Quando tuttavia si caricano o si esportano grandi volumi di dati oppure sono necessarie prestazioni elevate, PolyBase è la scelta migliore.  

PolyBase è progettato per sfruttare l'architettura MPP (Massively Parallel Processing) e caricherà ed esporterà le magnitudini dei dati più velocemente di qualsiasi altro strumento.  Le operazioni di caricamento di PolyBase possono essere eseguite usando CTAS o INSERT INTO.  

> [!TIP]
> L'uso di CTAS riduce al minimo la registrazione delle transazioni e rappresenta il modo più rapido per caricare i dati.

Azure Data Factory supporta anche il caricamento PolyBase e può raggiungere prestazioni simili a quelle di CTAS.  PolyBase supporta una vasta gamma di formati di file, inclusi i file GZIP.  
  
> [!NOTE]
> Per ottimizzare la velocità effettiva quando si usano file di testo gzip, suddividere i file in 60 o più file per ottimizzare il parallelismo del carico.   Per una velocità effettiva totale maggiore, prendere in considerazione il caricamento simultaneo dei dati.

Vedere anche [Caricare dati,](design-elt-data-loading.md) [Guida per l'utilizzo](guidance-for-loading-data.md)di PolyBase , Modelli e strategie di [caricamento del pool SQL](https://blogs.msdn.microsoft.com/sqlcat/20../../), Caricamento dati con Azure [Data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), Spostamento dei dati con Azure [Data Factory](../../data-factory/transform-data-using-machine-learning.md), CREATE EXTERNAL FILE [FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e Create table as [select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Caricare ed eseguire query su tabelle esterne

PolyBase, tecnologia nota anche per le tabelle esterne, può rappresentare il modo più rapido per caricare i dati, ma non il migliore per eseguire query. Le tabelle Polybase attualmente supportano solo i file BLOB di Azure e l'archiviazione di Azure Data Lake.Polybase tables currently only support Azure blob files and Azure Data Lake storage. Questi file non sono supportati da alcuna risorsa di calcolo.  

Di conseguenza, il pool SQL non può eseguire l'offload di questo lavoro e pertanto deve leggere l'intero file caricandolo in tempdb per leggere i dati.  Pertanto, in presenza di numerose query relative a questi dati, è consigliabile caricare i dati una volta ed eseguire le query tramite la tabella locale.

Vedere anche [Guida per l'utilizzo](guidance-for-loading-data.md)di PolyBase .

## <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.  In questo modo, gli utenti possono iniziare a creare le tabelle in modo semplice senza dover decidere come devono essere distribuite.  Le tabelle round robin possono funzionare bene per alcuni carichi di lavoro, ma spesso la selezione di una colonna di distribuzione offre prestazioni notevolmente migliori.  

L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  

Se, ad esempio, sono presenti una tabella degli ordini, distribuita in base a id_ordine, e una tabella delle transazioni, anch'essa distribuita in base a id_ordine, quando si uniscono in join le due tabelle in base a id_ordine, la query diventa una query pass-through, il che significa che si eliminano le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.  

> [!TIP]
> Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  

Per ulteriori informazioni su come migliorare le prestazioni e su come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLE, vedere i collegamenti seguenti su come migliorare le prestazioni e definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLE.

Vedere anche [Panoramica tabella](sql-data-warehouse-tables-overview.md), [Distribuzione tabella](sql-data-warehouse-tables-distribute.md), Selezione [della distribuzione](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)tabella , CREATE [TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), CREATE TABLE [AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni

Mentre il partizionamento dei dati può essere efficace per mantenere i dati tramite la commutazione delle partizioni o l'ottimizzazione delle scansioni con l'eliminazione delle partizioni, la presenza di un numero elevato di partizioni può rallentare le query.  Spesso una strategia di partizionamento granularità elevata, che potrebbe funzionare bene in SQL ServerSQL Server, potrebbe non funzionare correttamente nel pool SQL.  

Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe.  Tenere presente che dietro le quinte, il pool SQL partiziona i dati automaticamente in 60 database, pertanto se si crea una tabella con 100 partizioni, si ottiene in realtà 6000 partizioni.  

Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  Prendere in considerazione una granularità inferiore rispetto a quella considerata appropriata in SQL Server.  Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [Partizionamento delle tabelle](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback.  Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE.  

Ad esempio, se si dispone di un'istruzione INSERT che si prevede di richiedere 1 ora, se possibile, suddividere l'istruzione INSERT in quattro parti, ognuna delle quali verrà eseguita in 15 minuti.  Sfruttare casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT per svuotare le tabelle, per ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  Ad esempio, anziché eseguire un'istruzione DELETE per eliminare tutte le righe di una tabella in cui il order_date era nell'ottobre del 2001, è possibile partizionare i dati mensilmente e quindi escludere la partizione con i dati per una partizione vuota da un'altra tabella (vedere Esempi [di ALTER TABLE).](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

Per le tabelle non partizionate, è consigliabile usare un CTAS per scrivere i dati che si desidera mantenere in una tabella anziché usare DELETE.  Se un CTAS richiede la stessa quantità di tempo, è un'operazione molto più sicura da eseguire in quanto ha una registrazione minima delle transazioni e può essere annullata rapidamente se necessario.

Vedere anche [Informazioni sulle transazioni](sql-data-warehouse-develop-transactions.md), [Ottimizzazione delle transazioni](sql-data-warehouse-develop-best-practices-transactions.md), [Partizionamento delle](sql-data-warehouse-tables-partition.md)tabelle , TABELLA [TRUNCATE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e Creazione della tabella come [selezione (CTAS).](sql-data-warehouse-develop-ctas.md)

## <a name="reduce-query-result-sizes"></a>Ridurre le dimensioni dei risultati delle query

Questo passaggio consente di evitare problemi sul lato client causati da risultati di query di grandi dimensioni.  È possibile modificare la query per ridurre il numero di righe restituite. Alcuni strumenti di generazione di query consentono di aggiungere la sintassi "top N" a ogni query.  È anche possibile CETAS il risultato della query in una tabella temporanea e quindi utilizzare l'esportazione PolyBase per l'elaborazione di livello inferiore.

## <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce il DDL, l'utilizzo del tipo di dati più piccolo che supporterà i dati migliorerà le prestazioni delle query.  Questo approccio è particolarmente importante per le colonne CHAR e VARCHAR.  

Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Panoramica tabella](sql-data-warehouse-tables-overview.md), [Tipi di dati tabella](sql-data-warehouse-tables-data-types.md), CREATE [TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilizzare tabelle heap temporanee per i dati temporanei

Quando si atterrano temporaneamente i dati, è possibile che l'utilizzo di una tabella heap velocisse il processo complessivo.  Se si caricano dati solo per la gestione temporanea, prima di eseguire ulteriori trasformazioni, il caricamento della tabella nella tabella heap sarà molto più rapido del caricamento dei dati in una tabella columnstore cluster.  

Inoltre, il caricamento dei dati in una tabella temporanea sarà molto più veloce del caricamento di una tabella in un archivio permanente.  Le tabelle temporanee iniziano con un "" e sono accessibili solo dalla sessione che l'ha creata, pertanto possono funzionare solo in scenari limitati.

Le tabelle heap sono definite nella clausola WITH di CREATE TABLE.  Se si usa una tabella temporanea, ricordarsi di creare le statistiche anche su tale tabella temporanea.

Vedere anche [Tabelle temporanee](sql-data-warehouse-tables-temporary.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), CREATE TABLE [AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster sono uno dei modi più efficienti per archiviare i dati nel pool SQL.  Per impostazione predefinita, le tabelle nel pool SQL vengono create come ColumnStore cluster.  Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  

Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  Vedere le [cause della scarsa qualità dell'indice columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) nell'articolo Indici di tabella per istruzioni [dettagliate](sql-data-warehouse-tables-index.md) sul rilevamento e il miglioramento della qualità dei segmenti per le tabelle columnstore raggruppate.  

Poiché i segmenti columnstore di alta qualità sono importanti, è consigliabile usare gli ID utente che si trovano nella classe di risorse di medie o grandi dimensioni per il caricamento dei dati. L'uso di [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non eseguiranno il push dei dati in un segmento columnstore compresso fino a quando non sono presenti più di 1 milione di righe per tabella e ogni tabella del pool SQL viene partizionata in 60 tabelle, come regola generale, le tabelle columnstore non trarranno vantaggio da una query a meno che la tabella non conveda più di 60 milioni di righe.  Per le tabelle con meno di 60 milioni di righe, l'uso di un indice columnstore potrebbe non essere utile,  ma potrebbe anche non avere effetti negativi.  

Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella ha 100 partizioni, dovrà disporre di almeno 6 miliardi di righe per usufruire di un archivio di colonne cluster (60 distribuzioni *100 partizioni* 1 milione di righe).  

Se la tabella non contiene 6 miliardi di righe come in questo esempio, ridurre il numero di partizioni o prendere in considerazione l'utilizzo di una tabella heap.  Potrebbe anche essere utile fare delle prove per verificare se è possibile ottenere prestazioni migliori con una tabella heap con indici secondari al posto di una tabella columnstore.

> [!TIP]
> Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche gli argomenti sull'[indicizzazione di tabelle](sql-data-warehouse-tables-index.md), la [guida agli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e la [ricompilazione degli indici columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilizzare una classe di risorse più grande per migliorare le prestazioni delle query

Il pool SQL usa i gruppi di risorse per allocare memoria alle query.  Tutti gli utenti vengono assegnati alla classe di risorse di piccole dimensioni, che concede 100 MB di memoria per distribuzione.  Poiché ci sono sempre 60 distribuzioni e a ogni distribuzione viene assegnato un minimo di 100 MB, l'allocazione di memoria totale a livello di sistema è di 6.000 MB o poco meno di 6 GB.  

Alcune query, ad esempio i join di grandi dimensioni oppure le operazioni di caricamento in tabelle columnstore cluster, risultano avvantaggiate da allocazioni di una quantità maggiore di memoria.  Alcune query, come le scansioni pure, non produrranno alcun vantaggio.  Tuttavia, l'utilizzo di classi di risorse più grandi riduce la concorrenza, pertanto è consigliabile prendere in considerazione questo impatto prima di spostare tutti gli utenti in una classe di risorse di grandi dimensioni.

Vedere anche [Classi di risorse per la gestione del carico di lavoro.](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usare una classe di risorse più piccola per aumentare la concorrenza

Se si nota che le query utente sembrano avere un lungo ritardo, è possibile che gli utenti siano in esecuzione in classi di risorse più grandi e stiano consumando molti slot di concorrenza causando l'accodamento di altre query.  Per vedere se le query degli utenti vengono inserite in coda, eseguire `SELECT * FROM sys.dm_pdw_waits` per verificare se vengono restituite righe.

Vedere anche Classi di risorse [per la gestione del carico](resource-classes-for-workload-management.md)di lavoro , [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Altre risorse

Vedere anche l'articolo sulla [risoluzione dei problemi](sql-data-warehouse-troubleshoot.md) per informazioni sui problemi più comuni e le relative soluzioni.

Se non è stato trovato ciò che si sta cercando in questo articolo, provare a usare "Cerca documenti" sul lato sinistro di questa pagina per cercare tutti i documenti di Azure Synapse.  Il forum di Azure Synapse consente di inviare domande ad altri utenti e al gruppo di prodotti Azure Synapse.The [Azure Synapse Forum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) is a place for you to post questions to other users and to the Azure Synapse Product Group. Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  

Se si preferisce porre le domande su Overflow dello stack, abbiamo anche un forum di [overflow dello stack di synapse](https://stackoverflow.com/questions/tagged/azure-sqldw)di Azure .

Usare la pagina Commenti e suggerimenti su [Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) per effettuare richieste di funzionalità.  Le richieste aggiunte o i voti per altre richieste sono utili per definire le priorità per le funzionalità.
