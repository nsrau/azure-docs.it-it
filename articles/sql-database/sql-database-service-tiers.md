---
title: Servizio del database SQL di Azure | Microsoft Docs
description: Informazioni sui livelli di servizio per database singoli e di pool per offrire livelli di prestazioni e dimensioni di archiviazione.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: 6153616de763eee1b20fff40d38816eca8b455de
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Quali sono i livelli di servizio del database SQL di Azure?

Il [database SQL di Azure](sql-database-technical-overview.md) offre i livelli di servizio **Basic**, **Standard** e **Premium** per i [database singoli](sql-database-single-database-resources.md) e i [pool elastici](sql-database-elastic-pool.md). Il database SQL offre un livello di servizio di utilizzo generico per [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md#managed-instance-service-tier). Le principali differenze tra i livelli di servizio sono dovute alla gamma di opzioni relative a livello di prestazioni, dimensioni di archiviazione e prezzo.  Tutti i livelli di servizio assicurano la flessibilità necessaria per modificare le dimensioni di archiviazione e il livello di prestazioni.  I database singoli e i pool elastici vengono fatturati su base oraria in base al livello di servizio, al livello di prestazioni e alle dimensioni di archiviazione.   

> [!IMPORTANT]
> Istanza gestita di database SQL, attualmente in anteprima pubblica, offre un singolo livello di servizio di utilizzo generico. Per altre informazioni, vedere l'articolo relativo a [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md). La parte restante di questo articolo non si applica a Istanza gestita.

## <a name="choosing-a-service-tier"></a>Scelta di un piano di servizio

La scelta di un livello di servizio dipende soprattutto dai requisiti in termini di continuità aziendale, archiviazione e prestazioni.
| | **Basic** | **Standard** |**Premium**  |
| :-- | --: |--:| --:| --:| 
|Carico di lavoro di destinazione|Sviluppo e produzione|Sviluppo e produzione|Sviluppo e produzione||
|Contratto di servizio relativo al tempo di attività|99,99%|99,99%|99,99%|N/d in anteprima|
|Conservazione backup|7 giorni|35 giorni|35 giorni|
|CPU|Basso|Basso, medio, elevato|Medio, elevato|
|Velocità effettiva di I/O (approssimativa) |2,5 IOPS per DTU  | 2,5 IOPS per DTU | 48 IOPS per DTU|
|Latenza di I/O (approssimativa)|5 ms (lettura), 10 ms (scrittura)|5 ms (lettura), 10 ms (scrittura)|2 ms (lettura/scrittura)|
|Indici ColumnStore e OLTP in memoria|N/D|N/D|Supportato|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Livello di prestazioni e limiti delle dimensioni di archiviazione

I livelli di prestazioni per i database singoli sono espressi in unità di transazione di database (DTU), quelli per i pool elastici sono espressi in unità di transazione di database elastico (eDTU). Per altre informazioni su DTU ed eDTU, vedere [Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)](sql-database-what-is-a-dtu.md).

### <a name="single-databases"></a>Database singoli

|  | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Dimensioni massime di archiviazione* | 2 GB | 1 TB | 4 TB  | 
| DTU massime | 5 | 3000 | 4000 | |
||||||

### <a name="elastic-pools"></a>Pool elastici

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Dimensioni massime di archiviazione per ogni database*  | 2 GB | 1 TB | 1 TB | 
| Dimensioni massime di archiviazione per ogni pool* | 156 GB | 4 TB | 4 TB | 
| Numero massimo di eDTU per ogni database | 5 | 3000 | 4000 | 
| Numero massimo di eDTU per ogni pool | 1600 | 3000 | 4000 | 
| Numero massimo di database per pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* Le dimensioni di archiviazione superiori alla quantità di risorse di archiviazione incluse sono disponibili in anteprima e vengono applicati costi aggiuntivi. Per ulteriori informazioni, vedere [Database SQL Prezzi](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* Nel livello Premium sono attualmente disponibili più di 1 TB di risorse di archiviazione nelle aree seguenti: Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Stati Uniti centrali, Francia centrale, Germania centrale, Giappone orientale, Giappone occidentale, Corea centrale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Regno Unito meridionale, Regno Unito orientale, Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia ed Europa occidentale. Vedere [Limitazioni correnti per P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Per i dettagli su livelli di prestazioni specifici e sulle opzioni relative alle dimensioni di archiviazione disponibili, vedere [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [risorse per database singoli](sql-database-single-database-resources.md).
- Altre informazioni sui pool elastici, vedere [Pool elastici](sql-database-elastic-pool.md).
- Per informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
* Altre informazioni sulle [DTU ed eDTU](sql-database-what-is-a-dtu.md).
* Per informazioni sul monitoraggio dell'utilizzo di DTU, vedere [Monitoraggio e ottimizzazione delle prestazioni](sql-database-troubleshoot-performance.md).

