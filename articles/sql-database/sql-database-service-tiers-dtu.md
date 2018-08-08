---
title: Livelli di servizio del database SQL di Azure - DTU | Microsoft Docs
description: Informazioni sui livelli di servizio per database singoli e di pool per offrire livelli di prestazioni e dimensioni di archiviazione.
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5d16763fc8f3331082b98216d25190b945d95b60
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411821"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Scelta di un livello di servizio basato su DTU, del livello di prestazioni e delle risorse di archiviazione 

I livelli di servizio si differenziano in base a diversi livelli di prestazioni con una quantità fissa di risorse di archiviazione, un periodo di conservazione fisso per i backup e un prezzo fisso. Tutti i livelli di servizio assicurano la flessibilità necessaria per modificare i livelli di prestazioni senza tempi di inattività. La fatturazione per i database singoli e i pool elastici viene effettuata con frequenza oraria in base al livello di servizio e al livello di prestazioni.

> [!IMPORTANT]
> Istanza gestita di database SQL, attualmente in anteprima pubblica, non supporta un modello di acquisto basato su DTU. Per altre informazioni, vedere l'articolo relativo a [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Scelta di un livello di servizio basato su DTU

La scelta di un livello di servizio dipende soprattutto dai requisiti in termini di continuità aziendale, archiviazione e prestazioni.
||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:| 
|Carico di lavoro di destinazione|Sviluppo e produzione|Sviluppo e produzione|Sviluppo e produzione||
|Contratto di servizio relativo al tempo di attività|99,99%|99,99%|99,99%|N/d in anteprima|
|Conservazione backup|7 giorni|35 giorni|35 giorni|
|CPU|Basso|Basso, medio, elevato|Medio, elevato|
|Velocità effettiva di I/O (approssimativa) |2,5 IOPS per DTU| 2,5 IOPS per DTU | 48 IOPS per DTU|
|Latenza di I/O (approssimativa)|5 ms (lettura), 10 ms (scrittura)|5 ms (lettura), 10 ms (scrittura)|2 ms (lettura/scrittura)|
|Indicizzazione ColumnStore |N/D|S3 e superiore|Supportato|
|OLTP in memoria|N/D|N/D|Supportato|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>DTU database singolo e limiti di archiviazione

I livelli di prestazioni per i database singoli sono espressi in unità di transazione di database (DTU), quelli per i pool elastici sono espressi in unità di transazione di database elastico (eDTU). Per altre informazioni su DTU ed eDTU, vedere [Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Dimensioni massime di archiviazione | 2 GB | 1 TB | 4 TB  | 
| DTU massime | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>eDTU pool elastico, archiviazione e limiti del database in pool

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Dimensioni massime di archiviazione per ogni database  | 2 GB | 1 TB | 1 TB | 
| Dimensioni massime di archiviazione per ogni pool | 156 GB | 4 TB | 4 TB | 
| Numero massimo di eDTU per ogni database | 5 | 3000 | 4000 | 
| Numero massimo di eDTU per ogni pool | 1600 | 3000 | 4000 | 
| Numero massimo di database per pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Nel livello Premium è attualmente disponibile più di 1 TB di archiviazione in tutte le aree tranne le seguenti: Stati Uniti centro-occidentali, Cina orientale, USDoDCentral, Germania centrale, USDoDEast, US Gov Southwest, USGov Iowa, Germania nord-orientale, Cina settentrionale. Nelle altre aree la quantità massima di risorse di archiviazione nel livello Premium è limitata a 1 TB. Vedere [Limitazioni correnti per P11-P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate su livelli di prestazioni specifici e sulle opzioni relative alle dimensioni di archiviazione disponibili per database singoli, vedere [Limiti delle risorse basate su DTU del database SQL per database singoli](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Per informazioni dettagliate su livelli di prestazioni specifici e sulle opzioni relative alle dimensioni di archiviazione disponibili per i pool elastici, vedere [Limiti delle risorse basate su DTU del database SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
