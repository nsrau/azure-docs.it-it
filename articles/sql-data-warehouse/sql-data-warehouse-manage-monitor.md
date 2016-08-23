<properties
   pageTitle="Monitoraggio del carico di lavoro mediante DMV | Microsoft Azure"
   description="Informazioni sul monitoraggio del carico di lavoro mediante DMV."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/22/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# Monitoraggio del carico di lavoro mediante DMV

In questo articolo viene descritto come utilizzare le viste a gestione dinamica (DMV) per monitorare il carico di lavoro ed esaminare l'esecuzione delle query SQL Data Warehouse di Azure.

## Monitorare le connessioni

La vista [sys.dm\_pdw\_exec\_sessions][] consente di monitorare le connessioni al database Azure SQL Data Warehouse. Questa vista contiene sessioni attive, nonché una cronologia delle sessioni disconnesse di recente. L'elemento session\_id è la chiave primaria per questa vista e viene assegnato in sequenza per ogni nuovo accesso.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## Monitorare l'esecuzione di query

Per monitorare l'esecuzione di query, iniziare con [sys.dm\_pdw\_exec\_requests][]. Questa vista contiene le query in corso, nonché una cronologia delle query completate di recente. L'elemento request\_id identifica in modo univoco ogni query ed è la chiave primaria per questa vista. Viene assegnato in sequenza per ogni nuova query. Se si esegue una query nella tabella per un dato session\_id, verranno visualizzate tutte le query per un determinato accesso.

>[AZURE.NOTE] Le stored procedure usano più ID richiesta, che vengono assegnati in ordine sequenziale.

Ecco i passaggi da seguire per analizzare i piani e i tempi di esecuzione delle query per una query specifica.

### PASSAGGIO 1: individuare la query da analizzare

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
```

**Prendere nota dell'ID richiesta** della query che si desidera analizzare dai risultati della query precedente.

Le query nello stato Suspended vengono accodate a causa dei limiti di concorrenza descritti in dettaglio nell'articolo [Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse][]. Queste query verranno visualizzate anche nella query sys.dm\_pdw\_waits waits con un tipo UserConcurrencyResourceType. L'attesa delle query può dipendere anche da altre motivazioni, come un blocco. Se la query è in attesa di una risorsa, vedere la sezione sull'[analisi delle query in attesa di risorse][].

### PASSAGGIO 2: individuare il passaggio in esecuzione più lungo del piano di query

Usare l'ID richiesta per recuperare un elenco dei passaggi del piano di query da [sys.dm\_pdw\_request\_steps][]. Individuare l'istruzione eseguita in tempi prolungati a osservando il tempo trascorso totale.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Controllare la colonna *operation\_type* del passo di query con esecuzione prolungata e notare l'**indice passo**:

- Procedere al passaggio 3a per le **operazioni SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Procedere al passaggio 3b per le **operazioni di spostamento dati**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### PASSAGGIO 3a: trovare lo stato di esecuzione di un passaggio SQL

Usare l'ID richiesta e l'indice passo per recuperare informazioni da [sys.dm\_pdw\_sql\_requests][], che contiene dettagli sull'esecuzione della query nelle istanze distribuite di SQL Server.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Se la query è in esecuzione, è possibile usare [DBCC PDW\_SHOWEXECUTIONPLAN][] per recuperare il piano stimato di SQL Server dalla cache dei piani di SQL Server per il passaggio SQL in esecuzione per una distribuzione particolare.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### PASSAGGIO 3b: trovare lo stato di esecuzione di un passaggio DMS

Usare l'ID richiesta e l'indice passo per recuperare informazioni sul passaggio DMS in esecuzione in ogni distribuzione da [sys.dm\_pdw\_dms\_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Controllare la colonna *total\_elapsed\_time* per verificare se una distribuzione particolare richiede più tempo per lo spostamento dati rispetto alle altre.
- Per la distribuzione con esecuzione prolungata, esaminare la colonna *rows\_processed* e controllare se il numero di righe spostato da tale distribuzione è significativamente più grande rispetto alle altre. In caso affermativo, questo potrebbe indicare asimmetria dei dati sottostanti.

Se la query è in esecuzione, è possibile usare [DBCC PDW\_SHOWEXECUTIONPLAN][] per recuperare il piano stimato di SQL Server dalla cache dei piani di SQL Server per il passaggio SQL in esecuzione per una distribuzione particolare.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## Monitorare le query in attesa

Se si rileva il mancato avanzamento di una query perché in attesa di una risorsa, ecco una query che mostra tutte le risorse per le quali una query è in attesa.

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

Se la query è attivamente in attesa di risorse da un'altra query, lo stato sarà **AcquireResources**. Se la query dispone di tutte le risorse necessarie, lo stato sarà **Granted**.

## Passaggi successivi
Per altre informazioni sulle viste a gestione dinamica (DMV), vedere [Viste di sistema][]. Per suggerimenti sulla gestione di SQL Data Warehouse, vedere [Gestire i database in Azure SQL Data Warehouse][]. Per informazioni sulle procedure consigliate, vedere [Procedure consigliate per Azure SQL Data Warehouse][].

<!--Image references-->  

<!--Article references-->
[Gestire i database in Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-manage.md
[Procedure consigliate per Azure SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Viste di sistema]: ./sql-data-warehouse-reference-tsql-system-views.md
[Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse]: ./sql-data-warehouse-develop-concurrency.md
[analisi delle query in attesa di risorse]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0810_2016-->