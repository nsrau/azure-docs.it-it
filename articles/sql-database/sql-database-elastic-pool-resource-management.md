---
title: Gestione delle risorse in pool elastici densi | Microsoft Docs
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
ms.date: 11/18/2019
ms.openlocfilehash: 4ce00743f6b77e6ac3e672e0ebce1e5eafc8235d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187189"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Gestione delle risorse in pool elastici densi

Il [pool elastici](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) del database SQL di Azure è una soluzione conveniente per la gestione di molti database con un utilizzo delle risorse variabile. Tutti i database in un pool elastico condividono la stessa allocazione di risorse, ad esempio CPU, memoria, thread di lavoro, spazio di archiviazione, tempdb, presupposto che solo un subset di database nel pool utilizzerà le risorse di calcolo in un determinato momento. Questo presupposto consente ai pool elastici di essere convenienti. Invece di pagare per tutte le risorse che ogni singolo database potrebbe richiedere, i clienti pagano per un set di risorse molto più piccolo, condivise tra tutti i database nel pool.

## <a name="resource-governance"></a>Governance delle risorse

Per la condivisione di risorse è necessario che il sistema controlli attentamente l'utilizzo delle risorse per ridurre al minimo l'effetto "rumoroso vicino", in cui un database con un consumo di risorse elevato influisce su altri database nello stesso pool elastico. Allo stesso tempo, il sistema deve fornire risorse sufficienti per funzionalità come la disponibilità elevata e il ripristino di emergenza (HADR), il backup e il ripristino, il monitoraggio, la Query Store e l'ottimizzazione automatica per funzionare in modo affidabile.

