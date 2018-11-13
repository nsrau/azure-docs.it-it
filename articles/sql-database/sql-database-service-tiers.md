---
title: Modelli di acquisto del database SQL di Azure | Microsoft Docs
description: Informazioni sui modelli di acquisto disponibili per i database nel servizio database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: 102a00ac79c91ac4d571c0254570f14e8f2af46f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239084"
---
# <a name="azure-sql-database-purchasing-models"></a>Modelli di acquisto del database SQL di Azure

Database SQL di Azure consente di acquistare con facilità un motore di database PaaS completamente gestito che si adatta alle proprie esigenze di prestazioni e costi. A seconda del modello di distribuzione del Database SQL di Azure, è possibile selezionare il modello di acquisto adatto alle proprie esigenze:

- I [server logici](sql-database-logical-servers.md) nel [database SQL di Azure](sql-database-technical-overview.md) offrono due modelli di acquisto per le risorse di calcolo, archiviazione e I/O basati rispettivamente su [DTU](sql-database-service-tiers-dtu.md) e [vCore](sql-database-service-tiers-vcore.md). All'interno di questo modello di acquisto, è possibile scegliere [database singoli](sql-database-single-databases-manage.md) oppure [pool elastici](sql-database-elastic-pool.md).
- Le [istanze gestite](sql-database-managed-instance.md) nel database SQL di Azure offrono solo il [modello di acquisto basato sui vCore](sql-database-service-tiers-vcore.md).

> [!IMPORTANT]
> I [database con iperscalabilità (anteprima)](sql-database-service-tier-hyperscale.md) sono in anteprima pubblica solo per i database singoli che usano il modello di acquisto basato su vCore.

La tabella e il grafico seguenti mettono a confronto questi due modelli.

