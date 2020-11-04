---
title: Procedure di sviluppo consigliate per SQL Synapse
description: Indicazioni e procedure consigliate da conoscere per lo sviluppo di soluzioni per SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a5e514602668c96d63562e45fb114cf9770a54a9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321488"
---
# <a name="development-best-practices-for-synapse-sql"></a>Procedure di sviluppo consigliate per SQL Synapse

Questo articolo descrive le procedure consigliate e linee guida quando si sviluppa una soluzione di data warehouse. 

## <a name="dedicated-sql-pool-development-best-practices"></a>Procedure consigliate per lo sviluppo di pool SQL dedicato

### <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire la potenza di calcolo](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Gestire le statistiche

Assicurarsi di aggiornare le statistiche ogni giorno o dopo ogni carico.  Sono sempre necessari compromessi tra le prestazioni e il costo di creazione e aggiornamento delle statistiche. Se si ritiene che la gestione di tutte le statistiche richieda troppo tempo, è più selettivo le colonne con le statistiche o le colonne che richiedono un aggiornamento frequente.  

Ad esempio, potrebbe essere necessario aggiornare le colonne date, in cui è possibile aggiungere nuovi valori ogni giorno. 

> [!NOTE]
> Il massimo vantaggio è offerto dalle statistiche su colonne usate nei join, colonne usate nella clausola WHERE e colonne presenti in GROUP BY.

