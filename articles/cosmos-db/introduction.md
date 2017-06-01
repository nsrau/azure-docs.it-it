---
title: Introduzione ad Azure Cosmos DB | Microsoft Docs
description: "Informazioni su Azure Cosmos DB. Questo database multimodello distribuito a livello globale è pensato per garantire bassa latenza, scalabilità elastica e disponibilità elevata."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 0736a6f9790b70686c4d936f2af80d5e42b89615
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="welcome-to-azure-cosmos-db"></a>Introduzione ad Azure Cosmos DB

Azure Cosmos DB è il database multimodello distribuito a livello globale di Microsoft. Con un semplice clic su un pulsante, Azure Cosmos DB garantisce la scalabilità elastica e indipendente della velocità effettiva e dello spazio di archiviazione tra un numero qualsiasi di aree geografiche di Azure. Assicura inoltre velocità effettiva, latenza, disponibilità e coerenza grazie a [contratti di servizio](https://aka.ms/acdbsla) (SLA, Service Level Agreement) completi, una garanzia che nessun altro servizio di database è in grado di offrire.

![Azure Cosmos DB è il servizio di database Microsoft distribuito a livello globale con scalabilità orizzontale elastica, bassa latenza garantita, cinque modelli di coerenza e contratti di servizio completi garantiti.](./media/introduction/azure-cosmos-db.png)

Azure Cosmos DB include un motore di database indipendente dallo schema, ottimizzato per la scrittura e gestito da risorse, che supporta in modo nativo più modelli di dati: chiave-valore, documenti, grafici e a colonne. Supporta inoltre molte API per l'accesso ai dati, tra cui [MongoDB](mongodb-introduction.md), [SQL di DocumentDB](documentdb-introduction.md), [Gremlin](graph-introduction.md) (anteprima) e [tabelle di Azure](table-introduction.md) (anteprima), in modo estendibile. 

Azure Cosmos DB è stato introdotto alla fine del 2010 per far fronte ai problemi critici di sviluppo riscontrati dalle applicazioni su larga scala all'interno di Microsoft. Poiché la creazione di applicazioni distribuite a livello globale non è un problema specifico di Microsoft, il servizio è stato reso disponibile esternamente per tutti gli sviluppatori di Azure con il nome di Azure DocumentDB. Azure Cosmos DB rappresenta lo stadio successivo di DocumentDB ed è disponibile per l'uso da parte di tutti gli utenti. Nell'ambito di questa release di Azure Cosmos DB, i clienti di DocumentDB (con i relativi dati) diventano automaticamente clienti di Azure Cosmos DB. La transizione è semplice ed ora è possibile accedere a una più ampia gamma di nuove funzionalità offerte da Azure Cosmos DB. 

## <a name="capability-comparison"></a>Confronto delle funzionalità

Azure Cosmos DB fornisce le migliori funzionalità dei database relazionali e non relazionali.

| Capabilities | Database relazionali    | Database non relazionali (NoSQL) |     Azure Cosmos DB |
| --- | --- | --- | --- |
| Distribuzione globale | x | x | ✓ Chiavi in mano, oltre 30 aree, multihosting |
| Scalabilità orizzontale | x | ✓  | ✓ Archiviazione e velocità effettiva scalabili in modo indipendente | 
| Garanzie di latenza | x | ✓  | ✓ <10 ms per le letture, <15 ms per le scritture al livello p99 | 
| Disponibilità elevata | x | ✓  | ✓ Always On, compromessi PACELC, failover automatico e manuale |
| Modello di dati + API | Relazionale + SQL | Multimodello + API OSS | Multimodello + SQL + API OSS (altre funzionalità presto disponibili) |
| Contratti di servizio | ✓  | x | ✓ Contratti di servizio completi per latenza, velocità effettiva, coerenza, disponibilità |

## <a name="key-capabilities"></a>Funzionalità principali
Come servizio di database distribuito a livello globale, Azure Cosmos DB fornisce le funzionalità seguenti che consentono di compilare applicazioni scalabili e altamente reattive, distribuite a livello globale:

* [**Distribuzione globale chiavi in mano**](#global-distribution)
    * L'applicazione è immediatamente disponibile per gli utenti, ovunque si trovino. E lo stesso vale per i dati.
    * Non serve preoccuparsi dell'hardware, né di aggiungere nodi, macchine virtuali o memoria centrale. È sufficiente fare clic e i dati sono disponibili. 

* [**Più modelli di dati e API comuni per l'accesso e le query sui dati**](#data-models)
    * Supporto per più modelli di dati, tra cui chiave-valore, documenti, grafici e a colonne.
    * API estendibili per Node.js, Java, .NET, .NET Core, Python e MongoDB.
    * SQL e Gremlin per le query. 

* [**Scalabilità elastica di velocità effettiva e archiviazione su richiesta, in tutto il mondo**](#horizontal-scale)
    * Possibilità di ridimensionare facilmente la velocità effettiva a granularità di [secondo](request-units.md) e [minuto](https://aka.ms/acdbrupm), modificandola in qualsiasi momento. 
    * Scalabilità dell'archiviazione [in modo trasparente e automatico](partition-data.md) per soddisfare i requisiti di dimensione presenti e futuri.

* [**Compilazione di applicazioni mission-critical altamente reattive**](#low-latency) 
    * Accesso ai dati con latenze pari a singole unità di millisecondi al livello p99 da ogni parte del mondo. 

* [**Disponibilità Always On**](#high-availability)
    * Disponibilità del 99,99% all'interno di una singola area.
    * Distribuzione in un numero qualsiasi di [aree di Azure](https://azure.microsoft.com/regions) per una maggiore disponibilità.
    * [Simulazione di errore](regional-failover.md) di una o più aree senza perdita di dati. 

* [**Scrittura di applicazioni distribuite a livello globale in modo immediato**](#consistency)
    * [Cinque modelli di coerenza](consistency-levels.md) offrono più livelli di coerenza, da quella assoluta di tipo SQL alla coerenza finale di tipo NoSQL, compresi i livelli intermedi. 
  
* [**Recupero dell'investimento**](#sla) 
    * I dati sono immediatamente disponibili in modo rapido, in caso contrario l'importo versato verrà rimborsato. 
    * [Contratti di servizio](https://aka.ms/acdbsla) per garantire disponibilità, latenza, velocità effettiva e coerenza. 

* [**Nessuna gestione di schemi/indici di database**](#schema-free)
    * Non è più necessario preoccuparsi di mantenere lo schema e gli indici del database sincronizzati con lo schema dell'applicazione. La soluzione è indipendente dallo schema. 

* [**Costo di proprietà ridotto**](#tco)
    * Da cinque a dieci volte [più conveniente](https://aka.ms/documentdb-tco-paper) rispetto a una soluzione non gestita.
    * Tre volte meno costoso di DynamoDB.

<a id="global-distribution"></a>

## <a name="global-distribution"></a>Distribuzione globale
I contenitori di Azure Cosmos DB sono distribuiti su due dimensioni: 

1. All'interno di una determinata area, tutte le risorse vengono partizionate orizzontalmente usando le partizioni di risorsa (distribuzione locale). 
2. Ogni partizione di risorsa viene inoltre replicata tra più aree geografiche (distribuzione globale). 

![Diagramma che descrive la distribuzione globale di Azure Cosmos DB](./media/introduction/azure-cosmos-db-global-distribution.png) 

Quando l'archiviazione e la velocità effettiva necessitano di scalabilità, Cosmos DB esegue in modo trasparente le operazioni di gestione delle partizioni tra tutte le aree. Indipendente da scalabilità, distribuzione o errori, Cosmos DB continua a fornire una singola immagine del sistema delle risorse distribuite a livello globale. 

La distribuzione globale delle risorse in Cosmos DB è disponibile [chiavi in mano](distribute-data-globally.md). In qualsiasi momento, con pochi clic (o a livello di codice con una sola chiamata API), è possibile associare un numero qualsiasi di aree geografiche con l'account di database. 

Indipendentemente dalla quantità di dati o dal numero di aree, Cosmos DB garantisce che ogni nuova area associata avvii l'elaborazione delle richieste client in meno di un'ora al livello p99. Ciò è possibile grazie alla parallelizzazione del seeding e alla copia dei dati da tutte le partizioni delle risorse di origine all'area appena associata. I clienti possono inoltre rimuovere un'area esistente o mettere offline un'area precedentemente associata al proprio account di database.

<a id="data-models"></a>
## <a name="multi-model-multi-api-support"></a>Supporto per più modelli e più API
 Azure Cosmos DB supporta in modalità nativa più modelli di dati, tra cui documenti, chiave-valore, grafici e colonne. Il modello di contenuto principale del motore di database di Cosmos DB è basato sulla sequenza di record ATOM (ARS, atom-record-sequence). Gli atomi sono costituiti da un set limitato di tipi primitivi come stringa, bool e numero. I record sono strutture composte da questi tipi. Le sequenze sono matrici costituite da atomi, record o sequenze. 
 
 Il motore di database può tradurre e progettare in modo efficiente diversi modelli di dati nel modello di dati basato su ARS. Il modello di dati principale di Cosmos DB è accessibile in modalità nativa da linguaggi di programmazione tipizzati dinamicamente e può essere esposto così com'è in formato JSON. 
 
 Il servizio supporta anche le più comuni API di database per l'accesso ai dati e le query. Il motore di database di Cosmos DB supporta attualmente [SQL di DocumentDB](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), [tabelle di Azure](table-introduction.md) (anteprima) e [Gremlin](graph-introduction.md) (anteprima). È possibile continuare a compilare le applicazioni usando le API OSS più comuni e ottenere tutti i vantaggi di un servizio di database ampiamente testato e completamente gestito, distribuito a livello globale. 

<a id="horizontal-scale"></a>
## <a name="horizontal-scaling-of-storage-and-throughput"></a>Scalabilità orizzontale di archiviazione e velocità effettiva
Tutti i dati in un contenitore Cosmos DB (ad esempio, una raccolta di documenti, una tabella o un grafico) sono partizionati orizzontalmente e gestiti in modo trasparente dalle partizioni di risorse. Una partizione di risorsa è un contenitore coerente a disponibilità elevata di dati partizionati in base a una [chiave di partizione specificata dal cliente](partition-data.md). Fornisce una singola immagine del sistema per un set di risorse che gestisce ed è un'unità fondamentale di scalabilità e distribuzione. Cosmos DB è progettato per garantire una scalabilità elastica della velocità effettiva in base ai modelli di traffico delle applicazioni tra aree geografiche differenti per supportare carichi di lavoro variabili a seconda dell'area geografica e dell'ora. Il servizio gestisce le partizioni in modo trasparente senza compromettere la disponibilità, la coerenza, la latenza o la velocità effettiva di un contenitore Cosmos DB.  
 
![Azure Cosmos DB è scalabile orizzontalmente](./media/introduction/azure-cosmos-db-partitioning.png) 

La scalabilità elastica della velocità effettiva di un contenitore di Azure Cosmos DB è garantita dal provisioning a livello di codice della velocità effettiva tramite [unità di richieste al secondo (UR/sec)](request-units.md). Internamente, il servizio gestisce in modo trasparente le partizioni di risorse per fornire la velocità effettiva per un contenitore specificato. Cosmos DB assicura che la velocità effettiva sia disponibile per l'uso in tutte le aree associate al contenitore. La nuova velocità effettiva è attiva entro cinque secondi dalla modifica del valore della velocità effettiva configurato. 

È possibile effettuare il provisioning della velocità effettiva in un contenitore Cosmos DB sia con la granularità al secondo che con la granularità [al minuto (UR/m)](request-units-per-minute.md). La velocità effettiva con provisioning con granularità al minuto viene usata per gestire gli spike imprevisti nel carico di lavoro che si verificano con la granularità al secondo. 

<a id="low-latency"></a>
## <a name="low-latency-guarantees-at-the-99th-percentile"></a>Garanzie di bassa latenza al livello p99
Nell'ambito dei contratti di servizio, Cosmos DB garantisce ai suoi clienti una bassa latenza end-to-end al livello p99. Per un elemento tipico da 1 KB, Cosmos DB assicura una latenza end-to-end inferiore a 10 ms per le letture e a 15 ms per le scritture indicizzate al livello p99, all'interno della stessa area di Azure. Le latenze medie sono significativamente inferiori (meno di 5 ms).  Con un limite superiore di elaborazione delle richieste per ogni transazione di database, Cosmos DB consente ai client di distinguere chiaramente tra transazioni con latenza elevata e un database non disponibile.

<a id="high-availability"></a>
## <a name="transparent-multi-homing-and-9999-high-availability"></a>Multihosting trasparente e disponibilità elevata al 99,99%
È possibile associare in modo dinamico "priorità" alle aree associate all'account di database Azure Cosmos DB. Le priorità vengono usate per indirizzare le richieste ad aree specifiche in caso di errori locali. Nell'eventualità di un'emergenza a livello locale, Cosmos DB esegue automaticamente il failover in ordine di priorità.

Per verificare la disponibilità end-to-end dell'applicazione, è possibile [attivare manualmente il failover](regional-failover.md) (frequenza limitata a due operazioni in un'ora). Cosmos DB assicura che non si verifichi alcuna perdita di dati durante i failover manuali locali. In caso di emergenza a livello locale, Cosmos DB garantisce un limite massimo per la perdita di dati durante il failover automatico avviato dal sistema. Non è necessario ridistribuire l'applicazione dopo un failover a livello locale e i contratti di servizio relativi alla disponibilità vengono mantenuti da Azure Cosmos DB. 

Per questo scenario, Cosmos DB consente di interagire con le risorse tramite endpoint logici (indipendenti dall'area) o fisici (specifici dell'area). I primi garantiscono che l'applicazione possa usufruire del multihosting in modo trasparente in caso di failover. I secondi, invece, offrono all'applicazione un controllo con granularità fine per reindirizzare le letture e le scritture ad aree specifiche. Cosmos DB offre un contratto di servizio con disponibilità garantita al 99,99% per ogni account di database. La garanzia di disponibilità è indipendente da scalabilità (archiviazione e velocità effettiva con provisioning), numero di aree o distanza geografica tra aree associate a un determinato database. 

<a id="consistency"></a>
## <a name="multiple-well-defined-consistency-models"></a>Più modelli di coerenza ben definiti
I database distribuiti a livello commerciale rientrano in due categorie: database che non offrono opzioni di coerenza comprovabili e ben definite e database che offrono due opzioni di programmabilità estreme (coerenza assoluta e coerenza finale). Nel primo caso gli sviluppatori di applicazioni devono preoccuparsi della gestione di aspetti secondari come i protocolli di replica e si trovano a scendere a difficili compromessi tra coerenza, disponibilità, latenza e velocità effettiva. Il secondo tipo di database, invece, implica una pressione per la scelta di uno dei due estremi. Nonostante il grande numero di ricerche e proposte per oltre 50 modelli di coerenza, la community dei database distribuiti non è riuscita a commercializzare livelli di coerenza che vadano oltre la coerenza assoluta e quella finale. 

Cosmos DB consente di scegliere tra [cinque modelli di coerenza ben definiti](consistency-levels.md): assoluta, decadimento ristretto, [sessione](http://dl.acm.org/citation.cfm?id=383631), prefisso coerente e finale. 

![Azure Cosmos DB offre numerosi modelli di coerenza, ampi e ben definiti, tra cui scegliere.](media/introduction/azure-cosmos-db-consistency-levels.png)

La tabella seguente illustra le garanzie specifiche fornite da ciascun livello di coerenza.
 
**Livelli di coerenza e garanzie**

| Livello di coerenza    | Garanzie |
| --- | --- |
| Assoluta | Linearità |
| Obsolescenza associata    | Prefisso coerente. Ritardo delle letture rispetto alle scritture in base a prefissi k o intervallo t |
| sessione    | Prefisso coerente. Letture costanti, scritture costanti, lettura delle proprie scritture, scrittura basata sulle letture |
| Prefisso coerente    | Gli aggiornamenti restituiti sono un prefisso di tutti gli aggiornamenti, senza interruzioni |
| Finale    | Letture non in ordine |

È possibile configurare il livello di coerenza predefinito per l'account Cosmos DB (e successivamente ignorare l'impostazione del livello di coerenza per una specifica richiesta di lettura). Internamente, il livello di coerenza predefinito si applica ai dati all'interno dei set di partizioni che possono estendersi a più aree. 


<a id="sla"></a>
## <a name="guaranteed-service-level-agreements"></a>Contratti di servizio garantiti

Cosmos DB è il primo servizio di database gestito in grado di offrire [contratti di servizio con garanzia](https://aka.ms/acdbsla) al 99,99% di disponibilità, velocità effettiva, bassa latenza e coerenza.
* Disponibilità: contratto di servizio con una disponibilità del tempo di attività pari al 99,99% per operazioni del piano dati e controllo.
* Velocità effettiva: 99,99% di richieste completate correttamente. 
* Latenza: 99,99% di latenze <10 ms al livello p99.
* Coerenza: 100% delle richieste di lettura che soddisfano la garanzia di coerenza per il livello di coerenza richiesto dall'utente.


<a id="schema-free"></a>
## <a name="schema-free"></a>Indipendenza dallo schema

Sia i database relazionali che quelli NoSQL implicano la necessità di dover gestire schemi e indici, il controllo delle versioni e la migrazione, tutti aspetti particolarmente complessi in un'installazione distribuita a livello globale. Con Cosmos DB tutto questo non è più un problema: non sarà più necessario, infatti, gestire schemi e indici, avere a che fare con il controllo delle versioni dello schema o preoccuparsi dei tempi di inattività delle applicazioni durante la migrazione degli schemi. Il motore di database di Cosmos DB è completamente indipendente dallo schema: questo significa che indicizza automaticamente tutti i dati inseriti senza richiedere schemi o indici e consente l'esecuzione di query ultrarapide. 

<a id="tco"></a>
## <a name="low-cost-of-ownership"></a>Costo di proprietà ridotto

 Tenendo in considerazione tutti gli aspetti relativi al costo totale di proprietà (TCO), i servizi cloud gestiti come Azure Cosmos DB possono essere da cinque a dieci volte più convenienti delle controparti OSS eseguite in locale o delle macchine virtuali. Inoltre, Azure Cosmos DB è da due a tre volte più economico di DynamoDB per carichi di lavoro con volumi elevati. Altre informazioni sono disponibili in questo [white paper sul costo totale di proprietà](https://aka.ms/documentdb-tco-paper). 

## <a name="next-steps"></a>Passaggi successivi
Per un'introduzione ad Azure Cosmos DB, fare riferimento alle guide introduttive seguenti:

* [Come iniziare a usare l'API DocumentDB di Azure Cosmos DB](create-documentdb-dotnet.md)
* [Come iniziare a usare l'API MongoDB di Azure Cosmos DB](create-mongodb-nodejs.md)
* [Come iniziare a usare l'API Graph di Azure Cosmos DB](create-graph-dotnet.md)
* [Come iniziare a usare l'API di tabella di Azure Cosmos DB](create-table-dotnet.md)

