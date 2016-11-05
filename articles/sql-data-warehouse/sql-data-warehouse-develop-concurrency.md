---
title: Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse | Microsoft Docs
description: Informazioni sulla gestione della concorrenza e del carico di lavoro nel Data Warehouse di SQL Azure per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: sonyam
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 09/27/2016
ms.author: sonyama;barbkess;jrj

---
# Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse
Per fornire prestazioni stimabili e scalabili, Microsoft Azure SQL Data Warehouse consente di controllare i livelli di concorrenza e le allocazioni di risorse, come la memoria e la classificazione in ordine di priorità della CPU. Questo articolo introduce i concetti di gestione della concorrenza e del carico di lavoro, spiegando in che modo entrambe le funzionalità sono state implementate e come vengono controllate nel data warehouse. La gestione del carico di lavoro di SQL Data Warehouse è concepita per facilitare il supporto di ambienti multiutente. Non è concepita per i carichi di lavoro multi-tenant.

## Limiti di concorrenza
SQL Data Warehouse consente un massimo di 1.024 connessioni simultanee. Tutte le 1.024 connessioni possono inviare query contemporaneamente. Tuttavia, per ottimizzare la velocità effettiva, SQL Data Warehouse può accodare alcune query per garantire che a ognuna venga assegnata una concessione di memoria minima. L'accodamento avviene in fase di esecuzione della query. Accodando le query quando vengono raggiunti i limiti di concorrenza, SQL Data Warehouse è in grado di aumentare la velocità effettiva totale, assicurando che le query attive ottengano l'accesso alle risorse di memoria critiche necessarie.

I limiti di concorrenza sono regolati da due concetti, *query simultanee* e *slot di concorrenza*. Perché una query venga eseguita, è necessario che rientri nel limite di concorrenza delle query e nell'allocazione di slot di concorrenza.

