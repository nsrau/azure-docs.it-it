---
title: Modelli di acquisto del database SQL di Azure | Microsoft Docs
description: Informazioni sui modelli di acquisto che sono disponibili per il Database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431376"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Scegliere tra i vCore e DTU modelli di acquisto

Database SQL di Azure consente di acquistare con facilità una piattaforma completamente gestita come un motore di database di servizio (PaaS) adatta alle proprie esigenze di prestazioni e costi. A seconda del modello di distribuzione che scelto per il Database SQL di Azure, è possibile selezionare il modello di acquisto che adatta a te:

- [Memoria centrale virtuale (vCore)-modello di acquisto basato su](sql-database-service-tiers-vcore.md) (scelta consigliata). Questo modello di acquisto consente di scegliere tra un livello di calcolo sottoposte a provisioning e un livello di calcolo senza server (anteprima). Con il livello di calcolo sottoposte a provisioning, scelto la quantità esatta di risorse di calcolo che viene sempre effettuato il provisioning per il carico di lavoro. Con il livello di calcolo senza server, si specifica la scalabilità automatica delle risorse di calcolo in un intervallo di calcolo può essere configurato. Con questo livello di calcolo, è automaticamente anche possibile sospendere e riprendere il database in base all'attività del carico di lavoro. Il prezzo unitario di vCore per ogni unità di tempo è inferiore rispetto al livello di calcolo senza server nel livello di calcolo sottoposte a provisioning.
- [Unità di transazione database (DTU)-modello di acquisto basato su](sql-database-service-tiers-dtu.md). Questo modello di acquisto fornisce i pacchetti di calcolo e archiviazione in dotazione bilanciati per carichi di lavoro comuni.

Diversi modelli di acquisto per sono disponibili diversi modelli di distribuzione di Database SQL di Azure:

