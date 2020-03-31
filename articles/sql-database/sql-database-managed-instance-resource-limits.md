---
title: Limiti delle risorse - istanza gestitaResource limits - managed instance
description: Questo articolo offre una panoramica sui limiti delle risorse del database SQL di Azure per le istanze gestite.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365381"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Panoramica dei limiti delle risorse dell'istanza gestita del database SQL di AzureOverview Azure SQL Database managed instance resource limits

Questo articolo offre una panoramica delle caratteristiche tecniche e dei limiti delle risorse per l'istanza gestita del database SQL di Azure e fornisce informazioni su come richiedere un aumento di questi limiti.

> [!NOTE]
> Per informazioni sulle differenze nelle funzionalità e nelle istruzioni T-SQL supportate, vedere [Confronto tra le funzionalità](sql-database-features.md) e [Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server](sql-database-managed-instance-transact-sql-information.md). Per le differenze generali tra i livelli di servizio in un singolo database e nell'istanza gestita, vedere [Confronto tra](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)livelli di servizio .

## <a name="hardware-generation-characteristics"></a>Caratteristiche delle generazioni dell'hardware

L'istanza gestita ha caratteristiche e limiti di risorse che dipendono dall'infrastruttura e dall'architettura sottostanti. Azure SQL Database managed instance can be deployed on two hardware generations: Gen4 and Gen5. Le generazioni di hardware hanno caratteristiche diverse, come descritto nella tabella seguente:

|   | **Quarta generazione** | **Quinta generazione** |
| --- | --- | --- |
| Hardware | Processori Intel E5-2673 v3 (Haswell) a 2,4 GHz, con unità vCore SSD = 1 PP (core fisico) | Processore Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake), processori veloci NVMe SSD, vCore 1 LP (hyper-thread) |
| Numero di vCore | 8, 16, 24 vCore | 4, 8, 16, 24, 32, 40, 64, 80 vCore |
| Memoria massima (rapporto memoria/core) | 7 GB per vCore<br/>Aggiungere più vCore per ottenere più memoria. | 5,1 GB per vCore<br/>Aggiungere più vCore per ottenere più memoria. |
| Memoria OLTP massima in memoria | Limite di istanze: 1-1,5 GB per vCore| Limite di istanze: 0,8 - 1,65 GB per vCore |
| Max archiviazione riservata dell'istanzaMax instance reserved storage |  Scopo generale: 8 TB<br/>Business Critical: 1 TB | Scopo generale: 8 TB<br/> Business Critical 1 TB, 2 TB o 4 TB a seconda del numero di core |

> [!IMPORTANT]
> - L'hardware Gen4 è in fase di eliminazione graduale e non è più disponibile per le nuove distribuzioni. Tutte le nuove istanze gestite devono essere distribuite nell'hardware Gen5.
> - Prendi in considerazione lo spostamento delle [istanze gestite](sql-database-service-tiers-vcore.md) nell'hardware di generazione 5 per sperimentare una gamma più ampia di scalabilità di vCore e storage, rete accelerata, migliori prestazioni di I/O e latenza minima.

### <a name="in-memory-oltp-available-space"></a>Spazio disponibile OLTP in memoria 

La quantità di spazio OLTP in memoria nel livello di servizio [Business Critical](sql-database-service-tier-business-critical.md) dipende dal numero di vCore e dalla generazione di hardware. Nella tabella seguente sono elencati i limiti di memoria che possono essere utilizzati per gli oggetti OLTP in memoria.

| Spazio OLTP in memoria  | **Quinta generazione** | **Quarta generazione** |
| --- | --- | --- |
| 4 vCore  | 3,14 GB | |   
| 8 vCore  | 6,28 GB | 8 GB |
| 16 vCore | 15,77 GB | 20 GB |
| 24 vCore | 25,25 GB | 36 GB |
| 32 vCore | 37,94 GB | |
| 40 vCore | 52,23 GB | |
| 64 vCore | 99,9 GB    | |
| 80 vCore | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Caratteristiche del livello di servizio

L'istanza gestita ha due livelli di servizio: [Scopo generale](sql-database-service-tier-general-purpose.md) e [Business Critical](sql-database-service-tier-business-critical.md). Questi livelli forniscono [funzionalità diverse,](sql-database-service-tiers-general-purpose-business-critical.md)come descritto nella tabella seguente.

