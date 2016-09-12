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
   ms.date="08/28/2016"
   ms.author="sonyama;barbkess"/>

# Monitoraggio del carico di lavoro mediante DMV

In questo articolo viene descritto come utilizzare le viste a gestione dinamica (DMV) per monitorare il carico di lavoro ed esaminare l'esecuzione delle query SQL Data Warehouse di Azure.

## Monitorare le connessioni

Tutti gli accessi a SQL Data Warehouse vengono registrati in [sys.dm\_pdw\_exec\_sessions][]. Questa DMV contiene gli ultimi 10.000 accessi. L'elemento session\_id è la chiave primaria e viene assegnato in sequenza per ogni nuovo accesso.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## Monitorare l'esecuzione di query

Tutte le query eseguite in SQL Data Warehouse vengono registrate in [sys.dm\_pdw\_exec\_requests][]. Questa DMV contiene le ultime 10.000 query eseguite. L'elemento request\_id identifica in modo univoco ogni query ed è la chiave primaria per questa DMV. L'elemento request\_id viene assegnato in sequenza per ogni nuova query ed è preceduto da un prefisso con QID, che indica l'ID query. Se si esegue una query nella DMV per un dato session\_id, vengono visualizzate tutte le query per un determinato accesso.

>[AZURE.NOTE] Le stored procedure usano più ID richiesta. Gli ID richiesta vengono assegnati in ordine sequenziale.

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

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

**Prendere nota dell'ID richiesta** della query che si desidera analizzare dai risultati della query precedente.

Le query con stato **Sospeso** vengono messe in coda a causa dei limiti di concorrenza. Queste query vengono visualizzate anche nella query sys.dm\_pdw\_waits waits con un tipo UserConcurrencyResourceType. Per ulteriori informazioni sui limiti di concorrenza, vedere [Gestione della concorrenza e del carico di lavoro][]. L'attesa delle query può dipendere anche da altre motivazioni, come i blocchi degli oggetti. Se la query è in attesa di una risorsa, vedere [Analisi delle query in attesa di risorse][] più avanti in questo articolo.

Per semplificare la ricerca di una query nella tabella sys.dm\_pdw\_exec\_requests, usare [LABEL][] per assegnare alla query un commento che possa essere cercato nella visualizzazione sys.dm\_pdw\_exec\_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### PASSAGGIO 2: Esaminare il piano di query

Usare l'ID richiesta per recuperare il piano di SQL distribuiti (DSQL) della query da [sys.dm\_pdw\_request\_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando un piano di DSQL impiega più tempo del previsto, la causa può essere un la complessità del piano, dovuta a molti passaggi di DSQL o a un solo passaggio che richiede molto tempo. Se il piano prevede molti passaggi con numerose operazioni di spostamento, prendere in considerazione di ottimizzare le distribuzioni di tabelle per ridurre lo spostamento dei dati. L'articolo [Distribuzione di tabelle][] spiega perché è necessario spostare i dati per risolvere una query e illustra alcune strategie di distribuzione per ridurre al minimo lo spostamento dei dati.

Per ulteriori dettagli su un singolo passaggio, fare riferimento alla colonna *operation\_type* del passaggio di query con esecuzione prolungata e all'**indice dei passaggi**:

- Procedere al passaggio 3a per le **operazioni SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Procedere al passaggio 3b per le **operazioni di spostamento dati**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### PASSAGGIO 3a: Esaminare SQL nei database distribuiti

Usare l'ID richiesta e l'indice dei passaggi per recuperare informazioni da [sys.dm\_pdw\_sql\_requests][], che contiene informazioni sull'esecuzione del passaggio della query in tutti i database distribuiti.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quando è in esecuzione il passaggio della query, è possibile usare [DBCC PDW\_SHOWEXECUTIONPLAN][] per recuperare il piano stimato di SQL Server dalla cache dei piani di SQL Server per il passaggio di esecuzione in una distribuzione particolare.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### PASSAGGIO 3b: Esaminare lo spostamento dei dati nei database distribuiti

Usare l'ID richiesta e l'indice dei passaggi per recuperare informazioni sul passaggio di spostamento dei dati in esecuzione in ogni distribuzione da [sys.dm\_pdw\_dms\_workers][].

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

Se la query è attivamente in attesa di risorse da un'altra query, lo stato sarà **AcquireResources**. Se la query dispone di tutte le risorse necessarie, lo stato sarà **Granted**.

## Passaggi successivi
Per altre informazioni sulle viste a gestione dinamica (DMV), vedere [Viste di sistema][]. 
Per suggerimenti sulla gestione di SQL Data Warehouse, vedere [Gestire i database in Azure SQL Data Warehouse][]. 
Per informazioni sulle procedure consigliate, vedere [Procedure consigliate per Azure SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Gestire i database in Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-manage.md
[Procedure consigliate per Azure SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Viste di sistema]: ./sql-data-warehouse-reference-tsql-system-views.md
[Distribuzione di tabelle]: ./sql-data-warehouse-tables-distribute.md
[Gestione della concorrenza e del carico di lavoro]: ./sql-data-warehouse-develop-concurrency.md
[Analisi delle query in attesa di risorse]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

<!---HONumber=AcomDC_0831_2016-->