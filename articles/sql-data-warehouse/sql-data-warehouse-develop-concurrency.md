<properties
   pageTitle="Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse | Microsoft Azure"
   description="Informazioni sulla gestione della concorrenza e del carico di lavoro nel Data Warehouse di SQL Azure per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse
Per garantire prestazioni stimabili in scala, SQL Data Warehouse implementa meccanismi per la gestione della concorrenza del carico di lavoro e dell’assegnazione delle risorse di calcolo.

In questo articolo vengono introdotti i concetti di gestione della concorrenza e del carico di lavoro, spiegando in che modo entrambe le funzionalità sono state implementate e come vengono controllate nel data warehouse.

## Concorrenza
È importante comprendere che la concorrenza in SQL Data Warehouse è disciplinata da due concetti: **query simultanee** e **slot di concorrenza**.

Le query simultanee equivalgono al numero di query in esecuzione nello stesso momento. SQL Data Warehouse supporta fino a 32 **query simultanee**. Ogni esecuzione di query viene considerata come una singola query sia che si tratti di una query seriale (thread singolo) o di una query parallela (multithread). Questo è un limite fisso e si applica a tutti i livelli di servizio.

Gli slot di concorrenza sono un concetto più dinamico e sono relativi all'obiettivo del livello di servizio Unità Data Warehouse (DWU) per il data warehouse. Quando si aumenta il numero di DWU allocate a SQL Data Warehouse, vengono assegnate più risorse di calcolo. Tuttavia, l’aumento delle DWU aumenta anche il numero di **slot di concorrenza** disponibili.

SQL Data Warehouse deve essere attivo entro le due soglie. Se sono presenti più di 32 query simultanee o si supera il numero di slot di concorrenza, la query verrà accodata fino a quando non vengono soddisfatte entrambe le soglie.

Ogni esecuzione simultanea di query utilizza uno o più slot di concorrenza. Il numero esatto di slot dipende da due fattori:

1. L'impostazione di DWU per SQL Data Warehouse
2. La **classe di risorse** a cui appartiene l'utente 

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| Utilizzo di slot di concorrenza | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | 
| Numero massimo di query simultanee | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 
| Numero massimo di slot di concorrenza | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 

Le classi di risorse sono una parte essenziale della gestione del carico di lavoro di SQL Data Warehouse in quanto gestiscono anche le risorse di calcolo allocate alla query. Verranno spiegate nella seguente sezione relativa alla gestione del carico di lavoro.

## Gestione del carico di lavoro

SQL Data Warehouse espone quattro classi di risorse diversi sotto forma di **ruoli del database** come parte dell’implementazione della gestione del carico di lavoro.

I ruoli sono:

- smallrc
- mediumrc
- largerc
- xlargerc

Per impostazione predefinita ogni utente è un membro della classe di risorse piccola (smallrc). Tuttavia, è possibile aggiungere qualsiasi utente a una o più classi di risorse più elevate. SQL Data Warehouse richiede l'appartenenza al ruolo più elevato per l'esecuzione di query. Aggiungendo un utente a una classe di risorse più elevata aumentano le risorse per l'utente, ma si utilizzano anche più slot di concorrenza, limitando potenzialmente la concorrenza. Ciò è dovuto al fatto che quando più risorse vengono allocate a una query, il sistema deve limitare le risorse utilizzate dagli altri utenti. Non si tratta di un colpo di fortuna.

La memoria è la risorsa più importante disciplinata dalla classe di risorse più elevata. La maggior parte delle tabelle del data warehouse di dimensione significativa utilizzerà gli indici columnstore cluster. Mentre in genere fornisce le migliori prestazioni per i carichi di lavoro di data warehouse, la loro gestione costituisce un'operazione con utilizzo intensivo della memoria. È spesso estremamente vantaggioso utilizzare le classi di risorse più elevate per le operazioni di gestione dati, ad esempio la ricostruzione dell'indice.

Per aumentare la memoria, aggiungere il database utente a uno dei ruoli/classi di risorse indicati in precedenza.

È possibile aggiungere e rimuovere l’utente corrente per il ruolo del database di gestione del carico di lavoro tramite le procedure `sp_addrolemember` e `sp_droprolemember`. Notare che è necessario disporre dell’autorizzazione `ALTER ROLE` per eseguire questa operazione. Non è possibile utilizzare la sintassi ALTER ROLE DDL. È necessario utilizzare le stored procedure menzionate in precedenza.

