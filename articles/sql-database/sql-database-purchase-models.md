---
title: Modelli di acquisto del database SQL di Azure | Microsoft Docs
description: Informazioni sui modelli di acquisto disponibili per il database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: ab291ab60a5e72b5c61552bc54c10e303c1df1a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492492"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Scegliere tra i modelli di acquisto vCore e DTU

Il database SQL di Azure ti permette di acquistare con facilità un motore di database di piattaforma distribuita come servizio (PaaS) completamente gestito che soddisfa le tue esigenze in termini di prestazioni e costi. A seconda del modello di distribuzione scelto per il database SQL di Azure, è possibile selezionare il modello di acquisto che funziona correttamente:

- [Modello di acquisto basato su Virtual Core (vCore)](sql-database-service-tiers-vcore.md) (scelta consigliata). Questo modello di acquisto consente di scegliere tra un livello di calcolo di cui è stato effettuato il provisioning e un livello di calcolo senza server. Con il livello di calcolo di cui è stato effettuato il provisioning, si sceglie la quantità esatta di risorse di calcolo di cui è sempre stato effettuato il provisioning per il carico di lavoro. Con il livello di calcolo senza server è possibile specificare la scalabilità automatica delle risorse di calcolo in un intervallo di calcolo configurabile. Con questo livello di calcolo, è anche possibile sospendere e riprendere automaticamente il database in base all'attività del carico di lavoro. Il prezzo unitario di vCore per unità di tempo è inferiore al livello di calcolo di cui è stato effettuato il provisioning rispetto al livello di calcolo senza server.
- [Modello di acquisto basato su unità di transazione di database (DTU)](sql-database-service-tiers-dtu.md). Questo modello di acquisto fornisce pacchetti di calcolo e archiviazione in bundle bilanciati per carichi di lavoro comuni.

Sono disponibili modelli di acquisto diversi per i diversi modelli di distribuzione del database SQL di Azure:

- Le opzioni di distribuzione [database singolo](sql-database-single-databases-manage.md) e [pool elastico](sql-database-elastic-pool.md) nel [database SQL di Azure](sql-database-technical-overview.md) offrono sia il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) sia il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- L'opzione di distribuzione [istanza gestita](sql-database-managed-instance.md) nel database SQL di Azure offre solo il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- Il [livello di servizio iperscalabile](sql-database-service-tier-hyperscale.md) è disponibile per i singoli database che usano il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

La tabella e il grafico seguenti confrontano e contrastano i modelli di acquisto basati su vCore e DTU:

|**Modello di acquisto**|**Descrizione**|**Ideale per**|
|---|---|---|
|Modello basato su DTU|Questo modello è basato su una misura in bundle di risorse di calcolo, archiviazione e I/O. Le dimensioni di calcolo sono espresse in DTU per database singoli e in unità di transazione di database elastico (edtu) per i pool elastici. Per ulteriori informazioni su DTU e edtu, vedere informazioni su [DTU e edtu](sql-database-purchase-models.md#dtu-based-purchasing-model).|Ideale per i clienti che desiderano opzioni semplici e preconfigurate per le risorse.|
|Modello basato su vCore|Questo modello consente di scegliere in modo indipendente le risorse di calcolo e archiviazione. Il modello di acquisto basato su vCore offre inoltre la possibilità di usare [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) per un risparmio in termini di costi.|Ideale per i clienti che danno valore alla trasparenza, al controllo e alla flessibilità.|
||||  

![confronto tra modelli di prezzi](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Costi di calcolo

### <a name="provisioned-compute-costs"></a>Costi di calcolo con provisioning

Nel livello di calcolo con provisioning il costo di calcolo riflette la capacità di calcolo totale di cui è stato effettuato il provisioning per l'applicazione.

Nel livello di servizio Business Critical vengono allocate automaticamente almeno tre repliche. Per riflettere questa allocazione aggiuntiva di risorse di calcolo, il prezzo nel modello di acquisto basato su vCore è superiore a 2.7 x nel livello di servizio business critical rispetto al livello di servizio per utilizzo generico. Analogamente, il prezzo di archiviazione superiore per GB nel livello di servizio business critical riflette l'i/O elevato e la bassa latenza dell'archiviazione SSD.

Il costo dell'archiviazione di backup è lo stesso per il livello di servizio business critical e per il livello di servizio per utilizzo generico perché entrambi i livelli usano l'archiviazione standard.

### <a name="serverless-compute-costs"></a>Costi di calcolo senza server

Per una descrizione del modo in cui viene definita la capacità di calcolo e i costi vengono calcolati per il livello di calcolo senza server, vedere [database SQL senza server](sql-database-serverless.md).

## <a name="storage-costs"></a>Costi di archiviazione

I vari tipi di archiviazione vengono fatturati in modo diverso. Per l'archiviazione dei dati, viene addebitata l'archiviazione di cui è stato effettuato il provisioning in base alle dimensioni massime del database o del pool selezionato. Il costo non cambia a meno che non si riduca o aumenti il valore massimo. Le risorse di archiviazione dei backup sono associate ai backup automatizzati dell'istanza e vengono allocate in modo dinamico. L'aumento del periodo di conservazione del backup comporta l'aumento dell'archiviazione di backup utilizzata dall'istanza.

Per impostazione predefinita, 7 giorni di backup automatici dei database vengono copiati in un account di archiviazione BLOB standard con ridondanza geografica e accesso in lettura (RA-GRS). Questa archiviazione viene utilizzata da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni, copiati ogni 5 minuti. Le dimensioni dei log delle transazioni dipendono dalla frequenza di modifica del database. Un importo di archiviazione minimo pari al 100% delle dimensioni del database viene fornito senza costi aggiuntivi. Se si utilizza una quantità maggiore di risorse di archiviazione per i backup, viene applicato un addebito in base ai GB utilizzati in più ogni mese.

Per altre informazioni sui prezzi delle risorse di archiviazione, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

Un core virtuale (vCore) rappresenta una CPU logica e offre la possibilità di scegliere tra generazioni di hardware e le caratteristiche fisiche dell'hardware, ad esempio il numero di core, la memoria e le dimensioni di archiviazione. Il modello di acquisto basato su vCore offre flessibilità, controllo, trasparenza del consumo di risorse individuali e un modo semplice per tradurre i requisiti del carico di lavoro locale nel cloud. Questo modello consente di scegliere risorse di calcolo, memoria e archiviazione in base alle esigenze del carico di lavoro.

Nel modello di acquisto basato su vCore è possibile scegliere tra i livelli di servizio per [utilizzo generico](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e [business critical](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) per [database singoli](sql-database-single-database-scale.md), [pool elastici](sql-database-elastic-pool.md)e [istanze gestite](sql-database-managed-instance.md). Per i database singoli, è anche possibile scegliere il [livello di servizio di iperscalabilità](sql-database-service-tier-hyperscale.md).

Il modello di acquisto basato su vCore consente di scegliere in modo indipendente le risorse di calcolo e di archiviazione, soddisfare le prestazioni locali e ottimizzare il prezzo. Nel modello di acquisto basato su vCore si paga:

- Risorse di calcolo (il livello di servizio + il numero di Vcore e la quantità di memoria e la generazione di hardware).
- Il tipo e la quantità di dati e l'archiviazione dei log.
- Archivio di backup (RA-GRS).

> [!IMPORTANT]
> Le risorse di calcolo, l'I/O e l'archiviazione di dati e log vengono addebitate in base al database o al pool elastico. L'archiviazione di backup viene addebitata per ogni database. Per altre informazioni sui costi delle istanze gestite, vedere le [istanze gestite](sql-database-managed-instance.md).
> **Limitazioni dell'area:** Per l'elenco corrente delle aree supportate, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Per creare un'istanza gestita in un'area che attualmente non è supportata, [inviare una richiesta di supporto tramite il portale di Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Se il database singolo o il pool elastico consuma più di 300 DTU, la conversione nel modello di acquisto basato su vCore potrebbe ridurre i costi. È possibile eseguire la conversione usando l'API scelta o usando il portale di Azure, senza tempi di inattività. Tuttavia, la conversione non è necessaria e non viene eseguita automaticamente. Se il modello di acquisto basato su DTU soddisfa i requisiti aziendali e di prestazioni, è consigliabile continuare a usarlo.

Per eseguire la conversione dal modello di acquisto basato su DTU al modello di acquisto basato su vCore, selezionare la dimensione di calcolo usando le seguenti regole di Thumb:

- Ogni 100 DTU nel livello standard richiede almeno 1 vCore nel livello di servizio per utilizzo generico.
- Ogni 125 DTU nel livello Premium richiede almeno 1 vCore nel livello di servizio business critical.

## <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

Un'unità di transazione di database (DTU) rappresenta una misura combinata di CPU, memoria, letture e scritture. Il modello di acquisto basato su DTU offre un set di bundle preconfigurati di risorse di calcolo e archiviazione per diversi livelli di prestazioni dell'applicazione. Se si preferisce la semplicità di un bundle preconfigurato e i pagamenti fissi ogni mese, il modello basato su DTU potrebbe essere più adatto alle proprie esigenze.

Nel modello di acquisto basato su DTU è possibile scegliere tra i livelli di servizio Basic, standard e Premium sia per i [database singoli](sql-database-single-database-scale.md) che per i [pool elastici](sql-database-elastic-pool.md). Il modello di acquisto basato su DTU non è disponibile per le [istanze gestite](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unità di transazione di database (DTU)

Per un singolo database con dimensioni di calcolo specifiche all'interno di un [livello di servizio](sql-database-single-database-scale.md), Microsoft garantisce un certo livello di risorse per il database, indipendentemente da qualsiasi altro database nel cloud di Azure. Questa garanzia garantisce un livello di prestazioni prevedibile. La quantità di risorse allocate per un database viene calcolata come numero di DTU ed è una misura in bundle di risorse di calcolo, archiviazione e I/O.

Il rapporto tra queste risorse è determinato in origine da un [carico di lavoro di benchmark OLTP (Online Transaction Processing)](sql-database-benchmark-overview.md) progettato per essere tipico di carichi di lavoro OLTP reali. Quando il carico di lavoro supera la quantità di queste risorse, la velocità effettiva è limitata, ottenendo prestazioni più lente e timeout.

Le risorse usate dal carico di lavoro non influiscano sulle risorse disponibili per altri database SQL nel cloud di Azure. Analogamente, le risorse usate da altri carichi di lavoro non influiscano sulle risorse disponibili per il database SQL.

![rettangolo di selezione](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU sono particolarmente utili per comprendere le risorse relative allocate per i database SQL di Azure con diverse dimensioni di calcolo e livelli di servizio. Ad esempio:

- Raddoppiare il DTU aumentando le dimensioni di calcolo di un database equivale a raddoppiare il set di risorse disponibili per il database.
- Un database P11 con livello di servizio Premium con 1750 DTU offre 350x più capacità di calcolo DTU rispetto a un database del livello di servizio Basic con 5 DTU.  

Per ottenere informazioni più approfondite sull'utilizzo delle risorse (DTU) del carico di lavoro, usare le [informazioni dettagliate sulle prestazioni delle query](sql-database-query-performance.md) per:

- Identificare le prime query per CPU/durata/conteggio delle esecuzioni che possono essere potenzialmente ottimizzate per migliorare le prestazioni. Ad esempio, una query con utilizzo intensivo di I/O può trarre vantaggio dalle [tecniche di ottimizzazione in memoria](sql-database-in-memory.md) per sfruttare al meglio la memoria disponibile a un livello di servizio e a una dimensione di calcolo specifici.
- Eseguire il drill-down nei dettagli di una query per visualizzarne il testo e la cronologia di utilizzo delle risorse.
- Suggerimenti per l'ottimizzazione delle prestazioni che mostrano le azioni eseguite da [SQL Advisor per database](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unità di transazione di database elastico (edtu)

Per i database SQL sempre disponibili, invece di fornire un set dedicato di risorse (DTU) che potrebbe non essere sempre necessario, è possibile inserire questi database in un [pool elastico](sql-database-elastic-pool.md). I database in un pool elastico si trovano in un singolo server di database SQL di Azure e condividono un pool di risorse.

Le risorse condivise in un pool elastico sono misurate dalle unità di transazione di database elastico (edtu). I pool elastici offrono una soluzione semplice e conveniente per gestire gli obiettivi di prestazioni per più database con modelli di utilizzo molto variabili e imprevedibili. Un pool elastico garantisce che tutte le risorse non possano essere utilizzate da un database nel pool, garantendo al tempo stesso che ogni database nel pool disponga sempre di una quantità minima di risorse necessarie.

A un pool viene assegnato un numero definito di eDTU per un prezzo prestabilito. Nel pool elastico i singoli database possono essere ridimensionati automaticamente entro i limiti configurati. Un database con carico più pesante utilizzerà più edtu per soddisfare la domanda. I database con carichi più leggeri utilizzeranno un minor numero di edtu. I database senza alcun carico di lavoro non utilizzeranno eDTU. Poiché viene eseguito il provisioning delle risorse per l'intero pool, anziché per database, i pool elastici semplificano le attività di gestione e forniscono un budget prevedibile per il pool.

È possibile aggiungere altre edtu a un pool esistente senza tempi di inattività del database e senza alcun effetto sui database nel pool. Analogamente, se non sono più necessarie edtu aggiuntive, rimuoverle da un pool esistente in qualsiasi momento. È inoltre possibile aggiungere o sottrarre i database da un pool in qualsiasi momento. Per riservare edtu per altri database, limitare il numero di edtu che un database può usare con un carico elevato. Se un database utilizza in modo coerente le risorse, spostarlo al di fuori del pool e configurarlo come database singolo con una quantità prevedibile di risorse obbligatorie.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinare il numero di DTU necessarie per un carico di lavoro

Se si vuole eseguire la migrazione di un carico di lavoro locale o SQL Server macchina virtuale esistente al database SQL di Azure, usare il [calcolatore DTU](https://dtucalculator.azurewebsites.net/) per approssimare il numero di DTU necessari. Per un carico di lavoro del database SQL di Azure esistente, usare [informazioni dettagliate sulle prestazioni delle query](sql-database-query-performance.md) per comprendere l'utilizzo delle risorse del database (DTU) e ottenere informazioni più dettagliate per l'ottimizzazione del carico di lavoro. La vista a gestione dinamica (DMV) [sys. dm _db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) consente di visualizzare l'utilizzo delle risorse nell'ultima ora. La vista del catalogo [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) Visualizza l'utilizzo delle risorse negli ultimi 14 giorni, ma con una fedeltà inferiore di medie di cinque minuti.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Carichi di lavoro che traggono vantaggio da un pool elastico di risorse

I pool sono particolarmente adatti per i database con una media di utilizzo delle risorse bassa e picchi di utilizzo relativamente poco frequenti. Il database SQL valuta automaticamente l'utilizzo delle risorse cronologiche dei database in un server di database SQL esistente e consiglia la configurazione del pool appropriata nel portale di Azure. Per ulteriori informazioni, vedere [quando considerare un pool elastico del database SQL](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>È necessario portare offline l'applicazione per eseguire la conversione da un livello di servizio basato su DTU a un livello di servizio basato su vCore?

No. Non è necessario portare l'applicazione offline. I nuovi livelli di servizio offrono un semplice metodo di conversione online simile al processo esistente di aggiornamento dei database dal livello di servizio standard a quello Premium e viceversa. È possibile avviare questa conversione usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>È possibile convertire un database da un livello di servizio nel modello di acquisto basato su vCore a un livello di servizio nel modello di acquisto basato su DTU?

Sì, è possibile convertire facilmente il database in qualsiasi obiettivo di prestazioni supportato usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul modello di acquisto basato su vCore, vedere [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- Per altre informazioni sul modello di acquisto basato su DTU, vedere [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md).
