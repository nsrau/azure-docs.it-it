---
title: Procedure consigliate per lo sviluppo per Synapse SQLDevelopment best practices for Synapse SQL
description: Consigli e procedure consigliate da conoscere durante lo sviluppo per Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3356817fbaef955e05358f9c92619f3c04f747d7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429616"
---
# <a name="development-best-practices-for-synapse-sql"></a>Procedure consigliate per lo sviluppo per Synapse SQLDevelopment best practices for Synapse SQL
Questo articolo descrive le procedure consigliate e linee guida quando si sviluppa una soluzione di data warehouse. 

## <a name="development-best-practices-for-synapse-sql"></a>Procedure consigliate per lo sviluppo per Synapse SQLDevelopment best practices for Synapse SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire la potenza di calcolo](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Gestire le statistiche

Assicurarsi di aggiornare le statistiche ogni giorno o dopo ogni caricamento.  Sono sempre necessari compromessi tra le prestazioni e il costo di creazione e aggiornamento delle statistiche. Se si ritiene che la gestione di tutte le statistiche sia necessaria troppo lunga, essere più selettivi su quali colonne contengono statistiche o su quali colonne devono essere aggiornate frequentemente.  

Ad esempio, è possibile aggiornare le colonne data, in cui è possibile aggiungere nuovi valori su base giornaliera. **È possibile ottenere il massimo vantaggio dalle statistiche sulle colonne coinvolte nei join, sulle colonne utilizzate nella clausola WHERE e sulle colonne disponibili in GROUP BY.**

Vedere anche [Gestire le statistiche delle tabelle](develop-tables-statistics.md), CREATE [STATISTICS](develop-tables-statistics.md), [UPDATE STATISTICS][UPDATE STATISTICS].

### <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.  In questo modo gli utenti possono iniziare a creare tabelle senza dover decidere come distribuire le tabelle.  Le tabelle Round Robin possono funzionare in modo sufficiente per alcuni carichi di lavoro. Tuttavia, nella maggior parte dei casi, la selezione di una colonna di distribuzione funzionerà molto meglio.  

L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  Ad esempio, se si dispone di una tabella orders, distribuita da order_id, e di una tabella delle transazioni, distribuita anche da order_id, quando si unisce la tabella orders alla tabella delle transazioni in order_id, questa query diventa una query pass-through. Ciò significa che eliminiamo le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.

Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  Vedere i collegamenti seguenti per ulteriori dettagli su come la selezione di una colonna di distribuzione può migliorare le prestazioni e come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLES.

Vedere anche [Panoramica tabella][Table overview], [Distribuzione tabella][Table distribution], Selezione [della distribuzione][Selecting table distribution]tabella , CREATE [TABLE][CREATE TABLE], CREATE TABLE [AS SELECT][CREATE TABLE AS SELECT].

### <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni
Mentre il partizionamento dei dati può essere efficace per mantenere i dati tramite la commutazione delle partizioni o l'ottimizzazione delle scansioni con l'eliminazione delle partizioni, la presenza di un numero elevato di partizioni può rallentare le query.  Spesso una strategia di partizionamento granularità elevata che può funzionare bene in SQL ServerSQL Server potrebbe non funzionare correttamente nel pool SQL.  

Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe. Il pool SQL partiziona i dati in 60 database. Pertanto, se si crea una tabella con 100 partizioni, il risultato sarà 6000 partizioni.  Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  

Un'opzione da considerare è l'utilizzo di una granularità inferiore a quella che potrebbe aver funzionato per l'utente in SQL ServerSQL Server.One option to consider is using a granularity that is lower than what may have worked for you in SQL ServerSQL Server.  Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [Partizionamento delle tabelle][Table partitioning].

### <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback.  Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE.  Ad esempio, se si dispone di un'istruzione INSERT che si prevede di richiedere 1 ora, è possibile suddividere l'istruzione INSERT in quattro parti, riducendo così ogni esecuzione a 15 minuti.

Sfruttare i casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, in modo da ridurre il rischio di rollback.  Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  Ad esempio, invece di eseguire un'istruzione DELETE per eliminare tutte le righe in una tabella in cui data_ordine corrisponde a ottobre 2001, è possibile creare una partizione mensile dei dati e quindi scambiare la partizione con i dati con una partizione vuota di un'altra tabella (vedere gli esempi relativi ad ALTER TABLE).  

Per le tabelle non partizionate, prendere in considerazione l'uso di CTAS per scrivere i dati da mantenere in una tabella, invece di usare DELETE.  Se un'operazione CTAS richiede la stessa quantità di tempo, è molto più sicura da eseguire perché prevede una registrazione delle transazioni minima e può essere annullata rapidamente, se necessario.

