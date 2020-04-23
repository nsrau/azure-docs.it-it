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
ms.openlocfilehash: ed2638cfe4ab7e849e428729ccd17ffdeb6314af
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086352"
---
# <a name="development-best-practices-for-synapse-sql"></a>Procedure consigliate per lo sviluppo per Synapse SQLDevelopment best practices for Synapse SQL
Questo articolo descrive le procedure consigliate e linee guida quando si sviluppa una soluzione di data warehouse. 

## <a name="sql-pool-development-best-practices"></a>Procedure consigliate per lo sviluppo di pool SQLSQL pool development best practices

### <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire la potenza di calcolo](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Gestire le statistiche

Assicurarsi di aggiornare le statistiche ogni giorno o dopo ogni caricamento.  Sono sempre necessari compromessi tra le prestazioni e il costo di creazione e aggiornamento delle statistiche. Se si ritiene che la gestione di tutte le statistiche sia necessaria troppo lunga, essere più selettivi su quali colonne contengono statistiche o su quali colonne devono essere aggiornate frequentemente.  

Ad esempio, è possibile aggiornare le colonne data, in cui è possibile aggiungere nuovi valori su base giornaliera. 

> [!NOTE]
> È possibile ottenere il massimo vantaggio dalle statistiche sulle colonne coinvolte nei join, sulle colonne utilizzate nella clausola WHERE e sulle colonne disponibili in GROUP BY.

