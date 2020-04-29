---
title: Limiti delle risorse del database SQL di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica dei limiti delle risorse del database SQL di Azure per database singoli e pool elastici. Offre anche informazioni su cosa accade quando tali limiti delle risorse vengono raggiunti o superati.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: afb30a17d7a1450f169402c18f41ce249415e89d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804827"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Limiti delle risorse del database SQL e governance delle risorse

Questo articolo offre una panoramica dei limiti delle risorse del database SQL per un server di database SQL che gestisce database singoli e pool elastici. Fornisce informazioni su ciò che accade quando i limiti delle risorse vengono raggiunti o superati e descrive i meccanismi di governance delle risorse usati per applicare questi limiti.

> [!NOTE]
> Per i limiti di Istanza gestita, vedere [Limiti delle risorse del database SQL per le istanze gestite](sql-database-managed-instance-resource-limits.md).

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
> Poiché il numero di database si avvicina al limite per server di database SQL, può verificarsi quanto segue:
>
> - Latenza in aumento nelle query in esecuzione nel database master.  Ciò include le visualizzazioni delle statistiche di utilizzo delle risorse, ad esempio sys.resource_stats.
> - Latenza in aumento nelle operazioni di gestione e nel portale di esecuzione del rendering dei punti di visualizzazione che coinvolgono l'enumerazione dei database nel server.

> [!NOTE]
> Per ottenere più quote DTU/eDTU, vCore quota o più server rispetto alla quantità predefinita, inviare una nuova richiesta di supporto nella portale di Azure. Per altre informazioni, vedere [incremento della quota di richieste per il database SQL di Azure](quota-increase-request.md).

### <a name="storage-size"></a>Dimensioni dello spazio di archiviazione

Per le dimensioni di archiviazione delle risorse dei singoli database, fare riferimento ai limiti [delle risorse basate su DTU](sql-database-dtu-resource-limits-single-databases.md) o ai [limiti delle risorse basate su vCore](sql-database-vcore-resource-limits-single-databases.md) per i limiti delle dimensioni di archiviazione per ogni piano tariffario.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Cosa accade quando vengono raggiunti i limiti delle risorse del database?

### <a name="compute-dtus-and-edtus--vcores"></a>Elaborazione (DTU ed eDTU/vCore)

Quando l'utilizzo del calcolo del database (misurato da DTU e edtu, o VCore) diventa elevato, la latenza delle query aumenta e il timeout delle query può addirittura essere esaurito. In queste condizioni, le query possono essere accodate dal servizio e vengono fornite le risorse per l'esecuzione perché le risorse diventano gratuite.
In caso di uso elevato di risorse di elaborazione, le opzioni di mitigazione includono:

- Aumento delle dimensioni di calcolo del database o del pool elastico per mettere a disposizione del database un numero maggiore di risorse del computer. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'utilizzo delle risorse di ogni query. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Archiviazione

Quando la quantità di spazio del database usato raggiunge il limite di dimensioni massime, gli inserimenti e gli aggiornamenti del database che aumentano la dimensione dei dati hanno esito negativo e i clienti ricevono un [messaggio di errore](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Le istruzioni SELECT e DELETE continuano ad avere esito positivo.

In caso di uso elevato di spazio, le opzioni di mitigazione includono:

- Aumentare le dimensioni massime del database o del pool elastico o aggiungere ulteriore spazio di archiviazione. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Se il database è in un pool elastico, in alternativa può essere spostato all'esterno del pool in modo che lo spazio di archiviazione non venga condiviso con altri database.
- Compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioni e ruoli di lavoro (richieste)

Il numero massimo di sessioni e di ruoli di lavoro è determinato dal livello di servizio e dalle dimensioni di calcolo (DTU/edtu o VCore). Le nuove richieste vengono rifiutate quando vengono raggiunti i limiti delle sessioni o dei ruoli di lavoro e i clienti ricevono un messaggio di errore. Mentre il numero di connessioni disponibili può essere controllato dall'applicazione, il numero di ruoli di lavoro simultanei è spesso più difficile da stimare e da controllare. Ciò vale soprattutto durante i periodi di picco del carico quando vengono raggiunti i limiti delle risorse del database e i ruoli di lavoro si accumulano a causa di query con esecuzione prolungata, catene di blocco di grandi dimensioni o parallelismo eccessivo

In caso di uso elevato di sessioni o ruoli di lavoro, le opzioni di mitigazione includono:

- Aumento del livello di servizio o delle dimensioni di calcolo del database o del pool elastico. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query, se l'aumento dell'uso di ruoli di lavoro è dovuto a un conflitto delle risorse di elaborazione. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).
- Riduzione dell'impostazione di [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (massimo grado di parallelismo).
- Ottimizzazione del carico di lavoro delle query per ridurre il numero di occorrenze e la durata del blocco di query.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Consumo di risorse in base ai carichi di lavoro degli utenti e ai processi interni

