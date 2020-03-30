---
title: Gestione delle risorse in pool elastici densi Documenti Microsoft
description: Gestire le risorse di calcolo nei pool elastici SQL di Azure con molti database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473728"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Gestione risorse in pool elastici densi

I [pool elastici](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) del database SQL di Azure sono una soluzione conveniente per la gestione di molti database con un utilizzo variabile delle risorse. Tutti i database di un pool elastico condividono la stessa allocazione di risorse, ad esempio CPU, memoria, thread di lavoro, spazio di archiviazione, tempdb, presupponendo che **solo un sottoinsieme di database nel pool utilizzerà le risorse di calcolo in un determinato momento.** Questo presupposto consente ai pool elastici di essere convenienti. Invece di pagare per tutte le risorse di cui ogni singolo database potrebbe avere bisogno, i clienti pagano per un set di risorse molto più piccolo, condiviso tra tutti i database del pool.

## <a name="resource-governance"></a>Governance delle risorse

La condivisione delle risorse richiede che il sistema controlli attentamente l'utilizzo delle risorse per ridurre al minimo l'effetto "noisy neighbor", in cui un database con un utilizzo elevato delle risorse influisce sugli altri database nello stesso pool elastico. Allo stesso tempo, il sistema deve fornire risorse sufficienti per funzionalità quali disponibilità elevata e ripristino di emergenza (HADR), backup e ripristino, monitoraggio, archivio query, ottimizzazione automatica e così via per funzionare in modo affidabile.

Il database SQL di Azure raggiunge questi obiettivi usando più meccanismi di governance delle risorse, tra cui oggetti processo di Windows per la governance delle risorse a livello di processo, Gestione risorse file server (FSRM) di Windows per la gestione delle quote di archiviazione e una versione modificata ed estesa di SQL Server Resource Governor per implementare la governance delle risorse all'interno di ogni istanza di SQL Server in esecuzione nel database SQL di Azure.Azure SQL Database achieves these goals by using multiple resource governance mechanisms, including Windows [Job Objects](https://docs.microsoft.com/windows/win32/procthread/job-objects) for process level resource governance, Windows [File Server Resource Manager (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) for storage quota management, and a modified and extended version of SQL Server Resource [Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) to implement resource governance within each SQL Server instance running in Azure SQL Database.

L'obiettivo di progettazione principale dei pool elastici è quello di essere convenienti. Per questo motivo, il sistema consente intenzionalmente ai clienti di creare pool _densi,_ ovvero pool con il numero di database che si avvicinano o al massimo consentito, ma con un'allocazione moderata delle risorse di calcolo. Per lo stesso motivo, il sistema non riserva tutte le risorse potenzialmente necessarie per i processi interni, ma consente la condivisione delle risorse tra processi interni e carichi di lavoro degli utenti.

Questo approccio consente ai clienti di utilizzare pool elastici densi per ottenere prestazioni adeguate e notevoli risparmi sui costi. Tuttavia, se il carico di lavoro per molti database in un pool denso è sufficientemente intenso, la contesa delle risorse diventa significativa. La contesa delle risorse riduce le prestazioni del carico di lavoro degli utenti e può avere un impatto negativo sui processi interni.

> [!IMPORTANT]
> Nei pool densi con molti database attivi, potrebbe non essere possibile aumentare il numero di database nel pool fino ai valori massimi documentati per i pool elastici [DTU](sql-database-dtu-resource-limits-elastic-pools.md) e [vCore.](sql-database-vcore-resource-limits-elastic-pools.md)
> 
> Il numero di database che possono essere inseriti in pool densi senza causare conflitti di risorse e problemi di prestazioni dipende dal numero di database attivi contemporaneamente e dall'utilizzo delle risorse da parte dei carichi di lavoro degli utenti in ogni database. Questo numero può cambiare nel tempo quando cambiano i carichi di lavoro degli utenti.

Quando si verifica una contesa di risorse in un pool densamente compresso, i clienti possono scegliere una o più delle seguenti azioni per ridurle:
- Ottimizzare il carico di lavoro delle query per ridurre il consumo di risorse o distribuire il consumo di risorse tra più database nel tempo.
- Ridurre la densità del pool spostando alcuni database in un altro pool o rendendoli database autonomi.
- Aumentare la scalabilità verticale del pool per ottenere più risorse.