> [!Important]
> Il livello di servizio Business Critical fornisce una copia predefinita aggiuntiva dell'istanza (replica secondaria) che può essere usata per il carico di lavoro di sola lettura. Se è possibile separare le query di lettura/scrittura e le query di sola lettura/analisi/reporting, si ottengono due volte vCore e memoria per lo stesso prezzo. La replica secondaria potrebbe essere in ritardo di pochi secondi rispetto all'istanza primaria, pertanto è progettata per eseguire l'offload del carico di lavoro di report/analisi che non richiedono l'esatto stato corrente dei dati. Nella tabella seguente le query di **sola lettura** sono le query eseguite nella replica secondaria.

| **Funzionalità** | **Scopo generale** | **Business Critical** |
| --- | --- | --- |
| Numero di vCore\* | Quarta generazione: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Quarta generazione: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Lo stesso numero di vCore è dedicato alle query di sola lettura. |
| Memoria massima | Gen4: 56 GB - 168 GB (7 GB/vCore)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/vCore)<br/>Aggiungere più vCore per ottenere più memoria. | Gen4: 56 GB - 168 GB (7 GB/vCore)<br/>Generazione 5: 20,4 GB - 408 GB (5,1 GB/vCore) per le query di lettura/scritturaGenGen5: 20.4 GB - 408 GB (5.1GB/vCore) for read-write queries<br/>- ulteriori 20,4 GB - 408 GB (5,1 GB/vCore) per le query di sola lettura.<br/>Aggiungere più vCore per ottenere più memoria. |
| Dimensione massima di archiviazione dell'istanza (riservata)Max instance storage size (reserved) | - 2 TB per 4 vCore (solo Gen5)<br/>- 8 TB per altre dimensioni | Gen4: 1 TB <br/> Quinta generazione: <br/>- 1 TB per 4, 8, 16 vCore<br/>- 2 TB per 24 vCore<br/>- 4 TB per 32, 40, 64, 80 vCore |
| Dimensioni massime del database | Fino alle dimensioni dell'istanza attualmente disponibili (max 2 TB - 8 TB a seconda del numero di vCore). | Fino alle dimensioni dell'istanza attualmente disponibili (max 1 TB - 4 TB a seconda del numero di vCore). |
| Dimensioni max di tempDB | Limitato a 24 GB/vCore (96 - 1.920 GB) e alle dimensioni di archiviazione dell'istanza attualmente disponibili.<br/>Aggiungere più vCores per ottenere più spazio TempDB.<br/> La dimensione del file di registro è limitata a 120 GB.| Fino alle dimensioni di archiviazione dell'istanza attualmente disponibili. |
| Numero massimo di database per istanza | 100, a meno che non sia stato raggiunto il limite di dimensione di archiviazione dell'istanza. | 100, a meno che non sia stato raggiunto il limite di dimensione di archiviazione dell'istanza. |
| Numero massimo di file di database per istanzaMax number of database files per instance | Fino a 280, a meno che non sia stata raggiunta la dimensione di archiviazione dell'istanza o il limite di spazio di archiviazione di archiviazione del disco Premium di Azure.Up to 280, unless the instance storage size or [Azure Premium Disk storage allocation limiti](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) is been reached. | 32.767 file per database, a meno che non sia stato raggiunto il limite di dimensione di archiviazione dell'istanza. |
| Dimensione massima del file di dati | Limitato alle dimensioni di archiviazione dell'istanza attualmente disponibili (max 2 TB - 8 TB) e allo spazio di allocazione dello spazio di allocazione dello spazio di allocazione per l'archiviazione dei [dischi Premium di Azure.](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) | Limite alle dimensioni di archiviazione dell'istanza attualmente disponibili (da 1 TB a 4 TB). |
| Dimensione massima del file di registro | Limitato a 2 TB e dimensioni di archiviazione dell'istanza attualmente disponibili. | Limitato a 2 TB e dimensioni di archiviazione dell'istanza attualmente disponibili. |
| Dati/Log di IOPS (approssimativi) | Fino a 30-40 K IOPS per istanza, 500 - 7500 per file<br/>\*[Aumentare le dimensioni del file per ottenere più operazioni di I/O al secondo](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS/vCore)<br/>Aggiungere più vCore per ottenere migliori prestazioni di I/O.Add more vCores to get better IO performance. |
| Limite di velocità effettiva di scrittura del log (per istanza)Log write throughput limit (per instance) | 3 MB/s per ogni vCore<br/>Massimo 22 MB/s | 4 MB/s per vCore<br/>Max 48 MB/s |
| Dati effettivi (approssimativi) | 100 - 250 MB/s per ogni file<br/>\*[Aumentare le dimensioni del file per ottenere prestazioni di I/O migliori](#file-io-characteristics-in-general-purpose-tier) | Non limitato. |
| Latenza I/O di archiviazione (approssimativa) | 5-10 ms | 1-2 ms |
| OLTP in memoria | Non supportate | Disponibile, [la dimensione dipende dal numero di vCore](#in-memory-oltp-available-space) |
| Numero massimo di sessioni | 30000 | 30000 |
| [Repliche di sola letturaRead-only replicas](sql-database-read-scale-out.md) | 0 | 1 (incluso nel prezzo) |

> [!NOTE]
> - **La dimensione di archiviazione dell'istanza attualmente disponibile** è la differenza tra la dimensione dell'istanza riservata e lo spazio di archiviazione utilizzato.
> - La dimensione di archiviazione dell'istanza, che viene confrontata con la dimensione massima di archiviazione, include la dimensione dei dati e dei file di log presenti sia nel database utenti che in quello di sistema. Usare la vista di sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> per determinare lo spazio totale usato dai database. I log degli errori non vengono salvati in modo permanente e non sono inclusi nella dimensione. I backup non sono inclusi nella dimensione di archiviazione.
> - La velocità effettiva e le operazioni di I/O al secondo nel livello Scopo generale dipendono anche dalle dimensioni del [file](#file-io-characteristics-in-general-purpose-tier) che non sono limitate in modo esplicito dall'istanza gestita.
> - È possibile creare un'altra replica leggibile in un'area di Azure diversa usando i gruppi di failover automatico.
> - Le operazioni di I/O al secondo massime dell'istanza dipendono dal layout del file e dalla distribuzione del carico di lavoro. Ad esempio, se si creano file da 7 x 1 TB con un massimo di 5K IOPS ogni e 7 file di piccole dimensioni (più piccoli di 128 GB) con 500 iOPS ciascuno, è possibile ottenere 38500 IOPS per istanza (7x5000 -7x500) se il carico di lavoro può utilizzare tutti i file. Si noti che una parte di operazioni di I/O al secondo viene utilizzata anche per i backup automatici.

> [!NOTE]
> In questo articolo sono disponibili ulteriori informazioni sui limiti delle risorse nei pool di [istanze gestite.](sql-database-instance-pools.md#instance-pools-resource-limitations)

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Caratteristiche di I/O dei file nel livello Scopo generale

Nel livello di servizio General Purpose ogni file di database sta ottenendo operazioni di I/O al secondo e velocità effettiva dedicate che dipendono dalle dimensioni del file. I file più grandi ottengono più operazioni di I/O al secondo e velocità effettiva. Le caratteristiche di I/O dei file di database sono illustrate nella tabella seguente:

| Dimensione del file | >n. 0 e <128 GIB | >128 e <256 GiB | >256 e <512 GiB | >0,5 e <TiB da 1 USD    | >1 e <2 TiB    | >2 e <TiB da 4 | >4 e <TiB da 8 |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Operazioni di I/O al secondo per file       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12.500   |
| Velocità effettiva per file | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Se si nota un'elevata latenza di I/O in un file di database o si nota che le operazioni di I/O al secondo/velocità effettiva raggiungono il limite, è possibile migliorare le prestazioni [aumentando le dimensioni del file.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)

Esistono anche limiti a livello di istanza, ad esempio velocità effettiva di scrittura del log massimo 22 MB/s, pertanto potrebbe non essere possibile raggiungere il file nel file di log perché si raggiunge il limite di velocità effettiva dell'istanza.

## <a name="supported-regions"></a>Aree supportate

Le istanze gestite possono essere create solo nelle [aree supportate.](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) Per creare un'istanza gestita in un'area attualmente non supportata, è possibile inviare una richiesta di [supporto tramite il portale](quota-increase-request.md)di Azure.

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

L'istanza gestita supporta attualmente la distribuzione solo nei tipi di sottoscrizioni seguenti:Managed instance currently supports deployment only on the following types of subscriptions:

- [Contratto Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pagamento in base al tuo](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provider di servizi cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Sottoscrizioni con credito mensile di Azure per i sottoscrittori di Visual StudioSubscriptions with monthly Azure credit for Visual Studio subscribers](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limiti delle risorse a livello di area

I tipi di sottoscrizioni supportati possono contenere un numero limitato di risorse per area. L'istanza gestita ha due limiti predefiniti per ogni area di Azure (che possono essere aumentati su richiesta creando una richiesta di supporto speciale nel portale di Azure a seconda del tipo di sottoscrizione:Managed instance has two default limits per azure region (that can be increased on-demand by creating a special [support request in the Azure portal](quota-increase-request.md) depending on a type of subscription type:

- **Limite di subnet**: numero massimo di subnet in cui vengono distribuite le istanze gestite in una singola area.
- **limite unità vCore:** il numero massimo di unità vCore che possono essere distribuite in tutte le istanze in una singola area. Un criterio di gruppo vCore utilizza un'unità vCore e un vCore BC accetta 4 unità vCore. Il numero totale di istanze non è limitato finché rientra nel limite di unità vCore.

> [!Note]
> Questi limiti sono impostazioni predefinite e non limitazioni tecniche. I limiti possono essere aumentati su richiesta creando una richiesta di supporto speciale [nel portale](quota-increase-request.md) di Azure se sono necessarie più istanze gestite nell'area corrente. In alternativa, è possibile creare nuove istanze gestite in un'altra area di Azure senza inviare richieste di supporto.

La tabella seguente mostra **i limiti regionali predefiniti** per i tipi di sottoscrizione supportati (i limiti predefiniti possono essere estesi utilizzando la richiesta di supporto descritta di seguito):

|Tipo di sottoscrizione| Numero massimo di subnet di istanza gestitaMax number of managed instance subnets | Numero massimo di unità vCore |
| :---| :--- | :--- |
|Pagamento in base al consumo|3|320|
|CSP |8 (15 in alcune regioni)|960 (1440 in alcune regioni)|
|Sviluppo/test con pagamento in base al consumo|3|320|
|Sviluppo/test Enterprise|3|320|
|Contratto Enterprise|8 (15 in alcune regioni)|960 (1440 in alcune regioni)|
|Visual Studio Enterprise|2 |64|
|Piattaforme Visual Studio Professional e MSDN|2|32|

\*Nella pianificazione delle distribuzioni, tenere in considerazione il fatto che il livello di servizio Business Critical (BC) richiede quattro (4) volte più capacità vCore rispetto al livello di servizio General Purpose (GP). Ad esempio: 1 GP vCore è 1 unità vCore e 1 BC vCore - 4 unità vCore. Per semplificare l'analisi del consumo rispetto ai limiti predefiniti, riepilogare le unità vCore in tutte le subnet dell'area in cui vengono distribuite le istanze gestite e confrontare i risultati con i limiti di unità dell'istanza per il tipo di sottoscrizione. **Il numero massimo di unità vCore** limite si applica a ogni sottoscrizione in un'area. Non esiste alcun limite per ogni singola subnet, ad eccezione del fatto che la somma di tutti i vCore distribuiti in più subnet deve essere inferiore o uguale al **numero massimo di unità vCore**.

\*\*I limiti più grandi di subnet e vCore sono disponibili nelle seguenti aree geografiche: Australia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti centro-meridionali, Sud-est asiatico, Regno Unito sud, Europa occidentale, Stati Uniti occidentali 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Richiedere un aumento della quota per l'istanza gestita SQLRequest a quota increase for SQL managed instance

Se sono necessarie più istanze gestite nelle aree correnti, inviare una richiesta di supporto per estendere la quota tramite il portale di Azure.If you need more managed instances in your current regions, send a support request to extend the quota using the Azure portal. Per altre informazioni, vedere Aumenti delle quote di richiesta per il database SQL di Azure.For more information, see [Request quota increases for Azure SQL Database](quota-increase-request.md).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'istanza gestita, vedere [Che cos'è un'istanza gestita?](sql-database-managed-instance.md).
- Per informazioni sui prezzi, vedere Determinazione dei prezzi delle [istanze gestite dal database SQL.](https://azure.microsoft.com/pricing/details/sql-database/managed/)
- Per informazioni su come creare la prima istanza gestita, vedere [la guida introduttiva](sql-database-managed-instance-get-started.md).
