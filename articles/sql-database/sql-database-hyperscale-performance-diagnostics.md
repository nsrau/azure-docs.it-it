---
title: Diagnostica delle prestazioni in HyperscalePerformance diagnostics in Hyperscale
description: Questo articolo descrive come risolvere i problemi di prestazioni Dihyperscale nel database SQL di Azure.This article describes how to troubleshoot Hyperscale performance problems in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75615071"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnostica della risoluzione dei problemi relativi alle prestazioni di Hyperscale SQLSQL Hyperscale performance troubleshooting diagnostics

Per risolvere i problemi di prestazioni in un database Hyperscale, [le metodologie di ottimizzazione generale delle prestazioni](sql-database-monitor-tune-overview.md) nel nodo di calcolo del database SQL di Azure sono il punto di partenza di un'analisi delle prestazioni. Tuttavia, data [l'architettura distribuita](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) di Hyperscale, è stata aggiunta una diagnostica aggiuntiva per facilitare l'assistenza. In questo articolo vengono descritti i dati di diagnostica specifici di Hyperscale.This article describes Hyperscale-specific diagnostic data.

## <a name="log-rate-throttling-waits"></a>Attee di limitazione della frequenza di logLog rate throttling waits

A ogni livello di servizio del database SQL di Azure sono applicati limiti di frequenza di generazione dei log tramite governance della [frequenza](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)dei log. In Hyperscale, il limite di generazione del log è attualmente impostato su 100 MB/sec, indipendentemente dal livello di servizio. Tuttavia, in alcuni casi è necessario limitare la velocità di generazione del log nella replica di calcolo primaria per mantenere i contratti di lavoro di recuperabilità. Questa limitazione si verifica quando un server di [paging o un'altra replica](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) di calcolo è significativamente dietro l'applicazione di nuovi record di log dal servizio di log.

I tipi di attesa seguenti (in sys.dm_os_wait_stats ) descrivono i motivi per cui la frequenza di log può essere limitata nella replica di calcolo primaria:The following wait types (in [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) describe the reasons why log rate can be throttled on the primary compute replica:

|Tipo di attesa    |Descrizione                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Si verifica quando la frequenza di generazione del log del nodo di calcolo primario di un database Hyperscale viene limitata a causa del consumo ritardato del log nei server di paging.         |
|RBIO_RG_DESTAGE        | Si verifica quando la frequenza di generazione di un nodo di calcolo del database Hyperscale viene limitata a causa del ritardo nell'utilizzo del log da parte dello spazio di archiviazione dei log a lungo termine.         |
|RBIO_RG_REPLICA        | Si verifica quando la frequenza di generazione del log del nodo di calcolo del database Hyperscale viene limitata a causa del consumo ritardato del log da parte delle repliche secondarie leggibili.         |
|RBIO_RG_LOCALDESTAGE   | Si verifica quando la frequenza di generazione di un nodo di calcolo del database Hyperscale viene limitata a causa del ritardo nell'utilizzo del log da parte del servizio di log.         |

## <a name="page-server-reads"></a>Letture del server di paging

Le repliche di calcolo non memorizzano nella cache una copia completa del database in locale. I dati locali alla replica di calcolo vengono archiviati nel pool di buffer (in memoria) e nella cache RBPEX (Resilient Buffer Pool Extension) locale che è una cache parziale (non di copertura) delle pagine di dati. Questa cache RBPEX locale viene ridimensionata proporzionalmente alla dimensione di calcolo ed è tre volte la memoria del livello di calcolo. RBPEX è simile al pool di buffer in quanto contiene i dati a cui si accede più di frequente. Ogni server di paging, d'altra parte, dispone di una cache RBPEX di copertura per la parte del database che gestisce.
 
Quando viene eseguita una lettura in una replica di calcolo, se i dati non esistono nel Pool di buffer o nella cache RBPEX locale, viene emessa una chiamata di funzione getPage(pageId, LSN) e la pagina viene recuperata dal server di paging corrispondente. Le letture dai server di paging sono letture remote e sono quindi più lente rispetto alle letture dall'RBPEX locale. Quando si risolvono i problemi di prestazioni correlati all'I/O, è necessario essere in grado di stabilire quanti i/o sono stati eseguiti tramite letture di pagine di pagina remote relativamente più lente.

Diverse DMV ed eventi estesi dispongono di colonne e campi che specificano il numero di letture remote da un server di paging, che possono essere confrontate con il totale delle letture. L'archivio query acquisisce inoltre le letture remote come parte delle statistiche di runtime delle query.

- Le colonne da segnalare letture del server delle pagine sono disponibili nelle DMV in esecuzione e nelle visualizzazioni del catalogo, ad esempio:
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- Le letture del server di paging vengono aggiunte ai seguenti eventi estesi:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - store_execution_runtime_info di query
- ActualPageServerReads/ActualPageServerReadAheads vengono aggiunti all'XML del piano di query per i piani effettivi. Ad esempio:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Per visualizzare questi attributi nella finestra delle proprietà del piano di query, è necessario SSMS 18.3 o versione successiva.

## <a name="virtual-file-stats-and-io-accounting"></a>Statistiche dei file virtuali e contabilità I/O

Nel database SQL di Azure, il DMF sys.dm_io_virtual_file_stats() è il modo principale per monitorare l'I/O di SQL Server.In Azure SQL Database, the [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF is the primary way to monitor SQL Server IO. Le caratteristiche di I/O in Hyperscale sono diverse a causa [dell'architettura distribuita.](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) In questa sezione, ci concentriamo su IO (letture e scritture) ai file di dati come visto in questo DMF. In Hyperscale, ogni file di dati visibile in questo dMF corrisponde a un server di paging remoto. La cache RBPEX menzionata qui è una cache locale basata su SSD, ovvero una cache non di copertura nella replica di calcolo.

### <a name="local-rbpex-cache-usage"></a>Utilizzo della cache RBPEX locale

La cache RBPEX locale esiste nella replica di calcolo, nell'archiviazione SSD locale. Pertanto, I/O rispetto a questa cache è più veloce di I/O rispetto ai server di paging remoti. Attualmente, [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) in un database Hyperscale include una riga speciale che segnala l'I/O rispetto alla cache RBPEX locale nella replica di calcolo. Questa riga ha il valore `database_id` `file_id` 0 per entrambe le colonne. Ad esempio, la query seguente restituisce le statistiche di utilizzo RBPEX dall'avvio del database.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Un rapporto di letture eseguite su RBPEX e letture aggregate eseguite su tutti gli altri file di dati fornisce il rapporto riscontri cache RBPEX.

### <a name="data-reads"></a>Letture dati

- Quando le letture vengono emesse dal motore di SQL Server sql In una replica di calcolo, possono essere servite dalla cache RBPEX locale o da server di paging remoti o da una combinazione dei due se si leggono più pagine.
- Quando la replica di calcolo legge alcune pagine da un file specifico, ad esempio file_id 1, se questi dati risiedono esclusivamente nella cache RBPEX locale, tutti i /o per questa lettura vengono contabilizzati su file_id 0 (RBPEX). Se una parte di tali dati si trova nella cache RBPEX locale e una parte si trova su un server di paging remoto, l'I/O viene conteggiato verso file_id 0 per la parte servita da RBPEX e la parte servita dal server di paging remoto viene conteggiata verso file_id 1. 
- Quando una replica di calcolo richiede una pagina in un particolare [numero LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) da un server di paging, se il server di paging non ha raggiunto il numero LSN richiesto, la lettura nella replica di calcolo attenderà che il server della pagina venga rilevato prima che la pagina venga restituita alla replica di calcolo. Per qualsiasi lettura da un server di paging nella replica di calcolo, verrà visualizzato il tipo di attesa PAGEIOLATCH_, se è in attesa di tale I/O. In Hyperscale, questo tempo di attesa include sia il tempo necessario per recuperare la pagina richiesta nel server di paging per l'LSN richiesto e il tempo necessario per trasferire la pagina dal server di paging alla replica di calcolo.
- Letture di grandi dimensioni come read-ahead sono spesso fatte utilizzando ["Scatter-Gather" Reads](/sql/relational-databases/reading-pages/). Ciò consente letture di un massimo di 4 MB di pagine alla volta, considerate una singola lettura nel motore di SQL Server.This allows reads of up to 4 MB of pages at a time, considered a single read in the SQL Server engine. Tuttavia, quando i dati letti sono in RBPEX, queste letture vengono considerate come più letture individuali da 8 KB, poiché il pool di buffer e RBPEX utilizzano sempre pagine da 8 KB. Di conseguenza, il numero di I/O di lettura visualizzati con RBPEX può essere maggiore del numero effettivo di I/O eseguite dal motore.

### <a name="data-writes"></a>Scritture dati

- La replica di calcolo primaria non scrive direttamente nei server di paging. I record di log del servizio di registro vengono invece riprodotti nei server di paging corrispondenti. 
- Le scritture che si verificano nella replica di calcolo vengono scritture prevalentemente nell'RBPEX locale (file_id 0). Per le scritture su file logici di dimensioni superiori a 8 KB, in altre parole quelle eseguite utilizzando [Gather-write](/sql/relational-databases/writing-pages/), ogni operazione di scrittura viene convertita in più scritture individuali da 8 KB in RBPEX poiché il pool di buffer e RBPEX utilizzano sempre pagine da 8 KB. Di conseguenza, il numero di operazioni di I/O di scrittura visualizzate rispetto a RBPEX può essere maggiore del numero effettivo di Operazioni I/O eseguite dal motore.
- Anche i file non RBPEX o i file di dati diversi da file_id 0 che corrispondono ai server di paging mostrano le scritture. Nel livello di servizio Hyperscale, queste scritture vengono simulate, perché le repliche di calcolo non scrivono mai direttamente nei server di paging. Le operazioni di I/O al secondo di scrittura e la velocità effettiva vengono considerate così come si verificano nella replica di calcolo, ma la latenza per i file di dati diversi da file_id 0 non riflette la latenza effettiva delle scritture nel server di paging.

### <a name="log-writes"></a>Scritture di log

- Nel calcolo primario viene contabilizzata una scrittura log in file_id 2 di sys.dm_io_virtual_file_stats. Una scrittura di log nel calcolo primario è una scrittura nella zona di destinazione del log.
- I record del log non vengono sottoposti a protezione dell'applicazione di protezione nella replica secondaria in un commit. In Hyperscale, log viene applicato dal servizio di log alle repliche secondarie in modo asincrono. Poiché le scritture di log non vengono effettivamente eseguite nelle repliche secondarie, qualsiasi accounting di Operazioni di I/O log nelle repliche secondarie è solo a scopo di rilevamento.

## <a name="data-io-in-resource-utilization-statistics"></a>I/O dei dati nelle statistiche di utilizzo delle risorseData IO in resource utilization statistics

In un database non Hyperscale le operazioni di I/O al secondo combinate di lettura e scrittura rispetto ai file `avg_data_io_percent` di dati, in relazione al limite di IOPS per i dati di governance delle [risorse,](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) vengono segnalate nelle visualizzazioni [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats,](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nella colonna. Lo stesso valore viene riportato nel portale come _Data IO Percentage_. 

In un database Hyperscale, questa colonna genera report sull'utilizzo delle operazioni di I/O al `tempdb`secondo dei dati in relazione al limite per l'archiviazione locale solo nella replica di calcolo, in particolare i/o su RBPEX e . Un valore 100% in questa colonna indica che la governance delle risorse limita le operazioni di I/O al secondo di archiviazione locale. Se questo è correlato a un problema di prestazioni, ottimizzare il carico di lavoro per generare meno I/O o aumentare l'obiettivo del servizio di database per aumentare il [limite](sql-database-vcore-resource-limits-single-databases.md)di _IOPS max Data di_ governance delle risorse . Per la governance delle risorse di RBPEX legge e scrive, il sistema conta singole operazioni di I/O 8 KB, anziché i i/O più grandi che possono essere emessi dal motore di SQL Server. 

L'I/O dei dati rispetto ai server di paging remoti non viene segnalato nelle visualizzazioni di utilizzo delle risorse o nel portale, ma viene segnalato nel DMF [sys.dm_io_virtual_file_stats(),](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) come indicato in precedenza.


## <a name="additional-resources"></a>Risorse aggiuntive

- Per i limiti delle risorse vCore per un singolo database Hyperscale, vedere Limiti del [livello di servizio HyperscaleFor vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) resource limits for a Hyperscale single database see Hyperscale service tier vCore Limits
- Per l'ottimizzazione delle prestazioni del database SQL di Azure, vedere Prestazioni delle query nel database SQL di [AzureFor](sql-database-performance-guidance.md) Azure SQL Database performance tuning, see Query performance in Azure SQL Database
- Per ottimizzare le prestazioni con Query Store, vedere [Monitoraggio delle prestazioni tramite l'archivio queryFor](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/) performance tuning using Query Store, see Performance monitoring using Query store
- Per gli script di monitoraggio DMV, vedere Monitoraggio delle prestazioni Database SQL di [Azure tramite viste](sql-database-monitoring-with-dmvs.md) a gestione dinamicaFor DMV monitoring scripts, see Monitoring performance Azure SQL Database using dynamic management views
