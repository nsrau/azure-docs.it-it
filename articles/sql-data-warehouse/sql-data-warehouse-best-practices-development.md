---
title: Procedure consigliate di sviluppo per Azure SQL Data Warehouse | Microsoft Docs
description: Indicazioni e procedure consigliate da conoscere per lo sviluppo di soluzioni per Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2debe9000cb60d4dea7395181dfb783fe26586f1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499850"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Procedure consigliate di sviluppo per Azure SQL Data Warehouse
Questo articolo descrive le procedure consigliate e linee guida quando si sviluppa una soluzione di data warehouse. 

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ottimizzare le prestazioni delle query con nuovi miglioramenti del prodotto  
- [Ottimizzazione delle prestazioni con viste materializzate](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Ottimizzazione delle prestazioni con indice columnstore cluster ordinato](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità
Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire la potenza di calcolo](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Gestire le statistiche
Azure SQL Data Warehouse può essere configurato per rilevare e creare automaticamente statistiche sulle colonne.  I piani di query creati dall'utilità di ottimizzazione sono validi solo per le statistiche disponibili.  È consigliabile abilitare AUTO_CREATE_STATISTICS per i database e mantenere aggiornate le statistiche ogni giorno o dopo ogni carico per garantire che le statistiche sulle colonne utilizzate nelle query siano sempre aggiornate. 

Se si ritiene che l'aggiornamento di tutte le statistiche richieda troppo tempo, è consigliabile provare a usare più selettivamente le colonne che necessitano di aggiornamenti frequenti delle statistiche. Potrebbe ad esempio essere consigliabile aggiornare le colonne di data, in cui potrebbero venire aggiunti nuovi valori ogni giorno. **Per ottenere il massimo vantaggio, è necessario aggiornare le statistiche sulle colonne incluse nei join, sulle colonne utilizzate nella clausola WHERE e sulle colonne presenti in GROUP BY.**

Vedere anche [Gestire le statistiche delle tabelle][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS] e [UPDATE STATISTICS][UPDATE STATISTICS].

## <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni
Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.  Questa progettazione consente agli utenti di iniziare a creare tabelle in modo semplice senza dover decidere come distribuire le tabelle.  Le tabelle round robin possono funzionare bene per alcuni carichi di lavoro, ma spesso la selezione di una colonna di distribuzione offre prestazioni notevolmente migliori.  L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  Se, ad esempio, sono presenti una tabella degli ordini, distribuita in base a id_ordine, e una tabella delle transazioni, anch'essa distribuita in base a id_ordine, quando si uniscono in join le due tabelle in base a id_ordine, la query diventa una query pass-through, il che significa che si eliminano le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.  Questa spiegazione solo la superficie. Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  Vedere i collegamenti seguenti per altri dettagli su come la selezione di una colonna di distribuzione può migliorare le prestazioni e come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE Tables.

Vedere anche [Panoramica sulle tabelle][Table overview], [Distribuzione delle tabelle][Table distribution], il post di blog relativo alla [scelta della modalità di distribuzione delle tabelle][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni
Sebbene i dati di partizionamento possano essere efficaci per gestire i dati tramite il cambio della partizione o l'ottimizzazione delle analisi tramite l'eliminazione della partizione, la presenza di un numero eccessivo di partizioni può rallentare le query.  Spesso una strategia di partizionamento a granularità elevata che può funzionare correttamente in SQL Server potrebbe non funzionare correttamente in SQL Data Warehouse.  Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe.  Tenere presente che in background SQL Data Warehouse partiziona automaticamente i dati in 60 database, quindi se si crea una tabella con 100 partizioni, in realtà si formano 6000 partizioni.  Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  Prendere in considerazione una granularità inferiore rispetto a quella considerata appropriata in SQL Server.  Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [Partizionamento delle tabelle][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni
Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback.  Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE.  Se, ad esempio, si dispone di un inserimento, che si prevede di eseguire 1 ora, se possibile, suddividere l'inserimento in quattro parti, ognuna delle quali verrà eseguita in 15 minuti.  Sfruttare i casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, in modo da ridurre il rischio di rollback.  Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  Ad esempio, invece di eseguire un'istruzione DELETE per eliminare tutte le righe in una tabella in cui data_ordine corrisponde a ottobre 2001, è possibile creare una partizione mensile dei dati e quindi scambiare la partizione con i dati con una partizione vuota di un'altra tabella (vedere gli esempi relativi ad ALTER TABLE).  Per le tabelle non partizionate, prendere in considerazione l'uso di CTAS per scrivere i dati da mantenere in una tabella, invece di usare DELETE.  Se un CTAS richiede la stessa quantità di tempo, è un'operazione molto più sicura da eseguire perché ha una registrazione minima delle transazioni e può essere annullata rapidamente, se necessario.

Vedere anche [Informazioni sulle transazioni][Understanding transactions], [Ottimizzazione delle transazioni][Optimizing transactions], [Partizionamento delle tabelle][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)].

## <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili
Quando si definisce la DDL, l'utilizzo del tipo di dati più piccolo che supporterà i dati migliorerà le prestazioni di esecuzione delle query.  Questo aspetto è particolarmente importante per le colonne CHAR e VARCHAR.  Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Panoramica sulle tabelle][Table overview], [Tipi di dati per le tabelle][Table data types], [CREATE TABLE][CREATE TABLE].

## <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster
Gli indici columnstore cluster rappresentano uno dei modi più efficienti per archiviare i dati in SQL Data Warehouse.  Per impostazione predefinita, le tabelle in SQL Data Warehouse vengono create come tabelle columnstore cluster.  Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere la sezione [Possibili cause di una qualità scadente dell'indice columnstore][Causes of poor columnstore index quality] nell'articolo [Indicizzazione delle tabelle][Table indexes].  Poiché i segmenti columnstore di alta qualità sono importanti, è consigliabile usare gli ID utente presenti nella classe di risorse media o large per il caricamento dei dati. L'uso di [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non effettuano il push dei dati in un segmento columnstore compresso fino a quando non sono presenti più di 1 milione di righe per tabella e ogni tabella di SQL Data Warehouse è suddivisa in 60 tabelle, come regola generale le tabelle columnstore non risultano vantaggiose per una query a meno che non contengano più di 60 milioni di righe.  Per le tabelle con meno di 60 milioni di righe, l'uso di un indice columnstore potrebbe non essere utile,  ma potrebbe anche non avere effetti negativi.  Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella ha 100 partizioni, dovrà contenere almeno 6 miliardi di righe per trarre vantaggio da un archivio colonne cluster (60 distribuzioni * 100 partizioni * 1 milione di righe).  Se la tabella non contiene 6 miliardi di righe come in questo esempio, ridurre il numero di partizioni o prendere in considerazione l'utilizzo di una tabella heap.  Potrebbe anche essere utile fare delle prove per verificare se è possibile ottenere prestazioni migliori con una tabella heap con indici secondari al posto di una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche gli argomenti sull'[indicizzazione di tabelle][Table indexes], la [guida agli indici columnstore][Columnstore indexes guide] e la [ricompilazione degli indici columnstore][Rebuilding columnstore indexes].

## <a name="next-steps"></a>Passaggi successivi
Se non si trova ciò che si sta cercando in questo articolo, provare a usare "Cerca documenti" sul lato sinistro della pagina per cercare tutti i documenti Azure SQL Data Warehouse.  Il [Forum Azure SQL data warehouse][Azure SQL Data Warehouse MSDN Forum] rappresenta una posizione in cui porre domande ad altri utenti e al gruppo di prodotti SQL data warehouse.  Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  Se si preferisce porre domande in Stack Overflow, è disponibile anche un [forum di Stack Overflow su Azure SQL Data Warehouse][Azure SQL Data Warehouse Stack Overflow Forum].

Per inviare richieste di funzionalità è possibile usare la pagina dei [commenti e suggerimenti su Azure SQL Data Warehouse][Azure SQL Data Warehouse Feedback] .  Le richieste aggiunte o i voti per altre richieste sono utili per definire le priorità per le funzionalità.

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
