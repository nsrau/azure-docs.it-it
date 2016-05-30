<properties
   pageTitle="Trovare query utente con esecuzione prolungata in Azure SQL Data Warehouse | Microsoft Azure"
   description="Informazioni su come monitorare il carico di lavoro e trovare query con esecuzione prolungata in Azure SQL Data Warehouse usando le viste a gestione dinamica (DMV)."
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
   ms.date="05/03/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# Trovare query con esecuzione prolungata

In questo articolo viene descritto come utilizzare le viste a gestione dinamica (DMV) per monitorare il carico di lavoro ed esaminare l'esecuzione delle query SQL Data Warehouse di Azure.

## Monitoraggio delle connessioni

La vista [sys.dm\_pdw\_exec\_sessions][] consente di monitorare le connessioni al database Azure SQL Data Warehouse. Questa vista contiene sessioni attive, nonché una cronologia delle sessioni disconnesse di recente. L'elemento session\_id è la chiave primaria per questa vista e viene assegnato in sequenza per ogni nuovo accesso.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## Analisi dell'esecuzione di query
Per monitorare l'esecuzione di query, iniziare con [sys.dm\_pdw\_exec\_requests][]. Questa vista contiene le query in corso, nonché una cronologia delle query completate di recente. L'elemento request\_id identifica in modo univoco ogni query ed è la chiave primaria per questa vista. Viene assegnato in sequenza per ogni nuova query. Se si esegue una query nella tabella per un dato session\_id, verranno visualizzate tutte le query per un determinato account di accesso.

Se si desidera analizzare l'esecuzione per una determinata query, di seguito sono descritte alcune semplici procedure da seguire.

### PASSAGGIO 1: individuare la query da analizzare

```sql
-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find 10 queries which ran the longest
SELECT TOP 10 * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;
```

Prendere nota dell'ID richiesta della query che si desidera analizzare.

### PASSAGGIO 2: verificare se la query è in attesa di risorse

```sql
-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,  
      waits.object_type,
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

I risultati della query sopra descritta indicano lo stato di attesa della query da analizzare.

- Se la query è in attesa di risorse da un'altra query, lo stato sarà **AcquireResources**.
- Se la query dispone di tutte le risorse necessarie e non è in attesa, lo stato sarà **Granted**. In questo caso, esaminare le istruzioni della query.

### PASSAGGIO 3: individuare il passaggio in esecuzione più lungo del piano di query

Usare l'ID richiesta per recuperare un elenco dei passaggi del piano di query da [sys.dm\_pdw\_request\_steps][]. Individuare l'istruzione eseguita in tempi prolungati a osservando il tempo trascorso totale.

```sql

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;
```

Salvare l'indice dell'istruzione eseguita in tempi prolungati.

Controllare la colonna *operation\_type* dell'istruzione di query con esecuzione prolungata:

- Procedere al passaggio 4a per le **operazioni SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Procedere al passaggio 4b per le **operazioni di spostamento dati**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### PASSAGGIO 4a: individuare lo stato di esecuzione di un'istruzione SQL

Usare l'ID richiesta e l'indice passo per recuperare informazioni dalla vista [sys.dm\_pdw\_sql\_requests][] che contiene dettagli sull'esecuzione della query sulle istanze distribuite di SQL Server. Prendere nota dell'ID di distribuzione e dello SPID se la query è ancora in esecuzione e si desidera ottenere il piano di distribuzione da SQL Server.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;
```


Se la query è in esecuzione, è possibile usare [DBCC PDW\_SHOWEXECUTIONPLAN][] per recuperare il piano di esecuzione di SQL Server per l'istruzione SQL in esecuzione per una distribuzione particolare.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```

### PASSAGGIO 4b: individuare lo stato di esecuzione di un passaggio del DMS

Usare l'ID richiesta e l'indice passo per recuperare informazioni sul passaggio del DMS in esecuzione in ciascuna distribuzione da [sys.dm\_pdw\_dms\_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Controllare la colonna *total\_elapsed\_time* per verificare se una distribuzione particolare richiede più tempo per lo spostamento dati rispetto alle altre.
- Per la distribuzione con esecuzione prolungata, esaminare la colonna *rows\_processed* e controllare se il numero di righe spostato da tale distribuzione è significativamente più grande rispetto alle altre. In caso affermativo, questo potrebbe indicare asimmetria dei dati sottostanti.

Se la query è in esecuzione, tramite [DBCC PDW\_SHOWEXECUTIONPLAN][] è possibile recuperare il piano di esecuzione di SQL Server per il passaggio DMS in esecuzione per una distribuzione particolare.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);

```

## Passaggi successivi
Per altre informazioni su DMV, vedere [Viste di sistema][]. Per alcuni suggerimenti sulla gestione di SQL Data Warehouse, vedere l'articolo che offre una [panoramica della gestione][]. Per le procedure consigliate, vedere l'articolo sulle [procedure consigliate di SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[manage data skew for distributed tables]: sql-data-warehouse-manage-distributed-data-skew.md
[panoramica della gestione]: sql-data-warehouse-overview-manage.md
[procedure consigliate di SQL Data Warehouse]: sql-data-warehouse-best-practices.md
[Viste di sistema]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0518_2016-->