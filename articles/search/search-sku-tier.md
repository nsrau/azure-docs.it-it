---
title: Scegliere un piano tariffario o SKU per il servizio Ricerca di Azure | Microsoft Docs
description: 'È possibile effettuare il provisioning di Ricerca di Azure per questi SKU: Gratuito, Basic e Standard; il piano Standard è disponibile in più configurazioni di risorse e livelli di capacità.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: heidist
ms.openlocfilehash: f7cf471a69395cef0aef7d5dd2e3c77218bf97a3
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715281"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Scegliere un piano tariffario per Ricerca di Azure

In Ricerca di Azure, il [provisioning del servizio](search-create-service-portal.md) viene effettuato a livello di piano tariffario o SKU specifico. Le opzioni disponibili includono **Gratuito**, **Basic** o **Standard**. Il piano **Standard** è disponibile in più configurazioni e capacità. 

Lo scopo di questo articolo è aiutare a scegliere un piano tariffario. Offre la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/search/) e la pagina dei [limiti del servizio](search-limits-quotas-capacity.md) con un riepilogo dei concetti di fatturazione e dei modelli di consumo associati ai diversi piani. Viene anche raccomandato un approccio iterativo per individuare il piano che meglio si adatta alle esigenze specifiche. 

I piani determinano la capacità, non le funzionalità. Se la capacità del piano si rivela insufficiente, sarà necessario effettuare il provisioning di un nuovo servizio a un piano superiore e quindi [ricaricare gli indici](search-howto-reindex.md). Non è disponibile alcun aggiornamento sul posto dello stesso servizio da uno SKU a un altro.

Le funzionalità disponibili non sono il primo aspetto da considerare per la scelta del piano. Tutti i livelli, incluso il livello **gratuito**, offrono parità delle funzionalità, con l'eccezione del supporto dell'indicizzatore per S3HD. Tuttavia, l'indicizzazione e i vincoli delle risorse possono limitare l'estensione dell'utilizzo delle funzionalità. Ad esempio, l'indicizzazione della [ricerca cognitiva](cognitive-search-concept-intro.md) ha competenze a esecuzione prolungata che scadono in un servizio gratuito a meno che il set di dati non sia di dimensioni ridotte.

> [!TIP]
> La maggior parte dei clienti inizia con il livello **gratuito** per una valutazione e quindi passa al livello **Standard** per lo sviluppo. Dopo aver scelto un piano e aver effettuato [il provisioning di un servizio di ricerca](search-create-service-portal.md), è possibile [aumentare il numero di partizioni e repliche](search-capacity-planning.md) per l'ottimizzazione delle prestazioni. Per altre informazioni su quando e perché modificare la capacità, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione](search-performance-optimization.md).
>

## <a name="billing-concepts"></a>Concetti di fatturazione

Per la selezione del piano è necessario tenere presente concetti come le definizioni delle capacità, i limiti del servizio e le unità del servizio. 

### <a name="capacity"></a>Capacity

La capacità è strutturata in *repliche* e *partizioni*. 

+ Le repliche sono istanze del servizio di ricerca, in cui ogni replica ospita una copia con carico bilanciato di un indice. Ad esempio, un servizio con 6 repliche ha 6 copie di ogni indice caricato nel servizio. 

+ Le partizioni archiviano gli indici e suddividono automaticamente i dati ricercabili: due partizioni suddividono l'indice in due parti, tre partizioni in tre parti e così via. In termini di capacità la *dimensione della partizione* è la principale caratteristica di differenziazione tra i livelli.