Vedere anche [Gestire le statistiche delle tabelle](develop-tables-statistics.md), CREATE [STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.  In questo modo gli utenti possono iniziare a creare tabelle senza dover decidere come distribuire le tabelle.  Le tabelle Round Robin possono funzionare in modo sufficiente per alcuni carichi di lavoro. Tuttavia, nella maggior parte dei casi, la selezione di una colonna di distribuzione funzionerà molto meglio.  

L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  

Ad esempio, se si dispone di una tabella orders, distribuita da order_id, e di una tabella delle transazioni, distribuita anche da order_id, quando si unisce la tabella orders alla tabella delle transazioni in order_id, questa query diventa una query pass-through. 

Ciò significa che eliminiamo le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.

> [!TIP]
> Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  

Per ulteriori informazioni su come migliorare le prestazioni e su come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLES, vedere i collegamenti seguenti.

Vedere anche [Panoramica tabella](develop-tables-overview.md), [Distribuzione tabella](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), Selezione della distribuzione delle [tabelle](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e CREATE TABLE [AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni
Mentre il partizionamento dei dati può essere efficace per mantenere i dati tramite la commutazione delle partizioni o l'ottimizzazione delle scansioni con l'eliminazione delle partizioni, la presenza di un numero elevato di partizioni può rallentare le query.  Spesso una strategia di partizionamento granularità elevata che può funzionare bene in SQL ServerSQL Server potrebbe non funzionare correttamente nel pool SQL.  

> [!NOTE]
> Spesso una strategia di partizionamento granularità elevata che può funzionare bene in SQL ServerSQL Server potrebbe non funzionare correttamente nel pool SQL.  

Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe. Il pool SQL partiziona i dati in 60 database. 

Pertanto, se si crea una tabella con 100 partizioni, il risultato sarà 6000 partizioni.  Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  

Un'opzione da considerare è l'utilizzo di una granularità inferiore a quella che potrebbe aver funzionato per l'utente in SQL ServerSQL Server.One option to consider is using a granularity that is lower than what may have worked for you in SQL ServerSQL Server.  Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [Partizionamento delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback.  Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE.  

Ad esempio, se si dispone di un'istruzione INSERT che si prevede di richiedere 1 ora, è possibile suddividere l'istruzione INSERT in quattro parti, riducendo così ogni esecuzione a 15 minuti.

> [!TIP]
> Sfruttare i casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, in modo da ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  

Ad esempio, invece di eseguire un'istruzione DELETE per eliminare tutte le righe in una tabella in cui data_ordine corrisponde a ottobre 2001, è possibile creare una partizione mensile dei dati e quindi scambiare la partizione con i dati con una partizione vuota di un'altra tabella (vedere gli esempi relativi ad ALTER TABLE).  

Per le tabelle non partizionate, prendere in considerazione l'uso di CTAS per scrivere i dati da mantenere in una tabella, invece di usare DELETE.  Se un'operazione CTAS richiede la stessa quantità di tempo, è molto più sicura da eseguire perché prevede una registrazione delle transazioni minima e può essere annullata rapidamente, se necessario.

Vedere anche [Informazioni sulle transazioni](develop-transactions.md), [Ottimizzazione delle transazioni](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Partizionamento delle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)tabelle , TABELLA [TRUNCATE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e Creazione della tabella come [selezione (CTAS).](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce il codice DDL, per migliorare le prestazioni di query usare il più piccolo tipo di dati in grado di supportare i dati.  Questo aspetto è particolarmente importante per le colonne CHAR e VARCHAR.  

Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Panoramica tabella](develop-tables-overview.md), [Tipi di dati tabella](develop-tables-data-types.md)e CREATE [TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster sono uno dei modi più efficienti per archiviare i dati nel pool SQL.  Per impostazione predefinita, le tabelle nel pool SQL vengono create come ColumnStore cluster.  

Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  

La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  Vedere l'articolo [Cause della scarsa qualità dell'indice columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) e Indici di tabella per istruzioni [dettagliate](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) sul rilevamento e il miglioramento della qualità dei segmenti per le tabelle columnstore cluster.  

Poiché l'elevata qualità dei segmenti columnstore è importante, è consigliabile usare ID di utenti della classe di risorse media o grande per caricare i dati. L'uso di [unità di data warehouse](resource-consumption-models.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non eseguiranno il push dei dati in un segmento columnstore compresso finché non sono presenti più di 1 milione di righe per tabella e ogni tabella del pool SQL viene partizionata in 60 tabelle, le tabelle columnstore non trarranno vantaggio da una query a meno che la tabella non conveda più di 60 milioni di righe.  

> [!TIP]
> Per le tabelle con meno di 60 milioni di righe, avere un indice columstore potrebbe non essere la soluzione ottimale.  

Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella ha 100 partizioni, dovrà disporre di almeno 6 miliardi di righe per usufruire di un archivio di colonne cluster (60 distribuzioni *100 partizioni* 1 milione di righe).  

Se la tabella non contiene 6 miliardi di righe, ridurre il numero di partizioni o prendere in considerazione l'utilizzo di una tabella heap.  Può anche essere utile sperimentare per vedere se è possibile ottenere prestazioni migliori utilizzando una tabella heap con indici secondari anziché una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche [Indici di tabella](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), Guida agli indici [Columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Ricompilazione](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)di indici columnstore .

## <a name="sql-on-demand-development-best-practices"></a>Procedure consigliate per lo sviluppo su richiesta SQL

### <a name="general-considerations"></a>Considerazioni generali

SQL su richiesta consente di eseguire query sui file negli account di archiviazione di Azure.SQL on-demand allows you to query files in your Azure storage accounts. Non dispone di funzionalità di archiviazione o inserimento locale, vale a dire che tutti i file che la query è destinata a SQL su richiesta. Pertanto, tutto ciò che riguarda la lettura di file dall'archiviazione potrebbe avere un impatto sulle prestazioni delle query.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocalizzare l'account di archiviazione di Azure e SQL su richiestaColocate Azure Storage account and SQL on-demand

Per ridurre al minimo la latenza, spostare l'account di archiviazione di Azure e l'endpoint SQL su richiesta. Gli account di archiviazione e gli endpoint di cui è stato eseguito il provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per ottenere prestazioni ottimali, se si accede ad altri account di archiviazione con SQL su richiesta, assicurarsi che si trovino nella stessa area. In caso contrario, si baserà una maggiore latenza per il trasferimento di rete dei dati dall'area remota all'area dell'endpoint.

### <a name="azure-storage-throttling"></a>Limitazione di Archiviazione di AzureAzure Storage throttling

Più applicazioni e servizi possono accedere all'account di archiviazione. Quando le operazioni di I/O al secondo o la velocità effettiva combinate generate da applicazioni, servizi e carico di lavoro SU richiesta SQL superano i limiti dell'account di archiviazione, si verifica la limitazione dello spazio di archiviazione. Quando si verifica la limitazione dello spazio di archiviazione, si verifica un notevole effetto negativo sulle prestazioni delle query.

Una volta rilevata la limitazione delle richieste, SQL on-demand include la gestione incorporata di questo scenario. SQL su richiesta effettuerà richieste di archiviazione a un ritmo più lento fino a quando non viene risolta la limitazione delle richieste. 

Tuttavia, per un'esecuzione ottimale delle query, si consiglia di non eseguire stress dell'account di archiviazione con altri carichi di lavoro durante l'esecuzione della query.

### <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di queryPrepare files for querying

Se possibile, è possibile preparare i file per migliorare le prestazioni:

- Converti CSV in parquet - Parquet è in formato colonnare. Poiché è compresso, ha file di dimensioni inferiori rispetto ai file CSV con gli stessi dati e SQL on-demand richiederà meno tempo e richieste di archiviazione per leggerlo.
- Se una query è destinata a un singolo file di grandi dimensioni, sarà possibile suddividerlo in più file più piccoli.
- Prova a mantenere le dimensioni del file CSV al di sotto di 10 GB.
- È preferibile avere file di dimensioni uguali per un singolo percorso OPENROWSET o una tabella esterna LOCATION.
- Partizionare i dati archiviando le partizioni in cartelle o nomi di file diversi: [selezionare le funzioni nome file e percorso file per utilizzare partizioni specifiche.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni fileinfo e filepath per assegnare partizioni specificheUse fileinfo and filepath functions to target specific partitions

I dati sono spesso organizzati in partizioni. È possibile indicare a SQL su richiesta di eseguire query su cartelle e file specifici. In questo modo si riduce il numero di file e la quantità di dati che la query deve leggere ed elaborare. 

Di conseguenza, si otterrà prestazioni migliori. Per ulteriori informazioni, controllare le funzioni [del nome del file](develop-storage-files-overview.md#filename-function) e del percorso del [file](develop-storage-files-overview.md#filepath-function) ed esempi su come eseguire query su [file specifici.](query-specific-files.md)

Se i dati nello spazio di archiviazione non sono partizionati, prendere in considerazione il partizionamento in modo da poter utilizzare queste funzioni per ottimizzare le query destinate a tali file.

Quando si [esegue una query sulle tabelle Spark partizionate](develop-storage-files-spark-tables.md) da SQL su richiesta, la query verrà automaticamente destinata solo ai file necessari.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare il CETAS per migliorare le prestazioni delle query e i joinUse CETAS to enhance query performance and joins

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili in SQL on-demand. CETAS è un'operazione parallela che crea metadati di tabella esterni ed esporta il risultato della query SELECT in un set di file nell'account di archiviazione.

È possibile usare CETAS per archiviare parte delle query usate spesso, ad esempio tabelle di riferimento unite in join, in un nuovo set di file. In un secondo momento, è possibile eseguire il join a questa singola tabella esterna anziché ripetere join comuni in più query. 

Quando CETAS genera file di parquet, le statistiche verranno create automaticamente quando la prima query è destinata a questa tabella esterna e si otterranno prestazioni migliori.

### <a name="next-steps"></a>Passaggi successivi

Se sono necessarie informazioni non fornite in questo articolo, utilizzare "Cerca documenti" sul lato sinistro di questa pagina per cercare tutti i documenti del pool SQL.  Il forum del [pool SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) è un luogo in cui porre domande ad altri utenti e al gruppo di prodotti del pool SQL.  

Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  Se si preferisce porre domande sull'overflow dello stack, è disponibile anche un forum di overflow dello stack del pool SQL di Azure.If you prefer to ask your questions on Stack Overflow, we also have an [Azure SQL pool Stack Overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).
 