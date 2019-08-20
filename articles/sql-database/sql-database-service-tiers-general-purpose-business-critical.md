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
ms.date: 02/23/2019
ms.openlocfilehash: decb4428321d5083d6ba7af134e223eb2fa5a912
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566697"
---
# <a name="azure-sql-database-service-tiers"></a>Livelli di servizio del database SQL di Azure

Il database SQL di Azure è basato su SQL Server architettura del motore di database adattata per l'ambiente cloud per garantire la disponibilità del 99,99%, anche in caso di errore dell'infrastruttura. Tre livelli di servizio vengono usati nel database SQL di Azure, ognuno con un modello architetturale diverso. Questi livelli di servizio sono:

- [Utilizzo](sql-database-service-tier-general-purpose.md)generico, progettato per la maggior parte dei carichi di lavoro generici.
- [Business critical](sql-database-service-tier-business-critical.md), progettato per i carichi di lavoro a bassa latenza con una replica leggibile.
- [Iperscalabile](sql-database-service-tier-hyperscale.md), progettata per database di dimensioni molto grandi (fino a 100 TB) con più repliche leggibili.

Questo articolo illustra le considerazioni relative all'archiviazione e al backup per i livelli di servizio per utilizzo generico e business critical nel modello di acquisto basato su vCore.

> [!NOTE]
> Per informazioni sul livello di servizio con iperscalabilità nel modello di acquisto basato su vCore, vedere [livello di servizio](sql-database-service-tier-hyperscale.md)con scalabilità automatica. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Archiviazione di dati e log

I seguenti fattori influiscono sulla quantità di spazio di archiviazione utilizzato per i file di dati e di log:

- L'archiviazione allocata viene utilizzata da file di dati (MDF) e file di log (LDF).
- Ogni singola dimensione di calcolo del database supporta una dimensione massima del database, con una dimensione massima predefinita di 32 GB.
- Quando si configurano le dimensioni di database singolo richieste (le dimensioni del file MDF), il 30 percento di spazio di archiviazione aggiuntivo viene aggiunto automaticamente per supportare i file LDF.
- Le dimensioni di archiviazione per un'istanza gestita di database SQL devono essere specificate in multipli di 32 GB.
- È possibile selezionare qualsiasi dimensione di database singolo compresa tra 10 GB e il valore massimo supportato.
  - Per l'archiviazione nei livelli di servizio standard o per utilizzo generico, aumentare o diminuire le dimensioni in incrementi di 10 GB.
  - Per l'archiviazione nei livelli di servizio Premium o business critical, aumentare o diminuire le dimensioni in incrementi di 250 GB.
- Nel livello `tempdb` di servizio per utilizzo generico usa un'unità SSD collegata e il costo di archiviazione è incluso nel prezzo vCore.
- Nel livello `tempdb` di servizio business critical condivide l'unità SSD collegata con i file MDF e ldf e il `tempdb` costo di archiviazione è incluso nel prezzo vCore.

> [!IMPORTANT]
> Viene addebitato lo spazio di archiviazione totale allocato per i file MDF e LDF.

Per monitorare la dimensione totale corrente dei file MDF e LDF, usare [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Per monitorare la dimensione corrente dei singoli file MDF e LDF, usare [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Backup e archiviazione

Lo spazio di archiviazione per i backup di database è allocato per supportare le funzionalità di ripristino temporizzato (ripristino temporizzato) e di [conservazione a lungo termine (LTR)](sql-database-long-term-retention.md) del database SQL. Queste risorse vengono allocate separatamente per ogni database e fatturate come due costi distinti.

- **PITR**: I singoli backup di database vengono copiati automaticamente nell'archiviazione con ridondanza geografica e [accesso in lettura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) . Le dimensioni di archiviazione aumentano in modo dinamico man mano che vengono creati nuovi backup. L'archiviazione viene utilizzata da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni, copiati ogni 5 minuti. Il consumo di spazio di archiviazione dipende dalla frequenza di modifica del database e dal periodo di memorizzazione per i backup. È possibile configurare un periodo di conservazione separato per ogni database compreso tra 7 e 35 giorni. Un importo di archiviazione minimo pari al 100% (1x) delle dimensioni del database viene fornito senza costi aggiuntivi. Per la maggior parte dei database, questa quantità è sufficiente per un periodo di archiviazione dei backup di 7 giorni.
- **LTR**: Il database SQL offre la possibilità di configurare la conservazione a lungo termine dei backup completi per un massimo di 10 anni. Se si configura un criterio LTR, questi backup vengono archiviati automaticamente nell'archiviazione RA-GRS, ma è possibile controllare la frequenza con cui vengono copiati i backup. Per soddisfare i diversi requisiti di conformità, è possibile selezionare diversi periodi di conservazione per i backup settimanali, mensili e/o annuali. La configurazione scelta determina la quantità di spazio di archiviazione che verrà usata per i backup con LTR. Per stimare il costo dell'archiviazione di LTR, è possibile usare il calcolatore dei prezzi di LTR. Per altre informazioni, vedere [conservazione a lungo termine del database SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle dimensioni di calcolo e sulle dimensioni di archiviazione specifiche disponibili per un database singolo nei livelli di servizio utilizzo generico e business critical, vedere [limiti delle risorse basate su vCore per database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md).
- Per informazioni dettagliate sulle dimensioni di calcolo e sulle dimensioni di archiviazione specifiche disponibili per i pool elastici nei livelli di servizio per utilizzo generico e business critical, vedere [limiti delle risorse basate su vCore del database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).
