---
title: Modelli di acquisto
description: Informazioni sui modelli di acquisto disponibili per il database SQL di Azure.Learn about the purchasing models that are available for Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255990"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Scegliere tra i modelli di acquisto vCore e DTU

Il database SQL di Azure consente di acquistare facilmente una piattaforma completamente gestita come motore di database (PaaS) che soddisfa le esigenze di prestazioni e costi. A seconda del modello di distribuzione scelto per il database SQL di Azure, è possibile selezionare il modello di acquisto adatto alle attività:

- Modello di [acquisto basato su core virtuale (vCore)](sql-database-service-tiers-vcore.md) (consigliato). Questo modello di acquisto consente di scegliere tra un livello di calcolo di cui è stato eseguito il provisioning e un livello di calcolo senza server. Con il livello di calcolo di cui è stato eseguito il provisioning, è possibile scegliere la quantità esatta di risorse di calcolo di cui viene sempre eseguito il provisioning per il carico di lavoro. Con il livello di calcolo senza server, si specifica la scalabilità automatica delle risorse di calcolo su un intervallo di calcolo configurabile. Con questo livello di calcolo, è anche possibile sospendere e riprendere automaticamente il database in base all'attività del carico di lavoro. Il prezzo unitario vCore per unità di tempo è inferiore nel livello di calcolo di cui è stato eseguito il provisioning rispetto al livello di calcolo senza server.
- Modello di acquisto basato su unità di [transazione di database (DTU).](sql-database-service-tiers-dtu.md) Questo modello di acquisto fornisce pacchetti di elaborazione e archiviazione aggregati bilanciati per carichi di lavoro comuni.

Sono disponibili modelli di acquisto diversi per i diversi modelli di distribuzione del database SQL di Azure:Different purchasing models are available for different Azure SQL Database deployment models:

- Le opzioni di distribuzione [database singolo](sql-database-single-databases-manage.md) e [pool elastico](sql-database-elastic-pool.md) nel [database SQL di Azure](sql-database-technical-overview.md) offrono sia il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) sia il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- L'opzione di distribuzione [dell'istanza gestita](sql-database-managed-instance.md) nel database SQL di Azure offre solo il modello di [acquisto basato su vCore.](sql-database-service-tiers-vcore.md)
- Il [livello di servizio Hyperscale](sql-database-service-tier-hyperscale.md) è disponibile per i singoli database che utilizzano il modello di acquisto [basato su vCore.](sql-database-service-tiers-vcore.md)

La tabella e il grafico seguenti confrontano e mettono a confronto i modelli di acquisto basati su vCore e DTU:

|**Modello di acquisto**|**Descrizione**|**Ideale per**|
|---|---|---|
|Modello basato su DTU|Questo modello si basa su una misura integrata di risorse di calcolo, archiviazione e I/O.This model is based on a bundled measure of compute, storage, and I/O resources. Le dimensioni di calcolo sono espresse nelle DPU per singoli database e nelle unità di transazione del database elastico (eDTUs) per i pool elastici. Per ulteriori informazioni sulle DCU e sulle eDTUs, vedere [Che cosa sono le DCU e le eDTUS?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Ideale per i clienti che desiderano opzioni di risorse semplici e preconfigurate.|
|Modello basato su vCore|Questo modello consente di scegliere in modo indipendente le risorse di calcolo e archiviazione. Il modello di acquisto basato su vCore offre inoltre la possibilità di usare [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) per un risparmio in termini di costi.|Ideale per i clienti che danno valore alla trasparenza, al controllo e alla flessibilità.|
||||  

![confronto dei modelli di determinazione dei prezzi](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Costi di calcolo

### <a name="provisioned-compute-costs"></a>Costi di calcolo di provisioning

Nel livello di calcolo di cui è stato eseguito il provisioning, il costo di calcolo riflette la capacità di calcolo totale di cui viene eseguito il provisioning per l'applicazione.

Nel livello di servizio Business Critical vengono allocate automaticamente almeno 3 repliche. Per riflettere questa allocazione aggiuntiva delle risorse di calcolo, il prezzo nel modello di acquisto basato su vCore è circa 2,7 volte superiore nel livello di servizio Business Critical rispetto al livello di servizio Responsabile generale. Analogamente, il prezzo di archiviazione più elevato per GB nel livello di servizio Business Critical riflette i limiti di I/O più elevati e la latenza inferiore dello spazio di archiviazione SSD.

Il costo dell'archiviazione di backup è lo stesso per il livello di servizio Business Critical e il livello di servizio General Purpose perché entrambi i livelli utilizzano l'archiviazione standard per i backup.

### <a name="serverless-compute-costs"></a>Costi di calcolo senza server

Per una descrizione della modalità di definizione della capacità di calcolo e il calcolo dei costi per il livello di calcolo senza server, vedere [SQL Database serverless](sql-database-serverless.md).

## <a name="storage-costs"></a>Costi di archiviazione

I vari tipi di archiviazione vengono fatturati in modo diverso. Per l'archiviazione dei dati, viene addebitato l'archiviazione di cui è stato eseguito il provisioning in base alle dimensioni massime del database o del pool selezionato. Il costo non cambia a meno che non si riduce o aumenta tale valore massimo. Le risorse di archiviazione dei backup sono associate ai backup automatizzati dell'istanza e vengono allocate in modo dinamico. L'aumento del periodo di conservazione dei backup aumenta lo spazio di archiviazione dei backup utilizzato dall'istanza.

Per impostazione predefinita, 7 giorni di backup automatici dei database vengono copiati in un account di archiviazione BLOB di archiviazione con ridondanza geografica di accesso in lettura (RA-GRS). Questo archivio viene utilizzato dai backup completi settimanali, dai backup differenziali giornalieri e dai backup del log delle transazioni, che vengono copiati ogni 5 minuti. La dimensione dei registri delle transazioni dipende dalla frequenza di modifica del database. Una quantità minima di spazio di archiviazione pari al 100% delle dimensioni del database viene fornita senza costi aggiuntivi. Se si utilizza una quantità maggiore di risorse di archiviazione per i backup, viene applicato un addebito in base ai GB utilizzati in più ogni mese.

Per altre informazioni sui prezzi delle risorse di archiviazione, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

Un core virtuale (vCore) rappresenta una CPU logica e offre la possibilità di scegliere tra generazioni di hardware e le caratteristiche fisiche dell'hardware (ad esempio, il numero di core, la memoria e la dimensione di archiviazione). Il modello di acquisto basato su vCore offre flessibilità, controllo, trasparenza del consumo di risorse individuali e un modo semplice per convertire i requisiti del carico di lavoro locale nel cloud. Questo modello consente di scegliere risorse di elaborazione, memoria e archiviazione in base alle esigenze del carico di lavoro.

Nel modello di acquisto basato su vCore è possibile scegliere tra i livelli di servizio [General Purpose](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e [Business Critical](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) per singoli [database,](sql-database-single-database-scale.md) [pool elastici](sql-database-elastic-pool.md)e [istanze gestite](sql-database-managed-instance.md). Per i singoli database, è anche possibile scegliere il livello di [servizio Hyperscale](sql-database-service-tier-hyperscale.md).

Il modello di acquisto basato su vCore consente di scegliere in modo indipendente le risorse di elaborazione e archiviazione, abbinare le prestazioni locali e ottimizzare il prezzo. Nel modello di acquisto basato su vCore, paghi per:

- Risorse di calcolo (il livello di servizio, ovvero il numero di vCore e la quantità di memoria, ovvero la generazione di hardware).
- Il tipo e la quantità di dati e di archiviazione del log.
- Archiviazione di backup (RA-GRS).

> [!IMPORTANT]
> Le risorse di calcolo, l'I/O e l'archiviazione dei dati e dei log vengono addebitati per ogni database o pool elastico. L'archiviazione dei backup viene addebitata per ogni database. Per altre informazioni sui costi delle istanze gestite, vedere le [istanze gestite](sql-database-managed-instance.md).
> **Limitazioni della regione:** Per l'elenco corrente delle aree supportate, vedere [i prodotti disponibili per regione](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Per creare un'istanza gestita in un'area attualmente non supportata, inviare una richiesta di [supporto tramite il portale](quota-increase-request.md)di Azure.To create a managed instance in a region that currently isn't supported, send a support request via the Azure portal .

Se il singolo database o pool elastico utilizza più di 300 DTU, la conversione al modello di acquisto basato su vCore potrebbe ridurre i costi. È possibile eseguire la conversione usando l'API scelta o usando il portale di Azure, senza tempi di inattività. Tuttavia, la conversione non è necessaria e non viene eseguita automaticamente. Se il modello di acquisto basato su DTU soddisfa i requisiti aziendali e di prestazioni, è consigliabile continuare a usarlo.

Per convertire dal modello di acquisto basato su DTU al modello di acquisto basato su vCore, selezionare la dimensione di calcolo utilizzando le regole empiriche seguenti:To convert from the DTU-based purchasing model to the vCore-based purchasing model, select the compute size by using the following rules of thumb:

- Ogni 100 DCU nel livello standard richiedono almeno 1 vCore nel livello di servizio General Purpose.
- Ogni 125 DCU nel livello Premium richiedono almeno 1 vCore nel livello di servizio Business Critical.

> [!NOTE]
> Le linee guida per il dimensionamento da DTU a vCore sono approssimative e vengono fornite per facilitare la stima iniziale dell'obiettivo del servizio di database di destinazione. La configurazione ottimale del database di destinazione dipende dal carico di lavoro. 
> 
> Il raggiungimento del rapporto prezzo/prestazioni ottimale può richiedere l'utilizzo della flessibilità del modello vCore per regolare il numero di vCore, la [generazione dell'hardware,](sql-database-service-tiers-vcore.md#hardware-generations)i livelli di [servizio](sql-database-service-tiers-vcore.md#service-tiers) e [di calcolo,](sql-database-service-tiers-vcore.md#compute-tiers) nonché l'ottimizzazione di altri parametri di configurazione del database, ad esempio il [massimo grado di parallelismo](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).

## <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

Un'unità di transazione di database (DTU) rappresenta una misura mista di CPU, memoria, letture e scritture. Il modello di acquisto basato su DTU offre un set di bundle preconfigurati di risorse di calcolo e archiviazione per diversi livelli di prestazioni dell'applicazione. Se si preferisce la semplicità di un pacchetto preconfigurato e pagamenti fissi ogni mese, il modello basato su DTU potrebbe essere più adatto alle proprie esigenze.

Nel modello di acquisto basato su DTU è possibile scegliere tra i livelli di servizio di base, standard e premium sia per i [singoli database](sql-database-single-database-scale.md) che per i [pool elastici.](sql-database-elastic-pool.md) Il modello di acquisto basato su DTU non è disponibile per [le istanze gestite.](sql-database-managed-instance.md)

### <a name="database-transaction-units-dtus"></a>Unità di transazione di database (DTU)

Per un singolo database con una dimensione di calcolo specifica all'interno di un livello di [servizio,](sql-database-single-database-scale.md)Microsoft garantisce un determinato livello di risorse per tale database (indipendentemente da qualsiasi altro database nel cloud di Azure). Questa garanzia fornisce un livello di prestazioni prevedibile. La quantità di risorse allocate per un database viene calcolata come numero di DCU ed è una misura integrata di risorse di calcolo, archiviazione e I/O.

Il rapporto tra queste risorse è originariamente determinato da un carico di lavoro di benchmark di elaborazione delle [transazioni online (OLTP)](sql-database-benchmark-overview.md) progettato per essere tipico dei carichi di lavoro OLTP reali. Quando il carico di lavoro supera la quantità di queste risorse, la velocità effettiva viene limitata, con conseguente riduzione delle prestazioni e dei timeout.

Le risorse usate dal carico di lavoro non influiscono sulle risorse disponibili per altri database SQL nel cloud di Azure.The resources used by your workload don't impact the resources available to other SQL databases in the Azure cloud. Analogamente, le risorse usate da altri carichi di lavoro non influiscono sulle risorse disponibili per il database SQL.

![rettangolo di selezione](./media/sql-database-what-is-a-dtu/bounding-box.png)

Le DCU sono particolarmente utili per comprendere le risorse relative allocate per i database SQL di Azure con dimensioni di calcolo e livelli di servizio diversi. Ad esempio:

- Raddoppiare le DTU aumentando le dimensioni di calcolo di un database equivale a raddoppiare il set di risorse disponibili per tale database.
- Un database P11 del livello di servizio premium con 1750 DTU offre una potenza di calcolo DTU 350 volte superiore rispetto a un database del livello di servizio di base con 5 DTU.  

Per ottenere informazioni più approfondite sull'utilizzo delle risorse (DTU) del carico di lavoro, usare le informazioni dettagliate sulle prestazioni delle query per:To gain deeper insight into the resource (DTU) consumption of your workload, use [query-performance insights](sql-database-query-performance.md) to:

- Identificare le query principali per CPU/durata/conteggio delle esecuzioni che possono essere potenzialmente sintonizzate per migliorare le prestazioni. Ad esempio, una query che richiede un utilizzo intensivo di I/O potrebbe trarre vantaggio dalle tecniche di [ottimizzazione in memoria](sql-database-in-memory.md) per sfruttare al meglio la memoria disponibile in un determinato livello di servizio e le dimensioni di calcolo.
- Eseguire il drill-down dei dettagli di una query per visualizzarne il testo e la cronologia dell'utilizzo delle risorse.
- Accedere ai suggerimenti per l'ottimizzazione delle prestazioni che mostrano le azioni eseguite da [SQL Database Advisor.](sql-database-advisor.md)

### <a name="elastic-database-transaction-units-edtus"></a>Unità di transazione del database elastico (eDTUs)

Per i database SQL sempre disponibili, anziché fornire un set dedicato di risorse (DTU) che potrebbero non essere sempre necessarie, è possibile inserire questi database in un [pool elastico.](sql-database-elastic-pool.md) I database in un pool elastico si trovano in un singolo server di database SQL di Azure e condividono un pool di risorse.

Le risorse condivise in un pool elastico vengono misurate in base alle unità di transazione del database elastico (eDTUS). I pool elastici forniscono una soluzione semplice ed economica per gestire gli obiettivi di prestazioni per più database con modelli di utilizzo molto diversi e imprevedibili. Un pool elastico garantisce che tutte le risorse non possono essere utilizzate da un database nel pool, garantendo al contempo che ogni database nel pool disponga sempre di una quantità minima di risorse necessarie.

A un pool viene assegnato un numero definito di eDTU per un prezzo prestabilito. Nel pool elastico, i singoli database possono eseguire la scalabilità automatica entro i limiti configurati. Un database con un carico maggiore utilizzerà più eDTUs per soddisfare la domanda. I database con carichi più leggeri consumano meno eDTUs. I database senza alcun carico di lavoro non utilizzeranno eDTU. Poiché viene eseguito il provisioning delle risorse per l'intero pool, anziché per ogni database, i pool elastici semplificano le attività di gestione e forniscono un budget prevedibile per il pool.

È possibile aggiungere ulteriori eDTU a un pool esistente senza tempi di inattività del database e senza alcun impatto sui database nel pool. Analogamente, se non sono più necessarie eDTU aggiuntive, rimuoverle da un pool esistente in qualsiasi momento. È inoltre possibile aggiungere o sottrarre database da un pool in qualsiasi momento. Per riservare eDTUs per altri database, limitare il numero di eDT che un database può utilizzare con un carico elevato. Se un database utilizza le risorse in modo coerente, spostarlo all'esterno del pool e configurarlo come un singolo database con una quantità prevedibile di risorse necessarie.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinare il numero di DTU necessarie per un carico di lavoro

Se si vuole eseguire la migrazione di un carico di lavoro di una macchina virtuale locale o di SQL Server esistente al database SQL di Azure, usare la [calcolatrice DTU](https://dtucalculator.azurewebsites.net/) per approssimare il numero di DTU necessarie. Per un carico di lavoro del database SQL di Azure esistente, usare [le informazioni dettagliate](sql-database-query-performance.md) sulle prestazioni delle query per comprendere il consumo di risorse del database e ottenere informazioni più approfondite per l'ottimizzazione del carico di lavoro. La vista a gestione dinamica (DMV) [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) consente di visualizzare il consumo di risorse per l'ultima ora. La visualizzazione del catalogo [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) visualizza il consumo di risorse per gli ultimi 14 giorni, ma con una fedeltà inferiore di medie di cinque minuti.

### <a name="determine-dtu-utilization"></a>Determinare l'utilizzo di DTU

Per determinare la percentuale media di utilizzo di DTU/eDTU rispetto al limite DTU/eDTU di un database o di un pool elastico, utilizzare la formula seguente:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

I valori di input per questa formula possono essere ottenuti da [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)e [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV. In altre parole, per determinare la percentuale di utilizzo di DTU/eDTU verso il limite DTU/eDTU `avg_cpu_percent`di `avg_data_io_percent`un `avg_log_write_percent` database o di un pool elastico, selezionare il valore percentuale più grande tra i seguenti: , e in un determinato momento.

> [!NOTE]
> Il limite DTU di un database è determinato dalla CPU, dalle letture, dalle scritture e dalla memoria disponibile per il database. Tuttavia, poiché il motore di database di SQL Server utilizza `avg_memory_usage_percent` in genere tutta la memoria disponibile per la cache di dati per migliorare le prestazioni, il valore sarà in genere vicino al 100% indipendentemente dal carico corrente del database. Pertanto, anche se la memoria influenza indirettamente il limite DTU, non viene utilizzata nella formula di utilizzo DTU.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Carichi di lavoro che traggono vantaggio da un pool elastico di risorse

I pool sono adatti per i database con una media di utilizzo delle risorse bassa e picchi di utilizzo relativamente poco frequenti. Per ulteriori informazioni, vedere [Quando si dovrebbe prendere in considerazione un pool elastico del database SQL?](sql-database-elastic-pool.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Generazioni di hardware nel modello di acquisto basato su DTU

Nel modello di acquisto basato su DTU, i clienti non possono scegliere la generazione di hardware utilizzata per i propri database. Mentre un determinato database in genere rimane su una generazione di hardware specifica per un lungo periodo di tempo (comunemente per più mesi), esistono alcuni eventi che possono causare un database da spostare a un'altra generazione di hardware.

Ad esempio, un database può essere spostato a una generazione di hardware diversa se viene aumentato o ridimensionato a un obiettivo di servizio diverso o se l'infrastruttura corrente in un data center si sta avvicinando ai limiti di capacità o se l'hardware attualmente utilizzato viene dismesso a causa della sua fine di vita.

Se un database viene spostato in hardware diverso, le prestazioni del carico di lavoro possono cambiare. Il modello DTU garantisce che la velocità effettiva e il tempo di risposta del carico di lavoro di [benchmark DTU](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) rimarranno sostanzialmente identici man mano che il database passa a una generazione hardware diversa, purché l'obiettivo del servizio (il numero di DTU) rimanga invariato. 

Tuttavia, nell'ampio spettro dei carichi di lavoro dei clienti in esecuzione nel database SQL di Azure, l'impatto dell'uso di hardware diverso per lo stesso obiettivo di servizio può essere più pronunciato. Carichi di lavoro diversi trarranno vantaggio da diverse funzionalità e configurazione hardware. Pertanto, per i carichi di lavoro diversi dal benchmark DTU, è possibile individuare le differenze di prestazioni se il database si sposta da una generazione di hardware a un'altra.

Ad esempio, un'applicazione sensibile alla latenza di rete può ottenere prestazioni migliori sull'hardware Gen5 rispetto a Gen4 a causa dell'utilizzo di Accelerated Networking in Gen5, ma un'applicazione che usa l'I/O di lettura intensiva può ottenere prestazioni migliori sull'hardware Gen4 rispetto a Gen5 memoria superiore per rapporto core su Gen4.

I clienti con carichi di lavoro sensibili alle modifiche hardware o i clienti che desiderano controllare la scelta della generazione dell'hardware per il database possono utilizzare il modello [vCore](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) per scegliere la generazione hardware preferita durante la creazione e il ridimensionamento del database. Nel modello vCore vengono documentati i limiti delle risorse di ogni obiettivo di servizio per ogni generazione di hardware, sia per i [singoli database](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) che per i [pool elastici.](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) Per ulteriori informazioni sulle generazioni di hardware nel modello vCore, vedere [Generazioni hardware](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>È necessario portare l'applicazione offline per eseguire la conversione da un livello di servizio basato su DTU a un livello di servizio basato su vCore?

No. Non è necessario disconnettere l'applicazione. I nuovi livelli di servizio offrono un semplice metodo di conversione online simile al processo esistente di aggiornamento dei database dallo standard al livello di servizio Premium e viceversa. È possibile avviare questa conversione usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>È possibile convertire un database da un livello di servizio nel modello di acquisto basato su vCore a un livello di servizio nel modello di acquisto basato su DTU?

Sì, è possibile convertire facilmente il database in qualsiasi obiettivo di prestazioni supportato usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sul modello di acquisto basato su vCore, vedere modello di [acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- Per ulteriori informazioni sul modello di acquisto basato su DTU, vedere Modello di [acquisto basato su DTU](sql-database-service-tiers-dtu.md).
