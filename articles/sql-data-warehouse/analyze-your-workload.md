---
title: Analizzare il carico di lavoro in Azure SQL Data Warehouse | Microsoft Docs
description: "Tecniche per l'analisi della definizione della priorità delle query per il carico di lavoro in Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: 98617f6b8366662e52d00420adc4c81abffc598d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="analyze-your-workload"></a>Analizzare il carico di lavoro
Tecniche per l'analisi della definizione della priorità delle query per il carico di lavoro in Azure SQL Data Warehouse.

## <a name="workload-groups"></a>Gruppi di carichi di lavoro 
SQL Data Warehouse implementa le classi di risorse utilizzando i gruppi del carico di lavoro. Esistono in totale otto gruppi di carichi di lavoro che controllano il comportamento delle classi di risorse nelle DWU di varie dimensioni. Per qualsiasi DWU, SQL Data Warehouse usa solo quattro degli otto gruppi del carico di lavoro. Il senso di questo approccio è assegnare a ogni gruppo di carichi di lavoro una delle quattro classi di risorse, tra smallrc, mediumrc, largerc o xlargerc. È importante comprendere per alcuni gruppi di carichi di lavoro viene impostato il livello di *priorità*più elevato. La priorità viene usata per la pianificazione della CPU. Le query eseguite con priorità alta otterranno tre volte più cicli della CPU rispetto a quelle con priorità media. Di conseguenza, i mapping degli slot della concorrenza determinano anche la priorità della CPU. Quando una query utilizza 16 o più slot, viene eseguita con priorità alta.

Nella tabella seguente vengono riportati i mapping di priorità per ogni gruppo di carichi di lavoro.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapping dei gruppi di carichi di lavoro agli slot di concorrenza e relativa importanza

| Gruppi di carichi di lavoro | Mapping degli slot di concorrenza | MB / Distribuzione (elasticità) | MB / Distribuzione (calcolo) | Mapping delle priorità |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Media             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Media             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | Media             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Media             |
| SloDWGroupC04   | 16                       |  1.600                         | 4000                        | Alto               |
| SloDWGroupC05   | 32                       |  3.200                         | 8000                        | Alto               |
| SloDWGroupC06   | 64                       |  6.400                         | 16.000                      | Alto               |
| SloDWGroupC07   | 128                      | 12.800                         | 32.000                      | Alto               |
| SloDWGroupC08   | 256                      | 25.600                         | 64.000                      | Alto               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
Come illustrato nel grafico **Allocation and consumption of concurrency slots** (Allocazione e utilizzo degli slot di concorrenza), DW500 utilizza 1, 4, 8 o 16 slot di concorrenza per smallrc, mediumrc, largerc e xlargerc rispettivamente. È possibile cercare questi valori nel grafico precedente per identificare la priorità di ciascuna classe di risorse.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapping dell'importanza delle classi di risorse in DW500
| classe di risorse | Gruppo del carico di lavoro | Numero di slot di concorrenza usati | MB/Distribuzione | priorità |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Media     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | Media     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Media     |
| xlargerc       | SloDWGroupC04  | 16                     | 1.600             | Alto       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Media     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Media     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | Media     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Media     |
| staticrc50     | SloDWGroupC03  | 16                     | 1.600             | Alto       |
| staticrc60     | SloDWGroupC03  | 16                     | 1.600             | Alto       |
| staticrc70     | SloDWGroupC03  | 16                     | 1.600             | Alto       |
| staticrc80     | SloDWGroupC03  | 16                     | 1.600             | Alto       |

## <a name="view-workload-groups"></a>Visualizzare i gruppi di carichi di lavoro
Per esaminare in dettaglio le differenze nell'allocazione delle risorse di memoria dal punto di vista di resource governor o per analizzare l'utilizzo attivo e cronologico dei gruppi di carichi di lavoro in caso di risoluzione dei problemi, è possibile usare la query DMV seguente:

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>Rilevamento di query in coda e altre viste a gestione dinamica
È possibile utilizzare la DMV `sys.dm_pdw_exec_requests` per identificare le query in attesa in una coda di concorrenza. Le query in attesa di uno slot di concorrenza saranno in stato **sospeso**.

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

* **LocalQueriesConcurrencyResourceType**: query che si trovano all'esterno del framework di slot di concorrenza. Le query DMV e le funzioni di sistema  come `SELECT @@VERSION` sono esempi di query locali.
* **UserConcurrencyResourceType**: query che si trovano all'interno del framework di slot di concorrenza. Le query sulle tabelle dell'utente finale rappresentano esempi in cui si usa questo tipo di risorsa.
* **DmsConcurrencyResourceType**: attese risultanti dalle operazioni di spostamento dei dati.
* **BackupConcurrencyResourceType**: indica che è in esecuzione il backup di un database. Il valore massimo per questo tipo di risorsa è 1. Se più copie di backup sono stati richieste contemporaneamente, le altre verranno accodate.

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

La DMV `sys.dm_pdw_resource_waits` mostra solo le attese di risorse utilizzate da una determinata query. Il tempo in attesa delle risorse misura solo il tempo richiesto per fornire le risorse, a differenza del tempo di attesa del segnale che rappresenta il tempo impiegato dal server SQL Server sottostante per pianificare la query sulla CPU.

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
Per altre informazioni sulla gestione degli utenti e della sicurezza del database, vedere [Proteggere un database in SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Per ulteriori informazioni sulle classi di risorse più grandi che possono migliorare le qualità degli indici indice columnstore cluster, vedere [Ricompilazione degli indici per migliorare la qualità del segmento].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Ricompilazione degli indici per migliorare la qualità del segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
