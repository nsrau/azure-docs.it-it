---
title: Livelli di servizio per utilizzo generico e business critical
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Questo articolo illustra i livelli di servizio per utilizzo generico e business critical nel modello di acquisto basato su vCore usato dal database SQL di Azure e da Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/30/2020
ms.openlocfilehash: c1f60888a3d1fda50e63c7d11ea5d871f7c1e9fc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321340"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Livelli di servizio del database SQL di Azure e di Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Il database SQL di Azure e Istanza gestita SQL di Azure si basano sull'architettura SQL Server motore di database adattata per l'ambiente cloud per garantire la disponibilità del 99,99%, anche in caso di errore dell'infrastruttura. Due livelli di servizio vengono usati dal database SQL di Azure e da Istanza gestita SQL di Azure, ognuno con un modello architetturale diverso. Questi livelli di servizio sono:

- [Utilizzo generico](service-tier-general-purpose.md), progettato per i carichi di lavoro orientati al budget.
- [Business critical](service-tier-business-critical.md), progettato per carichi di lavoro a bassa latenza con resilienza elevata a errori e failover rapidi.

Il database SQL di Azure dispone di un livello di servizio aggiuntivo: 

- [Iperscalabile](service-tier-hyperscale.md), progettata per la maggior parte dei carichi di lavoro aziendali, che offre archiviazione altamente scalabile, scalabilità orizzontale in lettura e funzionalità di ripristino rapido del database.

Questo articolo illustra le differenze tra i livelli di servizio, le considerazioni relative all'archiviazione e al backup per i livelli di servizio per utilizzo generico e business critical nel modello di acquisto basato su vCore.

## <a name="service-tier-comparison"></a>Confronto tra livelli di servizio

Nella tabella seguente vengono descritte le differenze principali tra i livelli di servizio per la generazione più recente (Quinta generazione). Si noti che le caratteristiche del livello di servizio possono essere diverse nel database SQL e in SQL Istanza gestita.

