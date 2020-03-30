---
title: Scopo generale e business critical
description: L'articolo illustra i livelli di servizio business e di scopo generale nel modello di acquisto basato su vCore.The article discusses the general purpose and business critical service tiers in the vCore-based purchasing model.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937846"
---
# <a name="azure-sql-database-service-tiers"></a>Livelli di servizio del database SQL di Azure

Il database SQL di Azure è basato sull'architettura del motore di database di SQL Server adattata per l'ambiente cloud per garantire la disponibilità del 99,99%, anche in caso di errore dell'infrastruttura. Nel database SQL di Azure vengono usati tre livelli di servizio, ognuno con un modello di architettura diverso. Questi livelli di servizio sono:

- [Scopo generale](sql-database-service-tier-general-purpose.md), progettato per carichi di lavoro orientati al budget.
- [Hyperscale](sql-database-service-tier-hyperscale.md), progettato per la maggior parte dei carichi di lavoro aziendali, che offre funzionalità di archiviazione altamente scalabili, scalabilità in lettura e ripristino rapido del database.
- [Business critical](sql-database-service-tier-business-critical.md), progettato per carichi di lavoro a bassa latenza con elevata resilienza agli errori e failover rapidi.

In questo articolo vengono illustrate le differenze tra i livelli di servizio, l'archiviazione e il backup per lo scopo generale e i livelli di servizio business critical nel modello di acquisto basato su vCore.

## <a name="service-tier-comparison"></a>Confronto tra livelli di servizioService tier comparison

Nella tabella seguente vengono descritte le differenze principali tra i livelli di servizio per l'ultima generazione (Gen5). Si noti che le caratteristiche del livello di servizio potrebbero essere diverse in Database singolo e istanza gestita.