Vedere anche [Informazioni sulle transazioni][Understanding transactions], [Ottimizzazione delle transazioni][Optimizing transactions], [Partizionamento tabella][Table partitioning], TABELLA TRUNCATE [TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], Creazione della tabella come [selezione (CTAS)][Create table as select (CTAS)].

### <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce il codice DDL, per migliorare le prestazioni di query usare il più piccolo tipo di dati in grado di supportare i dati.  Questo aspetto è particolarmente importante per le colonne CHAR e VARCHAR.  Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Panoramica sulle tabelle][Table overview], [Tipi di dati per le tabelle](develop-tables-data-types.md), [CREATE TABLE][CREATE TABLE].

### <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster sono uno dei modi più efficienti per archiviare i dati nel pool SQL.  Per impostazione predefinita, le tabelle nel pool SQL vengono create come ColumnStore cluster.  Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  

La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere la sezione [Possibili cause di una qualità scadente dell'indice columnstore][Causes of poor columnstore index quality] nell'articolo [Indicizzazione delle tabelle][Table indexes].  Poiché l'elevata qualità dei segmenti columnstore è importante, è consigliabile usare ID di utenti della classe di risorse media o grande per caricare i dati. L'uso di [unità di data warehouse](azure -synapse-resource-consumption-models.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non eseguiranno il push dei dati in un segmento columnstore compresso finché non sono presenti più di 1 milione di righe per tabella e ogni tabella del pool SQL viene partizionata in 60 tabelle, le tabelle columnstore non trarranno vantaggio da una query a meno che la tabella non conveda più di 60 milioni di righe.  Per le tabelle con meno di 60 milioni di righe, avere un indice columstore potrebbe non essere la soluzione ottimale.  

Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella ha 100 partizioni, dovrà disporre di almeno 6 miliardi di righe per usufruire di un archivio di colonne cluster (60 distribuzioni *100 partizioni* 1 milione di righe).  

Se la tabella non contiene 6 miliardi di righe, ridurre il numero di partizioni o prendere in considerazione l'utilizzo di una tabella heap.  Può anche essere utile sperimentare per vedere se è possibile ottenere prestazioni migliori utilizzando una tabella heap con indici secondari anziché una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche [Indici di tabella][Table indexes], Guida agli indici [Columnstore][Columnstore indexes guide], [Ricompilazione][Rebuilding columnstore indexes]di indici columnstore .

### <a name="next-steps"></a>Passaggi successivi

Se non è stato trovato ciò che si stava cercando in questo articolo, provare a usare "Cerca documenti" sul lato sinistro di questa pagina per cercare tutti i documenti del pool SQL di Azure.If you don't find what you were looking in this article, try using the "Search for docs" on the left of this page to search all of the Azure SQL pool documents.  Il forum del [pool SQL][Azure SQL pool MSDN Forum] di Azure è una posizione in cui porre domande ad altri utenti e al gruppo di prodotti del pool SQL.  

Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  Se si preferisce porre domande sull'overflow dello stack, è disponibile anche un forum di overflow dello stack del pool SQL di Azure.If you prefer to ask your questions on Stack Overflow, we also have an [Azure SQL pool Stack Overflow Forum][Azure SQL pool Stack Overflow Forum].

Usare la pagina Commenti e suggerimenti [del pool SQL][Azure SQL pool Feedback] di Azure per effettuare richieste di funzionalità.  Le richieste aggiunte o i voti per altre richieste sono utili per definire le priorità per le funzionalità.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Create table as select (CTAS)]: ../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table overview]:develop-tables-overview.md
[Table distribution]: ../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Causes of poor columnstore index quality]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Temporary tables]:develop-tables-temporary.md
[Guide for using PolyBase]:../sql-analytics/data-loading-best-practices.md
[Load data]: ../sql-data-warehouse/esign-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Monitor your workload using DMVs]: ../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Pause compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Resume compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Scale compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Understanding transactions]:develop-transactions.md
[Optimizing transactions]: ../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Troubleshooting]: ../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[LABEL]:develop-label.md

<!--MSDN references-->
[ALTER TABLE]: /sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE EXTERNAL FILE FORMAT]: /sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE STATISTICS]: /sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE AS SELECT]: /sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[DBCC PDW_SHOWEXECUTIONPLAN]: /sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[INSERT]: /sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[OPTION]: /sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[TRUNCATE TABLE]: /sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[UPDATE STATISTICS]: /sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_exec_sessions]: /sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_exec_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_request_steps]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_sql_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_dms_workers]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_waits]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[Columnstore indexes guide]: /sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL pool Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL pool MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL pool Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL pool loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/

## <a name="development-best-practices-for-sql-on-demand-preview"></a>Procedure consigliate di sviluppo per SQL su richiesta (anteprima)Development best practices for SQL on-demand (preview)

