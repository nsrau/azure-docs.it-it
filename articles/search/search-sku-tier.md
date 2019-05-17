---
title: Scegliere un piano tariffario o SKU per il servizio Ricerca di Azure - Ricerca di Azure
description: 'Ricerca di Azure può eseguire il provisioning in questi SKU: Gratuito, Basic e Standard e Standard è disponibile in diverse configurazioni di risorse e i livelli di capacità.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 00422209302bbcc2139be4f6b490f0bb2816c051
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539259"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Scegliere un piano tariffario per Ricerca di Azure

Quando si crea un servizio di ricerca di Azure, un [risorsa viene creata](search-create-service-portal.md) in un piano tariffario o SKU che è fissa per la durata del servizio. I livelli includono Free, Basic, Standard e ottimizzate per l'archiviazione. Standard e ottimizzate per l'archiviazione sono disponibili con diverse configurazioni e capacità.

La maggior parte dei clienti iniziano con il livello gratuito in modo che è possibile valutare il servizio. È quindi l'aggiornamento a uno dei livelli superiori per le distribuzioni di sviluppo e produzione. È possibile completare tutte le guide introduttive ed esercitazioni usando il livello gratuito, inclusi quelli per la ricerca cognitiva a elevato utilizzo di risorse.

> [!NOTE]
> Microsoft offre attualmente l'ottimizzazione dell'archiviazione i livelli di servizio in una versione di anteprima al prezzo scontato per i test e sperimentazione, allo scopo di raccogliere commenti e suggerimenti. I prezzi finali verranno annunciati in un secondo momento quando questi livelli sono disponibili a livello generale. È consigliabile evitare di utilizzare questi livelli per le applicazioni di produzione.

I livelli rispecchiano le caratteristiche dell'hardware che ospita il servizio (anziché le funzionalità) e si differenziano per:

+ Il numero di indici che è possibile creare.
+ Le dimensioni e velocità di partizioni (archiviazione fisica).

Anche se tutti i livelli, tra cui il livello gratuito, in genere offrono parità delle funzionalità, i carichi di lavoro più grande possibile dettare necessità di livelli superiori. Ad esempio, [indicizzazione di intelligenza artificiale con servizi cognitivi](cognitive-search-concept-intro.md) ha competenze con esecuzione prolungata che raggiungono timeout su un servizio gratuito, a meno che il set di dati è di piccole dimensioni.

> [!NOTE] 
> L'eccezione a parità di funzionalità [indicizzatori](search-indexer-overview.md), che non sono disponibili in S3 HD.
>

All'interno di un livello, è possibile [regolare le risorse di replica e partizione](search-capacity-planning.md) per aumentare o diminuire la scala. È possibile iniziare con uno o due di ognuna e quindi aumentare temporaneamente la potenza di elaborazione per un elevato carico di lavoro di indicizzazione. La possibilità di ottimizzare i livelli di risorse nell'ambito di un piano aumenta la flessibilità, ma tende anche a rendere leggermente più complessa l'analisi. Potrebbe essere necessario fare delle prove per verificare se un livello più basso con altre risorse/repliche offre migliori prestazioni rispetto a un livello superiore con meno risorse e valore. Per altre informazioni su quando e perché regolare la capacità, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Livelli per la ricerca di Azure

