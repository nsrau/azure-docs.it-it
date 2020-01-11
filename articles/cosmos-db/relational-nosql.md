---
title: Informazioni sulle differenze tra Azure Cosmos DB NoSQL e i database relazionali
description: In questo articolo vengono enumerate le differenze tra NoSQL e i database relazionali
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896623"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Informazioni sulle differenze tra NoSQL e i database relazionali

In questo articolo vengono enumerati alcuni dei principali vantaggi dei database NoSQL sui database relazionali. Verranno inoltre illustrati alcuni dei problemi relativi all'utilizzo di NoSQL. Per un esame approfondito dei diversi archivi dati esistenti, vedere l'articolo sulla [scelta dell'archivio dati appropriato](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Velocità effettiva elevata

Una delle più ovvie problemi di gestione di un sistema di database relazionali è che la maggior parte dei motori relazionali applica i blocchi e i latch per applicare una [semantica ACID](https://en.wikipedia.org/wiki/ACID)rigida. Questo approccio presenta dei vantaggi in termini di garantire uno stato di dati coerente all'interno del database. Tuttavia, esistono compromessi pesanti rispetto alla concorrenza, alla latenza e alla disponibilità. A causa di queste restrizioni architetturali fondamentali, i volumi transazionali elevati possono comportare la necessità di eseguire manualmente il partizionamento dei dati. L'implementazione del partizionamento orizzontale manuale può richiedere molto tempo e un esercizio doloroso.

In questi scenari, i [database distribuiti](https://en.wikipedia.org/wiki/Distributed_database) possono offrire una soluzione più scalabile. Tuttavia, la manutenzione può comunque essere un esercizio costoso e dispendioso in termini di tempo. Gli amministratori possono dover eseguire operazioni aggiuntive per garantire che la natura distribuita del sistema sia trasparente. Potrebbero anche dover tenere conto della natura "disconnessa" del database.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) semplifica tali problemi, distribuendo il mondo in tutte le aree di Azure. Gli intervalli di partizioni sono in grado di essere suddivisi in modo dinamico per espandere facilmente il database in linea con l'applicazione, mantenendo al tempo stesso la disponibilità elevata. La governance con granularità fine e il controllo rigoroso delle risorse native del cloud facilitano le [garanzie di latenza](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) e le prestazioni prevedibili. Il partizionamento è completamente gestito, quindi gli amministratori non devono scrivere codice o gestire partizioni.

Se i volumi transazionali raggiungono livelli estremi, ad esempio molte migliaia di transazioni al secondo, è consigliabile considerare un database NoSQL distribuito. Prendere in considerazione Azure Cosmos DB per la massima efficienza, facilità di manutenzione e riduzione del costo totale di proprietà.

![Back-end](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>Dati gerarchici

Un numero significativo di casi d'uso in cui le transazioni nel database possono contenere molte relazioni padre-figlio. Queste relazioni possono aumentare significativamente nel tempo e risultare difficili da gestire. I moduli dei [database gerarchici](https://en.wikipedia.org/wiki/Hierarchical_database_model) sono emersi negli anni '80, ma non sono stati diffusi a causa dell'inefficienza nell'archiviazione. Hanno perso anche la trazione perché il [modello relazionale di Ted Codd](https://en.wikipedia.org/wiki/Relational_model) è diventato lo standard de facto usato praticamente da tutti i sistemi di gestione di database mainstream.

Tuttavia, oggi la popolarità dei database di tipo documento è aumentata significativamente. Questi database potrebbero essere considerati un reinventamento del paradigma del database gerarchico, ora non inibito da preoccupazioni circa il costo di archiviazione dei dati su disco. Di conseguenza, la gestione di molte relazioni di entità padre-figlio complesse in un database relazionale potrebbe ora essere considerata un anti-modello rispetto ai moderni approcci orientati ai documenti.

L'emergenza della [progettazione orientata agli oggetti](https://en.wikipedia.org/wiki/Object-oriented_design)e della [mancata corrispondenza dell'impedenza](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) che si verifica quando la si combina con i modelli relazionali, evidenzia anche un anti-pattern nei database relazionali per determinati casi d'uso. È possibile che si verifichino i costi di manutenzione nascosti ma spesso significativi. Sebbene gli [approcci ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) si siano evoluti per mitigare in parte questo problema, i database orientati ai documenti sono comunque coalesti con approcci orientati agli oggetti. Con questo approccio, gli sviluppatori non sono costretti a eseguire il commit ai driver ORM o ai [motori di database oo](https://en.wikipedia.org/wiki/Object_database)specifici del linguaggio. Se i dati contengono molte relazioni padre-figlio e livelli profondi di gerarchia, è consigliabile usare un database di documenti NoSQL, ad esempio l' [API SQL Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

![OrderDetails](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>Reti e relazioni complesse

Ironicamente, dato il nome, i database relazionali presentano una soluzione non ottimale per la modellazione di relazioni complesse e complete. Il motivo è che le relazioni tra entità non esistono effettivamente in un database relazionale. Devono essere calcolate in fase di esecuzione, con relazioni complesse che richiedono join cartesiani per consentire il mapping tramite query. Di conseguenza, le operazioni diventano esponenzialmente più onerose in termini di calcolo Man mano che le relazioni aumentano. In alcuni casi, un database relazionale che tenta di gestire tali entità diventerà inutilizzabile.

Sono emerse varie forme di database di "rete" durante il periodo in cui emergono i database relazionali, ma come per i database gerarchici, questi sistemi hanno avuto difficoltà a ottenere popolarità. L'adozione lenta era dovuta alla mancanza di casi d'uso al momento e alle inefficienze di archiviazione. Attualmente, i motori di database Graph possono essere considerati un nuovo emergenze del paradigma del database di rete. Il vantaggio principale di questi sistemi è che le relazioni vengono archiviate come "primi cittadini di classe" all'interno del database. Di conseguenza, l'attraversamento delle relazioni può essere eseguito in un tempo costante, anziché aumentare la complessità del tempo con ogni nuovo prodotto join o incrociato.

Se si gestisce una rete complessa di relazioni nel database, è possibile prendere in considerazione un database a grafo, ad esempio l' [API Gremlin Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) per la gestione di questi dati.

![Grafo](./media/relational-or-nosql/graph.png)

Azure Cosmos DB è un servizio di database multimodello, che offre una proiezione API per tutti i principali tipi di modelli di NoSQL. Famiglia di colonne, documento, grafo e chiave-valore. I livelli di API dei documenti [Gremlin (Graph)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) e SQL (Core) sono completamente interoperativi. Questa operazione offre vantaggi per il cambio tra modelli diversi a livello di programmabilità. È possibile eseguire query sugli archivi Graph in termini di attraversamenti di rete complessi e di transazioni modellate come record di documenti nello stesso archivio.

## <a name="fluid-schema"></a>Schema fluido

Un'altra caratteristica particolare dei database relazionali è che è necessario definire gli schemi in fase di progettazione. Si tratta di vantaggi in termini di integrità referenziale e conformità dei dati. Tuttavia, può anche essere restrittivo Man mano che l'applicazione aumenta. La risposta alle modifiche apportate allo schema attraverso modelli logicamente distinti che condividono la stessa tabella o definizione di database può diventare complessa nel tempo. Questi casi d'uso spesso traggono vantaggio dallo schema che viene devoluto all'applicazione per la gestione in base ai singoli record. Per questo è necessario che il database sia "indipendente dallo schema" e che i record siano "autodescrittivi" in termini di dati in essi contenuti.

Se si gestiscono dati le cui strutture cambiano costantemente a una frequenza elevata, in particolare se le transazioni possono provenire da origini esterne in cui è difficile applicare la conformità nel database, è possibile considerare un approccio più indipendente dallo schema. uso di un servizio di database NoSQL gestito come Azure Cosmos DB.

## <a name="microservices"></a>Microservizi

Il modello di [microservizi](https://en.wikipedia.org/wiki/Microservices) è cresciuto significativamente negli ultimi anni. Questo modello presenta le sue radici nell' [architettura orientata ai servizi](https://en.wikipedia.org/wiki/Service-oriented_architecture). Lo standard de facto per la trasmissione dei dati in queste architetture moderne di microservizi è [JSON](https://en.wikipedia.org/wiki/JSON), che è anche il supporto di archiviazione per la maggior parte dei database NoSQL orientati ai documenti. In questo modo, il documento NoSQL archivia una soluzione molto più adatta per la persistenza e la sincronizzazione (usando modelli di origine [eventi](https://en.wikipedia.org/wiki/Event-driven_architecture)) tra implementazioni di microservizi complesse. Più database relazionali tradizionali possono essere molto più complessi da gestire in queste architetture. Ciò è dovuto alla maggiore quantità di trasformazione necessaria per lo stato e la sincronizzazione tra le API. Azure Cosmos DB in particolare dispone di una serie di funzionalità che lo rendono una soluzione ancora più adatta per le architetture di microservizi basati su JSON rispetto a molti database NoSQL:

* scelta dei tipi di dati JSON puri
* un motore JavaScript e un' [API di query](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) incorporate nel database.
* un [feed di modifiche](https://docs.microsoft.com/azure/cosmos-db/change-feed) all'avanguardia a cui i client possono effettuare la sottoscrizione per ricevere una notifica delle modifiche apportate a un contenitore.

## <a name="some-challenges-with-nosql-databases"></a>Problemi con i database NoSQL

Sebbene esistano alcuni vantaggi evidenti quando si implementano i database NoSQL, è possibile che si desideri prendere in considerazione anche alcuni problemi. Quando si utilizza il modello relazionale, è possibile che non siano presenti allo stesso livello:

* transazioni con molte relazioni che puntano alla stessa entità.
* transazioni che richiedono coerenza assoluta nell'intero set di dati.

Esaminando la prima sfida, la regola empirica nei database NoSQL viene in genere denormalizzata, come articolata in precedenza, produce letture più efficienti in un sistema distribuito. Tuttavia, esistono alcuni problemi di progettazione che vengono affrontati con questo approccio. Si prenda ad esempio un prodotto correlato a una categoria e a più Tag:

![Join](./media/relational-or-nosql/many-joins.png)

Un approccio procedura consigliata in un database di documenti NoSQL consiste nel denormalizzare il nome della categoria e i nomi di tag direttamente in un "documento del prodotto". Tuttavia, per mantenere sincronizzate le categorie, i tag e i prodotti, le opzioni di progettazione per semplificare questa operazione hanno aggiunto complessità di manutenzione, perché i dati vengono duplicati tra più record del prodotto, anziché essere un semplice aggiornamento in uno "uno-a-molti" relazione e un join per recuperare i dati. 

Il compromesso è che le letture sono più efficienti nel record denormalizzato e diventano sempre più efficienti man mano che aumenta il numero di entità concettualmente Unite. Tuttavia, proprio come l'efficienza di lettura aumenta con l'aumentare del numero di entità unite in join in un record denormalizzate, anche la complessità di manutenzione per mantenere le entità sincronizzate. Un modo per attenuare questo compromesso consiste nel creare un modello di [dati ibrido](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models).

Sebbene sia disponibile una maggiore flessibilità nei database NoSQL per gestire questi compromessi, una maggiore flessibilità può produrre anche altre decisioni di progettazione. Vedere l'articolo [come modellare e partizionare i dati in Azure Cosmos DB usando un esempio reale](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example), che include un approccio per mantenere [i dati utente denormalizzati sincronizzati](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) , in cui gli utenti non solo si trovano in partizioni diverse, ma in contenitori diversi.

Per quanto riguarda la coerenza assoluta, è raro che questa operazione verrà richiesta nell'intero set di dati. Tuttavia, nei casi in cui questa operazione è necessaria, può trattarsi di una sfida nei database distribuiti. Per garantire la coerenza assoluta, i dati devono essere sincronizzati tra tutte le repliche e le aree prima di consentire ai client di leggerli. Questo può aumentare la latenza delle letture.

Anche in questo caso, Azure Cosmos DB offre una maggiore flessibilità rispetto ai database relazionali per i diversi compromessi rilevanti in questa sede, ma per le implementazioni su scala ridotta questo approccio può aggiungere ulteriori considerazioni sulla progettazione. Per informazioni dettagliate su questo argomento, vedere l'articolo sui [compromessi relativi a coerenza, disponibilità e prestazioni](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire l'account Azure Cosmos e altri concetti:

* [Come gestire l'account Azure Cosmos](how-to-manage-database-account.md)
* [Distribuzione globale](distribute-data-globally.md)
* [Livelli di coerenza](consistency-levels.md)
* [Working with Azure Cosmos containers and items](databases-containers-items.md) (Uso di contenitori ed elementi di Azure Cosmos)
* [Endpoint di servizio di rete virtuale per l'account Azure Cosmos](vnet-service-endpoint.md)
* [Firewall per gli indirizzi IP per l'account Azure Cosmos](firewall-support.md)
* [Come aggiungere e rimuovere aree di Azure per l'account Azure Cosmos](how-to-manage-database-account.md)
* [Contratti di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
