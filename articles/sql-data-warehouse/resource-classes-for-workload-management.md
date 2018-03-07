---
title: Classi di risorse per la gestione del carico di lavoro - Azure SQL Data Warehouse | Microsoft Docs
description: Materiale sussidiario per l'uso delle classi di risorse per gestire la concorrenza e le risorse di calcolo per le query in Azure SQL Data Warehouse.
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
ms.openlocfilehash: c76fb73c9beda93c407d1af29e157682c7fe58c0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="resource-classes-for-workload-management"></a>Classi di risorse per la gestione del carico di lavoro
Materiale sussidiario per l'uso delle classi di risorse per gestire il numero di query eseguite contemporaneamente e le risorse di calcolo per le query in Azure SQL Data Warehouse.
 
## <a name="what-is-workload-management"></a>Definizione di gestione del carico di lavoro
La gestione del carico di lavoro è la possibilità di ottimizzare le prestazioni complessive di tutte le query. Un carico di lavoro ottimizzato consente di eseguire query e operazioni di caricamento in modo efficiente anche in presenza di un elevato utilizzo di calcolo o di I/O. 

SQL Data Warehouse offre funzionalità di gestione del carico di lavoro per ambienti con più utenti. Il data warehouse non è concepito per i carichi di lavoro multi-tenant.

## <a name="what-are-resource-classes"></a>Che cosa sono le classi di risorse?
Le classi di risorse sono limiti predeterminati delle risorse che regolano l'esecuzione delle query. SQL Data Warehouse limita le risorse di calcolo per ogni query in base alla classe di risorse. 

Le classi di risorse consentono di gestire le prestazioni complessive del carico di lavoro del data warehouse. L'uso delle classi di risorse semplifica notevolmente la gestione del carico di lavoro poiché vengono impostati limiti per il numero di query eseguite simultaneamente e per le risorse di calcolo assegnate a ogni query. 

- Le classi di risorse più piccole usano meno risorse di calcolo ma consentono una maggiore concorrenza complessiva delle query
- Le classi di risorse più grandi offrono più risorse di calcolo ma limitano la concorrenza delle query

Le classi di risorse sono progettate per le attività di gestione e modifica dei dati. Possono essere utili nell'esecuzione di query molto complesse quando sono presenti join e ordinamenti di grandi dimensioni, poiché il sistema esegue la query nella memoria anziché distribuirla su disco.

Le operazioni seguenti sono gestite dalle classi di risorse:

* INSERT-SELECT, UPDATE, DELETE
* SELEZIONARE (quando si esegue una query sulle tabelle utente)
* ALTER INDEX - REBUILD o REORGANIZE
* MODIFICA TABELLA RICOMPILAZIONE
* CREATE INDEX
* CREARE L'INDICE COLUMNSTORE CLUSTER
* CREATE TABLE AS SELECT (CTAS)
* Caricamento dei dati
* Operazioni di spostamento dati condotte dal Servizio di spostamento dati (DMS)

> [!NOTE]  
> Le istruzioni SELECT per le viste a gestione dinamica (DMV) o altre viste del sistema non sono regolate da nessuno dei limiti di concorrenza. È possibile monitorare il sistema indipendentemente dal numero di query in esecuzione nel sistema.
> 
> 

## <a name="static-and-dynamic-resource-classes"></a>Classi di risorse statiche e dinamiche

Esistono due tipi di classi di risorse: statiche e dinamiche.

- Le **classi di risorse statiche** allocano la stessa quantità di memoria indipendentemente dal livello di servizio corrente, misurato in [unità Data Warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). Questa allocazione statica significa che per i livelli di servizio superiori è possibile eseguire più query in ogni classe di risorse.  Le classi di risorse statiche sono denominate staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70 e staticrc80. Queste classi di risorse sono particolarmente adatte alle soluzioni che aumentano la classe di risorse per ottenere risorse di calcolo aggiuntive.

- Le **classi di risorse dinamiche** allocano una quantità variabile di memoria in base al livello di servizio corrente. Quando si passa a un livello di servizio superiore, le query ottengono automaticamente più memoria. Le classi di risorse dinamiche sono denominate smallrc, mediumrc, largerc e xlargerc. Queste classi di risorse sono particolarmente adatte alle soluzioni che aumentano la scala di calcolo per ottenere risorse aggiuntive. 

I [livelli di prestazioni](performance-tiers.md) usano gli stessi nomi di classe di risorse ma con [specifiche diverse per memoria e concorrenza](performance-tiers.md). 


## <a name="assigning-resource-classes"></a>Assegnazione di classi di risorse