|**Modello di acquisto**|**Descrizione**|**Ideale per**|
|---|---|---|
|Modello basato su DTU|Questo modello è basato su una misura combinata di risorse di calcolo, archiviazione e I/O. Le dimensioni di calcolo per i database singoli sono espresse in unità di transazione di database (DTU), quelle per i pool elastici sono espresse in unità di transazione di database elastico (eDTU). Per altre informazioni su DTU ed eDTU, vedere [Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)](sql-database-service-tiers.md#dtu-based-purchasing-model).|Ideale per i clienti che desiderano opzioni di risorse semplici e preconfigurate.|
|Modello basato su vCore|Questo modello consente di scegliere in modo indipendente le risorse di calcolo e archiviazione. Offre inoltre la possibilità di usare il Vantaggio Azure Hybrid per SQL Server per ottenere un risparmio sui costi.|Ideale per i clienti che danno valore alla trasparenza, al controllo e alla flessibilità.|
||||  

![modello di prezzi](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>Modello di acquisto in base ai vCore

Una memoria centrale virtuale rappresenta la CPU logica offerta con la possibilità di scegliere tra generazioni di hardware e caratteristiche fisiche dell'hardware (ad esempio, numero di core, memoria, spazio di archiviazione). Il modello di acquisto basato su vCore offre flessibilità, controllo, trasparenza nell'utilizzo individuale delle risorse e un metodo diretto per convertire i requisiti dei carichi di lavoro locali nel cloud. Questo modello consente di scegliere le risorse di calcolo, memoria e archiviazione in base ai requisiti dei carichi di lavoro. Nel modello di acquisto basato su vCore è possibile scegliere tra livelli di servizio [Utilizzo generico](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e [Business critical](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) per [database singoli](sql-database-single-database-scale.md), [istanze gestite](sql-database-managed-instance.md) e [pool elastici](sql-database-elastic-pool.md). Per i database singoli è anche possibile scegliere il livello di servizio [Iperscalabilità (anteprima)](sql-database-service-tier-hyperscale.md).

Il modello di acquisto basato su vCore consente di scegliere le risorse di calcolo e archiviazione in modo indipendente, soddisfare le esigenze di prestazioni locali e ottimizzare i costi. Nel modello di acquisto basato su vCore i clienti pagano per le risorse seguenti:

- Calcolo (livello di servizio + numero di vCore e quantità di memoria + generazione di hardware)
- Tipo e quantità di risorse di archiviazione per dati e log
- Risorse di archiviazione per i backup (RA-GRS)

> [!IMPORTANT]
> I costi delle risorse di calcolo, I/O e archiviazione di dati e log vengono addebitati per database singolo o pool elastico. Il costo delle risorse di archiviazione per i backup viene addebitato per ogni database. Per informazioni dettagliate sull'addebito dei costi di Istanza gestita, vedere la sezione relativa all'[istanza gestita di database SQL di Azure](sql-database-managed-instance.md).
> **Limitazioni regionali:** il modello di acquisto basato su vCore non è disponibile nelle seguenti regioni: Europa occidentale, Francia centrale, Regno Unito meridionale, Regno Unito occidentale e Australia sud-orientale.

Se il database o il pool elastico utilizza più di 300 DTU, la conversione a vCore può consentire di ridurre i costi. È possibile eseguire questa conversione usando l'API preferita o il portale di Azure, senza tempi di inattività. La conversione non è tuttavia obbligatoria. Se il modello di acquisto basato su DTU soddisfa i requisiti aziendali e di prestazioni, è consigliabile continuare a usarlo. Se si decide di eseguire la conversione dal modello basato su DTU a quello basato su vCore, selezionare le dimensioni di calcolo usando la regola empirica seguente: è necessario almeno 1 vCore nel livello per utilizzo generico per ogni 100 DTU nel livello Standard; per ogni 125 DTU nel livello Premium è necessario almeno 1 vCore nel livello business critical.

## <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

La DTU (unità di transazione di database) rappresenta una misura combinata di CPU, memoria e operazioni di lettura e scrittura. Il modello di acquisto basato su DTU offre un set di bundle preconfigurati di risorse di calcolo e archiviazione per diversi livelli di prestazioni dell'applicazione. I clienti che preferiscono la semplicità di un bundle preconfigurato, pagando un importo fisso mensile, possono considerare il modello basato su DTU come più adatto alle proprie esigenze. In questo modello i clienti possono scegliere tra livelli di servizio **Basic**, **Standard** e **Premium** sia per [database singoli](sql-database-single-database-scale.md) sia per [pool elastici](sql-database-elastic-pool.md). Il modello d’acquisto non è disponibile nell'[istanza gestita](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unità di transazione di database (DTU)

Per un singolo database SQL di Azure con dimensioni di calcolo specifiche all'interno di un [livello di servizio](sql-database-single-database-scale.md), Microsoft garantisce un certo livello di risorse per il database (a prescindere da qualsiasi altro database nel cloud di Azure), fornendo così un livello di prestazioni prevedibile. La quantità di risorse viene calcolata come numero di unità di transazione di database o DTU ed è una misura combinata delle risorse di calcolo, archiviazione e I/O. Il rapporto tra queste risorse è stato originariamente stabilito da un [carico di lavoro di benchmark OLTP](sql-database-benchmark-overview.md) progettato per essere rappresentativo dei carichi di lavoro OLTP reali. Quando il carico di lavoro supera la quantità di una di queste risorse, la velocità effettiva viene limitata, generando timeout e prestazioni più lente. Le risorse usate dal carico di lavoro non incidono sulle risorse disponibili per gli altri database SQL nel cloud di Azure e le risorse usate dagli altri carichi di lavoro non incidono sulle risorse disponibili per il database SQL.

![rettangolo di selezione](./media/sql-database-what-is-a-dtu/bounding-box.png)

Le DTU sono particolarmente utili per comprendere la quantità relativa di risorse tra i database SQL di Azure a diverse dimensioni di calcolo e livelli di servizio. Ad esempio, raddoppiare le DTU aumentando le dimensioni di calcolo di un database equivale a raddoppiare il set di risorse disponibili per quel database. Ad esempio, un database Premium P11 con 1750 DTU fornisce 350 volte più potenza di calcolo DTU di un database Basic con 5 DTU.  

Per ottenere maggiori dettagli sul consumo di risorse (DTU) del carico di lavoro, usare [Query Performance Insight del database SQL di Azure](sql-database-query-performance.md) per:

- Identificare le query principali a livello di CPU/durata/conteggio delle esecuzioni, che possono essere potenzialmente ottimizzate per migliorare le prestazioni. Una query con uso intensivo dell'I/O, ad esempio, può trarre vantaggio dall'uso di [tecniche di ottimizzazione in memoria](sql-database-in-memory.md) per usare in modo più efficiente la memoria disponibile per un livello di servizio e una dimensione di calcolo specifici.
- Eseguire il drill-down dei dettagli di una query, visualizzarne il testo e la cronologia di utilizzo delle risorse.
- Accedere alle raccomandazioni relative all'ottimizzazione delle prestazioni che descrivono le azioni eseguite da [Advisor per database SQL](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unità di transazione di database elastico (eDTU)

Anziché fornire un set di risorse (DTU) dedicato che potrebbe non essere sempre necessario per un database SQL sempre disponibile, è possibile inserire i database in un [pool elastico](sql-database-elastic-pool.md) in un server di database SQL che condivide un pool di risorse tra tali database. Le risorse condivise in un pool elastico sono misurate dalle unità di transazione di database elastiche o eDTU. I pool elastici offrono una soluzione semplice e conveniente per gestire gli obiettivi di prestazioni per più database con modelli di utilizzo estremamente mutevoli e imprevedibili. Un pool elastico garantisce che le risorse non possano essere utilizzate da un solo database nel pool, assicurando allo stesso tempo che per ogni database del pool sia sempre disponibile una quantità minima di risorse necessaria.

A un pool viene assegnato un numero definito di eDTU per un prezzo prestabilito. All'interno del pool elastico, i singoli database sono sufficientemente flessibili da assicurare la scalabilità automatica nell'ambito di limiti configurati. Un database con un carico di lavoro più importante utilizzerà più eDTU per soddisfare la domanda. I database con carichi più leggeri utilizzeranno meno eDTU. I database senza alcun carico di lavoro non utilizzeranno eDTU. Effettuando il provisioning delle risorse per l'intero pool e non per i singoli database, le attività di gestione si semplificano e si ha a disposizione un budget prevedibile per il pool.

È possibile aggiungere altre eDTU a un pool esistente senza causare tempi di inattività del database o effetti negativi sui database nel pool. Analogamente, se le eDTU aggiuntive non sono più necessarie, è possibile rimuoverle da un pool esistente in qualsiasi momento. È possibile aggiungere o sottrarre database al pool oppure limitare la quantità di eDTU che un database può usare in condizioni di carico elevato per riservare eDTU per altri database. Se si prevede un sottoutilizzo di risorse per un database, il database può essere rimosso dal pool e configurato come database singolo con una quantità prevedibile di risorse necessarie.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinare il numero di DTU necessarie per un carico di lavoro

Se si intende eseguire la migrazione di un carico di lavoro locale o di un carico di lavoro di macchine virtuali di SQL Server a un database SQL di Azure, è possibile usare lo [strumento di calcolo DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessarie. Per un carico di lavoro esistente del database SQL di Azure, è possibile usare [Informazioni dettagliate prestazioni query del database SQL](sql-database-query-performance.md) per comprendere il consumo di risorse di database (DTU) e ottenere dati più approfonditi per l'ottimizzazione del carico di lavoro. È anche possibile usare la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) per visualizzare il consumo di risorse per l'ultima ora. In alternativa, la vista del catalogo [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) visualizza il consumo di risorse per gli ultimi 14 giorni, ma a una fedeltà inferiore di medie di cinque minuti.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Carichi di lavoro che traggono vantaggio da un pool elastico di risorse

I pool sono adatti per un numero elevato di database con modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti. Database SQL valuta automaticamente la cronologia d’utilizzo delle risorse dei database in un server di database SQL esistente e consiglia una configurazione appropriata del pool nel portale di Azure. Per altre informazioni, vedere [Quando usare un pool elastico](sql-database-elastic-pool.md)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul modello di acquisto basato su vCore, vedere [Modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md)
- Per il modello di acquisto basato su DTU, vedere [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md).
