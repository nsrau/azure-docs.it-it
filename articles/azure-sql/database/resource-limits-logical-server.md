---
title: Limiti delle risorse per i server logici in Azure
description: Questo articolo fornisce una panoramica dei limiti delle risorse per il server logico in Azure usato dal database SQL di Azure e da Azure sinapsi Analytics. Offre anche informazioni su cosa accade quando tali limiti delle risorse vengono raggiunti o superati.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 06/10/2020
ms.openlocfilehash: eac5814eb977a01135ad2fcd9551b3475673dbca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84691748"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Limiti delle risorse per il database SQL di Azure e i server di analisi di Azure sinapsi
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Questo articolo fornisce una panoramica dei limiti delle risorse per il server logico usato dal database SQL di Azure e da Azure sinapsi Analytics. Fornisce informazioni su ciò che accade quando i limiti delle risorse vengono raggiunti o superati e descrive i meccanismi di governance delle risorse usati per applicare questi limiti.

> [!NOTE]
> Per i limiti di Istanza gestita SQL di Azure, vedere [limiti delle risorse del database SQL per le istanze gestite](../managed-instance/resource-limits.md).

## <a name="maximum-resource-limits"></a>Limiti massimi delle risorse

| Risorsa | Limite |
| :--- | :--- |
| Database per server | 5000 |
| Numero predefinito di server per sottoscrizione in ogni area | 20 |
| Numero massimo di server per sottoscrizione in ogni area | 200 |  
| Quota DTU/eDTU per server | 54.000 |  
| Quota vCore per server/istanza | 540 |
| N. max pool per server | Limitato dal numero di DTU o vCore. Se ad esempio ogni pool è da 1000 DTU, un server può supportare 54 pool.|
|||

> [!IMPORTANT]
> Poiché il numero di database si avvicina al limite per ogni server, può verificarsi quanto segue:
>
> - Latenza in aumento nelle query in esecuzione nel database master.  Ciò include le visualizzazioni delle statistiche di utilizzo delle risorse, ad esempio sys.resource_stats.
> - Latenza in aumento nelle operazioni di gestione e nel portale di esecuzione del rendering dei punti di visualizzazione che coinvolgono l'enumerazione dei database nel server.

> [!NOTE]
> Per ottenere più quote DTU/eDTU, vCore quota o più server rispetto alla quantità predefinita, inviare una nuova richiesta di supporto nella portale di Azure. Per altre informazioni, vedere [Request quota increases for Azure SQL Database](quota-increase-request.md) (Richiedere aumenti di quota per il database SQL di Azure).

### <a name="storage-size"></a>Dimensioni dello spazio di archiviazione

Per le dimensioni di archiviazione delle risorse dei singoli database, fare riferimento ai limiti [delle risorse basate su DTU](resource-limits-dtu-single-databases.md) o ai [limiti delle risorse basate su vCore](resource-limits-vcore-single-databases.md) per i limiti delle dimensioni di archiviazione per ogni piano tariffario.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Cosa accade quando vengono raggiunti i limiti delle risorse del database?

### <a name="compute-cpu"></a>CPU di calcolo

Quando l'utilizzo della CPU per il calcolo del database diventa elevato, la latenza delle query aumenta e anche il timeout delle query. In queste condizioni, le query possono essere accodate dal servizio e vengono fornite le risorse per l'esecuzione perché le risorse diventano gratuite.
In caso di uso elevato di risorse di elaborazione, le opzioni di mitigazione includono:

- Aumento delle dimensioni di calcolo del database o del pool elastico per mettere a disposizione del database un numero maggiore di risorse del computer. Vedere [Ridimensionare le risorse del database singolo](single-database-scale.md) e [Ridimensionare le risorse del pool elastico](elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'utilizzo delle risorse della CPU di ogni query. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Archiviazione

Quando la quantità di spazio del database usato raggiunge il limite di dimensioni massime, gli inserimenti e gli aggiornamenti del database che aumentano la dimensione dei dati hanno esito negativo e i clienti ricevono un [messaggio di errore](troubleshoot-common-errors-issues.md). Le istruzioni SELECT e DELETE continuano ad avere esito positivo.

In caso di uso elevato di spazio, le opzioni di mitigazione includono:

- Aumentare le dimensioni massime del database o del pool elastico o aggiungere ulteriore spazio di archiviazione. Vedere [Ridimensionare le risorse del database singolo](single-database-scale.md) e [Ridimensionare le risorse del pool elastico](elastic-pool-scale.md).
- Se il database si trova in un pool elastico, in alternativa è possibile spostare il database all'esterno del pool in modo che lo spazio di archiviazione non sia condiviso con altri database.
- Compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](file-space-manage.md)

