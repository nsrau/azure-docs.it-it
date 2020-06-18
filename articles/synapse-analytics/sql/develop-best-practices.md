---
title: Procedure di sviluppo consigliate per SQL Synapse
description: Indicazioni e procedure consigliate da conoscere per lo sviluppo di soluzioni per SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 9a291971ce0edead9ca28a47f7ad0689b0f65547
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834952"
---
# <a name="development-best-practices-for-synapse-sql"></a>Procedure di sviluppo consigliate per SQL Synapse
Questo articolo descrive le procedure consigliate e linee guida quando si sviluppa una soluzione di data warehouse. 

## <a name="sql-pool-development-best-practices"></a>Procedure consigliate per lo sviluppo del pool SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire la potenza di calcolo](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Gestire le statistiche

Assicurarsi di aggiornare le statistiche ogni giorno o dopo ogni caricamento.  Sono sempre necessari compromessi tra le prestazioni e il costo di creazione e aggiornamento delle statistiche. Se si ritiene che la gestione di tutte le statistiche richieda troppo tempo, scegliere in modo più selettivo le colonne con le statistiche o quelle che richiedono aggiornamenti frequenti.  

Potrebbe ad esempio essere consigliabile aggiornare le colonne di data, in cui potrebbero venire aggiunti nuovi valori su base giornaliera. 

> [!NOTE]
> Il massimo vantaggio è offerto dalle statistiche su colonne usate nei join, colonne usate nella clausola WHERE e colonne presenti in GROUP BY.

