---
title: Monitoraggio del carico di lavoro mediante DMV | Microsoft Docs
description: Informazioni sul monitoraggio del carico di lavoro mediante DMV.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 69ecd479-0941-48df-b3d0-cf54c79e6549
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: 7ce6c2cdf1e28852da536414533ccdcdaeb437e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-your-workload-using-dmvs"></a>Monitoraggio del carico di lavoro mediante DMV
In questo articolo viene descritto come utilizzare le viste a gestione dinamica (DMV) per monitorare il carico di lavoro ed esaminare l'esecuzione delle query SQL Data Warehouse di Azure.

## <a name="permissions"></a>Autorizzazioni
Per eseguire una query sulle DMV in questo articolo è necessaria l'autorizzazione VISUALIZZAZIONE STATO DEL DATABASE o CONTROLLO. In genere si consiglia di concedere l'autorizzazione VISUALIZZAZIONE STATO DEL DATABASE poiché è molto più restrittiva.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitorare le connessioni
Tutti gli accessi a SQL Data Warehouse vengono registrati in [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Questa DMV contiene gli ultimi 10.000 accessi.  L'elemento session_id è la chiave primaria e viene assegnato in sequenza per ogni nuovo accesso.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitorare l'esecuzione di query
Tutte le query eseguite in SQL Data Warehouse vengono registrate in [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Questa DMV contiene le ultime 10.000 query eseguite.  L'elemento request_id identifica in modo univoco ogni query ed è la chiave primaria per questa DMV.  L'elemento request_id viene assegnato in sequenza per ogni nuova query ed è preceduto da un prefisso con QID, che indica l'ID query.  Se si esegue una query nella DMV per un dato session_id, vengono visualizzate tutte le query per un determinato accesso.

> [!NOTE]
> Le stored procedure usano più ID richiesta.  Gli ID richiesta vengono assegnati in ordine sequenziale. 
> 
> 

Ecco i passaggi da seguire per analizzare i piani e i tempi di esecuzione delle query per una query specifica.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>PASSAGGIO 1: individuare la query da analizzare
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

**Prendere nota dell'ID richiesta** della query che si desidera analizzare dai risultati della query precedente.

Le query con stato **Sospeso** vengono messe in coda a causa dei limiti di concorrenza. Queste query vengono visualizzate anche nella query sys.dm_pdw_waits waits con un tipo UserConcurrencyResourceType. Per altre informazioni sui limiti di concorrenza, vedere [Gestione della concorrenza e del carico di lavoro][Concurrency and workload management]. L'attesa delle query può dipendere anche da altre motivazioni, come i blocchi degli oggetti.  Se la query è in attesa di una risorsa, vedere [Analisi delle query in attesa di risorse][Investigating queries waiting for resources] più avanti in questo articolo.

Per semplificare la ricerca di una query nella tabella sys.dm_pdw_exec_requests, usare [LABEL][LABEL] per assegnare alla query un commento che possa essere cercato nella visualizzazione sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>PASSAGGIO 2: Esaminare il piano di query
Usare l'ID richiesta per recuperare il piano Distributed SQL (DSQL) della query da [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando un piano di DSQL impiega più tempo del previsto, la causa può essere un la complessità del piano, dovuta a molti passaggi di DSQL o a un solo passaggio che richiede molto tempo.  Se il piano prevede molti passaggi con numerose operazioni di spostamento, prendere in considerazione di ottimizzare le distribuzioni di tabelle per ridurre lo spostamento dei dati. L'articolo [Distribuzione di tabelle][Table distribution] spiega perché è necessario spostare i dati per risolvere una query e illustra alcune strategie di distribuzione per ridurre al minimo lo spostamento dei dati.

Per altre informazioni su un singolo passaggio, fare riferimento alla colonna *operation_type* del passaggio della query con esecuzione prolungata e all'**indice dei passaggi**:

* Procedere al passaggio 3a per le **operazioni SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Procedere al passaggio 3b per le **operazioni di spostamento dati**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>PASSAGGIO 3a: Esaminare SQL nei database distribuiti
Usare l'ID richiesta e l'indice dei passaggi per recuperare informazioni da [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], che contiene informazioni sull'esecuzione del passaggio della query in tutti i database distribuiti.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quando è in esecuzione il passaggio della query, è possibile usare [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] per recuperare il piano stimato di SQL Server dalla cache dei piani di SQL Server per il passaggio di esecuzione in una distribuzione specifica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>PASSAGGIO 3b: Esaminare lo spostamento dei dati nei database distribuiti
Usare l'ID richiesta e l'indice dei passaggi per recuperare informazioni sul passaggio di spostamento dei dati in esecuzione in ogni distribuzione da [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Controllare la colonna *total_elapsed_time* per verificare se una distribuzione particolare richiede più tempo per lo spostamento dei dati rispetto alle altre.
* Per la distribuzione con esecuzione prolungata, esaminare la colonna *rows_processed* e controllare se il numero di righe spostato da tale distribuzione è significativamente più grande rispetto alle altre. In caso affermativo, questo potrebbe indicare asimmetria dei dati sottostanti.

Se la query è in esecuzione, è possibile usare [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] per recuperare il piano stimato di SQL Server dalla cache dei piani di SQL Server per il passaggio SQL in esecuzione per una distribuzione specifica.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorare le query in attesa
Se si rileva il mancato avanzamento di una query perché in attesa di una risorsa, ecco una query che mostra tutte le risorse attese da una query.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se la query è attivamente in attesa di risorse da un'altra query, lo stato sarà **AcquireResources**.  Se la query dispone di tutte le risorse necessarie, lo stato sarà **Granted**.

## <a name="monitor-tempdb"></a>Monitorare tempdb
Un uso intensivo di tempdb può essere la causa principale del rallentamento delle prestazioni e dei problemi di memoria insufficiente. In primo luogo verificare se sono presenti rowgroup di qualità insufficiente o asimmetria dei dati e adottare le misure appropriate. Se durante l'esecuzione di query tempdb raggiunge i limiti previsti, prendere in considerazione il ridimensionamento del data warehouse. Di seguito viene descritto come identificare l'uso di tempdb per ogni query in ogni nodo. 

Creare la vista seguente per associare l'ID nodo appropriato per sys.dm_pdw_sql_requests. In questo modo sarà possibile sfruttare altre DMV pass-through e unire le tabelle con sys.dm_pdw_sql_requests.

```sql
-- sys.dm_pdw_sql_requests with the correct node id
CREATE VIEW sql_requests AS
(SELECT
       sr.request_id,
       sr.step_index,
       (CASE 
              WHEN (sr.distribution_id = -1 ) THEN 
              (SELECT pdw_node_id FROM sys.dm_pdw_nodes WHERE type = 'CONTROL') 
              ELSE d.pdw_node_id END) AS pdw_node_id,
       sr.distribution_id,
       sr.status,
       sr.error_id,
       sr.start_time,
       sr.end_time,
       sr.total_elapsed_time,
       sr.row_count,
       sr.spid,
       sr.command
FROM sys.pdw_distributions AS d
RIGHT JOIN sys.dm_pdw_sql_requests AS sr ON d.distribution_id = sr.distribution_id)
```
Per monitorare tempdb eseguire la query seguente:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```
## <a name="monitor-memory"></a>Monitorare la memoria

La memoria può essere la causa principale del rallentamento delle prestazioni e dei problemi di memoria insufficiente. In primo luogo verificare se sono presenti rowgroup di qualità insufficiente o asimmetria dei dati e adottare le misure appropriate. Se durante l'esecuzione di query l'uso di memoria di SQL Server raggiunge i limiti previsti, prendere in considerazione il ridimensionamento del data warehouse.

La query seguente restituisce l'uso di memoria di SQL Server e l'eventuale uso elevato in ogni nodo:   
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>Monitorare le dimensioni del log delle transazioni
La query seguente restituisce le dimensioni del log delle transazioni per ogni distribuzione. Verificare se sono presenti rowgroup di qualità insufficiente o asimmetria dei dati e adottare le misure appropriate. Se uno dei file di log sta per raggiungere i 160 GB, considerare l'espansione dell'istanza del programma o la limitazione delle dimensioni delle transazioni. 
```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
AND counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>Monitorare il ripristino dello stato precedente del log delle transazioni
Se le query non vanno a buon fine o richiedono molto tempo, verificare se è in corso il ripristino dello stato precedente delle transazioni e monitorare questa operazione.
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle DMV, vedere [Viste di sistema][System views].
Per altre informazioni sulle procedure consigliate, vedere [Procedure consigliate per Azure SQL Data Warehouse][SQL Data Warehouse best practices]

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Concurrency and workload management]: ./sql-data-warehouse-develop-concurrency.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