Vedere anche [Gestire le statistiche delle tabelle](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin. Questa funzionalità consente agli utenti di iniziare a creare tabelle in modo semplice senza dover decidere come distribuire le tabelle.  Le tabelle round robin possono garantire prestazioni sufficienti per alcuni carichi di lavoro. Tuttavia, nella maggior parte dei casi, la selezione di una colonna di distribuzione sarà molto migliore.  

L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  

Se, ad esempio, si dispone di una tabella Orders distribuita da order_id e di una tabella Transactions, distribuita anche da order_id, quando si crea un join della tabella Orders alla tabella Transactions in order_id, questa query diventa una query pass-through. 

Ciò significa che si eliminano le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.

> [!TIP]
> Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  

Vedere i collegamenti seguenti per informazioni più dettagliate su come la selezione di una colonna di distribuzione possa migliorare le prestazioni e su come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLES.

Vedere anche [Panoramica sulle tabelle](develop-tables-overview.md), [Distribuzione delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Selezione della distribuzione delle tabelle](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni
Anche se il partizionamento dei dati può essere efficace per la gestione dei dati tramite il cambio di partizione o l'ottimizzazione delle analisi con l'eliminazione delle partizioni, un numero eccessivo di partizioni può rallentare le query.  Spesso una strategia di partizionamento a granularità elevata che può funzionare correttamente in SQL Server potrebbe non funzionare correttamente nel pool SQL dedicato.  

> [!NOTE]
> Spesso una strategia di partizionamento a granularità elevata che può funzionare correttamente in SQL Server potrebbe non funzionare correttamente nel pool SQL dedicato.  

Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe. Il pool SQL dedicato esegue il partizionamento dei dati in 60 database. 

Quindi, se si crea una tabella con 100 partizioni, il risultato sarà 6000 partizioni.  Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  

Una delle opzioni da considerare è l'uso di una granularità inferiore a quella considerata appropriata in SQL Server.  Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [Partizionamento delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback.  Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE.  

Se, ad esempio, si dispone di un'istruzione INSERT che si prevede richieda 1 ora, è possibile suddividerla in quattro parti, riducendo quindi ciascuna esecuzione a 15 minuti.

> [!TIP]
> Sfruttare i casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, in modo da ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  

Ad esempio, invece di eseguire un'istruzione DELETE per eliminare tutte le righe in una tabella in cui data_ordine corrisponde a ottobre 2001, è possibile creare una partizione mensile dei dati e quindi scambiare la partizione con i dati con una partizione vuota di un'altra tabella (vedere gli esempi relativi ad ALTER TABLE).  

Per le tabelle non partizionate, prendere in considerazione l'uso di CTAS per scrivere i dati da mantenere in una tabella, invece di usare DELETE.  Se un'operazione CTAS richiede la stessa quantità di tempo, è molto più sicura da eseguire perché prevede una registrazione delle transazioni minima e può essere annullata rapidamente, se necessario.

Vedere anche [Informazioni sulle transazioni](develop-transactions.md), [Ottimizzazione delle transazioni](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Partizionamento delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce il codice DDL, per migliorare le prestazioni di query usare il più piccolo tipo di dati in grado di supportare i dati. Questa azione è particolarmente importante per le colonne CHAR e VARCHAR.  

Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Panoramica sulle tabelle](develop-tables-overview.md), [Tipi di dati per le tabelle](develop-tables-data-types.md) e [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster rappresentano uno dei modi più efficienti per archiviare i dati in un pool SQL dedicato.  Per impostazione predefinita, le tabelle nel pool SQL dedicato vengono create come cluster columnstore.  

Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  

La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere l'articolo relativo alle cause degli [indici](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [columnstore e alla qualità degli indici columnstore scarso](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) .  

Poiché l'elevata qualità dei segmenti columnstore è importante, è consigliabile usare ID di utenti della classe di risorse media o grande per caricare i dati. L'uso di [unità di data warehouse](resource-consumption-models.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non eseguono il push dei dati in un segmento columnstore compresso finché non sono presenti più di 1 milione righe per tabella e ogni tabella del pool SQL dedicata è partizionata in 60 tabelle, le tabelle columnstore non trarranno vantaggio da una query, a meno che la tabella non contenga più di 60 milioni righe.  

> [!TIP]
> Per le tabelle con meno di 60 milioni di righe, la presenza di un indice columnstore potrebbe non essere la soluzione ottimale.  

Inoltre, se si partizionano i dati, è opportuno tenere presente che ogni partizione deve avere 1 milione righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella è divisa in 100 partizioni, per poter sfruttare i vantaggi di un archivio colonne cluster deve contenere almeno 6 miliardi di righe, ovvero 60 distribuzioni *100 partizioni* 1 milione di righe.  

Se la tabella non contiene 6 miliardi di righe, occorre ridurre il numero di partizioni o prendere in considerazione l'uso di una tabella heap.  Potrebbe anche essere utile fare delle prove per verificare se è possibile ottenere prestazioni migliori usando una tabella heap con indici secondari al posto di una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche [Indicizzazione delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Guida agli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [Ricompilazione degli indici columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="serverless-sql-pool-development-best-practices"></a>Procedure consigliate per lo sviluppo di pool SQL senza server

### <a name="general-considerations"></a>Considerazioni generali

Il pool SQL senza server consente di eseguire query sui file negli account di archiviazione di Azure. Non dispone di funzionalità di archiviazione locale o di inserimento, ovvero tutti i file di destinazione della query sono esterni al pool SQL senza server. Quindi, tutti gli elementi correlati alla lettura di file dall'archiviazione potrebbero avere un effetto sulle prestazioni di esecuzione delle query.

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>Trovare un account di archiviazione di Azure e un pool SQL senza server

Per ridurre al minimo la latenza, colocare l'account di archiviazione di Azure e l'endpoint del pool SQL senza server. Gli account di archiviazione e gli endpoint sottoposti a provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per ottenere prestazioni ottimali, se si accede ad altri account di archiviazione con un pool SQL senza server, assicurarsi che si trovino nella stessa area. In caso contrario, si verifica un aumento della latenza per il trasferimento in rete dei dati tra l'area remota e quella dell'endpoint.

### <a name="azure-storage-throttling"></a>Limitazione delle richieste di archiviazione di Azure

Più applicazioni e servizi possono accedere all'account di archiviazione. Quando le operazioni di i/o al secondo combinate generate da applicazioni, servizi e carichi di lavoro del pool SQL senza server superano i limiti dell'account di archiviazione, viene applicata la limitazione dell'archiviazione. Quando si verifica la limitazione delle richieste di archiviazione, esiste un effetto negativo significativo sulle prestazioni delle query.

Una volta rilevata la limitazione, il pool SQL senza server dispone di una gestione incorporata di questo scenario. Il pool SQL senza server effettuerà richieste di archiviazione a un ritmo più lento fino a quando non viene risolta la limitazione delle richieste. 

Tuttavia, per un'esecuzione ottimale delle query, è consigliabile non sottoporre a stress l'account di archiviazione con altri carichi di lavoro durante l'esecuzione della query.

### <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di query

Se possibile, preparare i file per migliorare le prestazioni:

- Convertire i file CSV in Parquet, un formato a colonne. Poiché è compresso, ha dimensioni di file inferiori rispetto ai file CSV con gli stessi dati e il pool SQL senza server avrà bisogno di meno tempo e richieste di archiviazione per leggerlo.
- Se una query è destinata a un singolo file di grandi dimensioni, è possibile dividerla in più file di dimensioni ridotte.
- Provare a mantenere le dimensioni dei file CSV al di sotto di 10 GB.
- È preferibile avere file di dimensioni uguali per un singolo percorso OPENROWSET o per la proprietà LOCATION di una tabella esterna.
- Partizionare i dati archiviando le partizioni in cartelle o nomi file diversi. Vedere come [usare le funzioni filename e filepath per impostare partizioni specifiche come destinazione](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni fileinfo e filepath per indicare come destinazione partizioni specifiche

I dati sono spesso organizzati in partizioni. È possibile impostare il pool SQL senza server per eseguire una query su cartelle e file specifici. Questa operazione consente di ridurre il numero di file e la quantità di dati che la query deve leggere ed elaborare. 

Di conseguenza, si otterranno prestazioni migliori. Per altre informazioni, vedere le funzioni [filename](query-data-storage.md#filename-function) e [filepath](query-data-storage.md#filepath-function), oltre ad esempi su come [eseguire query su file specifici](query-specific-files.md).

Se i dati nell'archivio non sono partizionati, provare a partizionarli per poter usare queste funzioni e ottimizzare le query destinate a tali file.

Quando si [eseguono query Apache Spark partizionate per le tabelle esterne di Azure sinapsi](develop-storage-files-spark-tables.md) da un pool SQL senza server, la query verrà destinata automaticamente solo ai file necessari.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare CETAS per ottimizzare le prestazioni e i join delle query

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili nel pool SQL senza server. CETAS è un'operazione parallela che crea metadati di tabelle esterne ed esporta i risultati delle query SELECT in un set di file nell'account di archiviazione.

È possibile usare CETAS per archiviare parti di query usate spesso, come le tabelle di riferimento unite in join, in un nuovo set di file. In seguito, è possibile creare un join con questa singola tabella esterna invece di ripetere join comuni in più query. 

Quando CETAS genera file parquet, le statistiche vengono create automaticamente quando la prima query è destinata a questa tabella esterna e si ottengono prestazioni migliori.

### <a name="next-steps"></a>Passaggi successivi

Se sono necessarie informazioni non fornite in questo articolo, usare la funzione **Cerca documento** sul lato sinistro di questa pagina per cercare tutti i documenti del pool SQL.  La pagina Domande e risposte di [Microsoft&per Azure sinapsi Analytics](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) è una posizione in cui è possibile porre domande ad altri utenti e al gruppo di prodotti analisi sinapsi di Azure. Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  

Se si preferisce porre domande in Stack Overflow, è anche presente un forum di [analisi delle sinapsi di Azure stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).
 
