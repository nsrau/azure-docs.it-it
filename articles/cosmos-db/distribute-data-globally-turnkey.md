---
title: Come Azure Cosmos DB abilita la distribuzione globale chiavi in mano | Microsoft Docs
description: Informazioni sulla replica geografica a livello globale, sul multimaster, sul failover e sul ripristino dei dati usando i database globali di Azure Cosmos DB, un servizio database multimodello distribuito a livello globale.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 1aa0fa3d4d9e1821a6980d9334456a78eba7bfbc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956915"
---
# <a name="how-azure-cosmos-db-enables-turnkey-global-distribution"></a>Come Azure Cosmos DB abilita la distribuzione globale chiavi in mano
Azure Cosmos DB offre le funzionalità seguenti per consentire agli sviluppatori di scrivere applicazioni distribuite a livello globale in tutta semplicità. Queste funzionalità sono disponibili tramite [API REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) basate su provider di risorse di Azure Cosmos DB e tramite il portale di Azure.

## <a id="GlobalDistribution"></a>Distribuzione globale

### <a id="RegionalPresence"></a>Presenza in tutte le aree 
La presenza geografica di Azure è in continuo aumento, grazie alla disponibilità di un crescente numero di [nuove aree](https://azure.microsoft.com/regions/). Azure Cosmos DB viene classificato come *servizio fondamentale* in Azure ed è disponibile in tutte le nuove aree di Azure per impostazione predefinita. In questo modo, è possibile associare un'area geografica all'account del database Azure Cosmos DB non appena Azure rende disponibile la nuova area.

### <a id="MultiMasterSupport"></a>Supporto multimaster
Azure Cosmos DB offre il supporto nativo lato server per le aree a più master che partecipano equamente a un modello di scrittura globale. Questo supporto offre latenza di scrittura inferiore a 10 ms e disponibilità in scrittura del 99,999% con [contratti di servizio con copertura finanziaria](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Il multimaster è disponibile per tutte le API, tra cui [SQL](sql-api-introduction.md), [MongoDB](mongodb-introduction.md), [Cassandra](cassandra-introduction.md), [Graph](graph-introduction.md) e [Tabella](table-introduction.md) e quelle di tutte le lingue degli SDK per Cosmos DB. Azure Cosmos DB supporta 4 diversi livelli di coerenza (decadimento ristretto, sessione, coerenza del prefisso e finale) per gli account con funzionalità di multimaster.

### <a id="UnlimitedRegionsPerAccount"></a>Associazione di un numero di aree illimitato all'account del database Azure Cosmos DB
Azure Cosmos DB consente di associare tutte le aree desiderate all'account del database Azure Cosmos DB. Ad eccezione delle limitazioni di geofencing, ad esempio Cina e Germania, non sono previsti limiti per il numero di aree che è possibile associare all'account del database Azure Cosmos DB. La figura seguente mostra un account del database configurato per estendersi su 25 aree di Azure:

![Account del database Azure Cosmos DB che si estende su 25 aree di Azure](./media/distribute-data-globally-turnkey/spanning-regions.png)


### <a id="PolicyBasedGeoFencing"></a>Geofencing basato su criteri
Azure Cosmos DB è progettato per il supporto della definizione del recinto virtuale (geofencing) basata su criteri. Il geofencing è un componente importante che garantisce la governance dei dati e il rispetto delle limitazioni di conformità e che può impedire l'associazione di una determinata area all'account. Tra gli esempi di geofencing sono inclusi, a titolo esemplificativo, la limitazione dell'ambito della distribuzione globale alle aree con cloud sovrano, come Cina e Germania, oppure all'interno dei confini di applicazione delle imposte governative, ad esempio l'Australia. I criteri vengono controllati tramite i metadati della sottoscrizione di Azure.

### <a id="DynamicallyAddRegions"></a>Aggiunta e rimozione di aree in modo dinamico
Azure Cosmos DB consente di aggiungere (associare) o rimuovere (dissociare) aree dall'account del database in qualsiasi momento (vedere la [figura precedente](#UnlimitedRegionsPerAccount)). Grazie alla replica parallela dei dati tra le partizioni, Azure Cosmos DB garantisce che, quando viene aggiunta una nuova area, sarà disponibile per le operazioni entro 30 minuti in qualunque parte del mondo (supponendo che i dati siano di un massimo di 100 TB). 

### <a id="FailoverPriorities"></a>Priorità di failover
Quando non si usa il multimaster, i clienti possono controllare la sequenza esatta dei failover a livello di area in caso di interruzione del servizio, Azure Cosmos DB consente di impostare una *priorità* per le varie aree associate all'account del database, come illustrato nella figura seguente. Azure Cosmos DB garantisce che la sequenza di failover automatico venga eseguita in base all'ordine di priorità specificato. Per altre informazioni su failover regionali, vedere [Failover a livello di area automatici per la continuità aziendale in Azure Cosmos DB](regional-failover.md). L'immagine seguente illustra come un tenant in Azure Cosmos DB può configurare l'ordine di priorità di failover (riquadro a destra) per le aree associate a un account del database:

![Configurazione delle priorità di failover con Azure Cosmos DB](./media/distribute-data-globally-turnkey/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Più modelli di coerenza ben definiti per i database distribuiti a livello globale
Azure Cosmos DB supporta [più modelli di coerenza ben definiti, intuitivi e pratici](consistency-levels.md) supportati da contratti di servizio. È possibile scegliere un modello di coerenza specifico nell'elenco di opzioni disponibili, a seconda del carico di lavoro e degli scenari.

### <a id="TunableConsistency"></a>Livelli di coerenza regolabili per i database con replica a livello globale
Azure Cosmos DB consente di eseguire l'override a livello di codice e sostituire il livello di coerenza per singola richiesta in fase di esecuzione.

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Aree di lettura e scrittura configurabili in modo dinamico
Azure Cosmos DB consente di configurare le aree associate al database come aree di "lettura", "scrittura" o "lettura/scrittura".

### <a id="ElasticallyScaleThroughput"></a>Scalabilità elastica della velocità effettiva tra aree di Azure
È possibile ridimensionare in modo elastico un contenitore di Azure Cosmos DB eseguendo il provisioning della velocità effettiva a livello di codice. La velocità effettiva viene applicata a tutte le aree in cui è distribuito il contenitore di Azure Cosmos DB.

### <a id="GeoLocalReadsAndWrites"></a>Letture e scritture nell'area locale specifica
Il vantaggio principale di un database distribuito a livello globale è l'accesso a bassa latenza ai dati ovunque nel mondo. Azure Cosmos DB assicura operazioni di lettura e scrittura a bassa latenza (<10 millisecondi) al 99° percentile. Garantisce che tutte le letture siano gestite dall'area locale più vicina. Per gestire una richiesta di lettura, viene usato il quorum locale dell'area in cui viene inviata la richiesta di lettura. Lo stesso vale per le operazioni di scrittura. Un'operazione di scrittura viene riconosciuta solo dopo che la maggior parte delle repliche ha eseguito in modo durevole il commit della scrittura localmente, senza che sia vincolata al riconoscimento delle operazioni di scrittura da parte delle repliche remote. In altre parole, il protocollo di replica di Azure Cosmos DB parte dal presupposto che i quorum di lettura e scrittura si trovino sempre nell'area in cui è stata inviata la richiesta.

### <a id="ManualFailover"></a>Failover manuale
Azure Cosmos DB consente di attivare il failover di un account del database per convalidare le proprietà di disponibilità *end-to-end* dell'intera applicazione, oltre il database. Poiché sia le proprietà di sicurezza e di attività del rilevamento di errori e dell'algoritmo di elezione sono garantite, Azure Cosmos DB garantisce una *perdita di dati pari a zero* per un'operazione di failover avviata manualmente dal tenant.

### <a id="AutomaticFailover"></a>Failover automatico
Azure Cosmos DB supporta il failover automatico nel corso di una o più interruzioni di servizio a livello di area. Durante un failover a livello di area, Azure Cosmos DB mantiene i contratti di servizio per latenza di lettura, disponibilità di tempo di attività, coerenza e velocità effettiva. Azure Cosmos DB prevede un limite superiore per la durata del completamento di un'operazione di failover automatico. Si tratta dell'intervallo di tempo della potenziale perdita di dati durante un'interruzione di servizio a livello di area.

### <a id="GranularFailover"></a>Progettato per varie granularità di failover
Attualmente le funzionalità di failover automatico e manuale sono esposte al livello di granularità dell'account del database. Si noti che internamente Azure Cosmos DB è progettato per offrire funzionalità di failover *automatico* a granularità più fini di un database, di un contenitore o persino di una partizione (di un contenitore proprietario di un intervallo di chiavi). 

### <a id="MultiHomingAPIs"></a>Multihosting in Azure Cosmos DB

In uno scenario a master singolo, con un'area in scrittura e più repliche in lettura, Azure Cosmos DB consente di interagire con un database usando endpoint logici (indipendenti dall'area) o fisici (specifici dell'area). L'uso di endpoint logici garantisce che l'applicazione possa usufruire del multihosting in modo trasparente in caso di failover. Un endpoint fisico, invece, offre all'applicazione un controllo con granularità fine per reindirizzare le letture e le scritture ad aree specifiche. Negli scenari con più aree abilitate per la scrittura, gli utenti devono specificare endpoint specifici dell'area con le aree aggiunte in ordine di priorità di utilizzo e reindirizzamento.

Informazioni su come configurare le preferenze di lettura per l'[API SQL](../cosmos-db/tutorial-global-distribution-sql-api.md), l'[API Table](../cosmos-db/tutorial-global-distribution-table.md) e l'[API MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) sono disponibili in questi articoli.

### <a id="TransparentSchemaMigration"></a>Migrazione di indici e di schemi del database trasparente e coerente 
Azure Cosmos DB è completamente [indipendente dallo schema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). La struttura esclusiva del motore di database consente ad Azure Cosmos DB di indicizzare automaticamente e in modo sincrono tutti i dati in fase di inserimento senza richiedere all'utente schemi o indici secondari. In questo modo è possibile iterare rapidamente l'applicazione distribuita a livello globale senza preoccuparsi della migrazione dell'indice o dello schema del database o del coordinamento di implementazioni di modifiche dello schema dell'applicazione in più fasi. Azure Cosmos DB garantisce che qualsiasi modifica ai criteri di indicizzazione eseguita dall'utente non comporti un calo significativo di prestazioni o disponibilità.  

### <a id="ComprehensiveSLAs"></a>Contratti di servizio completi (oltre la disponibilità elevata)
In quanto servizio di database distribuito a livello globale, Azure Cosmos DB offre contratti di servizio ben definiti e completi per **disponibilità**, **latenza**, **velocità effettiva** e **coerenza** per il database in esecuzione su scala globale, indipendentemente dal numero di aree associate al database.  

## <a id="LatencyGuarantees"></a>Garanzie di latenza
Il vantaggio principale di un database distribuito a livello globale come Azure Cosmos DB è l'accesso a bassa latenza ai dati ovunque nel mondo. Azure Cosmos DB offre garanzie di bassa latenza al 99° percentile per varie operazioni su database. Il protocollo di replica usato da Azure Cosmos DB garantisce che le operazioni su database, sia in lettura che in scrittura, vengano sempre eseguite nell'area in cui si trova il client. Il contratto di servizio per la latenza di Azure Cosmos DB offre garanzie al 99° percentile per letture, scritture indicizzate in modo sincrono e query per varie dimensioni di richiesta e risposta. Le garanzie di latenza per le operazioni di scrittura includono i commit dei quorum di maggioranza durevoli all'interno dell'area locale.

### <a id="LatencyAndConsistency"></a>Relazione tra latenza e coerenza 
Affinché un servizio distribuito a livello globale possa offrire coerenza assoluta in una configurazione distribuita a livello globale, deve replicare in modo sincrono le operazioni di scrittura oppure eseguire letture sincrone tra aree. La velocità della luce e l'affidabilità della rete WAN indicano che la coerenza assoluta comporterà latenze elevate e disponibilità ridotta delle operazioni su database. Di conseguenza, per offrire basse latenze al 99° percentile e disponibilità del 99,99% garantite per tutti gli account in una singola area e per tutti gli account in più aree con coerenza flessibile, oltre a disponibilità del 99,999% per tutti gli account di database in più aree, il servizio deve usare la replica asincrona. Questo, a sua volta, richiede che il servizio offra anche [modelli di coerenza media ben definiti](consistency-levels.md), meno efficienti rispetto alla coerenza assoluta (per offrire le garanzie di bassa latenza e disponibilità) ma più efficienti rispetto alla coerenza "finale" (con un modello di programmazione intuitivo).

Azure Cosmos DB garantisce che un'operazione di lettura non sia tenuta a contattare le repliche estese su più aree per rispettare una garanzia del livello di coerenza specifico. Allo stesso modo, assicura che un'operazione di scrittura non venga bloccata mentre è in corso la replica dei dati su tutte le aree, ovvero le operazioni di scrittura sono replicate in modo asincrono tra le aree. Per gli account del database in più aree è disponibile la coerenza assoluta, oltre a più livelli di coerenza media. 

### <a id="LatencyAndAvailability"></a>Relazione tra latenza e disponibilità 
Latenza e disponibilità sono le due facce della stessa medaglia. Si parla di latenza di un'operazione in stato stabile e di disponibilità in presenza di errori e di partizioni di rete. Dal punto di vista dell'applicazione, un'operazione di database con esecuzione lenta non si distingue da un database non disponibile. 

Per distinguere la latenza elevata dalla mancata disponibilità, Azure Cosmos DB consente un limite superiore per la latenza di varie operazioni su database. Se il tempo necessario per completare un'operazione su database è maggiore del limite superiore, Azure Cosmos DB restituisce un errore di timeout. Il contratto di servizio relativo alla disponibilità di Azure Cosmos DB garantisce che i timeout vengano conteggiati ai fini del contratto di servizio per la disponibilità. 

### <a id="LatencyAndThroughput"></a>Relazione tra latenza e velocità effettiva
Con Azure Cosmos DB non è necessario scegliere tra latenza e velocità effettiva. Viene rispettato il contratto di servizio per latenza al 99° percentile e viene garantita la velocità effettiva di cui è stato effettuato il provisioning. 

## <a id="ConsistencyGuarantees"></a>Garanzie di coerenza
Sebbene il [modello di coerenza assoluta](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) rappresenti lo standard di programmabilità dei dati più elevato, comporta una latenza elevata (in stato stabile) e una disponibilità ridotta (in caso di errori). 

Azure Cosmos DB offre un modello di programmazione ben definito che consente di valutare la coerenza dei dati replicati. Per consentire la creazione semplificata di applicazioni distribuite a livello globale con funzionalità multihosting, i modelli di coerenza esposti da Azure Cosmos DB sono progettati per essere indipendenti dall'area e per non dipendere dall'area in cui vengono gestite le operazioni di lettura e scrittura. 

Il contratto di servizio per la coerenza di Azure Cosmos DB garantisce che il 100% delle richieste di lettura soddisferà la garanzia di coerenza per il modello di coerenza specificato dall'utente a livello dell'account del database o della richiesta. Si considera che una richiesta abbia soddisfatto il contratto di servizio per la coerenza se sono state soddisfatte tutte le garanzie di coerenza associate al livello di coerenza. La tabella seguente indica il modello di coerenza e le garanzie di coerenza. Ogni modello di coerenza garantisce un contratto di servizio con copertura del 100%. 

|Modello di coerenza  | Caratteristiche  |
|---------|---------|
|Assoluta |  Linearizzabile  |
|Obsolescenza associata  |  Lettura monotonica (all'interno di un'area), coerenza del prefisso, decadimento ristretto &lt; K,T   |
|sessione  |  Lettura delle proprie scritture, lettura monotonica, coerenza del prefisso    |
|Prefisso coerente  | Prefisso coerente  |

### <a id="ConsistencyAndAvailability"></a>Relazione tra coerenza e disponibilità
Il [risultato di impossibilità](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) determinato dal [teorema CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) (Consistency, Availability, Partition Tolerance - Coerenza, Disponibilità, Tolleranza di partizione) dimostra che è impossibile che un sistema rimanga disponibile e offra coerenza linearizzabile in caso di errori. Il servizio di database deve scegliere tra coerenza e tolleranza di partizione (CP) o tra disponibilità e tolleranza di partizione (AP). I sistemi CP rinunciano alla disponibilità a favore della coerenza linearizzabile, mentre i sistemi AP rinunciano alla [coerenza linearizzabile](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) a favore della disponibilità. Azure Cosmos DB non viola mai il modello di coerenza necessario, di conseguenza può essere formalmente considerato un sistema CP. In pratica, però, la coerenza non è una soluzione del tipo "tutto o niente"; esistono più modelli di coerenza ben definiti tra la coerenza linearizzabile e la coerenza finale. Per Azure Cosmos DB sono stati identificati vari modelli di coerenza media applicabili a scenari reali e intuitivi da usare. Azure Cosmos DB supera i compromessi relativi a coerenza e disponibilità offrendo [più modelli di coerenza media ben definiti](consistency-levels.md) e una disponibilità del 99,99% per tutti gli account di database in una singola area, oltre a una disponibilità del 99,999% in lettura e scrittura per tutti gli account di database in più aree. 

### <a id="ConsistencyAndAvailability"></a>Relazione tra coerenza e latenza
Una variante più completa del teorema CAP è denominata [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) e tiene in considerazione i compromessi tra latenza e coerenza in stato stabile. Afferma che in uno stato stabile un sistema di database deve scegliere tra coerenza e latenza. Con più modelli di coerenza media (supportati da replica asincrona e quorum di lettura e scrittura locali), Azure Cosmos DB garantisce che tutte le operazioni di lettura e scrittura vengano eseguite rispettivamente nelle aree di lettura e scrittura locali specifiche. Questo consente ad Azure Cosmos DB di offrire garanzie di bassa latenza all'interno dell'area per modelli di coerenza specifici.  

### <a id="ConsistencyAndThroughput"></a>Relazione tra coerenza e velocità effettiva
Poiché l'implementazione di un modello di coerenza specifico dipende dalla scelta di un [tipo di quorum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), anche la velocità effettiva varia in base al tipo di modello di coerenza scelto. In Azure Cosmos DB, ad esempio, l'addebito delle unità richiesta con le letture con coerenza assoluta è pari al *doppio* di quella delle letture con coerenza finale. In questo caso, sarà necessario eseguire il provisioning del doppio delle unità richiesta per ottenere la stessa velocità effettiva. L'immagine seguente illustra la relazione della capacità di lettura per un modello di coerenza specifico in Azure Cosmos DB:

![Relazione tra coerenza e velocità effettiva](./media/distribute-data-globally-turnkey/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Garanzie di velocità effettiva 
Azure Cosmos DB consente di ridimensionare la velocità effettiva e lo spazio di archiviazione in modo elastico su un numero qualsiasi di aree a seconda delle esigenze o della domanda. L'immagine seguente illustra un singolo contenitore di Azure Cosmos DB con partizionamento orizzontale (su tre partizioni di risorse all'interno di un'area) distribuito a livello globale su tre aree di Azure:

![Contenitori di Azure Cosmos DB partizionati e distribuiti](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Un contenitore di Azure Cosmos DB viene distribuito in due dimensioni (i) all'interno di un'area e (ii) in più aree. Ecco come: 

* **Distribuzione locale**: all'interno di una singola area, la scalabilità orizzontale di un contenitore di Azure Cosmos DB viene garantita tramite l'aumento di *partizioni di risorse*. Ogni partizione di risorsa gestisce un set di chiavi e offre coerenza assoluta e disponibilità elevata essendo rappresentata fisicamente da quattro repliche chiamate anche *set di repliche* e dalla replica della macchina a stati tra tali repliche. Azure Cosmos DB è un sistema completamente gestito da risorse, in cui una partizione di risorsa è responsabile della distribuzione della propria quota di velocità effettiva per il budget di risorse di sistema allocate. Il ridimensionamento di un contenitore di Azure Cosmos DB è trasparente agli utenti. Azure Cosmos DB gestisce le partizioni di risorse, dividendole e unendole in base alle esigenze di pari passo con l'evoluzione dei requisiti di archiviazione e di velocità effettiva. 
* **Distribuzione globale**: se si tratta di un database con più aree, ogni partizione di risorsa viene quindi distribuita in tali aree. Le partizioni di risorse proprietarie dello stesso set di chiavi in più aree formano un *set di partizioni* (vedere la [figura precedente](#ThroughputGuarantees)).  Le partizioni di risorse in un set di partizioni sono coordinate tramite la replica della macchina a stati tra le aree associate al database. A seconda del livello di coerenza configurato, le partizioni di risorse in un set di partizioni sono configurate in modo dinamico con topologie diverse, ad esempio a stella, con collegamento in cascata, ad albero e così via. 

Grazie a funzionalità di gestione delle partizioni a velocità di risposta elevata, bilanciamento del carico e governance delle risorse rigida, Azure Cosmos DB consente di ridimensionare in modo elastico la velocità effettiva in più aree di Azure associate a un contenitore o a un database di Azure Cosmos DB. La modifica della velocità effettiva di cui è stato effettuato il provisioning è un'operazione di runtime in Azure Cosmos DB. Come per qualsiasi altra operazione su database, Azure Cosmos DB garantisce il limite superiore assoluto della latenza durante l'operazione di modifica della velocità effettiva di cui è stato effettuato il provisioning. Ad esempio, la figura seguente mostra il contenitore di un cliente per il quale il provisioning della velocità effettiva (compresa tra 1 milione e 10 milioni di richieste al secondo in due aree) viene eseguito in modo elastico in base alla domanda.

![Provisioning elastico della velocità effettiva di Azure Cosmos DB](./media/distribute-data-globally-turnkey/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Relazione tra velocità effettiva e coerenza 
È la stessa descritta in [Relazione tra coerenza e velocità effettiva](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relazione tra velocità effettiva e disponibilità
Azure Cosmos DB continua a mantenere la disponibilità elevata anche quando vengono apportate modifiche alla velocità effettiva di cui è stato effettuato il provisioning. Azure Cosmos DB gestisce le partizioni di risorse in modo trasparente (eseguendo operazioni di divisione, unione e clonazione) e garantisce che le operazioni non degradino le prestazioni o la disponibilità mentre l'applicazione aumenta o riduce la velocità effettiva in modo elastico. 

## <a id="AvailabilityGuarantees"></a>Garanzie di disponibilità
Azure Cosmos DB offre un contratto di servizio con disponibilità del 99,99% per tutti gli account di database in una singola area e tutti gli account in più aree con coerenza media e con disponibilità del 99,999% per tutti gli account di database in più aree. Come descritto in precedenza, le garanzie di disponibilità di Azure Cosmos DB includono un limite superiore assoluto di latenza per ogni operazione del piano dati e controllo. Le garanzie di disponibilità non variano in base al numero di aree o alla distanza geografica tra le aree. Le garanzie di disponibilità sono applicabili sia al failover manuale sia a quello automatico. Azure Cosmos DB offre API multihosting trasparenti che garantiscono che l'applicazione possa funzionare su endpoint logici e indirizzare le richieste in modo trasparente a una nuova area in caso di failover oppure è possibile specificare endpoint a livello di area in ordine di priorità di utilizzo. Non è necessario ridistribuire l'applicazione in caso di failover in un'altra area e i contratti di servizio relativi alla disponibilità vengono mantenuti in qualsiasi momento.

### <a id="AvailabilityAndConsistency"></a>Relazione tra disponibilità e coerenza, latenza e velocità effettiva
La relazione tra disponibilità e coerenza, latenza e velocità effettiva è descritta nelle sezioni [Relazione tra coerenza e disponibilità](#ConsistencyAndAvailability), [Relazione tra latenza e disponibilità](#LatencyAndAvailability) e [Relazione tra velocità effettiva e disponibilità](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>Metriche del contratto di servizio per il cliente
Azure Cosmos DB espone in modo trasparente le metriche per velocità effettiva, latenza, coerenza e disponibilità. Queste metriche sono accessibili a livello di codice e tramite il portale di Azure (vedere la figura seguente). È anche possibile impostare avvisi per varie soglie usando Azure Application Insights. L'immagine seguente illustra le metriche per coerenza, latenza, velocità effettiva e disponibilità disponibili in modo trasparente per ogni tenant:
 
![Metriche del contratto di servizio di Azure Cosmos DB visibili per il cliente](./media/distribute-data-globally-turnkey/customer-slas.png)

## <a id="Next Steps"></a>Passaggi successivi

* [Vantaggi chiave della distribuzione globale di Azure Cosmos DB](distribute-data-globally-benefits.md)

* [Come configurare la replica del database globale di Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [How to enable multi-master for Azure Cosmos DB accounts (Come abilitare il multimaster per gli account di Azure Cosmos DB)](enable-multi-master.md)

* [Come funziona il failover automatico e manuale in Azure Cosmos DB](regional-failover.md)

* [Understanding conflict resolution in Azure Cosmos DB (Informazioni sulla risoluzione dei conflitti in Azure Cosmos DB)](multi-master-conflict-resolution.md)

* [Uso del multimaster di Azure Cosmos DB con database NoSQL open source](multi-master-oss-nosql.md)


## <a id="References"></a>Riferimenti
1. Eric Brewer. [Towards Robust Distributed Systems](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) (Verso sistemi distribuiti affidabili)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf) (Teorema CAP dodici anni dopo - Come sono cambiate le regole)
3. Gilbert, Lynch. - [Brewer&#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) (Ipotesi e fattibilità dei servizi Web con coerenza, disponibilità e tolleranza di errore)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) (Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (Non definire più i database come CP (coerenza e tolleranza di partizione) o AP (disponibilità e tolleranza di partizione))
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Decadimento ristretto probabilistico per quorum parziali pratici)
7. Naor e Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) (Carico, capacità e disponibilità nei sistemi di quorum)
8. Herlihy e Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) (Linearizzabilità: una condizione di correttezza per gli oggetti simultanei)
9. [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
