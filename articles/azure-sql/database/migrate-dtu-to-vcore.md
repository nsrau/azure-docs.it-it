---
title: Eseguire la migrazione da DTU a vCore
description: Eseguire la migrazione di un database nel database SQL di Azure dal modello DTU al modello vCore. La migrazione a vCore è simile all'aggiornamento o al downgrade tra i livelli standard e Premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 05/28/2020
ms.openlocfilehash: b8c7671e655594456621e4489cb06191d820b134
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333155"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Eseguire la migrazione del database SQL di Azure dal modello basato su DTU al modello basato su vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo descrive come eseguire la migrazione del database nel database SQL di Azure dal [modello di acquisto basato su DTU](service-tiers-dtu.md) al modello di [acquisto basato su vCore](service-tiers-vcore.md). 

## <a name="migrate-a-database"></a>Eseguire la migrazione di un database

La migrazione di un database dal modello di acquisto basato su DTU al modello di acquisto basato su vCore è simile alla scalabilità tra gli obiettivi del servizio nei livelli di servizio Basic, standard e Premium, con [durata](single-database-scale.md#latency) analoga e [tempi di inattività minimi](scale-resources.md) alla fine del processo di migrazione. È possibile eseguire la migrazione di un database al modello di acquisto basato su vCore al modello di acquisto basato su DTU in qualsiasi momento allo stesso modo, ad eccezione dei database di cui è stata eseguita la migrazione al livello di servizio di [iperscalabilità](service-tier-hyperscale.md) . 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>Scegliere il livello di servizio e l'obiettivo di servizio vCore

Per la maggior parte degli scenari di migrazione da DTU a vCore, i database e i pool elastici nei livelli di servizio Basic e standard eseguiranno il mapping al livello di servizio [per utilizzo generico](service-tier-general-purpose.md) . I database e i pool elastici nel livello di servizio Premium eseguiranno il mapping al livello di servizio [business critical](service-tier-business-critical.md) . A seconda dello scenario e dei requisiti dell'applicazione, il livello di servizio [iperscalabile](service-tier-hyperscale.md) può spesso essere usato come destinazione della migrazione per database singoli in tutti i livelli di servizio DTU.

Per scegliere l'obiettivo di servizio o le dimensioni di calcolo per il database migrato nel modello vCore, è possibile usare una regola empirica semplice ma approssimativa: ogni 100 DTU nei livelli Basic o standard richiede almeno *1 vCore* e ogni 125 DTU nel livello Premium richiede *almeno 1 vCore* . 

> [!TIP]
> Questa regola è approssimativa perché non considera la generazione hardware usata per il database DTU o il pool elastico. 

Nel modello DTU, qualsiasi [generazione hardware](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) disponibile può essere usata per il database o il pool elastico. Inoltre, si dispone solo di un controllo indiretto sul numero di Vcore (CPU logiche), scegliendo valori DTU o eDTU più alti o più bassi. 

Con il modello vCore, i clienti devono effettuare una scelta esplicita sia per la generazione dell'hardware che per il numero di Vcore (CPU logiche). Il modello DTU non offre queste scelte, tuttavia la generazione dell'hardware e il numero di CPU logiche utilizzate per ogni database e pool elastico vengono esposti tramite DMV. In questo modo è possibile determinare più precisamente l'obiettivo di servizio vCore corrispondente. 

L'approccio seguente usa queste informazioni per determinare un obiettivo di servizio vCore con un'allocazione simile di risorse, per ottenere un livello di prestazioni simile dopo la migrazione al modello vCore.

### <a name="dtu-to-vcore-mapping"></a>Mapping tra DTU e vCore

Una query T-SQL riportata di seguito, quando viene eseguita nel contesto di un database DTU di cui eseguire la migrazione, restituirà un numero corrispondente (probabilmente frazionario) di Vcore in ogni generazione di hardware nel modello vCore. Arrotondando questo numero al numero più vicino di Vcore disponibili per i [database](resource-limits-vcore-single-databases.md) e i [pool elastici](resource-limits-vcore-elastic-pools.md) in ogni generazione di hardware nel modello vCore, i clienti possono scegliere l'obiettivo di servizio vCore che è la corrispondenza più vicina per il database DTU o il pool elastico. 

Gli scenari di migrazione di esempio che usano questo approccio sono descritti nella sezione [esempi](#dtu-to-vcore-migration-examples) .

Eseguire la query nel contesto del database di cui eseguire la migrazione, anziché nel `master` database. Quando si esegue la migrazione di un pool elastico, eseguire la query nel contesto di qualsiasi database nel pool.

```SQL
WITH dtu_vcore_map AS
(
SELECT TOP (1) rg.slo_name,
               CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
                    WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
               END AS dtu_hardware_gen,
               s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
               CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Fattori aggiuntivi

Oltre al numero di Vcore (CPU logiche) e alla generazione dell'hardware, molti altri fattori possono influenzare la scelta dell'obiettivo di servizio vCore:

- La query T-SQL di mapping corrisponde agli obiettivi del servizio DTU e vCore in termini di capacità della CPU, pertanto i risultati saranno più accurati per i carichi di lavoro associati alla CPU.
- Per la stessa generazione hardware e lo stesso numero di Vcore, IOPS e i limiti delle risorse di velocità effettiva del log delle transazioni per i database vCore sono spesso più elevati rispetto ai database DTU. Per i carichi di lavoro associati a IO, potrebbe essere possibile ridurre il numero di Vcore nel modello vCore per ottenere lo stesso livello di prestazioni. I limiti delle risorse per i database DTU e vCore in valori assoluti vengono esposti nella visualizzazione [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . Il confronto di questi valori tra il database DTU da migrare e un database vCore usando un obiettivo di servizio approssimativamente corrispondente consentirà di selezionare più precisamente l'obiettivo di servizio vCore.
- La query di mapping restituisce anche la quantità di memoria per core per il database DTU o il pool elastico da migrare e per ogni generazione di hardware nel modello vCore. Garantire una memoria totale simile o superiore dopo la migrazione a vCore è importante per i carichi di lavoro che richiedono una cache di dati di memoria di grandi dimensioni per ottenere prestazioni sufficienti o carichi di lavoro che richiedono concessioni di memoria per l'elaborazione di query. Per questi carichi di lavoro, a seconda delle prestazioni effettive, potrebbe essere necessario aumentare il numero di Vcore per ottenere memoria totale sufficiente.
- Quando si sceglie l'obiettivo di servizio vCore, è necessario prendere in considerazione l' [utilizzo delle risorse cronologiche](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) del database DTU. I database DTU con risorse CPU con utilizzo coerente potrebbero richiedere un numero inferiore di Vcore rispetto al numero restituito dalla query di mapping. Viceversa, i database DTU in cui l'utilizzo della CPU costantemente elevato causa prestazioni inadeguate del carico di lavoro possono richiedere più Vcore rispetto a quelli restituiti dalla query.
- Se si esegue la migrazione di database con modelli di utilizzo intermittenti o imprevedibili, prendere in considerazione l'uso del livello di calcolo senza [Server](serverless-tier-overview.md) .  Si noti che il numero massimo di ruoli di lavoro simultanei (richieste) in senza server è pari al 75% del limite nel calcolo di cui è stato effettuato il provisioning per lo stesso numero di Vcore massimo configurato.  Inoltre, la memoria massima disponibile in senza server è 3 GB volte il numero massimo di Vcore configurati; ad esempio, Max Memory è 120 GB quando vengono configurati 40 Vcore max.   
- Nel modello vCore, le dimensioni massime supportate del database possono variare a seconda della generazione dell'hardware. Per i database di grandi dimensioni, controllare le dimensioni massime supportate nel modello vCore per i [database singoli](resource-limits-vcore-single-databases.md) e i [pool elastici](resource-limits-vcore-elastic-pools.md).
- Per i pool elastici, i modelli [DTU](resource-limits-dtu-elastic-pools.md) e [vCore](resource-limits-vcore-elastic-pools.md) presentano differenze nel numero massimo di database supportato per ogni pool. Questa operazione deve essere presa in considerazione quando si esegue la migrazione di pool elastici con molti database.
- Alcune generazioni hardware potrebbero non essere disponibili in ogni area. Controllare la disponibilità in [generazioni hardware](service-tiers-vcore.md#hardware-generations).

> [!IMPORTANT]
> Le linee guida per il ridimensionamento di DTU a vCore sono fornite per semplificare la stima iniziale dell'obiettivo di servizio del database di destinazione.
>
> La configurazione ottimale del database di destinazione dipende dal carico di lavoro. Per questo motivo, il raggiungimento del rapporto prezzo/prestazioni ottimale dopo la migrazione può richiedere l'uso della flessibilità del modello vCore per regolare il numero di Vcore, la [generazione dell'hardware](service-tiers-vcore.md#hardware-generations), i livelli di [servizio](service-tiers-vcore.md#service-tiers) e di [calcolo](service-tiers-vcore.md#compute-tiers) , nonché l'ottimizzazione di altri parametri di configurazione del database, ad esempio il [grado massimo di parallelismo](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).
> 

### <a name="dtu-to-vcore-migration-examples"></a>Esempi di migrazione da DTU a vCore

> [!NOTE]
> I valori degli esempi seguenti sono solo a scopo illustrativo. I valori effettivi restituiti negli scenari descritti potrebbero essere diversi.
> 

**Migrazione di un database standard S9**

La query di mapping restituisce il risultato seguente (alcune colonne non vengono visualizzate per brevità):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|Quinta generazione|5.40|16,800|7|24,000|5,05|

Si noterà che il database DTU ha 24 CPU logiche (VCore) con 5,4 GB di memoria per vCore e usa l'hardware quinta generazione. La corrispondenza diretta a è un database per utilizzo generico 24 vCore su hardware quinta generazione, ovvero l'obiettivo di servizio **GP_Gen5_24** vCore.

**Migrazione di un database S0 standard**

La query di mapping restituisce il risultato seguente (alcune colonne non vengono visualizzate per brevità):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|Quarta generazione|0,42|0,250|7|0,425|5,05|

Si noterà che il database DTU ha l'equivalente di 0,25 CPU logiche (VCore), con 0,42 GB di memoria per vCore e usa l'hardware Gen4. Gli obiettivi del servizio vCore più piccoli nelle generazioni hardware Gen4 e quinta generazione, **GP_Gen4_1** e **GP_Gen5_2**, forniscono più risorse di calcolo rispetto al database S0 standard, pertanto non è possibile trovare una corrispondenza diretta. Poiché è in corso la [rimozione delle autorizzazioni](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)per l'hardware Gen4, è preferibile l'opzione **GP_Gen5_2** . Inoltre, se il carico di lavoro è particolarmente adatto per il livello di calcolo senza [Server](serverless-tier-overview.md) , **GP_S_Gen5_1** sarebbe una corrispondenza più vicina.

**Migrazione di un database P15 Premium**

La query di mapping restituisce il risultato seguente (alcune colonne non vengono visualizzate per brevità):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|Quinta generazione|4,86|29,400|7|42,000|5,05|

Si noterà che il database DTU ha 42 CPU logiche (VCore) con 4,86 GB di memoria per vCore e usa l'hardware quinta generazione. Sebbene non esista un obiettivo di servizio vCore con 42 core, l'obiettivo di servizio **BC_Gen5_40** è molto vicino sia in termini di capacità di CPU che di memoria e rappresenta una corrispondenza ottima.

**Migrazione di un pool elastico 200 eDTU di base**

La query di mapping restituisce il risultato seguente (alcune colonne non vengono visualizzate per brevità):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4,00|Quinta generazione|5.40|2,800|7|4,000|5,05|

Si noterà che il pool elastico DTU ha 4 CPU logiche (VCore) con 5,4 GB di memoria per vCore e usa l'hardware quinta generazione. La corrispondenza diretta nel modello vCore è un pool elastico **GP_Gen5_4** . Tuttavia, questo obiettivo di servizio supporta un massimo di 200 database per pool, mentre il pool elastico Basic 200 eDTU supporta fino a 500 database. Se il pool elastico da migrare dispone di più di 200 database, l'obiettivo di servizio vCore corrispondente dovrebbe essere **GP_Gen5_6**, che supporta fino a 500 database.

## <a name="migrate-geo-replicated-databases"></a>Eseguire la migrazione di database con replica geografica

La migrazione dal modello basato su DTU al modello di acquisto basato su vCore è simile all'aggiornamento o al downgrade delle relazioni di replica geografica tra i database nei livelli di servizio standard e Premium. Durante la migrazione non è necessario arrestare la replica geografica, ma è necessario seguire le regole di sequenziazione seguenti:

- Quando si esegue l'aggiornamento, è necessario aggiornare prima il database secondario e dopo il database primario.
- Quando si esegue il downgrade, è necessario seguire l'ordine inverso, ovvero eseguire prima il downgrade del database primario e dopo quello del database secondario.

Quando si usa la replica geografica tra due pool elastici, è consigliabile designare un pool come primario e l'altro come secondario. In tal caso, quando si esegue la migrazione dei pool elastici è necessario usare le stesse linee guida per la sequenziazione. Tuttavia, se si dispone di pool elastici che contengono sia database primari che secondari, trattare il pool con l'utilizzo più elevato come primario e seguire le regole di sequenziazione di conseguenza.  

La tabella seguente fornisce indicazioni per scenari di migrazione specifici:

|Livello di servizio corrente|Livello di servizio di destinazione|Tipo di migrazione|Azioni utente|
|---|---|---|---|
|Standard|Scopo generico|Laterale|È possibile eseguire la migrazione in qualsiasi ordine, ma è necessario garantire il dimensionamento vCore appropriato come descritto in precedenza.|
|Premium|Business critical|Laterale|È possibile eseguire la migrazione in qualsiasi ordine, ma è necessario garantire il dimensionamento vCore appropriato come descritto in precedenza.|
|Standard|Business critical|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
|Business critical|Standard|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Premium|Scopo generico|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Scopo generico|Premium|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
|Business critical|Scopo generico|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Scopo generico|Business critical|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
||||

## <a name="migrate-failover-groups"></a>Eseguire la migrazione dei gruppi di failover

Per i gruppi di failover con più database è necessario eseguire separatamente la migrazione dei database primari e secondari. Durante questo processo sono valide le stesse considerazioni e regole di sequenziazione. Dopo la conversione dei database nel modello di acquisto basato su vCore, il gruppo di failover rimarrà in vigore con le stesse impostazioni dei criteri.

### <a name="create-a-geo-replication-secondary-database"></a>Creare un database secondario con replica geografica

È possibile creare un database secondario con replica geografica, ovvero una replica geografica secondaria, solo usando lo stesso livello di servizio usato per il database primario. Per i database con una frequenza di generazione dei log elevata, è consigliabile creare la replica geografica secondaria con le stesse dimensioni di calcolo della replica primaria.

Se si sta creando una replica geografica secondaria nel pool elastico per un singolo database primario, assicurarsi che l' `maxVCore` impostazione per il pool corrisponda alle dimensioni di calcolo del database primario. Se si crea un database di replica geografica secondario per un database primario in un altro pool elastico, è consigliabile che i pool abbiano le stesse `maxVCore` impostazioni.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Usare la copia del database per eseguire la migrazione da DTU a vCore

È possibile copiare un database con dimensioni di calcolo basate su DTU in un database con dimensioni di calcolo basate su vCore senza restrizioni o particolari regole di sequenziazione a condizione che le dimensioni di calcolo del database di destinazione supportino le dimensioni massime del database di origine. La copia del database crea uno snapshot dei dati a partire dall'ora di inizio dell'operazione di copia e non sincronizza i dati tra l'origine e la destinazione.

## <a name="next-steps"></a>Passaggi successivi

- Per le opzioni di calcolo e dimensioni di archiviazione specifiche disponibili per i singoli database, vedere [limiti delle risorse basate su vCore del database SQL per database singoli](resource-limits-vcore-single-databases.md).
- Per le opzioni di calcolo e dimensioni di archiviazione specifiche disponibili per i pool elastici, vedere [limiti delle risorse basate su vCore del database SQL per i pool elastici](resource-limits-vcore-elastic-pools.md).
