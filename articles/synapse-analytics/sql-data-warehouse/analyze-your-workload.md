---
title: Analizzare il carico di lavoro
description: Tecniche per l'analisi della priorità delle query per il carico di lavoro in Azure sinapsi Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c547263be8c61d75491d1517b58c03b6365ef929
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208400"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>Analizzare il carico di lavoro in Azure sinapsi Analytics

Tecniche per l'analisi del carico di lavoro SQL sinapsi in Azure sinapsi Analytics. 

## <a name="resource-classes"></a>Classi di risorse

Sinapsi SQL fornisce le classi di risorse per assegnare le risorse di sistema alle query.  Per altre informazioni sulle classi di risorse, vedere [classi di risorse & gestione del carico di lavoro](resource-classes-for-workload-management.md).  Le query attendono se la classe di risorse assegnata a una query richiede più risorse rispetto a quelle attualmente disponibili.

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

Il tipo di attesa di sinapsi SQL è il seguente:

* **LocalQueriesConcurrencyResourceType**: query che si trovano all'esterno del framework di slot di concorrenza. Le query DMV e le funzioni di sistema  come `SELECT @@VERSION` sono esempi di query locali.
* **UserConcurrencyResourceType**: query che si trovano all'interno del framework di slot di concorrenza. Le query sulle tabelle dell'utente finale rappresentano esempi in cui si usa questo tipo di risorsa.
* **DmsConcurrencyResourceType**: attese risultanti dalle operazioni di spostamento dei dati.
* **BackupConcurrencyResourceType**: indica che è in esecuzione il backup di un database. Il valore massimo per questo tipo di risorsa è 1. Se sono stati richiesti più backup contemporaneamente, gli altri vengono accodati. In generale, è consigliabile un tempo minimo tra gli snapshot consecutivi di 10 minuti.

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

La `sys.dm_pdw_resource_waits` DMV Mostra le informazioni di attesa per una determinata query. Il tempo di attesa delle risorse misura il tempo necessario per fornire le risorse. Il tempo di attesa del segnale è il tempo necessario per i server SQL sottostanti per pianificare la query sulla CPU.

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

Per altre informazioni sulla gestione degli utenti e della sicurezza del database, vedere [Proteggere un database in Synapse SQL](sql-data-warehouse-overview-manage-security.md). Per ulteriori informazioni sulle classi di risorse più grandi che possono migliorare la qualità degli indici columnstore cluster, vedere [ricompilazione degli indici per migliorare la qualità dei segmenti](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
