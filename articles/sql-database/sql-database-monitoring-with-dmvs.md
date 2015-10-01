<properties
   pageTitle="Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica | Microsoft Azure"
   description="Informazioni su come rilevare e diagnosticare i problemi di prestazioni comuni utilizzando visualizzazioni a gestione dinamica per monitorare il Database SQL di Microsoft Azure."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/15/2015"
   ms.author="rickbyh"/>

# Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica

Il database SQL di Microsoft Azure consente a un sottoinsieme di visualizzazioni a gestione dinamica di diagnosticare i problemi delle prestazioni che potrebbero essere causati da query bloccate o con esecuzione prolungata, colli di bottiglia delle risorse, piani di query insufficienti e così via. In questo argomento vengono fornite informazioni su come rilevare problemi di prestazioni comuni utilizzando visualizzazioni a gestione dinamica.

Il database SQL supporta parzialmente tre categorie di visualizzazioni a gestione dinamica:

- Visualizzazioni a gestione dinamica relative al database.
- Visualizzazioni a gestione dinamica relative all'esecuzione. 
- Visualizzazioni a gestione dinamica relative alle transazioni. 

Per informazioni dettagliate sulle visualizzazioni a gestione dinamica, vedere [Visualizzazioni a gestione dinamica e funzioni (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) nella documentazione Online di SQL Server.

## Autorizzazioni

Nel Database SQL, l'esecuzione di query in una visualizzazione a gestione dinamica richiede autorizzazioni **VIEW DATABASE STATE**. Le autorizzazioni **VIEW DATABASE STATE** restituiscono informazioni su tutti gli oggetti all'interno del database corrente. Per concedere le autorizzazioni **VIEW DATABASE STATE** a un utente di database specifico, eseguire la query seguente:

```GRANT VIEW DATABASE STATE TO database_user; ```

In an instance of on-premises SQL Server, dynamic management views return server state information. In SQL Database, they return information regarding your current logical database only.

## Calculating database size

The following query returns the size of your database (in megabytes):

```
-- Calcola le dimensioni del database. SELECT SUM(reserved\_page\_count)*8.0/1024 FROM sys.dm\_db\_partition\_stats; GO 
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

## Monitoraggio delle connessioni

È possibile utilizzare la visualizzazione [sys.dm\_exec\_connections](https://msdn.microsoft.com/library/ms181509.aspx) per recuperare informazioni sulle connessioni stabilite con il server del database SQL specifico di Azure e i dettagli di ogni connessione. Inoltre, la visualizzazione [sys.dm\_exec\_sessions](https://msdn.microsoft.com/library/ms176013.aspx) è utile durante il recupero di informazioni su tutte le connessioni utente attive e le attività interne. La query seguente recupera le informazioni sulla connessione corrente.

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

> [AZURE.NOTE]Quando si eseguono le visualizzazioni **sys.dm\_exec\_requests** e **sys.dm\_exec\_sessions**, se l'utente dispone di un’autorizzazione**VIEW DATABASE STATE** sul database, l'utente vedrà tutte le sessioni in esecuzione sul database; in caso contrario, l'utente vedrà solo la sessione corrente.

## Monitoraggio delle prestazioni delle query

L’esecuzione della query rallentata o prolungata può consumare delle risorse di sistema importanti. In questa sezione viene illustrato come utilizzare le visualizzazioni a gestione dinamica per rilevare alcuni problemi di prestazione delle query comuni. Un riferimento precedente ma comunque utile per la risoluzione dei problemi, è l’articolo [Risoluzione dei problemi di prestazioni in SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) su Microsoft TechNet.

### Ricerca delle prime n query

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

### Monitoraggio delle query bloccate

Le query lente o con esecuzione prolungata possono contribuire al consumo eccessivo delle risorse ed essere la conseguenza di query bloccate. Le cause del blocco possono essere una progettazione povera dell'applicazione, dei piani di query non validi, la mancanza di indici utili e così via. È possibile utilizzare la visualizzazione in sys.dm\_tran\_locks per ottenere informazioni sulle attività di blocco corrente nel Database di SQL Azure. Per un codice di esempio, vedere [sys.dm\_tran\_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) nella documentazione Online di SQL Server.

### Monitoraggio dei piani di query

Un piano di query inefficiente può anche aumentare il consumo della CPU. Nell'esempio seguente viene utilizzata la visualizzazione [sys.dm\_exec\_query\_stats](https://msdn.microsoft.com/library/ms189741.aspx) per determinare quali query utilizza la CPU cumulativa maggiore.

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

## Vedere anche

[Introduzione al Database SQL](sql-database-technical-overview.md)

<!----HONumber=Sept15_HO3-->