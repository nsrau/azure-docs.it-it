---
title: Limiti delle risorse-istanza gestita
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80365381"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Panoramica dei limiti delle risorse dell'istanza gestita di database SQL di Azure

Questo articolo fornisce una panoramica delle caratteristiche tecniche e dei limiti delle risorse per istanza gestita di database SQL di Azure e fornisce informazioni su come richiedere un aumento a questi limiti.

> [!NOTE]
> Per informazioni sulle differenze nelle funzionalità e nelle istruzioni T-SQL supportate, vedere [Confronto tra le funzionalità](sql-database-features.md) e [Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server](sql-database-managed-instance-transact-sql-information.md). Per differenze generali tra i livelli di servizio nel database singolo e nell'istanza gestita, vedere [confronto tra livelli di servizio](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Caratteristiche delle generazioni dell'hardware

L'istanza gestita presenta le caratteristiche e i limiti delle risorse che dipendono dall'infrastruttura e dall'architettura sottostanti. Istanza gestita di database SQL di Azure può essere distribuita in due generazioni hardware: Gen4 e quinta generazione. Le generazioni hardware hanno caratteristiche diverse, come descritto nella tabella seguente:

|   | **Quarta generazione** | **Quinta generazione** |
| --- | --- | --- |
| Hardware | Processori Intel E5-2673 v3 (Haswell) a 2,4 GHz, con unità vCore SSD = 1 PP (core fisico) | Processori Intel E5-2673 V4 (Broadwell) 2,3 GHz e Intel SP-8160 (Skylake), unità SSD NVMe veloce, vCore = 1 LP (Hyper-thread) |
| Numero di vCore | 8, 16, 24 vCore | 4, 8, 16, 24, 32, 40, 64, 80 vcore |
| Memoria massima (rapporto memoria/Core) | 7 GB per vCore<br/>Aggiungere altri Vcore per ottenere una maggiore quantità di memoria. | 5,1 GB per vCore<br/>Aggiungere altri Vcore per ottenere una maggiore quantità di memoria. |
| Memoria OLTP max in memoria | Limite di istanze: 1-1,5 GB per vCore| Limite di istanze: 0,8-1,65 GB per vCore |
| Archiviazione riservata istanza massima |  Per utilizzo generico: 8 TB<br/>Business critical: 1 TB | Per utilizzo generico: 8 TB<br/> Business critical 1 TB, 2 TB o 4 TB a seconda del numero di core |

