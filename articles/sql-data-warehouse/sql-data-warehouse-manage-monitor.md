<properties
   pageTitle="Monitoraggio del carico di lavoro mediante DMV | Microsoft Azure"
   description="Informazioni sul monitoraggio del carico di lavoro mediante DMV."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="sahajs"/>

# Monitoraggio del carico di lavoro mediante DMV

In questo articolo viene descritto come utilizzare le viste a gestione dinamica (DMV) per monitorare il carico di lavoro ed esaminare l'esecuzione delle query SQL Data Warehouse di Azure.



## Monitoraggio delle connessioni

È possibile utilizzare la vista *sys.dm\_pdw\_nodes\_exec\_connections* per recuperare informazioni sulle connessioni stabilite con il database SQL Data Warehouse di Azure. Inoltre, la vista *sys.dm\_exec\_sessions* è utile durante il recupero di informazioni su tutte le connessioni utente attive.

```

SELECT * FROM sys.dm_pdw_nodes_exec_connections;
SELECT * FROM sys.dm_pdw_nodes_exec_sessions;

```


Utilizzare la query seguente per recuperare le informazioni sulla connessione corrente.

```

SELECT * 
FROM sys.dm_pdw_nodes_exec_connections AS c 
   JOIN sys.dm_pdw_nodes_exec_sessions AS s 
   ON c.session_id = s.session_id 
WHERE c.session_id = @@SPID;

```





## Analisi dell'esecuzione di query
Potrebbero verificarsi situazioni in cui la query non viene completata o viene eseguita in tempi più lunghi del previsto. In questi casi è possibile utilizzare i passaggi seguenti per raccogliere dati e circoscrivere il problema.



### PASSAGGIO 1: individuare la query da analizzare

```

-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the longest running queries
SELECT * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;

```

Salvare l'ID richiesta della query.


  
### PASSAGGIO 2: verificare se la query è in attesa di risorse

```

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


I risultati della query precedente indicano lo stato di attesa della richiesta.

- Se la query è in attesa di risorse da un'altra query, lo stato sarà **AcquireResources**.
- Se la query dispone di tutte le risorse necessarie e non è in attesa, lo stato sarà **Granted**. In questo caso, esaminare le istruzioni della query.




### PASSAGGIO 3: individuare l'istruzione della query eseguita più lentamente

Utilizzare l'ID richiesta per recuperare un elenco di tutte le istruzioni della query distribuite. Individuare l'istruzione eseguita in tempi prolungati a osservando il tempo trascorso totale.

```

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

Utilizzare l'ID richiesta e l'indice dell'istruzione per recuperare informazioni sulla distribuzione di query di SQL Server come parte dell'istruzione SQL nella query. Salvare l'ID nodo e lo SPID.

```

-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;

```


Utilizzare la query seguente per recuperare il piano di esecuzione di SQL Server per l'istruzione SQL su un dato nodo.

```

-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node. 
-- Replace node_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```



### PASSAGGIO 4b: individuare lo stato di esecuzione di un passaggio del DMS

Utilizzare l'ID richiesta e l'indice dell'istruzione per recuperare informazioni sull'istruzione relativa allo spostamento dati in esecuzione in ciascuna distribuzione.

```

-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.
 
SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Controllare la colonna *total\_elapsed\_time* per verificare se una distribuzione particolare richiede più tempo per lo spostamento dati rispetto alle altre. 
- Per la distribuzione con esecuzione prolungata, esaminare la colonna *rows\_processed* e controllare se il numero di righe spostato da tale distribuzione è significativamente più grande rispetto alle altre. Ciò indica che la query presenta una differenza di dati.





## Analisi della differenza di dati

```

-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");

```


Il risultato di questa query indicherà il numero di righe della tabella archiviato in ciascuna delle 60 distribuzioni del database. Per prestazioni ottimali, le righe nella tabella distribuita devono essere assegnate in modo uniforme a tutte le distribuzioni. Per ulteriori informazioni, vedere [struttura tabella][].



## Passaggi successivi
Per ulteriori suggerimenti sulla gestione di SQL Data Warehouse, vedere [panoramica della gestione][].

<!--Image references-->

<!--Article references-->
[panoramica della gestione]: sql-data-warehouse-overview-manage.md
[struttura tabella]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!---HONumber=Oct15_HO3-->