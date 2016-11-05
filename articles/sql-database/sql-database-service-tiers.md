---
title: 'Opzioni e prestazioni del database SQL: livelli di servizio | Microsoft Docs'
description: Confrontare le prestazioni e le funzionalità di continuità aziendale dei vari livelli di servizio del database SQL per trovare il giusto compromesso tra costi e funzionalità a sostegno della scalabilità.
keywords: opzioni di database,prestazioni del database
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler

ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/10/2016
ms.author: carlrab

---
# <a name="sql-database-options-and-performance:-understand-what's-available-in-each-service-tier"></a>Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL
[database SQL di Azure](sql-database-technical-overview.md) offre tre livelli di servizio con diversi livelli di prestazioni per la gestione di carichi di lavoro differenti. Ogni livello di prestazioni offre un set di risorse superiore progettato per garantire un aumento della velocità effettiva. È possibile gestire ogni database nel proprio [livello di servizio](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) con il proprio livello di prestazioni. È anche possibile gestire più database in un [pool elastico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) con un set di risorse condiviso. Le risorse disponibili per i database autonomi sono espressi in unità di transazione di database (DTU) e per i pool elastici in unità di transazione di database elastico o eDTU. Per altre informazioni sulle DTU e le eDTU, vedere le [informazioni sulle unità di transazione di database (DTU)](sql-database-what-is-a-dtu.md). 

In entrambi i casi, i livelli di servizio includono **Basic**, **Standard** e **Premium**. Le opzioni di database in questi livelli sono simili per i database autonomi e i pool elastici, ma per questi ultimi esistono altre considerazioni. Questo articolo fornisce i dettagli relativi ai livelli di servizio per i database autonomi e per i pool elastici.

## <a name="service-tiers-and-database-options"></a>Livelli di servizio e opzioni di database
I livelli di servizio Basic, Standard e Premium garantiscono un tempo di attività previsto dal contratto di servizio del 99,99% e offrono prestazioni prevedibili, opzioni di continuità aziendale flessibili, funzionalità di sicurezza e fatturazione su base oraria. Nella tabella seguente sono riportati esempi dei livelli ottimali adatti ai carichi di lavoro di diverse applicazioni.

| Livello di servizio | Carichi di lavoro di destinazione |
| --- | --- |
| **Basic** |Più adatto per un database di piccole dimensioni, che supporta in genere una singola operazione attiva in un determinato momento. Ad esempio, database usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| **Standard** |Opzione più adatta per la maggior parte delle applicazioni cloud, che supporta più query contemporaneamente. Ad esempio, applicazioni web o per gruppi di lavoro. |
| **Premium** |Progettato per un volume di transazioni elevato, che supporta molti utenti simultanei e richiede il massimo livello di funzionalità di continuità aziendale. Ad esempio, database che supportano applicazioni mission-critical. |

