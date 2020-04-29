---
title: Procedure consigliate per i pool SQL
description: Raccomandazioni e procedure consigliate da tenere presente durante l'utilizzo dei pool SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81427796"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Procedure consigliate per i pool SQL in Azure sinapsi Analytics

Questo articolo fornisce una raccolta di procedure consigliate che consentono di ottenere prestazioni ottimali per i pool SQL in Azure sinapsi Analytics. Di seguito sono riportate le linee guida di base e le aree importanti per concentrarsi sulla compilazione della soluzione. Ogni sezione presenta un concetto e quindi punta a articoli più dettagliati che trattano il concetto in modo più approfondito.

## <a name="sql-pools-loading"></a>Caricamento pool SQL

Per istruzioni sul caricamento di pool SQL, vedere [linee guida per il caricamento dei dati](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per ulteriori informazioni sulla riduzione dei costi tramite la sospensione e la scalabilità, vedere [Manage Compute](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Gestire le statistiche

Mentre SQL Server rileva e crea o aggiorna automaticamente le statistiche sulle colonne, i pool SQL richiedono la manutenzione manuale delle statistiche. È consigliabile mantenere le statistiche per assicurarsi che i piani del pool SQL siano ottimizzati.  La qualità dei piani creati dall'utilità di ottimizzazione dipende dalla qualità delle statistiche disponibili.

> [!TIP]
> La creazione di statistiche campionate su ogni colonna è un modo semplice per iniziare a usare le statistiche.  

 È altrettanto importante aggiornare le statistiche quando vengono apportate modifiche significative ai dati.  Un approccio conservativo consiste nell'aggiornare le statistiche ogni giorno o dopo ogni operazione di caricamento.  Sono sempre necessari compromessi tra le prestazioni e il costo di creazione e aggiornamento delle statistiche.

Per abbreviare i tempi di manutenzione delle statistiche, è necessario selezionare le colonne con statistiche o richiedere l'aggiornamento più frequente. Ad esempio, potrebbe essere necessario aggiornare le colonne date in cui è possibile aggiungere nuovi valori ogni giorno. Concentrarsi sulla presenza di statistiche per le colonne incluse nei join, sulle colonne utilizzate nella clausola WHERE e sulle colonne presenti in GROUP BY.

Altre informazioni sulle statistiche sono disponibili negli articoli gestire le statistiche delle [tabelle](develop-tables-statistics.md), [creare statistiche](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [aggiornare le statistiche](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="group-insert-statements-into-batches"></a>Raggruppare le istruzioni INSERT in batch

Un caricamento monouso in una tabella di piccole dimensioni con un'istruzione INSERT, `INSERT INTO MyLookup VALUES (1, 'Type 1')`ad esempio, può essere l'approccio migliore a seconda delle esigenze. Tuttavia, se è necessario caricare migliaia o milioni di righe nel corso della giornata, è probabile che gli inserimenti singleton non siano ottimali.

Un modo per risolvere questo problema consiste nello sviluppare un processo che scrive in un file e quindi un altro processo per caricare periodicamente questo file. Per ulteriori informazioni, fare riferimento all'articolo di [inserimento](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilizzare PolyBase per caricare ed esportare rapidamente i dati

Il pool SQL supporta il caricamento e l'esportazione di dati tramite diversi strumenti, tra cui Azure Data Factory, polibase e BCP.  Per piccole quantità di dati in cui le prestazioni non sono fondamentali, qualsiasi strumento può essere adeguato per soddisfare le esigenze.  

> [!NOTE]
> La polibase è la scelta migliore quando si caricano o si esportano grandi volumi di dati o se sono necessarie prestazioni più veloci.

Le operazioni di caricamento di PolyBase possono essere eseguite usando CTAS o INSERT INTO. CTAS ridurrà al minimo la registrazione delle transazioni ed è il modo più rapido per caricare i dati. Azure Data Factory supporta anche i caricamenti di polibase e può ottenere prestazioni simili a CTAS. La polibase supporta diversi formati di file, inclusi i file gzip.

Per ottimizzare la velocità effettiva quando si usano file di testo gzip, suddividere i file in 60 o più file per ottimizzare il parallelismo del carico.  Per una velocità effettiva totale maggiore, prendere in considerazione il caricamento simultaneo dei dati. Informazioni aggiuntive per gli argomenti relativi a questa sezione sono incluse negli articoli seguenti:

- [Carica dati](data-loading-overview.md)
- [Guida per l'uso di PolyBase](data-loading-best-practices.md)
- [Modelli e strategie di caricamento del pool SQL di Azure](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Caricare i dati con Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Spostare dati con Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Crea tabella come Select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Caricare ed eseguire query su tabelle esterne

La polibase non è ottimale per le query. Le tabelle di base per i pool SQL attualmente supportano solo file BLOB di Azure e archiviazione Azure Data Lake. Questi file non hanno risorse di calcolo che ne eseguono il backup. Di conseguenza, i pool SQL non possono eseguire l'offload di questo lavoro ed è necessario leggere l'intero file caricando il file in tempdb per poter leggere i dati.

Se sono presenti più query per eseguire query su questi dati, è preferibile caricare i dati una sola volta e fare in modo che le query usino la tabella locale. Altre informazioni aggiuntive sulla polibase sono incluse nella [Guida all'uso di polibase](data-loading-best-practices.md) .

## <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.   Questa impostazione predefinita rende più semplice per gli utenti iniziare a creare tabelle senza dover decidere come distribuire le tabelle. Le tabelle Round Robin possono essere sufficientemente efficienti per alcuni carichi di lavoro. Nella maggior parte dei casi, tuttavia, una colonna di distribuzione fornisce prestazioni migliori.  

L'esempio più comune di una tabella distribuita da una colonna che supera una tabella Round Robin è quando vengono unite due tabelle dei fatti di grandi dimensioni.  

Se, ad esempio, si dispone di una tabella Orders distribuita da order_id e di una tabella Transactions distribuita anche da order_id, quando si crea un join della tabella Orders alla tabella Transactions in order_id, questa query diventa una query pass-through. Le operazioni di spostamento dei dati vengono quindi eliminate. Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query. Anche un numero inferiore di operazioni di spostamento consente query più veloci.

> [!NOTE]
> Quando si carica una tabella distribuita, i dati in ingresso non devono essere ordinati in base alla chiave di distribuzione. In questo modo i carichi verranno rallentati.

I collegamenti agli articoli riportati di seguito forniscono informazioni aggiuntive sul miglioramento delle prestazioni tramite la selezione di una colonna di distribuzione. Sono inoltre disponibili informazioni su come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLE:

- [Panoramica delle tabelle](develop-tables-overview.md)
- [Distribuzione di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [celta della modalità di distribuzione delle tabelle](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni

Sebbene i dati di partizionamento possano essere efficaci per gestire i dati tramite il cambio della partizione o l'ottimizzazione delle analisi tramite l'eliminazione della partizione, la presenza di un numero eccessivo di partizioni può rallentare le query.  Spesso una strategia di partizionamento a granularità elevata che può funzionare correttamente in SQL Server potrebbe non funzionare correttamente nel pool SQL.  

La presenza di un numero eccessivo di partizioni può ridurre l'efficacia degli indici columnstore cluster se ogni partizione ha meno di 1 milione righe. I pool SQL partizionano automaticamente i dati in database 60. Quindi, se si crea una tabella con partizioni 100, il risultato sarà 6000 partizioni. Ogni carico di lavoro è diverso, quindi il Consiglio migliore è sperimentare il partizionamento per vedere cosa funziona meglio per il carico di lavoro.  

Una delle opzioni da considerare è l'uso di una granularità inferiore rispetto a quella implementata usando SQL Server. Si supponga, ad esempio, di utilizzare partizioni settimanali o mensili anziché partizioni giornaliere.

Altre informazioni sul partizionamento sono descritte in dettaglio nell'articolo relativo al [partizionamento delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione. Quando hanno esito negativo, è necessario eseguirne il rollback. Per ridurre il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  La riduzione delle dimensioni delle transazioni può essere eseguita suddividendo le istruzioni INSERT, UPDATE e DELETE in parti. Se, ad esempio, si dispone di un inserimento che si prevede di richiedere 1 ora, è possibile suddividere l'inserimento in quattro parti. Ogni esecuzione verrà quindi ridotta a 15 minuti.  

> [!TIP]
> Utilizzare casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, per ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  Ad esempio, anziché eseguire un'istruzione DELETE per eliminare tutte le righe di una tabella in cui il order_date è stato nel mese di ottobre 2001, è possibile partizionare i dati mensilmente. È quindi possibile disattivare la partizione con i dati per una partizione vuota da un'altra tabella. vedere gli esempi di ALTER TABLE.  

Per le tabelle non partizionate, è consigliabile usare un CTAS per scrivere i dati da tenere in una tabella anziché usare DELETE.  Se un CTAS richiede la stessa quantità di tempo, è molto più sicuro da eseguire perché ha una registrazione minima delle transazioni e può essere annullato rapidamente, se necessario.

Ulteriori informazioni sui contenuti correlati a questa sezione sono disponibili negli articoli seguenti:

- [Crea tabella come Select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Informazioni sulle transazioni](develop-transactions.md)
- [Ottimizzazione delle transazioni per SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Partizionamento di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Ridurre le dimensioni dei risultati delle query

La riduzione delle dimensioni dei risultati delle query consente di evitare problemi sul lato client causati da risultati di query di grandi dimensioni.  È possibile modificare la query per ridurre il numero di righe restituite. Alcuni strumenti per la generazione di query consentono di aggiungere la sintassi "Top N" a ogni query.  È anche possibile CETAS il risultato della query in una tabella temporanea e quindi usare l'esportazione di base per l'elaborazione di livello inferiore.

## <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce la DDL, usare il tipo di dati più piccolo per supportare i dati, in modo da migliorare le prestazioni di esecuzione delle query.  Questa indicazione è particolarmente importante per le colonne CHAR e VARCHAR.  Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, anziché usare NVARCHAR.

Per una revisione più dettagliata dei concetti essenziali relativi alle informazioni precedenti, vedere [Panoramica delle tabelle](develop-tables-overview.md), [tipi di dati delle tabelle](develop-tables-data-types.md)e articoli [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilizzare tabelle heap temporanee per i dati temporanei

Quando si inseriscono temporaneamente i dati nei pool SQL, le tabelle heap generalmente rendono più veloce il processo complessivo.  Se si caricano dati solo per eseguirne il staging prima di eseguire altre trasformazioni, il caricamento della tabella in una tabella heap sarà più rapido del caricamento dei dati in una tabella columnstore cluster.  

Il caricamento dei dati in una tabella temporanea viene caricato molto più velocemente rispetto al caricamento di una tabella in un archivio permanente.  Le tabelle temporanee iniziano con "#" e sono accessibili solo dalla sessione in cui è stata creata. Di conseguenza, possono funzionare solo in scenari limitati. Le tabelle heap sono definite nella clausola WITH di CREATE TABLE.  Se si usa una tabella temporanea, ricordarsi di creare le statistiche anche su tale tabella temporanea.

Per altre istruzioni, vedere le [tabelle temporanee](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [create table come articoli selezionati](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster rappresentano uno dei modi più efficienti per archiviare i dati nel pool SQL.  Per impostazione predefinita, le tabelle nel pool SQL vengono create come cluster columnstore.  Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  

La qualità dei segmenti può essere misurata in base al numero di righe in un gruppo di righe compresso. Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere l'articolo relativo alle [cause della qualità dell'indice columnstore scadente](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) nell'articolo sugli [indici di tabella](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .  

Poiché i segmenti columnstore di alta qualità sono importanti, è consigliabile usare gli ID utente presenti nella classe di risorse media o large per il caricamento dei dati. L'uso di [unità di data warehouse](resource-consumption-models.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Le tabelle columnstore in genere non eseguono il push dei dati in un segmento columnstore compresso fino a quando non sono presenti più di 1 milione righe per tabella. Ogni tabella del pool SQL è partizionata in 60 tabelle. Di conseguenza, le tabelle columnstore non trarranno vantaggio da una query, a meno che la tabella non contenga più di 60 milioni righe.  

> [!TIP]
> Per le tabelle con meno di 60 milioni righe, la presenza di un indice columnstore potrebbe non essere la soluzione ottimale.  

Se si partizionano i dati, ogni partizione dovrà avere 1 milione righe per trarre vantaggio da un indice columnstore cluster.  Per una tabella con partizioni 100, è necessario disporre di almeno 6 miliardi righe per trarre vantaggio da un archivio columnstore cluster (60 distribuzioni *100 partizioni* 1 milione righe).  

Se la tabella non contiene 6 miliardi righe, sono disponibili due opzioni principali. Ridurre il numero di partizioni o prendere in considerazione l'uso di una tabella heap.  Potrebbe anche essere utile provare a verificare se è possibile ottenere prestazioni migliori usando una tabella heap con indici secondari anziché una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  Altre informazioni sugli indici di tabella e columnstore sono reperibili all'interno degli [indici delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Guida agli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e ricompilazione degli articoli sugli [indici columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) .

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilizzare una classe di risorse più grande per migliorare le prestazioni delle query

I pool SQL usano i gruppi di risorse come metodo per allocare memoria alle query. Inizialmente, tutti gli utenti vengono assegnati alla classe di risorse piccola, che concede 100 MB di memoria per ogni distribuzione.  Sono sempre presenti 60 distribuzioni. A ogni distribuzione viene assegnato un minimo di 100 MB. L'allocazione di memoria totale a livello di sistema è di 6.000 MB o solo di 6 GB.  

Alcune query, ad esempio i join di grandi dimensioni oppure le operazioni di caricamento in tabelle columnstore cluster, risultano avvantaggiate da allocazioni di una quantità maggiore di memoria.  Alcune query, ad esempio le analisi pure, non vedranno alcun vantaggio. L'uso di classi di risorse più grandi influisca sulla concorrenza. Quindi, è opportuno tenere presenti questi fatti prima di trasferire tutti gli utenti a una classe di risorse di grandi dimensioni.

Per ulteriori informazioni sulle classi di risorse, vedere l'articolo relativo alle [classi di risorse per la gestione del carico di lavoro](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usare una classe di risorse più piccola per aumentare la concorrenza

Se si nota un ritardo prolungato nelle query utente, è possibile che gli utenti vengano eseguiti in classi di risorse più grandi. Questo scenario promuove il consumo di slot di concorrenza che possono causare l'accodamento di altre query.  Per determinare se le query degli utenti vengono accodate `SELECT * FROM sys.dm_pdw_waits` , eseguire per verificare se vengono restituite righe.

Le [classi di risorse per la gestione del carico di lavoro](../sql-data-warehouse/resource-classes-for-workload-management.md) e gli articoli [sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) forniranno ulteriori informazioni.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilizzare DMV per monitorare e ottimizzare le query

I pool SQL hanno diversi DMV che possono essere usati per monitorare l'esecuzione delle query.  Nell'articolo di monitoraggio riportato di seguito vengono fornite istruzioni dettagliate su come visualizzare i dettagli di una query in esecuzione.  Per trovare rapidamente le query in queste DMV, può essere utile usare l'opzione LABEL con le query. Per ulteriori informazioni dettagliate, vedere gli articoli inclusi nell'elenco seguente:

- [Monitoraggio del carico di lavoro mediante DMV](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [ETICHETTA](develop-label.md)
- [OPZIONE](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Passaggi successivi

Vedere anche l'articolo sulla [risoluzione dei](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) problemi e delle soluzioni comuni.

Se sono necessarie informazioni non fornite in questo articolo, usare "Cerca documenti" sul lato sinistro della pagina per cercare tutti i documenti del pool SQL.  Il [Forum sul pool SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) è una posizione in cui è possibile porre domande ad altri utenti e al gruppo di prodotti del pool SQL.  

Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  Se si preferisce porre domande in Stack Overflow, è anche presente un [pool SQL di Azure stack overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Per le richieste di funzionalità, usare la pagina [commenti e suggerimenti sul pool SQL di Azure](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  L'aggiunta di richieste o il voto di altre richieste ci consente di concentrarsi sulle funzionalità più richieste.