Vedere anche [Gestire le statistiche delle tabelle](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.  In questo modo, gli utenti possono iniziare a creare le tabelle in modo semplice senza dover decidere come devono essere distribuite.  Le tabelle round robin possono garantire prestazioni sufficienti per alcuni carichi di lavoro. Tuttavia, nella maggior parte dei casi, la selezione di una colonna di distribuzione sarà molto migliore.  

L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  

Se, ad esempio, sono presenti una tabella degli ordini, distribuita in base a id_ordine, e una tabella delle transazioni, anch'essa distribuita in base a id_ordine, quando si uniscono in join le due tabelle in base a id_ordine, la query diventa una query pass-through, il che significa che si eliminano le operazioni di spostamento dei dati. 

Ciò significa che si eliminano le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.

> [!TIP]
> Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  

Vedere i collegamenti seguenti per informazioni più dettagliate su come la selezione di una colonna di distribuzione possa migliorare le prestazioni e su come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLES.

Vedere anche [Panoramica sulle tabelle](develop-tables-overview.md), [Distribuzione delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Selezione della distribuzione delle tabelle](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni
Anche se il partizionamento dei dati può essere efficace per la gestione dei dati tramite il cambio di partizione o l'ottimizzazione delle analisi con l'eliminazione delle partizioni, un numero eccessivo di partizioni può rallentare le query.  Spesso una strategia di partizionamento che prevede una granularità molto elevata, che potrebbe funzionare bene in SQL Server, potrebbe non essere ideale nel pool SQL.  

> [!NOTE]
> Spesso una strategia di partizionamento che prevede una granularità molto elevata, che potrebbe funzionare bene in SQL Server, potrebbe non essere ideale nel pool SQL.  

Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe. Il pool SQL esegue il partizionamento dei dati in 60 database. 

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

Vedere anche [Informazioni sulle transazioni](develop-transactions.md), [Ottimizzazione delle transazioni](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Partizionamento delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce il codice DDL, per migliorare le prestazioni di query usare il più piccolo tipo di dati in grado di supportare i dati.  Questo aspetto è particolarmente importante per le colonne CHAR e VARCHAR.  

Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Panoramica sulle tabelle](develop-tables-overview.md), [Tipi di dati per le tabelle](develop-tables-data-types.md) e [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster rappresentano uno dei modi più efficienti per archiviare i dati nel pool SQL.  Per impostazione predefinita, le tabelle nel pool SQL vengono create come tabelle columnstore cluster.  

Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  

La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere la sezione [Possibili cause di una qualità scadente dell'indice columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) e [Indicizzazione delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

Poiché l'elevata qualità dei segmenti columnstore è importante, è consigliabile usare ID di utenti della classe di risorse media o grande per caricare i dati. L'uso di [unità di data warehouse](resource-consumption-models.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non eseguono il push dei dati in un segmento columnstore compresso fino a quando non sono presenti più di 1 milione di righe per tabella e ogni tabella di pool SQL è suddivisa in 60 tabelle, le tabelle columnstore non risultano vantaggiose per una query a meno che non contengano più di 60 milioni di righe.  

> [!TIP]
> Per le tabelle con meno di 60 milioni righe, la presenza di un indice columnstore potrebbe non essere la soluzione ottimale.  

Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella è divisa in 100 partizioni, per poter sfruttare i vantaggi di un archivio colonne cluster deve contenere almeno 6 miliardi di righe, ovvero 60 distribuzioni *100 partizioni* 1 milione di righe.  

Se la tabella non contiene 6 miliardi di righe, occorre ridurre il numero di partizioni o prendere in considerazione l'uso di una tabella heap.  Potrebbe anche essere utile fare delle prove per verificare se è possibile ottenere prestazioni migliori usando una tabella heap con indici secondari al posto di una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche [Indicizzazione delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Guida agli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [Ricompilazione degli indici columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="sql-on-demand-development-best-practices"></a>Procedure consigliate per lo sviluppo di SQL su richiesta

### <a name="general-considerations"></a>Considerazioni generali

SQL su richiesta consente di eseguire query sui file negli account di archiviazione di Azure. Non dispone di funzionalità di archiviazione o inserimento locale, ovvero tutti i file di destinazione della query sono esterni a SQL su richiesta. Pertanto, tutte le operazioni correlate alla lettura di file dall'archiviazione possono avere un impatto sulle prestazioni delle query.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Condividere la posizione dell'account di archiviazione di Azure e di SQL su richiesta

Per ridurre al minimo la latenza, condividere la posizione dell'account di archiviazione di Azure e dell'endpoint SQL su richiesta. Gli account di archiviazione e gli endpoint sottoposti a provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per prestazioni ottimali, se si accede ad altri account di archiviazione con SQL su richiesta, assicurarsi che si trovino nella stessa area. In caso contrario, si verifica un aumento della latenza per il trasferimento in rete dei dati tra l'area remota e quella dell'endpoint.

### <a name="azure-storage-throttling"></a>Limitazione delle richieste di archiviazione di Azure

Più applicazioni e servizi possono accedere all'account di archiviazione. La limitazione delle richieste di archiviazione si verifica quando il numero combinato di operazioni di I/O al secondo o unità di elaborazione generato da applicazioni, servizi e carichi di lavoro di SQL su richiesta supera i limiti dell'account di archiviazione. Quando si verifica la limitazione delle richieste di archiviazione, esiste un effetto negativo significativo sulle prestazioni delle query.

Una volta rilevata la limitazione delle richieste, SQL su richiesta prevede una soluzione predefinita di gestione di questo scenario. SQL su richiesta effettuerà richieste di archiviazione a un ritmo più lento fino a quando non verrà risolta la limitazione delle richieste. 

Tuttavia, per un'esecuzione ottimale delle query, è consigliabile evitare di sovraccaricare l'account di archiviazione con altri carichi di lavoro durante l'esecuzione di query.

### <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di query

Se possibile, preparare i file per migliorare le prestazioni:

- Convertire i file CSV in Parquet, un formato a colonne. Poiché è compresso, ha dimensioni del file inferiori rispetto ai file CSV con gli stessi dati e SQL su richiesta richiede meno tempo e richieste di archiviazione per leggerlo.
- Se una query è destinata a un singolo file di grandi dimensioni, è possibile dividerla in più file di dimensioni ridotte.
- Provare a mantenere le dimensioni dei file CSV al di sotto di 10 GB.
- È preferibile avere file di dimensioni uguali per un singolo percorso OPENROWSET o per la proprietà LOCATION di una tabella esterna.
- Partizionare i dati archiviando le partizioni in cartelle o nomi file diversi. Vedere come [usare le funzioni filename e filepath per impostare partizioni specifiche come destinazione](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni fileinfo e filepath per indicare come destinazione partizioni specifiche

I dati sono spesso organizzati in partizioni. È possibile indicare a SQL su richiesta di eseguire query su cartelle e file specifici. Questo ridurrà il numero di file e la quantità di dati che la query deve leggere ed elaborare. 

Di conseguenza, si otterranno prestazioni migliori. Per altre informazioni, vedere le funzioni [filename](develop-storage-files-overview.md#filename-function) e [filepath](develop-storage-files-overview.md#filepath-function), oltre ad esempi su come [eseguire query su file specifici](query-specific-files.md).

Se i dati nell'archivio non sono partizionati, provare a partizionarli per poter usare queste funzioni e ottimizzare le query destinate a tali file.

Quando [si eseguono query su tabelle esterne Apache Spark per Azure Synapse partizionate](develop-storage-files-spark-tables.md) da SQL su richiesta, la query verrà destinata automaticamente solo ai file necessari.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare CETAS per ottimizzare le prestazioni e i join delle query

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili in SQL su richiesta. CETAS è un'operazione parallela che crea metadati di tabelle esterne ed esporta i risultati delle query SELECT in un set di file nell'account di archiviazione.

È possibile usare CETAS per archiviare parti di query usate spesso, come le tabelle di riferimento unite in join, in un nuovo set di file. In seguito, è possibile creare un join con questa singola tabella esterna invece di ripetere join comuni in più query. 

Poiché CETAS genera file Parquet, le statistiche vengono create automaticamente quando la prima query raggiunge questa tabella esterna, con un conseguente miglioramento delle prestazioni.

### <a name="next-steps"></a>Passaggi successivi

Se sono necessarie informazioni non fornite in questo articolo, usare "Cerca documenti" sul lato sinistro della pagina per cercare tutti i documenti del pool SQL.  La [pagina delle domande di Domande e risposte Microsoft per pool SQL](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) è un posto in cui è possibile inviare domande ad altri utenti e al gruppo di prodotti pool SQL.  

Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  Se si preferisce porre domande in Stack Overflow, è disponibile anche un [forum di Stack Overflow su pool SQL di Azure](https://stackoverflow.com/questions/tagged/azure-sqldw).
 