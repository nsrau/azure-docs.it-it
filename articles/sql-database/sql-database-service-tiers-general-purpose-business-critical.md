---
title: Database SQL di Azure - Utilizzo generico e Business critical | Microsoft Docs
description: Questo articolo illustra i livelli di servizio per utilizzo generico e business critical nel modello di acquisto basato su vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/01/2019
ms.openlocfilehash: a971b428da72028bcabd874e848d53bc2392a0f6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496110"
---
# <a name="azure-sql-database-service-tiers"></a>Livelli di servizio del database SQL di Azure

Il database SQL di Azure è basato su SQL Server architettura del motore di database adattata per l'ambiente cloud per garantire la disponibilità del 99,99%, anche in caso di errore dell'infrastruttura. Tre livelli di servizio vengono usati nel database SQL di Azure, ognuno con un modello architetturale diverso. Questi livelli di servizio sono:

- [Utilizzo generico](sql-database-service-tier-general-purpose.md), progettato per i carichi di lavoro orientati al budget.
- [Iperscalabile](sql-database-service-tier-hyperscale.md), progettata per la maggior parte dei carichi di lavoro aziendali, che offre archiviazione altamente scalabile, scalabilità orizzontale in lettura e funzionalità di ripristino rapido del database.
- [Business critical](sql-database-service-tier-business-critical.md), progettato per carichi di lavoro a bassa latenza con resilienza elevata a errori e failover rapidi.

Questo articolo illustra le differenze: è possibile intervenire sulle considerazioni relative a livelli di servizio, archiviazione e backup per i livelli di servizio per utilizzo generico e business critical nel modello di acquisto basato su vCore.

## <a name="service-tier-comparison"></a>Confronto tra livelli di servizio

Nella tabella seguente vengono descritte le differenze principali tra i livelli di servizio per la generazione più recente (Quinta generazione). Si noti che le caratteristiche del livello di servizio potrebbero essere diverse in Database singolo e Istanza gestita.

