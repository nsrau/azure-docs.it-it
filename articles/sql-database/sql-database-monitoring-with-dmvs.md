---
title: Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica | Documentazione Microsoft
description: Informazioni su come rilevare e diagnosticare i problemi di prestazioni comuni utilizzando visualizzazioni a gestione dinamica per monitorare il Database SQL di Microsoft Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 8185e2748f4aeec4343fa649d8b54958cdcb262a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica
Il database SQL di Microsoft Azure consente a un sottoinsieme di visualizzazioni a gestione dinamica di diagnosticare i problemi delle prestazioni che potrebbero essere causati da query bloccate o con esecuzione prolungata, colli di bottiglia delle risorse, piani di query insufficienti e così via. Questo argomento fornisce informazioni su come rilevare problemi comuni relativi alle prestazioni tramite le DMV.

Il database SQL supporta parzialmente tre categorie di visualizzazioni a gestione dinamica:

* Visualizzazioni a gestione dinamica relative al database.
* Visualizzazioni a gestione dinamica relative all'esecuzione.
* Visualizzazioni a gestione dinamica relative alle transazioni.

Per informazioni dettagliate sulle visualizzazioni a gestione dinamica, vedere [Visualizzazioni a gestione dinamica e funzioni (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) nella documentazione Online di SQL Server.

## <a name="permissions"></a>Autorizzazioni
Nel Database SQL, l'esecuzione di query in una visualizzazione a gestione dinamica richiede autorizzazioni **VIEW DATABASE STATE** . Le autorizzazioni **VIEW DATABASE STATE** restituiscono informazioni su tutti gli oggetti all'interno del database corrente.
Per concedere le autorizzazioni **VIEW DATABASE STATE** a un utente di database specifico, eseguire la query seguente:

```GRANT VIEW DATABASE STATE TO database_user; ```

In un'istanza di SQL Server locale, le viste a gestione dinamica restituiscono informazioni sullo stato del server. Nel database SQL, restituiscono informazioni relative esclusivamente al database logico corrente.

## <a name="calculating-database-size"></a>Calcolo della dimensione del database
La seguente query restituisce la dimensione del database in megabyte:

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

La query seguente restituisce le dimensioni dei singoli oggetti (in megabyte) nel database:

```
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

```
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
> 
> 

## <a name="monitoring-query-performance"></a>Monitoraggio delle prestazioni delle query
L’esecuzione della query rallentata o prolungata può consumare delle risorse di sistema importanti. In questa sezione viene illustrato come utilizzare le visualizzazioni a gestione dinamica per rilevare alcuni problemi di prestazione delle query comuni. Un riferimento precedente ma comunque utile per la risoluzione dei problemi, è l’articolo [Risoluzione dei problemi di prestazioni in SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) su Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Ricerca delle prime n query
Nell'esempio seguente vengono restituite informazioni sulle prime cinque query classificate in base al tempo medio della CPU. Nell'esempio le query vengono aggregate in base al relativo valore hash, in modo da raggruppare le query logicamente equivalenti in base all'utilizzo di risorse cumulativo.

```
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
Le query lente o con esecuzione prolungata possono contribuire al consumo eccessivo delle risorse ed essere la conseguenza di query bloccate. Le cause del blocco possono essere una progettazione povera dell'applicazione, dei piani di query non validi, la mancanza di indici utili e così via. È possibile utilizzare la visualizzazione in sys.dm_tran_locks per ottenere informazioni sulle attività di blocco corrente nel Database di SQL Azure. Per un codice di esempio, vedere [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) nella 	documentazione online di Microsoft SQL Server.

### <a name="monitoring-query-plans"></a>Monitoraggio dei piani di query
Un piano di query inefficiente può anche aumentare il consumo della CPU. Nell'esempio seguente viene usata la visualizzazione [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) per determinare la query che usa la CPU cumulativa maggiore.

```
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