* Le query simultanee sono le query in esecuzione contemporaneamente. SQL Data Warehouse supporta fino a 32 query simultanee nelle DWU di dimensioni maggiori.
* In base alle Unità Data Warehouse (DWU), vengono allocati slot di concorrenza. Ogni 100 DWU, vengono forniti 4 slot di concorrenza. Ad esempio, per DW100 vengono allocati 4 slot di concorrenza e per DW1000 ne vengono allocati 40. Ogni query utilizza uno o più slot di concorrenza, a seconda della [classe di risorse](#resource-classes) della query. Le query in esecuzione nella classe di risorse smallrc utilizzano uno slot di concorrenza. Le query in esecuzione in una classe di risorse superiore usano più slot di concorrenza.

La tabella seguente descrive i limiti sia per le query simultanee che per gli slot di concorrenza a seconda delle dimensioni della DWU.

### Limiti di concorrenza
| DWU | Numero massimo di query simultanee | Numero di slot di concorrenza allocati |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

Quando viene raggiunta una di queste soglie, le nuove query vengono accodate e vengono eseguite in base al principio FIFO (First-In-First-Out). Al termine dell'esecuzione delle query e quando il numero di query e di slot risulta inferiore ai limiti, le code accodate vengono rilasciate.

> [!NOTE]
> Le query di *selezione* eseguite esclusivamente sulle viste del catalogo o sulle viste a gestione dinamica (DMV) non sono disciplinate da nessuno dei limiti di concorrenza. È possibile monitorare il sistema indipendentemente dal numero di query in esecuzione nel sistema.
> 
> 

## Classi di risorse
Attraverso le classi di risorse è possibile controllare l'allocazione di memoria e cicli di CPU assegnati a una query. È possibile assegnare quattro classi di risorse a un utente sotto forma di *ruoli del database*. Le quattro classi di risorse sono **smallrc**, **mediumrc**, **largerc** e **xlargerc**. Agli utenti nel ruolo smallrc viene assegnata una quantità di memoria più piccola e ciò permette di sfruttare una concorrenza maggiore. Al contrario, agli utenti con il ruolo xlargerc vengono assegnate grandi quantità di memoria e quindi l'esecuzione contemporanea è possibile per un minor numero di query.

Per impostazione predefinita, ogni utente è membro della classe di risorse piccola (smallrc). Per aumentare la classe di risorse viene usata la procedura `sp_addrolemember`, mentre per diminuirla viene usata la procedura `sp_droprolemember`. Questo comando, ad esempio, aumenta la classe di risorse di loaduser a largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

È buona norma assegnare in modo permanente gli utenti a una classe di risorse invece di modificare la classe di risorse degli utenti. I caricamenti in tabelle columnstore cluster, ad esempio, creano indici di qualità superiore quando viene allocata una quantità di memoria maggiore. Per assicurarsi che caricamenti abbiano accesso alla memoria superiore, creare un utente specifico per il caricamento dei dati e assegnare permanentemente a questo utente una classe di risorse superiore.

Esistono alcuni tipi di query che non traggono vantaggio da un'allocazione di memoria maggiore. Il sistema ignorerà l'allocazione della classe di risorse, anzi eseguirà sempre queste query nella classe di risorse piccola. Se queste query vengono eseguite sempre nella classe di risorse piccola, è possibile eseguirle quando gli slot di concorrenza sono sotto pressione, per non consumare più slot del necessario. Vedere [Eccezioni della classe di risorse](#query-exceptions-to-concurrency-limits) per ulteriori informazioni.

Altri dettagli sulla classe di risorse:

* L'autorizzazione *Ruolo Alter* è obbligatoria per modificare la classe di risorse di un utente.
* Sebbene si possa aggiungere un utente a una o più classi di risorse superiori, gli utenti acquisiranno gli attributi della classe di risorse più alta a cui sono assegnati. Ovvero, se a un utente sono assegnate mediumrc e largerc, verrà applicata la classe di risorse superiore (largerc).
* La classe di risorse dell'utente amministratore di sistema non può essere modificata.

Per un esempio dettagliato, vedere [Esempio di modifica della classe di risorse di un utente](#changing-user-resource-class-example).

## Allocazione della memoria
Esistono vantaggi e svantaggi legati all'incremento della classe di risorse dell'utente. L'incremento della classe di risorse per un utente consentirà alle rispettive query di accedere a una quantità maggiore di memoria, ovvero le query verranno eseguite con maggiore rapidità. Una quantità maggiore di classi di risorse, tuttavia, riduce anche il numero di query simultanee che è possibile eseguire. Questo è il compromesso tra il fatto di allocare grandi quantità di memoria a una singola query o consentire l'esecuzione simultanea di altre query che necessitano di allocazioni di memoria. Se a un utente vengono allocate grandi quantità di memoria per una query, gli altri utenti non potranno accedere alla stessa memoria per eseguire una query.

Nella tabella seguente la memoria allocata è associata a ogni distribuzione per classe DWU e classe di risorse.

### Allocazioni di memoria per ogni distribuzione (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1\.600 |
| DW500 |100 |400 |800 |1\.600 |
| DW600 |100 |400 |800 |1\.600 |
| DW1000 |100 |800 |1\.600 |3\.200 |
| DW1200 |100 |800 |1\.600 |3\.200 |
| DW1500 |100 |800 |1\.600 |3\.200 |
| DW2000 |100 |1\.600 |3\.200 |6\.400 |
| DW3000 |100 |1\.600 |3\.200 |6\.400 |
| DW6000 |100 |3\.200 |6\.400 |12\.800 |

Come si può notare nella tabella precedente, una query in esecuzione in DW2000 nella classe di risorse xlargerc avrà accesso a 6.400 MB di memoria in ognuno dei 60 database distribuiti. In SQL Data Warehouse sono presenti 60 distribuzioni. Per calcolare quindi l'allocazione totale di memoria per una query in una classe di risorse specifica, è necessario moltiplicare per 60 i valori precedenti.

### Allocazioni di memoria a livello di sistema (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Come si può notare in questa tabella di allocazioni di memoria a livello di sistema, a una query in esecuzione in DW2000 nella classe di risorse xlargerc viene allocato un totale di 375 GB di memoria (6.400 MB * 60 distribuzioni/1.024 per la conversione in GB) nell'intero SQL Data Warehouse.

## Utilizzo di slot di concorrenza
SQL Data Warehouse concede più memoria alle query in esecuzione nelle classi di risorse più grandi. La memoria è una risorsa fissa. Maggiore sarà la quantità di memoria allocata per ogni query, quindi, minore sarà il numero di richieste simultanee che è possibile eseguire. La tabella seguente riprende tutti i concetti descritti finora in un'unica rappresentazione che mostra il numero di slot di concorrenza disponibili per DWU e gli slot usati da ogni classe di risorse.

### Allocazione e consumo di slot di concorrenza
| DWU | Numero massimo di query simultanee | Numero di slot di concorrenza allocati | Slot utilizzati da smallrc | Slot utilizzati da mediumrc | Slot utilizzati da largerc | Slot utilizzati da xlargerc |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

Da questa tabella è possibile vedere che l'esecuzione di SQL Data Warehouse come DW1000 alloca un totale di 40 slot di concorrenza per un massimo di 32 query simultanee. Se l'esecuzione avviene da parte di tutti gli utenti nella classe smallrc, vengono consentite 32 query simultanee, poiché ognuna richiede 1 slot di concorrenza. Se l'esecuzione avviene da parte di tutti gli utenti di un DW1000 in una classe mediumrc, per ogni query vengono allocati 800 MB a distribuzione, per un'allocazione di memoria totale pari a 47 GB per query, mentre il numero degli utenti simultanei viene limitato a 5 (40 slot di concorrenza, 8 per ogni utente mediumrc).

## Priorità delle query
SQL Data Warehouse implementa le classi di risorse utilizzando i gruppi del carico di lavoro. Esistono in totale otto gruppi di carichi di lavoro che controllano il comportamento delle classi di risorse nelle DWU di varie dimensioni. Per qualsiasi DWU, SQL Data Warehouse usa solo quattro degli otto gruppi del carico di lavoro. Il senso di questo approccio è assegnare a ogni gruppo di carichi di lavoro una delle quattro classi di risorse, tra smallrc, mediumrc, largerc o xlargerc. È importante comprendere per alcuni gruppi di carichi di lavoro viene impostato il livello di *priorità* più elevato. La priorità viene usata per la pianificazione della CPU. Le query eseguite con priorità alta otterranno tre volte più cicli della CPU rispetto a quelle con priorità media. Di conseguenza, i mapping degli slot della concorrenza determinano anche la priorità della CPU. Quando una query utilizza 16 o più slot, viene eseguita con priorità alta.

Nella tabella seguente vengono riportati i mapping di priorità per ogni gruppo di carichi di lavoro.

### Mapping dei gruppi di carichi di lavoro agli slot di concorrenza e relativa importanza
| Gruppi di carichi di lavoro | Mapping degli slot di concorrenza | MB/Distribuzione | Mapping delle priorità |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Media |
| SloDWGroupC01 |2 |200 |Media |
| SloDWGroupC02 |4 |400 |Media |
| SloDWGroupC03 |8 |800 |Media |
| SloDWGroupC04 |16 |1\.600 |Alto |
| SloDWGroupC05 |32 |3\.200 |Alto |
| SloDWGroupC06 |64 |6\.400 |Alto |
| SloDWGroupC07 |128 |12\.800 |Alto |

Come illustrato nel grafico **Allocation and consumption of concurrency slots** (Allocazione e utilizzo degli slot di concorrenza), DW500 utilizza 1, 4, 8 o 16 slot di concorrenza per smallrc, mediumrc, largerc e xlargerc rispettivamente. È possibile cercare questi valori nel grafico precedente per identificare la priorità di ciascuna classe di risorse.

### Mapping dell'importanza delle classi di risorse in DW500
| Classe di risorse | Gruppo del carico di lavoro | Numero di slot di concorrenza usati | MB/Distribuzione | Importance |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Media |
| mediumrc |SloDWGroupC02 |4 |400 |Media |
| largerc |SloDWGroupC03 |8 |800 |Media |
| xlargerc |SloDWGroupC04 |16 |1\.600 |Alto |

Per esaminare in dettaglio le differenze nell'allocazione delle risorse di memoria dal punto di vista di resource governor o per analizzare l'utilizzo attivo e cronologico dei gruppi di carichi di lavoro in caso di risoluzione dei problemi, è possibile usare la query DMV seguente:

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## Query che rispettano i limiti di concorrenza
La maggior parte delle query è regolata da classi di risorse. Queste query devono rientrare in entrambe le soglie delle query simultanee e degli slot di concorrenza. Un utente può scegliere di escludere una query dal modello di slot di concorrenza.

Le istruzioni seguenti rispettano le classi di risorse:

* INSERT SELECT
* AGGIORNAMENTO
* DELETE
* SELEZIONARE (quando si esegue una query sulle tabelle utente)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* MODIFICA TABELLA RICOMPILAZIONE
* CREATE INDEX
* CREARE L'INDICE COLUMNSTORE CLUSTER
* CREATE TABLE AS SELECT (CTAS)
* Caricamento dei dati
* Operazioni di spostamento dati condotte dal Servizio di spostamento dati (DMS)

## Eccezioni query ai limiti di concorrenza
Alcune query non rispettano la classe di risorse alla quale è assegnato l'utente. Queste eccezioni ai limiti di concorrenza vengono effettuate quando le risorse di memoria necessarie per un particolare comando sono basse, spesso perché il comando è un'operazione dei metadati. Con queste eccezioni, si intende evitare l'allocazione di più memoria alle query che non ne hanno bisogno. In questi casi, viene sempre usata la classe di risorse piccola predefinita (smallrc), indipendentemente dalla classe di risorse effettivamente assegnata all'utente. Ad esempio, in smallrc verrà sempre eseguita `CREATE LOGIN`. Le risorse necessarie per svolgere questa operazione sono molto ridotte e non avrebbe senso includere la query nel modello di slot di concorrenza. Per queste query non è previsto il limite di 32 utenti simultanei; è possibile eseguire un numero illimitato di queste query fino al limite di 1.024 sessioni.

Le istruzioni seguenti non rispettano le classi di risorse:

* CREATE o DROP TABLE
* ALTER TABLE... SWITCH, SPLIT o MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE o DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER o DROP USER
* CREATE, ALTER o DROP PROCEDURE
* CREATE o DROP VIEW
* INSERT VALUES
* SELECT da viste di sistema e DMV
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## Esempio di modifica della classe di risorse di un utente
1. **Creare un account di accesso:** aprire una connessione al database **master** nel server SQL che ospita il database SQL Data Warehouse ed eseguire i comandi seguenti.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > È consigliabile creare un utente nel database master per gli utenti di SQL Data Warehouse di Azure. La creazione di un utente nel database master consente all'utente di accedere tramite strumenti come SSMS senza specificare un nome di database. Consente inoltre di usare Esplora oggetti per visualizzare tutti i database in SQL server. Per ulteriori informazioni su creazione e gestione degli utenti, vedere [Proteggere un database in SQL Data Warehouse][Proteggere un database in SQL Data Warehouse].
   > 
   > 
2. **Creare un utente di SQL Data Warehouse:** aprire una connessione al database di **SQL Data Warehouse** ed eseguire il comando seguente.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Concedere autorizzazioni**: l'esempio seguente concede l'autorizzazione `CONTROL` nel database **SQL Data Warehouse**. `CONTROL` a livello di database è l'equivalente del ruolo db\_owner in SQL Server.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Aumentare la classe di risorse:** per aggiungere un utente a un ruolo di gestione del carico di lavoro più elevato, usare la query seguente.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Diminuire la classe di risorse:** per rimuovere un utente da un ruolo di gestione del carico di lavoro, usare la query seguente.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > Non è possibile rimuovere un utente da smallrc.
   > 
   > 

## Rilevamento di query in coda e altre viste a gestione dinamica
È possibile utilizzare la DMV `sys.dm_pdw_exec_requests` per identificare le query in attesa in una coda di concorrenza. Le query in attesa di uno slot di concorrenza saranno in stato **sospeso**.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
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
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse prevede i tipi di attesa seguenti.

* **LocalQueriesConcurrencyResourceType**: query che si trovano all'esterno del framework di slot di concorrenza. Le query DMV e le funzioni di sistema come`SELECT @@VERSION`sono esempi di query locali.
* **UserConcurrencyResourceType**: query che si trovano all'interno del framework di slot di concorrenza. Le query sulle tabelle dell'utente finale rappresentano esempi in cui si usa questo tipo di risorsa.
* **DmsConcurrencyResourceType**: attese risultanti dalle operazioni di spostamento dei dati.
* **BackupConcurrencyResourceType**: indica che è in esecuzione il backup di un database. Il valore massimo per questo tipo di risorsa è 1. Se più copie di backup sono stati richieste contemporaneamente, le altre verranno accodate.

La DMV `sys.dm_pdw_waits` può essere usata per visualizzare le risorse per cui una richiesta è in attesa.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
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
WHERE    [session_id] <> SESSION_ID();
```

L DMV `sys.dm_pdw_wait_stats` può essere usata per l'analisi della tendenza cronologica delle attese.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## Passaggi successivi
Per ulteriori informazioni sulla gestione degli utenti e della sicurezza del database, vedere [Proteggere un database in SQL Data Warehouse][Proteggere un database in SQL Data Warehouse]. Per ulteriori informazioni sulle classi di risorse più grandi che possono migliorare le qualità degli indici indice columnstore cluster, vedere [Ricompilazione degli indici per migliorare la qualità del segmento].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Ricompilazione degli indici per migliorare la qualità del segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Proteggere un database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0928_2016-->