La tabella seguente elenca i piani disponibili. È possibile trovare ulteriori informazioni sui vari livelli nel [pagina dei prezzi](https://azure.microsoft.com/pricing/details/search/), nella [limiti dei servizi in ricerca di Azure](search-limits-quotas-capacity.md) articolo e nel portale di pagina quando si effettua il provisioning di un servizio.

|Livello | Capacity |
|-----|-------------|
|Gratuito | Condiviso con altri sottoscrittori. Non è scalabile. Limitato a tre indici e 50 MB di spazio di archiviazione. |
|Di base | Risorse di calcolo dedicate per carichi di lavoro di produzione su scala ridotta. Una partizione di 2 GB e un massimo di tre repliche. |
|Standard 1 (S1) | Per S1 e versioni successive, dedicato macchine con maggiore capacità di elaborazione e archiviazione a ogni livello. Per S1, dimensioni della partizione sono 25 GB per partizione (con un massimo di 300 GB per ogni servizio). |
|Standard 2 (S2) | Simile a S1, ma con partizioni di 100 GB (e un massimo di 1,2 TB per ogni servizio). |
|Standard 3 (S3) | Partizioni di 200 GB (con un massimo di 2,4 TB per ogni servizio). |
|Standard 3 ad alta densità (S3 HD) | Ad alta densità è un *modalità di hosting* per S3. L'hardware sottostante è ottimizzato per un numero elevato di indici più piccoli e per gli scenari multi-tenancy. S3 HD ha lo stesso addebito per ogni unità S3, ma l'hardware è ottimizzato per le letture di file veloce in un numero elevato di indici più piccoli.|
|Ottimizzazione dell'archiviazione 1 (L1) | Partizioni da 1 TB (con un massimo di 12 TB per ogni servizio). |
|2 (L2) ottimizzazione dell'archiviazione | Partizioni di 2 TB (con un massimo di 24 TB per ogni servizio). |

> [!NOTE] 
> I livelli di ottimizzazione dell'archiviazione offrono capacità di archiviazione superiore a un prezzo inferiore per ogni TB di livelli Standard. Il compromesso principale è maggiore latenza delle query, che è necessario convalidare per le proprie esigenze specifiche dell'applicazione.  Per altre informazioni sulle considerazioni relative alla prestazioni di questo livello, vedere [considerazioni sulle prestazioni e ottimizzazione](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Modalità di funzionamento della fatturazione

Esistono tre modi di sostenere i costi in ricerca di Azure e sono presenti componenti fissi e variabili. In questa sezione vengono descritti i tre componenti di fatturazione: i costi dei servizi, i costi di uscita dei dati e migliorato per intelligenza artificiale di indicizzazione di base.

### <a name="core-service-costs-fixed-and-variable"></a>Costi dei servizi principali (fisse e variabile)

Per il servizio stesso, l'addebito minimo è la prima unità di ricerca (partizione 1 replica x 1). Questo requisito minimo è fisso per la durata del servizio perché il servizio non è possibile eseguire su un valore minore di questa configurazione.

Oltre il valore minimo, è possibile aggiungere in modo indipendente le repliche e partizioni. Ad esempio, è possibile aggiungere solo le repliche o partizioni. Aumento incrementale capacità tramite le repliche e partizioni costituiscono il componente di costo variabile.

La fatturazione è basata su un [formula (repliche x partizioni x frequenza)](#search-units). Indica la tariffa addebitata varia in base al piano tariffario che scelto.

Nello screenshot seguente, il prezzo unitario è indicato per Free, Basic e S1. (S2, S3, L1 e L2 non vengono visualizzati.) Se si crea un servizio di base, del costo mensile verrà Media il valore visualizzato per *price-1*. Per un servizio Standard, il costo mensile verrà Media il valore visualizzato per *price-2*. Poiché il calcolo risparmio energia e capacità di archiviazione è maggiore di ogni livello consecutivi, per ogni livello di aumento dei costi di unità. Le tariffe per la ricerca di Azure sono disponibili nel [pagina dei prezzi di ricerca di Azure](https://azure.microsoft.com/pricing/details/search/).

![Prezzo unitario](./media/search-sku-tier/per-unit-pricing.png "prezzo unitario")

Quando si esegue la stima del costo di una soluzione di ricerca, tenere presente che prezzi e la capacità non lineare. (Raddoppiare la capacità raddoppia il costo.) Per un esempio di come dei lavori formule, vedere [modalità di allocazione di partizioni e repliche](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

#### <a name="billing-based-on-search-units"></a>Fatturazione in base a unità di ricerca

Il concetto di fatturazione più importante da comprendere per le operazioni di ricerca di Azure è il *unità di ricerca* (SU). Poiché Ricerca di Azure dipende sia dalle repliche che dalle partizioni per l'esecuzione di indicizzazione e repliche, la fatturazione non può essere eseguita solo in base all'uno o all'altro elemento. Al contrario, la fatturazione si basa su una combinazione di entrambi gli elementi.

Unità di streaming è il prodotto del *repliche* e *partizioni* utilizzato da un servizio: **(R x P = SU)**.

Ogni servizio inizia con una SU (una replica moltiplicata per una partizione) come valore minimo. Il valore massimo per tutti i servizi è 36 unità di streaming. Questo limite può essere ottenuto in diversi modi: 6 partizioni x 6 repliche o le repliche di 3 partizioni x 12, ad esempio. Accade spesso di usare minore capacità totale (ad esempio, una replica di 3, 3-partition servizio fatturata come 9 unità di streaming). Vedere le [combinazioni di partizioni e repliche](search-capacity-planning.md#chart) grafico per le combinazioni valide.

La tariffa di fatturazione è su base oraria per ogni unità di streaming. Ogni livello presenta una progressivamente maggiore velocità. I livelli maggiori sono dotate di partizioni più grandi e più veloce e ciò contribuisce a una tariffa oraria complessivamente maggiore per tale livello. È possibile visualizzare le tariffe per ogni livello nella [dettagli prezzi](https://azure.microsoft.com/pricing/details/search/) pagina.

La maggior parte dei clienti porta online solo una parte della capacità totale, tenendo il resto di riserva. Per la fatturazione, il numero di partizioni e repliche che portare online, calcolato mediante l'unità di streaming formula, determina paghi su base oraria.

### <a name="data-egress-charges-during-indexing"></a>Costi di uscita dei dati durante l'indicizzazione.

Usando [indicizzatori di ricerca di Azure](search-indexer-overview.md) potrebbe influire sulla fatturazione, a seconda della posizione dei servizi. È possibile eliminare i costi di uscita dei dati completamente se si crea il servizio di ricerca di Azure nella stessa area dei dati. Ecco alcune informazioni dal [pagina dei prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft non addebita il costo per tutti i dati in ingresso a qualsiasi servizio in Azure o per tutti i dati in uscita da ricerca di Azure.

+ Nelle soluzioni multiservizio, non sono previsti addebiti per i dati che attraversa la rete quando tutti i servizi sono nella stessa area.

Gli addebiti vengono applicati per i dati in uscita se servizi si trovano in aree diverse. Questi addebiti non fanno parte della fattura di ricerca di Azure. Si sta sopra indicate perché se si usa i dati o indicizzatori migliorato per intelligenza artificiale per estrarre i dati da aree diverse, si noterà i costi riflessi la fattura complessiva.

### <a name="ai-enriched-indexing-with-cognitive-services"></a>Migliorato per intelligenza artificiale indicizzazione con servizi cognitivi

Per la [indicizzazione di intelligenza artificiale con servizi cognitivi](cognitive-search-concept-intro.md), sarà possibile collegare una risorsa di servizi cognitivi di Azure fatturabile, nella stessa area come ricerca di Azure al livello S0 sui prezzi per l'elaborazione con pagamento a consumo. Non sono previsti costi fissi associati al collegamento di servizi cognitivi. Si paga solo per l'elaborazione che è necessario.

Estrazione di immagini durante decifrazione del documento è previsto un addebito di ricerca di Azure. Viene fatturata in base al numero di immagini estratti dai tuoi documenti. L'estrazione di testo è attualmente gratuita.

Altri miglioramenti, ad esempio l'elaborazione in linguaggio naturale, si basano [competenze cognitive predefinite](cognitive-search-predefined-skills.md) e fatturate in base a una risorsa di servizi cognitivi. Verranno fatturate alla stessa tariffa come se si aveva eseguito l'operazione usando direttamente i servizi cognitivi. Per altre informazioni, vedere [collegare una risorsa di servizi cognitivi con un insieme di competenze](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Fatturazione per l'estrazione di immagini nella ricerca cognitiva

Le immagini per estrarre da file in una pipeline di indicizzazione di ricerca cognitiva, verrà addebitata tale operazione nella fattura di ricerca di Azure. In un' [configurazione dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** è il parametro che attiva l'estrazione di immagini. Se **imageAction** è impostato su "none" (predefinito), non verrà addebitato per l'estrazione di immagini.

I prezzi sono soggetti a modifiche. È documentata nel [dettagli prezzi](https://azure.microsoft.com/pricing/details/search/) pagina per la ricerca di Azure.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Fatturazione per le competenze predefinite nella ricerca cognitiva

Quando si configura una pipeline di arricchimento, eventuali [competenze predefinite](cognitive-search-predefined-skills.md) usate nella pipeline sono basate su modelli di Machine Learning. Questi modelli vengono forniti da servizi cognitivi. Se si usano questi modelli durante l'indicizzazione, l'addebito avviene alla stessa tariffa come sarebbe se richiesta la risorsa direttamente.

Ad esempio, si dispone di una pipeline che usa il riconoscimento ottico dei caratteri (OCR) con i file JPEG analizzati e il testo risultante viene inserito in un indice di ricerca di Azure per le query di ricerca in formato libero. La pipeline di indicizzazione includerebbe un indicizzatore con la [competenza OCR](cognitive-search-skill-ocr.md) e tale competenza sarebbe [collegata a una risorsa Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Quando si esegue l'indicizzatore, gli addebiti per l'esecuzione di OCR verranno visualizzato nella fattura di Cognitive risorse.

## <a name="tips-for-reducing-costs"></a>Suggerimenti per la riduzione dei costi

È non è possibile arrestare il servizio per ridurre la fattura. Risorse dedicate siano sempre operative, allocata per l'uso esclusivo per la durata del servizio. È l'unico modo per ridurre l'importo della fattura per ridurre le repliche e partizioni a un livello che comunque offre prestazioni accettabili e [conformità SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Un modo per ridurre i costi consiste nello scegliere un livello con una frequenza oraria inferiore. Le tariffe orarie S1 sono inferiori alle tariffe S2 e S3. Supponendo che il provisioning del servizio dall'estremità inferiore delle proiezioni di carico, se si diventano il servizio, è possibile creare un secondo servizio più grandi a più livelli, ricompilare gli indici nel secondo servizio e quindi eliminare il primo elemento.

Se si è usato pianificazione della capacità per i server locali, si saprà già che sia comune acquistare "" in modo da poter gestire la crescita prevista. Con un servizio cloud, è possibile eseguire in modo aggressivo i risparmi sui costi ulteriori perché non è vincolante un acquisto specifico. È sempre possibile passare a un servizio a più livelli se quello corrente non è sufficiente.

### <a name="capacity"></a>Capacity

In Ricerca di Azure la capacità è strutturata in *repliche* e *partizioni*.

+ Le repliche sono istanze del servizio di ricerca. Ogni replica ospita una copia con bilanciamento del carico di un indice. Ad esempio, un servizio con sei repliche ha sei copie di ogni indice caricate nel servizio.

+ Le partizioni archiviano indici e suddividono automaticamente i dati ricercabili. L'indice nella metà di dividere due partizioni, e tre le partizioni dividono in terzi e così via. In termini di capacità *dimensione della partizione* rappresenta la funzionalità di differenziazione principale tra i livelli.

> [!NOTE]
> Tutti i livelli Standard e con ottimizzazione per la memoria supportano [combinazioni flessibili di partizioni e repliche](search-capacity-planning.md#chart) in modo da poter [ottimizzare il sistema di archiviazione o velocità](search-performance-optimization.md) modificando il saldo. Il livello Basic offre fino a tre repliche per la disponibilità elevata, ma è solo una partizione. Livelli gratuiti non forniscono risorse dedicate: calcolo delle risorse sono condivise da più sottoscrittori.

### <a name="more-about-service-limits"></a>Altre informazioni sui limiti dei servizi

Servizi di risorse host quali indici e indicizzatori. Ogni livello impone [limiti dei servizi](search-limits-quotas-capacity.md) sul numero di risorse è possibile creare. Pertanto, il numero massimo di indici (e altri oggetti) è la seconda funzionalità di differenziazione tra i livelli. Quando si analizzano ogni opzione nel portale, tenere presente i limiti sul numero di indici. Altre risorse, come gli indicizzatori, origini dati e competenze, sono apposta entro i limiti dell'indice.

## <a name="consumption-patterns"></a>Modelli di consumo

La maggior parte dei clienti iniziano con il servizio gratuito, che mantengono in modo indefinito, e quindi scegliere uno dei livelli Standard o archiviazione ottimizzata per carichi di lavoro sviluppo o produzione grave.

![Ricerca di Azure i piani tariffari](./media/search-sku-tier/tiers.png "piani tariffari di ricerca di Azure")

Alle estremità superiore e inferiore, Basic e S3 HD sono per i modelli di utilizzo atipico ma importante. Basic è per i carichi di lavoro di produzione di dimensioni ridotte. Che offre contratti di servizio, risorse dedicate e disponibilità elevata, ma offre archiviazione di dimensioni eccessive, aspettavo in totale 2 GB. Questo livello è stato progettato per i clienti che sottoutilizzano costantemente la capacità disponibile. Nella parte superiore, S3 HD è per i carichi di lavoro tipici di fornitori di software indipendenti, i partner [soluzioni multi-tenant](search-modeling-multitenant-saas-applications.md), o le configurazioni che chiamano per un numero elevato di indici di dimensioni ridotte. Spesso è chiaro quando base o S3 HD è il livello corretto. Se si desidera conferma, si può inviare a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) oppure [contattare il supporto tecnico Azure](https://azure.microsoft.com/support/options/) per materiale sussidiario.

I livelli standard più comunemente utilizzati, da S1 a S3, costituiscono una progressione di aumentare i livelli di capacità. Esistono limiti al numero degli indici, indicizzatori e le risorse corollario e punti di curvatura sulla dimensione di partizione:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Dimensioni partizione|  25 GB | 100 GB | 200 GB |  |  |  |  |
| limiti di indice e dell'indicizzatore| 50 | 200 | 200 |  |  |  |  |

S1 è una soluzione comune per i clienti che necessitano di risorse dedicate e più partizioni. S1 offre partizioni di 25 GB e un massimo di 12 partizioni, che fornisce un limite per ogni servizio di 300 GB, se si ingrandisce le partizioni tramite le repliche. (Vedere [allocare partizioni e repliche](search-capacity-planning.md#chart) per più bilanciato allocazioni.)

Le pagine del portale e dei prezzi inserire lo stato attivo su dimensioni della partizione e di archiviazione, ma, per ogni livello, tutte le funzionalità (capacità del disco, velocità, la CPU) di calcolo in genere aumenta in modo lineare con prezzo. È più veloce di S1 di una replica di S2 e S3 è più veloce rispetto a S2. Livelli S3 interrompere il modello di prezzi di calcolo lineare con i/o in modo non proporzionale più veloce. Se si prevede che i/o essere il collo di bottiglia, tenere presente che è possibile ottenere molte più IOPS con S3 quella che è possibile con i livelli inferiori.

S3 ed S3 HD sono supportati dall'infrastruttura di ad alta capacità identico, ma raggiungono i limiti massimi in modi diversi. S3 è destinato a un numero minore di indici di dimensioni molto grandi, in modo che il limite massimo è associato alle risorse (2,4 TB per ogni servizio). S3 HD è destinato a un numero elevato di indici di dimensioni molto ridotte. A 1000 indici, S3 HD raggiunge il limite in termini di vincoli di indice. Se sei un cliente S3 HD ed è necessario più di 1000 indici, contattare il supporto tecnico Microsoft per informazioni su come procedere.

> [!NOTE]
> Limiti per i documenti sono stati presi in considerazione in una sola volta, ma non sono più applicabili per i nuovi servizi. Per informazioni sulle condizioni in cui vengono mantenuti i limiti dei documenti, vedere [documentare i limiti](search-limits-quotas-capacity.md#document-limits).
>

I livelli ottimizzato di archiviazione L1 e L2, sono ideali per applicazioni con requisiti di dati di grandi dimensioni, ma con un numero relativamente ridotto di utenti finali, quando riducendo al minimo la latenza delle query non è la principale priorità.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Dimensioni partizione|  1 TB | 2 TB |  |  |  |  |  |
| limiti di indice e dell'indicizzatore| 10 | 10 |  |  |  |  |  |

L2 offre due volte la capacità di archiviazione complessiva di tipo L1.  Scegliere il livello in base alla quantità massima di dati che si ritiene che è l'indice. Le partizioni di livello L1 aumentare le prestazioni in incrementi di 1 TB a un massimo di 12 TB. Le partizioni L2 incrementato di 2 TB per ogni partizione con un massimo di 24 TB.

## <a name="evaluating-capacity"></a>La valutazione delle capacità

Capacità e i costi dell'esecuzione del servizio sono direttamente correlati. I livelli prevedono limiti per due livelli: archiviazione e risorse. È opportuno configurare entrambi perché a seconda del limite a raggiungere prima di tutto è il limite effettivo.

I requisiti aziendali prevedono in genere il numero di indici che è necessario. Ad esempio, potrebbe essere necessario un indice globale per un repository di grandi dimensioni dei documenti. Oppure potrebbe essere necessario più gli indici basati sull'area, applicazione o nicchia business.

Per determinare le dimensioni di un indice, è necessario [crearne uno](search-create-index-portal.md). La struttura dei dati in ricerca di Azure è principalmente un [indice invertito](https://en.wikipedia.org/wiki/Inverted_index) struttura, che ha caratteristiche diverse rispetto a dati di origine. Per un indice invertito, dimensioni e la complessità vengono determinate in base al contenuto, non necessariamente dalla quantità di dati che è inserire al suo interno. Un'origine dati di grandi dimensioni con ridondanza elevata potrebbero essere in un indice minore rispetto a un set di dati più piccolo che include contenuto estremamente variabile. Pertanto, è raramente possibile dedurre le dimensioni di indice in base alla dimensione del set di dati originale.

> [!NOTE] 
> Anche se può avere la stima delle esigenze future per gli indici e l'archiviazione, ad esempio la loro influenza, vale la pena di tale operazione. Se la capacità del livello si rivela insufficiente, è necessario eseguire il provisioning di un nuovo servizio a un livello superiore e quindi [ricaricare gli indici](search-howto-reindex.md). Non esiste un aggiornamento sul posto di un servizio da uno SKU a un'altra.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Passaggio 1: Sviluppare stima approssimativa degli aspetti tramite il livello gratuito

Uno degli approcci per la stima della capacità consiste nell'iniziare con il livello gratuito. Tenere presente che il servizio gratuito offre fino a tre indici, 50 MB di spazio di archiviazione e 2 minuti di tempo di indicizzazione. Può essere difficile per stimare la dimensione di un indice previsto con questi vincoli. Ecco un approccio che è possibile eseguire:

+ [Creare un servizio gratuito](search-create-service-portal.md).
+ Preparare un set di dati piccolo ma rappresentativo (ad esempio, 5.000 documenti e dimensioni del campione 10 percento).
+ [Compilare un indice iniziale](search-create-index-portal.md) e prendere nota delle relative dimensioni in portale (ad esempio, 30 MB).

Se il codice di esempio è rappresentativo e 10% dell'intera origine dati, un indice di 30 MB diventa circa 300 MB se tutti i documenti vengono indicizzati. Grazie a questo numero preliminare, è possibile fare doppio per budget per due indici non cluster (sviluppo e produzione). Ciò consente un totale di 600 MB nei requisiti di archiviazione. Questo requisito viene soddisfatto dal livello Basic, facilmente in modo che si può iniziare non esiste.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Passaggio 2: Sviluppare Affinate stime tramite un livello fatturabile

Alcuni clienti preferiscono iniziano con risorse dedicate che possono soddisfare le esigenze campionamento più grande e tempi di elaborazione e quindi sviluppare realistiche stime delle quantità di indice, dimensioni e i volumi di query durante lo sviluppo. Inizialmente, un servizio di provisioning viene eseguito in base a una stima ipotesi. Quindi, come il progetto di sviluppo viene perfezionata, i team in genere sapere se il servizio esistente è sopra o sotto la capacità per carichi di lavoro di produzione previsto.

1. [Esaminare i limiti del servizio in ogni piano](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) per determinare se i livelli inferiori possono supportare il numero di indici necessari. Nei livelli Basic, S1 e S2, limiti per gli indici sono 15, 50 e 200, rispettivamente. Il livello ottimizzato per archiviazione ha un limite di 10 indici, poiché questa è progettata per supportare un numero ridotto di indici di dimensioni molto grandi.

1. [Creare un servizio a un livello fatturabile](search-create-service-portal.md):

    + Avviare basso, Basic o S1, se si è all'inizio della curva di apprendimento.
    + Avviare elevato, in S2 o S3 anche, se si sa che si intende eseguire caricamenti di indicizzazione e query su larga scala.
    + Iniziare con l'archiviazione ottimizzata, L1 o L2, se si indicizzano una grande quantità di dati e carico della query è relativamente bassa, come con un'applicazione aziendale interna.

1. [Generare un indice iniziale](search-create-index-portal.md) per determinare il modo in cui i dati di origine vengono convertiti in un indice. Questo è l'unico modo per stimare le dimensioni di un indice.

1. [Monitorare l'archiviazione, i limiti del servizio, il volume di query e la latenza](search-monitor-usage.md) nel portale. Il portale Mostra le query per ogni query in secondo luogo, limitata e latenza di ricerca. Tutti questi valori sono utili per decidere se è stato selezionato il livello corretto. È anche possibile configurare il monitoraggio completo di valori, ad esempio analisi click-through abilitando [analitica il traffico di ricerca](search-traffic-analytics.md).

Numero di indice e le dimensioni sono altrettanto importante per l'analisi. Questo avviene perché i limiti massimi vengono raggiunti tramite l'utilizzo di archiviazione (partizioni) o in base ai limiti massimi sulle risorse (indici, indicizzatori e così via), a seconda del valore raggiunto per primo. Il portale consente di tenere traccia di entrambi gli aspetti visualizzando l'utilizzo corrente accanto ai limiti massimi nella pagina Panoramica.

> [!NOTE]
> Requisiti di archiviazione possono essere sottoposto a inflating se i documenti contengono dati estranei. In teoria, i documenti contengono solo i dati necessari per l'esperienza di ricerca. Dati binari non sono possibile eseguire ricerche e devono essere archiviati separatamente (magari in un archivio blob o tabella di Azure). Deve quindi essere aggiunto un campo nell'indice che contiene un riferimento URL ai dati esterni. Le dimensioni massime di un singolo documento sono 16 MB o inferiore se si caricano più documenti in un'unica richiesta. Per altre informazioni, vedere [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md).
>

**Considerazioni sul volume delle query**

Query al secondo (QPS) è un parametro importante durante l'ottimizzazione delle prestazioni, ma è in genere solo una considerazione di livello, se si prevede che il volume di query elevati all'inizio.

I livelli Standard possono fornire un equilibrio tra partizioni e repliche. È possibile aumentare turnaround query mediante l'aggiunta di repliche per il bilanciamento del carico o aggiungere partizioni per l'elaborazione parallela. È quindi possibile ottimizzare le prestazioni dopo il provisioning del servizio.

Se si prevede che i volumi di query elevati sostenuta fin dall'inizio, è necessario considerare i livelli Standard maggiori, supportati da hardware più potente. È possibile quindi portare offline le partizioni e repliche o passare a un servizio di livello inferiore, anche se non si verificano tali volumi di query. Per altre informazioni su come calcolare la velocità effettiva delle query, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca di Azure](search-performance-optimization.md).

I livelli di archiviazione ottimizzati sono utili per carichi di lavoro di dati di grandi dimensioni, supporto di più spazio di archiviazione indice spazio complessivo per quando i requisiti di latenza di query sono meno importanti. È comunque consigliabile usare repliche aggiuntive per le partizioni aggiuntive e bilanciamento del carico per l'elaborazione parallela. È quindi possibile ottimizzare le prestazioni dopo il provisioning del servizio.

**Contratti di servizio**

Le funzionalità di anteprima e il livello gratuito non offrono [contratti di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Per tutti i livelli fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. È necessario disporre di due o più repliche per i contratti di servizio di query (lettura). È necessario disporre di tre o più repliche per i contratti di servizio (lettura e scrittura) di indicizzazione e query. Il numero di partizioni non influenza i contratti di servizio.

## <a name="tips-for-tier-evaluation"></a>Suggerimenti per la valutazione del livello

+ Informazioni su come creare indici efficienti e informazioni su quali metodi di aggiornamento hanno un impatto minimo. Uso [analitica il traffico di ricerca](search-traffic-analytics.md) per ottenere informazioni dettagliate sull'attività di query.

+ Consentire le metriche creare query e raccogliere dati relativi ai modelli di utilizzo (query durante l'orario di ufficio, durante le ore di indicizzazione). Usare questi dati per guidare le decisioni di provisioning del servizio. Anche se non è pratico una cadenza oraria o giornaliera, è possibile modificare dinamicamente le partizioni e le risorse per supportare le modifiche pianificate nei volumi di query. È inoltre possibile offrire modifiche pianificate ma sostenute se i livelli contengono sufficientemente lungo da giustificare l'azione.

+ Tenere presente che l'unico svantaggio di underprovisioning è che potrebbe essere necessario chiudere un servizio se i requisiti effettivi sono superiori alle stime. Per evitare l'interruzione del servizio, è possibile creare un nuovo servizio nella stessa sottoscrizione a un livello superiore e scegliere un'esecuzione side-by-side fino a quando tutte le app e le richieste non sono indirizzate al nuovo endpoint.

## <a name="next-steps"></a>Passaggi successivi

Iniziare con un livello gratuito e compilare un indice iniziale usando un subset dei dati per comprendere le caratteristiche. La struttura dei dati in ricerca di Azure è una struttura con indice invertito. Le dimensioni e complessità di un indice invertito è determinato in base al contenuto. Tenere presente che un indice molto ridondante tende a generare un indice più piccolo rispetto a un contenuto molto irregolare. In modo che caratteristiche contenute piuttosto che le dimensioni del set di dati determinano i requisiti di archiviazione dell'indice.

Dopo aver creato una stima iniziale delle dimensioni di indice, [effettuare il provisioning di un servizio fatturabile](search-create-service-portal.md) su uno dei piani descritti in questo articolo: Basic, Standard o con ottimizzazione per la memoria. Ridurre eventuali vincoli artificiali sul ridimensionamento di dati e [ricompilazione dell'indice](search-howto-reindex.md) per includere tutti i dati che si desidera rendere disponibili per la ricerca.

[Allocare le partizioni e le repliche](search-capacity-planning.md) in base alle esigenze per ottenere le prestazioni e la scala desiderate.

Se le prestazioni e capacità sono corrette, è terminata. In caso contrario, ricreare un servizio di ricerca in un altro livello più adatto alle proprie esigenze.

> [!NOTE]
> Se hai domande, pubblicare [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) oppure [contattare il supporto tecnico Azure](https://azure.microsoft.com/support/options/).
