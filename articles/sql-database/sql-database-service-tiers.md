---
title: 'Prestazioni del database SQL: livelli di servizio | Microsoft Docs'
description: Confrontare i livelli di servizio del database SQL.
keywords: opzioni di database,prestazioni del database
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/09/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 8ed4b3b30df6756c4e99e77476bc4c3a21bba90e
ms.openlocfilehash: e93d54910d8ed64879e66542c4c795101bc19a41


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL
Il [database SQL di Azure](sql-database-technical-overview.md) offre tre livelli di servizio, **Basic**, **Standard** e **Premium**, con diversi livelli di prestazioni per la gestione di carichi di lavoro differenti. Livelli di prestazioni più elevati offrono sempre più risorse progettate per garantire un aumento della velocità effettiva. È possibile modificare i [livelli di servizio e di prestazioni in modo dinamico](sql-database-scale-up.md) senza tempi di inattività. I livelli di servizio Basic, Standard e Premium garantiscono un tempo di attività previsto dal contratto di servizio del 99,99% e opzioni di continuità aziendale flessibili, funzionalità di sicurezza e fatturazione su base oraria. 

È possibile creare database singoli con risorse dedicate per il [livello di prestazioni](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) selezionato. È inoltre possibile gestire più database in un [pool elastico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) in cui le risorse sono condivise tra i database. Le risorse disponibili per i database singoli sono espresse in unità di transazione di database (DTU) e per i pool elastici in unità di transazione di database elastico (eDTU). Per altre informazioni sulle DTU e le eDTU, vedere [Informazioni sulle DTU](sql-database-what-is-a-dtu.md). 

In entrambi i casi, i livelli di servizio includono **Basic**, **Standard** e **Premium**. 

## <a name="choosing-a-service-tier"></a>Scelta di un piano di servizio
Nella tabella seguente sono riportati esempi dei livelli ottimali adatti ai carichi di lavoro di diverse applicazioni.

| Livello di servizio | Carichi di lavoro di destinazione |
| :--- | --- |
| **Basic** | Più adatto per un database di piccole dimensioni, che supporta in genere una singola operazione attiva in un determinato momento. Ad esempio, database usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| **Standard** |Opzione per le applicazioni cloud con requisiti di prestazioni I/O bassi o medi, supporto di più query contemporaneamente. Ad esempio, applicazioni web o per gruppi di lavoro. |
| **Premium** | Progettato per volumi di transazioni elevati con alti requisiti di prestazioni I/O che supportano più utenti contemporaneamente. Ad esempio, database che supportano applicazioni mission-critical. |

Innanzitutto, decidere se si vuole eseguire un database singolo o se si vuole raggruppare i database che condividono risorse. Fare riferimento alle [considerazioni sul pool elastico](sql-database-elastic-pool-guidance.md). Per decidere il livello di servizio da usare, stabilire innanzitutto le funzionalità minime necessarie del database:

* Dimensioni massime del database per database singoli: 2 GB massimo per Basic, 250 GB massimo per Standard e da 500 GB a 1 TB massimo per Premium per livelli di prestazioni elevati
* Archiviazione totale massima in un pool elastico: 117 GB per Basic, 1200 GB per Standard e 750 GB per Premium
* Numero massimo di database per ogni pool: 400 per Basic, 400 per Standard e 50 per Premium
* Periodo di conservazione dei backup del database: 7 giorni per Basic e 35 giorni per Standard e Premium

Dopo aver determinato il livello di servizio minimo, è possibile determinare il livello di prestazioni del database, ovvero il numero di DTU. I livelli di prestazioni Standard S2 e S3 sono in molti casi un valido punto di inizio. Per i database con requisiti elevati di CPU o I/O, i livelli di prestazioni Premium sono il punto di partenza ottimale. Premium offre più CPU e inizia a 10 volte più I/O rispetto al livello di prestazioni Standard massimo.

## <a name="single-database-service-tiers-and-performance-levels"></a>Livelli di servizio e di prestazioni per database singoli
Per i database singoli sono disponibili più livelli di prestazioni all'interno di ogni livello di servizio. È possibile scegliere il livello che meglio soddisfa le esigenze del carico di lavoro. Se è necessario aumentare o ridurre le prestazioni, è possibile modificare facilmente i livelli del database. Per informazioni dettagliate, vedere [Modifica dei livelli di servizio e dei livelli di prestazioni di un database](sql-database-scale-up.md) .

