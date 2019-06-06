---
title: Database SQL di Azure - Utilizzo generico e Business critical | Microsoft Docs
description: L'articolo descrive l'utilizzo generico e livelli di servizio critici di business nel modello di acquisto basato su vCore.
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
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431337"
---
# <a name="azure-sql-database-service-tiers"></a>Livelli di servizio del database SQL di Azure

Database SQL di Azure si basa sull'architettura di motore di database SQL Server che si adatta per l'ambiente cloud assicurare la disponibilità al 99,99%, anche se si verifica un errore di infrastruttura. Tre livelli di servizio vengono utilizzati nel Database SQL di Azure, ognuno con un modello architettura differente. Questi livelli di servizio sono:

- [Utilizzo generico](sql-database-service-tier-general-purpose.md), che è progettato per carichi di lavoro più generici.
- [Aziendale critica](sql-database-service-tier-business-critical.md), che è progettato per carichi di lavoro a bassa latenza con una replica leggibile.
- [Su scala molto vasta](sql-database-service-tier-hyperscale.md), che è progettato per database molto grandi (fino a 100 TB) con più repliche leggibili.

Questo articolo illustra considerazioni sul backup e archiviazione per utilizzo generico e livelli di servizio critici di business nel modello di acquisto basato su vCore.

> [!NOTE]
> Per informazioni sul livello di servizio su scala molto vasta nel modello di acquisto basato su vCore, vedere [livello di servizio su scala molto vasta](sql-database-service-tier-hyperscale.md). Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Archiviazione di dati e log

I seguenti fattori influiscono sulla quantità di spazio di archiviazione usato per i file di dati e di log:

- Memoria allocata è usata dal file di dati (MDF) e i file di log (LDF).
- Ogni singolo database di calcolo delle dimensioni supporta una dimensione massima del database con una dimensione massima di 32 GB.
- Quando si configura la dimensione del database singolo desiderata (le dimensioni del file MDF), più il 30% di spazio di archiviazione aggiuntivo viene automaticamente aggiunto per supportare i file LDF.
- Le dimensioni di archiviazione per un'istanza gestita di Database SQL devono essere specificata in multipli di 32 GB.
- È possibile selezionare qualsiasi dimensione di database singolo tra 10 GB e il valore massimo supportato.
  - Per l'archiviazione nei livelli di servizio generico o standard, aumentare o diminuire le dimensioni in incrementi di 10 GB.
  - Per l'archiviazione in premium o business critici livelli di servizio, aumentare o diminuire le dimensioni in incrementi di 250 GB.
- Nel livello di servizio utilizzo generico, `tempdb` Usa un'unità SSD collegata e questo costo di archiviazione è incluso nel prezzo del vCore.
- Nel livello business critiche dei servizi `tempdb` condivide l'unità SSD collegata con i file MDF e LDF e il `tempdb` costo di archiviazione è incluso nel prezzo del vCore.

> [!IMPORTANT]
> Viene addebitato per l'archiviazione totale allocato per i file MDF e LDF.

Per monitorare la dimensione totale corrente dei file MDF e LDF, usare [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Per monitorare la dimensione corrente dei singoli file MDF e LDF, usare [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Backup e archiviazione

Spazio di archiviazione per i backup dei database verrà allocata per supportare il ripristino temporizzato in (PITR) e [conservazione a lungo termine (conservazione a lungo termine)](sql-database-long-term-retention.md) funzionalità del Database SQL. Queste risorse vengono allocate separatamente per ogni database e fatturate come due costi distinti.

- **PITR**: Vengono copiati i backup dei database singoli [archiviazione con ridondanza geografica (RA-GRS) e accesso in lettura](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticamente. Le dimensioni di archiviazione aumentano dinamicamente con i nuovi backup vengono creati. L'archiviazione viene usata da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni, che vengono copiati ogni 5 minuti. Lo spazio di archiviazione dipende dalla frequenza di modifica del database e il periodo di memorizzazione per i backup. È possibile configurare un periodo di conservazione separato per ogni database compreso tra 7 e 35 giorni. Una quantità di spazio di archiviazione minimo pari al 100% (1 x) delle dimensioni del database viene fornita senza costi aggiuntivi. Per la maggior parte dei database, questa quantità è sufficiente per un periodo di archiviazione dei backup di 7 giorni.
- **LTR**: Database SQL offre la possibilità di configurare la conservazione a lungo termine dei backup completi fino a 10 anni. Se si configura un criterio di conservazione a lungo termine, questi backup vengono archiviati in archiviazione RA-GRS automaticamente, ma è possibile controllare con quale frequenza vengono copiati i backup. Per soddisfare i requisiti di conformità diversi, è possibile selezionare i periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. La configurazione che scelta determina quanto spazio di archiviazione verrà usato per i backup di conservazione a lungo termine. Per stimare i costi di archiviazione di conservazione a lungo termine, è possibile usare il calcolatore dei prezzi di conservazione a lungo termine. Per altre informazioni, vedere [conservazione a lungo termine del Database SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle specifiche di calcolo le dimensioni e dimensioni di archiviazione disponibili per un database singolo in utilizzo generico e livelli di servizio critici di business, vedere [limiti delle risorse basate su Vcore per Database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md).
- Per informazioni dettagliate sulle specifiche di calcolo le dimensioni e dimensioni di archiviazione disponibili per i pool elastici in utilizzo generico e livelli di servizio critici di business, vedere [limiti delle risorse basate su Vcore per Database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).