> [!NOTE]
> Le edizioni Web e Business sono state ritirate. Se si prevede di continuare a usare le edizioni Web e Business, vedere [Domande frequenti sul ritiro dell'edizione Web e Business](https://azure.microsoft.com/pricing/details/sql-database/web-business/) .
> 
> 

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Livelli di servizio e di prestazioni per database autonomi
Per database autonomi sono disponibili più livelli di prestazioni per ogni livello di servizio. È possibile scegliere il livello che meglio soddisfa le esigenze del carico di lavoro. Se è necessario aumentare o ridurre le prestazioni, è possibile modificare facilmente i livelli del database. Per informazioni dettagliate, vedere [Modifica dei livelli di servizio e dei livelli di prestazioni di un database](sql-database-scale-up.md) .

Le caratteristiche delle prestazioni riportate di seguito si applicano a database creati con [SQL Database V12](sql-database-v12-whats-new.md). Indipendentemente dal numero di database ospitati, il database in uso ottiene un set garantito di risorse e le caratteristiche delle prestazioni previste per il database non saranno interessate.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Per una spiegazione dettagliata di tutte le altre righe in questa tabella dei livelli di servizio, vedere [Limiti e funzionalità dei livelli di servizio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 
> 

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Livelli di servizio e di prestazioni per pool elastici in eDTU
Oltre a creare e a ridimensionare un database autonomo, è anche possibile gestire più database all'interno di un [pool elastico](sql-database-elastic-pool.md). Tutti i database in un pool elastico condividono un set comune di risorse. Le caratteristiche delle prestazioni sono misurate dalle *unità di transazione del database elastico* (eDTU). Come con i database autonomi, per i pool sono disponibili tre livelli di servizio: **Basic**, **Standard** e **Premium**. Per i pool questi tre livelli definiscono i limiti delle prestazioni complessive e diverse funzionalità.

I pool consentono ai database di condividere e usare risorse DTU senza dover assegnare un livello di prestazioni specifico a ogni database nel pool. Ad esempio, un database autonomo in un pool Standard può passare dall'uso di 0 eDTU al numero massimo di eDTU impostato per il database quando si configura il pool. I pool consentono a più database con carichi di lavoro diversi di usare in modo efficiente le risorse eDTU disponibili per l'intero pool. Per altre informazioni relative alle considerazioni su prezzi e prestazioni per un pool di database elastici, vedere [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md) .

La tabella seguente descrive le caratteristiche dei livelli di servizio del pool.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Ogni database all'interno di un pool è anche conforme alle caratteristiche del database autonomo per quel livello. Ad esempio, il pool Basic ha un limite di 4800 - 28800 per il numero massimo di sessioni per pool. Un database singolo all'interno del pool Basic ha un limite di 300 sessioni.

## <a name="choosing-a-service-tier"></a>Scelta di un piano di servizio
Per stabilire un livello di servizio, iniziare determinando se il database deve essere un database autonomo o far parte di un pool elastico. 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>Scelta di un livello di servizio per un database autonomo
Per stabilire un livello di servizio per un database autonomo, determinare prima di tutto le funzionalità di database necessarie per scegliere l'edizione del database SQL:

* Dimensioni del database: 2 GB massimo per Basic, 250 GB massimo per Standard e da 500 GB a 1 TB massimo per Premium, a seconda del livello di prestazioni.
* Periodo di conservazione dei backup del database: 7 giorni per Basic, 35 giorni per Standard e 35 giorni per Premium.

Dopo aver determinato l'edizione del database SQL, è possibile determinare il livello di prestazioni del database, ovvero il numero di DTU. Si può fare una supposizione e quindi [aumentare o ridurre le prestazioni in modo dinamico](sql-database-scale-up.md) in base all'esperienza effettiva. È anche possibile usare lo strumento per il [calcolo di DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessario. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool."></a>Scelta di un livello di servizio per un pool di database elastici
Per stabilire un livello di servizio per un pool di database elastici, determinare prima di tutto le funzionalità di database necessarie per scegliere il livello di servizio per il pool.

* Dimensioni del database: 2 GB per Basic, 250 GB per Standard e 500 GB per Premium.
* Periodo di conservazione dei backup del database: 7 giorni per Basic, 35 giorni per Standard e 35 giorni per Premium.
* Numero di database per ogni pool: 400 per Basic, 400 per Standard e 50 per Premium.
* Archiviazione massima per ogni pool: 117 GB per Basic, 1200 per Standard e 750 per Premium.

Dopo aver determinato il livello di servizio per il pool, è possibile determinarne il livello di prestazioni (eDTU). Si può fare una supposizione e quindi [aumentare o ridurre le prestazioni in modo dinamico](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) in base all'esperienza effettiva. È possibile usare lo strumento per il [calcolo di DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessario per ogni database in un pool, per facilitare la definizione del limite massimo per il pool.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sui prezzi di tali livelli sono disponibili in [Database SQL - Prezzi](https://azure.microsoft.com/pricing/details/sql-database/).
* Informazioni dettagliate sui [pool elastici](sql-database-elastic-pool-guidance.md) e [considerazioni su prezzi e prestazioni dei pool elastici](sql-database-elastic-pool-guidance.md).
* Informazioni su come [monitorare, gestire e ridimensionare i pool elastici](sql-database-elastic-pool-manage-portal.md) e su come [monitorare le prestazioni di database autonomi](sql-database-single-database-monitor.md).
* Dopo aver acquisito familiarità con i livelli del database SQL, provare con un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) per scoprire come [creare il primo database SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Modelli di progettazione per le applicazioni SaaS multi-tenant e il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Esercitazione video di Microsoft Virtual Academy sulle funzionalità di database elastico nel database SQL di Azure](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)

<!--HONumber=Oct16_HO2-->


