---
title: Monitoraggio delle prestazioni del database SQL di Azure con DMV | Microsoft Docs
description: Informazioni su come rilevare e diagnosticare i problemi di prestazioni comuni utilizzando visualizzazioni a gestione dinamica per monitorare il Database SQL di Microsoft Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 1f3aaf34283f4e7c81f97b8e2ec4987749f3935c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465283"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Monitoraggio delle prestazioni del database SQL di Azure tramite DMV

Il database SQL di Microsoft Azure consente a un sottoinsieme di visualizzazioni a gestione dinamica di diagnosticare i problemi delle prestazioni che potrebbero essere causati da query bloccate o con esecuzione prolungata, colli di bottiglia delle risorse, piani di query insufficienti e così via. Questo argomento fornisce informazioni su come rilevare problemi comuni relativi alle prestazioni tramite le DMV.

Il database SQL supporta parzialmente tre categorie di visualizzazioni a gestione dinamica:

- Visualizzazioni a gestione dinamica relative al database.
- Visualizzazioni a gestione dinamica relative all'esecuzione.
- Visualizzazioni a gestione dinamica relative alle transazioni.

Per informazioni dettagliate sulle visualizzazioni a gestione dinamica, vedere [Visualizzazioni a gestione dinamica e funzioni (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) nella documentazione Online di SQL Server.

## <a name="permissions"></a>Autorizzazioni

Nel Database SQL, l'esecuzione di query in una visualizzazione a gestione dinamica richiede autorizzazioni **VIEW DATABASE STATE** . Le autorizzazioni **VIEW DATABASE STATE** restituiscono informazioni su tutti gli oggetti all'interno del database corrente.
Per concedere le autorizzazioni **VIEW DATABASE STATE** a un utente di database specifico, eseguire la query seguente:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

In un'istanza di SQL Server locale, le viste a gestione dinamica restituiscono informazioni sullo stato del server. Nel database SQL, restituiscono informazioni relative esclusivamente al database logico corrente.

## <a name="identify-cpu-performance-issues"></a>Identificare i problemi di prestazioni della CPU

Se l'utilizzo della CPU è superiore all'80% per lunghi periodi di tempo, considerare i passaggi di risoluzione dei problemi seguenti:

### <a name="the-cpu-issue-is-occurring-now"></a>Il problema della CPU si sta verificando in questo momento

Se il problema si sta verificando in questo momento, esistono due possibili scenari:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Numerose singole query hanno un utilizzo cumulativo elevato della CPU

Usare la query seguente per identificare gli hash di query più frequenti:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Query con esecuzione prolungata che usano la CPU sono ancora in esecuzione

Usare la query seguente per identificare le query:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>Il problema della CPU si è verificato in precedenza

Se il problema si è verificato in precedenza e si vuole eseguire l'analisi della causa radice, usare [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Gli utenti con accesso al database possono usare T-SQL per eseguire query sui dati di Query Store.  Le configurazioni predefinite di Query Store usano una granularità di 1 ora.  Usare la query seguente per esaminare l'attività di query con un utilizzo elevato della CPU. Questa query restituisce le 15 query con un maggior utilizzo della CPU.  Ricordarsi di modificare `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Dopo avere identificato le query problematiche, è possibile ottimizzare le query per ridurre l'utilizzo della CPU.  Se l'ottimizzazione delle query richiede troppo tempo, è anche possibile eseguire l'aggiornamento dello SLO del database per risolvere il problema.

## <a name="identify-io-performance-issues"></a>Identificare i problemi di prestazioni di IO

Quando si identificano i problemi di prestazioni di IO, i tipi di attesa più frequenti associati a problemi di IO sono i seguenti:

- `PAGEIOLATCH_*`

  Per i problemi di IO dei file di dati (inclusi `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Se il nome del tipo di attesa include **IO**, il tipo di attesa punta a un problema di IO. Se il nome di attesa latch della pagina non include **IO**, il tipo di attesa punta a un tipo di problema diverso, ad esempio alla contesa di tempdb.

- `WRITE_LOG`

  Per i problemi di IO del log delle transazioni.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Se il problema di IO si sta verificando in questo momento

Usare [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) o [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) per visualizzare `wait_type` e `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identificare l'utilizzo di IO dei dati e dei log

Usare la query seguente per identificare l'utilizzo di IO dei dati e dei log. Se l'IO dei dati e dei log è superiore all'80%, significa che gli utenti hanno usato l'IO disponibile per il livello di servizio del database SQL.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Se è stato raggiunto il limite di IO, sono disponibili due opzioni:

- Opzione 1: Aggiornare le dimensioni di calcolo o il livello di servizio
- Opzione 2: Identificare e ottimizzare le query che usano la maggior parte dell'IO.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Visualizzare l'IO correlato ai buffer usando Query Store

Per l'opzione 2, è possibile usare la query seguente in Query Store per l'IO correlato ai buffer per visualizzare le ultime due ore di attività tracciate:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Visualizzare l'IO totale dei log per le attese WRITELOG

Se il tipo di attesa è `WRITELOG`, usare la query seguente per visualizzare l'IO totale dei log per istruzione:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identificare i problemi di prestazioni di `tempdb`

Quando si identificano i problemi di prestazioni di IO, il tipo di attesa più frequente associato a problemi di `tempdb` è `PAGELATCH_*` (non `PAGEIOLATCH_*`). Tuttavia, le attese `PAGELATCH_*` non indicano sempre una contesa di `tempdb`.  Questo tipo di attesa può anche indicare una contesa della pagina di dati utente-oggetto causata da richieste simultanee che puntano alla stessa pagina di dati. Per confermare la contesa di `tempdb`, usare [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) per confermare che il valore wait_resource inizia con `2:x:y` dove 2 è `tempdb` è l'ID database, `x` è l'ID file e `y` è l'ID pagina.  

Per la contesa di tempdb, un metodo comune consiste nel ridurre o riscrivere il codice dell'applicazione che si basa su `tempdb`.  Le aree di utilizzo di `tempdb` comuni includono:

- Tabelle temporanee
- Variabili di tabella
- Parametri con valori di tabella
- Utilizzo dell'archivio versioni (associato in modo specifico alle transazioni a esecuzione prolungata)
- Query con piani di query che usano ordinamenti, hash join e spool

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Query con maggior utilizzo di variabili di tabella e tabelle temporanee

Usare la query seguente per identificare le query con maggior utilizzo di variabili di tabella e tabelle temporanee:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identificare le transazioni a esecuzione prolungata

Usare la query seguente per identificare le transazioni a esecuzione prolungata. Le transazioni a esecuzione prolungata impediscono la pulizia dell'archivio versioni.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identificare i problemi di prestazioni di attesa della concessione di memoria

Se il tipo di attesa più frequente è `RESOURCE_SEMAHPORE` e non si riscontra un problema di utilizzo elevato della CPU, è possibile che si stia verificando un problema di attesa della concessione di memoria.

### <a name="determine-if-a-resourcesemahpore-wait-is-a-top-wait"></a>Determinare se un'attesa `RESOURCE_SEMAHPORE` è una delle attese più frequenti

Usare la query seguente per determinare se un'attesa `RESOURCE_SEMAHPORE` è una delle attese più frequenti

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Identificare le istruzioni con utilizzo della memoria elevato

Usare la query seguente per identificare le istruzioni con utilizzo della memoria elevato:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identificare le 10 concessioni di memoria attive più frequenti

Usare la query seguente per identificare le 10 concessioni di memoria attive più frequenti:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Calcolo delle dimensioni del database e degli oggetti

La seguente query restituisce la dimensione del database in megabyte:

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

La query seguente restituisce le dimensioni dei singoli oggetti (in megabyte) nel database:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitoraggio delle connessioni

È possibile usare la visualizzazione [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) per recuperare informazioni sulle connessioni stabilite con il server del database SQL specifico di Azure e i dettagli di ogni connessione. Inoltre, la visualizzazione [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) è utile durante il recupero di informazioni su tutte le connessioni utente attive e le attività interne.
La query seguente recupera le informazioni sulla connessione corrente.

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Quando si eseguono **sys.dm_exec_requests** e **sys.dm_exec_sessions**, se si dispone di un'autorizzazione **VIEW DATABASE STATE** sul database, saranno visibili tutte le sessioni in esecuzione sul database; in caso contrario, sarà visibile solo la sessione corrente.

## <a name="monitor-resource-use"></a>Monitorare l'uso delle risorse

È possibile monitorare l'utilizzo di risorse usando [Informazioni dettagliate prestazioni query del Database SQL](sql-database-query-performance.md) e [Archivio query](https://msdn.microsoft.com/library/dn817826.aspx).

È inoltre possibile monitorare l'utilizzo tramite queste due viste:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats

È possibile usare la vista [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) in ogni database SQL. La vista **sys.dm_db_resource_stats** mostra i dati recenti sull'uso delle risorse rispetto al livello di servizio. Le percentuali medie relative a CPU, I/O dei dati, scritture nei log e memoria vengono registrate ogni 15 secondi e vengono mantenute per un'ora.

Poiché questa vista fornisce una visione più granulare sull'uso delle risorse, usare prima **sys.dm_db_resource_stats** per eventuali analisi o risoluzioni di problemi allo stato corrente. Ad esempio, questa query descrive l'uso medio e massimo delle risorse per il database corrente nell'ultima ora:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Per altre query, vedere gli esempi in [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats

La vista [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nel database **master** fornisce informazioni aggiuntive utili per il monitoraggio dell'uso delle prestazioni del database SQL ai relativi livello di servizio e dimensione di calcolo. I dati vengono raccolti ogni 5 minuti e conservati per circa 14 giorni. Questa vista è utile per analisi cronologiche a lungo termine dell'uso delle risorse del database SQL.

Il grafico seguente illustra l'uso di risorse della CPU per un database Premium con dimensione di calcolo P2 per ogni ora nell'arco di una settimana. Questo grafico inizia di lunedì, con 5 giorni lavorativi e un fine settimana in cui l'uso di risorse nell'applicazione è molto inferiore.

![Uso delle risorse del database SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

In base ai dati, per la dimensione di calcolo P2 il carico massimo della CPU di questo database attualmente supera di poco il 50% dell'uso della CPU (a mezzogiorno di martedì). Se la CPU è il fattore più importante nel profilo delle risorse dell'applicazione, si può scegliere P2 come dimensione di calcolo idonea a garantire che il carico di lavoro sia sempre adeguato. Se si prevede che un'applicazione presenti un incremento nel tempo, è consigliabile avere un buffer di risorse aggiuntivo, in modo che l'applicazione non raggiunga mai il limite del livello di prestazioni. Se si aumenta la dimensione di calcolo, è possibile evitare gli errori visibili ai clienti che si possono verificare se un database non ha risorse sufficienti per elaborare in modo efficiente le richieste, in particolare in ambienti sensibili alla latenza. Un esempio è costituito da un database che supporta un'applicazione per la creazione di pagine Web in base ai risultati delle chiamate al database.

Altri tipi di applicazioni possono interpretare in modo diverso lo stesso grafico. Se ad esempio un'applicazione prova a elaborare i dati del libro paga ogni giorno e usa lo stesso grafico, questo tipo di modello di processo batch potrebbe essere eseguito correttamente con una dimensione di calcolo P1. Il valore di DTU della dimensione di calcolo P1 è pari a 100, mentre quello della dimensione di calcolo P2 è pari a 200. Il livello di prestazioni fornito dalla dimensione di calcolo P2 è doppio rispetto a quello fornito dalla dimensione di calcolo P1. Il 50% dell'uso della CPU nel livello P2 equivale quindi al 100% dell'uso della CPU in P1. Se l'applicazione non presenta timeout, è possibile che non sia rilevante se il completamento di un processo richiede 2 ore o 2,5 ore, se viene completato in giornata. Per un'applicazione che rientra in questa categoria è probabilmente sufficiente usare la dimensione di calcolo P1. Si può sfruttare la presenza di periodi di tempo durante il giorno in cui l'uso delle risorse è inferiore, in modo da spalmare un picco massimo in altri momenti nel corso della giornata. La dimensione di calcolo P1 può essere ottimale per questo tipo di applicazione e può consentire di limitare i costi, purché i processi vengano completati in orario ogni giorno.

Il database SQL di Azure espone le informazioni sulla risorsa usata per ogni database attivo nella vista **sys.resource_stats** del database **master** in ogni server. I dati nella tabella vengono aggregati per intervalli di 5 minuti. Con i livelli di servizio Basic, Standard e Premium, è possibile che la visualizzazione dei dati nella tabella richieda più di 5 minuti, quindi i dati risultano più utili per le analisi cronologiche, invece che per le analisi in tempo quasi reale. Eseguire una query nella vista **sys.resource_stats** per visualizzare la cronologia recente di un database e per verificare se la prenotazione scelta ha offerto le prestazioni desiderate quando necessario.

> [!NOTE]
> È necessario essere connessi al database **master** del server di database SQL per eseguire la query **sys.resource_stats** negli esempi seguenti.

Questo esempio illustra la modalità di esposizione dei dati in questa vista:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Vista del catalogo sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

L'esempio successivo mostra i diversi modi in cui è possibile usare la vista del catalogo **sys.resource_stats** per ottenere informazioni sul modo in cui il database SQL usa le risorse:

1. Per esaminare l'uso delle risorse nella settimana precedente per il database userdb1, è possibile eseguire questa query:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Per verificare l'idoneità del carico di lavoro per la dimensione di calcolo, è necessario eseguire il drill-down in ogni aspetto delle metriche delle risorse, ovvero CPU, operazioni di lettura e scrittura, numero di thread di lavoro e numero di sessioni. Ecco la query modificata usando**sys.resource_stats** per segnalare i valori medi e massimi di queste metriche delle risorse:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Con queste informazioni sui valori medi e massimi di ogni metrica delle risorse è possibile valutare l'idoneità della dimensione di calcolo scelta in rapporto al carico di lavoro. I valori medi di **sys.resource_stats** offrono in genere una buona baseline da usare nelle dimensioni di destinazione. Deve trattarsi dello strumento di misurazione principale. È ad esempio possibile che si usi il livello di servizio Standard con la dimensione di calcolo S2. Le percentuali medie di uso per la CPU e per le operazioni I/O di scrittura e lettura sono inferiori al 40%, il numero medio di ruoli di lavoro è inferiore a 50 e il numero medio di sessioni è inferiore a 200. Il carico di lavoro potrebbe essere idoneo per la dimensione di calcolo S1. È facile verificare se il database rientra nei limiti dei thread di lavoro e delle sessioni. Per verificare se un database può rientrare in una dimensione di calcolo inferiore prendendo in considerazione la CPU e le operazioni di lettura e scrittura, è sufficiente dividere il numero di DTU della dimensione di calcolo inferiore per il numero di DTU della dimensione di calcolo corrente e moltiplicare il risultato per 100:

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    Il risultato rappresenta la differenza di prestazioni relativa, in percentuale, tra le due dimensioni di calcolo. Se l'uso delle risorse non supera questa quantità, il carico di lavoro potrebbe essere idoneo per la dimensione di calcolo inferiore. È tuttavia necessario esaminare anche tutti gli intervalli dei valori di uso delle risorse e determinare, a livello di percentuale, la frequenza con cui il carico di lavoro del database può rientrare nella dimensione di calcolo inferiore. La query seguente genera questa percentuale per ogni dimensione di risorsa, in base alla soglia del 40% calcolata in questo esempio:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    In base livello di servizio del database, è possibile stabilire se il carico di lavoro può rientrare nella dimensione di calcolo inferiore. Se l'obiettivo del carico di lavoro del database è 99,9% e la query precedente restituisce valori superiori al 99,9% per tutte e tre le dimensioni della risorsa, è probabile che il carico di lavoro possa rientrare nella dimensione di calcolo inferiore.

    La percentuale calcolata in precedenza consente inoltre di stabilire se è opportuno usare la dimensione di calcolo superiore a quella attualmente in uso per soddisfare l'obiettivo. Ad esempio, userdb1 mostra il valore di uso della CPU seguente per la settimana precedente:

   | Percentuale CPU media | Percentuale CPU massima |
   | --- | --- |
   | 24,5 |100,00 |

    L'uso medio della CPU corrisponde a circa un quarto del limite della dimensione di calcolo, che potrebbe rientrare nella dimensione di calcolo del database. Il valore massimo corrisponde tuttavia al limite della dimensione di calcolo del database. È necessario passare alla dimensione di calcolo superiore a quella attualmente in uso? Considerare il numero di volte in cui il carico di lavoro raggiunge il 100% e quindi confrontare tale numero con l'obiettivo del carico di lavoro del database.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Se questa query restituisce un valore inferiore a 99,9% per qualsiasi delle tre dimensioni della risorsa, è consigliabile prendere in esame la possibilità di passare alla dimensione di calcolo superiore o usare tecniche di ottimizzazione dell'applicazione per ridurre il carico nel database SQL.

4. Questo esercizio tiene in considerazione anche l'aumento di carico di lavoro previsto in futuro.

Per i pool elastici, è possibile monitorare i singoli database nel pool con le tecniche descritte in questa sezione. Tuttavia, è anche possibile monitorare il pool nel complesso. Per informazioni, vedere [Monitorare e gestire un elastico](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Numero massimo di richieste simultanee

Per visualizzare il numero di richieste simultanee, eseguire questa query Transact-SQL sul database SQL:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

Per analizzare il carico di lavoro di un database SQL locale, modificare questa query in modo che applichi il filtro al database specifico da analizzare. Se ad esempio è presente un database locale denominato MyDatabase, questa query Transact-SQL restituirà il numero di richieste simultanee in tale database:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

Questo è solo uno snapshot in un singolo punto nel tempo. Per una migliore comprensione del carico di lavoro e dei requisiti relativi alle richieste simultanee, sarà necessario raccogliere molti campioni nel tempo.

### <a name="maximum-concurrent-logins"></a>Numero massimo di accessi simultanei

Per avere un'idea della frequenza degli accessi, è possibile analizzare i modelli dell'utente e dell'applicazione. È inoltre possibile eseguire carichi reali in un ambiente di test per assicurarsi di non raggiungere questo o gli altri limiti descritti in questo argomento. Non è disponibile alcuna vista di query singola o vista a gestione dinamica per la visualizzazione dei numeri o della cronologia degli accessi simultanei.

Se più client usano la stessa stringa di connessione, il servizio autentica ogni account di accesso. Se 10 utenti si connettono contemporaneamente a un database con nome utente e password identici, ci saranno dieci account di accesso simultanei. Questo limite si applica solo alla durata dell'account di accesso e dell'autenticazione. Se gli stessi 10 utenti si connettono in sequenza al database, il numero di account di accesso simultanei non sarà mai superiore a uno.

> [!NOTE]
> Questo limite attualmente non si applica ai database in pool elastici.

### <a name="maximum-sessions"></a>Numero massimo di sessioni

Per visualizzare il numero di sessioni attive correnti, eseguire questa query Transact-SQL sul database SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Se si analizza un carico di lavoro di SQL Server locale, modificare la query per concentrarsi su un database specifico. Questa query consente di determinare le possibili esigenze della sessione per tale database se si sta prendendo in considerazione lo spostamento nel database SQL di Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Queste query restituiscono un conteggio temporizzato. Se si raccolgono più campioni nel tempo, si otterrà una comprensione ottimale dell'uso della sessione.

Per l'analisi del database SQL, è possibile anche ottenere dati statistici cronologici sulle sessioni eseguendo query della visualizzazione [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ed esaminando la colonna **active_session_count**.

## <a name="monitoring-query-performance"></a>Monitoraggio delle prestazioni delle query

L’esecuzione della query rallentata o prolungata può consumare delle risorse di sistema importanti. In questa sezione viene illustrato come utilizzare le visualizzazioni a gestione dinamica per rilevare alcuni problemi di prestazione delle query comuni. Un riferimento precedente ma comunque utile per la risoluzione dei problemi, è l’articolo [Risoluzione dei problemi di prestazioni in SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) su Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Ricerca delle prime n query

Nell'esempio seguente vengono restituite informazioni sulle prime cinque query classificate in base al tempo medio della CPU. Nell'esempio le query vengono aggregate in base al relativo valore hash, in modo da raggruppare le query logicamente equivalenti in base all'utilizzo di risorse cumulativo.

    ```sql
    SELECT TOP 5 query_stats.query_hash AS "Query Hash",
       SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
       MIN(query_stats.statement_text) AS "Statement Text"
    FROM
       (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
        ((CASE statement_end_offset
           WHEN -1 THEN DATALENGTH(ST.text)
           ELSE QS.statement_end_offset END
           - QS.statement_start_offset)/2) + 1) AS statement_text
    FROM sys.dm_exec_query_stats AS QS
    CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
    GROUP BY query_stats.query_hash
    ORDER BY 2 DESC;
    ```

### <a name="monitoring-blocked-queries"></a>Monitoraggio delle query bloccate

Le query lente o con esecuzione prolungata possono contribuire al consumo eccessivo delle risorse ed essere la conseguenza di query bloccate. Le cause del blocco possono essere una progettazione povera dell'applicazione, dei piani di query non validi, la mancanza di indici utili e così via. È possibile utilizzare la visualizzazione in sys.dm_tran_locks per ottenere informazioni sulle attività di blocco corrente nel Database di SQL Azure. Per codice di esempio, vedere [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) nella documentazione online di Microsoft SQL Server.

### <a name="monitoring-query-plans"></a>Monitoraggio dei piani di query

Un piano di query inefficiente può anche aumentare il consumo della CPU. Nell'esempio seguente viene usata la visualizzazione [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) per determinare la query che usa la CPU cumulativa maggiore.

    ```sql
    SELECT
       highest_cpu_queries.plan_handle,
       highest_cpu_queries.total_worker_time,
       q.dbid,
       q.objectid,
       q.number,
       q.encrypted,
       q.[text]
    FROM
       (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
    ORDER BY highest_cpu_queries.total_worker_time DESC;
    ```

## <a name="see-also"></a>Vedere anche 

[Introduzione al Database SQL](sql-database-technical-overview.md)