Indipendentemente dal numero di database ospitati, il database in uso ottiene un set garantito di risorse e le caratteristiche delle prestazioni previste per il database non saranno interessate.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Per una spiegazione dettagliata di tutte le altre righe in questa tabella dei livelli di servizio, vedere [Limiti e funzionalità dei livelli di servizio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Ridimensionamento di un singolo database

Dopo aver selezionato inizialmente un livello di servizio e di prestazioni, è possibile ridimensionare un singolo database in modo dinamico in base all'esperienza effettiva. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modifica del livello di servizio e/o di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Questa finestra varia, ma in media è inferiore a 4 secondi e in più del 99% dei casi è inferiore a 30 secondi. Se è presente un elevato numero di transazioni in-flight quando le connessioni sono disabilitate, questa finestra potrebbe essere più lunga.  

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del database prima e dopo la modifica. Ad esempio, il passaggio di un database di 250 GB al livello di servizio Standard o dal livello di servizio Standard a un altro livello o nell'ambito dello stesso livello di servizio Standard viene completato entro 6 ore. Per un database delle stesse dimensioni in fase di modifica dei livelli di prestazioni all'interno del livello di servizio Premium, il completamento dovrebbe avvenire entro 3 ore.

* Per effettuare il downgrade di un database, la dimensione di quest'ultimo deve essere inferiore alla dimensione massima consentita per il livello del servizio di destinazione. 
* Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, è necessario aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario.
* Quando si effettua il downgrade da un livello di servizio Premium, è necessario prima terminare tutte le relazioni di replica geografica. È possibile attenersi alla procedura descritta nell'argomento [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md) per arrestare il processo di replica tra il database primario e i database secondari attivi.
* Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per ulteriori informazioni, vedere [Backup e ripristino del database SQL di Azure](sql-database-business-continuity.md).
* Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

> [!IMPORTANT]
> Per informazioni dettagliate, vedere [Gestione di singoli database nel portale di Azure](sql-database-manage-single-databases-portal.md), [Gestione di singoli database con Powershell](sql-database-manage-single-databases-powershell.md) oppure [Gestione di singoli database con Transact-SQL](sql-database-manage-single-databases-tsql.md).
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Livelli di servizio e di prestazioni per pool elastici in eDTU

I pool consentono ai database di condividere e usare risorse DTU senza dover assegnare un livello di prestazioni specifico a ogni database nel pool. Ad esempio, un database singolo in un pool Standard può passare dall'uso di 0 eDTU al valore eDTU massimo per il database impostato quando si configura il pool. I pool consentono a più database con carichi di lavoro diversi di usare in modo efficiente le risorse eDTU disponibili per l'intero pool. Per altre informazioni relative alle considerazioni su prezzi e prestazioni per un pool di database elastici, vedere [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md) .

La tabella seguente descrive le caratteristiche dei livelli di servizio del pool.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Ciascun database all'interno di un pool è inoltre conforme alle caratteristiche di database singolo per il livello in questione. Ad esempio, il pool Basic ha un limite di 4800 - 28800 per il numero massimo di sessioni per pool. Un database singolo all'interno del pool Basic ha un limite di 300 sessioni.

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>Ridimensionamento di un pool elastico

Dopo aver selezionato inizialmente un livello di servizio e di prestazioni, è possibile ridimensionare un pool elastico in modo dinamico in base all'esperienza effettiva. 

* La modifica del numero minimo di eDTU per database o del numero massimo di eDTU per database in genere viene completata entro cinque minuti.
* Il tempo richiesto per modificare le dimensioni del pool (eDTU) dipende dalla dimensione combinata di tutti i database nel pool. Le modifiche richiedono una media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale di tutti i database nel pool è pari a 200 GB, la latenza prevista per la modifica del numero di eDTU del pool per ogni pool è di 3 ore o meno.

> [!IMPORTANT]
> Per informazioni dettagliate, vedere [Gestione di pool elastici nel portale di Azure](sql-database-elastic-pool-manage-portal.md), [Gestione di pool elastici con Powershell](sql-database-elastic-pool-manage-powershell.md), [Gestione di pool elastici con Transact-SQL](sql-database-elastic-pool-manage-tsql.md) oppure [Gestione di pool elastici con C#](sql-database-elastic-pool-manage-csharp.md).
>

## <a name="next-steps"></a>Passaggi successivi

* Informazioni dettagliate sui [pool elastici](sql-database-elastic-pool-guidance.md) e [considerazioni su prezzi e prestazioni dei pool elastici](sql-database-elastic-pool-guidance.md).
* Informazioni su come [Monitorare e gestire un pool di database elastici con il portale di Azure](sql-database-elastic-pool-manage-portal.md) e [Monitorare le prestazioni del database nel database SQL di Azure](sql-database-single-database-monitor.md).
* Dopo aver acquisito familiarità con i livelli del database SQL, provare con un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) per scoprire come [creare il primo database SQL](sql-database-get-started.md).
* Per gli scenari di migrazione usare lo strumento per il [calcolo di DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessario. 




<!--HONumber=Feb17_HO2-->


