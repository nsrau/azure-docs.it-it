---
title: Scegliere un piano tariffario o uno SKU
titleSuffix: Azure Cognitive Search
description: 'È possibile eseguire il provisioning di Ricerca cognitiva di Azure in questi SKU: Gratuito, Di base e Standard e Standard è disponibile in varie configurazioni delle risorse e livelli di capacità.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: eb11a5cc2deef372ca91c23a8b9c82e17143c85b
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617711"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Scegliere un piano tariffario per Ricerca cognitiva di AzureChoose a pricing tier for Azure Cognitive Search

Quando si crea un servizio Ricerca cognitiva di Azure, [viene creata](search-create-service-portal.md) una risorsa a un piano tariffario (o SKU) che viene risolto per la durata del servizio. I livelli includono Gratuito, Basic, Standard e Storage Optimized. Standard e Storage Ottimizzati sono disponibili con diverse configurazioni e capacità.

La maggior parte dei clienti inizia con il livello Gratuito in modo da poter valutare il servizio. Dopo la valutazione, è comune creare un secondo servizio in uno dei livelli superiori per le distribuzioni di sviluppo e produzione.

## <a name="feature-availability-by-tier"></a>Disponibilità delle funzionalità per livelloFeature availability by tier

Quasi tutte le funzionalità sono disponibili in ogni livello, incluso Gratuito, ma una funzionalità o un flusso di lavoro che richiede un utilizzo intensivo delle risorse potrebbe non funzionare correttamente a meno che non si formi una capacità sufficiente. Ad esempio, [l'arricchimento dell'iaaformazione](cognitive-search-concept-intro.md) ha competenze a esecuzione prolungata che scadano su un servizio gratuito a meno che il set di dati non sia piccolo.

Nella tabella seguente vengono descritti i vincoli di funzionalità correlati al livello.

| Funzionalità | Limitazioni |
|---------|-------------|
| [Indicizzatori](search-indexer-overview.md) | Gli indicizzatori non sono disponibili su S3 HD. |
| [Chiavi di crittografia gestite dal cliente](search-security-manage-encryption-keys.md) | Non disponibile nel livello Gratuito. |

## <a name="tiers-skus"></a>Livelli (SKU)Tiers (SKUs)

I livelli sono differenziati da:

+ Quantità di indici e indicizzatori che è possibile creare
+ Dimensione e velocità delle partizioni (archiviazione fisica)

