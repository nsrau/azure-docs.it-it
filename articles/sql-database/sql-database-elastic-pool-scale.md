---
title: Ridimensionare le risorse dei pool elastici - Database SQL di Azure | Microsoft Docs
description: Questa pagina descrive come ridimensionare le risorse per i pool elastici nel database SQL di Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 0d15382f413485ccc287bac945b3c88eb748a9f6
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311335"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Ridimensionare le risorse dei pool elastici nel database SQL di Azure

Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per i pool elastici e i database in pool nel database SQL di Azure. 

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>Modello di acquisto basato su vCore: modifica delle dimensioni di archiviazione del pool elastico

- Il provisioning dell'archiviazione può essere effettuato fino al limite massimo di dimensioni: 
 - Per l'archiviazione Standard, aumentare o diminuire le dimensioni in incrementi di 10 GB
 - Per l'archiviazione Premium, aumentare o diminuire la dimensione in base a incrementi di 250 GB.
- Il provisioning dell'archiviazione per un pool elastico può essere effettuato aumentandone o diminuendone le dimensioni massime.
- Il prezzo dell'archiviazione per un pool elastico corrisponde alle dimensioni di archiviazione moltiplicate per il prezzo unitario dell'archiviazione del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>Modello di acquisto basato su vCore: modifica delle risorse di calcolo del pool elastico (vCore)

È possibile aumentare o ridurre il livello di prestazioni per un pool elastico in base alle risorse necessarie tramite il [portale di Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o l'[API REST](/rest/api/sql/elasticpools/update).

- Durante il ridimensionamento dei vCore del pool, le connessioni di database vengono brevemente interrotte. Si tratta dello stesso comportamento che si verifica durante il ridimensionamento delle DTU per un singolo database (non in un pool). Per informazioni dettagliate sulla durata e sull'impatto delle connessioni interrotte per un database durante le operazioni di ridimensionamento, vedere la sezione relativa al [ridimensionamento delle DTU per un singolo database](#single-database-change-storage-size). 
- La durata per ridimensionare i vCore del pool può dipendere dalle dimensioni totali di archiviazione usate da tutti i database nel pool. In generale, la latenza media di ridimensionamento è di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale usato da tutti i database nel pool è pari a 200 GB, la latenza prevista per il ridimensionamento del pool è di 3 ore o meno. In alcuni casi, all'interno del livello Standard o Basic, la latenza di ridimensionamento può essere inferiore a cinque minuti, indipendentemente dalla quantità di spazio usata.
- In generale, la durata per modificare il numero minimo di vCore per database o il numero massimo di vCore per database è cinque minuti o meno.
- Quando si riducono le dimensioni dei vCore del pool, lo spazio utilizzato del pool deve essere inferiore alle dimensioni massime consentite del livello di servizio di destinazione e dei vCore del pool.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>Modello di acquisto basato su DTU: modifica delle dimensioni di archiviazione del pool elastico

- Il prezzo eDTU per un pool elastico include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per le quantità di risorse di archiviazione incluse e i limiti di dimensioni massime, vedere [Pool elastico: dimensioni di archiviazione e livelli delle prestazioni](#elastic-pool-storage-sizes-and-performance-levels).
- Le risorse di archiviazione extra per un pool elastico possono essere sottoposte a provisioning aumentandone le dimensioni massime mediante il [portale di Azure](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o l'[API REST](/rest/api/sql/elasticpools/update).
- Il prezzo delle risorse di archiviazione extra per un pool elastico corrisponde alla quantità di risorse di archiviazione extra moltiplicata per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>Modello di acquisto basato su DTU: modifica delle risorse di calcolo del pool elastico (eDTU)

È possibile aumentare o ridurre le risorse disponibili per un pool elastico in base alle esigenze mediante il [portale di Azure](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) o l'[API REST](/rest/api/sql/elasticpools/update).

- Durante il ridimensionamento delle eDTU del pool, le connessioni di database vengono interrotte per un breve periodo. Si tratta dello stesso comportamento che si verifica durante il ridimensionamento delle DTU per un singolo database (non in un pool). Per informazioni dettagliate sulla durata e sull'impatto delle connessioni interrotte per un database durante le operazioni di ridimensionamento, vedere la sezione relativa al [ridimensionamento delle DTU per un singolo database](#single-database-change-storage-size). 
- La durata per ridimensionare le eDTU del pool può dipendere dalla quantità totale di risorse di archiviazione usate da tutti i database nel pool. In generale, la latenza media di ridimensionamento è di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale usato da tutti i database nel pool è pari a 200 GB, la latenza prevista per il ridimensionamento del pool è di 3 ore o meno. In alcuni casi, all'interno del livello Standard o Basic, la latenza di ridimensionamento può essere inferiore a cinque minuti, indipendentemente dalla quantità di spazio usata.
- In generale, la durata per modificare il numero minimo di eDTU per database o il numero massimo di eDTU per database è di cinque minuti o meno.
- Quando si riducono le dimensioni delle eDTU del pool, lo spazio del pool usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio e il livello di prestazioni di destinazione.
- Durante il ridimensionamento delle eDTU del pool viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime delle risorse di archiviazione del pool sono supportate dal pool di destinazione e (2) la dimensione massima delle risorse di archiviazione supera la quantità inclusa del pool di destinazione. Se ad esempio un pool standard da 100 eDTU con una dimensione massima di 100 GB viene ridotto a un pool standard da 50 eDTU, viene applicato un costo per le risorse di archiviazione extra, poiché il pool di destinazione supporta una dimensione massima di 100 GB e la quantità di risorse di archiviazione inclusa è solo di 50 GB. La quantità di risorse di archiviazione extra è quindi 100 GB - 50 GB = 50 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore alla quantità inclusa di risorse di archiviazione, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino alla quantità inclusa. 

## <a name="next-steps"></a>Passaggi successivi

Per i limiti generali delle risorse, vedere [Limiti delle risorse basate su vCore per il database SQL - pool elastici](sql-database-vcore-resource-limits-elastic-pools.md) e [Limiti delle risorse basate su DTU per il database SQL - pool elastici](sql-database-dtu-resource-limits-elastic-pools.md).
