---
title: Servizio del database SQL di Azure - DTU | Microsoft Docs
description: Informazioni sui livelli di servizio per database singoli e di pool per offrire livelli di prestazioni e dimensioni di archiviazione.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 075e04db27006719ec7d6e08eb2696436d1010f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648650"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Modello di acquisto basato su DTU per il database SQL di Azure 


Il [database SQL di Azure](sql-database-technical-overview.md) offre due modelli di acquisto per le risorse di calcolo, archiviazione e I/O basati rispettivamente su DTU e su vCore (anteprima). La tabella e il grafico seguenti mettono a confronto questi due modelli.

> [!IMPORTANT]
> Per informazioni sul modello di acquisto basato su vCore (anteprima), vedere [Modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).


|**Modello di acquisto**|**Descrizione**|**Ideale per**|
|---|---|---|
|Modello basato su DTU|Questo modello è basato su una misura combinata di risorse di calcolo, archiviazione e I/O. I livelli di prestazioni per i database singoli sono espressi in unità di transazione di database (DTU), quelli per i pool elastici sono espressi in unità di transazione di database elastico (eDTU). Per altre informazioni su DTU ed eDTU, vedere [Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)](sql-database-what-is-a-dtu.md).|Ideale per i clienti che desiderano opzioni di risorse semplici e preconfigurate.| 
|Modello basato su vCore|Questo modello consente di ridimensionare in modo indipendente le risorse di calcolo e archiviazione. Offre inoltre la possibilità di usare il Vantaggio Azure Hybrid per SQL Server per ottenere un risparmio sui costi.|Ideale per i clienti che danno valore alla trasparenza, al controllo e alla flessibilità.|
||||  

![modello di prezzi](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

La DTU (Database Throughput Unit) rappresenta una misura combinata di CPU, memoria e operazioni di lettura e scrittura. Il modello di acquisto basato su DTU offre un set di bundle preconfigurati di risorse di calcolo e archiviazione per diversi livelli di prestazioni dell'applicazione. I clienti che preferiscono la semplicità di un bundle preconfigurato, pagando un importo fisso mensile, possono considerare il modello basato su DTU come più adatto alle proprie esigenze. In questo modello i clienti possono scegliere tra livelli di servizio **Basic**, **Standard** e **Premium** sia per [database singoli](sql-database-single-database-resources.md) sia per [pool elastici](sql-database-elastic-pool.md). I livelli di servizio si differenziano in base a diversi livelli di prestazioni con una quantità fissa di risorse di archiviazione, un periodo di conservazione fisso per i backup e un prezzo fisso. Tutti i livelli di servizio assicurano la flessibilità necessaria per modificare i livelli di prestazioni senza tempi di inattività. La fatturazione per i database singoli e i pool elastici viene effettuata con frequenza oraria in base al livello di servizio e al livello di prestazioni.

> [!IMPORTANT]
> Istanza gestita di database SQL, attualmente in anteprima pubblica, non supporta un modello di acquisto basato su DTU. Per altre informazioni, vedere l'articolo relativo a [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md). 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>Scelta di un livello di servizio nel modello di acquisto basato su DTU

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

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Limiti dei livelli di prestazioni e delle dimensioni di archiviazione nel modello di acquisto basato su DTU

I livelli di prestazioni per i database singoli sono espressi in unità di transazione di database (DTU), quelli per i pool elastici sono espressi in unità di transazione di database elastico (eDTU). Per altre informazioni su DTU ed eDTU, vedere [Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)](sql-database-what-is-a-dtu.md).

### <a name="single-databases"></a>Database singoli

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Dimensioni massime di archiviazione* | 2 GB | 1 TB | 4 TB  | 
| DTU massime | 5 | 3000 | 4000 | |
||||||

Per informazioni dettagliate su livelli di prestazioni specifici e sulle opzioni relative alle dimensioni di archiviazione disponibili per database singoli, vedere [Limiti delle risorse basate su DTU del database SQL per database singoli](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels).

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
> - Le dimensioni di archiviazione superiori alla quantità di risorse di archiviazione incluse sono disponibili in anteprima e vengono applicati costi aggiuntivi. Per ulteriori informazioni, vedere [Database SQL Prezzi](https://azure.microsoft.com/pricing/details/sql-database/). 
> - Più di 1 TB di spazio di archiviazione nel livello Premium è disponibile in tutte le aree tranne le seguenti: Regno Unito settentrionale, Stati Uniti centro-occidentali, Regno Unito meridionale 2, Cina orientale, USDoDCentral, Germania centrale, USDoDEast, US Gov Southwest, US Gov South Central, Germania nord-orientale, Cina settentrionale, US Gov East. È pianificata una disponibilità più ampia per il futuro. Nelle altre aree la quantità massima di risorse di archiviazione nel livello Premium è limitata a 1 TB. Vedere [Limitazioni correnti per P11-P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
Per informazioni dettagliate su livelli di prestazioni specifici e sulle opzioni relative alle dimensioni di archiviazione disponibili per i pool elastici, vedere [Limiti delle risorse basate su DTU del database SQL](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sugli specifici livelli di prestazioni e dimensioni di archiviazione disponibili, vedere [Limiti delle risorse basate su DTU del database SQL](sql-database-dtu-resource-limits.md) e [Limiti delle risorse basate su vCore del database SQL](sql-database-vcore-resource-limits.md).
- Per le risposte alle domande più frequenti, vedere [Domande frequenti sul database SQL](sql-database-faq.md).
- Per informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
