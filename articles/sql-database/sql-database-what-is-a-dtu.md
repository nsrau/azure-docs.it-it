---
title: 'Database SQL: Informazioni sulle DTU | Microsoft Docs'
description: Informazioni sulle unità di transazione di database SQL di Azure.
keywords: opzioni di database,prestazioni del database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 3fb0d0f73f475e246580f7ea6e4aea60b069c230
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894008"
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)
Questo articolo illustra le unità di transazione di database (DTU) e le unità di transazione di database elastico (eDTU) e cosa accade quando si raggiunge il numero massimo di DTU o eDTU. Per informazioni specifiche sui prezzi, vedere [Prezzi di Database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="what-are-database-transaction-units-dtus"></a>Cosa sono le unità di transazione di database (DTU)?
Per un singolo database SQL di Azure a un livello di prestazioni specifico all'interno di un [livello di servizio](sql-database-single-database-resources.md), Microsoft garantisce un certo livello di risorse per il database (a prescindere da qualsiasi altro database nel cloud di Azure), fornendo così un livello di prestazioni prevedibile. La quantità di risorse viene calcolata come numero di unità di transazione di database o DTU ed è una misura combinata delle risorse di calcolo, archiviazione e I/O. Il rapporto tra queste risorse è stato originariamente stabilito da un [carico di lavoro di benchmark OLTP](sql-database-benchmark-overview.md) progettato per essere rappresentativo dei carichi di lavoro OLTP reali. Quando il carico di lavoro supera la quantità di una di queste risorse, la velocità effettiva viene limitata, generando timeout e prestazioni più lente. Le risorse usate dal carico di lavoro non incidono sulle risorse disponibili per gli altri database SQL nel cloud di Azure e le risorse usate dagli altri carichi di lavoro non incidono sulle risorse disponibili per il database SQL.

![rettangolo di selezione](./media/sql-database-what-is-a-dtu/bounding-box.png)

Le DTU sono particolarmente utili per comprendere la quantità relativa di risorse tra i database SQL di Azure a diversi livelli di prestazioni e livelli di servizio. Ad esempio, raddoppiare le DTU aumentando il livello di prestazioni di un database equivale a raddoppiare il set di risorse disponibili per quel database. Ad esempio, un database Premium P11 con 1750 DTU fornisce 350 volte più potenza di calcolo DTU di un database Basic con 5 DTU.  

Per ottenere maggiori dettagli sul consumo di risorse (DTU) del carico di lavoro, usare [Query Performance Insight del database SQL di Azure](sql-database-query-performance.md) per:

- Identificare le query principali a livello di CPU/durata/conteggio delle esecuzioni, che possono essere potenzialmente ottimizzate per migliorare le prestazioni. Una query con uso intensivo dell'I/O, ad esempio, può trarre vantaggio dall'uso di [tecniche di ottimizzazione in memoria](sql-database-in-memory.md) per usare in modo più efficiente la memoria disponibile per un livello di servizio e di prestazioni specifico.
- Eseguire il drill-down dei dettagli di una query, visualizzarne il testo e la cronologia di utilizzo delle risorse.
- Accedere alle raccomandazioni relative all'ottimizzazione delle prestazioni che descrivono le azioni eseguite da [Advisor per database SQL](sql-database-advisor.md).

È possibile [modificare i livelli di servizio delle DTU](sql-database-service-tiers-dtu.md) in qualsiasi momento con tempi di inattività minimi per l'applicazione, in genere meno di 4 secondi. Per molte aziende e app, la possibilità di creare database e connettere o disconnettere prestazioni su richiesta è sufficiente, specialmente se i modelli d'uso sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. Per questo scenario usare un pool elastico con un determinato numero di eDTU condivise tra più database nel pool.

![Introduzione al database SQL: DTU di database singolo in base al livello](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Cosa sono le unità di transazione di database elastico (eDTU)?
Anziché fornire un set di risorse (DTU) dedicato che potrebbe non essere sempre necessario per un database SQL sempre disponibile, è possibile inserire i database in un [pool elastico](sql-database-elastic-pool.md) in un server di database SQL che condivide un pool di risorse tra tali database. Le risorse condivise in un pool elastico sono misurate dalle unità di transazione di database elastiche o eDTU. I pool elastici offrono una soluzione semplice e conveniente per gestire gli obiettivi di prestazioni per più database con modelli di utilizzo estremamente mutevoli e imprevedibili. Un pool elastico garantisce che le risorse non possano essere utilizzate da un solo database nel pool, assicurando allo stesso tempo che per ogni database del pool sia sempre disponibile una quantità minima di risorse necessaria. 

![Introduzione al database SQL: eDTU in base al livello](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A un pool viene assegnato un numero definito di eDTU per un prezzo prestabilito. All'interno del pool elastico, i singoli database sono sufficientemente flessibili da assicurare la scalabilità automatica nell'ambito di limiti configurati. Un database con un carico di lavoro più importante utilizzerà più eDTU per soddisfare la domanda. I database con carichi più leggeri utilizzeranno meno eDTU. I database senza alcun carico di lavoro non utilizzeranno eDTU. Effettuando il provisioning delle risorse per l'intero pool e non per i singoli database, le attività di gestione si semplificano e si ha a disposizione un budget prevedibile per il pool.

È possibile aggiungere altre eDTU a un pool esistente senza causare tempi di inattività del database o effetti negativi sui database nel pool. Analogamente, se le eDTU aggiuntive non sono più necessarie, è possibile rimuoverle da un pool esistente in qualsiasi momento. È possibile aggiungere o sottrarre database al pool oppure limitare la quantità di eDTU che un database può usare in condizioni di carico elevato per riservare eDTU per altri database. Se si prevede un sottoutilizzo di risorse per un database, il database può essere rimosso dal pool e configurato come database singolo con una quantità prevedibile di risorse necessarie.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Come si determina il numero di DTU necessarie per il carico di lavoro?
Se si intende eseguire la migrazione di un carico di lavoro locale o di un carico di lavoro di macchine virtuali di SQL Server a un database SQL di Azure, è possibile usare lo [strumento di calcolo DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessarie. Per un carico di lavoro esistente del database SQL di Azure, è possibile usare [Informazioni dettagliate prestazioni query del database SQL](sql-database-query-performance.md) per comprendere il consumo di risorse di database (DTU) e ottenere dati più approfonditi per l'ottimizzazione del carico di lavoro. È anche possibile usare la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) per visualizzare il consumo di risorse per l'ultima ora. In alternativa, la vista del catalogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) visualizza il consumo di risorse per gli ultimi 14 giorni, ma a una fedeltà inferiore di medie di cinque minuti.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Come è possibile sapere se un pool elastico di risorse può essere utile?
I pool sono adatti per un numero elevato di database con modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti. Database SQL valuta automaticamente la cronologia d’utilizzo delle risorse dei database in un server di database SQL esistente e consiglia una configurazione appropriata del pool nel portale di Azure. Per altre informazioni, vedere [Quando usare un pool elastico](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Cosa succede se si raggiunge il numero massimo di DTU?
I livelli di prestazioni vengono calibrati e gestiti per offrire le risorse necessarie per eseguire il carico di lavoro del database fino ai limiti massimi consentiti per il livello di servizio/livello di prestazioni selezionato. Se il carico di lavoro raggiunge uno dei limiti di CPU/I/O dati/I/O log, si continuerà a ricevere il livello massimo di risorse consentito, ma probabilmente si sperimenteranno anche latenze di query più elevate. Con questi limiti non si verificheranno errori, ma solo un rallentamento nel carico di lavoro, a meno che il rallentamento non diventi così grave da provocare il timeout delle query. Se si raggiunge il limite massimo di richieste o di sessioni utente simultanee (thread di lavoro), verranno visualizzati errori espliciti. Vedere [Limiti delle risorse del database SQL di Azure]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) per informazioni sui limiti delle risorse non correlati a CPU, memoria, I/O dei dati o I/O del log delle transazioni.

## <a name="next-steps"></a>Passaggi successivi
* Vedere [Modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) per informazioni sulle DTU e sulle eDTU disponibili per database singoli e per pool elastici, nonché sui limiti di risorse diverse da CPU, memoria, I/O dei dati e I/O del log delle transazioni.
* Vedere [Modello di acquisto basato su vCore (anteprima)](sql-database-service-tiers-vcore.md) per informazioni sull'allocazione delle risorse basato su vCore e i relativi livelli di servizio. 
* Vedere [Informazioni dettagliate prestazioni query del database SQL di Azure](sql-database-query-performance.md) per informazioni sul consumo di DTU.
* Vedere [Informazioni generali sul benchmark del database SQL di Azure](sql-database-benchmark-overview.md) per comprendere la metodologia alla base del carico di lavoro di benchmark OLTP usato per determinare la combinazione di valori per la DTU.