Il livello selezionato determina la tariffa fatturabile. La schermata seguente del portale di Azure mostra i livelli disponibili, meno i prezzi (disponibili nel portale e nella [pagina dei prezzi.](https://azure.microsoft.com/pricing/details/search/) **Free**, **Basic**e **Standard** sono i livelli più comuni.

**Gratuito** crea un servizio di ricerca limitato per i progetti più piccoli, tra cui guide introduttive ed esercitazioni. Internamente, le repliche e le partizioni condivise tra più sottoscrittori. Non è possibile ridimensionare un servizio gratuito o eseguire carichi di lavoro significativi.

**Basic** e **Standard** sono i livelli fatturabili più comunemente usati, con **Standard** come valore predefinito. Con risorse dedicate sotto controllo, è possibile distribuire progetti di grandi dimensioni, ottimizzare le prestazioni e impostare la capacità.

![Piani dei prezzi di Ricerca cognitiva di AzurePricing tiers of Azure Cognitive Search](media/search-sku-tier/tiers.png "Piani dei prezzi di Ricerca cognitiva di AzurePricing tiers of Azure Cognitive Search")

Alcuni livelli sono ottimizzati per determinati tipi di lavoro. Ad esempio, **Standard 3 High Density (S3 HD)** è una *modalità* di hosting per S3, in cui l'hardware sottostante è ottimizzato per un numero elevato di indici più piccoli ed è destinato a scenari di multi-tenancy. S3 HD ha la stessa carica per unità di S3, ma l'hardware è ottimizzato per letture di file veloci su un gran numero di indici più piccoli.

Storage I livelli **ottimizzati** offrono una maggiore capacità di archiviazione a un prezzo inferiore per TB rispetto ai livelli Standard. Il compromesso principale è una latenza delle query più elevata, che è necessario convalidare per i requisiti specifici dell'applicazione.  Per altre informazioni sulle considerazioni relative alle prestazioni di questo livello, vedere Considerazioni sulle [prestazioni e sull'ottimizzazione](search-performance-optimization.md).

Per altre informazioni sui vari livelli, nella [pagina dei prezzi,](https://azure.microsoft.com/pricing/details/search/)nell'articolo [Limiti del servizio in Ricerca cognitiva](search-limits-quotas-capacity.md) di Azure e nella pagina del portale durante il provisioning di un servizio.

## <a name="billable-events"></a>Eventi fatturabili

Una soluzione basata su Ricerca cognitiva di Azure può comportare costi nei modi seguenti:A solution built on Azure Cognitive Search can incur costs in the following ways:

+ Costo fisso del servizio stesso, con 24x7, nella configurazione minima (una partizione e una replica)
+ Costo incrementale durante la scalabilità verticale (aggiungere repliche o partizioni)Incremental cost when scaling up (add replicas or partitions)
+ Costi della larghezza di banda (trasferimento dati in uscita) 
+ Ricerca cognitiva (collegamento di servizi cognitivi per l'arricchimento dell'iaformazione dell'io o l'uso dell'archiviazione di Azure per l'archivio informazioni)Cognitive search (attaching Cognitive Services for AI enrichment, or using Azure storage for knowledge store)

### <a name="service-costs"></a>Costi dei servizi

A differenza delle macchine virtuali o di altre risorse che possono essere "sospese" per evitare addebiti, un servizio Ricerca cognitiva di Azure è sempre disponibile nell'hardware dedicato per l'uso esclusivo. Di conseguenza, la creazione di un servizio è un evento fatturabile che viene avviato quando si crea il servizio e termina quando si elimina il servizio. 

Il costo minimo è la prima unità di ricerca (una replica x una partizione) alla tariffa fatturabile. Questo valore minimo è stato corretto per la durata del servizio perché il servizio non può essere eseguito su un valore inferiore a questa configurazione. Oltre il minimo, è possibile aggiungere repliche e partizioni indipendentemente l'una dall'altra. Gli aumenti incrementali di capacità tramite repliche e partizioni aumenteranno la fattura in base alla formula seguente: [(repliche x partizioni x frequenza)](#search-units), in cui la tariffa addebitata dipende dal piano tariffario selezionato.

Quando si stima il costo di una soluzione di ricerca, tenere presente che i prezzi e la capacità non sono lineari. (Raddoppiare la capacità più che raddoppiare il costo.) Per un esempio del funzionamento della formula, vedere [Come allocare repliche e partizioni.](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)

### <a name="bandwidth-charges"></a>Costi della larghezza di banda

L'uso degli [indicizzatori](search-indexer-overview.md) di Ricerca cognitiva di Azure potrebbe influire sulla fatturazione, a seconda della posizione dei servizi. È possibile eliminare completamente i costi di uscita dei dati se si crea il servizio Ricerca cognitiva di Azure nella stessa area dei dati. Ecco alcune informazioni dalla pagina dei prezzi della [larghezza di banda:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ Microsoft doesn't charge for any inbound data to any service on Azure, or for any outbound data from Azure Cognitive Search.
+ Nelle soluzioni multiservizio, non è previsto alcun costo per i dati che attraversano la rete quando tutti i servizi si trovano nella stessa area.

Gli addebiti si applicano ai dati in uscita se i servizi si trovano in aree diverse. Questi addebiti non fanno effettivamente parte della fattura di Ricerca cognitiva di Azure.These charges aren't actually part of your Azure Cognitive Search bill. Sono menzionati qui perché se si utilizzano dati o indicizzatori arricchiti di aiO per estrarre i dati da aree diverse, verranno visualizzati i costi riflessi nella fattura complessiva.

### <a name="ai-enrichment-with-cognitive-services"></a>Arricchimento dell'iA con Servizi cognitivi

Per [l'arricchimento](cognitive-search-concept-intro.md)dell'iaaformazione , è consigliabile pianificare [l'associazione](cognitive-search-attach-cognitive-services.md)di una risorsa servizi cognitivi di Azure fatturabile, nella stessa area di Ricerca cognitiva di Azure, al piano tariffario S0 per l'elaborazione con pagamento in base al consumo. Non sono previsti costi fissi associati alla connessione dei servizi cognitivi. Si paga solo per l'elaborazione di cui si ha bisogno.

| Operazione | Impatto della fatturazione |
|-----------|----------------|
| Cracking di documenti, estrazione di testo | Gratuito |
| Cracking di documenti, estrazione di immagini | Fatturato in base al numero di immagini estratte dai documenti. In una [configurazione dell'indicizzatore,](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** è il parametro che attiva l'estrazione dell'immagine. Se **imageAction** è impostato su "none" (impostazione predefinita), non ti verrà addebitato alcun costo per l'estrazione dell'immagine. La frequenza per l'estrazione delle immagini è documentata nella pagina dei [dettagli dei prezzi](https://azure.microsoft.com/pricing/details/search/) per Ricerca cognitiva di Azure.The rate for image extraction is documented on the pricing details page for Azure Cognitive Search.|
| [Competenze cognitive predefinite](cognitive-search-predefined-skills.md) | Fatturato alla stessa velocità come se l'attività fosse stata eseguita utilizzando direttamente Servizi cognitivi. |
| Competenze personalizzate | Una competenza personalizzata è la funzionalità fornita. Il costo dell'utilizzo di una competenza personalizzata dipende interamente dal fatto che il codice personalizzato chiami altri servizi a consumo. |

La funzionalità [di arricchimento incrementale (anteprima)](cognitive-search-incremental-indexing-conceptual.md) consente di fornire una cache che consente all'indicizzatore di essere più efficiente nell'esecuzione solo delle competenze cognitive necessarie se si modifica il set di competenze in futuro, risparmiando tempo e denaro.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Formula di fatturazione (R x P e SU)

Il concetto di fatturazione più importante da comprendere per le operazioni di Ricerca cognitiva di Azure è *l'unità di ricerca* (SU). Poiché Ricerca cognitiva di Azure dipende sia dalle repliche che dalle partizioni per l'indicizzazione e le query, non ha senso fatturare solo da uno o dall'altro. Al contrario, la fatturazione si basa su una combinazione di entrambi gli elementi.

SU è il prodotto delle *repliche* e delle *partizioni* utilizzate da un servizio: **(R x P - SU)**.

Ogni servizio inizia con una SU (una replica moltiplicata per una partizione) come valore minimo. Il valore massimo per qualsiasi servizio è 36 di gestione di sistema. Questo valore massimo può essere raggiunto in diversi modi:, ad esempio, 6 partizioni x 6 repliche o 3 partizioni x 12 repliche. È comune usare meno della capacità totale (ad esempio, un servizio a 3 repliche e a 3 partizioni fatturato come 9 unità di dominio). Vedere il grafico delle combinazioni di [partizioni e repliche](search-capacity-planning.md#chart) per le combinazioni valide.

La tariffa di fatturazione è oraria per SU. Ogni livello ha un tasso progressivamente più alto. I livelli più alti sono dotati di partizioni più grandi e più veloci e questo contribuisce a una tariffa oraria complessiva più alta per quel livello. È possibile visualizzare le tariffe per ogni livello nella pagina dei [dettagli dei prezzi.](https://azure.microsoft.com/pricing/details/search/)

La maggior parte dei clienti porta online solo una parte della capacità totale, tenendo il resto di riserva. Per la fatturazione, il numero di partizioni e repliche portate online, calcolato dalla formula SU, determina quanto si paga su base oraria.

## <a name="how-to-manage-costs"></a>Come gestire i costi

I seguenti suggerimenti possono aiutarti a ridurre al minimo i costi:

+ Creare tutte le risorse nella stessa area o nel minor numero possibile di aree per ridurre al minimo o eliminare i costi della larghezza di banda.

+ Consolidare tutti i servizi in un unico gruppo di risorse, ad esempio Ricerca cognitiva di Azure, Servizi cognitivi e qualsiasi altro servizio di Azure usato nella soluzione. Nel portale di Azure trovare il gruppo di risorse e usare i comandi **di gestione dei costi** per informazioni dettagliate sulla spesa effettiva e prevista.

+ Si consideri Azure Web App per l'applicazione front-end in modo che le richieste e le risposte rimangano entro il limite del data center.

+ Scalabilità verticale per operazioni che richiedono un uso intensivo delle risorse, ad esempio l'indicizzazione, quindi si adatta nuovamente ai normali carichi di lavoro di query. Iniziare con la configurazione minima per Ricerca cognitiva di Azure (una unità di registrazione delle operazioni di servizio composta da una partizione e una replica) e quindi monitorare l'attività degli utenti per identificare i modelli di utilizzo che indichino la necessità di una maggiore capacità. Se è presente un modello prevedibile, potrebbe essere possibile sincronizzare la scalabilità con l'attività (è necessario scrivere codice per automatizzare questa operazione).

Inoltre, visita [Fatturazione e gestione dei costi](https://docs.microsoft.com/azure/billing/billing-getting-started) per gli strumenti incorporati e le funzionalità correlate alla spesa.

Non è possibile arrestare un servizio di ricerca su base temporanea. Le risorse dedicate sono sempre operative, allocate per l'uso esclusivo per tutta la durata del servizio. L'eliminazione di un servizio è permanente ed elimina anche i dati associati.

In termini di servizio stesso, l'unico modo per abbassare la fattura consiste nel ridurre le repliche e le partizioni a un livello che fornisce ancora prestazioni accettabili e [conformità del](https://azure.microsoft.com/support/legal/sla/search/v1_0/)contratto di servizio oppure creare un servizio a un livello inferiore (le tariffe orarie S1 sono inferiori alle tariffe S2 o S3). Supponendo di eseguire il provisioning del servizio all'estremità inferiore delle proiezioni del carico, se si supera il servizio, è possibile creare un secondo servizio a più livelli, ricompilare gli indici nel secondo servizio e quindi eliminare il primo.

## <a name="how-to-evaluate-capacity-requirements"></a>Come valutare i requisiti di capacità

In Ricerca cognitiva di Azure la capacità è strutturata come *repliche* e *partizioni.*

+ Le repliche sono istanze del servizio di ricerca. Ogni replica ospita una copia con carico bilanciato di un indice. Ad esempio, un servizio con sei repliche dispone di sei copie di ogni indice caricato nel servizio.

+ Le partizioni archiviano gli indici e suddividono automaticamente i dati ricercabili. Due partizioni suddividono l'indice a metà, tre partizioni lo suddividono in terzi e così via. In termini di capacità, la dimensione della *partizione* è la funzionalità di differenziazione principale tra i livelli.

> [!NOTE]
> Tutti i livelli Standard and Storage Optimized supportano [combinazioni flessibili di repliche e partizioni](search-capacity-planning.md#chart) in modo da poter [ottimizzare il sistema per](search-performance-optimization.md) la velocità o l'archiviazione modificando il bilanciamento. Il livello Basic offre fino a tre repliche per la disponibilità elevata, ma ha una sola partizione. I livelli gratuiti non forniscono risorse dedicate: le risorse di elaborazione sono condivise da più sottoscrittori.

### <a name="evaluating-capacity"></a>Valutazione della capacità

La capacità e i costi di gestione del servizio vanno di pari passo. I livelli impongono limiti su due livelli: archiviazione e risorse. Dovresti pensare a entrambi perché qualsiasi limite raggiungi per primo è il limite effettivo.

I requisiti aziendali in genere determinano il numero di indici necessari. Ad esempio, potrebbe essere necessario un indice globale per un archivio di documenti di grandi dimensioni. In alternativa, potrebbero essere necessari più indici in base all'area geografica, all'applicazione o alla nicchia aziendale.

Per determinare le dimensioni di un indice, è necessario [crearne uno](search-create-index-portal.md). Le sue dimensioni saranno basate sui dati importati e sulla configurazione dell'indice, ad esempio se si abilitano i suggerimenti, il filtro e l'ordinamento. Per ulteriori informazioni sull'impatto sulla configurazione sulle dimensioni, vedere [Creare un indice di base ](search-what-is-an-index.md).

Per la ricerca full-text, la struttura di dati primario è una struttura di [indice invertita,](https://en.wikipedia.org/wiki/Inverted_index) che ha caratteristiche diverse rispetto ai dati di origine. Per un indice invertito, le dimensioni e la complessità sono determinate dal contenuto, non necessariamente dalla quantità di dati che invii in esso contenuti. Un'origine dati di grandi dimensioni con ridondanza elevata potrebbe comportare un indice più piccolo rispetto a un set di dati più piccolo che include contenuto altamente variabile. Quindi raramente è possibile dedurre le dimensioni dell'indice in base alle dimensioni del set di dati originale.

> [!NOTE] 
> Anche se la stima delle esigenze future per gli indici e l'archiviazione può sembrare una congettura, vale la pena farlo. Se la capacità di un livello risulta essere troppo bassa, è necessario eseguire il provisioning di un nuovo servizio a un livello superiore e quindi [ricaricare gli indici.](search-howto-reindex.md) Non esiste alcun aggiornamento sul posto di un servizio da uno SKU a un altro.
>

### <a name="estimate-with-the-free-tier"></a>Stima con il livello Gratuito

Un approccio per la stima della capacità consiste nell'iniziare con il livello gratuito. Tenere presente che il servizio gratuito offre fino a tre indici, 50 MB di spazio di archiviazione e 2 minuti di tempo di indicizzazione. Può essere difficile stimare una dimensione dell'indice prevista con questi vincoli, ma questi sono i passaggi seguenti:It can be challenging to estimate a projected index size with these constraints, but these are the steps:

+ [Creare un servizio gratuito](search-create-service-portal.md).
+ Preparare un set di dati piccolo e rappresentativo.
+ [Creare un indice iniziale nel portale](search-create-index-portal.md) e annotenerne le dimensioni. Le funzionalità e gli attributi hanno un impatto sull'archiviazione. Ad esempio, l'aggiunta di suggerimenti (query di ricerca durante la digitazione) aumenterà i requisiti di archiviazione. Utilizzando lo stesso set di dati, è possibile provare a creare più versioni di un indice, con attributi diversi in ogni campo, per verificare come variano i requisiti di archiviazione. Per ulteriori informazioni, vedere ["Implicazioni di archiviazione" in Creare un indice di base](search-what-is-an-index.md#index-size).

Con una stima approssimativa in mano, si potrebbe raddoppiare tale importo al budget per due indici (sviluppo e produzione) e quindi scegliere il livello di conseguenza.

### <a name="estimate-with-a-billable-tier"></a>Stima con un livello fatturabile

Le risorse dedicate possono supportare tempi di campionamento ed elaborazione più elevati per stime più realistiche dei volumi di quantità, dimensioni e query dell'indice durante lo sviluppo. Alcuni clienti passano direttamente con un livello fatturabile e quindi rivalutano man mano che il progetto di sviluppo matura.

1. [Esaminare i limiti](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) del servizio a ogni livello per determinare se i livelli inferiori possono supportare il numero di indici necessari. Nei livelli Basic, S1 e S2, i limiti di indice sono rispettivamente 15, 50 e 200. Il livello Ottimizzazione archiviazione ha un limite di 10 indici perché è progettato per supportare un numero ridotto di indici molto grandi.

1. [Creare un servizio a un livello fatturabile](search-create-service-portal.md):

    + Iniziare basso, in Base o S1, se non si è sicuri circa il carico previsto.
    + Iniziare da high, da S2 o anche S3, se si sa che si sta andando ad avere l'indicizzazione su larga scala e il caricamento di query.
    + Iniziare con Storage Optimized, in L1 o L2, se si sta indicizzare una grande quantità di dati e il carico di query è relativamente basso, come con un'applicazione aziendale interna.

1. [Generare un indice iniziale](search-create-index-portal.md) per determinare il modo in cui i dati di origine vengono convertiti in un indice. Questo è l'unico modo per stimare le dimensioni di un indice.

1. [Monitorare l'archiviazione, i limiti del servizio, il volume di query e la latenza](search-monitor-usage.md) nel portale. Il portale mostra le query al secondo, le query limitate e la latenza di ricerca. Tutti questi valori consentono di decidere se è stato selezionato il livello corretto. 

Il numero e le dimensioni dell'indice sono ugualmente importanti per l'analisi. Ciò è dovuto al fatto che i limiti massimi vengono raggiunti tramite l'utilizzo completo dell'archiviazione (partizioni) o i limiti massimi sulle risorse (indici, indicizzatori e così via), a seconda di quale si trova per primo. Il portale consente di tenere traccia di entrambi gli aspetti visualizzando l'utilizzo corrente accanto ai limiti massimi nella pagina Panoramica.

> [!NOTE]
> I requisiti di archiviazione possono essere gonfiati se i documenti contengono dati estranei. Idealmente, i documenti contengono solo i dati necessari per l'esperienza di ricerca. I dati binari non sono ricercabili e devono essere archiviati separatamente (ad esempio in una tabella di Azure o in un'archiviazione BLOB). Un campo deve quindi essere aggiunto nell'indice per contenere un riferimento URL ai dati esterni. La dimensione massima di un singolo documento è 16 MB (o meno se stai caricando in blocco più documenti in una richiesta). Per altre informazioni, vedere [Limiti del servizio in Ricerca cognitiva](search-limits-quotas-capacity.md)di Azure .For more information, see Service limits in Azure Cognitive Search .
>

**Considerazioni sul volume delle query**

Le query al secondo (QPS) sono una metrica importante durante l'ottimizzazione delle prestazioni, ma in genere è una considerazione del livello solo se si prevede un volume di query elevato all'inizio.

I livelli Standard possono fornire un bilanciamento di repliche e partizioni. È possibile aumentare il turnaround delle query aggiungendo repliche per il bilanciamento del carico o aggiungendo partizioni per l'elaborazione parallela. È quindi possibile ottimizzare le prestazioni dopo il provisioning del servizio.

Se si prevedono volumi elevati di query sostenuti fin dall'inizio, è necessario considerare livelli Standard più elevati, supportati da hardware più potente. È quindi possibile portare le partizioni e le repliche offline o persino passare a un servizio di livello inferiore, se tali volumi di query non si verificano. Per altre informazioni su come calcolare la velocità effettiva delle query, vedere Ottimizzazione e prestazioni di [Ricerca cognitiva di Azure.For](search-performance-optimization.md)more information on how to calculate query throughput, see Azure Cognitive Search performance and optimization .

I livelli Ottimizzati di archiviazione sono utili per carichi di lavoro di dati di grandi dimensioni, che supportano una maggiore quantità di archiviazione degli indici disponibile per i requisiti di latenza delle query meno importanti. È comunque consigliabile usare repliche aggiuntive per il bilanciamento del carico e partizioni aggiuntive per l'elaborazione parallela. È quindi possibile ottimizzare le prestazioni dopo il provisioning del servizio.

**Contratti di servizio**

Il livello Gratuito e le funzionalità di anteprima non forniscono contratti di [servizio (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Per tutti i livelli fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. È necessario disporre di due o più repliche per i contratti di gruppo di query (lettura). È necessario disporre di tre o più repliche per i contratti di servizio per query e indicizzazione (lettura/scrittura). Il numero di partizioni non influisce sui contratti di archiviazione.

## <a name="tips-for-tier-evaluation"></a>Suggerimenti per la valutazione del livello

+ Consentire alle metriche di basarsi sulle query e raccogliere dati sui modelli di utilizzo (query durante le ore di ufficio, indicizzazione durante le ore non di punta). Usare questi dati per prendere decisioni sul provisioning dei servizi. Anche se non è pratico a una cadenza oraria o giornaliera, è possibile regolare dinamicamente le partizioni e le risorse per supportare le modifiche pianificate nei volumi di query. È inoltre possibile gestire modifiche non pianificate ma sostenute se i livelli sono sufficienti a giustificare l'intervento.

+ Tenere presente che l'unico aspetto negativo dell'under provisioning è che potrebbe essere necessario arrestare un servizio se i requisiti effettivi sono maggiori delle stime. Per evitare interruzioni del servizio, è necessario creare un nuovo servizio a un livello superiore ed eseguirlo side-by-side fino a quando tutte le app e le richieste non sono destinate al nuovo endpoint.

## <a name="next-steps"></a>Passaggi successivi

Iniziare con un livello Gratuito e creare un indice iniziale usando un subset dei dati per comprenderne le caratteristiche. La struttura dei dati in Ricerca cognitiva di Azure è una struttura di indice invertita. Le dimensioni e la complessità di un indice invertito sono determinate dal contenuto. Tenere presente che un indice molto ridondante tende a generare un indice più piccolo rispetto a un contenuto molto irregolare. Pertanto, le caratteristiche del contenuto anziché le dimensioni del set di dati determinano i requisiti di archiviazione dell'indice.

Dopo aver specificato una stima iniziale delle dimensioni dell'indice, eseguire il provisioning di [un servizio fatturabile](search-create-service-portal.md) in uno dei livelli illustrati in questo articolo: Basic, Standard o Storage Optimized. Rilassare eventuali vincoli artificiali sul dimensionamento dei dati e [ricostruire l'indice](search-howto-reindex.md) per includere tutti i dati che si desidera essere ricercabili.

[Allocare le partizioni e le repliche](search-capacity-planning.md) in base alle esigenze per ottenere le prestazioni e la scala desiderate.

Se le prestazioni e la capacità vanno bene, il processo è finito. In caso contrario, ricreare un servizio di ricerca in un altro livello più adatto alle proprie esigenze.

> [!NOTE]
> In caso di domande, passare a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) o contattare il [supporto di Azure.](https://azure.microsoft.com/support/options/)
