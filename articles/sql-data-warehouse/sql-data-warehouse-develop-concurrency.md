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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse

Per fornire prestazioni stimabili su larga scala SQL Data Warehouse consente agli utenti di controllare i livelli di concorrenza, nonché le allocazioni di risorse, come la memoria e la classificazione in ordine di priorità della CPU. Questo articolo introduce i concetti di gestione della concorrenza e del carico di lavoro, spiegando in che modo entrambe le funzionalità sono state implementate e come vengono controllate nel data warehouse. La gestione del carico di lavoro di SQL Data Warehouse è concepita per facilitare il supporto di ambienti multiutente. Non è concepita per i carichi di lavoro multi-tenant.

## Limiti di concorrenza

SQL Data Warehouse consente un massimo di 1.024 connessioni simultanee. Tutte le 1.024 connessioni possono inviare query contemporaneamente. Tuttavia, per ottimizzare la velocità effettiva, SQL Data Warehouse può accodare alcune query per garantire che a ognuna venga assegnata una concessione di memoria minima. L'accodamento si verifica in fase di esecuzione delle query quando vengono rilevati limiti di concorrenza. Accodando le query quando vengono raggiunti i limiti di concorrenza, SQL Data Warehouse è in grado di aumentare la velocità effettiva totale, assicurando che le query attive ottengano l'accesso alle risorse di memoria critiche necessarie.

I limiti di concorrenza sono regolati da due concetti, **query simultanee** e **slot di concorrenza**. Perché una query venga eseguita, è necessario che rientri sia nel limite di concorrenza e nell'allocazione di slot di concorrenza.