Il database SQL di Azure consente di ottenere questi obiettivi usando più meccanismi di governance delle risorse, inclusi [gli oggetti processo](https://docs.microsoft.com/windows/win32/procthread/job-objects) di Windows per la governance delle risorse a livello di processo, il [file Server di Windows Gestione risorse (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) per la gestione delle quote di archiviazione e una versione modificata ed estesa di SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) implementare la governance delle risorse in ogni istanza di SQL Server in esecuzione nel database

L'obiettivo di progettazione globale dei pool elastici è quello di essere economicamente conveniente. Per questo motivo, il sistema consente intenzionalmente ai clienti di creare pool _densi_ , ovvero pool con il numero di database che si avvicinano o al massimo consentito, ma con un'allocazione moderata delle risorse di calcolo. Per lo stesso motivo, il sistema non riserva tutte le risorse potenzialmente necessarie per i processi interni, ma consente la condivisione delle risorse tra i processi interni e i carichi di lavoro degli utenti.

Questo approccio consente ai clienti di usare pool elastici densi per ottenere prestazioni adeguate e maggiori risparmi sui costi. Tuttavia, se il carico di lavoro per i database in un pool denso è sufficientemente intenso, la contesa delle risorse diventa significativa. La contesa delle risorse riduce le prestazioni dei carichi di lavoro degli utenti e può influire negativamente sui processi interni.

Quando si verifica una contesa di risorse in un pool densamente compresso, i clienti possono scegliere una o più delle azioni seguenti per attenuarla:
- Ottimizzare il carico di lavoro delle query per ridurre l'utilizzo delle risorse.
- Ridurre la densità del pool spostando alcuni database in un altro pool o rendendoli database autonomi.
- Aumentare le prestazioni del pool per ottenere altre risorse.

Per suggerimenti su come implementare le ultime due azioni, vedere [raccomandazioni operative](#operational-recommendations) più avanti in questo articolo. La riduzione della contesa di risorse consente di gestire i carichi di lavoro degli utenti e i processi interni e consente al sistema di gestire in modo affidabile il livello di servizio previsto.

## <a name="monitoring-resource-consumption"></a>Monitoraggio dell'utilizzo delle risorse

Per evitare un calo delle prestazioni a causa di una contesa di risorse, i clienti che usano pool elastici densi dovrebbero monitorare in modo proattivo l'utilizzo delle risorse e intraprendere le azioni tempestive se l'aumento della contesa di risorse inizia a influenzare i carichi di lavoro. Il monitoraggio continuo è importante perché l'utilizzo delle risorse in un pool cambia nel tempo, a causa delle modifiche del carico di lavoro dell'utente, delle modifiche apportate alla distribuzione e dei volumi di dati, delle modifiche alla densità del pool e delle modifiche nel motore di database di SQL Server 

Il database SQL di Azure offre diverse metriche rilevanti per questo tipo di monitoraggio. Il superamento del valore medio consigliato per ogni metrica indica una contesa di risorse nel pool e deve essere risolta utilizzando una delle azioni citate in precedenza.

|Nome metrica|DESCRIZIONE|Valore medio consigliato| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Utilizzo della CPU da parte del processo di SQL Server associato a un pool elastico, misurato dal sistema operativo sottostante. Disponibile nella vista [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) in ogni database e nella vista [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel database `master`. Questa metrica viene anche emessa in monitoraggio di Azure, dove viene [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`e può essere visualizzata in portale di Azure. Questo valore è lo stesso per ogni database nello stesso pool elastico.|Inferiore al 70%. Potrebbero essere accettabili picchi brevi occasionali fino al 90%.|
|`max_worker_percent`|Utilizzo del [thread di lavoro]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) . Fornito per ogni database nel pool, nonché per il pool stesso. Esistono limiti diversi per il numero di thread di lavoro a livello di database e a livello di pool, quindi è consigliabile monitorare questa metrica a entrambi i livelli. Disponibile nella vista [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) in ogni database e nella vista [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel database `master`. Questa metrica viene anche emessa in monitoraggio di Azure, dove viene [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`e può essere visualizzata in portale di Azure.|Inferiore al 80%. I picchi fino al 100% provocheranno un errore di tentativi di connessione e query.|
|`avg_data_io_percent`|Utilizzo di IOPS per i/o fisici di lettura e scrittura. Fornito per ogni database nel pool, nonché per il pool stesso. Sono previsti limiti diversi per il numero di operazioni di i/o al secondo a livello di database e a livello di pool, di conseguenza è consigliabile monitorare questa metrica a entrambi i livelli. Disponibile nella vista [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) in ogni database e nella vista [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel database `master`. Questa metrica viene anche emessa in monitoraggio di Azure, dove viene [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`e può essere visualizzata in portale di Azure.|Inferiore al 80%. Potrebbero essere accettabili picchi brevi occasionali fino al 100%.|
|`avg_log_write_percent`|Utilizzo della velocità effettiva per i/o di scrittura del log delle transazioni. Fornito per ogni database nel pool, nonché per il pool stesso. Esistono limiti diversi per la velocità effettiva dei log a livello di database e a livello di pool, quindi è consigliabile monitorare questa metrica a entrambi i livelli. Disponibile nella vista [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in ogni database e nella vista [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel database `master`. Questa metrica viene anche emessa in monitoraggio di Azure, dove viene [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`e può essere visualizzata in portale di Azure. Quando questa metrica è prossima al 100%, tutte le modifiche al database (INSERT, UPDATE, DELETE, MERGE statements, SELECT... IN, BULK INSERT e così via) sarà più lento.|Inferiore al 90%. Potrebbero essere accettabili picchi brevi occasionali fino al 100%.|
|`oom_per_second`|Frequenza di errori di memoria insufficiente in un pool elastico, che rappresenta un indicatore di utilizzo elevato della memoria. Disponibile nella vista [sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) . Per calcolare questa metrica, vedere [esempi](#examples) per una query di esempio.|0|
|`avg_storage_percent`|Utilizzo dello spazio di archiviazione a livello di pool elastico. Disponibile nella vista [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nel database `master`. Questa metrica viene anche emessa in monitoraggio di Azure, dove viene [denominata](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`e può essere visualizzata in portale di Azure.|Inferiore al 80%. Può raggiungere il 100% per i pool senza crescita dei dati.|
|`tempdb_log_used_percent`|Utilizzo dello spazio del log delle transazioni nel database `tempdb`. Anche se gli oggetti temporanei creati in un database non sono visibili in altri database nello stesso pool elastico, `tempdb` è una risorsa condivisa per tutti i database nello stesso pool. Una transazione a esecuzione prolungata o inattiva in `tempdb` avviata da un database nel pool può utilizzare una parte considerevole del log delle transazioni e causare errori per le query in altri database nello stesso pool. Disponibile nella vista [sys. dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) . Questa metrica viene anche emessa in monitoraggio di Azure e può essere visualizzata in portale di Azure. Per restituire il valore corrente di questa metrica, vedere [esempi](#examples) di query di esempio.|Inferiore al 50%. Sono accettabili picchi occasionali fino al 80%.|
|||

Oltre a queste metriche, il database SQL di Azure offre una visualizzazione che restituisce i limiti effettivi di governance delle risorse, nonché visualizzazioni che restituiscono statistiche di utilizzo delle risorse a livello di pool di risorse e a livello di gruppo del carico di lavoro.

|Nome visualizzazione|DESCRIZIONE|  
|-----------------|--------------------------------|  
|[sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Restituisce le impostazioni di configurazione e capacità effettive utilizzate dai meccanismi di governance delle risorse nel database o nel pool elastico corrente.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Restituisce informazioni sullo stato corrente del pool di risorse, sulla configurazione corrente dei pool di risorse e sulle statistiche cumulative del pool di risorse.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Restituisce le statistiche del gruppo di carico di lavoro cumulativo e la configurazione corrente del gruppo del carico di lavoro. Questa vista può essere unita a sys. dm_resource_governor_resource_pools nella colonna `pool_id` per ottenere informazioni sul pool di risorse.|
|[sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Restituisce le statistiche di utilizzo del pool di risorse per gli ultimi 32 minuti. Ogni riga rappresenta un intervallo di 20 secondi. Le colonne `delta_` restituiscono la modifica in ogni statistica durante l'intervallo.|
|[sys. dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Restituisce le statistiche di utilizzo del gruppo di carico di lavoro per gli ultimi 32 minuti. Ogni riga rappresenta un intervallo di 20 secondi. Le colonne `delta_` restituiscono la modifica in ogni statistica durante l'intervallo.|
|||

Queste visualizzazioni possono essere usate per monitorare l'utilizzo delle risorse e risolvere i problemi di contesa delle risorse quasi in tempo reale. Il carico di lavoro dell'utente sulle repliche secondarie primarie e leggibili, incluse le repliche geografiche, è classificato nel pool di risorse `SloSharedPool1` e nel gruppo del carico di lavoro `UserPrimaryGroup.DBId[N]`, in cui `N` corrisponde al valore ID del database.

Oltre al monitoraggio dell'utilizzo delle risorse corrente, i clienti che usano pool densi possono mantenere i dati cronologici sull'utilizzo delle risorse in un archivio dati separato. Questi dati possono essere usati nell'analisi predittiva per gestire in modo proattivo l'utilizzo delle risorse in base alle tendenze cronologiche e stagionali.

## <a name="operational-recommendations"></a>Raccomandazioni operative

**Lasciare**un numero sufficiente di risorse. Se si verificano conflitti tra le risorse e le prestazioni, la mitigazione può comportare il trasferimento di alcuni database dal pool elastico interessato o il ridimensionamento del pool, come indicato in precedenza. Tuttavia, queste azioni richiedono il completamento di risorse di calcolo aggiuntive. In particolare, per i pool Premium e business critical, queste azioni richiedono il trasferimento di tutti i dati per i database spostati o per tutti i database nel pool elastico se il pool viene ridimensionato. Il trasferimento dei dati è un'operazione a esecuzione prolungata e a elevato utilizzo di risorse. Se il pool è già sottoposto a un elevato utilizzo di risorse, l'operazione di mitigazione potrebbe peggiorare ulteriormente le prestazioni. In casi estremi, potrebbe non essere possibile risolvere la contesa di risorse tramite lo spostamento del database o la scalabilità verticale del pool, perché le risorse richieste non sono disponibili. In questo caso, la riduzione temporanea del carico di lavoro delle query nel pool elastico interessato potrebbe essere l'unica soluzione.

I clienti che usano pool densi devono monitorare attentamente le tendenze di utilizzo delle risorse come descritto in precedenza e adottare misure di attenuazione mentre le metriche rimangono negli intervalli consigliati e sono ancora disponibili risorse sufficienti nel pool elastico.

L'utilizzo delle risorse dipende da diversi fattori che cambiano nel tempo per ogni database e ogni pool elastico. Il raggiungimento di un rapporto prezzo/prestazioni ottimale nei pool di densità richiede il monitoraggio e il ribilanciamento continui, ovvero lo stato di trasferimento dei database da pool più utilizzati a pool meno utilizzati e la creazione di nuovi pool in base alle esigenze per supportare un maggiore carico di lavoro.

**Non spostare i database "Hot"** . Se la contesa di risorse a livello di pool è causata principalmente da un numero ridotto di database altamente utilizzati, è possibile che si stia tentando di spostare questi database in un pool meno utilizzato o di renderli database autonomi. Questa operazione, tuttavia, quando un database rimane molto utilizzato non è consigliabile, perché l'operazione di spostamento comporta un peggioramento delle prestazioni, sia per il database spostato sia per l'intero pool. In alternativa, è possibile attendere fino a quando non si utilizza un elevato livello di utilizzo oppure spostare database meno utilizzati per ridurre la pressione delle risorse a livello di pool. Tuttavia, lo stato di trasferimento dei database con un utilizzo molto basso non offre alcun vantaggio in questo caso, perché non riduce materialmente l'utilizzo delle risorse a livello di pool.

**Creare nuovi database in un pool di "quarantena"** . Negli scenari in cui i nuovi database vengono creati di frequente, ad esempio le applicazioni che utilizzano il modello tenant per database, esiste il rischio che un nuovo database inserito in un pool elastico esistente utilizzi in modo imprevisto risorse significative e influisca su altri database e processi interni nel pool. Per attenuare questo rischio, creare un pool di "quarantena" separato con un'allocazione di risorse ampia. Usare questo pool per i nuovi database con modelli di utilizzo delle risorse ancora sconosciuti. Una volta che un database è rimasto in questo pool per un ciclo aziendale, ad esempio una settimana o un mese, e il consumo di risorse è noto, può essere spostato in un pool con una capacità sufficiente a contenere questo utilizzo aggiuntivo delle risorse.

**Evitare i server logici eccessivamente densi**. Il database SQL di Azure [supporta](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) fino a 5000 database per ogni server logico. I clienti che usano pool elastici con migliaia di database possono considerare la possibilità di collocare più pool elastici in un singolo server, con il numero totale di database fino al limite supportato. Tuttavia, i server logici con molte migliaia di database creano problemi operativi. Le operazioni che richiedono l'enumerazione di tutti i database in un server, ad esempio la visualizzazione dei database nel portale, saranno più lente. Gli errori operativi, ad esempio la modifica errata degli account di accesso a livello di server o delle regole del firewall, avranno effetto su un numero maggiore di database. L'eliminazione accidentale del server logico richiederà assistenza da supporto tecnico Microsoft per ripristinare i database nel server eliminato e provocherà un'interruzione prolungata per tutti i database interessati.

È consigliabile limitare il numero di database per ogni server logico a un numero inferiore rispetto al valore massimo supportato. In molti scenari, l'uso di un massimo di 1000-2000 database per server è ottimale. Per ridurre la probabilità di eliminazione accidentale del server, è consigliabile inserire un [blocco Delete](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) sul server logico o sul relativo gruppo di risorse.

In passato, determinati scenari che comportavano lo spostamento dei database in, out o tra pool elastici sullo stesso server logico erano più veloci rispetto allo spostando i database tra server logici. Attualmente, tutti gli spostamenti del database vengono eseguiti con la stessa velocità indipendentemente dal server logico di origine e di destinazione.

## <a name="examples"></a>esempi

### <a name="monitoring-memory-utilization"></a>Monitoraggio dell'utilizzo della memoria

Questa query calcola la metrica `oom_per_second` per ogni pool di risorse, negli ultimi 32 minuti. Questa query può essere eseguita in qualsiasi database in un pool elastico.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>Monitoraggio dell'utilizzo dello spazio del log `tempdb`

Questa query restituisce il valore corrente della metrica `tempdb_log_used_percent`. Questa query può essere eseguita in qualsiasi database in un pool elastico.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione ai pool elastici, vedere [pool elastici per gestire e ridimensionare più database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool).
* Per informazioni sull'ottimizzazione dei carichi di lavoro di query per ridurre l'utilizzo delle risorse, vedere [monitoraggio e ottimizzazione]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index), [monitoraggio e ottimizzazione delle prestazioni](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview).