|-| Tipo di risorsa | Utilizzo generico |  Hyperscale | Business critical |
|:---:|:---:|:---:|:---:|:---:|
| **Ideale per** | |  Offre opzioni di calcolo e archiviazione bilanciate a prezzi convenienti. | La maggior parte dei carichi di lavoro aziendali. Ridimensionamento automatico delle dimensioni di archiviazione fino a 100 TB, scalabilità verticale e orizzontale del calcolo, ripristino rapido del database. | Applicazioni OLTP con frequenza di transazione elevata e bassa latenza di i/o. Offre la massima resilienza agli errori e a failover rapidi usando più repliche aggiornate in modo sincrono.|
|  **Disponibile in tipo di risorsa:** ||Database SQL/SQL Istanza gestita | Singolo database SQL di Azure | Database SQL/SQL Istanza gestita |
| **Dimensioni di calcolo**| Database SQL | Da 1 a 80 vCore | da 1 a 80 vcore | Da 1 a 80 vCore |
| | Istanza gestita di SQL | 4, 8, 16, 24, 32, 40, 64, 80 vcore | N/D | 4, 8, 16, 24, 32, 40, 64, 80 vcore |
| | Pool di Istanza gestita SQL | 2, 4, 8, 16, 24, 32, 40, 64, 80 vcore | N/D | N/D |
| **Tipo di archiviazione** | Tutti | Archiviazione remota Premium (per istanza) | Archiviazione disaccoppiata con cache SSD locale (per istanza) | Archiviazione SSD locale estremamente veloce (per istanza) |
| **Dimensioni del database** | Database SQL | 5 GB - 4 TB | Fino a 100 TB | 5 GB - 4 TB |
| | Istanza gestita di SQL  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Dimensioni dello spazio di archiviazione** | Database SQL | 5 GB - 4 TB | Fino a 100 TB | 5 GB - 4 TB |
| | Istanza gestita di SQL  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Dimensioni di TempDB** | Database SQL | [32 GB per vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB per vCore](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB per vCore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Istanza gestita di SQL  | [24 GB per vCore](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | Fino a 4 TB- [limitato dalle dimensioni di archiviazione](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Velocità effettiva scrittura log** | Database SQL | [1,875 MB/s per vCore (massimo 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s per vCore (Max 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Istanza gestita di SQL | [3 MB/s per vCore (massimo 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s per Vcore (max 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Disponibilità**|Tutti| 99,99% |  [99,95% con una replica secondaria, 99,99% con più repliche](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% con database singolo con ridondanza della zona](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Backup**|Tutti|RA-GRS, da 7 a 35 giorni (7 giorni per impostazione predefinita)| RA-GRS, 7 giorni, ripristino temporizzato a tempo costante (ripristino temporizzato) | RA-GRS, da 7 a 35 giorni (7 giorni per impostazione predefinita) |
|**OLTP in memoria** | | N/D | N/D | Disponibile |
|**Repliche di sola lettura**| | 0 incorporato <br> 0-4 uso della [replica geografica](active-geo-replication-overview.md) | 0-4 predefinito | 1 incorporato, incluso nel prezzo <br> 0-4 uso della [replica geografica](active-geo-replication-overview.md) |
|**Prezzi/fatturazione** | Database SQL | vengono addebitati [vCore, archiviazione riservata e archiviazione di backup](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>Per IOPS non viene addebitato alcun costo. | vengono addebitati [vCore per ogni replica e l'archiviazione usata](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS non ancora addebitato. | vengono addebitati [vCore, archiviazione riservata e archiviazione di backup](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>Per IOPS non viene addebitato alcun costo. |
|| Istanza gestita di SQL | vengono addebitati [vCore, archiviazione riservata e archiviazione di backup](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>IOPS non addebitato| N/D | vengono addebitati [vCore, archiviazione riservata e archiviazione di backup](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>Per IOPS non viene addebitato alcun costo.| 
|**Modelli di sconto**| | [Istanze riservate](reserved-capacity-overview.md)<br/>[Vantaggio Azure Hybrid](../azure-hybrid-benefit.md) (non disponibile nelle sottoscrizioni di sviluppo/test)<br/>Sottoscrizioni di sviluppo/test [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [con pagamento in base al](https://azure.microsoft.com/offers/ms-azr-0023p/) consumo| [Vantaggio Azure Hybrid](../azure-hybrid-benefit.md) (non disponibile nelle sottoscrizioni di sviluppo/test)<br/>Sottoscrizioni di sviluppo/test [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [con pagamento in base al](https://azure.microsoft.com/offers/ms-azr-0023p/) consumo| [Istanze riservate](reserved-capacity-overview.md)<br/>[Vantaggio Azure Hybrid](../azure-hybrid-benefit.md) (non disponibile nelle sottoscrizioni di sviluppo/test)<br/>Sottoscrizioni di sviluppo/test [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [con pagamento in base al](https://azure.microsoft.com/offers/ms-azr-0023p/) consumo|

Per altre informazioni, vedere le differenze dettagliate tra i livelli di servizio nel [database SQL di Azure (vCore)](resource-limits-vcore-single-databases.md), il [singolo database SQL di Azure (DTU)](resource-limits-dtu-single-databases.md), il [database SQL di Azure in pool (DTU)](resource-limits-dtu-single-databases.md)e le pagine di [istanza gestita di Azure SQL](../managed-instance/resource-limits.md) .

> [!NOTE]
> Per informazioni sul livello di servizio con iperscalabilità nel modello di acquisto basato su vCore, vedere [livello di servizio con scalabilità](service-tier-hyperscale.md)automatica. Per un confronto tra il modello di acquisto basato su vCore e il modello di acquisto basato su DTU, vedere [acquisto di modelli e risorse](purchasing-models.md).

## <a name="data-and-log-storage"></a>Archiviazione di dati e log

I seguenti fattori influiscono sulla quantità di spazio di archiviazione utilizzato per i file di dati e di log e si applicano a per utilizzo generico e business critical. Per informazioni dettagliate sull'archiviazione di dati e log in iperscalabilità, vedere [livello di servizio con scalabilità](service-tier-hyperscale.md)automatica.

- L'archiviazione allocata viene utilizzata da file di dati (MDF) e file di log (LDF).
- Ogni singola dimensione di calcolo del database supporta una dimensione massima del database, con una dimensione massima predefinita di 32 GB.
- Quando si configurano le dimensioni di database singolo richieste (le dimensioni del file MDF), il 30 percento di spazio di archiviazione aggiuntivo viene aggiunto automaticamente per supportare i file LDF.
- È possibile selezionare qualsiasi dimensione di database singolo compresa tra 10 GB e il valore massimo supportato.
  - Per l'archiviazione nei livelli di servizio standard o per utilizzo generico, aumentare o diminuire le dimensioni in incrementi di 10 GB.
  - Per l'archiviazione nei livelli di servizio Premium o business critical, aumentare o diminuire le dimensioni in incrementi di 250 GB.
- Nel livello di servizio per utilizzo generico `tempdb` Usa un'unità SSD collegata e il costo di archiviazione è incluso nel prezzo vCore.
- Nel livello di servizio business critical `tempdb` condivide l'unità SSD collegata con i file MDF e ldf e il `tempdb` costo di archiviazione è incluso nel prezzo vCore.
- Le dimensioni di archiviazione per un Istanza gestita SQL devono essere specificate in multipli di 32 GB.


> [!IMPORTANT]
> Viene addebitato lo spazio di archiviazione totale allocato per i file MDF e LDF.

Per monitorare la dimensione totale corrente dei file MDF e LDF, usare [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Per monitorare la dimensione corrente dei singoli file MDF e LDF, usare [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [gestire lo spazio di file nel database SQL di Azure](file-space-manage.md).

## <a name="backups-and-storage"></a>Backup e archiviazione

Lo spazio di archiviazione per i backup di database è allocato per supportare le funzionalità di ripristino temporizzato (ripristino temporizzato) e [conservazione a lungo termine (LTR)](long-term-retention-overview.md) del database SQL e di SQL istanza gestita. Queste risorse vengono allocate separatamente per ogni database e fatturate come due costi distinti.

- **Ripristino temporizzato**: i singoli backup di database vengono copiati automaticamente nell' [archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)](../../storage/common/geo-redundant-design.md) . Le dimensioni di archiviazione aumentano in modo dinamico man mano che vengono creati nuovi backup. L'archiviazione viene utilizzata da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni, copiati ogni 5 minuti. Il consumo di spazio di archiviazione dipende dalla frequenza di modifica del database e dal periodo di memorizzazione per i backup. È possibile configurare un periodo di conservazione separato per ogni database compreso tra 7 e 35 giorni. Un importo di archiviazione minimo pari al 100% (1x) delle dimensioni del database viene fornito senza costi aggiuntivi. Per la maggior parte dei database, questa quantità è sufficiente per un periodo di archiviazione dei backup di 7 giorni.
- **LTR**: è anche possibile configurare la conservazione a lungo termine dei backup completi per un massimo di 10 anni. questa funzionalità è in [anteprima pubblica limitata per SQL istanza gestita](long-term-retention-overview.md#sql-managed-instance-support). Se si configura un criterio LTR, questi backup vengono archiviati automaticamente nell'archiviazione RA-GRS, ma è possibile controllare la frequenza con cui vengono copiati i backup. Per soddisfare i diversi requisiti di conformità, è possibile selezionare diversi periodi di conservazione per i backup settimanali, mensili e/o annuali. La configurazione scelta determina la quantità di spazio di archiviazione che verrà usata per i backup con LTR. Per stimare il costo dell'archiviazione di LTR, è possibile usare il calcolatore dei prezzi di LTR. Per altre informazioni, vedere [conservazione a lungo termine del database SQL](long-term-retention-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sulle dimensioni di calcolo e archiviazione specifiche disponibili nei livelli di servizio utilizzo generico e business critical, vedere: 

- [limiti delle risorse basate su vCore per il database SQL di Azure](resource-limits-vcore-single-databases.md).
- [limiti delle risorse basate su vCore per i database in pool nel database SQL di Azure](resource-limits-vcore-elastic-pools.md).
- [limiti delle risorse basate su vCore per istanza gestita SQL di Azure](../managed-instance/resource-limits.md). 

