---
title: Limiti delle risorse
titleSuffix: Azure SQL Managed Instance
description: Questo articolo fornisce una panoramica dei limiti delle risorse per Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: 11c3de703a4b37318b7b99f60d74190fe8ec8610
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077371"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Panoramica dei limiti delle risorse di Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo fornisce una panoramica delle caratteristiche tecniche e dei limiti delle risorse per Istanza gestita SQL di Azure e fornisce informazioni su come richiedere un aumento a questi limiti.

> [!NOTE]
> Per informazioni sulle differenze nelle funzionalità e nelle istruzioni T-SQL supportate, vedere [Confronto tra le funzionalità](../database/features-comparison.md) e [Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server](transact-sql-tsql-differences-sql-server.md). Per le differenze generali tra i livelli di servizio per il database SQL di Azure e SQL Istanza gestita vedere [confronto tra livelli di servizio](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Caratteristiche delle generazioni dell'hardware

SQL Istanza gestita presenta le caratteristiche e i limiti delle risorse che dipendono dall'infrastruttura e dall'architettura sottostanti. SQL Istanza gestita può essere distribuito in due generazioni hardware: Gen4 e quinta generazione. Le generazioni hardware hanno caratteristiche diverse, descritte nella tabella seguente:

|   | **Quarta generazione** | **Quinta generazione** |
| --- | --- | --- |
| **Hardware** | Processori Intel® E5-2673 V3 (Haswell) a 2,4 GHz, unità SSD vCore = 1 PP (core fisico) | Intel® E5-2673 V4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) e Intel® 8272CL (Cascade Lake) 2,5 GHz Processor, Fast NVMe SSD, vCore = 1 LP (Hyper-thread) |
| **Numero di vCore** | 8, 16, 24 vCore | 4, 8, 16, 24, 32, 40, 64, 80 vCore |
| **Memoria massima (rapporto memoria/core)** | 7 GB per vCore<br/>Aggiungere altri vCore per ottenere una maggiore quantità di memoria. | 5,1 GB per vCore<br/>Aggiungere altri vCore per ottenere una maggiore quantità di memoria. |
| **Memoria OLTP in memoria massima** | Limite istanza: 1-1,5 GB per vCore| Limite istanza: 0,8 - 1,65 GB per vCore |
| **Archiviazione riservata istanza massima** |  Utilizzo generico: 8 TB<br/>Business Critical: 1 TB | Utilizzo generico: 8 TB<br/> Business Critical: 1 TB, 2 TB o 4 TB in base al numero di core |

