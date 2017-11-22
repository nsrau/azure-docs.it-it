---
title: Distribuire i dati a livello globale con Azure Cosmos DB | Microsoft Docs
description: Informazioni sulla replica geografica a livello globale, sul failover e sul ripristino dei dati usando i database globali di Azure Cosmos DB, un servizio database multimodello distribuito a livello globale.
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: arramac
ms.openlocfilehash: f09c96aabe637582ef43b863f8381a6ecfbebbf5
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Come distribuire i dati a livello globale con Azure Cosmos DB
Azure è ovunque, offre una copertura globale in oltre 30 aree geografiche ed è in continua espansione. Ciò consente ad Azure di offrire in esclusiva agli sviluppatori la possibilità di creare, distribuire e gestire applicazioni distribuite a livello globale con la massima semplicità. 

[Azure Cosmos DB](../cosmos-db/introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Azure Cosmos DB offre distribuzione globale chiavi in mano, [scalabilità elastica in termini di archiviazione e velocità effettiva](../cosmos-db/partition-data.md) ovunque nel mondo, latenze pari a singole unità di millisecondi al 99° percentile, [cinque livelli di coerenza ben definiti](consistency-levels.md) e disponibilità elevata garantita, il tutto supportato da [contratti di servizio leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indicizza automaticamente i dati](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) senza che sia necessario gestire manualmente indici e schemi. Si tratta di un database multimodello che supporta modelli di dati di documenti, coppie chiave-valore, grafi e colonne. In quanto servizio cloud, Azure Cosmos DB è stato accuratamente progettato fin dall'inizio per gestire multi-tenancy e distribuzione globale.

**Una singola raccolta di Azure Cosmos DB partizionata e distribuita tra più aree di Azure**

![Raccolta di Azure Cosmos DB partizionata e distribuita tra tre aree](./media/distribute-data-globally/global-apps.png)

Durante lo sviluppo di Azure Cosmos DB, è emerso che non era possibile aggiungere la distribuzione globale a posteriori, perché non è una funzionalità che si può integrare su un sistema di database basato su "singolo sito". Le funzionalità offerte da un database distribuito a livello globale superano quelle del ripristino di emergenza con ridondanza geografica tradizionale offerte dai database basati su "singolo sito". Il database basati su singolo sito che offrono la funzionalità di ripristino di emergenza con ridondanza geografica rappresentano un sottoinsieme limitato dei database distribuiti a livello globale. 

Grazie alla funzionalità di distribuzione globale chiavi in mano di Azure Cosmos DB, gli sviluppatori non devono creare i propri scaffolding di replica usando il modello lambda, ad esempio la [replica di AWS DynamoDB](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md), sul log del database oppure eseguendo "doppie scritture" tra più aree. Questi approcci non sono consigliati perché la correttezza non è garantita e non offrono contratti di servizio affidabili. 

In questo articolo viene illustrata una panoramica delle funzionalità di distribuzione globale di Azure Cosmos DB e viene descritto l'approccio esclusivo di Azure Cosmos DB per l'offerta di contratti di servizio completi. 

## <a id="EnableGlobalDistribution"></a>Abilitazione della distribuzione globale chiavi in mano
Azure Cosmos DB offre le funzionalità seguenti per consentire agli sviluppatori di scrivere applicazioni su scala globale in tutta semplicità. Queste funzionalità sono disponibili tramite [API REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) basate su provider di risorse di Azure Cosmos DB e tramite il portale di Azure.

### <a id="RegionalPresence"></a>Presenza in tutte le aree 
La presenza geografica di Azure è in continuo aumento, grazie alla disponibilità di un crescente numero di [nuove aree](https://azure.microsoft.com/regions/). Per impostazione predefinita, Azure Cosmos DB è disponibile in tutte le nuove aree di Azure. In questo modo, è possibile associare un'area geografica all'account del database Azure Cosmos DB non appena Azure rende disponibile la nuova area.

### <a id="UnlimitedRegionsPerAccount"></a>Associazione di un numero di aree illimitato all'account del database Azure Cosmos DB
Azure Cosmos DB consente di associare tutte le aree desiderate all'account del database Azure Cosmos DB. Ad eccezione delle limitazioni di geofencing, ad esempio Cina e Germania, non sono previsti limiti per il numero di aree che è possibile associare all'account del database Azure Cosmos DB. La figura seguente mostra un account del database configurato per estendersi su 25 aree di Azure.  

**Account del database Azure Cosmos DB di un tenant che si estende su 25 aree di Azure**

![Account del database Azure Cosmos DB che si estende su 25 aree di Azure](./media/distribute-data-globally/spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Geofencing basato su criteri
Azure Cosmos DB è progettato per l'uso di funzionalità di geofencing basate su criteri. Il geofencing è un componente importante che garantisce la governance dei dati e il rispetto delle limitazioni di conformità e che può impedire l'associazione di una determinata area all'account. Tra gli esempi di geofencing sono inclusi, a titolo esemplificativo, la limitazione dell'ambito della distribuzione globale alle aree con cloud sovrano, come Cina e Germania, oppure all'interno dei confini di applicazione delle imposte governative, ad esempio l'Australia. I criteri vengono controllati tramite i metadati della sottoscrizione di Azure.

### <a id="DynamicallyAddRegions"></a>Aggiunta e rimozione di aree in modo dinamico
Azure Cosmos DB consente di aggiungere (associare) o rimuovere (dissociare) aree nell'account del database in qualsiasi momento (vedere la [figura precedente](#UnlimitedRegionsPerAccount)). Grazie alla replica dei dati in parallelo tra le partizioni, Azure Cosmos DB garantisce che, quando viene aggiunta una nuova area in Azure, Azure Cosmos DB sia disponibile in qualsiasi parte del mondo per un massimo di 100 TB entro 30 minuti. 

### <a id="FailoverPriorities"></a>Priorità di failover
Per controllare la sequenza esatta dei failover a livello di area quando si verifica un'interruzione di servizio in più aree, Azure Cosmos DB consente di impostare una priorità per le varie aree associate all'account del database, come illustrato nella figura seguente. Azure Cosmos DB garantisce che la sequenza di failover automatico venga eseguita in base all'ordine di priorità specificato. Per altre informazioni su failover regionali, vedere [Failover a livello di area automatici per la continuità aziendale in Azure Cosmos DB](regional-failover.md).

**Un tenant di Azure Cosmos DB può configurare l'ordine di priorità di failover (riquadro a destra) per le aree associate a un account del database**

![Configurazione delle priorità di failover con Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Più modelli di coerenza ben definiti per i database con replica a livello globale
Azure Cosmos DB espone [più livelli di coerenza ben definiti](consistency-levels.md) supportati da contratti di servizio. È possibile scegliere un modello di coerenza specifico nell'elenco di opzioni disponibili, a seconda del carico di lavoro e degli scenari. 

### <a id="TunableConsistency"></a>Livelli di coerenza regolabili per i database con replica a livello globale
Azure Cosmos DB consente di eseguire l'override a livello di codice e sostituire il livello di coerenza per singola richiesta, in fase di esecuzione. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Aree di lettura e scrittura configurabili in modo dinamico
Azure Cosmos DB consente di configurare le aree associate al database come aree di "lettura", "scrittura" o "lettura/scrittura". 

### <a id="ElasticallyScaleThroughput"></a>Scalabilità elastica della velocità effettiva tra aree di Azure
È possibile ridimensionare in modo elastico una raccolta di Azure Cosmos DB eseguendo il provisioning della velocità effettiva a livello di codice. La velocità effettiva viene applicata a tutte le aree in cui è distribuita la raccolta.

### <a id="GeoLocalReadsAndWrites"></a>Letture e scritture nell'area locale specifica
Il vantaggio principale di un database distribuito a livello globale è l'accesso a bassa latenza ai dati ovunque nel mondo. Azure Cosmos DB offre garanzie di bassa latenza di livello p99 per varie operazioni su database. Garantisce che tutte le letture siano instradate all'area di lettura locale più vicina. Per gestire una richiesta di lettura, viene usato il quorum locale dell'area in cui viene inviata la richiesta di lettura. Lo stesso si applica alle operazioni di scrittura. Un'operazione di scrittura viene riconosciuta solo dopo che la maggior parte delle repliche ha eseguito in modo durevole il commit della scrittura localmente senza che sia vincolata al riconoscimento delle operazioni di scrittura da parte delle repliche remote. In altre parole, il protocollo di replica di Azure Cosmos DB parte dal presupposto che i quorum di lettura e scrittura si trovino sempre rispettivamente nelle stesse aree di lettura e scrittura in cui viene inviata la richiesta.

### <a id="ManualFailover"></a>Avvio manuale del failover a livello di area
Azure Cosmos DB consente di attivare il failover dell'account del database per convalidare le proprietà di disponibilità *end-to-end* dell'intera applicazione, oltre il database. Poiché sia le proprietà di sicurezza e di attività del rilevamento di errori e dell'algoritmo di elezione sono garantite, Azure Cosmos DB garantisce una *perdita di dati pari a zero* per un'operazione di failover avviata manualmente dal tenant.

### <a id="AutomaticFailover"></a>Failover automatico
Azure Cosmos DB supporta il failover automatico in caso di una o più interruzioni di servizio a livello di area. Durante un failover a livello di area, Azure Cosmos DB mantiene i contratti di servizio per latenza di lettura, disponibilità di tempo di attività, coerenza e velocità effettiva. Azure Cosmos DB prevede un limite superiore per la durata di completamento di un'operazione di failover automatico. Si tratta dell'intervallo di tempo della potenziale perdita di dati durante l'interruzione di servizio a livello di area.

### <a id="GranularFailover"></a>Progettato per varie granularità di failover
Attualmente le funzionalità di failover automatico e manuale sono esposte al livello di granularità dell'account del database. Si noti che internamente Azure Cosmos DB è progettato per offrire funzionalità di failover *automatico* a granularità più fini di un database, di una raccolta o persino di una partizione (di una raccolta proprietaria di un intervallo di chiavi). 

### <a id="MultiHomingAPIs"></a>API multihosting in Azure Cosmos DB
Azure Cosmos DB consente di interagire con il database tramite endpoint logici (indipendenti dall'area) o fisici (specifici dell'area). L'uso di endpoint logici garantisce che l'applicazione possa usufruire del multihoming in modo trasparente in caso di failover. Gli endpoint fisici, invece, offrono all'applicazione un controllo con granularità fine per reindirizzare le letture e le scritture ad aree specifiche.

Informazioni su come configurare le preferenze di lettura per le [API DocumentDB](../cosmos-db/tutorial-global-distribution-documentdb.md), [API Graph](../cosmos-db/tutorial-global-distribution-graph.md), [API Table](../cosmos-db/tutorial-global-distribution-table.md) e [API MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) sono disponibili nei rispettivi articoli collegati.

### <a id="TransparentSchemaMigration"></a>Migrazione di indici e di schemi del database trasparente e coerente 
Azure Cosmos DB è completamente [indipendente dallo schema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). La struttura esclusiva del motore di database consente di indicizzare automaticamente e in modo sincrono tutti i dati inseriti senza richiedere all'utente schemi o indici secondari. In questo modo è possibile iterare rapidamente l'applicazione distribuita a livello globale senza preoccuparsi della migrazione dell'indice o dello schema del database o del coordinamento di implementazioni di modifiche dello schema dell'applicazione in più fasi. Azure Cosmos DB garantisce che qualsiasi modifica ai criteri di indicizzazione eseguita dall'utente non comporti un calo significativo di prestazioni o disponibilità.  

### <a id="ComprehensiveSLAs"></a>Contratti di servizio completi (oltre la sola disponibilità elevata)
In quanto servizio di database distribuito a livello globale, Azure Cosmos DB offre un contratto di servizio ben definito per **perdita di dati**, **disponibilità**, **latenza a livello p99**, **velocità effettiva** e **coerenza** per il database nel suo complesso, indipendentemente dal numero di aree associate al database.  

## <a id="LatencyGuarantees"></a>Garanzie di latenza
Il vantaggio principale di un database distribuito a livello globale come Azure Cosmos DB è l'accesso a bassa latenza ai dati ovunque nel mondo. Azure Cosmos DB offre garanzie di bassa latenza di livello p99 per varie operazioni su database. Il protocollo di replica usato da Azure Cosmos DB garantisce che le operazioni su database, lettura e scrittura, vengano sempre eseguite nell'area in cui si trova il client. Il contratto di servizio per la latenza di Azure Cosmos DB prevede il livello p99 per letture, scritture indicizzate in modo sincrono e query per varie dimensioni di richiesta e risposta. Le garanzie di latenza per le operazioni di scrittura includono i commit dei quorum di maggioranza durevoli all'interno del data center locale.

### <a id="LatencyAndConsistency"></a>Relazione tra latenza e coerenza 
Affinché un servizio distribuito a livello globale possa offrire coerenza assoluta in una configurazione distribuita a livello globale, deve replicare in modo sincrono le operazioni di scrittura oppure eseguire letture sincrone tra aree: la velocità della luce e l'affidabilità della rete WAN indicano che la coerenza assoluta comporta latenze elevate e disponibilità ridotta delle operazioni su database. Di conseguenza, per offrire basse latenze p99 e disponibilità del 99,99% garantite per tutti gli account in una singola area e per tutti gli account in più aree con coerenza flessibile e disponibilità in lettura del 99,999% per tutti gli account di database in più aree, il servizio deve usare la replica asincrona. Questo, a sua volta, richiede che il servizio offra inoltre [opzioni di coerenza media ben definite](consistency-levels.md), meno efficienti rispetto alla coerenza assoluta (per offrire le garanzie di bassa latenza e disponibilità) ma più efficienti rispetto alla coerenza "finale" (per garantire un modello di programmazione intuitivo).

Azure Cosmos DB garantisce che un'operazione di lettura non sia tenuta a contattare le repliche estese su più aree per rispettare la garanzia del livello di coerenza specifico. Allo stesso modo, assicura che un'operazione di scrittura non venga bloccata mentre è in corso la replica dei dati su tutte le aree, ovvero le operazioni di scrittura sono replicate in modo asincrono tra le aree. Per gli account del database in più aree sono disponibili più livelli di coerenza media. 

### <a id="LatencyAndAvailability"></a>Relazione tra latenza e disponibilità 
Latenza e disponibilità sono le due facce della stessa medaglia. Si parla di latenza dell'operazione in stato stabile e di disponibilità in caso di errori. Dal punto di vista dell'applicazione, un'operazione di database con esecuzione lenta non si distingue da un database non disponibile. 

Per distinguere la latenza elevata dalla mancata disponibilità, Azure Cosmos DB consente un limite superiore per la latenza di varie operazioni su database. Se il tempo necessario per completare un'operazione su database è maggiore del limite superiore, Azure Cosmos DB restituisce un errore di timeout. Il contratto di servizio relativo alla disponibilità di Azure Cosmos DB garantisce che i timeout vengano conteggiati ai fini del contratto di servizio per la disponibilità. 

### <a id="LatencyAndThroughput"></a>Relazione tra latenza e velocità effettiva
Con Azure Cosmos DB non è necessario scegliere tra latenza e velocità effettiva. Viene rispettato il contratto di servizio per latenza a livello p99 e viene garantita la velocità effettiva di cui è stato effettuato il provisioning. 

## <a id="ConsistencyGuarantees"></a>Garanzie di coerenza
Sebbene il [modello di coerenza assoluta](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) rappresenti lo standard di programmabilità più elevato, comporta una latenza elevata (in stato stabile) e una disponibilità ridotta (in caso di errori). 

Azure Cosmos DB offre un modello di programmazione ben definito che consente di valutare la coerenza dei dati replicati. Per consentire la creazione di applicazioni multihosting, i modelli di coerenza esposti da Azure Cosmos DB sono progettati per essere indipendenti dall'area e per non dipendere dall'area in cui vengono gestite le operazioni di lettura e scrittura. 

Il contratto di servizio per la coerenza di Azure Cosmos DB garantisce che il 100% delle richieste di lettura soddisferà la garanzia di coerenza per il livello di coerenza necessario all'utente o il livello di coerenza predefinito per l'account del database o per il valore sostituito nella richiesta. Si considera che una richiesta abbia soddisfatto il contratto di servizio per la coerenza se sono state soddisfatte tutte le garanzie di coerenza associate al livello di coerenza. La tabella seguente mostra le garanzie di coerenza corrispondenti ai livelli di coerenza specifici offerti da Azure Cosmos DB.

**Garanzie di coerenza associate ai livelli di coerenza specifici in Azure Cosmos DB** 

<table>
    <tr>
        <td><strong>Livello di coerenza</strong></td>
        <td><strong>Caratteristiche di coerenza</strong></td>
        <td><strong>Contratto di servizio</strong></td>
    </tr>
    <tr>
        <td rowspan="3">sessione</td>
        <td>Lettura delle proprie scritture</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Lettura monotonica</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefisso coerente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Obsolescenza associata</td>
        <td>Lettura monotonica (all'interno di un'area)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefisso coerente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Decadimento ristretto &lt; K,T</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefisso coerente</td>
        <td>Prefisso coerente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Assoluta</td>
        <td>Linearizzabile</td>
        <td>100%</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>Relazione tra coerenza e disponibilità
Il [risultato di impossibilità](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) determinato dal [teorema CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) (Consistency, Availability, Partition Tolerance - Coerenza, Disponibilità, Tolleranza di partizione) dimostra che è impossibile che il sistema rimanga disponibile e offra coerenza linearizzabile in caso di errori. Il servizio di database deve scegliere tra coerenza e tolleranza di partizione (CP) o tra disponibilità e tolleranza di partizione (AP). I sistemi CP rinunciano alla disponibilità a favore della coerenza linearizzabile, mentre i sistemi AP rinunciano alla [coerenza linearizzabile](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) a favore della disponibilità. Azure Cosmos DB non viola mai il livello di coerenza necessario, di conseguenza può essere formalmente considerato un sistema CP. In pratica, però, la coerenza non è una soluzione del tipo "tutto o niente": esistono più modelli di coerenza ben definiti tra la coerenza linearizzabile e la coerenza finale. Per Azure Cosmos DB sono stati identificati vari modelli di coerenza media tramite l'applicabilità reale e un modello di programmazione intuitivo. Azure Cosmos DB supera i compromessi relativi a coerenza e disponibilità offrendo [più livelli di coerenza media ben definiti](consistency-levels.md) e una disponibilità del 99,99% per tutti gli account in una singola area e per tutti gli account in più aree con coerenza flessibile. Garantisce inoltre al 99,999% la disponibilità in lettura per tutti gli account di database in più aree. 

### <a id="ConsistencyAndAvailability"></a>Relazione tra coerenza e latenza
Il Prof. Daniel Abadi ha proposto una variante più completa del teorema CAP denominata [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), che tiene in considerazione i compromessi tra latenza e coerenza in stato stabile. Afferma che nello stato stabile, il sistema di database deve scegliere tra coerenza e latenza. Con più modelli di coerenza media (supportati da replica asincrona e quorum di lettura/scrittura locali), Azure Cosmos DB garantisce che tutte le operazioni di lettura e scrittura vengano eseguite rispettivamente nelle aree di lettura e scrittura locali specifiche.  Questo consente a Azure Cosmos DB di offrire garanzie di bassa latenza all'interno dell'area per i livelli di coerenza.  

### <a id="ConsistencyAndThroughput"></a>Relazione tra coerenza e velocità effettiva
Poiché l'implementazione di un modello di coerenza specifico dipende dalla scelta di un [tipo di quorum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), anche la velocità effettiva varia in base al tipo di coerenza scelto. In Azure Cosmos DB, ad esempio, l'addebito delle unità richiesta con le letture con coerenza assoluta è pari al doppio di quella delle letture con coerenza finale. In questo caso sarà necessario eseguire il provisioning del doppio delle unità richiesta nella raccolta per ottenere la stessa velocità effettiva.
 
**Relazione della capacità di lettura per un livello di coerenza specifico in Azure Cosmos DB** 

![Relazione tra coerenza e velocità effettiva](./media/distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Garanzie di velocità effettiva 
Azure Cosmos DB consente di ridimensionare la velocità effettiva e lo spazio di archiviazione in modo elastico su più aree a seconda della domanda. 

**Una singola raccolta di Azure Cosmos DB partizionata (su tre partizioni) e quindi distribuita su tre aree di Azure**

![Raccolte di Azure Cosmos DB partizionate e distribuite](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Una raccolta di Azure Cosmos DB viene distribuita usando due dimensioni, all'interno di un'area e in più aree. Ecco come: 

* All'interno di una singola area, la scalabilità di una raccolta di Azure Cosmos DB viene garantita tramite l'aumento di partizioni di risorse. Ogni partizione di risorsa gestisce un set di chiavi e offre coerenza assoluta e disponibilità elevata grazie alla replica della macchina a stati tra un set di repliche. Azure Cosmos DB è un sistema completamente gestito da risorse in cui una partizione di risorsa è responsabile della distribuzione della propria quota di velocità effettiva per il budget di risorse di sistema allocate. La scalabilità di una raccolta di Azure Cosmos DB è completamente trasparente. Azure Cosmos DB gestisce le partizioni di risorse, dividendole e unendole in base alle esigenze. 
* Ogni partizione di risorsa viene quindi distribuita in più aree. Le partizioni di risorse proprietarie dello stesso set di chiavi in più aree formano un set di partizioni (vedere la [figura precedente](#ThroughputGuarantees)).  Le partizioni di risorse in un set di partizioni sono coordinate tramite la replica della macchina a stati tra le aree. A seconda del livello di coerenza configurato, le partizioni di risorse in un set di partizioni sono configurate in modo dinamico con topologie diverse, ad esempio a stella, con collegamento in cascata, ad albero e così via. 

Grazie a funzionalità di gestione delle partizioni a velocità di risposta elevata, bilanciamento del carico e governance delle risorse rigida, Azure Cosmos DB consente di ridimensionare in modo elastico la velocità effettiva in più aree di Azure per una raccolta di Azure Cosmos DB. La modifica della velocità effettiva per una raccolta è un'operazione di runtime in Azure Cosmos DB. Come per qualsiasi altra operazione su database, Azure Cosmos DB garantisce il limite superiore assoluto della latenza durante l'operazione di modifica della velocità effettiva. Ad esempio, la figura seguente mostra la raccolta di un cliente per la quale il provisioning della velocità effettiva (compreso tra 1 milione e 10 milioni di richieste al secondo in due aree) viene eseguito in modo elastico in base alla domanda.
 
**Raccolta di un cliente con provisioning elastico della velocità effettiva (compresa tra 1 milione e 10 milioni di richieste al secondo)**

![Provisioning elastico della velocità effettiva di Azure Cosmos DB](./media/distribute-data-globally/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Relazione tra velocità effettiva e coerenza 
Analoga alla [Relazione tra coerenza e velocità effettiva](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relazione tra velocità effettiva e disponibilità
Azure Cosmos DB continua a mantenere la disponibilità anche quando vengono apportate modifiche alla velocità effettiva. Azure Cosmos DB gestisce le partizioni in modo trasparente (operazioni di divisione, unione e clonazione) e garantisce che le operazioni non degradino le prestazioni o la disponibilità mentre l'applicazione aumenta o riduce la velocità effettiva in modo elastico. 

## <a id="AvailabilityGuarantees"></a>Garanzie di disponibilità
Azure Cosmos DB offre un contratto di servizio con disponibilità del 99,99% per tutti gli account in una singola area e tutti gli account in più aree con coerenza media e con disponibilità in lettura del 99,999% per tutti gli account di database in più aree. Come descritto in precedenza, le garanzie di disponibilità di Azure Cosmos DB includono un limite superiore assoluto di latenza per ogni operazione del piano dati e controllo. Le garanzie di disponibilità sono costanti e non variano in base al numero di aree o alla distanza geografica tra aree. Le garanzie di disponibilità si applicano sia al failover manuale sia a quello automatico. Azure Cosmos DB offre API multihosting trasparenti che garantiscono che l'applicazione possa funzionare su endpoint logici e indirizzare le richieste in modo trasparente alla nuova area in caso di failover. In altre parole, non è necessario ridistribuire l'applicazione in caso di failover in un'altra area e i contratti di servizio relativi alla disponibilità vengono comunque mantenuti.

### <a id="AvailabilityAndConsistency"></a>Relazione tra disponibilità e coerenza, latenza e velocità effettiva
La relazione tra disponibilità e coerenza, latenza e velocità effettiva è descritta nelle sezioni [Relazione tra coerenza e disponibilità](#ConsistencyAndAvailability), [Relazione tra latenza e disponibilità](#LatencyAndAvailability) e [Relazione tra velocità effettiva e disponibilità](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Garanzie e comportamento del sistema per la "perdita di dati"
In Azure Cosmos DB la disponibilità elevata di ogni partizione (di una raccolta) è determinata da un numero di repliche distribuite tra un minimo di 10 e un massimo di 20 domini di errore. Il commit sincrono e durevole delle scritture viene eseguito da un quorum di maggioranza di repliche prima che ne venga accertata la ricezione nel client. La replica asincrona è coordinata tra partizioni distribuite in più aree. Azure Cosmos DB garantisce che non si verifichi alcuna perdita di dati per un failover manuale avviato dal tenant. Durante il failover automatico, Azure Cosmos DB garantisce un limite superiore dell'intervallo di decadimento ristretto nell'intervallo di perdita dei dati come parte del contratto di servizio.

## <a id="CustomerFacingSLAMetrics"></a>Metriche del contratto di servizio per il cliente
Azure Cosmos DB espone in modo trasparente le metriche per velocità effettiva, latenza, coerenza e disponibilità. Queste metriche sono accessibili a livello di codice e tramite il portale di Azure (vedere la figura seguente). È anche possibile impostare avvisi per varie soglie usando Azure Application Insights.
 
**Le metriche per coerenza, latenza, velocità effettiva e disponibilità sono disponibili in modo trasparente per ogni tenant**

![Metriche del contratto di servizio di Azure Cosmos DB visibili per il cliente](./media/distribute-data-globally/customer-slas.png)

## <a id="Next Steps"></a>Passaggi successivi
* Per implementare la replica globale nell'account Azure Cosmos DB tramite il portale di Azure, vedere [Come eseguire la replica del database globale di Azure Cosmos DB con il portale di Azure](tutorial-global-distribution-documentdb.md).
* Per altre informazioni su come implementare architetture multimaster con Azure Cosmos DB, vedere [Architetture di database multimaster con Azure Cosmos DB](multi-region-writers.md).
* Per altre informazioni sul funzionamento di failover automatico e manuale in Azure Cosmos DB, vedere [Failover a livello di area in Azure Cosmos DB](regional-failover.md).

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
