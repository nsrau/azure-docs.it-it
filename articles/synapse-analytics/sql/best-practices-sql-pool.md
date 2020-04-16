---
title: Procedure consigliate per i pool SQLBest practices for SQL pools
description: Consigli e procedure consigliate da conoscere quando si lavora con i pool SQL.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427796"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Procedure consigliate per i pool SQL in Azure Synapse AnalyticsBest practices for SQL pools in Azure Synapse Analytics

Questo articolo offre una raccolta di procedure consigliate che consentono di ottenere prestazioni ottimali per i pool SQL in Analisi synapse di Azure.This article provides a collection of best practices to help you achieve optimal performance for SQL pools in Azure Synapse Analytics. Di seguito sono riportate le indicazioni di base e le aree importanti su cui concentrarsi durante la compilazione della soluzione. Ogni sezione ti introduce a un concetto e poi ti indirizza ad articoli più dettagliati che coprono il concetto in modo più approfondito.

## <a name="sql-pools-loading"></a>Caricamento dei pool SQL

Per istruzioni sul caricamento dei pool SQL, vedere [Linee guida per il caricamento dei dati.](data-loading-best-practices.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per ulteriori informazioni sulla riduzione dei costi tramite sospensione e ridimensionamento, vedere [Gestire il calcolo](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Gestire le statistiche

Mentre SQL Server rileva e crea o aggiorna automaticamente le statistiche sulle colonne, i pool SQL richiedono la manutenzione manuale delle statistiche. È consigliabile mantenere le statistiche per assicurarsi che i piani del pool SQL siano ottimizzati.  La qualità dei piani creati dall'utilità di ottimizzazione dipende dalla qualità delle statistiche disponibili.

> [!TIP]
> La creazione di statistiche campionate su ogni colonna è un modo semplice per iniziare a usare le statistiche.  

 È altrettanto importante aggiornare le statistiche quando vengono apportate modifiche significative ai dati.  Un approccio conservativo consiste nell'aggiornare le statistiche ogni giorno o dopo ogni operazione di caricamento.  Sono sempre necessari compromessi tra le prestazioni e il costo di creazione e aggiornamento delle statistiche.

Per ridurre i tempi di manutenzione delle statistiche, essere selettivi sulle colonne con statistiche o è necessario aggiornare più frequentemente. Ad esempio, è possibile aggiornare le colonne data in cui è possibile aggiungere nuovi valori ogni giorno. Concentrarsi sulla disponibilità di statistiche per le colonne coinvolte nei join, sulle colonne utilizzate nella clausola WHERE e sulle colonne disponibili in GROUP BY.

Ulteriori informazioni sulle statistiche sono disponibili negli articoli [Gestisci statistiche tabella](develop-tables-statistics.md), CREATE [STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [UPDATE STATISTICS.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="group-insert-statements-into-batches"></a>Raggruppare le istruzioni INSERT in batch

Un carico una tantera in una tabella `INSERT INTO MyLookup VALUES (1, 'Type 1')`di piccole dimensioni con un'istruzione INSERT, ad esempio può essere l'approccio migliore a seconda delle esigenze. Tuttavia, se è necessario caricare migliaia o milioni di righe nel corso della giornata, è probabile che singleton INSERTS non sono ottimali.

Un modo per risolvere questo problema consiste nello sviluppare un processo che scrive in un file e quindi un altro processo per caricare periodicamente questo file. Per ulteriori informazioni, vedere l'articolo [INSERT.](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilizzare PolyBase per caricare ed esportare rapidamente i dati

Il pool SQL supporta il caricamento e l'esportazione di dati tramite diversi strumenti, tra cui Azure Data Factory, PolyBase e BCP.  Per piccole quantità di dati in cui le prestazioni non sono fondamentali, qualsiasi strumento può essere adeguato per soddisfare le esigenze.  

> [!NOTE]
> Polybase è la scelta migliore quando si caricano o si esportano grandi volumi di dati o sono necessarie prestazioni più veloci.

Le operazioni di caricamento di PolyBase possono essere eseguite usando CTAS o INSERT INTO. CTAS ridurrà al minimo la registrazione delle transazioni ed è il modo più veloce per caricare i dati. Azure Data Factory supporta anche i carichi PolyBase e può ottenere prestazioni simili a CTAS. PolyBase supporta vari formati di file, inclusi i file Gzip.

Per ottimizzare la velocità effettiva quando si usano file di testo Gzip, suddividere i file in 60 o più file per ottimizzare il parallelismo del carico.  Per una velocità effettiva totale maggiore, prendere in considerazione il caricamento simultaneo dei dati. Ulteriori informazioni sugli argomenti relativi a questa sezione sono incluse negli articoli seguenti:

- [Caricare dati](data-loading-overview.md)
- [Guida per l'uso di PolyBase](data-loading-best-practices.md)
- [Modelli e strategie di caricamento del pool SQL di AzureAzure SQL pool loading patterns and strategies](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Caricare dati con Azure Data FactoryLoad Data with Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Spostare dati con Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREA FORMATO DI FILE ESTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Crea tabella come selezione (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Caricare ed eseguire query su tabelle esterne

Polybase non è ottimale per le query. Le tabelle Polybase per i pool SQL attualmente supportano solo i file BLOB di Azure e l'archiviazione di Azure Data Lake.Polybase tables for SQL pools currently only support Only blob files and Azure Data Lake storage. Questi file non dispongono di risorse di calcolo che li assornonano. Di conseguenza, i pool SQL non possono eseguire l'offload di questo lavoro e devono leggere l'intero file caricandolo in tempdb in modo che possa leggere i dati.

Se si dispone di più query per l'esecuzione di query su questi dati, è preferibile caricare questi dati una sola volta e fare in modo che le query utilizzino la tabella locale. Ulteriori linee guida Polybase sono incluse nella [Guida per l'utilizzo di PolyBase](data-loading-best-practices.md) articolo.

## <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.   Questa impostazione predefinita semplifica agli utenti l'inizio della creazione di tabelle senza dover decidere come distribuire le tabelle. Le tabelle Round Robin possono funzionare in modo sufficiente per alcuni carichi di lavoro. Tuttavia, nella maggior parte dei casi, una colonna di distribuzione offre prestazioni migliori.  

L'esempio più comune di una tabella distribuita da una colonna che supera una tabella Round Robin è quando vengono unite due tabelle dei fatti di grandi dimensioni.  

Ad esempio, se si dispone di una tabella orders distribuita per order_id e di una tabella delle transazioni distribuita anche per order_id, quando si unisce la tabella orders alla tabella delle transazioni in order_id, questa query diventa una query pass-through. Le operazioni di spostamento dei dati vengono quindi eliminate. Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query. Anche un numero inferiore di operazioni di spostamento consente query più veloci.

> [!NOTE]
> Quando si carica una tabella distribuita, i dati in ingresso non devono essere ordinati in base alla chiave di distribuzione. In questo modo si rallentano i carichi.

I collegamenti dell'articolo forniti di seguito forniscono ulteriori dettagli sul miglioramento delle prestazioni tramite la selezione di una colonna di distribuzione. Inoltre, sono disponibili informazioni su come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLE:

- [Panoramica della tabella](develop-tables-overview.md)
- [Distribuzione tabelle](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [celta della modalità di distribuzione delle tabelle](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni

Mentre il partizionamento dei dati può essere efficace per mantenere i dati tramite la commutazione delle partizioni o l'ottimizzazione delle scansioni con l'eliminazione delle partizioni, la presenza di un numero elevato di partizioni può rallentare le query.  Spesso una strategia di partizionamento granularità elevata che può funzionare bene in SQL ServerSQL Server potrebbe non funzionare correttamente nel pool SQL.  

La presenza di un numero di partizioni numerose può ridurre l'efficacia degli indici columnstore cluster se ogni partizione ha meno di 1 milione di righe. I pool SQL partizionano automaticamente i dati in 60 database. Pertanto, se si crea una tabella con 100 partizioni, il risultato sarà 6000 partizioni. Ogni carico di lavoro è diverso, quindi il consiglio migliore è quello di sperimentare con il partizionamento per vedere cosa funziona meglio per il carico di lavoro.  

Un'opzione da considerare è l'utilizzo di una granularità inferiore a quella implementata tramite SQL Server.One option to consider is using a granularity that is lower than what you've implemented using SQL Server. Ad esempio, è consigliabile usare partizioni settimanali o mensili anziché partizioni giornaliere.

Ulteriori informazioni sul partizionamento sono descritte in dettaglio nell'articolo [Partizionamento delle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) tabelle.

## <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione. Quando falliscono, devono essere annullati. Per ridurre il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni quando possibile.  È possibile ridurre al minimo le dimensioni delle transazioni dividendo le istruzioni INSERT, UPDATE e DELETE in parti. Ad esempio, se si dispone di un'istruzione INSERT che si prevede di richiedere 1 ora, è possibile suddividere l'istruzione INSERT in quattro parti. Ogni corsa sarà quindi ridotta a 15 minuti.  

> [!TIP]
> Sfruttare casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT per svuotare le tabelle per ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  Ad esempio, anziché eseguire un'istruzione DELETE per eliminare tutte le righe di una tabella in cui la order_date si trovava nell'ottobre del 2001, è possibile partizionare i dati mensilmente. È quindi possibile scambiare la partizione con i dati per una partizione vuota da un'altra tabella (vedere esempi DI ALTER TABLE).  

Per le tabelle non partizionate, è consigliabile usare un CTAS per scrivere i dati che si desidera mantenere in una tabella anziché usare DELETE.  Se un CTAS richiede la stessa quantità di tempo, è molto più sicuro da eseguire poiché ha una registrazione minima delle transazioni e può essere annullato rapidamente se necessario.

Ulteriori informazioni sul contenuto relativo a questa sezione sono incluse negli articoli seguenti:

- [Crea tabella come selezione (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Informazioni sulle transazioni](develop-transactions.md)
- [Ottimizzazione delle transazioni per SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Partizionamento di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABELLA](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER A TABELLA](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Ridurre le dimensioni dei risultati delle query

La riduzione delle dimensioni dei risultati delle query consente di evitare problemi sul lato client causati da risultati di query di grandi dimensioni.  È possibile modificare la query per ridurre il numero di righe restituite. Alcuni strumenti di generazione di query consentono di aggiungere la sintassi "top N" a ogni query.  È anche possibile CETAS il risultato della query in una tabella temporanea e quindi utilizzare l'esportazione PolyBase per l'elaborazione di livello inferiore.

## <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce il DDL, utilizzare il tipo di dati più piccolo che supporterà i dati in quanto in questo modo si miglioreranno le prestazioni delle query.  Questa raccomandazione è particolarmente importante per le colonne CHAR e VARCHAR.  Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Inoltre, definire le colonne come VARCHAR quando questo è tutto ciò che è necessario anziché utilizzare NVARCHAR.

Per una revisione più dettagliata dei concetti essenziali relativi alle informazioni precedenti, vedere gli articoli [Table overview](develop-tables-overview.md), Table [data types](develop-tables-data-types.md)e CREATE [TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilizzare tabelle heap temporanee per i dati temporanei

Quando si atterrano temporaneamente i dati nei pool SQL, le tabelle heap rendono in genere il processo complessivo più veloce.  Se si caricano i dati solo per eseguirne lo stage prima di eseguire più trasformazioni, il caricamento della tabella in una tabella heap sarà più rapido rispetto al caricamento dei dati in una tabella columnstore cluster.  

Il caricamento dei dati in una tabella temporanea verrà inoltre caricato molto più velocemente rispetto al caricamento di una tabella in un archivio permanente.  Le tabelle temporanee iniziano con un "" e sono accessibili solo dalla sessione che l'ha creata. Di conseguenza, possono funzionare solo in scenari limitati. Le tabelle heap sono definite nella clausola WITH di CREATE TABLE.  Se si usa una tabella temporanea, ricordarsi di creare le statistiche anche su tale tabella temporanea.

Per ulteriori informazioni, vedere gli articoli [Tabelle temporanee](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e CREATE TABLE [AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster sono uno dei modi più efficienti per archiviare i dati nel pool SQL.  Per impostazione predefinita, le tabelle nel pool SQL vengono create come ColumnStore cluster.  Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  

La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso. Vedere le [cause della scarsa qualità dell'indice columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) nell'articolo Indici di tabella per istruzioni [dettagliate](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) sul rilevamento e il miglioramento della qualità dei segmenti per le tabelle columnstore raggruppate.  

Poiché i segmenti columnstore di alta qualità sono importanti, è consigliabile usare gli ID utente che si trovano nella classe di risorse di medie o grandi dimensioni per il caricamento dei dati. L'uso di [unità di data warehouse](resource-consumption-models.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Le tabelle Columnstore in genere non eseguiranno il push dei dati in un segmento columnstore compresso finché non sono presenti più di 1 milione di righe per tabella. Ogni tabella del pool SQL è partizionata in 60 tabelle. Di conseguenza, le tabelle columnstore non trarranno vantaggio da una query a meno che la tabella non continchi più di 60 milioni di righe.  

> [!TIP]
> Per le tabelle con meno di 60 milioni di righe, la presenza di un indice columnstore potrebbe non essere la soluzione ottimale.  

Se si partizionano i dati, ogni partizione dovrà disporre di 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Per una tabella con 100 partizioni, è necessario disporre di almeno 6 miliardi di righe per trarre vantaggio da un archivio di colonne cluster (60 distribuzioni *100 partizioni* 1 milione di righe).  

Se la tabella non dispone di 6 miliardi di righe, sono disponibili due opzioni principali. Ridurre il numero di partizioni o prendere in considerazione l'utilizzo di una tabella heap.  Può anche essere utile sperimentare per vedere se è possibile ottenere prestazioni migliori utilizzando una tabella heap con indici secondari anziché una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  Ulteriori informazioni sugli indici table e columnstore sono disponibili all'interno degli [articoli Table indexes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Columnstore indexes](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [Rebuilding columnstore indexes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) .

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilizzare una classe di risorse più grande per migliorare le prestazioni delle query

I pool SQL usano i gruppi di risorse per allocare memoria alle query. Inizialmente, tutti gli utenti vengono assegnati alla classe di risorse di piccole dimensioni, che concede 100 MB di memoria per distribuzione.  Ci sono sempre 60 distribuzioni. A ogni distribuzione viene assegnato un minimo di 100 MB. L'allocazione di memoria totale a livello di sistema è di 6.000 MB o poco meno di 6 GB.  

Alcune query, ad esempio i join di grandi dimensioni oppure le operazioni di caricamento in tabelle columnstore cluster, risultano avvantaggiate da allocazioni di una quantità maggiore di memoria.  Alcune query, ad esempio le scansioni pure, non vedranno alcun vantaggio. L'utilizzo di classi di risorse più grandi influisce sulla concorrenza. Quindi, ti consigliamo di tenere a mente questi fatti prima di spostare tutti gli utenti a una classe di risorse di grandi dimensioni.

Per altre informazioni sulle classi di risorse, vedere l'articolo Classi di risorse per la [gestione del carico di lavoro.](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usare una classe di risorse più piccola per aumentare la concorrenzaUse smaller resource class to increase concurrency

Se si nota un lungo ritardo nelle query utente, gli utenti potrebbero essere in esecuzione in classi di risorse più grandi. Questo scenario promuove l'utilizzo degli slot di concorrenza, che può causare l'accodamento di altre query.  Per determinare se le query `SELECT * FROM sys.dm_pdw_waits` degli utenti vengono accodate, eseguire per verificare se vengono restituite righe.

Gli [articoli Resource classes for workload management](../sql-data-warehouse/resource-classes-for-workload-management.md) e [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) forniranno ulteriori informazioni.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilizzare DMV per monitorare e ottimizzare le query

I pool SQL dispongono di diverse DMV che possono essere utilizzate per monitorare l'esecuzione delle query.  L'articolo di monitoraggio seguente illustra le istruzioni dettagliate su come visualizzare i dettagli di una query in esecuzione.  Per trovare rapidamente le query in queste DMV, può essere utile usare l'opzione LABEL con le query. Per ulteriori informazioni dettagliate, vedere gli articoli inclusi nell'elenco seguente:

- [Monitoraggio del carico di lavoro mediante DMV](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [Etichetta](develop-label.md)
- [Opzione](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Passaggi successivi

Vedere anche l'articolo [Risoluzione dei problemi](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per problemi e soluzioni comuni.

Se sono necessarie informazioni non fornite in questo articolo, utilizzare "Cerca documenti" sul lato sinistro di questa pagina per cercare tutti i documenti del pool SQL.  Il forum del [pool SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) è un luogo in cui porre domande ad altri utenti e al gruppo di prodotti del pool SQL.  

Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  Se si preferisce porre domande sull'overflow dello stack, è disponibile anche un forum di overflow dello stack del pool SQL di Azure.If you prefer to ask your questions on Stack Overflow, we also have an [Azure SQL pool Stack Overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Per le richieste di funzionalità, usare la pagina Commenti e suggerimenti del pool SQL di [Azure.For](https://feedback.azure.com/forums/307516-sql-data-warehouse) feature requests, use the Azure SQL pool Feedback page.  L'aggiunta di richieste o l'aggiornamento di altre richieste ci aiuta a concentrarci sulle funzionalità più richieste.
