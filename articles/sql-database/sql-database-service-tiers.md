---
title: 'Prestazioni del database SQL: livelli di servizio | Microsoft Docs'
description: Confrontare i livelli di servizio del database SQL.
keywords: opzioni di database,prestazioni del database
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/15/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 7bbdbe345bd468c01e2a790610bcf6c063c11f9b


---
# <a name="sql-database-service-tiers-for-single-databases-and-elastic-database-pools"></a>Livelli di servizio del database SQL per database singoli e pool di database elastici
[database SQL di Azure](sql-database-technical-overview.md) offre tre livelli di servizio con diversi livelli di prestazioni per la gestione di carichi di lavoro differenti. Livelli di prestazioni più elevati offrono un set di risorse superiore progettato per garantire un aumento della velocità effettiva. È possibile cambiare i livelli di servizio e di prestazioni in modo dinamico. Per informazioni dettagliate, vedere [Modifica dei livelli di servizio e dei livelli di prestazioni di un database](sql-database-scale-up.md) .

È possibile gestire ogni database nel proprio [livello di servizio](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) con il proprio livello di prestazioni. È anche possibile gestire più database in un [pool di database elastici](sql-database-service-tiers.md#elastic-database-pool-service-tiers-and-performance-in-edtus) con un set di risorse condiviso. Le risorse disponibili per i database singoli sono espresse in DTU (Database Transaction Unit) e per i pool di database elastici in DTU o eDTU. Per altre informazioni sulle DTU e le eDTU, vedere le [informazioni sulle unità di transazione di database (DTU)](sql-database-what-is-a-dtu.md). 

In entrambi i casi, i livelli di servizio includono **Basic**, **Standard** e **Premium**. 

## <a name="service-tiers"></a>Livelli di servizio
I livelli di servizio Basic, Standard e Premium garantiscono un tempo di attività previsto dal contratto di servizio del 99,99% e offrono prestazioni prevedibili, opzioni di continuità aziendale flessibili, funzionalità di sicurezza e fatturazione su base oraria. Nella tabella seguente sono riportati esempi dei livelli ottimali adatti ai carichi di lavoro di diverse applicazioni.

| Livello di servizio | Carichi di lavoro di destinazione |
| :--- | --- |
| **Basic** |Più adatto per un database di piccole dimensioni, che supporta in genere una singola operazione attiva in un determinato momento. Ad esempio, database usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| **Standard** |Opzione più adatta per la maggior parte delle applicazioni cloud, che supporta più query contemporaneamente. Ad esempio, applicazioni web o per gruppi di lavoro. |
| **Premium** |Progettato per un volume di transazioni elevato, che supporta molti utenti simultanei e richiede il massimo livello di funzionalità di continuità aziendale. Ad esempio, database che supportano applicazioni mission-critical. |

> [!NOTE]
> Le edizioni Web e Business sono state ritirate. Se si prevede di continuare a usare le edizioni Web e Business, vedere [Domande frequenti sul ritiro dell'edizione Web e Business](https://azure.microsoft.com/pricing/details/sql-database/web-business/) .
> 
> 

## <a name="single-database-service-tiers-and-performance-levels"></a>Livelli di servizio e di prestazioni per database singoli
Per i database singoli sono disponibili più livelli di prestazioni all'interno di ogni livello di servizio. È possibile scegliere il livello che meglio soddisfa le esigenze del carico di lavoro. Se è necessario aumentare o ridurre le prestazioni, è possibile modificare rapidamente il livello di prestazioni del database. Per informazioni dettagliate, vedere [Modifica dei livelli di servizio e dei livelli di prestazioni di un database](sql-database-scale-up.md) .

Le caratteristiche delle prestazioni riportate di seguito si applicano a database creati con [SQL Database V12](sql-database-technical-overview.md). Indipendentemente dal numero di database ospitati, il database in uso ottiene un set garantito di risorse e le caratteristiche delle prestazioni previste per il database non saranno interessate.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Per una spiegazione dettagliata di tutte le altre righe in questa tabella dei livelli di servizio, vedere [Limiti e funzionalità dei livelli di servizio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 
> 

## <a name="elastic-database-pool-service-tiers-and-performance-in-edtus"></a>Livelli di servizio e di prestazioni per pool di database elastici in eDTU
È anche possibile gestire più database all'interno di un [pool di database elastici](sql-database-elastic-pool.md). Tutti i database in un pool di database elastici condividono un insieme di risorse comune. Le caratteristiche delle prestazioni sono misurate dalle *unità di transazione del database elastico* (eDTU). Come con i database singoli, per i pool sono disponibili tre livelli di servizio: **Basic**, **Standard** e **Premium**. Per i pool questi tre livelli definiscono i limiti delle prestazioni complessive e diverse funzionalità.

I pool consentono ai database di condividere e usare risorse DTU senza dover assegnare un livello di prestazioni specifico a ogni database nel pool. Ad esempio, un database in un pool Standard può passare dall'uso di 0 eDTU al valore eDTU massimo per il database impostato quando si configura il pool. I pool consentono a più database con carichi di lavoro diversi di usare in modo efficiente le risorse eDTU disponibili per l'intero pool. Per informazioni dettagliate, vedere [Considerazioni su prezzi e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).

La tabella seguente descrive le caratteristiche dei pool di database elastici Basic, Standard e Premium.

[!INCLUDE [SQL DB service tiers table for elastic database pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Ciascun database all'interno di un pool è inoltre conforme alle caratteristiche di database singolo per il livello in questione. Ad esempio, il pool Basic ha un limite di 4800 - 28800 per il numero massimo di sessioni per pool. Un database singolo all'interno del pool Basic ha un limite di 300 sessioni.

## <a name="choosing-a-service-tier"></a>Scelta di un piano di servizio
Per stabilire un livello di servizio, iniziare determinando se il database deve essere un database singolo o far parte di un pool di database elastici. 

### <a name="choosing-a-service-tier-for-a-single-database"></a>Scelta di un livello di servizio per un database singolo
Per stabilire un livello di servizio per un database singolo, determinare prima di tutto le funzionalità di database necessarie per scegliere l'edizione del database SQL:

* Dimensioni del database: 2 GB massimo per Basic, 250 GB massimo per Standard e da 500 GB a 1 TB massimo per Premium, a seconda del livello di prestazioni.
* Periodo di conservazione dei backup del database: 7 giorni per Basic, 35 giorni per Standard e 35 giorni per Premium.

Dopo aver determinato l'edizione del database SQL, è possibile determinare il livello di prestazioni del database, ovvero il numero di DTU. Si può fare una supposizione e quindi [aumentare o ridurre le prestazioni in modo dinamico](sql-database-scale-up.md) in base all'esperienza effettiva. È anche possibile usare lo strumento per il [calcolo di DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessario. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Scelta di un livello di servizio per un pool di database elastici
Per stabilire un livello di servizio per un pool di database elastici, determinare prima di tutto le funzionalità di database necessarie per scegliere il livello di servizio per il pool.

* Dimensioni del database: 2 GB per Basic, 250 GB per Standard e 500 GB per Premium.
* Periodo di conservazione dei backup del database: 7 giorni per Basic, 35 giorni per Standard e 35 giorni per Premium.
* Numero di database per ogni pool: 400 per Basic, 400 per Standard e 50 per Premium.
* Archiviazione massima per ogni pool: 117 GB per Basic, 1200 per Standard e 750 per Premium.

Dopo aver determinato il livello di servizio per il pool, è possibile determinarne il livello di prestazioni (eDTU). Si può fare una supposizione e quindi [aumentare o ridurre le prestazioni in modo dinamico](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) in base all'esperienza effettiva. È possibile usare lo strumento per il [calcolo di DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessario per ogni database in un pool, per facilitare la definizione del limite massimo per il pool.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sui prezzi di tali livelli sono disponibili in [Database SQL - Prezzi](https://azure.microsoft.com/pricing/details/sql-database/).
* Informazioni dettagliate sui [pool di database elastici](sql-database-elastic-pool-guidance.md) e [considerazioni su prezzi e prestazioni per i pool di database elastici](sql-database-elastic-pool-guidance.md).
* Informazioni su come [monitorare, gestire e ridimensionare un pool di database elastici](sql-database-elastic-pool-manage-portal.md) e [monitorare le prestazioni di database singoli](sql-database-single-database-monitor.md).
* Dopo aver acquisito familiarità con i livelli del database SQL, provare con un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) per scoprire come [creare il primo database SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Modelli di progettazione per le applicazioni SaaS multi-tenant e il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Esercitazione video di Microsoft Virtual Academy sulle funzionalità di database elastico nel database SQL di Azure](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)




<!--HONumber=Nov16_HO4-->