> [!NOTE]
> Tutti i livelli **Standard** supportano [combinazioni flessibili di repliche e partizioni](search-capacity-planning.md#chart) che consentono di [definire il sistema in termini di velocità o archiviazione](search-performance-optimization.md) modificando il bilanciamento. Il livello **Basic** offre tre repliche per una disponibilità elevata ma include un'unica partizione. I livelli **gratuiti** non offrono risorse dedicate: le risorse di calcolo sono condivise da più servizi gratuiti.

### <a name="search-units"></a>Unità di ricerca

Il concetto di fatturazione più importante è l'*unità di ricerca* (SU, Search Unit) che è l'unità di fatturazione per Ricerca di Azure. Poiché il funzionamento di Ricerca di Azure è basato sia sulle repliche e che sulle partizioni, la fatturazione non può essere eseguita per partizione o replica. Al contrario, la fatturazione si basa su una combinazione di entrambi gli elementi. Nelle formule un'unità di ricerca (SU) è il prodotto delle repliche e delle partizioni usate da un servizio: (R X P = SU). Ogni servizio viene avviato con almeno un'unità di ricerca (una replica moltiplicata per una partizione). Un modello più realistico potrebbe essere quello di un servizio a 3 repliche e 3 partizioni fatturato come 9 unità di ricerca. 

Sebbene ogni livello tenda progressivamente a una capacità più elevata, è possibile portare online una parte della capacità totale e mantenere la parte rimanente come riserva. In termini di fatturazione, l'effettivo importo del pagamento corrisponde al numero di partizioni e repliche portate online, calcolato usando la formula delle unità di ricerca.

La tariffa di fatturazione è oraria per ogni unità di ricerca e diversa per ogni livello. Le tariffe per ogni livello sono disponibili in [Prezzi di Ricerca](https://azure.microsoft.com/pricing/details/search/).

### <a name="limits"></a>Limiti

I servizi ospitano le risorse, ad esempio indici, indicizzatori e così via. Ogni livello impone [limiti del servizio](search-limits-quotas-capacity.md) sulla quantità di risorse che è possibile creare. Di conseguenza, un limite al numero di indici e altri oggetti è la seconda caratteristica di differenziazione tra i livelli. Quando si esamina ogni opzione nel portale, tenere presente i limiti sul numero di indici. Altre risorse, come gli indicizzatori, le origini dati e i set di competenze, sono sottoposte ai limiti degli indici.

## <a name="consumption-patterns"></a>Modelli di consumo

La maggior parte dei clienti inizia con il servizio **gratuito** che mantiene per un periodo illimitato e quindi sceglie uno dei livelli **Standard** per i carichi di lavoro di sviluppo o produzione. 

![Livelli di Ricerca di Azure](./media/search-sku-tier/tiers.png "Piani tariffari di Ricerca di Azure")

Il livello minimo **Basic** e il livello massimo **S3 HD** sono disponibili per modelli di consumo importanti ma atipici. Il livello **Basic** è per i carichi di lavoro di produzione limitati: offre contratto di servizio, risorse dedicate, disponibilità elevata, ma archiviazione limitata di un massimo di 2 GB. Questo livello è stato progettato per i clienti che usano in genere una capacità minore di quella disponibile. Il livello massimo **S3 HD** per i carichi di lavoro tipici di ISV, partner, [soluzioni multi-tenant](search-modeling-multitenant-saas-applications.md) o qualsiasi configurazione che richiede un numero elevato di indici di piccole dimensioni. Il livello più adatto tra **Basic** o **S3 HD** è spesso evidente. Tuttavia, se si vuole una conferma è possibile inviare un post a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) o [contattare il supporto di Azure](https://azure.microsoft.com/support/options/) per assistenza.

Tra i livelli standard più usati, **S1-S3** sono una serie crescente di livelli di capacità dove i punti di inflessione sono le dimensioni della partizione e i valori massimi del numero di indici, indicizzatori e risorse accessorie:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| dimensioni della partizione|  25 GB | 100 GB | 250 GB |  |  |  |  |
| limiti di indici e indicizzatori| 50 | 200 | 200 |  |  |  |  |

**S1** è una scelta comune nei casi in cui risorse dedicate e più partizioni diventano una necessità. Con partizioni di 25 GB per un massimo di 12 partizioni, il limite per servizio nel livello **S1** è di 300 GB in totale se vengono aumentate le partizioni rispetto alle repliche (per composizioni più bilanciate, vedere [Allocare partizioni e repliche](search-capacity-planning.md#chart)).

Le pagine del portale e dei prezzi mettono in evidenza le dimensioni delle partizioni e l'archiviazione, ma per ogni livello tutte le capacità di calcolo (capacità del disco, velocità, CPU) aumentano in modo lineare con il prezzo. Una replica **S2** è più veloce di **S1**, mentre **S3** è più veloce di **S2**. I livelli **S3** interrompono il modello di calcolo-prezzo generalmente lineare con un I/O sproporzionatamente più veloce. Se si prevede che l'I/O rappresenti un collo di bottiglia, un livello **S3** offre un numero maggiore di operazioni di I/O al secondo rispetto ai livelli inferiori.

**S3** e **S3 HD** hanno la stessa infrastruttura di capacità elevata, ma ognuno dei livelli raggiunge il limite massimo in modi diversi. **S3** è destinato a un numero minore di indici di grandi dimensioni. Di conseguenza, il limite massimo è associato alle risorse (2,4 TB per ogni servizio). **S3 HD** è destinato a un numero elevato di indici di dimensioni molto ridotte. A 1000 indici, **S3 HD** raggiunge il limite in termini di vincoli di indice. Se l'utente è un cliente **S3 HD** che richiede più di 1000 indici, può contattare il supporto tecnico Microsoft per informazioni su come procedere.

> [!NOTE]
> I limiti dei documenti considerati in precedenza non sono più applicabili per la maggior parte dei servizi di Ricerca di Azure di cui è stato eseguito il provisioning dopo il gennaio 2018. Per altre informazioni sulle situazioni in cui i limiti dei documenti sono ancora applicati, vedere [Limiti dei servizi: Limiti per i documenti](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Valutare la capacità

La capacità e i costi dell'esecuzione del servizio sono in stretta associazione. Poiché i livelli impongono limiti su due aspetti (archiviazione e risorse), è necessario considerarli entrambi poiché il primo limite raggiunto rappresenta il limite effettivo. 

In genere i requisiti aziendali definiscono il numero di indici necessario. Ad esempio, un indice globale per un repository di documenti di grandi dimensioni o più indici in base all'area, all'applicazione o alla nicchia di mercato.

Per determinare le dimensioni di un indice, è necessario [crearne uno](search-create-index-portal.md). La struttura dei dati in Ricerca di Azure è fondamentalmente un [indice invertito](https://en.wikipedia.org/wiki/Inverted_index) con caratteristiche diverse rispetto ai dati di origine. In un indice invertito le dimensioni e la complessità dipendono dal contenuto, non necessariamente dalla quantità di dati inseriti. Un'origine dati di grandi dimensioni con una ridondanza massiccia può generare un indice più piccolo rispetto a un set di dati di dimensioni minori che include contenuto altamente variabile.  Per questa ragione raramente è possibile dedurre le dimensioni dell'indice in base alle dimensioni del set di dati originale.

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Passaggio 1: Preparare delle stime approssimative tramite il livello gratuito

Un approccio per la stima della capacità consiste nell'iniziare con il livello **gratuito**. Tenere presente che il servizio **gratuito** offre un massimo di 3 indici, 50 MB di archiviazione e 2 minuti di indicizzazione. Sebbene la stima delle dimensioni previste per un indice con questi vincoli può risultare complessa, l'esempio seguente descrive un possibile approccio:

+ [Creare un servizio gratuito](search-create-service-portal.md)
+ Preparare un set di dati rappresentativo di dimensioni ridotte (in questo caso 5.000 documenti e dimensioni di esempio del 10%)
+ [Generare un indice iniziale](search-create-index-portal.md) e prendere nota delle dimensioni nel portale (in questo caso 30 MB)

Presupponendo che l'esempio sia rappresentativo e corrisponda al 10% dell'intera origine dati, un indice di 30 MB diventa un indice di circa 300 MB se vengono indicizzati tutti i documenti. Con questo numero preliminare, è possibile raddoppiare la quantità da destinare a due indici (sviluppo e produzione) per un totale di 600 MB nei requisiti di indicizzazione. Poiché i requisiti possono essere facilmente soddisfatti dal livello **Basic**, iniziare con questo livello.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Passaggio 2: Preparare delle stime affinate tramite un livello fatturabile

Alcuni clienti preferiscono iniziare con risorse dedicate adatte a tempi di campionamento ed elaborazione maggiori e quindi sviluppare stime realistiche della quantità di indici, delle dimensioni e dei volumi di query durante lo sviluppo. Inizialmente, il provisioning di un servizio viene eseguito in base alla stima migliore quindi, man mano che il progetto di sviluppo avanza, i team sono in grado di determinare se la capacità del servizio esistente è superiore o inferiore a quella necessaria per i carichi di lavoro di produzione previsti. 

1. [Rivedere i limiti di servizio di ogni livello](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity#index-limits) per determinare se livelli inferiori possono supportare la quantità di indici necessaria. Nei livelli **Basic**-**S1**- **S2** i limiti degli indici sono rispettivamente 15-50-200.

1. [Creare un servizio a un livello fatturabile](search-create-service-portal.md):

    + Iniziare con un livello basso, **Basic** o **S1**, se ci si trova all'inizio della propria curva di apprendimento.
    + Iniziare con un livello elevato, **S2** o **S3**, se indicizzazione e carichi di query su ampia scala sono evidenti.

1. [Generare un indice iniziale](search-create-index-portal.md) per determinare il modo in cui i dati di origine vengono convertiti in un indice. Questo è l'unico modo per stimare le dimensioni di un indice.

1. [Monitorare l'archiviazione, i limiti del servizio, il volume di query e la latenza](search-monitor-usage.md) nel portale. Il portale visualizza le query al secondo, le query limitate e la latenza della ricerca che consentono di determinare se è in uso il livello più adatto. Oltre alle metriche del portale, è possibile configurare un monitoraggio approfondito, ad esempio un'analisi clickthrough, abilitando l'[analisi del traffico di ricerca](search-traffic-analytics.md). 

Il numero di indici e le dimensioni sono ugualmente rilevanti per l'analisi poiché i limiti massimi vengono raggiunti tramite l'utilizzo totale dell'archiviazione (partizioni) o i limiti massimi nelle risorse (indici, indicizzatori e così via), a seconda del limite raggiunto per primo. Il portale consente di tenere traccia di entrambi gli aspetti visualizzando l'utilizzo corrente accanto ai limiti massimi nella pagina Panoramica.

> [!NOTE]
> I requisiti di archiviazione possono apparire maggiori del necessario se i documenti contengono dati estranei. In teoria, i documenti contengono solo i dati necessari per l'esperienza di ricerca. I dati binari non sono ricercabili e devono essere archiviati separatamente, ad esempio in una tabella o un archivio BLOB di Azure, con un campo nell'indice che contenga un riferimento URL ai dati esterni. Le dimensioni massime di un singolo documento sono pari a 16 MB o meno, se si caricano più documenti in una richiesta. Per altre informazioni, vedere [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md).
>

**Considerazioni sul volume delle query**

Le query al secondo (QPS, Queries-Per-Second) sono una metrica rilevante per l'ottimizzazione delle prestazioni ma non lo sono per la scelta del livello, a meno che non si preveda un volume di query molto elevato sin dall'inizio.

Tutti i livelli standard sono in grado di offrire un equilibrio tra repliche e partizioni supportando un turnaround delle query più rapido attraverso repliche aggiuntive per il bilanciamento del carico e partizioni aggiuntive per l'elaborazione parallela. È possibile ottimizzare le prestazioni dopo il provisioning del servizio.

I clienti che prevedono volumi di query elevati sin dall'inizio possono scegliere livelli più elevati supportati da hardware più potenti. In seguito, se i volumi di query sono inferiori al previsto, sarà possibile portare offline le partizioni e le repliche o passare a un servizio di livello inferiore. Per altre informazioni su come calcolare la velocità effettiva delle query, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca di Azure](search-performance-optimization.md).


**Contratti di servizio**

I [contratti di servizio](https://azure.microsoft.com/support/legal/sla/search/v1_0/) non includono il livello **gratuito** e le funzionalità di anteprima. Per tutti i livelli fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. Per il Contratto di servizio di query (lettura) sono necessarie due o più repliche. Per il contratto di servizio di query e indicizzazione (lettura-scrittura) sono necessarie tre o più repliche. Il numero di partizioni non è un fattore di cui tiene conto il Contratto di servizio. 

## <a name="tips-for-tier-evaluation"></a>Suggerimenti per la valutazione del livello

+ Informazioni su come creare indici efficienti e sulle metodologie di aggiornamento a minor impatto. È consigliabile eseguire un'[analisi del traffico di ricerca](search-traffic-analytics.md) per i dati acquisiti relativi all'attività di query.

+ Basare le metriche sulle query, raccogliere i dati in base ai modelli di utilizzo (query durante l'orario di ufficio, indicizzazione negli orari di scarso traffico) e utilizzare questi dati per le decisioni relative al provisioning futuro dei servizi. Sebbene non pratico a un livello orario o giornaliero, è possibile adattare dinamicamente le partizioni e le risorse per rispondere alle variazioni previste nei volumi di query o a variazioni impreviste ma sostenute se i livelli durano a sufficienza per consentire l'esecuzione di un'azione.

+ Tenere presente che l'unico aspetto negativo di un provisioning non sufficiente potrebbe essere la necessità di chiudere un servizio se i requisiti correnti sono maggiori di quelli stimati. Per evitare l'interruzione del servizio, è possibile creare un nuovo servizio nella stessa sottoscrizione a un livello superiore e scegliere un'esecuzione side-by-side fino a quando tutte le app e le richieste non sono indirizzate al nuovo endpoint.

## <a name="next-steps"></a>Passaggi successivi

Iniziare con un livello **gratuito** e generare un indice iniziale usando un subset dei dati per comprenderne le caratteristiche. La struttura dei dati in Ricerca di Azure è un indice invertito le cui dimensioni e complessità dipendono dal contenuto. Tenere presente che un indice molto ridondante tende a generare un indice più piccolo rispetto a un contenuto molto irregolare. Per questa ragione, i requisiti di archiviazione dell'indice sono determinati più dalle caratteristiche del contenuto che non dalla dimensione del set di dati.

Quando si ha un'idea iniziale delle dimensioni dell'indice, [eseguire il provisioning di un servizio fatturabile](search-create-service-portal.md) in uno dei livelli descritti in questo articolo, **Basic** o **Standard**. Ridurre eventuali vincoli artificiali dei subset di dati e [rigenerare l'indice](search-howto-reindex.md) per includere tutti i dati in cui si vuole effettivamente effettuare le ricerche.

[Allocare le partizioni e le repliche](search-capacity-planning.md) in base alle esigenze per ottenere le prestazioni e la scala desiderate.

Se le prestazioni e la capacità risultano adatte, l'operazione è stata completata. In caso contrario, ricreare un servizio di ricerca in un altro livello più adatto alle proprie esigenze.

> [!NOTE]
> Per altre domande, inviare un post a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) o [contattare il supporto di Azure](https://azure.microsoft.com/support/options/).