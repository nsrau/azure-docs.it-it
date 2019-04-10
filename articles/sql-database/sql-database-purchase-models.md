---
title: Modelli di acquisto del database SQL di Azure | Microsoft Docs
description: Informazioni sui modelli di acquisto disponibili per i database nel servizio database SQL di Azure.
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
ms.date: 02/08/2019
ms.openlocfilehash: 46a620900896d07273da22e53171330b85d3f1ec
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360183"
---
# <a name="azure-sql-database-purchasing-models"></a>Modelli di acquisto del database SQL di Azure

Database SQL di Azure consente di acquistare con facilità un motore di database PaaS completamente gestito che si adatta alle proprie esigenze di prestazioni e costi. A seconda del modello di distribuzione del Database SQL di Azure, è possibile selezionare il modello di acquisto adatto alle proprie esigenze:

- [Modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md) (scelta consigliata) che consente di scegliere la quantità esatta di capacità di archiviazione e di calcolo necessaria per il carico di lavoro.
- [Modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) in cui è possibile scegliere pacchetti di calcolo e archiviazione in bundle bilanciati per i carichi di lavoro comuni.

Nei modelli di distribuzione del database SQL di Azure sono disponibili modelli di acquisto diversi:

- Le opzioni di distribuzione [database singolo](sql-database-single-databases-manage.md) e [pool elastico](sql-database-elastic-pool.md) nel [database SQL di Azure](sql-database-technical-overview.md) offrono sia il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) sia il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
- L'opzione di distribuzione [istanza gestita](sql-database-managed-instance.md) nel database SQL di Azure offre solo il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

> [!IMPORTANT]
> Il [livello di servizio con iperscalabilità (anteprima)](sql-database-service-tier-hyperscale.md) è disponibile solo nell'anteprima pubblica per database singoli che usano il modello di acquisto vCore.

La tabella e il grafico seguenti mettono a confronto questi due modelli.