### <a name="general-considerations"></a>Considerazioni generali

SQL su richiesta consente di eseguire query sui file negli account di archiviazione di Azure.SQL on-demand allows you to query files in your Azure storage accounts. Non dispone di funzionalità di archiviazione o inserimento locale, vale a dire che tutti i file che la query è destinata a SQL su richiesta. Pertanto, tutto ciò che riguarda la lettura di file dall'archiviazione potrebbe avere un impatto sulle prestazioni delle query.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocalizzare l'account di archiviazione di Azure e SQL su richiestaColocate Azure Storage account and SQL on-demand

 
Per ridurre al minimo la latenza, spostare l'account di archiviazione di Azure e l'endpoint SQL su richiesta. Gli account di archiviazione e gli endpoint di cui è stato eseguito il provisioning durante la creazione dell'area di lavoro si trovano nella stessa area. 
 
Per ottenere prestazioni ottimali, se si accede ad altri account di archiviazione con SQL su richiesta, assicurarsi che si trovino nella stessa area. In caso contrario, si baserà una maggiore latenza per il trasferimento di rete dei dati dall'area remota all'area dell'endpoint.

### <a name="azure-storage-throttling"></a>Limitazione di Archiviazione di AzureAzure Storage throttling

Più applicazioni e servizi possono accedere all'account di archiviazione. Quando le operazioni di I/O al secondo o la velocità effettiva combinate generate da applicazioni, servizi e carico di lavoro SU richiesta SQL superano i limiti dell'account di archiviazione, si verifica la limitazione dello spazio di archiviazione. Quando si verifica la limitazione dello spazio di archiviazione, si verifica un notevole effetto negativo sulle prestazioni delle query. 
 
Una volta rilevata la limitazione delle richieste, SQL on-demand include la gestione incorporata di questo scenario. SQL su richiesta effettuerà richieste di archiviazione a un ritmo più lento fino a quando non viene risolta la limitazione delle richieste. Tuttavia, per un'esecuzione ottimale delle query, si consiglia di non eseguire stress dell'account di archiviazione con altri carichi di lavoro durante l'esecuzione della query.

### <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di queryPrepare files for querying

Se possibile, è possibile preparare i file per migliorare le prestazioni:

- Converti CSV in parquet - Parquet è in formato colonnare. Poiché è compresso, ha file di dimensioni inferiori rispetto ai file CSV con gli stessi dati e SQL on-demand richiederà meno tempo e richieste di archiviazione per leggerlo.
- Se una query è destinata a un singolo file di grandi dimensioni, sarà possibile suddividerlo in più file più piccoli.
- Prova a mantenere le dimensioni del file CSV al di sotto di 10 GB.
- È preferibile avere file di dimensioni uguali per un singolo percorso OPENROWSET o una tabella esterna LOCATION.
- Partizionare i dati archiviando le partizioni in cartelle o nomi di file diversi: [selezionare le funzioni nome file e percorso file per utilizzare partizioni specifiche.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni fileinfo e filepath per assegnare partizioni specificheUse fileinfo and filepath functions to target specific partitions

I dati sono spesso organizzati in partizioni. È possibile indicare a SQL su richiesta di eseguire query su cartelle e file specifici. In questo modo si riduce il numero di file e la quantità di dati che la query deve leggere ed elaborare. Di conseguenza, si otterrà prestazioni migliori. Per ulteriori informazioni, controllare le funzioni [del nome del file](develop-storage-files-overview.md#filename-function) e del percorso del [file](develop-storage-files-overview.md#filepath-function) ed esempi su come eseguire query su [file specifici.](query-specific-files.md)

Se i dati nello spazio di archiviazione non sono partizionati, prendere in considerazione il partizionamento in modo da poter utilizzare queste funzioni per ottimizzare le query destinate a tali file.

Quando si [esegue una query sulle tabelle Spark partizionate](develop-storage-files-spark-tables.md) da SQL su richiesta, la query verrà automaticamente destinata solo ai file necessari.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare il CETAS per migliorare le prestazioni delle query e i joinUse CETAS to enhance query performance and joins

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili in SQL on-demand. CETAS è un'operazione parallela che crea metadati di tabella esterni ed esporta il risultato della query SELECT in un set di file nell'account di archiviazione.

È possibile usare CETAS per archiviare parte delle query usate spesso, ad esempio tabelle di riferimento unite in join, in un nuovo set di file. In un secondo momento, è possibile eseguire il join a questa singola tabella esterna anziché ripetere join comuni in più query. Quando CETAS genera file di parquet, le statistiche verranno create automaticamente quando la prima query è destinata a questa tabella esterna e si otterranno prestazioni migliori.