- Le **query simultanee** equivalgono semplicemente al numero di query in esecuzione contemporaneamente. SQL Data Warehouse supporta fino a 32 **query simultanee** nei DW di dimensioni maggiori, DW1000 e superiori. Tuttavia, poiché il numero di query simultanee varia in base al numero di DWU, di seguito è riportata una tabella che mostra le limitazioni per DWU.
- Gli **slot di concorrenza** rappresentano un concetto più dinamico. Ogni esecuzione simultanea di query utilizza uno o più slot di concorrenza. Il numero esatto di slot utilizzato da una query dipende dalle dimensioni di SQL Data Warehouse e dalla [classe di risorse](#resource-classes) della query.

La tabella seguente descrive i limiti sia per le query simultanee che per gli slot di concorrenza.

### Limiti di concorrenza

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| Numero massimo di query simultanee | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Numero massimo di slot di concorrenza | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 120 | 240 |

Quando viene raggiunta una di queste soglie, le nuove query vengono messe in coda. Le query in coda vengono eseguite in base al metodo FIFO (First In, First Out) man mano che le query vengono completate e gli slot scendono al di sotto dei limiti.

> [AZURE.NOTE]  Le query di selezione eseguite esclusivamente sulle viste del catalogo o sulle viste a gestione dinamica (DMV) **non** sono disciplinate dalle classi di risorse. Ciò consente agli utenti di monitorare il sistema anche quando tutti gli slot di concorrenza sono in uso.

## Classi di risorse

Le classi di risorse sono una parte essenziale della gestione del carico di lavoro di SQL Data Warehouse, perché consentono di allocare più memoria e/o cicli della CPU alle query eseguite da un determinato utente. Esistono quattro classi di risorse, ognuna in formato di **ruolo del database**. Le quattro classi di risorse sono **smallrc, mediumrc, largerc e xlargerc**. Agli utenti nel ruolo smallrc viene assegnata una quantità di memoria più piccola e ciò consente una concorrenza maggiore. Al contrario, agli utenti con il ruolo xlargerc vengono assegnate grandi quantità di memoria e quindi l'esecuzione contemporanea è consentita a un minor numero di query.

Esistono alcuni tipi di query che non ottengono vantaggi da una maggiore allocazione di memoria e di conseguenza ignorano l'allocazione della relativa classe di risorse e vengono invece eseguite in una classe di risorse piccola. Forzare sempre l'esecuzione di queste query in una classe di risorse piccola ne consente l'esecuzione quando gli slot di concorrenza sono sotto pressione e impedisce che utilizzino più slot di quelli necessari. Le [Eccezioni della classe di risorse](#resource-class-exceptions) sono descritte più avanti in questo articolo.

Per impostazione predefinita, ogni utente è membro della classe di risorse piccola (smallrc). La procedura `sp_addrolemember` viene usata per aumentare la classe di risorse, mentre `sp_droprolemember` viene usata per diminuirla. Questo comando, ad esempio, aumenta la classe di risorse di loaduser a largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

È buona norma creare utenti assegnati in modo permanente a una classe di risorse invece di modificare la classe di risorse di un utente. I caricamenti in tabelle columnstore cluster, ad esempio, creano indici di qualità superiore quando viene allocata una quantità di memoria maggiore. Per assicurarsi che caricamenti abbiano accesso alla memoria superiore, creare un utente specifico per il caricamento dei dati e assegnare permanentemente a questo utente una classe di risorse superiore.

Altri dettagli sulla classe di risorse:

- L'autorizzazione `ALTER ROLE` è obbligatoria per modificare la classe di risorse di un utente.
- Mentre un utente può essere aggiunto a una o più classi di risorse superiori, gli utenti acquisiranno gli attributi della classe di risorse più alta a cui sono assegnati. Ovvero, se a un utente sono assegnate mediumrc e largerc, verrà applicata la classe di risorse superiore, largerc.
- La classe di risorse dell'utente amministratore di sistema non può essere modificata.
 
Altri dettagli ed esempi di creazione di utenti e assegnazione a classi di risorse sono disponibili nella sezione[Gestione degli utenti](#Managing-users) alla fine di questo articolo.

## Allocazione della memoria

Esistono vantaggi e svantaggi legati all'incremento della classe di risorse dell'utente. Anche se l'aumento di una classe di risorse per un utente può significare che le query hanno accesso a una maggiore quantità memoria e possono essere eseguite più velocemente, riduce però il numero di query simultanee che è possibile eseguire. Questo è il compromesso tra l'allocazione di grandi quantità di memoria a una singola query e consentire l'esecuzione di altre query simultanee che necessitano di allocazioni di memoria. Se a un utente viene allocata più memoria per una query, altri utenti non avranno memoria disponibile per l'esecuzione di una query.

Nella tabella seguente la memoria allocata è associata a ogni distribuzione per classe DWU e classe di risorse. In SQL Data Warehouse sono disponibili distribuzioni di 60 per ogni database, quindi una query in esecuzione in DW2000 nella classe di risorse xlarge avrà accesso a 6.400 MB in ognuno dei 60 database.

### Allocazioni di memoria per ogni distribuzione (MB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| smallrc | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| mediumrc | 100 | 200 | 200 | 400 | 400 | 400 | 800 | 800 | 800 | 1\.600 | 1\.600 | 3\.200 |
| largerc | 200 | 400 | 400 | 800 | 800 | 800 | 1\.600 | 1\.600 | 1\.600 | 3\.200 | 3\.200 | 6\.400 |
| xlargerc | 400 | 800 | 800 | 1\.600 | 1\.600 | 1\.600 | 3\.200 | 3\.200 | 3\.200 | 6\.400 | 6\.400 | 12\.800 |


Usando lo stesso esempio precedente, a una query a livello di sistema in esecuzione in DW2000 nella classe di risorse xlarge vengono allocati 375 GB di memoria totale (6.400 MB * 60 distribuzioni/1.024 per la conversione in GB).

### Allocazioni di memoria a livello di sistema (GB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
|smallrc | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 |
|mediumrc | 6 | 12 | 12 | 23 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 188 |
|largerc | 12 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 375 |
|xlargerc | 23 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 188 | 375 | 375 | 750 |

## Utilizzo di slot di concorrenza

Come indicato in precedenza, maggiore è la classe di risorse, più memoria viene concessa. Poiché la memoria è una risorsa fissa, maggiore è la quantità di memoria allocata per ogni query, minore è la concorrenza che può essere supportata. Nella tabella seguente viene reiterato il numero di slot di concorrenza disponibili per DWU, nonché gli slot utilizzati da ogni classe di risorse.

### Allocazione e consumo di slot di concorrenza

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :---------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| **Allocazione** | | | | | | | | | | | | |
| Numero massimo di query simultanee | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Numero massimo di slot di concorrenza | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 80 | 80 |
| **Utilizzo di slot** | | | | | | | | | | | | |
| smallrc | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 32 |
| largerc | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 64 |
| xlargerc | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 | 64 | 128 |

Da questa tabella si può notare che un SQL Data Warehouse in esecuzione come DW100 consentirà 4 query simultanee smallrc o 2 query simultanee largerc.

## Priorità delle query

In realtà esistono in totale otto gruppi di carichi di lavoro che controllano il comportamento delle classi di risorse. Tuttavia, solo quattro degli otto gruppi di carichi di lavoro vengono utilizzati in una data DWU. Questo approccio ha senso, perché ogni gruppo di carichi di lavoro viene assegnato a smallrc, mediumrc, largerc oppure a xlargerc. È importante comprendere questi gruppi di carichi di lavoro sottostanti perché alcuni di essi sono impostati sul livello di **PRIORITÀ** più elevato. La priorità viene usata per la pianificazione della CPU. Le query eseguite con priorità alta otterranno 3 volte più cicli della CPU rispetto a quelle con priorità media. Di conseguenza, i mapping degli slot della concorrenza determinano anche la priorità della CPU. Quando una query utilizza 16 o più slot, viene eseguita con priorità alta.

Di seguito sono riportati i mapping di priorità per ogni gruppo di carichi di lavoro.

| Gruppi di carichi di lavoro | Mapping di Slot di concorrenza | Mapping di priorità |
| :------------------  | :----------------------: | :----------------- |
| SloDWGroupC00 | 1 | Media |
| SloDWGroupC01 | 2 | Media |
| SloDWGroupC02 | 4 | Media |
| SloDWGroupC03 | 8 | Media |
| SloDWGroupC04 | 16 | Alto |
| SloDWGroupC05 | 32 | Alto |
| SloDWGroupC06 | 64 | Alto |
| SloDWGroupC07 | 128 | Alto |

Per un SQL Data Warehouse DW500 i gruppi di carichi di lavoro attivi saranno associati alle classi di risorse come segue.

| Classe di risorse | Gruppo di carico | Numero di slot di concorrenza usate | Importance |
| :--------------- | :------------- | :--------------------:   | :--------- |
| smallrc | SloDWGroupC00 | 1 | Media |
| mediumrc | SloDWGroupC02 | 4 | Media |
| largerc | SloDWGroupC03 | 8 | Media |
| xlargerc | SloDWGroupC04 | 16 | Alto |


Per esaminare in dettaglio le differenze nell'allocazione delle risorse di memoria dal punto di vista di resource governor o per analizzare l'utilizzo attivo e cronologico dei gruppi di carichi di lavoro in caso di risoluzione dei problemi, è possibile usare la query DMV seguente:

```sql
WITH rg
AS
(   SELECT  
     pn.name						AS node_name
    ,pn.[type]						AS node_type
    ,pn.pdw_node_id					AS node_id
    ,rp.name						AS pool_name
    ,rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,wg.name						AS group_name
    ,wg.importance					AS group_importance
    ,wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,wg.max_dop						AS group_max_dop
    ,wg.effective_max_dop				AS group_effective_max_dop
    ,wg.total_request_count				AS group_total_request_count
    ,wg.total_queued_request_count			AS group_total_queued_request_count
    ,wg.active_request_count				AS group_active_request_count
    ,wg.queued_request_count				AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
    	    AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn	
            ON	wg.pdw_node_id	= pn.pdw_node_id
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

## Eccezioni della classe di risorse

La maggior parte delle query rispetta le classi di risorse, tuttavia esistono alcune eccezioni. In genere ciò si verifica quando le risorse necessarie per soddisfare l'azione sono insufficienti. Vale a dire che le eccezioni riguardano in genere casi in cui una query non userà mai la memoria più elevata allocata dalle classi di risorse superiori. In questi casi, viene sempre usata la classe di risorse predefinita o piccola (smallrc), indipendentemente dalla classe di risorse assegnata all'utente. Ad esempio, `CREATE LOGIN` viene sempre eseguito in smallrc. Le risorse necessarie per svolgere questa operazione sono molto bassa e pertanto non avrebbe senso di includere la query nel modello di slot della concorrenza. Sarebbe uno spreco di pre-allocare grandi quantità di memoria per questa azione. Escludendo `CREATE LOGIN` dal modello slot concorrenza SQL Data Warehouse può essere molto più efficiente.

Le istruzioni seguenti **non** rispettano le classi di risorse:

- CREATE o DROP TABLE
- ALTER TABLE... SWITCH, SPLIT o MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE, UPDATE o DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE, ALTER o DROP USER
- CREATE, ALTER o DROP PROCEDURE
- CREATE o DROP VIEW
- INSERT VALUES
- SELECT da viste di sistema e DMV
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

### Query che rispettano i limiti di concorrenza

È importante ricordare che la maggior parte delle query degli utenti finali sono probabilmente disciplinati dalle classi di risorse. La regola generale prevede che il carico di lavoro di query attiva deve rientrare in entrambe le simultanee query e la concorrenza slot soglie a meno che non è stata esclusa in modo specifico della piattaforma. Come un utente finale è possibile scegliere di escludere una query dal modello di slot della concorrenza. Una volta superata una soglia, la query viene accodata. Le query in coda verranno risolte in ordine di priorità in base all'ora di invio.

Le istruzioni seguenti **non** rispettano le classi di risorse:

- INSERT SELECT
- AGGIORNAMENTO
- DELETE
- SELEZIONARE (quando si esegue una query sulle tabelle utente)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- MODIFICA TABELLA RICOMPILAZIONE
- CREATE INDEX
- CREARE L'INDICE COLUMNSTORE CLUSTER
- CREATE TABLE AS SELECT
- Caricamento dei dati
- Operazioni di spostamento dati condotte dal Servizio di spostamento dati (DMS)


## Gestione degli utenti

1. **Creare un account di accesso:** aprire una connessione al database **master** per SQL Data Warehouse ed eseguire i comandi seguenti.
	
	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] È consigliabile creare utenti per gli account di accesso nel database master sia nel database SQL di Azure che in SQL Data Warehouse. Su questo livello ci sono due ruoli disponibili che richiedono l'accesso per avere un utente nel database master per concedere l’appartenenza. I ruoli sono`Loginmanager` e `dbmanager`: Nel database SQL Azure e SQL Data Warehouse questi ruoli concedono diritti per gestire gli account di accesso e per creare database. Questo comportamento è diverso rispetto a SQL Server. Per altre informazioni, vedere l'articolo[Autenticazione e autorizzazione per database SQL: concessione dell'accesso][].

2. **Creare un account utente:** aprire una connessione al **database SQL Data Warehouse** ed eseguire il comando seguente.

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **Concedere autorizzazioni**: l'esempio seguente concede l'autorizzazione `CONTROL` nel database SQL Data Warehouse. `CONTROL` a livello di database è l'equivalente del ruolo db\_owner in SQL Server.

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **Aumentare la classe di risorse:** per aggiungere un utente a un ruolo di gestione per l'aumento del carico di lavoro, usare la query seguente.

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **Diminuire la classe di risorse:** per rimuovere un utente da un ruolo di gestione del carico di lavoro, usare la query seguente.

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] Non è possibile rimuovere un utente da smallrc.

## Rilevamento di query in coda e altre viste a gestione dinamica

La DMV `sys.dm_pdw_exec_requests` può essere usata per identificare le query in attesa in una coda di concorrenza.

```sql
SELECT 	 r.[request_id]				 AS Request_ID
	,r.[status]				 AS Request_Status
	,r.[submit_time]			 AS Request_SubmitTime
	,r.[start_time]				 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

I ruoli di gestione del carico di lavoro possono essere visualizzati con `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La query seguente mostra il ruolo a cui è assegnato ogni utente.

```sql
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse prevede i tipi di attesa seguenti.

- LocalQueriesConcurrencyResourceType: query che si trovano all'esterno del framework di slot di concorrenza. Le query DMV e le funzioni di sistema come`SELECT @@VERSION`sono esempi di query locali.
- UserConcurrencyResourceType: query che si trovano all'interno del framework di slot di concorrenza. Le query sulle tabelle dell’utente finale rappresentano esempi in cui si utilizza questo tipo di risorsa.
- DmsConcurrencyResourceType: attese risultanti dalle operazioni di spostamento dei dati.
- BackupConcurrencyResourceType: indica che è in esecuzione il backup di un database. Il valore massimo per questo tipo di risorsa è 1. Se più copie di backup sono stati richieste contemporaneamente, le altre verranno accodate.

La DMV `sys.dm_pdw_waits` può essere usata per visualizzare le risorse per cui una richiesta è in attesa.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]			AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,	SESSION_ID()			AS Current_session
,	s.[status]			AS Session_status
,	s.[login_name]
,	s.[query_count]
,	s.[client_id]
,	s.[sql_spid]
,	r.[command]			AS Request_command
,	r.[label]
,	r.[status]			AS Request_status
,	r.[submit_time]
,	r.[start_time]
,	r.[end_compile_time]
,	r.[end_time]
,	DATEDIFF(ms,r.[submit_time],r.[start_time])		AS Request_queue_time_ms
,	DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,	DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,	r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID();
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
WHERE	[session_id] <> SESSION_ID();
```

L DMV `sys.dm_pdw_wait_stats` può essere usata per l'analisi della tendenza cronologica delle attese.

```sql
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w;
```

## Passaggi successivi

Per altre informazioni sulla gestione degli utenti e della sicurezza del database, vedere [Proteggere un database in SQL Data Warehouse][]. Per altre informazioni sulle classi di risorse più grandi che possono migliorare le qualità degli indici indice columnstore cluster, vedere [Rebuilding indexes to improve segment quality] (Ricompilazione degli indici per migliorare la qualità del segmento).

<!--Image references-->

<!--Article references-->
[Proteggere un database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality

<!--MSDN references-->
[Autenticazione e autorizzazione per database SQL: concessione dell'accesso]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0713_2016-->