Per suggerimenti su come implementare le ultime due azioni, vedere [Consigli operativi](#operational-recommendations) più avanti in questo articolo. La riduzione dei conflitti di risorse è vantaggiosa sia per i carichi di lavoro degli utenti che per i processi interni e consente al sistema di mantenere in modo affidabile il livello di servizio previsto.

## <a name="monitoring-resource-consumption"></a>Monitoraggio del consumo di risorseMonitoring resource consumption

Per evitare una riduzione delle prestazioni a causa di conflitti di risorse, i clienti che usano pool elastici densi devono monitorare in modo proattivo il consumo di risorse e intraprendere azioni tempestive se l'aumento della contesa delle risorse inizia a influire sui carichi di lavoro. Il monitoraggio continuo è importante perché l'utilizzo delle risorse in un pool cambia nel tempo, a causa di modifiche nel carico di lavoro degli utenti, modifiche nei volumi di dati e nella distribuzione, modifiche nella densità del pool e modifiche nel servizio di database SQL di Azure.Continuous monitoring is important because resource usage in a pool changes in time, due changes in user workload, changes in data volumes and distribution, changes in pool density, and changes in the Azure SQL Database service. 

Il database SQL di Azure offre diverse metriche rilevanti per questo tipo di monitoraggio. Il superamento del valore medio consigliato per ogni metrica indica la contesa delle risorse nel pool e deve essere affrontata utilizzando una delle azioni menzionate in precedenza.

|Nome metrica|Descrizione|Valore medio consigliato| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Utilizzo della CPU del processo di SQL Server associato a un pool elastico, misurato dal sistema operativo sottostante. Disponibile nella vista [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) in ogni database e nella `master` vista [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel database. Questa metrica viene inoltre generata in Monitoraggio di Azure, dove è [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`, e può essere visualizzata nel portale di Azure.This metric is also emitted to Azure Monitor, where it is named , and can be viewed in Azure portal. Questo valore è lo stesso per ogni database nello stesso pool elastico.|Inferiore al 70%. I picchi brevi occasionali fino al 90% possono essere accettabili.|
|`max_worker_percent`|[Utilizzo dei thread di lavoro.]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) Fornito per ogni database nel pool, nonché per il pool stesso. Esistono limiti diversi al numero di thread di lavoro a livello di database e a livello di pool, pertanto è consigliabile monitorare questa metrica a entrambi i livelli. Disponibile nella vista [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) in ogni database e nella `master` vista [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel database. Questa metrica viene inoltre generata in Monitoraggio di Azure, dove è [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`, e può essere visualizzata nel portale di Azure.This metric is also emitted to Azure Monitor, where it is named , and can be viewed in Azure portal.|Inferiore all'80%. Picchi fino al 100% causerà tentativi di connessione e query di esito negativo.|
|`avg_data_io_percent`|Utilizzo delle operazioni di I/O al secondo per l'I/O fisico in lettura e scrittura. Fornito per ogni database nel pool, nonché per il pool stesso. Esistono limiti diversi al numero di operazioni di I/O al secondo a livello di database e a livello di pool, pertanto è consigliabile monitorare questa metrica a entrambi i livelli. Disponibile nella vista [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) in ogni database e nella `master` vista [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel database. Questa metrica viene inoltre generata in Monitoraggio di Azure, dove è [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`, e può essere visualizzata nel portale di Azure.This metric is also emitted to Azure Monitor, where it is named , and can be viewed in Azure portal.|Inferiore all'80%. Picchi brevi occasionali fino al 100% possono essere accettabili.|
|`avg_log_write_percent`|Utilizzo della velocità effettiva per l'I/O di scrittura del log delle transazioni. Fornito per ogni database nel pool, nonché per il pool stesso. Esistono limiti diversi alla velocità effettiva del log a livello di database e a livello di pool, pertanto è consigliabile monitorare questa metrica a entrambi i livelli. Disponibile nella vista [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in ogni database e nella `master` vista [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel database. Questa metrica viene inoltre generata in Monitoraggio di Azure, dove è [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`, e può essere visualizzata nel portale di Azure.This metric is also emitted to Azure Monitor, where it is named , and can be viewed in Azure portal. Quando questa metrica è vicina al 100%, tutte le modifiche al database (INSERT, UPDATE, DELETE, MERGE istruzioni, SELECT ... INTO, BULK INSERT, ecc.) sarà più lento.|Al di sotto del 90%. Picchi brevi occasionali fino al 100% possono essere accettabili.|
|`oom_per_second`|Frequenza di errori di memoria insufficiente in un pool elastico, ovvero un indicatore della pressione della memoria. Disponibile nella visualizzazione [sys.dm_resource_governor_resource_pools_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) Vedere [Esempi](#examples) per una query di esempio per calcolare questa metrica.|0|
|`avg_storage_percent`|Utilizzo dello spazio di archiviazione a livello di pool elastico. Disponibile nella vista [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel `master` database. Questa metrica viene inoltre generata in Monitoraggio di Azure, dove è [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`, e può essere visualizzata nel portale di Azure.This metric is also emitted to Azure Monitor, where it is named , and can be viewed in Azure portal.|Inferiore all'80%. Può avvicinarsi al 100% per i pool senza crescita dei dati.|
|`tempdb_log_used_percent`|Utilizzo dello spazio `tempdb` del log delle transazioni nel database. Anche se gli oggetti temporanei creati in un database non `tempdb` sono visibili in altri database nello stesso pool elastico, è una risorsa condivisa per tutti i database nello stesso pool. Una transazione a esecuzione `tempdb` prolungata o inattiva avviata da un database del pool può utilizzare una grande parte del log delle transazioni e causare errori per le query in altri database nello stesso pool. Disponibile nella vista [sys.dm_db_log_space_usage.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) Questa metrica viene generata anche in Monitoraggio di Azure e può essere visualizzata nel portale di Azure.This metric is also emitted to Azure Monitor, and can be viewed in Azure portal. Vedere [Esempi](#examples) per una query di esempio per restituire il valore corrente di questa metrica.|Al di sotto del 50%. I picchi occasionali fino all'80% sono accettabili.|
|||

Oltre a queste metriche, il database SQL di Azure offre una visualizzazione che restituisce i limiti effettivi di governance delle risorse, nonché visualizzazioni aggiuntive che restituiscono statistiche sull'utilizzo delle risorse a livello di pool di risorse e a livello di gruppo di carico di lavoro.

|Nome della vista|Descrizione|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Restituisce le impostazioni di configurazione e capacità effettive utilizzate dai meccanismi di governance delle risorse nel database o nel pool elastico corrente.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Restituisce informazioni sullo stato corrente del pool di risorse, sulla configurazione corrente dei pool di risorse e sulle statistiche cumulative del pool di risorse.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Restituisce le statistiche cumulative del gruppo di carico di lavoro e la configurazione corrente del gruppo del carico di lavoro. Questa visualizzazione può essere unita con `pool_id` sys.dm_resource_governor_resource_pools nella colonna per ottenere informazioni sul pool di risorse.|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Restituisce le statistiche di utilizzo del pool di risorse per gli ultimi 32 minuti. Ogni riga rappresenta un intervallo di 20 secondi. Le `delta_` colonne restituiscono la modifica in ogni statistica durante l'intervallo.|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Restituisce le statistiche di utilizzo del gruppo di carico di lavoro per gli ultimi 32 minuti. Ogni riga rappresenta un intervallo di 20 secondi. Le `delta_` colonne restituiscono la modifica in ogni statistica durante l'intervallo.|
|||

Queste visualizzazioni possono essere utilizzate per monitorare l'utilizzo delle risorse e risolvere i problemi delle risorse quasi in tempo reale. Il carico di lavoro dell'utente nelle repliche secondarie primarie `SloSharedPool1` e leggibili, incluse le repliche geografiche, è classificato nel pool di risorse e `UserPrimaryGroup.DBId[N]` nel gruppo di carico di lavoro, dove `N` rappresenta il valore ID del database.

Oltre a monitorare l'utilizzo corrente delle risorse, i clienti che utilizzano pool densi possono gestire i dati cronologici sull'utilizzo delle risorse in un archivio dati separato. Questi dati possono essere utilizzati nell'analisi predittiva per gestire in modo proattivo l'utilizzo delle risorse in base alle tendenze storiche e stagionali.

## <a name="operational-recommendations"></a>Raccomandazioni operative

**Lasciare sufficiente spazio per la testa delle risorse**. Se si verifica la contesa delle risorse e la riduzione delle prestazioni, l'attenuazione può comportare lo spostamento di alcuni database all'esterno del pool elastico interessato o la scalabilità verticale del pool, come indicato in precedenza. Tuttavia, queste azioni richiedono risorse di calcolo aggiuntive per il completamento. In particolare, per i pool Premium e Business Critical, queste azioni richiedono il trasferimento di tutti i dati per i database da spostare o per tutti i database nel pool elastico se il pool viene aumentato. Il trasferimento dei dati è un'operazione a esecuzione prolungata e che richiede un uso intensivo delle risorse. Se il pool è già sotto pressione delle risorse, l'operazione di attenuazione stessa commiscerà ulteriormente le prestazioni. In casi estremi, potrebbe non essere possibile risolvere i conflitti di risorse tramite lo spostamento del database o la scalabilità del pool perché le risorse necessarie non sono disponibili. In questo caso, ridurre temporaneamente il carico di lavoro delle query nel pool elastico interessato può essere l'unica soluzione.

I clienti che usano pool densi devono monitorare attentamente le tendenze di utilizzo delle risorse come descritto in precedenza e intraprendere azioni attenuanti mentre le metriche rimangono all'interno degli intervalli consigliati e nel pool elastico sono ancora disponibili risorse sufficienti.

L'utilizzo delle risorse dipende da diversi fattori che cambiano nel tempo per ogni database e ogni pool elastico. Il raggiungimento del rapporto prezzo/prestazioni ottimale in pool densi richiede un monitoraggio e un ribilanciamento continui, ovvero lo spostamento dei database da più pool utilizzati a pool meno utilizzati e la creazione di nuovi pool in base alle esigenze per gestire un aumento del carico di lavoro.

**Non spostare i database "caldi".** Se la contesa delle risorse a livello di pool è causata principalmente da un numero ridotto di database altamente utilizzati, potrebbe essere allettante spostare questi database in un pool meno utilizzato o renderli database autonomi. Tuttavia, questa operazione mentre un database rimane altamente utilizzato non è consigliabile, perché l'operazione di spostamento diminuirà ulteriormente le prestazioni, sia per il database da spostare, sia per l'intero pool. Al contrario, attendere che l'utilizzo elevato diminuisca o spostare invece database meno utilizzati per alleviare l'utilizzo delle risorse a livello di pool. In questo caso, tuttavia, lo spostamento di database con un utilizzo molto basso non offre alcun vantaggio, perché non riduce in modo sostanziale l'utilizzo delle risorse a livello di pool.

**Creare nuovi database in un pool "in quarantena".** Negli scenari in cui vengono creati frequentemente nuovi database, ad esempio le applicazioni che utilizzano il modello tenant per database, esiste il rischio che un nuovo database inserito in un pool elastico esistente consumi in modo imprevisto risorse significative e influisca su altri database e processi interni nel pool. Per ridurre questo rischio, creare un pool di "quarantena" separato con un'ampia allocazione delle risorse. Utilizzare questo pool per nuovi database con modelli di utilizzo delle risorse ancora sconosciuti. Una volta che un database è rimasto in questo pool per un ciclo aziendale, ad esempio una settimana o un mese, e il relativo utilizzo delle risorse è noto, può essere spostato in un pool con capacità sufficiente per supportare questo utilizzo aggiuntivo delle risorse.

**Evitare server logici eccessivamente densi .** Il database SQL di Azure [supporta](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) fino a 5000 database per server logico. I clienti che utilizzano pool elastici con migliaia di database possono considerare l'inserimento di più pool elastici in un singolo server, con il numero totale di database fino al limite supportato. Tuttavia, i server logici con molte migliaia di database creano problemi operativi. Le operazioni che richiedono l'enumerazione di tutti i database in un server, ad esempio la visualizzazione di database nel portale, saranno più lente. Gli errori operativi, ad esempio la modifica non corretta degli account di accesso a livello di server o delle regole del firewall, influiranno su un numero maggiore di database. L'eliminazione accidentale del server logico richiederà assistenza dal supporto tecnico Microsoft per ripristinare i database nel server eliminato e causerà un'interruzione prolungata per tutti i database interessati.

È consigliabile limitare il numero di database per server logico a un numero inferiore al massimo supportato. In molti scenari, l'utilizzo di un massimo di 1000-2000 database per server è ottimale. Per ridurre la probabilità di eliminazione accidentale del server, è consigliabile inserire un [blocco di eliminazione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) nel server logico o nel relativo gruppo di risorse.

In passato, alcuni scenari che prevedono lo spostamento di database all'esterno, all'esterno o tra pool elastici nello stesso server logico erano più veloci rispetto allo spostamento di database tra server logici. Attualmente, tutti gli spostamenti del database vengono eseguiti alla stessa velocità indipendentemente dal server logico di origine e di destinazione.

## <a name="examples"></a>Esempi

### <a name="monitoring-memory-utilization"></a>Monitoraggio dell'utilizzo della memoria

Questa query calcola la `oom_per_second` metrica per ogni pool di risorse negli ultimi 32 minuti. Questa query può essere eseguita in qualsiasi database in un pool elastico.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>Monitoraggio `tempdb` dell'utilizzo dello spazio di log

Questa query restituisce il `tempdb_log_used_percent` valore corrente della metrica. Questa query può essere eseguita in qualsiasi database in un pool elastico.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione ai pool elastici, vedere [I pool elastici consentono](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)di gestire e ridimensionare più database SQL di Azure.
* Per informazioni sull'ottimizzazione dei carichi di lavoro di query per ridurre l'utilizzo delle risorse, vedere [Monitoraggio e ottimizzazione]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)e [Monitoraggio e ottimizzazione delle prestazioni](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview).