L'utilizzo della CPU e della memoria da parte dei carichi di lavoro degli utenti in ogni database viene segnalato nelle viste [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) e `avg_memory_usage_percent` [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) , nelle `avg_cpu_percent` colonne e. Per i pool elastici, l'utilizzo delle risorse a livello di pool viene segnalato nella vista [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) . Il consumo di CPU del carico di lavoro dell' `cpu_percent` utente viene segnalato anche tramite la metrica di monitoraggio di Azure, per [database singoli](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) e [pool elastici](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) a livello di pool.

Il database SQL di Azure richiede risorse di calcolo per implementare le funzionalità di base del servizio, ad esempio disponibilità elevata e ripristino di emergenza, backup e ripristino del database, monitoraggio, Query Store, ottimizzazione automatica e così via. Il sistema accantona una determinata parte limitata delle risorse complessive per questi processi interni usando i meccanismi di [governance delle risorse](#resource-governance) , rendendo disponibili le restanti risorse per i carichi di lavoro degli utenti. In alcuni casi, quando i processi interni non usano risorse di calcolo, il sistema li rende disponibili per i carichi di lavoro degli utenti.

Il consumo totale di CPU e memoria da parte dei carichi di lavoro degli utenti e dei processi interni nell'istanza SQL Server che ospita un database singolo o in un pool elastico viene segnalato nelle viste [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) e [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) , nelle `avg_instance_cpu_percent` colonne e `avg_instance_memory_percent` . Questi dati vengono segnalati anche tramite `sqlserver_process_core_percent` le `sqlserver_process_memory_percent` metriche di monitoraggio di Azure e per i [database singoli](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) e i [pool elastici](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) a livello di pool.

Una suddivisione più dettagliata del consumo di risorse recenti da parte dei carichi di lavoro degli utenti e dei processi interni viene segnalata nelle viste [sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) e [sys. dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) . Per informazioni dettagliate sui pool di risorse e i gruppi del carico di lavoro a cui si fa riferimento in queste visualizzazioni, vedere [governance delle](#resource-governance) Queste visualizzazioni segnalano l'utilizzo delle risorse da parte dei carichi di lavoro degli utenti e dei processi interni specifici nei pool di risorse e nei gruppi del carico di lavoro associati.

Nel contesto del monitoraggio delle prestazioni e della risoluzione dei problemi, è importante considerare sia il **consumo della CPU utente** (`avg_cpu_percent` `cpu_percent`,) sia l' **utilizzo totale della CPU** da parte dei carichi di`avg_instance_cpu_percent`lavoro`sqlserver_process_core_percent`degli utenti e dei processi interni (,).

L' **utilizzo della CPU utente** viene calcolato come percentuale dei limiti del carico di lavoro dell'utente in ogni obiettivo di servizio. L' **utilizzo della CPU** da parte dell'utente al 100% indica che il carico di lavoro dell'utente ha raggiunto il limite dell'obiettivo di servizio. Tuttavia, quando l' **utilizzo totale della CPU** raggiunge l'intervallo del 70-100%, è possibile visualizzare la velocità effettiva del carico di lavoro dell'utente e aumentare la latenza delle query, anche se il **consumo di CPU dell'utente** segnalato rimane significativamente inferiore al 100%. È più probabile che si verifichi quando si usano obiettivi di servizio più piccoli con un'allocazione moderata di risorse di calcolo, ma carichi di lavoro utente relativamente intensi, ad esempio in [pool elastici densi](sql-database-elastic-pool-resource-management.md). Questo può verificarsi anche con obiettivi di servizio più piccoli quando i processi interni richiedono temporaneamente risorse aggiuntive, ad esempio quando si crea una nuova replica del database.

Quando l' **utilizzo totale della CPU** è elevato, le opzioni di mitigazione sono le stesse indicate in precedenza e includono l'aumento dell'obiettivo di servizio e/o l'ottimizzazione del carico di lavoro dell'utente.

## <a name="resource-governance"></a>Governance delle risorse

Per applicare i limiti delle risorse, il database SQL di Azure usa un'implementazione di governance delle risorse basata su SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), modificata ed estesa per l'esecuzione di un servizio di database SQL Server in Azure. In ogni istanza SQL Server nel servizio sono presenti più pool di [risorse](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) e [gruppi di carico di lavoro](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group), con limiti di risorse impostati a livello di pool e di gruppo per fornire un [database come servizio bilanciato](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). I carichi di lavoro e i carichi di lavoro interni sono classificati in pool di risorse e gruppi di carico di lavoro distinti Il carico di lavoro dell'utente sulle repliche secondarie primarie e leggibili, incluse le repliche `SloSharedPool1` geografiche, `UserPrimaryGroup.DBId[N]` è classificato nel pool `N` di risorse e nel gruppo del carico di lavoro, dove corrisponde al valore ID del database. Sono inoltre disponibili più pool di risorse e gruppi di carico di lavoro per vari carichi di lavoro interni.

Oltre a usare Resource Governor per gestire le risorse all'interno del processo di SQL Server, il database SQL di Azure usa anche [oggetti processo](https://docs.microsoft.com/windows/win32/procthread/job-objects) di Windows per la governance delle risorse a livello di processo e Windows [file server Gestione risorse (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) per la gestione delle quote di archiviazione.

La governance delle risorse del database SQL di Azure è di natura gerarchica. Dall'alto verso il basso, i limiti vengono applicati a livello di sistema operativo e a livello di volume di archiviazione utilizzando i meccanismi di governance delle risorse del sistema operativo e Resource Governor, quindi a livello del pool di risorse utilizzando Resource Governor e quindi a livello di gruppo del carico di lavoro utilizzando Resource Governor. I limiti di governance delle risorse attivati per il database o il pool elastico corrente vengono visualizzati nella vista [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . 

### <a name="data-io-governance"></a>Governance IO dati

La governance IO dei dati è un processo nel database SQL di Azure usato per limitare l'i/o fisico di lettura e scrittura sui file di dati di un database. I limiti di IOPS vengono impostati per ogni livello di servizio in modo da ridurre al minimo l'effetto "rumoroso vicino", per garantire l'equità di allocazione delle risorse nel servizio multi-tenant e per restare all'interno delle funzionalità dell'hardware e dell'archiviazione sottostanti.

Per i database singoli, i limiti dei gruppi di carico di lavoro vengono applicati a tutti i/o di archiviazione nel database, mentre i `tempdb` limiti del pool di risorse si applicano a tutti i/o di archiviazione su tutti i database nella stessa istanza di SQL Server, incluso Per i pool elastici, i limiti dei gruppi di carico di lavoro si applicano a ogni database nel pool, mentre il limite del pool di `tempdb` risorse si applica all'intero pool elastico, incluso il database, che è condiviso tra tutti i database nel pool. In generale, i limiti del pool di risorse potrebbero non essere raggiungibili dal carico di lavoro rispetto a un database (singolo o in pool), perché i limiti del gruppo di carico di lavoro sono inferiori ai limiti del pool di risorse e limitano IOPS/velocità effettiva. Tuttavia, i limiti del pool possono essere raggiunti dal carico di lavoro combinato rispetto a più database nella stessa istanza di SQL Server.

Se, ad esempio, una query genera 1000 IOPS senza governance delle risorse di i/o, ma il limite massimo di IOPS del gruppo di carico di lavoro è impostato su 900 IOPS, la query non sarà in grado di generare più di 900 IOPS. Tuttavia, se il limite massimo di IOPS del pool di risorse è impostato su 1500 IOPS e l'i/o totale da tutti i gruppi di carico di lavoro associati al pool di risorse supera 1500 IOPS, l'i/o della stessa query può essere ridotto al di sotto del limite del gruppo di lavoro di 900 IOPS.

I valori min/max di IOPS e velocità effettiva restituiti dalla vista [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) agiscono come limiti/maiuscole, non come garanzie. Inoltre, la governance delle risorse non garantisce una latenza di archiviazione specifica. La latenza, i IOPS e la velocità effettiva migliori per un determinato carico di lavoro degli utenti dipendono non solo dai limiti di governance delle risorse di i/o, ma anche dalla combinazione delle dimensioni di i/o utilizzate e dalle funzionalità dell'archiviazione sottostante. SQL Server usa IOs che variano in dimensioni comprese tra 512 KB e 4 MB. Ai fini dell'applicazione dei limiti di IOPS, ogni IO viene considerato indipendentemente dalle dimensioni, ad eccezione dei database con file di dati in archiviazione di Azure. In tal caso, IOs di dimensioni superiori a 256 KB vengono conteggiati come più 256 KB IOs, per essere allineati con l'account di i/o di archiviazione di Azure.

Per i database Basic, standard e per utilizzo generico, che usano file di dati in archiviazione di Azure `primary_group_max_io` , il valore potrebbe non essere raggiungibile se un database non dispone di un numero di file di dati sufficiente per fornire cumulativamente questo numero di IOPS o se i dati non vengono distribuiti in modo uniforme tra i file o se il livello di prestazioni dei BLOB sottostanti limita IOPS/velocità effettiva sotto il limite Analogamente, con log IOs di piccole dimensioni generato da commit di `primary_max_log_rate` transazione frequente, il valore potrebbe non essere raggiungibile da un carico di lavoro a causa del limite di IOPS nel BLOB di archiviazione di Azure sottostante.

I valori di utilizzo delle risorse `avg_data_io_percent` , `avg_log_write_percent`ad esempio e, riportati nelle viste [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)e [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , vengono calcolati come percentuali dei limiti massimi di governance delle risorse. Pertanto, quando i fattori diversi dalla governance delle risorse limitano le operazioni di i/o al secondo, è possibile vedere IOPS/velocità effettiva flat e latenze che aumentano con l'aumentare del carico di lavoro, anche se l'utilizzo delle risorse segnalato rimane inferiore al 100%. 

Per visualizzare IOPS, velocità effettiva e latenza di lettura e scrittura per ogni file di database, usare la funzione [sys. dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Questa funzione consente di ripartire tutti i/o sul database, inclusi i/o `avg_data_io_percent`in background che non sono conteggiati, ma usa IOPS e la velocità effettiva dell'archiviazione sottostante e possono avere un effetto sulla latenza di archiviazione osservata. La funzione presenta anche una latenza aggiuntiva che può essere introdotta dalla governance delle risorse di i `io_stall_queued_read_ms` / `io_stall_queued_write_ms` o per le operazioni di lettura e scrittura, rispettivamente nelle colonne e.

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
- Scalabilità fino a un livello di servizio superiore per ottenere la velocità massima di log di 96 MB/s oppure passare a un livello di servizio diverso. Il livello di servizio di [iperscalabilità](sql-database-service-tier-hyperscale.md) offre una velocità di log di 100 MB/s indipendentemente dal livello di servizio scelto.
- Se i dati caricati sono temporanei, ad esempio i dati di staging in un processo ETL, possono essere caricati in tempdb (con registrazione minima). 
- Per gli scenari di analisi, caricare in una tabella analizzata columnstore cluster. In questo modo si riduce la velocità di log necessaria a causa della compressione. Questa tecnica aumenta l'utilizzo della CPU ed è applicabile solo ai set di dati che traggono vantaggio dagli indici columnstore cluster. 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Per informazioni su DTU ed eDTU, vedere [DTU ed eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Per informazioni sui limiti di tempdb relativi alle dimensioni, vedere [TempDB nel database SQL di Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
