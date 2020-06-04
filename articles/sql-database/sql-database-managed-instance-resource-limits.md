---
title: Limiti di risorse - Istanza gestita
description: Questo articolo offre una panoramica sui limiti delle risorse del database SQL di Azure per le istanze gestite.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: d46746b03935ff5d7893d149b9eb744bb65293ed
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774231"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Panoramica dei limiti delle risorse del database SQL di Azure per le istanze gestite

Questo articolo offre una panoramica sulle caratteristiche tecniche e sui limiti delle risorse relative alle istanze gestite di database SQL di Azure e fornisce informazioni su come chiedere un aumento di questi limiti.

> [!NOTE]
> Per informazioni sulle differenze nelle funzionalità e nelle istruzioni T-SQL supportate, vedere [Confronto tra le funzionalità](sql-database-features.md) e [Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server](sql-database-managed-instance-transact-sql-information.md). Per una panoramica delle differenze tra i livelli di servizio in un singolo database e nell'istanza gestita, vedere [Confronto tra i livelli di servizio](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Caratteristiche delle generazioni dell'hardware

L'istanza gestita ha caratteristiche e limiti delle risorse che dipendono dall'infrastruttura e dall'architettura sottostanti. L'istanza gestita di database SQL di Azure può essere distribuita in due generazioni hardware: Quarta generazione e Quinta generazione. Le generazioni hardware hanno caratteristiche diverse, descritte nella tabella seguente:

|   | **Quarta generazione** | **Quinta generazione** |
| --- | --- | --- |
| Hardware | Processori Intel E5-2673 v3 (Haswell) a 2,4 GHz, con unità vCore SSD = 1 PP (core fisico) | Processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz e Intel SP-8160 (Skylake), unità SSD NVMe veloce, vCore = 1 LP (hyperthread) |
| Numero di vCore | 8, 16, 24 vCore | 4, 8, 16, 24, 32, 40, 64, 80 vCore |
| Memoria massima (rapporto memoria/core) | 7 GB per vCore<br/>Aggiungere altri vCore per ottenere una maggiore quantità di memoria. | 5,1 GB per vCore<br/>Aggiungere altri vCore per ottenere una maggiore quantità di memoria. |
| Memoria OLTP in memoria massima | Limite istanza: 1-1,5 GB per vCore| Limite istanza: 0,8 - 1,65 GB per vCore |
| Archiviazione riservata istanza massima |  Utilizzo generico: 8 TB<br/>Business Critical: 1 TB | Utilizzo generico: 8 TB<br/> Business Critical: 1 TB, 2 TB o 4 TB in base al numero di core |

> [!IMPORTANT]
> - L'hardware di quarta generazione è in corso di sostituzione e non è più disponibile per le nuove distribuzioni. Tutte le nuove istanze gestite devono essere distribuite in hardware di quinta generazione.
> - Valutare la possibilità di [spostare le istanze gestite in hardware di quinta generazione](sql-database-service-tiers-vcore.md) per usufruire di una maggiore scalabilità di vCore e dispositivi di archiviazione, di connessioni di rete più veloci, delle migliori prestazioni di I/O e di una latenza minima.

### <a name="in-memory-oltp-available-space"></a>Spazio disponibile OLTP in memoria 

La quantità di spazio OLTP in memoria nel livello di servizio [Business Critical](sql-database-service-tier-business-critical.md) dipende dal numero di vCore e dalla generazione dell'hardware. Nella tabella seguente sono elencati i limiti di memoria che possono essere usati per gli oggetti OLTP in memoria.

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

In Istanza gestita sono disponibili due livelli di servizio: [Utilizzo generico](sql-database-service-tier-general-purpose.md) e [Business Critical](sql-database-service-tier-business-critical.md). Questi livelli offrono [funzionalità diverse](sql-database-service-tiers-general-purpose-business-critical.md), come descritto nella tabella seguente.