|**Modello di acquisto**|**DESCRIZIONE**|**Ideale per**|
|---|---|---|
|Modello basato su DTU|Questo modello è basato su una misura combinata di risorse di calcolo, archiviazione e I/O. Le dimensioni di calcolo per i database singoli sono espresse in unità di transazione di database (DTU), quelle per i pool elastici sono espresse in unità di transazione di database elastico (eDTU). Per altre informazioni su DTU ed eDTU, vedere [Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)](sql-database-purchase-models.md#dtu-based-purchasing-model).|Ideale per i clienti che desiderano opzioni di risorse semplici e preconfigurate.|
|Modello basato su vCore|Questo modello consente di scegliere in modo indipendente le risorse di calcolo e archiviazione. Il modello di acquisto basato su vCore offre inoltre la possibilità di usare il [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) per ottenere un risparmio sui costi.|Ideale per i clienti che danno valore alla trasparenza, al controllo e alla flessibilità.|
||||  

![modello di prezzi](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Costi di calcolo

Il costo delle risorse di calcolo riflette la capacità di calcolo totale di cui è stato eseguito il provisioning per l'applicazione. Nel livello di servizio Business Critical vengono allocate automaticamente almeno tre repliche. Per riflettere questa allocazione aggiuntiva di risorse di calcolo, il prezzo nel modello di acquisto basato su vCore è circa 2,7 volte più alto nel livello di servizio Business Critical rispetto al livello di servizio di utilizzo generico. Per lo stesso motivo, il prezzo di archiviazione per GB più alto nel livello di servizio Business Critical riflette l'I/O elevato e la bassa latenza dell'archiviazione su unità SSD. Il costo di archiviazione per i backup è tuttavia lo stesso per i due livelli di servizio perché in entrambi i casi viene usata una classe di archiviazione standard.

## <a name="storage-costs"></a>Costi di archiviazione

I vari tipi di archiviazione vengono fatturati in modo diverso. Per l'archiviazione dei dati, i costi vengono addebitati per le risorse di archiviazione totali di cui è stato eseguito il provisioning in base alle dimensioni massime del database o del pool selezionato. Il costo non cambia, a meno che il valore delle dimensioni massime non venga ridotto o aumentato. Le risorse di archiviazione dei backup sono associate ai backup automatizzati dell'istanza e vengono allocate in modo dinamico. L'incremento del periodo di conservazione dei backup comporta l'aumento delle risorse di archiviazione dei backup utilizzate dall'istanza.

7 giorni di backup automatizzati dei database vengono copiati nell'archivio BLOB Standard con archiviazione con ridondanza geografica e accesso in lettura per impostazione predefinita. Le risorse di archiviazione vengono usate da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni copiati ogni 5 minuti. Le dimensioni del log delle transazioni dipendono dalla frequenza di modifica del database. Una quantità di risorse di archiviazione minima equivalente al 100% delle dimensioni del database viene fornita senza addebiti aggiuntivi. L'utilizzo aggiuntivo dell'archivio di backup verrà addebitato in base a GB/mese.

Per altre informazioni sui prezzi delle risorse di archiviazione, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto in base ai vCore

Una memoria centrale virtuale rappresenta la CPU logica offerta con la possibilità di scegliere tra generazioni di hardware e caratteristiche fisiche dell'hardware (ad esempio, numero di core, memoria, spazio di archiviazione). Il modello di acquisto basato su vCore offre flessibilità, controllo, trasparenza nell'uso individuale delle risorse e un metodo diretto per convertire i requisiti dei carichi di lavoro locali nel cloud. Questo modello consente di scegliere le risorse di calcolo, memoria e archiviazione in base ai requisiti dei carichi di lavoro. Nel modello di acquisto basato su vCore è possibile scegliere tra livelli di servizio [Utilizzo generico](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e [Business critical](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) per [database singoli](sql-database-single-database-scale.md), [pool elastici](sql-database-elastic-pool.md) e [istanze gestite](sql-database-managed-instance.md). Per i database singoli è anche possibile scegliere il [livello di servizio con iperscalabilità (anteprima)](sql-database-service-tier-hyperscale.md).

Il modello di acquisto basato su vCore consente di scegliere le risorse di calcolo e archiviazione in modo indipendente, soddisfare le esigenze di prestazioni locali e ottimizzare i costi. Nel modello di acquisto basato su vCore i clienti pagano per le risorse seguenti:

- Calcolo (livello di servizio + numero di vCore e quantità di memoria + generazione di hardware)
- Tipo e quantità di risorse di archiviazione per dati e log
- Risorse di archiviazione per i backup (RA-GRS)

> [!IMPORTANT]
> I costi delle risorse di calcolo, I/O e archiviazione di dati e log vengono addebitati per database singolo o pool elastico. Il costo delle risorse di archiviazione per i backup viene addebitato per ogni database. Per altre informazioni sui costi delle istanze gestite, vedere le [istanze gestite](sql-database-managed-instance.md).
> **Limitazioni di area:** per l'elenco aggiornato delle aree supportate, vedere i [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se si vuole creare un'istanza gestita in un'area attualmente non supportata, è possibile [inviare una richiesta di supporto tramite il portale di Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).
.

Se un database singolo o un pool elastico usa più di 300 DTU, la conversione in modello di acquisto basato su vCore può ridurre i costi. Se si sceglie questa opzione, è possibile usare l'API preferita o il portale di Azure, senza tempi di inattività. La conversione tuttavia non è obbligatoria e non viene eseguita automaticamente. Se il modello di acquisto basato su DTU soddisfa i requisiti aziendali e di prestazioni, è consigliabile continuare a usarlo. Se si decide di passare dal modello di acquisto basato su DTU al modello di acquisto basato su vCore, selezionare le dimensioni di calcolo usando le regole generali seguenti:

- Per ogni 100 DTU nel livello Standard è necessario almeno 1 vCore nel livello di utilizzo generico
- Per ogni 125 DTU nel livello Premium è necessario almeno 1 vCore nel livello Business Critical

## <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

La DTU (unità di transazione di database) rappresenta una misura combinata di CPU, memoria e operazioni di lettura e scrittura. Il modello di acquisto basato su DTU offre un set di bundle preconfigurati di risorse di calcolo e archiviazione per diversi livelli di prestazioni dell'applicazione. I clienti che preferiscono la semplicità di un bundle preconfigurato, pagando un importo fisso mensile, possono considerare il modello basato su DTU come più adatto alle proprie esigenze. In questo modello i clienti possono scegliere tra livelli di servizio **Basic**, **Standard** e **Premium** sia per [database singoli](sql-database-single-database-scale.md) sia per [pool elastici](sql-database-elastic-pool.md). Il modello d’acquisto non è disponibile nell'[istanza gestita](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unità di transazione di database (DTU)

Per un singolo database con dimensioni di calcolo specifiche all'interno di un [livello di servizio](sql-database-single-database-scale.md), Microsoft garantisce un certo livello di risorse per il database (a prescindere da qualsiasi altro database nel cloud di Azure), fornendo così un livello di prestazioni prevedibile. La quantità di risorse viene calcolata come numero di unità di transazione di database o DTU ed è una misura combinata delle risorse di calcolo, archiviazione e I/O. Il rapporto tra queste risorse è stato originariamente stabilito da un [carico di lavoro di benchmark OLTP](sql-database-benchmark-overview.md) progettato per essere rappresentativo dei carichi di lavoro OLTP reali. Quando il carico di lavoro supera la quantità di una di queste risorse, la velocità effettiva viene limitata, generando timeout e prestazioni più lente. Le risorse usate dal carico di lavoro non incidono sulle risorse disponibili per gli altri database SQL nel cloud di Azure e le risorse usate dagli altri carichi di lavoro non incidono sulle risorse disponibili per il database SQL.

![rettangolo di selezione](./media/sql-database-what-is-a-dtu/bounding-box.png)

Le DTU sono particolarmente utili per comprendere la quantità relativa di risorse tra i database SQL di Azure a diverse dimensioni di calcolo e livelli di servizio. Ad esempio, raddoppiare le DTU aumentando le dimensioni di calcolo di un database equivale a raddoppiare il set di risorse disponibili per quel database. Ad esempio, un database Premium P11 con 1750 DTU fornisce 350 volte più potenza di calcolo DTU di un database Basic con 5 DTU.  

Per ottenere maggiori dettagli sul consumo di risorse (DTU) del carico di lavoro, usare le [informazioni dettagliate sulle prestazioni delle query](sql-database-query-performance.md) per:

- Identificare le query principali a livello di CPU/durata/conteggio delle esecuzioni, che possono essere potenzialmente ottimizzate per migliorare le prestazioni. Una query con uso intensivo dell'I/O, ad esempio, può trarre vantaggio dall'uso di [tecniche di ottimizzazione in memoria](sql-database-in-memory.md) per usare in modo più efficiente la memoria disponibile per un livello di servizio e una dimensione di calcolo specifici.
- Eseguire il drill-down dei dettagli di una query, visualizzarne il testo e la cronologia di utilizzo delle risorse.
- Accedere alle raccomandazioni relative all'ottimizzazione delle prestazioni che descrivono le azioni eseguite da [Advisor per database SQL](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unità di transazione di database elastico (eDTU)

Anziché fornire un set di risorse (DTU) dedicato che potrebbe non essere sempre necessario per un database SQL sempre disponibile, è possibile inserire i database in un [pool elastico](sql-database-elastic-pool.md) in un server di database SQL che condivide un pool di risorse tra tali database. Le risorse condivise in un pool elastico sono misurate dalle unità di transazione di database elastiche o eDTU. I pool elastici offrono una soluzione semplice e conveniente per gestire gli obiettivi di prestazioni per più database con modelli di utilizzo estremamente mutevoli e imprevedibili. Un pool elastico garantisce che le risorse non possano essere utilizzate da un solo database nel pool, assicurando allo stesso tempo che per ogni database del pool sia sempre disponibile una quantità minima di risorse necessaria.

A un pool viene assegnato un numero definito di eDTU per un prezzo prestabilito. All'interno del pool elastico, i singoli database sono sufficientemente flessibili da assicurare la scalabilità automatica nell'ambito di limiti configurati. Un database con un carico di lavoro più importante utilizzerà più eDTU per soddisfare la domanda. I database con carichi più leggeri utilizzeranno meno eDTU. I database senza alcun carico di lavoro non utilizzeranno eDTU. Effettuando il provisioning delle risorse per l'intero pool e non per i singoli database, le attività di gestione si semplificano e si ha a disposizione un budget prevedibile per il pool.

È possibile aggiungere altre eDTU a un pool esistente senza causare tempi di inattività del database o effetti negativi sui database nel pool. Analogamente, se le eDTU aggiuntive non sono più necessarie, è possibile rimuoverle da un pool esistente in qualsiasi momento. È possibile aggiungere o sottrarre database al pool oppure limitare la quantità di eDTU che un database può usare in condizioni di carico elevato per riservare eDTU per altri database. Se si prevede un sottoutilizzo di risorse per un database, il database può essere rimosso dal pool e configurato come database singolo con una quantità prevedibile di risorse necessarie.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinare il numero di DTU necessarie per un carico di lavoro

Se si intende eseguire la migrazione di un carico di lavoro di macchine virtuali locali esistenti o di SQL Server a un database SQL di Azure, è possibile usare lo [strumento di calcolo DTU](https://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessarie. Per un carico di lavoro esistente del database SQL di Azure, è possibile usare le [informazioni dettagliate sulle prestazioni delle query](sql-database-query-performance.md) per conoscere il consumo di risorse di database (DTU) e ottenere dati più approfonditi per l'ottimizzazione del carico di lavoro. È anche possibile usare la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) per visualizzare il consumo di risorse per l'ultima ora. In alternativa, la vista del catalogo [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) visualizza il consumo di risorse per gli ultimi 14 giorni, ma a una fedeltà inferiore di medie di cinque minuti.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Carichi di lavoro che traggono vantaggio da un pool elastico di risorse

I pool sono adatti per un numero elevato di database con modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti. Database SQL valuta automaticamente la cronologia d’utilizzo delle risorse dei database in un server di database SQL esistente e consiglia una configurazione appropriata del pool nel portale di Azure. Per altre informazioni, vedere [Quando usare un pool elastico](sql-database-elastic-pool.md)

## <a name="purchase-model-frequently-asked-questions-faq"></a>Domande frequenti sui modelli di acquisto

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>È necessario portare offline l'applicazione per eseguire la conversione da un database basato su DTU a un livello di servizio basato su vCore?

I nuovi livelli di servizio offrono un semplice metodo di conversione online simile al processo esistente di aggiornamento dei database dal livello di servizio Standard a Premium e viceversa. Questa conversione può essere avviata tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-using-the-vcore-based-purchase-to-a-service-tier-using-the-dtu-based-purchasing-model"></a>È possibile convertire un database da un livello di servizio che usa il modello di acquisto basato su vCore a un livello di servizio che usa il modello di acquisto basato su DTU?

Sì, è possibile convertire facilmente il database a qualsiasi obiettivo di prestazioni supportato tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, T-SQL o l'API REST. Vedere [Gestire database singoli](sql-database-single-database-scale.md) e [Gestire pool elastici](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul modello di acquisto basato su vCore, vedere [Modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md)
- Per il modello di acquisto basato su DTU, vedere [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md).
