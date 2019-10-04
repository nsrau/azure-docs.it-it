---
title: Analizzare il carico di lavoro in Azure SQL Data Warehouse | Microsoft Docs
description: Tecniche per l'analisi della definizione della priorità delle query per il carico di lavoro in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 54652ba573fb2ec2d064b7a85ad5728b73e71db3
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588740"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Analizzare il carico di lavoro in Azure SQL Data Warehouse

Tecniche per analizzare il carico di lavoro in Azure SQL Data Warehouse.

## <a name="resource-classes"></a>Classi di risorse

SQL Data Warehouse fornisce le classi di risorse per assegnare le risorse di sistema per le query.  Per altre informazioni sulle classi di risorse, vedere [gestione delle classi di & carico di lavoro risorse](resource-classes-for-workload-management.md).  Le query rimarrà in attesa se la classe di risorse assegnata a una query richiede più risorse rispetto a quelli attualmente disponibili.

## <a name="queued-query-detection-and-other-dmvs"></a>Rilevamento di query in coda e altre viste a gestione dinamica

È possibile utilizzare la DMV `sys.dm_pdw_exec_requests` per identificare le query in attesa in una coda di concorrenza. Le query in attesa di uno slot di concorrenza sono in stato **sospeso**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

I ruoli di gestione del carico di lavoro possono essere visualizzati con `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

La query seguente mostra il ruolo a cui è assegnato ogni utente.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse prevede i tipi di attesa seguenti.

* **LocalQueriesConcurrencyResourceType**: query che si trovano all'esterno del framework degli slot di concorrenza. Le query DMV e le funzioni di sistema  come `SELECT @@VERSION` sono esempi di query locali.
* **UserConcurrencyResourceType**: query che si trovano all'interno del framework degli slot di concorrenza. Le query sulle tabelle dell'utente finale rappresentano esempi in cui si usa questo tipo di risorsa.
* **DmsConcurrencyResourceType**: attese risultanti dalle operazioni di spostamento dei dati.
* **BackupConcurrencyResourceType**: questa attesa indica che è in esecuzione il backup di un database. Il valore massimo per questo tipo di risorsa è 1. Se sono stati richiesti più backup contemporaneamente, gli altri vengono accodati. In generale, è consigliabile un tempo minimo tra snapshot consecutivi di 10 minuti. 

La DMV `sys.dm_pdw_waits` può essere usata per visualizzare le risorse per cui una richiesta è in attesa.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

Il `sys.dm_pdw_resource_waits` DMV Mostra le informazioni di attesa per una determinata query. Risorsa attesa misure ora il tempo di attesa per le risorse devono essere fornite. Tempo di attesa del segnale è il tempo che necessario per il server SQL Server sottostante pianificare la query sulla CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

È anche possibile usare la DMV `sys.dm_pdw_resource_waits` per calcolare quanti slot di concorrenza sono stati concessi.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

L DMV `sys.dm_pdw_wait_stats` può essere usata per l'analisi della tendenza cronologica delle attese.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla gestione degli utenti e della sicurezza del database, vedere [Proteggere un database in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md). Per ulteriori informazioni sulle classi di risorse più grandi che possono migliorare le qualità degli indici indice columnstore cluster, vedere [Ricompilazione degli indici per migliorare la qualità del segmento](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
