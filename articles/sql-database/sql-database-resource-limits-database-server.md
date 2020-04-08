---
title: Limiti delle risorse del database SQL di Azure Documenti Microsoft
description: Questo articolo offre una panoramica dei limiti delle risorse del database SQL di Azure per singoli database e pool elastici. Offre anche informazioni su cosa accade quando tali limiti delle risorse vengono raggiunti o superati.
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
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804827"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Limiti delle risorse del database SQL e governance delle risorse

Questo articolo offre una panoramica dei limiti delle risorse del database SQL per un server di database SQL che gestisce database singoli e pool elastici. Fornisce informazioni su cosa accade quando tali limiti di risorse vengono raggiunti o superati e descrive i meccanismi di governance delle risorse utilizzati per applicare tali limiti.

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
> Per ottenere più quota DTU/eDTU, quota vCore o più server rispetto alla quantità predefinita, inviare una nuova richiesta di supporto nel portale di Azure.To obtain more DTU/eDTU quota, vCore quota, or more servers than the default amount, submit a new support request in the Azure portal. Per altre informazioni, vedere Aumenti delle quote di richiesta per il database SQL di Azure.For more information, see [Request quota increases for Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Dimensioni dello spazio di archiviazione

Per le dimensioni di archiviazione delle risorse per singoli database, fare riferimento ai limiti delle [risorse basate su DTU](sql-database-dtu-resource-limits-single-databases.md) o ai limiti delle risorse basate su [vCore](sql-database-vcore-resource-limits-single-databases.md) per i limiti delle dimensioni di archiviazione per ogni piano tariffario.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Cosa accade quando vengono raggiunti i limiti delle risorse del database?

### <a name="compute-dtus-and-edtus--vcores"></a>Elaborazione (DTU ed eDTU/vCore)

Quando l'utilizzo del calcolo del database (misurato da DCU ed eDCU o vCore) diventa elevato, la latenza delle query aumenta e le query possono anche scadere. In queste condizioni, le query possono essere accodate dal servizio e vengono fornite risorse per l'esecuzione man mano che le risorse diventano libere.
In caso di uso elevato di risorse di elaborazione, le opzioni di mitigazione includono:

- Aumento delle dimensioni di calcolo del database o del pool elastico per mettere a disposizione del database un numero maggiore di risorse del computer. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'utilizzo delle risorse di ogni query. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Archiviazione

Quando la quantità di spazio del database usato raggiunge il limite di dimensioni massime, gli inserimenti e gli aggiornamenti del database che aumentano la dimensione dei dati hanno esito negativo e i clienti ricevono un [messaggio di errore](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Le istruzioni SELECT e DELETE continuano a avere esito positivo.

In caso di uso elevato di spazio, le opzioni di mitigazione includono:

- Aumentare le dimensioni massime del database o del pool elastico o aggiungere più spazio di archiviazione. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Se il database è in un pool elastico, in alternativa può essere spostato all'esterno del pool in modo che lo spazio di archiviazione non venga condiviso con altri database.
- Compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioni e ruoli di lavoro (richieste)

Il numero massimo di sessioni e ruoli di lavoro è determinato dal livello di servizio e dalle dimensioni di calcolo (DCU/eDTUs o vCore). Le nuove richieste vengono rifiutate quando vengono raggiunti i limiti delle sessioni o dei ruoli di lavoro e i clienti ricevono un messaggio di errore. Mentre il numero di connessioni disponibili può essere controllato dall'applicazione, il numero di ruoli di lavoro simultanei è spesso più difficile da stimare e da controllare. Ciò è particolarmente vero durante i periodi di picco di carico quando vengono raggiunti i limiti delle risorse del database e i lavoratori si accumulano a causa di query a esecuzione prolungata, catene di blocco di grandi dimensioni o parallelismo eccessivo delle query.

In caso di uso elevato di sessioni o ruoli di lavoro, le opzioni di mitigazione includono:

- Aumento del livello di servizio o delle dimensioni di calcolo del database o del pool elastico. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query, se l'aumento dell'uso di ruoli di lavoro è dovuto a un conflitto delle risorse di elaborazione. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).
- Riduzione dell'impostazione [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (massimo grado di parallelismo).
- Ottimizzazione del carico di lavoro delle query per ridurre il numero di occorrenze e la durata del blocco delle query.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Consumo di risorse per carichi di lavoro utente e processi interni

Il consumo di CPU e memoria per i carichi di lavoro degli `avg_cpu_percent` utenti `avg_memory_usage_percent` in ogni database viene segnalato nelle viste [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) e [sys.resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) nelle colonne e nelle colonne. Per i pool elastici, l'utilizzo delle risorse a livello di pool viene riportato nella visualizzazione [sys.elastic_pool_resource_stats.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) L'utilizzo della CPU del `cpu_percent` carico di lavoro degli utenti viene segnalato anche tramite la metrica di Monitoraggio di Azure, per [singoli database](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) e [pool elastici](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) a livello di pool.

Il database SQL di Azure richiede risorse di calcolo per implementare funzionalità di base del servizio, ad esempio disponibilità elevata e ripristino di emergenza, backup e ripristino del database, monitoraggio, archivio query, ottimizzazione automatica e così via. Il sistema accantonato una certa parte delle risorse complessive per questi processi interni utilizzando meccanismi di [governance delle risorse,](#resource-governance) rendendo il resto delle risorse disponibili per i carichi di lavoro degli utenti. In alcuni casi in cui i processi interni non utilizzano le risorse di calcolo, il sistema le rende disponibili per i carichi di lavoro degli utenti.

Il consumo totale di CPU e memoria da parte dei carichi di lavoro utente e dei processi interni nell'istanza `avg_instance_cpu_percent` di `avg_instance_memory_percent` SQL Server che ospita un singolo database o un pool elastico viene segnalato nelle viste [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) e [sys.resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) in e nelle colonne. Questi `sqlserver_process_core_percent` dati vengono segnalati `sqlserver_process_memory_percent` anche tramite le metriche di e Monitoraggio di Azure, per [singoli database](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) e [pool elastici](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) a livello di pool.

Una suddivisione più dettagliata del recente utilizzo delle risorse da parte dei carichi di lavoro degli utenti e dei processi interni viene segnalata nelle viste [sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) e [sys.dm_resource_governor_workload_groups_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) Per informazioni dettagliate sui pool di risorse e sui gruppi di carico di lavoro a cui si fa riferimento in queste visualizzazioni, vedere [Governance delle risorse.](#resource-governance) Queste visualizzazioni report sull'utilizzo delle risorse per carichi di lavoro utente e processi interni specifici nei pool di risorse associati e nei gruppi di carico di lavoro.

Nel contesto del monitoraggio delle prestazioni e della risoluzione`avg_cpu_percent` `cpu_percent`dei problemi, è importante considerare sia l'utilizzo della **CPU dell'utente** ( , ) che **l'utilizzo totale della CPU** da parte dei carichi di lavoro degli utenti e dei processi interni ( ,`avg_instance_cpu_percent``sqlserver_process_core_percent`).

**L'utilizzo della CPU dell'utente** viene calcolato come percentuale dei limiti del carico di lavoro dell'utente in ogni obiettivo del servizio. **L'utilizzo della CPU utente** al 100% indica che il carico di lavoro dell'utente ha raggiunto il limite dell'obiettivo del servizio. Tuttavia, quando il **consumo totale della CPU** raggiunge l'intervallo 70-100%, è possibile vedere la velocità effettiva del carico di lavoro dell'utente appiattirsi e aumentare la latenza delle query, anche se l'utilizzo della **CPU dell'utente** segnalato rimane significativamente inferiore al 100%. Ciò è più probabile che si verifichi quando si utilizzano obiettivi di servizio più piccoli con un'allocazione moderata delle risorse di calcolo, ma carichi di lavoro utente relativamente intensi, ad esempio in [pool elastici densi](sql-database-elastic-pool-resource-management.md). Ciò può verificarsi anche con obiettivi di servizio più piccoli quando i processi interni richiedono temporaneamente risorse aggiuntive, ad esempio quando si crea una nuova replica del database.

Quando **l'utilizzo totale della CPU** è elevato, le opzioni di attenuazione sono le stesse di cui è stato indicato in precedenza e includono l'aumento dell'obiettivo del servizio e/o l'ottimizzazione del carico di lavoro dell'utente.

## <a name="resource-governance"></a>Governance delle risorse

Per applicare i limiti delle risorse, il database SQL di Azure usa un'implementazione di governance delle risorse basata su SQL Server [Resource Governor,](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)modificata ed estesa per eseguire un servizio di database di SQL Server in Azure.To enforce resource limits, Azure SQL Database uses a resource governance implementation that is based on SQL Server Resource Governor , modified and extended to run a SQL Server database service in Azure. In ogni istanza di SQL Server nel servizio sono presenti più pool di [risorse](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) e [gruppi](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)di carichi di lavoro, con limiti di risorse impostati sia a livello di pool che di gruppo per fornire un database come [servizio bilanciato.](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/) Il carico di lavoro degli utenti e i carichi di lavoro interni sono classificati in pool di risorse e gruppi di carico di lavoro separati. Il carico di lavoro dell'utente nelle repliche secondarie primarie `SloSharedPool1` e leggibili, incluse le repliche geografiche, è classificato nel pool di risorse e `UserPrimaryGroup.DBId[N]` nel gruppo di carico di lavoro, dove `N` rappresenta il valore ID del database. Sono inoltre disponibili più pool di risorse e gruppi di carichi di lavoro per vari carichi di lavoro interni.

Oltre a usare Resource Governor per gestire le risorse all'interno del processo di SQL Server, il database SQL di Azure usa anche oggetti [processo](https://docs.microsoft.com/windows/win32/procthread/job-objects) di Windows per la governance delle risorse a livello di processo e [Gestione risorse file server (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) per la gestione delle quote di archiviazione.

La governance delle risorse del database SQL di Azure è di natura gerarchica. Dall'alto verso il basso, i limiti vengono applicati a livello di sistema operativo e a livello di volume di archiviazione utilizzando i meccanismi di governance delle risorse del sistema operativo e Resource Governor, quindi a livello di pool di risorse usando Resource Governor e quindi a livello di gruppo di carico di lavoro usando Resource Governor. I limiti di governance delle risorse in vigore per il database o il pool elastico corrente vengono visualizzati nella vista [sys.dm_user_db_resource_governance.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 

### <a name="data-io-governance"></a>Governance i/o dei dati

La governance di I/O dei dati è un processo nel database SQL di Azure usato per limitare sia l'i/o di lettura che quella di scrittura fisica sui file di dati di un database. I limiti di operazioni di I/O al secondo sono impostati per ogni livello di servizio per ridurre al minimo l'effetto "noisy neighbor", per garantire l'equità dell'allocazione delle risorse nel servizio multi-tenant e per rimanere all'interno delle funzionalità dell'hardware e dell'archiviazione sottostanti.

Per i singoli database, i limiti del gruppo di carico di lavoro vengono applicati a tutte le attività di I/O di archiviazione nel database, mentre i limiti del pool di risorse si applicano a tutti i database di archiviazione per tutti i database nella stessa istanza di SQL ServerSQL Server , incluso il `tempdb` database. Per i pool elastici, i limiti del gruppo di carico di lavoro si `tempdb` applicano a ogni database del pool, mentre il limite del pool di risorse si applica all'intero pool elastico, incluso il database, che viene condiviso tra tutti i database del pool. In generale, i limiti del pool di risorse potrebbero non essere raggiungibili dal carico di lavoro su un database (singolo o in pool), perché i limiti del gruppo di carico di lavoro sono inferiori ai limiti del pool di risorse e limitano prima IOPS/throughput. Tuttavia, i limiti del pool possono essere raggiunti dal carico di lavoro combinato su più database nella stessa istanza di SQL Server.However, pool limits may be reached by the combined workload against multiple databases on the same SQL Server instance.

Ad esempio, se una query genera 1000 operazioni di I/O al secondo senza alcuna governance delle risorse di I/O, ma il limite massimo di Operazioni di I/O al secondo del gruppo del carico di lavoro è impostato su 900 operazioni di I/O al secondo, la query non sarà in grado di generare più di 900 operazioni di I/O al secondo. Tuttavia, se il limite massimo di operazioni di I/O al secondo del pool di risorse è impostato su 1500 operazioni di I/O al secondo e il totale delle operazioni di I/O al secondo associate al pool di risorse supera 1500 IOPS, l'I/O della stessa query può essere ridotto al di sotto del limite del gruppo di lavoro di 900 IOPS.

I valori di IOPS e throughput min/max restituiti dalla vista [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) fungono da limiti/limiti, non come garanzie. Inoltre, la governance delle risorse non garantisce alcuna latenza di archiviazione specifica. La latenza, le operazioni di I/O al secondo e la velocità effettiva migliori per un determinato carico di lavoro utente dipendono non solo dai limiti di governance delle risorse di I/O, ma anche dalla combinazione di dimensioni di I/O usate e dalle funzionalità dell'archiviazione sottostante. SQL Server utilizza i / o scheggiate che variano in dimensioni comprese tra 512 KB e 4 MB. Ai fini dell'applicazione dei limiti di Operazioni di I/O al secondo, ogni I/O viene considerato indipendentemente dalle dimensioni, ad eccezione dei database con file di dati in Archiviazione di Azure.For purposes on enforcing IOPS limits, every IO is accounted regarded from its size, as the exception of databases with data files in Azure Storage. In tal caso, le operazioni di I/O superiori a 256 KB vengono considerate come più operazioni di I/O da 256 KB, per essere allineate con l'accounting di I/O di Archiviazione di Azure.In that case, IOs larger than 256 KB are accounted as multiple 256 KB IOs, to align with Azure Storage IO accounting.

Per i database Basic, Standard e General Purpose, che `primary_group_max_io` usano file di dati in Archiviazione di Azure, il valore potrebbe non essere raggiungibile se un database non dispone di file di dati sufficienti per fornire cumulativamente questo numero di operazioni di I/O al secondo o se i dati non vengono distribuiti in modo uniforme tra i file o se il livello di prestazioni dei limiti BLOB sottostanti IOPS/velocità effettiva al di sotto del limite di governance delle risorse. Analogamente, con piccole operazioni di I/O `primary_max_log_rate` di log generate dal commit frequente delle transazioni, il valore potrebbe non essere raggiungibile da un carico di lavoro a causa del limite di operazioni di I/O al secondo nel BLOB di archiviazione di Azure sottostante.

I valori di `avg_data_io_percent` `avg_log_write_percent`utilizzo delle risorse, ad esempio e , riportati nelle viste [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)e [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , vengono calcolati come percentuali dei limiti massimi di governance delle risorse. Pertanto, quando fattori diversi dal limite di IOPS/velocità effettiva di governance delle risorse, è possibile vedere iOPS/velocità effettiva appiattirsi e aumentare le latenze con l'aumentare del carico di lavoro, anche se l'utilizzo delle risorse segnalate rimane al di sotto del 100%. 

Per visualizzare le operazioni di I/O al secondo, la velocità effettiva e la latenza in lettura e scrittura per ogni file di database, utilizzare la funzione [sys.dm_io_virtual_file_stats().](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) Questa funzione espone tutte le operazioni di I/O nel database, `avg_data_io_percent`incluse le operazioni di I/O in background non contabilizzate nei confronti di , ma utilizza operazioni di I/O al secondo e velocità effettiva dell'archiviazione sottostante e può influire sulla latenza di archiviazione osservata. La funzione espone anche una latenza aggiuntiva che può essere introdotta `io_stall_queued_read_ms` dalla `io_stall_queued_write_ms` governance delle risorse di I/O per le letture e le scritture, rispettivamente nelle colonne e .

### <a name="transaction-log-rate-governance"></a>Governance del tasso del log delle transazioniTransaction log rate governance

La governance della frequenza del log delle transazioni è un processo nel database SQL di Azure usato per limitare velocità di inserimento elevate per carichi di lavoro quali inserimento bulk, SELECT INTO e compilazioni di indici. Questi limiti vengono registrati e applicati a livello di sottosecondi alla frequenza di generazione dei record di log, limitando la velocità effettiva indipendentemente dal numero di operazioni di I/O emesse nei file di dati.  Le velocità di generazione del log delle transazioni attualmente scalano linearmente fino a un punto dipendente dall'hardware, con la velocità di log massima consentita di 96 MB/s con il modello di acquisto vCore. 

> [!NOTE]
> Le operazioni di I/O fisiche effettive nei file di registro delle transazioni non sono governate o limitate.

Le frequenze di log sono impostate in modo che possano essere raggiunte e sostenute in una varietà di scenari, mentre l'intero sistema può mantenere la propria funzionalità con un impatto ridotto al minimo il carico dell'utente. La governance della frequenza di log garantisce che i backup del log delle transazioni rimangano all'interno dei contratti di servizio di recuperabilità pubblicati.  Questa governance impedisce anche un backlog eccessivo nelle repliche secondarie.

Man mano che vengono generati i record di log, ogni operazione viene valutata e valutata per verificare se deve essere ritardata per mantenere una frequenza di log massima desiderata (MB/s al secondo). I ritardi non vengono aggiunti quando i record di log vengono scaricati nell'archiviazione, piuttosto la governance della frequenza di log viene applicata durante la generazione della frequenza di log stessa.

Le velocità effettive di generazione dei log imposte in fase di esecuzione possono anche essere influenzate dai meccanismi di feedback, riducendo temporaneamente i tassi di log consentiti in modo che il sistema possa stabilizzarsi. Gestione dello spazio dei file di log, evitando di esaurire le condizioni di spazio di log e i meccanismi di replica del gruppo di disponibilità possono ridurre temporaneamente i limiti complessivi del sistema.

Il data shaping del traffico di Log rate governor viene visualizzato tramite i seguenti tipi di attesa (esposti nelle viste [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_os_wait_stats):](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

| Tipo di attesa | Note |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitazione del database |
| POOL_LOG_RATE_GOVERNOR | Limitazione delle piscine |
| INSTANCE_LOG_RATE_GOVERNOR | Limitazione a livello di istanza |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controllo del feedback, la replica fisica del gruppo di disponibilità in Premium/Business Critical non tiene il passo |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controllo del feedback, limitazione dei tassi per evitare una condizione di spazio di log out |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Controllo del feedback di replica geografica, limitazione della frequenza di log per evitare un'elevata latenza dei dati e indisponibilità dei database geografici|
|||

Quando si rileva un limite di frequenza di registro che ostacola la scalabilità desiderata, considerare le opzioni seguenti:
- Scalabilità verticale a un livello di servizio superiore per ottenere la velocità di registrazione massima di 96 MB/s o passare a un livello di servizio diverso. Il livello di servizio [Hyperscale](sql-database-service-tier-hyperscale.md) fornisce una frequenza di registrazione di 100 MB/s indipendentemente dal livello di servizio scelto.
- Se i dati caricati sono temporanei, ad esempio i dati di gestione temporanea in un processo ETL, possono essere caricati in tempdb (che è registrato in modo minimo). 
- Per gli scenari analitici, caricare in una tabella coperta da columnstore cluster. In questo modo si riduce la frequenza di registrazione richiesta a causa della compressione. Questa tecnica aumenta l'utilizzo della CPU ed è applicabile solo ai set di dati che traggono vantaggio dagli indici columnstore cluster. 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Per informazioni su DTU ed eDTU, vedere [DTU ed eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Per informazioni sui limiti di tempdb relativi alle dimensioni, vedere [TempDB nel database SQL di Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