Le classi di risorse vengono implementate assegnando gli utenti ai ruoli del database. Quando un utente esegue una query, la query viene eseguita con la classe di risorse dell'utente. Ad esempio, se un utente è membro del ruolo del database smallrc o staticrc10, le relative query vengono eseguite con piccole quantità di memoria. Se un utente del database è membro del ruolo xlargerc o staticrc80, le relative query vengono eseguite con grandi quantità di memoria. 

Per aumentare la classe di risorse di un utente, usare la stored procedure [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Per diminuire la classe di risorse, usare [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

La classe di risorse dell'amministratore del servizio è fissa e non può essere modificata.  L'amministratore del servizio è l'utente creato durante il processo di provisioning.

> [!NOTE]
> Gli utenti o gruppi definiti come amministratori di Active Directory sono anche amministratori del servizio.
>
>

### <a name="default-resource-class"></a>Classe di risorse predefinita
Per impostazione predefinita, ogni utente è membro della classe di risorse piccola **smallrc**. 

### <a name="resource-class-precedence"></a>Precedenza delle classi di risorse
Gli utenti possono essere membri di più classi di risorse. Quando un utente appartiene a più di una classe di risorse:

- Le classi di risorse dinamiche hanno la precedenza sulle classi di risorse statiche. Ad esempio, se un utente è membro sia di mediumrc (dinamica) sia di staticrc80 (statica), le query vengono eseguite con mediumrc.
- Le classi di risorse più grandi hanno la precedenza sulle classi di risorse più piccole. Ad esempio, se un utente è membro di mediumrc e largerc, le query vengono eseguite con largerc. Analogamente, se un utente è membro sia di staticrc20, sia di statirc80, le query vengono eseguite con allocazioni di risorse staticrc80.

### <a name="queries-exempt-from-resource-classes"></a>Query esenti da classi di risorse
Alcune query vengono sempre eseguite nella classe di risorse smallrc anche se l'utente è membro di una classe di risorse più grande. Le query esenti non vengono conteggiate ai fini del limite di concorrenza. Ad esempio, se il limite di concorrenza è 16, molti utenti possono selezionare le viste di sistema senza conseguenze per gli slot di concorrenza disponibili.

Le istruzioni seguenti sono esenti dalle classi di risorse e vengono sempre eseguite in smallrc:

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

## <a name="recommendations"></a>Raccomandazioni
Si consiglia di creare un utente dedicato all'esecuzione di un tipo specifico di query o di operazione di caricamento. Assegnare quindi a tale utente una classe di risorse permanente anziché modificare regolarmente la classe di risorse. Dato che le classi di risorse statiche consentono un maggiore controllo generale sul carico di lavoro, è consigliabile usarle per prime e dopo considerare le classi di risorse dinamiche.

### <a name="resource-classes-for-load-users"></a>Classi di risorse per gli utenti del caricamento
`CREATE TABLE` usa indici columnstore cluster per impostazione predefinita. La compressione dei dati in un indice columnstore è un'operazione a uso intensivo di memoria e questo può ridurre la qualità dell'indice. Di conseguenza, è più probabile che sia necessaria una classe di risorse superiore durante il caricamento dei dati. Per garantire che sia disponibile una quantità di memoria sufficiente per il caricamento, è possibile creare un utente designato per l'esecuzione del caricamento e assegnare tale utente a una classe di risorse superiore.

La memoria necessaria per elaborare in modo efficiente i caricamenti dipende dalla natura della tabella caricata e dalle dimensioni dei dati. Per altre informazioni sui requisiti di memoria, vedere [Ottimizzazione della qualità di un gruppo di righe per columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Dopo aver determinato i requisiti di memoria, scegliere se assegnare l'utente del caricamento a una classe di risorse statica o dinamica.

- Usare una classe di risorse statica quando i requisiti di memoria della tabella rientrano in un intervallo specifico. I caricamenti vengono eseguiti con la memoria appropriata. Quando si ridimensiona il data warehouse, i caricamenti non richiedono altra memoria. Se si usa una classe di risorse statica, le allocazioni di memoria rimangono costanti. Questa coerenza consente di conservare la memoria e di eseguire contemporaneamente più query. Si consiglia di fare in modo che le nuove soluzioni usino prima le classi di risorse statiche poiché offrono un maggiore controllo.
- Usare una classe di risorse dinamica quando i requisiti di memoria della tabella variano notevolmente. I caricamenti potrebbero richiedere più memoria rispetto alla quantità messa a disposizione dal livello attuale di DWU o DWU a elevato utilizzo di calcolo. Pertanto, il ridimensionamento del data warehouse aggiunge altra memoria alle operazioni di caricamento, consentendo un'esecuzione più rapida di tali operazioni.

### <a name="resource-classes-for-queries"></a>Classi di risorse per le query

Alcune query sono a elevato utilizzo di calcolo e altre no.  

- Scegliere una classe di risorse dinamica quando le query sono complesse ma non richiedono una concorrenza elevata.  Ad esempio, la generazione di report giornalieri o settimanali richiede risorse in modo occasionale. Se i report elaborano grandi quantità di dati, il ridimensionamento del data warehouse rende disponibile una maggiore quantità di memoria per la classe di risorse esistente dell'utente.
- Scegliere una classe di risorse statica quando le aspettative di risorse variano nel corso della giornata. Ad esempio, una classe di risorse statica funziona bene quando molti utenti eseguono query nel data warehouse. Quando si ridimensiona il data warehouse, la quantità di memoria allocata all'utente non cambia. Di conseguenza, è possibile eseguire più query in parallelo nel sistema.

La selezione di una concessione di memoria appropriata dipende da molti fattori, come la quantità di dati sottoposti a query, la natura degli schemi di tabella e i vari predicati di gruppo, selezione e join. In generale, l'allocazione di più memoria consente di completare più rapidamente le query, ma riduce la concorrenza complessiva. Se la concorrenza non è un problema, un'allocazione eccessiva di memoria non influisce sulla velocità effettiva. 

Per ottimizzare le prestazioni, utilizzare diverse classi di risorse. La sezione successiva presenta una stored procedure che consente di individuare la classe di risorse migliore.

## <a name="example-code-for-finding-the-best-resource-class"></a>Esempio di codice per la ricerca della classe di risorse migliore
 
È possibile usare la stored procedure seguente per ottenere informazioni sulla concorrenza e sulla concessione di memoria per ogni classe di risorse in un determinato SLO e sulla classe di risorse migliore possibile per operazioni CCI a elevato utilizzo di memoria su una tabella CCI non partizionata con una classe di risorse specifica:

Ecco lo scopo della stored procedure:  
1. Visualizzare informazioni sulla concorrenza e sulla concessione di memoria per ogni classe di risorse in un determinato SLO. L'utente deve specificare NULL sia per lo schema che per il nome di tabella, come indicato nell'esempio.  
2. Visualizzare informazioni sulla classe di risorse migliore possibile per operazioni CCI a elevato utilizzo di memoria (caricamento, copia di tabelle, ricompilazione dell'indice e così via) su una tabella CCI non partizionata con una classe di risorse specifica. La stored procedure usa lo schema di tabella per individuare la concessione di memoria necessaria.

### <a name="dependencies--restrictions"></a>Dipendenze e restrizioni:
- Questa stored procedure non è progettata per calcolare i requisiti di memoria per una tabella CCI partizionata.    
- Questa stored procedure non prende in considerazione i requisiti di memoria per la parte SELECT di un'istruzione CTAS/INSERT-SELECT e presuppone che si tratti di un'istruzione SELECT.
- Questa stored procedure usa una tabella temporanea che è disponibile nella sessione in cui è stata creata la stored procedure.    
- Questa stored procedure dipende dalle risorse correnti (ad esempio, configurazione hardware e configurazione DMS) e in caso di modifiche non funziona più correttamente.  
- Questa stored procedure dipende dal limite di concorrenza esistente e in caso di modifiche non funziona più correttamente.  
- Questa stored procedure dipende dalle offerte di classi di risorse esistenti e in caso di modifiche non funziona più correttamente.  

>  [!NOTE]  
>  Se non si ottiene alcun output dopo l'esecuzione della stored procedure con i parametri specificati, i motivi potrebbero essere due. <br />1. Uno dei parametri di Data Warehouse contiene un valore SLO non valido <br />2. Oppure, non esiste alcuna classe di risorse corrispondente per l'operazione CCI nella tabella. <br />Ad esempio, con DW100, la concessione di memoria massima disponibile è 400 MB e lo schema di tabella è sufficientemente ampio per soddisfare il requisito di 400 MB.
      
### <a name="usage-example"></a>Esempio d'uso:
Sintassi:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: Fornire un parametro NULL per estrarre la DWU corrente dal database di Data Warehouse oppure fornire una DWU supportata nel formato "DW100"
2. @SCHEMA_NAME: Fornire un nome di schema della tabella
3. @TABLE_NAME: Fornire un nome di tabella

Esempi di esecuzione di questa stored procedure:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

L'istruzione seguente crea l'elemento Table1 usato negli esempi precedenti.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definizione della stored procedure

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla gestione degli utenti e della sicurezza del database, vedere [Proteggere un database in SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Per altre informazioni su come le classi di risorse più grandi possono migliorare la qualità degli indici columnstore cluster, vedere [Ottimizzazione della qualità di un gruppo di righe per columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
