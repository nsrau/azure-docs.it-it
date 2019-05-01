---
title: Database SQL di Azure - Utilizzo generico e Business critical | Microsoft Docs
description: Questo articolo illustra i livelli di servizio Utilizzo generico e Business critical nel modello di acquisto basato su vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e2230bc8adf13825692f93b1e2bc33a4b79076e2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574364"
---
# <a name="azure-sql-database-service-tiers"></a>Livelli di servizio del database SQL di Azure

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli architetturali usati nel database SQL di Azure:

- [Utilizzo generico](sql-database-service-tier-general-purpose.md) progettato per la maggior parte dei carichi di lavoro generici.
- [Business Critical](sql-database-service-tier-business-critical.md) progettato per carichi di lavoro a bassa latenza con una replica leggibile.
- [Hyperscale](sql-database-service-tier-hyperscale.md) progettato per database di dimensioni molto grandi (fino a 100 TB) con più repliche leggibili.

Questo articolo contiene alcune considerazioni sull'archiviazione e il backup per i livelli di servizio Utilizzo generico e Business critical nel modello di acquisto basato su vCore.

> [!NOTE]
> Per informazioni dettagliate sul livello di servizio Hyperscale nel modello di acquisto basato su vCore, vedere [Livello di servizio con iperscalabilità (anteprima) per database fino a 100 TB](sql-database-service-tier-hyperscale.md). Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Archiviazione di dati e log

Valutare gli aspetti seguenti:

- Le risorse di archiviazione allocate vengono usate dai file di dati (MDF) e dai file di log (LDF).
- Le dimensioni di calcolo di un database singolo supportano una dimensione massima del database. La dimensione massima predefinita è di 32 GB.
- Quando si configura la dimensione del database singolo desiderata (dimensione dei file MDF), viene aggiunto automaticamente il 30% delle risorse di archiviazione per il supporto dei file LDF
- Le dimensioni di archiviazione in istanza gestita devono essere specificate in multipli di 32 GB.
- È possibile selezionare qualsiasi dimensione di database singolo compresa tra 10 GB e il valore massimo supportato
  - Per l'archiviazione nei livelli di servizio per utilizzo generico o standard, aumentare o ridurre le dimensioni in incrementi di 10 GB
  - Per l'archiviazione nei livelli di servizio premium o business critical, aumentare o ridurre le dimensioni in incrementi di 250 GB
- Nel livello di servizio Utilizzo generico `tempdb` usa un'unità SSD collegata e questo costo di archiviazione è incluso nel prezzo del vCore.
- Nel livello di servizio Business Critical `tempdb` condivide l'unità SSD collegata con i file MDF e LDF e il costo di archiviazione di tempDB è incluso nel prezzo del vCore.

> [!IMPORTANT]
> Il costo addebitato dipende dalla quantità totale di risorse di archiviazione allocate per i file MDF e LDF.

Per monitorare la dimensione totale corrente dei file MDF e LDF, usare [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Per monitorare la dimensione corrente dei singoli file MDF e LDF, usare [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Backup e archiviazione

Le risorse di archiviazione per i backup di database vengono allocate per supportare le funzionalità di ripristino temporizzato e di [conservazione a lungo termine](sql-database-long-term-retention.md) del database SQL. Queste risorse vengono allocate separatamente per ogni database e fatturate come due costi distinti.

- **PITR**: i singoli backup di database vengono copiati automaticamente in [risorse di archiviazione con ridondanza geografica e accesso in lettura](../storage/common/storage-designing-ha-apps-with-ragrs.md). Le dimensioni di archiviazione aumentano dinamicamente con la creazione di nuovi backup.  Le risorse di archiviazione vengono usate da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni copiati ogni 5 minuti. L'utilizzo delle risorse di archiviazione dipende dalla frequenza con cui vengono apportate modifiche al database e dal periodo di conservazione. È possibile configurare un periodo di conservazione separato per ogni database compreso tra 7 e 35 giorni. Uno spazio di archiviazione minimo, equivalente al 100% della dimensione dei dati, viene fornito senza costi aggiuntivi. Per la maggior parte dei database, questa quantità è sufficiente per un periodo di archiviazione dei backup di 7 giorni.
- **LTR**: il database SQL offre la possibilità di configurare la conservazione a lungo termine dei backup completi fino a 10 anni. Se i criteri di conservazione a lungo termine sono abilitati, questi backup vengono archiviati automaticamente in risorse di archiviazione RA-GRS, ma è possibile controllare la frequenza con cui vengono copiati. A secondo dei vari requisiti di conformità, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. Questa configurazione definirà la quantità di risorse di archiviazione usate per i backup con conservazione a lungo termine. È possibile usare lo strumento di calcolo dei prezzi per la conservazione a lungo termine per stimare il costo di questo tipo di archiviazione. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per database singoli nei livelli di servizio Utilizzo generico e Business critical, vedere [Limiti delle risorse basate su vCore del database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md).
- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per i pool elastici nei livelli di servizio Utilizzo generico e Business critical, vedere [Limiti delle risorse basate su vCore del database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).