| | Tipo di risorsa | Utilizzo generico |  Hyperscale | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **Ideale per** | |  Offre opzioni di calcolo e archiviazione bilanciate a prezzi convenienti. | La maggior parte dei carichi di lavoro aziendali. Dimensione automatica dello spazio di archiviazione con scalabilità verticale fino a 100 TB, scalabilità di calcolo verticale e orizzontale fluida, ripristino rapido del database. | Applicazioni OLTP con un tasso di transazione elevato e una bassa latenza di I/O. Offre la massima resilienza agli errori e ai failover rapidi utilizzando più repliche aggiornate in modo sincrono.|
|  **Disponibile nel tipo di risorsa:** ||Database singolo/pool elastico/istanza gestita | Database singolo | Database singolo/pool elastico/istanza gestita |
| **Dimensioni di calcolo**|Database singolo/pool elastico | Da 1 a 80 vCore | Da 1 a 80 vCore | Da 1 a 80 vCore |
| | Istanza gestita | 4, 8, 16, 24, 32, 40, 64, 80 vCore | N/D | 4, 8, 16, 24, 32, 40, 64, 80 vCore |
| | Pool di istanze gestite | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCore | N/D | N/D |
| **Tipo di archiviazione** | Tutti | Archiviazione remota Premium (per istanza) | Archiviazione disaccoppiata con cache SSD locale (per istanza) | Archiviazione SSD locale estremamente veloce (per istanza) |
| **Dimensioni del database** | Database singolo/pool elastico | 5 GB - 4 TB | Fino a 100 TB | 5 GB - 4 TB |
| | Istanza gestita  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Dimensioni archiviazione** | Database singolo/pool elastico | 5 GB - 4 TB | Fino a 100 TB | 5 GB - 4 TB |
| | Istanza gestita  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Dimensioni TempDB** | Database singolo/pool elastico | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Istanza gestita  | [24 GB per vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | Fino a 4 TB - [limitato dalle dimensioni dello storage](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Velocità effettiva di scrittura del logLog write throughput** | Database singolo | [1,875 MB/s per vCore (max 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s per vCore (massimo 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Istanza gestita | [3 MB/s per vCore (max 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s per vcore (max 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Disponibilità**|Tutti| 99,99% |  [99,95% con una replica secondaria, 99,99% con più repliche](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% con singolo database con ridondanza di zona](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Backup**|Tutti|RA-GRS, da 7 a 35 giorni (7 giorni per impostazione predefinita)| RA-GRS, 7 giorni, recupero point-in-time costante (PITR) | RA-GRS, da 7 a 35 giorni (7 giorni per impostazione predefinita) |
|**OLTP in memoria** | | N/D | N/D | Disponibile |
|**Repliche di sola letturaRead-only replicas**| | 0 built-in <br> 0 - 4 utilizzando la [replica geografica](sql-database-active-geo-replication.md) | 0 - 4 built-in | 1 built-in, incluso nel prezzo <br> 0 - 4 utilizzando la [replica geografica](sql-database-active-geo-replication.md) |
|**Prezzi/fatturazione** | Database singolo | [vCore, archiviazione riservata e archiviazione](https://azure.microsoft.com/pricing/details/sql-database/single/) di backup sono caricati. <br/>IOPS non viene addebitato. | [vCore per ogni replica e archiviazione usata](https://azure.microsoft.com/pricing/details/sql-database/single/) vengono addebitati. <br/>IOPS non ancora addebitato. | [vCore, archiviazione riservata e archiviazione](https://azure.microsoft.com/pricing/details/sql-database/single/) di backup sono caricati. <br/>IOPS non viene addebitato. |
|| database SQL | [vCore, archiviazione riservata e archiviazione](https://azure.microsoft.com/pricing/details/sql-database/managed/) di backup sono caricati. <br/>Le operazioni di I/O al secondo non vengono addebitate| N/D | [vCore, archiviazione riservata e archiviazione](https://azure.microsoft.com/pricing/details/sql-database/managed/) di backup sono caricati. <br/>IOPS non viene addebitato.| 
|**Modelli di sconto**| | [Istanze riservate](sql-database-reserved-capacity.md)<br/>[Vantaggio Azure Hybrid](sql-database-azure-hybrid-benefit.md) (non disponibile nelle sottoscrizioni di sviluppo/test)Azure Hybrid Benefit (not available on dev/test subscriptions)<br/>[Abbonamenti Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [Con pagamento](https://azure.microsoft.com/offers/ms-azr-0023p/) in base al costo per gli sviluppatori/test| [Vantaggio Azure Hybrid](sql-database-azure-hybrid-benefit.md) (non disponibile nelle sottoscrizioni di sviluppo/test)Azure Hybrid Benefit (not available on dev/test subscriptions)<br/>[Abbonamenti Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [Con pagamento](https://azure.microsoft.com/offers/ms-azr-0023p/) in base al costo per gli sviluppatori/test| [Istanze riservate](sql-database-reserved-capacity.md)<br/>[Vantaggio Azure Hybrid](sql-database-azure-hybrid-benefit.md) (non disponibile nelle sottoscrizioni di sviluppo/test)Azure Hybrid Benefit (not available on dev/test subscriptions)<br/>[Abbonamenti Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [Con pagamento](https://azure.microsoft.com/offers/ms-azr-0023p/) in base al costo per gli sviluppatori/test|

Per ulteriori informazioni, vedere le differenze dettagliate tra i livelli di servizio nelle pagine [Database singolo (vCore),](sql-database-vcore-resource-limits-single-databases.md)Pool di [database singoli (vCore),](sql-database-dtu-resource-limits-single-databases.md) [Database singolo (DTU),](sql-database-dtu-resource-limits-single-databases.md) [Pool di database singoli (DTU)](sql-database-dtu-resource-limits-single-databases.md)e [Istanza gestita.](sql-database-managed-instance-resource-limits.md)

> [!NOTE]
> Per informazioni sul livello di servizio di hyperscale nel modello di acquisto basato su vCore, vedere [livello di servizio hyperscale](sql-database-service-tier-hyperscale.md). Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Archiviazione di dati e log

I fattori seguenti influiscono sulla quantità di spazio di archiviazione utilizzato per i file di dati e di log e si applicano a General Purpose e Business Critical. Per informazioni dettagliate sull'archiviazione di dati e log in Hyperscale, vedere [Livello di servizio Hyperscale](sql-database-service-tier-hyperscale.md).

- Lo spazio di archiviazione allocato viene utilizzato dai file di dati (MDF) e dai file di registro (LDF).
- Ogni singola dimensione di calcolo del database supporta una dimensione massima del database, con una dimensione massima predefinita di 32 GB.
- Quando si configurano le dimensioni del singolo database richieste (la dimensione del file MDF), il 30% di spazio di archiviazione aggiuntivo viene aggiunto automaticamente per supportare i file LDF.
- La dimensione di archiviazione per un'istanza gestita del database SQL deve essere specificata in multipli di 32 GB.
- È possibile selezionare qualsiasi dimensione di database singola tra 10 GB e il massimo supportato.
  - Per l'archiviazione nei livelli di servizio standard o generico, aumentare o ridurre le dimensioni in incrementi di 10 GB.
  - Per l'archiviazione nei livelli di servizio critici premium o business, aumentare o ridurre le dimensioni in incrementi di 250 GB.
- Nel livello di servizio `tempdb` generico, usa un SSD collegato e questo costo di archiviazione è incluso nel prezzo di vCore.In the general purpose service tier, uses an attached SSD, and this storage cost is included in the vCore price.
- Nel livello di servizio `tempdb` business critical, condivide l'SSD allegato con `tempdb` i file MDF e LDF e il costo di archiviazione è incluso nel prezzo vCore.

> [!IMPORTANT]
> Viene addebitato lo spazio di archiviazione totale allocato per i file MDF e LDF.

Per monitorare la dimensione totale corrente dei file MDF e LDF, utilizzare [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Per monitorare la dimensione corrente dei singoli file MDF e LDF, usare [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere Gestire lo spazio file nel database SQL di Azure.For more information, see [Manage file space in Azure SQL Database.](sql-database-file-space-management.md)

## <a name="backups-and-storage"></a>Backup e archiviazione

L'archiviazione per i backup di database viene allocata per supportare le funzionalità di ripristino temporizzato (PITR) e [conservazione a lungo termine (LTR)](sql-database-long-term-retention.md) del database SQL. Queste risorse vengono allocate separatamente per ogni database e fatturate come due costi distinti.

- **PITR**: I singoli backup di database vengono copiati automaticamente [nell'archiviazione RIdondante tramite interfaccia geografica (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) di accesso in lettura. Le dimensioni di archiviazione aumentano dinamicamente man mano che vengono creati nuovi backup. L'archiviazione viene utilizzata dai backup completi settimanali, dai backup differenziali giornalieri e dai backup del log delle transazioni, che vengono copiati ogni 5 minuti. Il consumo di spazio di archiviazione dipende dalla frequenza di modifica del database e dal periodo di conservazione per i backup. È possibile configurare un periodo di conservazione separato per ogni database compreso tra 7 e 35 giorni. Una quantità minima di spazio di archiviazione pari al 100% (1x) delle dimensioni del database viene fornita senza costi aggiuntivi. Per la maggior parte dei database, questa quantità è sufficiente per un periodo di archiviazione dei backup di 7 giorni.
- **LTR**: SQL Database offre la possibilità di configurare la conservazione a lungo termine dei backup completi per un massimo di 10 anni. Se si imposta un criterio LTR, questi backup vengono archiviati automaticamente nell'archivio RA-GRS, ma è possibile controllare la frequenza di copia dei backup. Per soddisfare diversi requisiti di conformità, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. La configurazione scelta determina la quantità di spazio di archiviazione che verrà utilizzata per i backup LTR. Per stimare il costo dello stoccaggio LTR, è possibile utilizzare il calcolatore dei prezzi LTR. Per ulteriori informazioni, vedere [Conservazione a lungo termine del database SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle dimensioni di calcolo e le dimensioni di archiviazione specifiche disponibili per un singolo database nei livelli di servizio generici e business critical, vedere Limiti delle [risorse basate su vCore del database SQL per singoli database.](sql-database-vcore-resource-limits-single-databases.md)
- Per informazioni dettagliate sulle dimensioni di calcolo e sulle dimensioni di archiviazione specifiche disponibili per i pool elastici nei livelli di servizio generici e business critical, vedere Limiti delle risorse basate su vCore del database SQL per i [pool elastici.](sql-database-vcore-resource-limits-elastic-pools.md)