> [AZURE.NOTE]Invece di aggiungere o rimuovere un utente in un gruppo di gestione del carico di lavoro, è spesso più semplice avviare le operazioni più intensive tramite un accesso/utente separato che è assegnato in modo permanente alla classe di risorse più elevata.

Nella tabella seguente viene descritto l'aumento di memoria disponibile per ogni query, dipendentemente dalla classe di risorse applicata all'utente che la esegue:

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |
-->

| Memoria disponibile (per distribuzione) | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s) | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc (m) | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB |
| largerc (l) | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl) | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |

Inoltre, come indicato in precedenza, più alta è la classe di risorse assegnata all'utente maggiore è l’utilizzo di slot della concorrenza. Nella tabella seguente viene illustrato il consumo di slot della concorrenza da parte delle query in una classe di risorse specificata.

<!--
| Concurrency slot consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| Utilizzo di slot di concorrenza | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| Numero massimo di query simultanee | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Numero massimo di slot di concorrenza | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc(default) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

È importante ricordare che il carico di lavoro della query attiva deve rientrare nelle soglie di slot di concorrenza e di query simultanee. Una volta superata una soglia, la query viene accodata. Le query in coda verranno risolte in ordine di priorità in base all'ora di invio.

Dietro le quinte le cose sono un po' più complicate. Le classi di risorse sono mappate in modo dinamico a un set di gruppi di gestione del carico di lavoro all'interno dei carico generico. I gruppi utilizzati dipenderanno sul valore DWU per il warehouse. Tuttavia, esistono in totale otto gruppi di carico di lavoro utilizzati da SQL Data Warehouse. Sono:

- SloDWGroupC00
- SloDWGroupC01
- SloDWGroupC02
- SloDWGroupC03
- SloDWGroupC04
- SloDWGroupC05
- SloDWGroupC06
- SloDWGroupC07

Questi 8 gruppi mappano il consumo di slot della concorrenza

| Gruppo di carico | Mapping di Slot di concorrenza | Mapping di priorità |
| :------------  | :----------------------- | :--------------- |
| SloDWGroupC00 | 1 | Media |
| SloDWGroupC01 | 2 | Media |
| SloDWGroupC02 | 4 | Media |
| SloDWGroupC03 | 8 | Media |
| SloDWGroupC04 | 16 | Alto |
| SloDWGroupC05 | 32 | Alto |
| SloDWGroupC06 | 64 | Alto |
| SloDWGroupC07 | 128 | Alto |

Quindi, ad esempio, se DW500 è l'impostazione DWU corrente per il SQL Data Warehouse allora i gruppi del carico di lavoro attivo saranno associati alle classi di risorse come segue:

| Classe di risorse | Gruppo di carico | Numero di slot di concorrenza usate | Importance |
| :------------- | :------------- | :---------------------   | :--------- |
| smallrc | SloDWGroupC00 | 1 | Media |
| mediumrc | SloDWGroupC02 | 4 | Media |
| largerc | SloDWGroupC03 | 8 | Media |
| xlargerc | SloDWGroupC04 | 16 | Alto |

Per esaminare nel dettaglio le differenze nell'allocazione di risorse di memoria dalla prospettiva del resource governor utilizzare la seguente query:

```
WITH rg
AS
(   SELECT  pn.name									AS node_name
	,		pn.[type]								AS node_type
	,		pn.pdw_node_id							AS node_id
	,		rp.name									AS pool_name
    ,       rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,       wg.name									AS group_name
    ,       wg.importance							AS group_importance
    ,       wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,       wg.max_dop								AS group_max_dop
    ,       wg.effective_max_dop					AS group_effective_max_dop
	,		wg.total_request_count					AS group_total_request_count
	,		wg.total_queued_request_count			AS group_total_queued_request_count
	,		wg.active_request_count					AS group_active_request_count
	,		wg.queued_request_count					AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
															        AND wg.pool_id      = rp.pool_id
	JOIN	sys.dm_pdw_nodes pn										ON	wg.pdw_node_id	= pn.pdw_node_id
	WHERE   wg.name like 'SloDWGroup%'
	AND     rp.name = 'SloDWPool'
) 
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY 
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

> [AZURE.NOTE]La query precedente consente inoltre di analizzare l’utilizzo attivo e passato dei gruppi del carico di lavoro durante la risoluzione degli

## Esempi di gestione del carico di lavoro

Per concedere l'accesso a un utente al SQL Data Warehouse prima è necessario avere le credenziali di accesso.

Aprire una connessione al database master per il SQL Data Warehouse ed eseguire i comandi seguenti:

```
CREATE LOGIN newperson WITH PASSWORD = 'mypassword'