| | Tipo di risorsa | Utilizzo generico |  Hyperscale | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **Ideale per** | |  Offre opzioni di calcolo e archiviazione bilanciate a prezzi convenienti. | La maggior parte dei carichi di lavoro aziendali. Ridimensionamento automatico delle dimensioni di archiviazione fino a 100 TB, scalabilità verticale e orizzontale del calcolo, ripristino rapido del database. | Applicazioni OLTP con frequenza di transazione elevata e bassa latenza di i/o. Offre la massima resilienza agli errori e a failover rapidi usando più repliche aggiornate in modo sincrono.|
|  **Disponibile in tipo di risorsa:** ||Database singolo/pool elastico/istanza gestita | Database singolo | Database singolo/pool elastico/istanza gestita |
| **Dimensioni di calcolo**|Database singolo/pool elastico | Da 1 a 80 vCore | da 1 a 80 vcore | Da 1 a 80 vCore |
| | Istanza gestita | 4, 8, 16, 24, 32, 40, 64, 80 vcore | N/D | 4, 8, 16, 24, 32, 40, 64, 80 vcore |
| | Pool di istanze gestite | 2, 4, 8, 16, 24, 32, 40, 64, 80 vcore | N/D | N/D |
| **Tipo di archiviazione** | Tutti | Archiviazione remota Premium (per istanza) | Archiviazione disaccoppiata con cache SSD locale (per istanza) | Archiviazione SSD locale estremamente veloce (per istanza) |
| **Dimensioni database** | Database singolo/pool elastico | 5 GB - 4 TB | Fino a 100 TB | 5 GB - 4 TB |
| | Istanza gestita  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Dimensioni archiviazione** | Database singolo/pool elastico | 5 GB - 4 TB | Fino a 100 TB | 5 GB - 4 TB |
| | Istanza gestita  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Dimensioni di TempDB** | Database singolo/pool elastico | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Istanza gestita  | [24 GB per vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | Fino a 4 TB- [limitato dalle dimensioni di archiviazione](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Velocità effettiva scrittura log** | Database singolo | [1,875 MB/s per vCore (massimo 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s per vCore (Max 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Istanza gestita | [3 MB/s per vCore (massimo 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s per Vcore (max 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Disponibilità**|Tutti| 99,99% |  [99,95% con una replica secondaria, 99,99% con più repliche](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% con database singolo con ridondanza della zona](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Backup**|Tutti|RA-GRS, da 7 a 35 giorni (7 giorni per impostazione predefinita)| RA-GRS, 7 giorni, ripristino temporizzato a tempo costante (ripristino temporizzato) | RA-GRS, da 7 a 35 giorni (7 giorni per impostazione predefinita) |
|**OLTP in memoria** | | N/D | N/D | Disponibile |
|**Repliche di sola lettura**| | 0  | 0 - 4 | 1 (incorporato, incluso nel prezzo) |
|**Prezzi/fatturazione** | Database singolo | vengono addebitati [vCore, archiviazione riservata e archiviazione di backup](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>Per IOPS non viene addebitato alcun costo. | vengono addebitati [vCore per ogni replica e l'archiviazione usata](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS non ancora addebitato. | vengono addebitati [vCore, archiviazione riservata e archiviazione di backup](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>Per IOPS non viene addebitato alcun costo. |
|| Istanza gestita | viene addebitato [vCore e l'archiviazione riservata](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>Per IOPS non viene addebitato alcun costo.<br/>L'archiviazione di backup non è ancora addebitata. | N/D | viene addebitato [vCore e l'archiviazione riservata](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>Per IOPS non viene addebitato alcun costo.<br/>L'archiviazione di backup non è ancora addebitata. | 
|**Modelli di sconto**| | [Istanze riservate](sql-database-reserved-capacity.md)<br/>[Vantaggio Azure Hybrid](sql-database-azure-hybrid-benefit.md) (non disponibile nelle sottoscrizioni di sviluppo/test)<br/>Sottoscrizioni di sviluppo/test [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [con pagamento in base al](https://azure.microsoft.com/offers/ms-azr-0023p/) consumo| [Vantaggio Azure Hybrid](sql-database-azure-hybrid-benefit.md) (non disponibile nelle sottoscrizioni di sviluppo/test)<br/>Sottoscrizioni di sviluppo/test [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [con pagamento in base al](https://azure.microsoft.com/offers/ms-azr-0023p/) consumo| [Istanze riservate](sql-database-reserved-capacity.md)<br/>[Vantaggio Azure Hybrid](sql-database-azure-hybrid-benefit.md) (non disponibile nelle sottoscrizioni di sviluppo/test)<br/>Sottoscrizioni di sviluppo/test [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [con pagamento in base al](https://azure.microsoft.com/offers/ms-azr-0023p/) consumo|

Per ulteriori informazioni, vedere le differenze dettagliate tra i livelli di servizio nel [database singolo (vCore)](sql-database-vcore-resource-limits-single-databases.md), i pool di database singoli ( [vCore)](sql-database-dtu-resource-limits-single-databases.md), i [database singoli (DTU)](sql-database-dtu-resource-limits-single-databases.md), i [pool di database singoli (DTU)](sql-database-dtu-resource-limits-single-databases.md)e [istanza gestita](sql-database-managed-instance-resource-limits.md) pagine.

> [!NOTE]
> Per informazioni sul livello di servizio con iperscalabilità nel modello di acquisto basato su vCore, vedere [livello di servizio con scalabilità](sql-database-service-tier-hyperscale.md)automatica. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Archiviazione di dati e log

I seguenti fattori influiscono sulla quantità di spazio di archiviazione utilizzato per i file di dati e di log e si applicano a per utilizzo generico e business critical. Per informazioni dettagliate sull'archiviazione di dati e log in iperscalabilità, vedere [livello di servizio con scalabilità](sql-database-service-tier-hyperscale.md)automatica.

- L'archiviazione allocata viene utilizzata da file di dati (MDF) e file di log (LDF).
- Ogni singola dimensione di calcolo del database supporta una dimensione massima del database, con una dimensione massima predefinita di 32 GB.
- Quando si configurano le dimensioni di database singolo richieste (le dimensioni del file MDF), il 30 percento di spazio di archiviazione aggiuntivo viene aggiunto automaticamente per supportare i file LDF.
- Le dimensioni di archiviazione per un'istanza gestita di database SQL devono essere specificate in multipli di 32 GB.
- È possibile selezionare qualsiasi dimensione di database singolo compresa tra 10 GB e il valore massimo supportato.
  - Per l'archiviazione nei livelli di servizio standard o per utilizzo generico, aumentare o diminuire le dimensioni in incrementi di 10 GB.
  - Per l'archiviazione nei livelli di servizio Premium o business critical, aumentare o diminuire le dimensioni in incrementi di 250 GB.
- Nel livello di servizio per utilizzo generico, `tempdb` usa un'unità SSD collegata e questo costo di archiviazione è incluso nel prezzo vCore.
- Nel livello di servizio business critical, `tempdb` condivide l'unità SSD collegata con i file MDF e LDF e il costo di archiviazione `tempdb` è incluso nel prezzo vCore.

> [!IMPORTANT]
> Viene addebitato lo spazio di archiviazione totale allocato per i file MDF e LDF.

Per monitorare la dimensione totale corrente dei file MDF e LDF, usare [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Per monitorare la dimensione corrente dei singoli file MDF e LDF, usare [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Backup e archiviazione

Lo spazio di archiviazione per i backup di database è allocato per supportare le funzionalità di ripristino temporizzato (ripristino temporizzato) e di [conservazione a lungo termine (LTR)](sql-database-long-term-retention.md) del database SQL. Queste risorse vengono allocate separatamente per ogni database e fatturate come due costi distinti.

- **Ripristino temporizzato**: i singoli backup di database vengono copiati automaticamente nell' [archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) . Le dimensioni di archiviazione aumentano in modo dinamico man mano che vengono creati nuovi backup. L'archiviazione viene utilizzata da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni, copiati ogni 5 minuti. Il consumo di spazio di archiviazione dipende dalla frequenza di modifica del database e dal periodo di memorizzazione per i backup. È possibile configurare un periodo di conservazione separato per ogni database compreso tra 7 e 35 giorni. Un importo di archiviazione minimo pari al 100% (1x) delle dimensioni del database viene fornito senza costi aggiuntivi. Per la maggior parte dei database, questa quantità è sufficiente per un periodo di archiviazione dei backup di 7 giorni.
- **LTR**: il database SQL offre la possibilità di configurare la conservazione a lungo termine dei backup completi per un massimo di 10 anni. Se si configura un criterio LTR, questi backup vengono archiviati automaticamente nell'archiviazione RA-GRS, ma è possibile controllare la frequenza con cui vengono copiati i backup. Per soddisfare i diversi requisiti di conformità, è possibile selezionare diversi periodi di conservazione per i backup settimanali, mensili e/o annuali. La configurazione scelta determina la quantità di spazio di archiviazione che verrà usata per i backup con LTR. Per stimare il costo dell'archiviazione di LTR, è possibile usare il calcolatore dei prezzi di LTR. Per altre informazioni, vedere [conservazione a lungo termine del database SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle dimensioni di calcolo e sulle dimensioni di archiviazione specifiche disponibili per un database singolo nei livelli di servizio utilizzo generico e business critical, vedere [limiti delle risorse basate su vCore per database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md).
- Per informazioni dettagliate sulle dimensioni di calcolo e sulle dimensioni di archiviazione specifiche disponibili per i pool elastici nei livelli di servizio per utilizzo generico e business critical, vedere [limiti delle risorse basate su vCore del database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).
