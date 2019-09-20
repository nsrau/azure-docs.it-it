---
title: Limiti delle risorse del database SQL di Azure per le istanze gestite | Microsoft Docs
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
ms.date: 09/16/2019
ms.openlocfilehash: d0356ff61ec8073e7fe69c3b09cbbdd8845fb787
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128907"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Panoramica dei limiti delle risorse dell'istanza gestita di database SQL di Azure

Questo articolo fornisce una panoramica delle caratteristiche tecniche e dei limiti delle risorse per istanza gestita di database SQL di Azure e fornisce informazioni su come richiedere un aumento a questi limiti.

> [!NOTE]
> Per informazioni sulle differenze nelle funzionalità e nelle istruzioni T-SQL supportate, vedere [Confronto tra le funzionalità](sql-database-features.md) e [Differenze T-SQL tra Istanza gestita del database SQL di Azure e SQL Server](sql-database-managed-instance-transact-sql-information.md). Per differenze generali tra i livelli di servizio nel database singolo e nell'istanza gestita, vedere [confronto tra livelli di servizio](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="instance-level-resource-limits"></a>Limiti delle risorse a livello di istanza

L'istanza gestita presenta le caratteristiche e i limiti delle risorse che dipendono dall'infrastruttura e dall'architettura sottostanti. I limiti dipendono dalla generazione dell'hardware e dal livello di servizio.

### <a name="hardware-generation-characteristics"></a>Caratteristiche delle generazioni dell'hardware

Istanza gestita di database SQL di Azure può essere distribuita in due generazioni hardware: Gen4 e quinta generazione. Le generazioni hardware hanno caratteristiche diverse, come descritto nella tabella seguente:

|   | **Quarta generazione** | **Quinta generazione** |
| --- | --- | --- |
| Hardware | Processori Intel E5-2673 v3 (Haswell) a 2,4 GHz, con unità vCore SSD = 1 PP (core fisico) | Processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz, unità SSD NVMe veloce, vCore = 1 LP (hyperthread) |
| Numero di vCore | 8, 16, 24 vCore | 4, 8, 16, 24, 32, 40, 64, 80 vcore |
| Memoria massima (rapporto memoria/Core) | 7 GB per vCore<br/>Aggiungere altri Vcore per ottenere una maggiore quantità di memoria. | 5,1 GB per vCore<br/>Aggiungere altri Vcore per ottenere una maggiore quantità di memoria. |
| Memoria OLTP max in memoria | Limite istanza: 3 GB per vCore<br/>Limiti del database:<br/> -8-core: 8 GB per database<br/> -16 core: 20 GB per database<br/> -24-core: 36 GB per database | Limite istanza: 2,5 GB per vCore<br/>Limiti del database:<br/> -8-core: 13 GB per database<br/> -16 core: 32 GB per database |
| Archiviazione riservata istanza massima |  Per utilizzo generico: 8 TB<br/>Business critical: 1 TB | Per utilizzo generico: 8 TB<br/> Business critical 1 TB, 2 TB o 4 TB a seconda del numero di core |

> [!IMPORTANT]
> - L'hardware Gen4 è in fase di eliminazione. È consigliabile distribuire nuove istanze gestite nell'hardware quinta generazione.
> - L'hardware Gen4 al momento è ancora disponibile solo nelle aree seguenti: Europa settentrionale, Europa occidentale, Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti occidentali 2, Stati Uniti centrali, Canada centrale, India meridionale, Asia sudorientale e Corea centrale.

### <a name="service-tier-characteristics"></a>Caratteristiche del livello di servizio

Istanza gestita ha due livelli di servizio: [Per utilizzo generico](sql-database-service-tier-general-purpose.md) e [business critical](sql-database-service-tier-business-critical.md). Questi livelli forniscono [funzionalità diverse](sql-database-service-tiers-general-purpose-business-critical.md), come descritto nella tabella seguente:

| **Funzionalità** | **Utilizzo generico** | **Business Critical** |
| --- | --- | --- |
| Numero di vCore\* | Quarta generazione: 8, 16, 24<br/>Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 | Quarta generazione: 8, 16, 24 <br/> Quinta generazione: 4, 8, 16, 24, 32, 40, 64, 80 |
| Memoria massima | Quarta generazione: 56 GB-168 GB (7 GB/vCore)<br/>Quinta generazione: 20,4 GB-408 GB (5.1 GB/vCore)<br/>Aggiungere altri Vcore per ottenere una maggiore quantità di memoria. | Quarta generazione: 56 GB-168 GB (7 GB/vCore)<br/>Quinta generazione: 20,4 GB-408 GB (5.1 GB/vCore)<br/>Aggiungere altri Vcore per ottenere una maggiore quantità di memoria. |
| Dimensioni massime archiviazione istanze (riservate) | -2 TB per 4 Vcore (solo quinta generazione)<br/>-8 TB per altre dimensioni | Quarta generazione: 1 TB <br/> Quinta generazione: <br/>-1 TB per 4, 8, 16 vcore<br/>- 2 TB per 24 vCore<br/>- 4 TB per 32, 40, 64, 80 vCore |
| Dimensioni massime del database | Fino alla dimensione dell'istanza attualmente disponibile (max 2 TB-8 TB, a seconda del numero di VCore). | Fino alla dimensione dell'istanza attualmente disponibile (max 1 TB-4 TB a seconda del numero di VCore). |
| Dimensioni max di tempDB | Limitato a 24 GB/vCore (96-1.920 GB) e alle dimensioni dell'istanza attualmente disponibili.<br/>Aggiungere altri Vcore per ottenere ulteriore spazio in TempDB. | Fino alle dimensioni dell'istanza attualmente disponibili. Le dimensioni del file di log TempDB sono attualmente limitate a 24 GB/vCore. |
| Numero massimo di database per istanza | 100, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. | 100, a meno che non sia stato raggiunto il limite per le dimensioni di archiviazione dell'istanza. |
| Numero massimo di file di database per istanza | Fino a 280, a meno che non sia stata raggiunta la dimensione di archiviazione dell'istanza o il limite di [spazio di allocazione di archiviazione su disco Premium](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files) | 32.767 file per database, a meno che non sia stato raggiunto il limite delle dimensioni di archiviazione dell'istanza. |
| Dimensioni massime file | Limitato a 8 TB, dimensioni attualmente disponibili per le istanze (max 2 TB-8 TB) e [spazio di allocazione dell'archiviazione su disco Premium di Azure](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | Limitato a 4 TB e dimensioni delle istanze attualmente disponibili (fino a 1 TB-4 TB). |
| Dimensioni massime file di log | Limitato a 2 TB, dimensioni dell'istanza attualmente disponibili e [spazio di allocazione dell'archiviazione su disco Premium di Azure](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | Limitato a 2 TB e alle dimensioni dell'istanza attualmente disponibili. |
| Dati/Log di IOPS (approssimativi) | 500 - 7.500 per file<br/>\*[Aumentare le dimensioni del file per ottenere più IOPS](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5,5 k-110 K (1375/vCore)<br/>Aggiungere altri Vcore per ottenere prestazioni di i/o migliori. |
| Limite velocità effettiva scrittura log (per istanza) | 3 MB/s per ogni vCore<br/>Massimo 22 MB/s | 4 MB/s per vCore<br/>Max 48 MB/s |
| Dati effettivi (approssimativi) | 100 - 250 MB/s per ogni file<br/>\*[Aumentare le dimensioni del file per ottenere prestazioni di i/o migliori](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | Non limitato. |
| Latenza IO di archiviazione (approssimativa) | 5-10 ms | 1-2 ms |
| OLTP in memoria | Non supportate | Disponibile |
| Numero massimo di sessioni | 30000 | 30000 |
| [Repliche di sola lettura](sql-database-read-scale-out.md) | 0 | 1 (incluso nel prezzo) |

> [!NOTE]
> - La dimensione di archiviazione dell'istanza, che viene confrontata con la dimensione massima di archiviazione, include la dimensione dei dati e dei file di log presenti sia nel database utenti che in quello di sistema. Usare la vista di sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> per determinare lo spazio totale usato dai database. I log degli errori non vengono salvati in modo permanente e non sono inclusi nella dimensione. I backup non sono inclusi nella dimensione di archiviazione.
> - La velocità effettiva e IOPS dipendono anche dalle dimensioni di pagina non esplicitamente limitate dall'istanza gestita.
> È possibile creare un'altra replica leggibile in un'area di Azure diversa usando i gruppi di failover automatico.

> [!NOTE]
> Altre informazioni sui [limiti delle risorse nei pool di istanze gestite sono disponibili in questo articolo](sql-database-instance-pools.md#instance-pools-resource-limitations).

## <a name="supported-regions"></a>Aree supportate

È possibile creare istanze gestite solo nelle [aree supportate](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Per creare un'istanza gestita in un'area che non è attualmente supportata, è possibile [inviare una richiesta di supporto tramite il portale di Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

Istanza gestita supporta attualmente la distribuzione solo sui seguenti tipi di sottoscrizioni:

- [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provider di servizi cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Sottoscrizioni con credito Azure mensile per Sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limiti delle risorse a livello di area

I tipi di sottoscrizioni supportati possono contenere un numero limitato di risorse per area. Istanza gestita prevede due limiti predefiniti per ogni area di Azure, che possono essere aumentati su richiesta mediante la creazione di una [richiesta di supporto speciale nel portale di Azure](#obtaining-a-larger-quota-for-sql-managed-instance), a seconda del tipo di sottoscrizione:

- **Limite di subnet**: numero massimo di subnet in cui vengono distribuite le istanze gestite in una singola area.
- **limite unità vCore**: Numero massimo di unità vCore che possono essere distribuite in tutte le istanze di una singola area. Un vCore GP usa un'unità vCore e uno BC vCore accetta 4 unità vCore. Il numero totale di istanze non è limitato a condizione che si trovi all'interno del limite di unità vCore.

> [!Note]
> Questi limiti sono impostazioni predefinite e non limitazioni tecniche. I limiti possono essere aumentati su richiesta creando una richiesta di [supporto speciale nel portale di Azure](#obtaining-a-larger-quota-for-sql-managed-instance) se sono necessarie più istanze gestite nell'area corrente. In alternativa, è possibile creare nuove istanze gestite in un'altra area di Azure senza inviare richieste di supporto.

La tabella seguente illustra i **limiti internazionali predefiniti** per i tipi di sottoscrizione supportati (i limiti predefiniti possono essere estesi usando la richiesta di supporto descritta di seguito):

|Tipo di sottoscrizione| Numero massimo di subnet istanza gestite | Numero massimo di unità vCore * |
| :---| :--- | :--- |
|Pagamento in base al consumo|3|320|
|CSP |8 (15 in alcune aree * *)|960 (1440 in alcune aree * *)|
|Sviluppo/test con pagamento in base al consumo|3|320|
|Sviluppo/test Enterprise|3|320|
|EA|8 (15 in alcune aree * *)|960 (1440 in alcune aree * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e MSDN Platforms|2|32|

\*Per la pianificazione delle distribuzioni, tenere presente che il livello di servizio business critical (BC) richiede quattro (4) volte più capacità vCore rispetto al livello di servizio per utilizzo generico (GP). Esempio: 1 GP vCore = 1 unità vCore e 1 BC vCore = 4 unità vCore. Per semplificare l'analisi del consumo rispetto ai limiti predefiniti, riepilogare le unità vCore in tutte le subnet dell'area in cui vengono distribuite le istanze gestite e confrontare i risultati con i limiti di unità di istanza per il tipo di sottoscrizione. Il **numero massimo di unità vCore** viene applicato a ogni sottoscrizione in un'area. Non esiste alcun limite per le singole subnet, ad eccezione del fatto che la somma di tutti i Vcore distribuiti tra più subnet deve essere minore o uguale al **numero massimo di unità vCore**.

\*\*I limiti di subnet e vCore maggiori sono disponibili nelle aree geografiche seguenti: Australia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Europa occidentale, Stati Uniti occidentali 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Ottenere una quota maggiore per l'istanza gestita di SQL

Se sono necessarie più istanze gestite nelle aree correnti, inviare una richiesta di supporto per estendere la quota usando il portale di Azure.
Per avviare il processo di acquisizione di una quota maggiore:

1. Aprire **Guida e supporto** e fare clic su **Nuova richiesta di supporto**.

   ![Guida e supporto tecnico](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Nella scheda Generale per la nuova richiesta di supporto:
   - Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)** .
   - In **Sottoscrizione** selezionare la propria sottoscrizione.
   - Per **Tipo di quota** selezionare **Istanza gestita di database SQL**.
   - Per **Piano di supporto** selezionare il piano di supporto in uso.

     ![Tipo di problema: Quota](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Fare clic su **Avanti**.
4. Nella **scheda problema** relativa alla nuova richiesta di supporto:
   - Per **Gravità** selezionare il livello di gravità del problema.
   - In **Dettagli** inserire informazioni aggiuntive relative al problema riscontrato, inclusi i messaggi di errore.
   - In **Caricamento file** allegare un file con ulteriori informazioni (fino a 4 MB).

     ![Dettagli del problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Una richiesta valida deve includere:
     > - Area in cui è necessario aumentare il limite della sottoscrizione.
     > - Numero necessario di Vcore, per livello di servizio nelle subnet esistenti dopo l'aumento della quota (se è necessario espandere una delle subnet esistenti.
     > - Numero necessario di nuove subnet e numero totale di Vcore per ogni livello di servizio all'interno delle nuove subnet, se è necessario distribuire istanze gestite in nuove subnet.

5. Fare clic su **Avanti**.
6. Nella scheda Informazioni contatto della nuova richiesta di supporto immettere la modalità di contatto preferita (posta elettronica o telefono) e i dettagli di contatto.
7. Fare clic su **Create**(Crea).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'istanza gestita, vedere informazioni su [istanza gestita](sql-database-managed-instance.md).
- Per informazioni sui prezzi, vedere [Prezzi dell'istanza gestita di database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Per informazioni su come creare la prima istanza gestita, vedere [la Guida introduttiva](sql-database-managed-instance-get-started.md).