> [!Important]
> Il livello di servizio Business Critical offre una copia predefinita aggiuntiva dell'istanza (replica secondaria), che può essere usata solo per carichi di lavoro di sola lettura. Se è possibile separare le query di lettura/scrittura e le query di sola lettura/analisi/report, si riceverà il doppio di vCore e memoria allo stesso prezzo. È possibile che la replica secondaria segua di alcuni secondi l'istanza primaria ed è quindi progettata per eseguire l'offload del carico di lavoro di report/analisi che non richiede l'esatto stato corrente dei dati. Nella tabella seguente, le **query di sola lettura** sono quelle eseguite nella replica secondaria.

| **Funzionalità** | **Utilizzo generico** | **Business Critical** |
| --- | --- | --- |
| Numero di vCore\* | Quarta generazione: 8, 16, 24<br/>Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 | Quarta generazione: 8, 16, 24 <br/> Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Lo stesso numero di vCore è dedicato alle query di sola lettura. |
| Memoria massima | Quarta generazione: 56 GB - 168 GB (7 GB/vCore)<br/>Quinta generazione: 20,4 GB - 408 GB (5,1 GB/vCore)<br/>Aggiungere altri vCore per ottenere una maggiore quantità di memoria. | Quarta generazione: 56 GB - 168 GB (7 GB/vCore)<br/>Quinta generazione: 20,4 GB - 408 GB (5,1 GB/vCore) per query di lettura/scrittura<br/>+ altri 20,4 GB - 408 GB (5,1 GB/vCore) per le query di sola lettura.<br/>Aggiungere altri vCore per ottenere una maggiore quantità di memoria. |
| Dimensioni massime archiviazione istanze (riservate) | - 2 TB per 4 vCore (solo quinta generazione)<br/>- 8 TB per altre dimensioni | Quarta generazione: 1 TB <br/> Quinta generazione: <br/>- 1 TB per 4, 8, 16 vCore<br/>- 2 TB per 24 vCore<br/>- 4 TB per 32, 40, 64, 80 vCore |
| Dimensioni massime del database | Fino alle dimensioni dell'istanza attualmente disponibili (max 2 TB - 8 TB, a seconda del numero di vCore). | Fino alle dimensioni dell'istanza attualmente disponibili (max 1 TB - 4 TB a seconda del numero di vCore). |
| Dimensioni max di tempDB | Limitate a 24 GB/vCore (96 - 1.920 GB) e alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile.<br/>Aggiungere altri vCore per ottenere più spazio in TempDB.<br/> Le dimensioni del file di log sono limitate a 120 GB.| Fino alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile. |
| Numero massimo di database per istanza | 100, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. | 100, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. |
| Numero massimo di file di database per istanza | Fino a 280, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza o per lo [spazio di allocazione dell'archiviazione nel disco Premium di Azure](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files). | 32.767 file per database, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. |
| Dimensioni massime dei file di dati | Limitate alle dimensioni dello spazio di archiviazione delle istanze attualmente disponibile (max 2 TB - 8 TB) e allo [spazio di allocazione dell'archiviazione nel disco Premium di Azure](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files). | Limitate alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile (fino a 1 TB - 4 TB). |
| Dimensioni massime del file di log | Limitate a 2 TB e alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile. | Limitate a 2 TB e alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile. |
| Dati/Log di IOPS (approssimativi) | Fino a 30 - 40 K IOPS per istanza*, 500 - 7500 per file<br/>\*[Aumentare le dimensioni del file per ottenere più IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS/vCore)<br/>Aggiungere altri vCore per ottenere prestazioni I/O migliori. |
| Limite di velocità effettiva della scrittura di log (per istanza) | 3 MB/s per ogni vCore<br/>Max 22 MB/s | 4 MB/s per vCore<br/>Max 48 MB/s |
| Dati effettivi (approssimativi) | 100 - 250 MB/s per ogni file<br/>\*[Aumentare le dimensioni del file per ottenere prestazioni I/O migliori](#file-io-characteristics-in-general-purpose-tier) | Non limitate. |
| Latenza I/O di archiviazione (approssimativa) | 5-10 ms | 1-2 ms |
| OLTP in memoria | Non supportate | Disponibile, [le dimensioni dipendono dal numero di vCore](#in-memory-oltp-available-space) |
| Numero massimo di sessioni | 30000 | 30000 |
| [Repliche di sola lettura](sql-database-read-scale-out.md) | 0 | 1 (inclusa nel prezzo) |
| Isolamento del calcolo | Quinta generazione:<br/>\- Supportato per 80 vCore<br/>\- Non supportato per altre dimensioni<br/><br/>Quarta generazione non supportata per deprecazione|Quinta generazione:<br/>\- Supportato per 60, 64, 80 vCore<br/>\- Non supportato per altre dimensioni<br/><br/>Quarta generazione non supportata per deprecazione|

> [!NOTE]
> - Per calcolare le **dimensioni dello spazio di archiviazione delle istanze attualmente disponibile** è sufficiente fare la differenza tra le dimensioni dell'istanza riservata e lo spazio di archiviazione usato.
> - La dimensione di archiviazione dell'istanza, che viene confrontata con la dimensione massima di archiviazione, include la dimensione dei dati e dei file di log presenti sia nel database utenti che in quello di sistema. Usare la vista di sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> per determinare lo spazio totale usato dai database. I log degli errori non vengono salvati in modo permanente e non sono inclusi nella dimensione. I backup non sono inclusi nella dimensione di archiviazione.
> - Nel livello Utilizzo generico, anche il numero di IOPS e la velocità effettiva dipendono dalle [dimensioni del file](#file-io-characteristics-in-general-purpose-tier), che non sono limitate in modo esplicito dall'istanza gestita.
> - Usando i gruppi di failover automatico, è possibile creare un'altra replica leggibile in un'area di Azure diversa.
> - Il numero massimo di IOPS dell'istanza dipende dal layout del file e dalla distribuzione del carico di lavoro. Se, ad esempio, si creano 7 file da 1 TB, ciascuno con 5000 IOPS, e 7 piccoli file (inferiori a 128 GB), ciascuno con 500 IOPS, è possibile ottenere 38.500 IOPS per istanza (7 x 5000 + 7 x 500), se il carico di lavoro può usare tutti i file. Tenere presente che una determinata quantità di IOPS viene usata anche per i backup automatici.

> [!NOTE]
> Per altre informazioni sui [limiti delle risorse nei pool di istanze gestite, vedere questo articolo](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Caratteristiche di I/O dei file nel livello Utilizzo generico

Nel livello di servizio Utilizzo generico, ogni file di database ottiene una quantità di IOPS e di velocità effettiva dedicata in base alle dimensioni stesse del file. I file più grandi ricevono quindi una quantità maggiore di IOPS e di velocità effettiva. Nella tabella seguente sono illustrate le caratteristiche di I/O dei file di database.

| Dimensione del file | >=0 e <=128 GiB | >128 e <=256 GiB | >256 e <= 512 GiB | >0,5 e <=1 TiB    | >1 e <=2 TiB    | >2 e <=4 TiB | >4 e <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS per file       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12.500   |
| Velocità effettiva per file | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Se si nota una latenza di I/O elevata per un file di database o si scopre che il rapporto IOPS/velocità effettiva sta raggiungendo il limite, è possibile migliorare le prestazioni [aumentando le dimensioni del file](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Esistono anche limiti a livello di istanza, come la velocità effettiva di scrittura del log, che non può superare 22 MB/s; è possibile quindi che non venga raggiunta la velocità effettiva nel file di log perché, nel frattempo, è stato raggiunto il limite di velocità effettiva dell'istanza.

## <a name="supported-regions"></a>Aree supportate

Le istanze gestite possono essere create solo in [aree supportate](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Per creare un'istanza gestita in un'area attualmente non supportata, è possibile [inviare una richiesta di supporto tramite il portale di Azure](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

L'istanza gestita supporta attualmente solo la distribuzione dei tipi di sottoscrizioni seguenti:

- [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provider di servizi cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Sottoscrizioni con il credito Azure mensile per i titolari di sottoscrizioni di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limiti delle risorse a livello di area

I tipi di sottoscrizioni supportati possono contenere un numero limitato di risorse per area. L'istanza gestita prevede due limiti predefiniti per ogni area di Azure, che possono essere aumentati su richiesta creando una speciale [richiesta di supporto nel portale di Azure](quota-increase-request.md) a seconda del tipo di sottoscrizione:

- **Limite di subnet**: numero massimo di subnet in cui vengono distribuite le istanze gestite in una singola area.
- **Limite unità vCore**: il numero massimo di unità vCore che possono essere distribuite in tutte le istanze di una singola area. Un vCore di Utilizzo generico usa un'unità vCore, mentre un vCore Business Critical accetta 4 unità vCore. Il numero totale di istanze non è limitato, almeno finché rientra nel limite massimo di unità vCore.

> [!Note]
> Questi limiti sono impostazioni predefinite e non limitazioni tecniche. Nel caso in cui nell'area corrente siano necessarie più istanze gestite, questi limiti possono essere aumentati su richiesta creando speciali [richieste di supporto nel portale di Azure](quota-increase-request.md). In alternativa, è possibile creare nuove istanze gestite in un'altra area di Azure senza inviare richieste di supporto.

La tabella seguente illustra i **limiti predefiniti a livello di area** per i tipi di sottoscrizione supportati (i limiti predefiniti possono essere estesi usando la richiesta di supporto descritta di seguito):

|Tipo di sottoscrizione| Numero massimo di subnet dell'istanza gestita | Numero massimo di unità vCore* |
| :---| :--- | :--- |
|Pagamento in base al consumo|3|320|
|CSP |8 (15 in alcune aree**)|960 (1440 in alcune aree**)|
|Sviluppo/test con pagamento in base al consumo|3|320|
|Sviluppo/test Enterprise|3|320|
|Contratto Enterprise|8 (15 in alcune aree**)|960 (1440 in alcune aree**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e MSDN Platforms|2|32|

\* Nella pianificazione delle distribuzioni, tenere presente che il livello di servizio Business Critical (BC) richiede una quantità di vCore quattro (4) volte superiore rispetto al livello di servizio Utilizzo generico (GP). Ad esempio: 1 vCore GP = 1 unità vCore e 1 vCore BC = 4 unità vCore. Per semplificare l'analisi del consumo in base ai limiti predefiniti, riepilogare le unità vCore in tutte le subnet nell'area in cui vengono distribuite le istanze gestite e confrontare i risultati con i limiti di unità di istanza per il tipo di sottoscrizione. Il **numero massimo di unità vCore** si applica a ogni sottoscrizione presente in un'area. Non esiste alcun limite per singole subnet, ad eccezione del fatto che la somma di tutti i vCore distribuiti tra più subnet deve essere inferiore o uguale al **numero massimo di unità vCore**.

\*\* Sono disponibili limiti di subnet e vCore maggiori nelle aree geografiche seguenti: Australia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Regno Unito meridionale, Europa occidentale, Stati Uniti occidentali 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Richiedere un aumento della quota per l'istanza gestita di SQL

Se servono più istanze gestite nelle aree correnti, è possibile inviare una richiesta di supporto per estendere la quota tramite il portale di Azure. Per altre informazioni, vedere [Request quota increases for Azure SQL Database](quota-increase-request.md) (Richiedere aumenti di quota per il database SQL di Azure).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'istanza gestita, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Per informazioni sui prezzi, vedere [Prezzi dell'istanza gestita di database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Per informazioni su come creare la prima istanza gestita, vedere la [guida di avvio rapido](sql-database-managed-instance-get-started.md).