- Le opzioni di distribuzione [database singolo](sql-database-single-databases-manage.md) e [pool elastico](sql-database-elastic-pool.md) nel [database SQL di Azure](sql-database-technical-overview.md) offrono sia il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) sia il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- Il [istanza gestita](sql-database-managed-instance.md) opzione di distribuzione nel Database SQL di Azure offre solo le [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- Il [livello di servizio su scala molto vasta](sql-database-service-tier-hyperscale.md) è disponibile per i database singoli che usano il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

La tabella e il grafico seguente confrontare e contrapporre le basato su vCore e i modelli di acquisto basato su DTU:

|**Modello di acquisto**|**Descrizione**|**Ideale per**|
|---|---|---|
|Modello basato su DTU|Questo modello è basato su una misura combinata di risorse di calcolo, archiviazione e i/o. Le dimensioni di calcolo sono espresse in Dtu per database singoli e in unità di transazione di database elastico (Edtu) per i pool elastici. Per altre informazioni sulle Dtu ed Edtu, vedere [quali sono Dtu ed Edtu?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Ideale per i clienti che desiderano opzioni di risorse semplici e preconfigurate.|
|Modello basato su vCore|Questo modello consente di scegliere in modo indipendente le risorse di calcolo e archiviazione. Il modello di acquisto basato su vCore offre inoltre la possibilità di usare il [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) per ottenere un risparmio sui costi.|Ideale per i clienti che danno valore alla trasparenza, al controllo e alla flessibilità.|
||||  

![confronto tra i modelli di prezzi](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Costi di calcolo

### <a name="provisioned-compute-costs"></a>Costi di calcolo sottoposte a provisioning

Nel livello di calcolo sottoposte a provisioning, il costo di calcolo riflette la capacità di calcolo totale che è stato effettuato il provisioning per l'applicazione.

Nel livello di servizio Business Critical vengono allocate automaticamente almeno tre repliche. Per riflettere questa allocazione aggiuntiva di risorse di calcolo, il prezzo nel modello di acquisto basato su vCore è circa 2,7 volte superiore al livello di servizio critico aziendale è nel livello di servizio utilizzo generico. Analogamente, il prezzo dell'archiviazione successiva per GB al livello di servizio critico aziendale riflette il / o elevato e bassa latenza dell'archiviazione unità SSD.

Il costo di archiviazione di backup è lo stesso per il livello di servizio critici di business e il livello di servizio utilizzo generico perché entrambi i livelli di usano l'archiviazione standard.

### <a name="serverless-compute-costs"></a>Costi di calcolo senza server

Per una descrizione del modo in cui è definita la capacità di calcolo e i costi vengono calcolati per il livello di calcolo senza server, vedere [Database SQL senza server (anteprima)](sql-database-serverless.md).

## <a name="storage-costs"></a>Costi di archiviazione

I vari tipi di archiviazione vengono fatturati in modo diverso. Per l'archiviazione dei dati, ti viene addebitata per l'archiviazione con provisioning in base alla dimensione massima di database o del pool selezionato. Il costo non cambia a meno che non venga ridotto o aumentato il valore massimo. Le risorse di archiviazione dei backup sono associate ai backup automatizzati dell'istanza e vengono allocate in modo dinamico. Se si aumenta il periodo di conservazione backup aumentano l'archivio di backup utilizzato dall'istanza di.

Per impostazione predefinita, 7 giorni di backup automatizzati dei tuoi database vengono copiati in un account di archiviazione Blob standard archiviazione con ridondanza geografica e accesso in lettura (RA-GRS). Questo archivio viene usato da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni, che vengono copiati ogni 5 minuti. Le dimensioni del log delle transazioni dipendono dalla frequenza di modifica del database. Una quantità di spazio di archiviazione minimo pari al 100% delle dimensioni del database viene fornita senza alcun costo aggiuntivo. Se si utilizza una quantità maggiore di risorse di archiviazione per i backup, viene applicato un addebito in base ai GB utilizzati in più ogni mese.

Per altre informazioni sui prezzi delle risorse di archiviazione, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

Una memoria centrale virtuale (vCore) rappresenta una CPU logica e offre la possibilità di scegliere tra generazioni di hardware e le caratteristiche fisiche dell'hardware (ad esempio, il numero di core, la memoria e le dimensioni di archiviazione). Il modello di acquisto basato su vCore offre grande flessibilità, controllo, trasparenza nell'utilizzo individuale delle risorse e un metodo diretto per tradurre in locale i requisiti di carico di lavoro nel cloud. Questo modello consente di scegliere le risorse di calcolo, memoria e archiviazione in base alle esigenze del carico di lavoro.

Nel modello di acquisto basato su vCore, è possibile scegliere tra i [generico](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e [aziendale critica](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) per livelli di servizio [database singoli](sql-database-single-database-scale.md), [ i pool elastici](sql-database-elastic-pool.md), e [istanze gestite](sql-database-managed-instance.md). Per i singoli database, è anche possibile scegliere il [livello di servizio su scala molto vasta](sql-database-service-tier-hyperscale.md).

Il modello di acquisto basato su vCore consente di scegliere le risorse di calcolo e archiviazione in modo indipendente, corrispondono prestazioni locali e ottimizzare i costi. Nel modello di acquisto basato su vCore, si paga per:

- Calcolo delle risorse (il livello di servizio + il numero di Vcore e la quantità di memoria e la generazione di hardware).
- Il tipo e la quantità di spazio di archiviazione di dati e di log.
- Archiviazione dei backup (RA-GRS).

> [!IMPORTANT]
> Le risorse di calcolo, i/o e archiviazione dati e di log vengono addebitate per ogni database o del pool elastico. Archiviazione dei backup sarà addebitato in base a ogni database. Per altre informazioni sui costi delle istanze gestite, vedere le [istanze gestite](sql-database-managed-instance.md).
> **Limitazioni di area:** per l'elenco aggiornato delle aree supportate, vedere i [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Per creare un'istanza gestita in un'area che attualmente non è supportata [invia una richiesta di supporto tramite il portale di Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Se il database singolo o pool elastico utilizza più di 300 Dtu, la conversione in modello di acquisto basato su vCore potrebbe ridurre i costi. È possibile convertire usando l'API preferita o tramite il portale di Azure, senza tempi di inattività. Tuttavia, la conversione non è obbligatorio e non viene eseguita automaticamente. Se il modello di acquisto basato su DTU soddisfa i requisiti aziendali e di prestazioni, è consigliabile continuare a usarlo.

Per convertire dal modello di acquisto basato su DTU per il modello di acquisto basato su vCore, selezionare le dimensioni di calcolo utilizzando le seguenti regole generali:

- Ogni 100 Dtu nel livello standard è necessario almeno 1 vCore nel livello di servizio utilizzo generico.
- Ogni 125 Dtu nel livello premium è necessario almeno 1 vCore nel livello di servizio critici di business.

## <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

Un'unità di transazione database (DTU) rappresenta una misura combinata di CPU, memoria, letture e scritture. Il modello di acquisto basato su DTU offre un set di bundle preconfigurati di risorse di calcolo e archiviazione per diversi livelli di prestazioni dell'applicazione. Se si preferiscono la semplicità di un bundle preconfigurato e pagamenti fissi ogni mese, il modello basato su DTU sia più adatto alle proprie esigenze.

Nel modello di acquisto basato su DTU, è possibile scegliere tra basic, standard e livelli di servizio premium per entrambi [database singoli](sql-database-single-database-scale.md) e [i pool elastici](sql-database-elastic-pool.md). Non è disponibile per il modello di acquisto basato su DTU [istanze gestite](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unità di transazione di database (DTU)

Per un singolo database a uno specifico di calcolo delle dimensioni all'interno di un [livello di servizio](sql-database-single-database-scale.md), Microsoft garantisce un certo livello di risorse per il database (indipendente da qualsiasi altro database nel cloud di Azure). Questa garanzia fornisce un livello di prestazioni prevedibile. La quantità di risorse allocate per un database viene calcolata come numero di Dtu ed è una misura combinata di risorse di calcolo, archiviazione e i/o.

Il rapporto tra queste risorse viene originariamente stabilito da un [carico di lavoro di elaborazione delle transazioni online (OLTP) benchmark](sql-database-benchmark-overview.md) progettato per essere tipico di carichi di lavoro OLTP reali. Quando il carico di lavoro supera la quantità di una di queste risorse, la velocità effettiva è limitata, causando i timeout e prestazioni più lente.

Le risorse usate dal carico di lavoro non incidono sulle risorse disponibili ad altri database SQL nel cloud di Azure. Analogamente, le risorse usate da altri carichi di lavoro non incidono sulle risorse disponibili per il database SQL.

![rettangolo di selezione](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu sono particolarmente utili per comprendere il relative risorse allocate per i database SQL di Azure con livelli di servizio e le dimensioni di calcolo diversi. Ad esempio:

- Raddoppiare le Dtu aumentando le dimensioni di calcolo di un database equivale a raddoppiare il set di risorse disponibili per quel database.
- Un database premium del servizio livello P11 con 1750 Dtu fornisce 350 volte maggiore DTU la potenza di un database di livello di servizio basic con 5 Dtu di calcolo.  

Per ottenere informazioni più approfondite il consumo di risorse (DTU) del carico di lavoro, usare [informazioni dettagliate prestazioni query](sql-database-query-performance.md) per:

- Identificare le prime query per CPU/durata/conteggio delle esecuzioni che può essere potenzialmente ottimizzata per migliorare le prestazioni. Ad esempio, una query dei / O intensivo può risultare vantaggioso [tecniche di ottimizzazione in memoria](sql-database-in-memory.md) per usare al meglio la memoria disponibile in un determinato livello di servizio e le dimensioni di calcolo.
- Eseguire il drill-nei dettagli di una query per visualizzare il testo e la relativa cronologia di utilizzo delle risorse.
- Accedere ai consigli di ottimizzazione delle prestazioni che descrivono le azioni eseguite dalla [Advisor per Database SQL](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unità di transazione di database elastico (Edtu)

Per i database SQL sono sempre disponibili, invece di fornire un set dedicato di risorse (Dtu) che potrebbero non sempre è necessario, è possibile inserire questi database in un' [pool elastico](sql-database-elastic-pool.md). I database in un pool elastico si trovano in un singolo server di Database SQL di Azure e condividono un pool di risorse.

Le risorse condivise in un pool elastico sono misurate dalle unità di transazione database elastico (Edtu). I pool elastici offrono una soluzione semplice e conveniente per gestire gli obiettivi di prestazioni per più database con i modelli di utilizzo estremamente variabili e imprevedibili. Un pool elastico garantisce che tutte le risorse non possono essere utilizzate da un database nel pool, assicurando che ogni database nel pool abbia sempre una quantità minima di risorse necessari disponibili.

A un pool viene assegnato un numero definito di eDTU per un prezzo prestabilito. Nel pool elastico, i singoli database possono scalabilità automatica all'interno di limiti configurati. Database con un carico pesante utilizzerà più Edtu per soddisfare la richiesta. I database con carichi leggeri utilizzeranno un minor numero di Edtu. I database senza alcun carico di lavoro non utilizzeranno eDTU. Poiché vengono effettuato il provisioning delle risorse per l'intero pool, anziché per ogni database, pool elastici semplificano le attività di gestione e forniscono un budget prevedibile per il pool.

È possibile aggiungere altre Edtu a un pool esistente senza tempi di inattività del database e senza alcun impatto sui database nel pool. Analogamente, se non è più necessario Edtu aggiuntivi, rimuoverli da un pool esistente in qualsiasi momento. È anche possibile aggiungere database a o sottrarre database da un pool in qualsiasi momento. Per riservare Edtu per altri database, limitare il numero di Edtu di che un database può utilizzare con un carico pesante. Se un database underuses in modo coerente le risorse, spostarlo fuori dal pool e configurarlo come database singolo con una certa quantità di risorse necessarie.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinare il numero di DTU necessarie per un carico di lavoro

Se si vuole eseguire la migrazione di un oggetto esistente in locale o carico di lavoro di macchina virtuale SQL Server al Database SQL di Azure, usare il [calcolo di DTU](https://dtucalculator.azurewebsites.net/) per simulare il numero di Dtu necessario. Per un carico di lavoro di Database SQL di Azure esistente, usare [informazioni dettagliate prestazioni query](sql-database-query-performance.md) per comprendere il consumo di risorse del database (Dtu) e ottenere informazioni più dettagliate per l'ottimizzazione del carico di lavoro. Il [DB resource_stats sys](https://msdn.microsoft.com/library/dn800981.aspx) vista a gestione dinamica (DMV) consente di visualizzare il consumo di risorse per l'ultima ora. Il [Sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) vista del catalogo Visualizza il consumo di risorse per gli ultimi 14 giorni, ma a una fedeltà inferiore di medie di cinque minuti.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Carichi di lavoro che traggono vantaggio da un pool elastico di risorse

I pool sono adatti per i database con una media di sottoutilizzo di risorse e i picchi di utilizzo relativamente poco frequenti. Database SQL automaticamente valuta l'utilizzo delle risorse cronologico dei database in un server di Database SQL esistente e consiglia una configurazione appropriata del pool nel portale di Azure. Per altre informazioni, vedere [quando è opportuno considerare un pool elastico del Database SQL?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>È necessario portare offline l'applicazione per convertire da un livello di servizio basato su DTU a un livello di servizio basato su vCore?

No. Non è necessario portare offline l'applicazione. Nuovi livelli di servizio offrono un metodo semplice di conversione online simile al processo esistente di aggiornamento dei database da standard a livello di servizio premium e viceversa. È possibile avviare questa conversione tramite il portale di Azure, PowerShell, la CLI di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>È possibile convertire un database da un livello di servizio nel modello di acquisto basato su vCore a un livello di servizio nel modello di acquisto basato su DTU?

Sì, è possibile convertire facilmente il database a qualsiasi obiettivo di prestazioni supportato tramite il portale di Azure, PowerShell, la CLI di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul modello di acquisto basato su vCore, vedere [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- Per altre informazioni sul modello di acquisto basato su DTU, vedere [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md).
