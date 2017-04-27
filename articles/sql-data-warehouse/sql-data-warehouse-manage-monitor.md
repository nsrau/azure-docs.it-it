---
title: Monitoraggio del carico di lavoro mediante DMV | Microsoft Docs
description: Informazioni sul monitoraggio del carico di lavoro mediante DMV.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
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
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3735d656429da1f1fe7569f640b272b099382032
ms.lasthandoff: 04/03/2017


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

