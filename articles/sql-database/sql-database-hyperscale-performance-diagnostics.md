---
title: Diagnostica delle prestazioni in iperscalabilità
description: Questo articolo descrive come risolvere i problemi di prestazioni di iperscalabilità nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: a7c64284c958fa8b3ec89c2b27515fe167a04011
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811156"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnostica per la risoluzione dei problemi delle prestazioni di scalabilità SQL


Per risolvere i problemi di prestazioni in un database con iperscalabilità, le [metodologie generali di ottimizzazione delle prestazioni](sql-database-monitor-tune-overview.md) nel nodo di calcolo del database SQL di Azure sono il punto di partenza per l'analisi delle prestazioni. Tuttavia, data l' [architettura distribuita](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) di iperscalabilità, sono state aggiunte ulteriori informazioni di diagnostica. Questo articolo descrive i dati di diagnostica specifici dell'iperscalabilità.


## <a name="log-rate-throttling-waits"></a>Attese limitazione frequenza log


Ogni livello di servizio del database SQL di Azure ha limiti di velocità di generazione del log applicati tramite la [governance della frequenza dei log](sql-database-resource-limits-database-server.md#transaction-log-rate-governance). In iperscalabilità, il limite di generazione dei log è attualmente impostato su 100 MB/sec, indipendentemente dal livello di servizio. In alcuni casi, tuttavia, la velocità di generazione del log nella replica di calcolo primaria deve essere limitata per mantenere i contratti di ripristino. Questa limitazione si verifica quando un [server di pagine o un'altra replica di calcolo](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) è significativamente alla base dell'applicazione di nuovi record di log dal servizio di log.

I tipi di attesa seguenti (in [sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) descrivono i motivi per cui la frequenza dei log può essere limitata alla replica di calcolo primaria:

|Tipo di attesa    |Descrizione                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Si verifica quando la velocità di generazione del log del nodo di calcolo primario di un database iperscala viene limitata a causa del consumo di log ritardato nei server di paging.         |
|RBIO_RG_DESTAGE        | Si verifica quando viene limitata una velocità di generazione del log del nodo di calcolo di un database iperscalare a causa del consumo di log ritardato da parte dell'archiviazione dei log a lungo termine.         |
|RBIO_RG_REPLICA        | Si verifica quando una velocità di generazione del log del nodo di calcolo di un database iperscalare viene limitata a causa del consumo di log ritardato da parte delle repliche secondarie leggibili.         |
|RBIO_RG_LOCALDESTAGE   | Si verifica quando viene limitata una velocità di generazione del log del nodo di calcolo di un database iperscalare a causa del consumo di log ritardato da parte del servizio di log.         |


## <a name="page-server-reads"></a>Letture del server di paging

Le repliche di calcolo non memorizzano nella cache una copia completa del database in locale. I dati locali per la replica di calcolo vengono archiviati nel pool di buffer (in memoria) e nella cache di estensione del pool di buffer resiliente locale (RBPEX) che è una cache parziale (non di copertura) delle pagine di dati. Questa cache RBPEX locale è dimensionata proporzionalmente alle dimensioni di calcolo ed è di 3 volte la memoria del livello di calcolo. RBPEX è simile al pool di buffer in quanto contiene i dati a cui si accede più di frequente. Ogni server della pagina, d'altra parte, presenta una cache RBPEX per la parte del database che mantiene.
 
Quando viene eseguita una lettura in una replica di calcolo, se i dati non esistono nel pool di buffer o nella cache RBPEX locale, viene eseguita una chiamata di funzione GetPage (pageId, LSN) e la pagina viene recuperata dal server di paging corrispondente. Le letture dai server della pagina sono letture remote e sono quindi più lente delle letture del RBPEX locale. Quando si risolvono i problemi di prestazioni correlati all'i/o, è necessario essere in grado di indicare il numero di IOs eseguiti tramite letture del server della pagina remota relativamente più lente.

Diversi DMV ed eventi estesi contengono colonne e campi che specificano il numero di letture remote da un server di pagine che possono essere confrontate con le letture totali. 

- Le colonne delle letture del server della pagina del report sono disponibili nell'esecuzione DMV, ad esempio:
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys. dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
- Le letture del server di paging vengono aggiunte agli eventi estesi seguenti:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - query-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads vengono aggiunti al piano di query XML per i piani effettivi. Ad esempio:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Per visualizzare questi attributi nella finestra delle proprietà del piano di query in SSMS è necessario SQL Server Management Studio 18,3 o versione successiva.


## <a name="virtual-file-stats-and-io-accounting"></a>Statistiche di file virtuali e accounting IO

Nel database SQL di Azure, la DMF [sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) è il modo principale per monitorare SQL Server io. Le caratteristiche di i/o sull'iperscala sono diverse a causa dell' [architettura distribuita](sql-database-service-tier-hyperscale.md#distributed-functions-architecture). Questa sezione è incentrata sulle operazioni di i/o (letture e scritture) in file di dati, come illustrato in questa DMF. In iperscalabilità ogni file di dati visibile in questa DMF corrisponde a un server di pagina remoto. La cache di RBPEX indicata qui è una cache locale basata su SSD che è una cache non coprente nella replica di calcolo.


### <a name="local-rbpex-cache-usage"></a>Utilizzo locale della cache RBPEX

La cache RBPEX locale esiste nel nodo di calcolo nell'archivio SSD locale. Quindi, i/o in questa cache RBPEX sono più veloci di IO nei server di pagine remote. Attualmente, [sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) in un database con iperscalabilità ha una riga speciale che segnala l'i/o eseguito nella cache RBPEX locale della replica di calcolo. Questa riga ha il valore 0 per le colonne `database_id` e `file_id`. Ad esempio, la query seguente restituisce le statistiche di utilizzo di RBPEX dall'avvio del database.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Un rapporto tra le letture eseguite su RBPEX e le letture aggregate eseguite su tutti gli altri file di dati fornisce la percentuale di riscontri nella cache di RBPEX.


### <a name="data-reads"></a>Letture dei dati

- Quando le letture vengono rilasciate dal motore di SQL Server in una replica di calcolo, possono essere servite dalla cache RBPEX locale o dai server della pagina remota o da una combinazione dei due se si leggono più pagine.
- Quando la replica di calcolo legge alcune pagine da un file specifico, ad esempio file_id 1, se i dati risiedono esclusivamente nella cache RBPEX locale, tutte le operazioni di i/o per questa lettura vengono contabilizzate rispetto file_id 0 (RBPEX). Se una parte di tali dati si trova nella cache RBPEX locale e alcune parti si trova in un server della pagina remota, i/o vengono conteggiati verso file_id 0 per la parte fornita da RBPEX e la parte fornita dal server della pagina remota viene conteggiata per file_id 1. 
- Quando una replica di calcolo richiede una pagina in un determinato [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) da un server di pagina, se il server della pagina non è stato aggiornato al numero LSN richiesto, la lettura nella replica di calcolo resterà in attesa finché il server della pagina non verrà aggiornato prima che la pagina venga restituita alla replica di calcolo. Per tutte le operazioni di lettura da un server di pagina nella replica di calcolo, verrà visualizzato il tipo di attesa PAGEIOLATCH_ * se è in attesa su tale i/o. Questo tempo di attesa include sia il tempo necessario per recuperare la pagina richiesta nel server della pagina fino al numero LSN necessario, sia il tempo necessario per trasferire la pagina dal server della pagina alla replica di calcolo.
- Le letture di grandi dimensioni, ad esempio Read-ahead, vengono spesso eseguite utilizzando le [letture "scatter-gather"](/sql/relational-databases/reading-pages/). Questo consente letture di un massimo di 4 MB di pagine alla volta, considerate come singole letture nel motore SQL Server. Tuttavia, quando i dati letti si trovano in RBPEX, queste letture vengono contabilizzate come più letture singole di 8 KB, dal momento che il pool di buffer e RBPEX utilizzano sempre pagine da 8 KB. Come risultato, il numero di letture IOs visualizzate rispetto a RBPEX potrebbe essere maggiore del numero effettivo di IOs eseguito dal motore.


### <a name="data-writes"></a>Scritture dati

- La replica di calcolo primaria non scrive direttamente nei server delle pagine. Al contrario, i record del log del servizio log vengono riprodotti nei server della pagina corrispondenti. 
- Le scritture eseguite nella replica di calcolo sono prevalentemente scritte nel RBPEX locale (file_id 0). Per le Scritture su file logici di dimensioni maggiori di 8 KB, ovvero quelle eseguite utilizzando [gather-Write](/sql/relational-databases/writing-pages/), ogni operazione di scrittura viene convertita in più 8 KB singole scritture in RBPEX, poiché il pool di buffer e RBPEX utilizzano sempre pagine da 8 KB. Come risultato, il numero di scritture IOs visualizzate rispetto a RBPEX potrebbe essere maggiore del numero effettivo di IOs eseguito dal motore.
- Anche i file non RBPEX o i file di dati diversi da file_id 0 che corrispondono ai server della pagina mostrano le Scritture. Nel livello di servizio iperscalare, queste scritture vengono simulate, perché le repliche di calcolo non scrivono mai direttamente nei server delle pagine. Le operazioni di i/o al secondo e la velocità effettiva sono contabilizzate quando si verificano nella replica di calcolo, ma la latenza per i file di dati diversi da file_id 0 non riflette la latenza effettiva delle Scritture del server di

### <a name="log-writes"></a>Scritture log

- Nel calcolo primario, una scrittura di log viene rappresentata in file_id 2 di sys. dm_io_virtual_file_stats. Una scrittura di log nel calcolo primario è una scrittura nell'area di destinazione del log.
- I record di log non vengono finalizzati alla replica secondaria su un commit. In iperscalabilità, il log viene applicato dal servizio xlog alle repliche remote. Poiché le Scritture del log non si verificano effettivamente sulle repliche secondarie, qualsiasi contabilità delle operazioni di i/o di log nelle repliche secondarie è esclusivamente a scopo di rilevamento.

## <a name="additional-resources"></a>Risorse aggiuntive

- Per i limiti delle risorse di vCore per un singolo database iperscalabile, vedere [limiti del livello di servizio vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- Per ottimizzare le prestazioni del database SQL di Azure, vedere [prestazioni delle query nel database SQL di Azure](sql-database-performance-guidance.md)
- Per ottimizzare le prestazioni con Query Store, vedere [monitoraggio delle prestazioni con query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- Per gli script di monitoraggio DMV, vedere [monitoraggio delle prestazioni del database SQL di Azure tramite le viste a gestione dinamica](sql-database-monitoring-with-dmvs.md)