CREATE USER newperson for LOGIN newperson
```

[AZURE.NOTE]è consigliabile creare utenti per gli accessi nel database master per quando si lavora con database SQL Azure e SQL Data Warehouse. Su questo livello ci sono due ruoli disponibili che richiedono l'accesso per avere un utente nel database master per concedere l’appartenenza. I ruoli sono`Loginmanager` e `dbmanager`: Nel database SQL Azure e SQL Data Warehouse questi ruoli concedono diritti per gestire gli account di accesso e per creare database. Questo comportamento è diverso rispetto a SQL Server. Per ulteriori informazioni, vedere il[Gestione di database e gli account di accesso nel Database di SQL Azure]articolo per ulteriori informazioni.
 
Dopo aver creato l'account di accesso, è necessario aggiungere un account utente.

Aprire una connessione al database SQL Data Warehouse ed eseguire il comando seguente:

```
CREATE USER newperson FOR LOGIN newperson
```

Sarà necessario concedere all'utente dispone di autorizzazioni complete. L'esempio seguente concede`CONTROL`nel database SQL Data Warehouse.`CONTROL`nel database di livello è l'equivalente del ruolo db_owner in SQL Server.

```
GRANT CONTROL ON DATABASE::MySQLDW to newperson
```

Per visualizzare i ruoli di gestione del carico di lavoro ruoli utilizzare la query seguente:

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Per aggiungere un utente a un ruolo di gestione di aumento del carico di lavoro utilizzare la query seguente:

``` 
EXEC sp_addrolemember 'largerc', 'newperson' 
```

Per rimuovere un utente da un ruolo di gestione del carico di lavoro utilizzare la query seguente:

``` 
EXEC sp_droprolemember 'largerc', 'newperson' 
```
> [AZURE.NOTE]Non è possibile rimuovere un utente da smallrc.

Per visualizzare gli utenti che sono membri di un determinato ruolo, utilizzare la query seguente:```
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc')
;
```

## Rilevamento di query in coda
Per individuare le query che vengono mantenute in una coda di concorrenza è sempre possibile fare riferimento alla DMV `sys.dm_pdw_exec_requests`.

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,r.resource_class                               AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

SQL Data Warehouse dispone di tipi di attesa specifici per la misurazione della concorrenza.

Sono:
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

LocalQueriesConcurrencyResourceType si riferisce alle query che si trovano di fuori del framework di slot della concorrenza. Le query DMV e le funzioni di sistema come`SELECT @@VERSION`sono esempi di query locali.

UserConcurrencyResourceType si riferisce alle query che si trovano all’interno del framework di slot della concorrenza. Le query sulle tabelle dell’utente finale rappresentano esempi in cui si utilizza questo tipo di risorsa.

DmsConcurrencyResourceType fa riferimento alle attese risultanti dalle operazioni di spostamento dati.

BackupConcurrencyResourceType può essere visualizzato quando si esegue il backup di un database. Il valore massimo per questo tipo di risorsa è 1. Se più copie di backup sono stati richieste contemporaneamente, le altre verranno accodate.


Per eseguire l'analisi delle query attualmente in coda per individuare le risorse di cui una richiesta è in attesa, fare riferimento alla DMV `sys.dm_pdw_waits`.

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

Per visualizzare solo le attese di risorse utilizzate da una determinata query, fare riferimento alla DMV `sys.dm_pdw_resource_waits`. Il tempo di attesa di risorse include solo il tempo di attesa delle risorse che devono essere fornite, a differenza del tempo di attesa del segnale che rappresenta il tempo impiegato dal server SQL Server sottostante per pianificare la query sulla CPU.

```
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
WHERE	[session_id] <> SESSION_ID()
;
```

Infine, per l'analisi delle tendenze cronologiche delle attese, SQL Data Warehouse fornisce la DMV `sys.dm_pdw_wait_stats`.

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[Gestione di database e gli account di accesso nel Database di SQL Azure]: https://msdn.microsoft.com/it-it/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=July15_HO4-->