> [!IMPORTANT]
> - L'hardware Gen4 viene eliminato e non è più disponibile per le nuove distribuzioni. Tutte le nuove istanze gestite devono essere distribuite in hardware quinta generazione.
> - Provare a [trasferire le istanze gestite all'hardware di generazione 5](sql-database-service-tiers-vcore.md) per sperimentare una gamma più ampia di vCore e scalabilità di archiviazione, rete accelerata, prestazioni di i/o migliori e latenza minima.

### <a name="in-memory-oltp-available-space"></a>Spazio disponibile OLTP in memoria 

La quantità di spazio di OLTP in memoria in [business critical](sql-database-service-tier-business-critical.md) livello di servizio dipende dal numero di Vcore e dalla generazione dell'hardware. Nella tabella seguente sono elencati i limiti di memoria che possono essere utilizzati per gli oggetti OLTP in memoria.

| Spazio OLTP in memoria  | **Quinta generazione** | **Quarta generazione** |
| --- | --- | --- |
| 4 vCore  | 3,14 GB | |   
| 8 vCore  | 6,28 GB | 8 GB |
| 16 vcore | 15,77 GB | 20 GB |
| 24 vcore | 25,25 GB | 36 GB |
| 32 vcore | 37,94 GB | |
| 40 vcore | 52,23 GB | |
| 64 vcore | 99,9 GB    | |
| 80 vcore | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Caratteristiche del livello di servizio

Istanza gestita ha due livelli di servizio: [per utilizzo generico](sql-database-service-tier-general-purpose.md) e [business critical](sql-database-service-tier-business-critical.md). Questi livelli forniscono [funzionalità diverse](sql-database-service-tiers-general-purpose-business-critical.md), come descritto nella tabella seguente.

> [!Important]
> Business critical livello di servizio fornisce una copia incorporata aggiuntiva dell'istanza (replica secondaria) che può essere utilizzata per il carico di lavoro di sola lettura. Se è possibile separare le query di lettura e scrittura e le query di sola lettura/analisi/report, si riceveranno due volte Vcore e memoria per lo stesso prezzo. La replica secondaria potrebbe ritardare pochi secondi alla base dell'istanza primaria, quindi è progettata per l'offload del carico di lavoro di report/analisi che non richiede lo stato di dati esatto. Nella tabella seguente, le **query** di sola lettura sono le query eseguite sulla replica secondaria.

| **Funzionalità** | **per utilizzo generico** | **Business Critical** |
| --- | --- | --- |
| Numero di vCore\* | Quarta generazione: 8, 16, 24<br/>Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 | Quarta generazione: 8, 16, 24 <br/> Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Lo stesso numero di Vcore è dedicato per le query di sola lettura. |
| Memoria massima | Gen4:56 GB-168 GB (7 GB/vCore)<br/>Quinta generazione: 20,4 GB-408 GB (5.1 GB/vCore)<br/>Aggiungere altri Vcore per ottenere una maggiore quantità di memoria. | Gen4:56 GB-168 GB (7 GB/vCore)<br/>Quinta generazione: 20,4 GB-408 GB (5.1 GB/vCore) per le query di lettura/scrittura<br/>+ 20,4 GB aggiuntivi-408 GB (5.1 GB/vCore) per le query di sola lettura.<br/>Aggiungere altri Vcore per ottenere una maggiore quantità di memoria. |
| Dimensioni massime archiviazione istanze (riservate) | -2 TB per 4 Vcore (solo quinta generazione)<br/>-8 TB per altre dimensioni | Gen4:1 TB <br/> Quinta generazione: <br/>-1 TB per 4, 8, 16 vcore<br/>- 2 TB per 24 vCore<br/>- 4 TB per 32, 40, 64, 80 vCore |
| Dimensioni massime del database | Fino alla dimensione dell'istanza attualmente disponibile (max 2 TB-8 TB, a seconda del numero di VCore). | Fino alla dimensione dell'istanza attualmente disponibile (max 1 TB-4 TB a seconda del numero di VCore). |
| Dimensioni max di tempDB | Limitato a 24 GB/vCore (96-1.920 GB) e alle dimensioni di archiviazione dell'istanza attualmente disponibili.<br/>Aggiungere altri Vcore per ottenere ulteriore spazio in TempDB.<br/> Le dimensioni del file di log sono limitate a 120 GB.| Fino alle dimensioni di archiviazione dell'istanza attualmente disponibili. |
| Numero massimo di database per istanza | 100, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. | 100, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. |
| Numero massimo di file di database per istanza | Fino a 280, a meno che non sia stata raggiunta la dimensione di archiviazione dell'istanza o il limite di [spazio di allocazione di archiviazione su disco Premium](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) | 32.767 file per database, a meno che non sia stato raggiunto il limite delle dimensioni di archiviazione dell'istanza. |
| Dimensioni massime file di dati | Limitato alle dimensioni di archiviazione delle istanze attualmente disponibili (max 2 TB-8 TB) e [allo spazio di allocazione dell'archiviazione su disco Premium di Azure](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files). | Limitato alle dimensioni di archiviazione delle istanze attualmente disponibili (fino a 1 TB-4 TB). |
| Dimensioni massime file di log | Limitato a 2 TB e alle dimensioni di archiviazione dell'istanza attualmente disponibili. | Limitato a 2 TB e alle dimensioni di archiviazione dell'istanza attualmente disponibili. |
| Dati/Log di IOPS (approssimativi) | Fino a 30-40 K IOPS per istanza *, 500-7500 per file<br/>\*[Aumentare le dimensioni del file per ottenere più IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K (2500 IOPS/vCore)<br/>Aggiungere altri Vcore per ottenere prestazioni di i/o migliori. |
| Limite velocità effettiva scrittura log (per istanza) | 3 MB/s per ogni vCore<br/>Massimo 22 MB/s | 4 MB/s per vCore<br/>Max 48 MB/s |
| Dati effettivi (approssimativi) | 100 - 250 MB/s per ogni file<br/>\*[Aumentare le dimensioni del file per ottenere prestazioni di i/o migliori](#file-io-characteristics-in-general-purpose-tier) | Non limitato. |
| Latenza IO di archiviazione (approssimativa) | 5-10 ms | 1-2 ms |
| OLTP in memoria | Non supportato | Disponibile, [le dimensioni dipendono dal numero di vCore](#in-memory-oltp-available-space) |
| Numero massimo di sessioni | 30000 | 30000 |
| [Repliche di sola lettura](sql-database-read-scale-out.md) | 0 | 1 (incluso nel prezzo) |

> [!NOTE]
> - Le **dimensioni di archiviazione dell'istanza attualmente disponibili** sono la differenza tra le dimensioni delle istanze riservate e lo spazio di archiviazione usato.
> - La dimensione di archiviazione dell'istanza, che viene confrontata con la dimensione massima di archiviazione, include la dimensione dei dati e dei file di log presenti sia nel database utenti che in quello di sistema. Usare la vista di sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> per determinare lo spazio totale usato dai database. I log degli errori non vengono salvati in modo permanente e non sono inclusi nella dimensione. I backup non sono inclusi nella dimensione di archiviazione.
> - La velocità effettiva e gli IOPS nel livello di per utilizzo generico dipendono anche dalle [dimensioni del file](#file-io-characteristics-in-general-purpose-tier) non esplicitamente limitate dall'istanza gestita.
> - È possibile creare un'altra replica leggibile in un'area di Azure diversa usando i gruppi di failover automatico.
> - Il numero massimo di IOPS dell'istanza dipende dal layout del file e dalla distribuzione del carico di lavoro. Ad esempio, se si creano 7 file da 1 TB con Max 5K IOPS ciascuno e 7 piccoli file (inferiori a 128 GB) con 500 IOPS ciascuno, è possibile ottenere 38500 IOPS per istanza (7x5000 + 7x500) se il carico di lavoro può usare tutti i file. Si noti che per i backup automatici viene usata anche una certa quantità di IOPS.

> [!NOTE]
> Altre informazioni sui [limiti delle risorse nei pool di istanze gestite sono disponibili in questo articolo](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Caratteristiche di i/o del file nel livello per utilizzo generico

In per utilizzo generico livello di servizio ogni file di database sta ottenendo IOPS e velocità effettiva dedicati che dipendono dalle dimensioni del file. I file più grandi ricevono un numero maggiore di IOPS e velocità effettiva. Le caratteristiche di i/o dei file di database sono illustrate nella tabella seguente:

| Dimensione del file | >= 0 e <= 128 GiB | >128 e <= 256 GiB | >256 e <= 512 GiB | >0,5 e <= 1 TiB    | >1 e <= 2 TiB    | >2 e <= 4 TiB | >4 e <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS per file       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12.500   |
| Velocità effettiva per file | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Se si nota una latenza di i/o elevata per un file di database o si osserva che IOPS/velocità effettiva sta raggiungendo il limite, è possibile migliorare [le prestazioni aumentando le dimensioni del file](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Esistono anche limiti a livello di istanza, come la velocità effettiva massima di scrittura del log di 22 MB/s, quindi potrebbe non essere possibile raggiungere il file nel file di log perché si raggiunge il limite della velocità effettiva dell'istanza.

## <a name="supported-regions"></a>Aree supportate

È possibile creare istanze gestite solo nelle [aree supportate](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Per creare un'istanza gestita in un'area che non è attualmente supportata, è possibile [inviare una richiesta di supporto tramite il portale di Azure](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

Istanza gestita supporta attualmente la distribuzione solo sui seguenti tipi di sottoscrizioni:

- [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provider di servizi cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Sottoscrizioni con credito Azure mensile per Sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limiti delle risorse a livello di area

I tipi di sottoscrizioni supportati possono contenere un numero limitato di risorse per area. Istanza gestita prevede due limiti predefiniti per ogni area di Azure, che possono essere aumentati su richiesta creando una [richiesta di supporto speciale nella portale di Azure](quota-increase-request.md) a seconda di un tipo di sottoscrizione:

- **Limite di subnet**: numero massimo di subnet in cui vengono distribuite le istanze gestite in una singola area.
- **limite unità vCore**: numero massimo di unità vCore che possono essere distribuite in tutte le istanze di una singola area. Un vCore GP usa un'unità vCore e uno BC vCore accetta 4 unità vCore. Il numero totale di istanze non è limitato a condizione che si trovi all'interno del limite di unità vCore.

> [!Note]
> Questi limiti sono impostazioni predefinite e non limitazioni tecniche. I limiti possono essere aumentati su richiesta creando una richiesta di [supporto speciale nel portale di Azure](quota-increase-request.md) se sono necessarie più istanze gestite nell'area corrente. In alternativa, è possibile creare nuove istanze gestite in un'altra area di Azure senza inviare richieste di supporto.

La tabella seguente illustra i **limiti internazionali predefiniti** per i tipi di sottoscrizione supportati (i limiti predefiniti possono essere estesi usando la richiesta di supporto descritta di seguito):

|Tipo di sottoscrizione| Numero massimo di subnet istanza gestite | Numero massimo di unità vCore * |
| :---| :--- | :--- |
|Pagamento in base al consumo|3|320|
|CSP |8 (15 in alcune aree * *)|960 (1440 in alcune aree * *)|
|Sviluppo/test con pagamento in base al consumo|3|320|
|Sviluppo/test Enterprise|3|320|
|Contratto Enterprise|8 (15 in alcune aree * *)|960 (1440 in alcune aree * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e MSDN Platforms|2|32|

\*Per la pianificazione delle distribuzioni, tenere presente che il livello di servizio business critical (BC) richiede quattro (4) volte più capacità vCore rispetto al livello di servizio per utilizzo generico (GP). Ad esempio: 1 vCore GP = 1 unità vCore e 1 BC vCore = 4 unità vCore. Per semplificare l'analisi del consumo rispetto ai limiti predefiniti, riepilogare le unità vCore in tutte le subnet dell'area in cui vengono distribuite le istanze gestite e confrontare i risultati con i limiti di unità di istanza per il tipo di sottoscrizione. Il **numero massimo di unità vCore** viene applicato a ogni sottoscrizione in un'area. Non esiste alcun limite per le singole subnet, ad eccezione del fatto che la somma di tutti i Vcore distribuiti tra più subnet deve essere minore o uguale al **numero massimo di unità vCore**.

\*\*I limiti di subnet e vCore più grandi sono disponibili nelle aree seguenti: Australia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Europa occidentale, Stati Uniti occidentali 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Richiedere un aumento della quota per l'istanza gestita di SQL

Se sono necessarie più istanze gestite nelle aree correnti, inviare una richiesta di supporto per estendere la quota usando il portale di Azure. Per altre informazioni, vedere [incremento della quota di richieste per il database SQL di Azure](quota-increase-request.md).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'istanza gestita, vedere informazioni su [istanza gestita](sql-database-managed-instance.md).
- Per informazioni sui prezzi, vedere [prezzi dell'istanza gestita di database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Per informazioni su come creare la prima istanza gestita, vedere [la Guida introduttiva](sql-database-managed-instance-get-started.md).