### <a name="sessions-and-workers-requests"></a>Sessioni e ruoli di lavoro (richieste)

Il numero massimo di sessioni e di ruoli di lavoro è determinato dal livello di servizio e dalle dimensioni di calcolo (DTU/edtu o VCore). Le nuove richieste vengono rifiutate quando vengono raggiunti i limiti delle sessioni o dei ruoli di lavoro e i clienti ricevono un messaggio di errore. Mentre il numero di connessioni disponibili può essere controllato dall'applicazione, il numero di ruoli di lavoro simultanei è spesso più difficile da stimare e da controllare. Ciò vale soprattutto durante i periodi di picco del carico quando vengono raggiunti i limiti delle risorse del database e i ruoli di lavoro si accumulano a causa di query con esecuzione prolungata, catene di blocco di grandi dimensioni o parallelismo eccessivo

In caso di uso elevato di sessioni o ruoli di lavoro, le opzioni di mitigazione includono:

- Aumento del livello di servizio o delle dimensioni di calcolo del database o del pool elastico. Vedere [Ridimensionare le risorse del database singolo](single-database-scale.md) e [Ridimensionare le risorse del pool elastico](elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query, se l'aumento dell'uso di ruoli di lavoro è dovuto a un conflitto delle risorse di elaborazione. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](performance-guidance.md#query-tuning-and-hinting).
- Riduzione dell'impostazione di [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (massimo grado di parallelismo).
- Ottimizzazione del carico di lavoro delle query per ridurre il numero di occorrenze e la durata del blocco di query.

### <a name="memory"></a>Memory

A differenza di altre risorse (CPU, ruoli di lavoro, archiviazione), raggiungere il limite di memoria non influisce negativamente sulle prestazioni delle query e non genera errori e errori. Come descritto in dettaglio in [Guida all'architettura di gestione della memoria](https://docs.microsoft.com/sql/relational-databases/memory-management-architecture-guide), il motore di database di SQL Server spesso usa tutta la memoria disponibile, in base alla progettazione. La memoria viene utilizzata principalmente per la memorizzazione nella cache dei dati, per evitare un accesso più costoso alle risorse di archiviazione. Pertanto, un utilizzo più elevato della memoria di solito migliora le prestazioni delle query a causa di letture più veloci dalla memoria, anziché letture più lente dall'archiviazione.

Dopo l'avvio del motore di database, quando il carico di lavoro inizia a leggere i dati dall'archivio, il motore di database memorizza nella cache i dati in memoria. Al termine di questo periodo iniziale, è normale e previsto che le `avg_memory_usage_percent` `avg_instance_memory_percent` colonne e in [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) siano vicine o uguali al 100%, in particolare per i database che non sono inattivi e che non si adattano completamente alla memoria.

Oltre alla cache dei dati, la memoria viene utilizzata in altri componenti del motore di database. Quando è richiesta la memoria e tutta la memoria disponibile è stata utilizzata dalla cache dei dati, il motore di database compatta in modo dinamico le dimensioni della cache dei dati per rendere disponibile la memoria ad altri componenti e aumenta dinamicamente la cache dei dati quando altri componenti rilasciano memoria.

In rari casi, un carico di lavoro sufficientemente impegnativo potrebbe causare una condizione di memoria insufficiente, causando errori di memoria insufficiente. Questa operazione può verificarsi a qualsiasi livello di utilizzo della memoria compreso tra 0% e 100%. È più probabile che si verifichino dimensioni di calcolo minori con limiti di memoria proporzionalmente inferiori e/o con carichi di lavoro che usano una maggiore quantità di memoria per l'elaborazione delle query, ad esempio in [pool elastici densi](elastic-pool-resource-management.md).

Quando si verificano errori di memoria insufficiente, le opzioni di mitigazione includono:
- Aumento del livello di servizio o delle dimensioni di calcolo del database o del pool elastico. Vedere [Ridimensionare le risorse del database singolo](single-database-scale.md) e [Ridimensionare le risorse del pool elastico](elastic-pool-scale.md).
- Ottimizzazione delle query e della configurazione per ridurre l'utilizzo della memoria. Nella tabella seguente sono descritte le soluzioni comuni.

|Soluzione|Descrizione|
| :----- | :----- |
|Ridurre le dimensioni delle concessioni di memoria|Per ulteriori informazioni sulle concessioni di memoria, vedere il post di Blog [informazioni sulla concessione di memoria SQL Server](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) . Una soluzione comune per evitare concessioni di memoria di dimensioni eccessive è mantenere aggiornate le [statistiche](https://docs.microsoft.com/sql/relational-databases/statistics/statistics) . Questo comporta stime più accurate dell'utilizzo di memoria da parte del motore di query, evitando concessioni di memoria inutilmente grandi.</br></br>Nei database che utilizzano il livello di compatibilità 140 e versioni successive, il motore di database può regolare automaticamente le dimensioni della concessione di memoria utilizzando il [feedback della concessione di memoria in modalità batch](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?view=sql-server-ver15#batch-mode-memory-grant-feedback) Nei database che utilizzano il livello di compatibilità 150 e versioni successive, il motore di database utilizza in modo analogo il [feedback delle concessioni di memoria in modalità riga](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?view=sql-server-ver15#row-mode-memory-grant-feedback), per query più comuni Questa funzionalità incorporata consente di evitare errori di memoria insufficiente a causa di concessioni di memoria inutilmente grandi.|
|Ridurre le dimensioni della cache dei piani di query|Il motore di database memorizza nella cache i piani di query in memoria per evitare di compilare un piano di query per ogni esecuzione della query. Per evitare il sovraccarico della cache dei piani di query causato da piani di memorizzazione nella cache che vengono usati una sola volta, abilitare la OPTIMIZE_FOR_AD_HOC_WORKLOADS [configurazione con ambito database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).|
|Ridurre le dimensioni della memoria di blocco|Il motore di database utilizza memoria per i [blocchi](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine). Quando possibile, evitare transazioni di grandi dimensioni che potrebbero acquisire un numero elevato di blocchi e causare un utilizzo elevato della memoria del blocco.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Consumo di risorse in base ai carichi di lavoro degli utenti e ai processi interni

L'utilizzo della CPU e della memoria da parte dei carichi di lavoro degli utenti in ogni database viene segnalato nelle viste [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) e [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) , nelle `avg_cpu_percent` `avg_memory_usage_percent` colonne e. Per i pool elastici, l'utilizzo delle risorse a livello di pool viene segnalato nella vista [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) . Il consumo di CPU del carico di lavoro dell'utente viene segnalato anche tramite la `cpu_percent` metrica di monitoraggio di Azure, per [database singoli](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) e [pool elastici](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) a livello di pool.

Il database SQL di Azure richiede risorse di calcolo per implementare le funzionalità di base del servizio, ad esempio disponibilità elevata e ripristino di emergenza, backup e ripristino del database, monitoraggio, Query Store, ottimizzazione automatica e così via. Il sistema accantona una determinata parte limitata delle risorse complessive per questi processi interni usando i meccanismi di [governance delle risorse](#resource-governance) , rendendo disponibili le restanti risorse per i carichi di lavoro degli utenti. In alcuni casi, quando i processi interni non usano risorse di calcolo, il sistema li rende disponibili per i carichi di lavoro degli utenti.

Il consumo totale di CPU e memoria da parte dei carichi di lavoro degli utenti e dei processi interni viene segnalato nelle viste [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) e [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) , nelle `avg_instance_cpu_percent` `avg_instance_memory_percent` colonne e. Questi dati vengono segnalati anche tramite `sqlserver_process_core_percent` le `sqlserver_process_memory_percent` metriche di monitoraggio di Azure e per i [database singoli](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) e i [pool elastici](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) a livello di pool.

Una suddivisione più dettagliata del consumo di risorse recenti da parte dei carichi di lavoro degli utenti e dei processi interni viene segnalata nelle viste [sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) e [sys. dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) . Per informazioni dettagliate sui pool di risorse e i gruppi del carico di lavoro a cui si fa riferimento in queste visualizzazioni, vedere [governance delle](#resource-governance) Queste visualizzazioni segnalano l'utilizzo delle risorse da parte dei carichi di lavoro degli utenti e dei processi interni specifici nei pool di risorse e nei gruppi del carico di lavoro associati.

Nel contesto del monitoraggio delle prestazioni e della risoluzione dei problemi, è importante considerare sia il **consumo della CPU utente** ( `avg_cpu_percent` ,) sia l' `cpu_percent` **utilizzo totale della CPU** da parte dei carichi di lavoro degli utenti e dei processi interni ( `avg_instance_cpu_percent` , `sqlserver_process_core_percent` ).

L' **utilizzo della CPU utente** viene calcolato come percentuale dei limiti del carico di lavoro dell'utente in ogni obiettivo di servizio. L' **utilizzo della CPU** da parte dell'utente al 100% indica che il carico di lavoro dell'utente ha raggiunto il limite dell'obiettivo di servizio. Tuttavia, quando l' **utilizzo totale della CPU** raggiunge l'intervallo del 70-100%, è possibile visualizzare la velocità effettiva del carico di lavoro dell'utente e aumentare la latenza delle query, anche se l' **utilizzo della CPU dell'utente** segnalato è significativamente inferiore al 100%. È più probabile che si verifichi quando si usano obiettivi di servizio più piccoli con un'allocazione moderata di risorse di calcolo, ma carichi di lavoro utente relativamente intensi, ad esempio in [pool elastici densi](elastic-pool-resource-management.md). Questo può verificarsi anche con obiettivi di servizio più piccoli quando i processi interni richiedono temporaneamente risorse aggiuntive, ad esempio quando si crea una nuova replica del database.

Quando l' **utilizzo totale della CPU** è elevato, le opzioni di mitigazione sono le stesse indicate in precedenza e includono l'aumento dell'obiettivo di servizio e/o l'ottimizzazione del carico di lavoro dell'utente.

## <a name="resource-governance"></a>Governance delle risorse

Per applicare i limiti delle risorse, il database SQL di Azure usa un'implementazione di governance delle risorse basata su SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), modificata ed estesa per l'esecuzione nel database SQL di Azure. Nel database SQL, più [pool di risorse](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) e [gruppi di carico di lavoro](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group), con limiti di risorse impostati a livello di pool e di gruppi, forniscono un [database come servizio bilanciato](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). I carichi di lavoro e i carichi di lavoro interni sono classificati in pool di risorse e gruppi di carico di lavoro distinti Il carico di lavoro dell'utente sulle repliche secondarie primarie e leggibili, incluse le repliche geografiche, è classificato nel `SloSharedPool1` pool di risorse e nel `UserPrimaryGroup.DBId[N]` gruppo del carico di lavoro, dove `N` corrisponde al valore ID del database. Sono inoltre disponibili più pool di risorse e gruppi di carico di lavoro per vari carichi di lavoro interni.

Oltre a usare Resource Governor per gestire le risorse all'interno del processo SQL, il database SQL di Azure usa anche [oggetti processo](https://docs.microsoft.com/windows/win32/procthread/job-objects) di Windows per la governance delle risorse a livello di processo e il [File server Windows Gestione risorse (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) per la gestione delle quote di archiviazione.

La governance delle risorse del database SQL di Azure è di natura gerarchica. Dall'alto verso il basso, i limiti vengono applicati a livello di sistema operativo e a livello di volume di archiviazione utilizzando i meccanismi di governance delle risorse del sistema operativo e Resource Governor, quindi a livello del pool di risorse utilizzando Resource Governor e quindi a livello di gruppo del carico di lavoro utilizzando Resource Governor. I limiti di governance delle risorse attivati per il database o il pool elastico corrente vengono visualizzati nella vista [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) .

### <a name="data-io-governance"></a>Governance IO dati

La governance IO dei dati è un processo nel database SQL di Azure usato per limitare l'i/o fisico di lettura e scrittura sui file di dati di un database. I limiti di IOPS vengono impostati per ogni livello di servizio in modo da ridurre al minimo l'effetto "rumoroso vicino", per garantire l'equità di allocazione delle risorse nel servizio multi-tenant e per restare all'interno delle funzionalità dell'hardware e dell'archiviazione sottostanti.

Per i database singoli, i limiti dei gruppi di carico di lavoro vengono applicati a tutti i/o di archiviazione nel database, mentre i limiti del pool di risorse si applicano a tutte le operazioni di i/o di archiviazione su tutti i database nello stesso pool SQL `tempdb` Per i pool elastici, i limiti dei gruppi di carico di lavoro si applicano a ogni database nel pool, mentre il limite del pool di risorse si applica all'intero pool elastico, incluso il `tempdb` database, che è condiviso tra tutti i database nel pool. In generale, i limiti del pool di risorse potrebbero non essere raggiungibili dal carico di lavoro rispetto a un database (singolo o in pool), perché i limiti del gruppo di carico di lavoro sono inferiori ai limiti del pool di risorse e limitano IOPS/velocità effettiva. Tuttavia, i limiti del pool possono essere raggiunti dal carico di lavoro combinato rispetto a più database nello stesso pool.

Se, ad esempio, una query genera 1000 IOPS senza governance delle risorse di i/o, ma il limite massimo di IOPS del gruppo di carico di lavoro è impostato su 900 IOPS, la query non sarà in grado di generare più di 900 IOPS. Tuttavia, se il limite massimo di IOPS del pool di risorse è impostato su 1500 IOPS e l'i/o totale da tutti i gruppi di carico di lavoro associati al pool di risorse supera 1500 IOPS, l'i/o della stessa query può essere ridotto al di sotto del limite del gruppo di lavoro di 900 IOPS.

I valori min/max di IOPS e velocità effettiva restituiti dalla vista [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) agiscono come limiti/maiuscole, non come garanzie. Inoltre, la governance delle risorse non garantisce una latenza di archiviazione specifica. La latenza, i IOPS e la velocità effettiva migliori per un determinato carico di lavoro degli utenti dipendono non solo dai limiti di governance delle risorse di i/o, ma anche dalla combinazione delle dimensioni di i/o utilizzate e dalle funzionalità dell'archiviazione sottostante. Il database SQL USA IOs che variano in base alle dimensioni comprese tra 512 KB e 4 MB. Ai fini dell'applicazione dei limiti di IOPS, ogni IO viene considerato indipendentemente dalle dimensioni, ad eccezione dei database con file di dati in archiviazione di Azure. In tal caso, IOs di dimensioni superiori a 256 KB vengono conteggiati come più 256 KB per l'allineamento con l'accounting di i/o di archiviazione di Azure.

Per i database Basic, standard e per utilizzo generico, che usano file di dati in archiviazione di Azure, il `primary_group_max_io` valore potrebbe non essere raggiungibile se un database non dispone di un numero di file di dati sufficiente per fornire cumulativamente questo numero di IOPS o se i dati non vengono distribuiti in modo uniforme tra i file o se il livello di prestazioni dei BLOB sottostanti limita IOPS/velocità effettiva al di sotto del limite di Analogamente, con log IOs di piccole dimensioni generato da commit di transazione frequente, il `primary_max_log_rate` valore potrebbe non essere raggiungibile da un carico di lavoro a causa del limite di IOPS nel BLOB di archiviazione di Azure sottostante.

I valori di utilizzo delle risorse, ad esempio `avg_data_io_percent` e `avg_log_write_percent` , riportati nelle viste [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)e [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , vengono calcolati come percentuali dei limiti massimi di governance delle risorse. Pertanto, quando i fattori diversi dalla governance delle risorse limitano le operazioni di i/o al secondo, è possibile vedere IOPS/velocità effettiva flat e latenze che aumentano con l'aumentare del carico di lavoro, anche se l'utilizzo delle risorse segnalato rimane inferiore al 100%.

Per visualizzare IOPS, velocità effettiva e latenza di lettura e scrittura per ogni file di database, usare la funzione [sys. dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Questa funzione copre tutti i/o nel database, inclusi i/o in background che non sono stati conteggiati `avg_data_io_percent` , ma usa IOPS e la velocità effettiva dell'archiviazione sottostante e può influisca sulla latenza di archiviazione osservata. La funzione presenta anche una latenza aggiuntiva che può essere introdotta dalla governance delle risorse di i/o per le operazioni di lettura e scrittura, `io_stall_queued_read_ms` `io_stall_queued_write_ms` rispettivamente nelle colonne e.

### <a name="transaction-log-rate-governance"></a>Governance della frequenza del log delle transazioni

La gestione della frequenza dei log delle transazioni è un processo nel database SQL di Azure usato per limitare le velocità di inserimento elevate per i carichi di lavoro, ad esempio le compilazioni BULK INSERT, SELECT INTO e index. Questi limiti vengono rilevati e applicati al livello di sottosecondo alla frequenza di generazione dei record di log, limitando la velocità effettiva indipendentemente dal numero di IOs che possono essere emessi sui file di dati.  Le frequenze di generazione del log delle transazioni attualmente si scalano in modo lineare fino a un punto dipendente dall'hardware, con la frequenza massima di log consentita di 96 MB/s con il modello di acquisto vCore.

> [!NOTE]
> Il valore effettivo di IOs per i file di log delle transazioni non è regolato o limitato.

Le frequenze dei log sono impostate in modo che possano essere realizzate e prolungate in diversi scenari, mentre il sistema generale può mantenerne le funzionalità con un effetto ridotto al carico dell'utente. La governance della frequenza dei log garantisce che i backup del log delle transazioni restino entro i contratti di sicurezza di ripristino pubblicati.  Questa governance impedisce anche un backlog eccessivo nelle repliche secondarie.

Quando vengono generati i record di log, ogni operazione viene valutata e valutata per determinare se deve essere ritardata al fine di mantenere una velocità massima di log desiderata (MB/s al secondo). I ritardi non vengono aggiunti quando i record del log vengono scaricati nella risorsa di archiviazione, ma la governance della frequenza dei log viene applicata durante la generazione della velocità del log.

Le velocità effettive di generazione dei log imposte in fase di esecuzione possono anche essere influenzate dai meccanismi di feedback, riducendo temporaneamente le frequenze di log consentite in modo che il sistema possa stabilizzarsi Gestione dello spazio del file di log, evitando che le condizioni di spazio del log e i meccanismi di replica dei gruppi di disponibilità possano ridurre temporaneamente i limiti di sistema complessivi.

Il data shaping di log rate Governor viene esposto tramite i tipi di attesa seguenti (esposti nelle viste [sys. dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) ):

| Tipo di attesa | Note |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitazione del database |
| POOL_LOG_RATE_GOVERNOR | Limitazione del pool |
| INSTANCE_LOG_RATE_GOVERNOR | Limitazione a livello di istanza |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controllo feedback, replica fisica del gruppo di disponibilità in Premium/business critical non in linea |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controllo feedback, limitazione delle tariffe per evitare una condizione di spazio del log esaurita |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Controllo della replica geografica, limitazione della velocità di log per evitare la latenza elevata dei dati e la mancata disponibilità dei database secondari|
|||

Quando si verifica un limite di velocità di log che ostacola la scalabilità desiderata, prendere in considerazione le opzioni seguenti:

- Scalabilità fino a un livello di servizio superiore per ottenere la velocità massima di log di 96 MB/s oppure passare a un livello di servizio diverso. Il livello di servizio di [iperscalabilità](service-tier-hyperscale.md) offre una velocità di log di 100 MB/s indipendentemente dal livello di servizio scelto.
- Se i dati caricati sono temporanei, ad esempio i dati di staging in un processo ETL, possono essere caricati in tempdb (con registrazione minima).
- Per gli scenari di analisi, caricare in una tabella analizzata columnstore cluster. In questo modo si riduce la velocità di log necessaria a causa della compressione. Questa tecnica aumenta l'utilizzo della CPU ed è applicabile solo ai set di dati che traggono vantaggio dagli indici columnstore cluster.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Per informazioni su DTU ed eDTU, vedere [DTU ed eDTU](purchasing-models.md#dtu-based-purchasing-model).
- Per informazioni sui limiti di tempdb relativi alle dimensioni, vedere [TempDB nel database SQL di Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
 