> [!IMPORTANT]
> - L'hardware Gen4 viene eliminato e non è più disponibile per le nuove distribuzioni. Tutte le nuove istanze di SQL Istanza gestita devono essere distribuite in hardware quinta generazione.
> - Si consiglia di [trasferire l'istanza di SQL istanza gestita a hardware di generazione 5](../database/service-tiers-vcore.md) per sperimentare una gamma più ampia di vCore e scalabilità di archiviazione, rete accelerata, prestazioni di i/o migliori e latenza minima.

### <a name="in-memory-oltp-available-space"></a>Spazio disponibile OLTP in memoria 

La quantità di spazio OLTP in memoria nel livello di servizio [Business Critical](../database/service-tier-business-critical.md) dipende dal numero di vCore e dalla generazione dell'hardware. Nella tabella seguente sono elencati i limiti della memoria che possono essere utilizzati per gli oggetti di OLTP in memoria.

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

SQL Istanza gestita dispone di due livelli di servizio: [per utilizzo generico](../database/service-tier-general-purpose.md) e [business critical](../database/service-tier-business-critical.md). Questi livelli offrono [funzionalità diverse](../database/service-tiers-general-purpose-business-critical.md), come descritto nella tabella seguente.

> [!Important]
> Business critical livello di servizio fornisce una copia incorporata aggiuntiva della Istanza gestita SQL (replica secondaria) che può essere utilizzata per il carico di lavoro di sola lettura. Se è possibile separare le query di lettura e scrittura e le query di sola lettura/analitica/report, si riceveranno due volte il vcore e la memoria per lo stesso prezzo. La replica secondaria potrebbe ritardare alcuni secondi alla base dell'istanza primaria, quindi è progettata per l'offload dei carichi di lavoro di report/analisi che non necessitano dello stato di dati esatto corrente. Nella tabella seguente, le **query di sola lettura** sono quelle eseguite nella replica secondaria.

| **Funzionalità** | **Utilizzo generico** | **Business Critical** |
| --- | --- | --- |
| Numero di vCore\* | Quarta generazione: 8, 16, 24<br/>Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 | Quarta generazione: 8, 16, 24 <br/> Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Lo stesso numero di vCore è dedicato alle query di sola lettura. |
| Memoria massima | Quarta generazione: 56 GB - 168 GB (7 GB/vCore)<br/>Quinta generazione: 20,4 GB - 408 GB (5,1 GB/vCore)<br/>Aggiungere altri vCore per ottenere una maggiore quantità di memoria. | Quarta generazione: 56 GB - 168 GB (7 GB/vCore)<br/>Quinta generazione: 20,4 GB - 408 GB (5,1 GB/vCore) per query di lettura/scrittura<br/>+ altri 20,4 GB - 408 GB (5,1 GB/vCore) per le query di sola lettura.<br/>Aggiungere altri vCore per ottenere una maggiore quantità di memoria. |
| Dimensioni massime archiviazione istanze (riservate) | - 2 TB per 4 vCore (solo quinta generazione)<br/>- 8 TB per altre dimensioni | Quarta generazione: 1 TB <br/> Quinta generazione: <br/>- 1 TB per 4, 8, 16 vCore<br/>- 2 TB per 24 vCore<br/>- 4 TB per 32, 40, 64, 80 vCore |
| Dimensioni massime del database | Fino alle dimensioni dell'istanza attualmente disponibili (max 2 TB - 8 TB, a seconda del numero di vCore). | Fino alle dimensioni dell'istanza attualmente disponibili (max 1 TB - 4 TB a seconda del numero di vCore). |
| Dimensioni max di tempDB | Limitate a 24 GB/vCore (96 - 1.920 GB) e alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile.<br/>Aggiungere altri vCore per ottenere più spazio in TempDB.<br/> Le dimensioni del file di log sono limitate a 120 GB.| Fino alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile. |
| Numero massimo di database per istanza | 100 database utente, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. | 100 database utente, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. |
| Numero massimo di file di database per istanza | Fino a 280, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza o per lo [spazio di allocazione dell'archiviazione nel disco Premium di Azure](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files). | 32.767 file per database, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. |
| Dimensioni massime dei file di dati | Limitate alle dimensioni dello spazio di archiviazione delle istanze attualmente disponibile (max 2 TB - 8 TB) e allo [spazio di allocazione dell'archiviazione nel disco Premium di Azure](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files). | Limitate alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile (fino a 1 TB - 4 TB). |
| Dimensioni massime del file di log | Limitate a 2 TB e alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile. | Limitate a 2 TB e alle dimensioni dello spazio di archiviazione dell'istanza attualmente disponibile. |
| Dati/Log di IOPS (approssimativi) | Fino a 30 - 40 K IOPS per istanza*, 500 - 7500 per file<br/>\*[Aumentare le dimensioni del file per ottenere più IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K (4000 IOPS/vCore)<br/>Aggiungere altri vCore per ottenere prestazioni I/O migliori. |
| Limite di velocità effettiva della scrittura di log (per istanza) | 3 MB/s per ogni vCore<br/>Massimo 120 MB/s per istanza<br/>22-65 MB/s per database<br/>\*[Aumentare le dimensioni del file per ottenere prestazioni I/O migliori](#file-io-characteristics-in-general-purpose-tier) | 4 MB/s per vCore<br/>Massimo 96 MB/s |
| Dati effettivi (approssimativi) | 100 - 250 MB/s per ogni file<br/>\*[Aumentare le dimensioni del file per ottenere prestazioni I/O migliori](#file-io-characteristics-in-general-purpose-tier) | Non limitate. |
| Latenza I/O di archiviazione (approssimativa) | 5-10 ms | 1-2 ms |
| OLTP in memoria | Non supportate | Disponibile, [le dimensioni dipendono dal numero di vCore](#in-memory-oltp-available-space) |
| Numero massimo di sessioni | 30000 | 30000 |
| Numero massimo di ruoli di lavoro simultanei (richieste) | Quarta generazione: 210 * numero di vCore + 800<br>Quinta generazione: 105 * numero di vCore + 800 | Quarta generazione: 210 * numero di vCore + 800<br>Quinta generazione: 105 * numero di vCore + 800 |
| [Repliche di sola lettura](../database/read-scale-out.md) | 0 | 1 (inclusa nel prezzo) |
| Isolamento del calcolo | Quinta generazione:<br/>\- Supportato per 80 vCore<br/>\- Non supportato per altre dimensioni<br/><br/>Quarta generazione non supportata per deprecazione|Quinta generazione:<br/>\- Supportato per 60, 64, 80 vCore<br/>\- Non supportato per altre dimensioni<br/><br/>Quarta generazione non supportata per deprecazione|


Di seguito sono riportate alcune considerazioni aggiuntive: 

- Per calcolare le **dimensioni dello spazio di archiviazione delle istanze attualmente disponibile** è sufficiente fare la differenza tra le dimensioni dell'istanza riservata e lo spazio di archiviazione usato.
- Le dimensioni dei file di dati e di log nei database utente e di sistema sono incluse nelle dimensioni di archiviazione dell'istanza rispetto al limite massimo di dimensioni di archiviazione. Utilizzare la vista di sistema [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) per determinare lo spazio totale utilizzato dai database. I log degli errori non vengono salvati in modo permanente e non sono inclusi nella dimensione. I backup non sono inclusi nella dimensione di archiviazione.
- La velocità effettiva e gli IOPS nel livello di per utilizzo generico dipendono anche dalle [dimensioni del file](#file-io-characteristics-in-general-purpose-tier) non esplicitamente limitate da SQL istanza gestita.
  È possibile creare un'altra replica leggibile in un'area di Azure diversa usando i [gruppi di failover automatico](../database/auto-failover-group-configure.md)
- Il numero massimo di IOPS dell'istanza dipende dal layout del file e dalla distribuzione del carico di lavoro. Se, ad esempio, si creano 7 file da 1 TB, ciascuno con 5000 IOPS, e 7 piccoli file (inferiori a 128 GB), ciascuno con 500 IOPS, è possibile ottenere 38.500 IOPS per istanza (7 x 5000 + 7 x 500), se il carico di lavoro può usare tutti i file. Si noti che alcuni IOPS vengono usati anche per i backup automatici.

Altre informazioni sui [limiti delle risorse nei pool di istanza gestita SQL sono disponibili in questo articolo](instance-pools-overview.md#resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Caratteristiche di I/O dei file nel livello Utilizzo generico

Nel livello di servizio per utilizzo generico ogni file di database ottiene IOPS e velocità effettiva dedicati che dipendono dalle dimensioni del file. I file più grandi ottengono più IOPS e velocità effettiva. Le caratteristiche di i/o dei file di database sono illustrate nella tabella seguente:

| Dimensione del file | >=0 e <=128 GiB | >128 e <= 512 GiB | >0,5 e <=1 TiB    | >1 e <=2 TiB    | >2 e <=4 TiB | >4 e <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS per file       | 500   | 2300              | 5000              | 7500              | 7500              | 12.500   |
| Velocità effettiva per file | 100 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Se si nota una latenza di I/O elevata per un file di database o si scopre che il rapporto IOPS/velocità effettiva sta raggiungendo il limite, è possibile migliorare le prestazioni [aumentando le dimensioni del file](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Esiste inoltre un limite a livello di istanza per la massima velocità effettiva di scrittura del log (ovvero 22 MB/s), pertanto potrebbe non essere possibile raggiungere il file massimo in tutto il file di log perché si sta raggiungendo il limite di velocità effettiva dell'istanza.

## <a name="supported-regions"></a>Aree supportate

È possibile creare SQL Istanza gestita solo nelle [aree supportate](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Per creare un Istanza gestita SQL in un'area non supportata al momento, è possibile [inviare una richiesta di supporto tramite il portale di Azure](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

SQL Istanza gestita supporta attualmente la distribuzione solo sui seguenti tipi di sottoscrizioni:

- [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provider di servizi cloud (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Sottoscrizioni con il credito Azure mensile per i titolari di sottoscrizioni di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limiti delle risorse a livello di area

> [!Note]
> Per le informazioni più aggiornate sulla disponibilità delle aree per le sottoscrizioni, selezionare prima di tutto [selezionare un'area](https://aka.ms/sqlcapacity).

I tipi di sottoscrizioni supportati possono contenere un numero limitato di risorse per area. SQL Istanza gestita prevede due limiti predefiniti per ogni area di Azure, che possono essere aumentati su richiesta creando una [richiesta di supporto speciale nell'portale di Azure](../database/quota-increase-request.md) a seconda del tipo di sottoscrizione:

- **Limite subnet** : numero massimo di subnet in cui le istanze di SQL istanza gestita vengono distribuite in una singola area.
- **Limite unità vCore** : il numero massimo di unità vCore che possono essere distribuite in tutte le istanze di una singola area. Un vCore di Utilizzo generico usa un'unità vCore, mentre un vCore Business Critical accetta 4 unità vCore. Il numero totale di istanze non è limitato, almeno finché rientra nel limite massimo di unità vCore.

> [!Note]
> Questi limiti sono impostazioni predefinite e non limitazioni tecniche. I limiti possono essere aumentati su richiesta creando una richiesta di [supporto speciale nel portale di Azure](../database/quota-increase-request.md) se sono necessarie più istanze nell'area corrente. In alternativa, è possibile creare nuove istanze di SQL Istanza gestita in un'altra area di Azure senza inviare richieste di supporto.

La tabella seguente illustra i **limiti predefiniti a livello di area** per i tipi di sottoscrizione supportati (i limiti predefiniti possono essere estesi usando la richiesta di supporto descritta di seguito):

|Tipo di sottoscrizione| Numero massimo di subnet di SQL Istanza gestita | Numero massimo di unità vCore* |
| :---| :--- | :--- |
|Pagamento in base al consumo|3|320|
|CSP |8 (15 in alcune aree**)|960 (1440 in alcune aree**)|
|Sviluppo/test con pagamento in base al consumo|3|320|
|Sviluppo/test Enterprise|3|320|
|Contratto Enterprise|8 (15 in alcune aree**)|960 (1440 in alcune aree**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e MSDN Platforms|2|32|

\* Nella pianificazione delle distribuzioni, tenere presente che il livello di servizio Business Critical (BC) richiede una quantità di vCore quattro (4) volte superiore rispetto al livello di servizio Utilizzo generico (GP). Ad esempio: 1 vCore GP = 1 unità vCore e 1 vCore BC = 4 unità vCore. Per semplificare l'analisi del consumo rispetto ai limiti predefiniti, riepilogare le unità vCore in tutte le subnet dell'area in cui viene distribuito SQL Istanza gestita e confrontare i risultati con i limiti di unità di istanza per il tipo di sottoscrizione. Il **numero massimo di unità vCore** si applica a ogni sottoscrizione presente in un'area. Non esiste alcun limite per singole subnet, ad eccezione del fatto che la somma di tutti i vCore distribuiti tra più subnet deve essere inferiore o uguale al **numero massimo di unità vCore** .

\*\* Sono disponibili limiti di subnet e vCore maggiori nelle aree geografiche seguenti: Australia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Regno Unito meridionale, Europa occidentale, Stati Uniti occidentali 2.

> [!IMPORTANT]
> Se il limite di vCore e subnet è 0, significa che il limite locale predefinito per il tipo di sottoscrizione non è impostato. È anche possibile usare la richiesta di aumento della quota per ottenere l'accesso alla sottoscrizione in un'area specifica seguendo la stessa procedura specificando i valori di vCore e subnet richiesti.

## <a name="request-a-quota-increase"></a>Richiedere un aumento della quota

Se sono necessarie più istanze nelle aree correnti, inviare una richiesta di supporto per estendere la quota usando il portale di Azure. Per altre informazioni, vedere [Request quota increases for Azure SQL Database](../database/quota-increase-request.md) (Richiedere aumenti di quota per il database SQL di Azure).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su SQL Istanza gestita, vedere [che cos'è un istanza gestita SQL?](sql-managed-instance-paas-overview.md).
- Per informazioni sui prezzi, vedere la pagina relativa ai [prezzi di SQL istanza gestita](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Per informazioni su come creare la prima Istanza gestita SQL, vedere [la Guida introduttiva](instance-create-quickstart.md).
