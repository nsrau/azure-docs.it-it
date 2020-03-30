---
title: Comprendere le differenze tra Azure Cosmos DB NoSQL e i database relazionali
description: In questo articolo vengono enumerate le differenze tra NoSQL e i database relazionali
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896623"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Comprendere le differenze tra NoSQL e i database relazionali

In questo articolo verranno enumerati alcuni dei vantaggi principali dei database NoSQL rispetto ai database relazionali. Discuteremo anche alcune delle sfide nel lavoro con NoSQL. Per un'analisi approfondita dei diversi archivi dati esistenti, vedere il nostro articolo sulla [scelta dell'archivio dati corretto.](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)

## <a name="high-throughput"></a>Velocità effettiva elevata

Una delle sfide più ovvie quando si mantiene un sistema di database relazionale è che la maggior parte dei motori relazionali applica blocchi e latti per applicare una [semantica ACID](https://en.wikipedia.org/wiki/ACID)rigorosa. Questo approccio offre vantaggi in termini di garantire uno stato dei dati coerente all'interno del database. Tuttavia, esistono compromessi pesanti per quanto riguarda la concorrenza, la latenza e la disponibilità. A causa di queste restrizioni architetturali fondamentali, volumi transazionali elevati possono comportare la necessità di partizionare manualmente i dati. L'implementazione del partizionamento manuale può essere un esercizio molto lungo e doloroso.

In questi scenari, i [database distribuiti](https://en.wikipedia.org/wiki/Distributed_database) possono offrire una soluzione più scalabile. Tuttavia, la manutenzione può ancora essere un esercizio costoso e dispendioso in termini di tempo. Gli amministratori potrebbero dover eseguire operazioni aggiuntive per garantire che la natura distribuita del sistema sia trasparente. Potrebbe anche essere necessario tenere conto della natura "disconnessa" del database.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) semplifica queste sfide, essendo distribuito in tutto il mondo in tutte le aree di Azure.Azure Cosmos DB semplifica queste sfide, by being deployed worldwide across all Azure regions. Gli intervalli di partizioni possono essere suddivisi dinamicamente per aumentare senza problemi il database in linea con l'applicazione, mantenendo contemporaneamente la disponibilità elevata. La multi-tenancy granulare e la governance delle risorse strettamente controllata e basata sul cloud facilitano garanzie di [latenza sorprendenti](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) e prestazioni prevedibili. Il partizionamento è completamente gestito, pertanto gli amministratori non devono scrivere codice o gestire le partizioni.

Se i volumi transazionali raggiungono livelli estremi, ad esempio molte migliaia di transazioni al secondo, è consigliabile prendere in considerazione un database NoSQL distribuito. Considera Azure Cosmos DB per ottenere la massima efficienza, facilità di manutenzione e riduzione del costo totale di proprietà.

![Back-end](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>Dati gerarchici

Esistono un numero significativo di casi d'uso in cui le transazioni nel database possono contenere molte relazioni padre-figlio. Queste relazioni possono crescere in modo significativo nel tempo e risultare difficili da gestire. Le forme di [database gerarchici](https://en.wikipedia.org/wiki/Hierarchical_database_model) sono emerse durante gli anni '80, ma non sono state popolari a causa dell'inefficienza nello stoccaggio. Hanno anche perso trazione come modello relazionale di [Ted Codd](https://en.wikipedia.org/wiki/Relational_model) è diventato lo standard de facto utilizzato da praticamente tutti i sistemi di gestione di database tradizionali.

Tuttavia, oggi la popolarità dei database in stile documento è cresciuta in modo significativo. Questi database potrebbero essere considerati una reimpostazione del paradigma gerarchico del database, ora disinibito dalle preoccupazioni circa il costo di archiviazione dei dati su disco. Di conseguenza, mantenere molte relazioni di entità padre-figlio complesse in un database relazionale potrebbe ora essere considerato un anti-modello rispetto ai moderni approcci orientati ai documenti.

L'emergere della [progettazione orientata](https://en.wikipedia.org/wiki/Object-oriented_design)agli oggetti e la [mancata corrispondenza di impedimento](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) che si verifica quando si combinano con modelli relazionali, evidenzia anche un anti-modello nei database relazionali per alcuni casi d'uso. Di conseguenza, possono verificarsi costi di manutenzione nascosti ma spesso significativi. Sebbene [gli approcci ORM si](https://en.wikipedia.org/wiki/Object-relational_mapping) siano evoluti per mitigare in parte questo problema, i database orientati ai documenti si fondono comunque molto meglio con approcci orientati agli oggetti. Con questo approccio, gli sviluppatori non sono obbligati a impegnarsi per i driver ORM o motori di [database OO](https://en.wikipedia.org/wiki/Object_database)specifici del linguaggio. Se i dati contengono molte relazioni padre-figlio e livelli profondi della gerarchia, è consigliabile usare un database di documenti NoSQL, ad esempio l'API SQL del [database Cosmos di Azure.](https://docs.microsoft.com/azure/cosmos-db/introduction)

![Dettagli Ordini](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>Reti e relazioni complesse

Ironia della sorte, dato il loro nome, i database relazionali presentano una soluzione non ottimale per la modellazione di relazioni profonde e complesse. Il motivo è che le relazioni tra entità non esistono effettivamente in un database relazionale. Devono essere calcolati in fase di esecuzione, con relazioni complesse che richiedono join cartesiani per consentire il mapping tramite query. Di conseguenza, le operazioni diventano esponenzialmente più costose in termini di calcolo con l'aumento delle relazioni. In alcuni casi, un database relazionale che tenta di gestire tali entità diventerà inutilizzabile.

Diverse forme di database "Network" sono emerse durante il periodo in cui sono emersi i database relazionali, ma come con i database gerarchici, questi sistemi hanno faticato a guadagnare popolarità. L'adozione lenta era dovuta alla mancanza di casi d'uso all'epoca e alle inefficienze di stoccaggio. Oggi, i motori di database a grafo potrebbero essere considerati una ricomparsa del paradigma del database di rete. Il vantaggio principale di questi sistemi è che le relazioni sono memorizzate come "cittadini di prima classe" all'interno del database. Pertanto, l'attraversamento delle relazioni può essere eseguito in tempi costanti, anziché aumentare la complessità temporale con ogni nuovo join o cross product.

Se si gestisce una rete complessa di relazioni nel database, è consigliabile prendere in considerazione un database grafico, ad esempio [l'API Azure Cosmos DB Gremlin](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) per la gestione di questi dati.

![Grafico](./media/relational-or-nosql/graph.png)

Azure Cosmos DB è un servizio di database multimodello, che offre una proiezione API per tutti i principali tipi di modello NoSQL. Gruppo di colonne, documento, grafico e chiave-valore. I livelli API [del documento Gremlin (grafico)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) e SQL (Core) sono completamente interoperabili. Ciò offre vantaggi per il passaggio tra i diversi modelli a livello di programmabilità. È possibile eseguire query negli archivi grafici sia in termini di attraversamenti di rete complessi che di transazioni modellate come record di documento nello stesso archivio.

## <a name="fluid-schema"></a>Schema fluido

Un'altra caratteristica particolare dei database relazionali è che gli schemi devono essere definiti in fase di progettazione. Ciò presenta vantaggi in termini di integrità referenziale e conformità dei dati. Tuttavia, può anche essere restrittivo man mano che l'applicazione cresce. Rispondere alle modifiche dello schema in modelli separati logicamente che condividono la stessa definizione di tabella o database può diventare complessa nel tempo. Tali casi d'uso spesso traggono vantaggio dal fatto che lo schema venga devoluto all'applicazione per la gestione per ogni record. Ciò richiede che il database sia "indipendente dallo schema" e consenta ai record di essere "autodescrittivi" in termini di dati in essi contenuti.

Se si gestiscono dati le cui strutture cambiano costantemente a un ritmo elevato, in particolare se le transazioni possono provenire da origini esterne in cui è difficile applicare la conformità all'interno del database, è possibile prendere in considerazione un approccio più indipendente dallo schema usando un servizio di database NoSQL gestito come Azure Cosmos DB.

## <a name="microservices"></a>Microservizi

Il modello di [microservizi](https://en.wikipedia.org/wiki/Microservices) è cresciuto in modo significativo negli ultimi anni. Questo modello ha le sue radici [nell'architettura orientata](https://en.wikipedia.org/wiki/Service-oriented_architecture)ai servizi. Lo standard de facto per la trasmissione dei dati in queste moderne architetture di microservizi è [JSON](https://en.wikipedia.org/wiki/JSON), che è anche il supporto di archiviazione per la maggior parte dei database NoSQL orientati ai documenti. In questo modo NoSQL document archivia una soluzione molto più semplice per la persistenza e la sincronizzazione (utilizzando modelli di [origine degli](https://en.wikipedia.org/wiki/Event-driven_architecture)eventi ) tra implementazioni Microservice complesse. I database relazionali più tradizionali possono essere molto più complessi da gestire in queste architetture. Ciò è dovuto alla maggiore quantità di trasformazione necessaria sia per lo stato che per la sincronizzazione tra le API. Azure Cosmos DB, in particolare, ha una serie di funzionalità che lo rendono ancora più semplice per le architetture di microservizi basate su JSON rispetto a molti database NoSQL:

* una scelta di tipi di dati JSON puri
* un motore JavaScript e [un'API](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) di query integrata nel database.
* un feed di [modifiche](https://docs.microsoft.com/azure/cosmos-db/change-feed) all'avanguardia che i client possono sottoscrivere per ricevere una notifica delle modifiche apportate a un contenitore.

## <a name="some-challenges-with-nosql-databases"></a>Alcune sfide con i database NoSQL

Anche se ci sono alcuni chiari vantaggi quando si implementano i database NoSQL, ci sono anche alcune sfide che si consiglia di prendere in considerazione. Questi potrebbero non essere presenti allo stesso grado quando si lavora con il modello relazionale:

* transazioni con molte relazioni che puntano alla stessa entità.
* transazioni che richiedono una forte coerenza nell'intero set di dati.

Guardando la prima sfida, la regola del pollice nei database NoSQL è generalmente la denormalizzazione, che come articolato in precedenza, produce letture più efficienti in un sistema distribuito. Tuttavia, ci sono alcune sfide di progettazione che entrano in gioco con questo approccio. Prendiamo un esempio di un prodotto correlato a una categoria e a più tag:

![Join](./media/relational-or-nosql/many-joins.png)

Un approccio di best practice in un database di documenti NoSQL sarebbe quello di denormalizzare il nome della categoria e i nomi dei tag direttamente in un "documento di prodotto". Tuttavia, al fine di mantenere categorie, tag e prodotti sincronizzati, le opzioni di progettazione per facilitare questo hanno aggiunto complessità di manutenzione, perché i dati vengono duplicati tra più record nel prodotto, piuttosto che essere un semplice aggiornamento in un "uno-a-molti" relazione e un join per recuperare i dati. 

Il compromesso è che le letture sono più efficienti nel record denormalizzato e diventano sempre più efficienti con l'aumentare del numero di entità unite concettualmente. Tuttavia, proprio come l'efficienza di lettura aumenta con un numero crescente di entità unite in un record di denormalizzazione, aumenta anche la complessità di manutenzione di mantenere sincronizzate le entità. Un modo per attenuare questo compromesso consiste nel creare un modello di [dati ibrido.](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)

Sebbene nei database NoSQL sia disponibile maggiore flessibilità per gestire questi compromessi, una maggiore flessibilità può anche produrre più decisioni di progettazione. Consultare il nostro articolo [su come modellare e partizionare i dati in Azure Cosmos DB usando un esempio reale,](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)che include un approccio per mantenere sincronizzati i [dati utente denormalizzati](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) in cui gli utenti non solo si trovano in partizioni diverse, ma in contenitori diversi.

Per quanto riguarda la coerenza elevata, è raro che questo sarà necessario nell'intero set di dati. Tuttavia, nei casi in cui ciò sia necessario, può essere una sfida nei database distribuiti. Per garantire una coerenza elevata, è necessario sincronizzare i dati in tutte le repliche e in tutte le aree prima di consentire ai client di leggerli. Ciò può aumentare la latenza delle letture.

Anche in questo caso, Azure Cosmos DB offre maggiore flessibilità rispetto ai database relazionali per i vari compromessi che sono rilevanti qui, ma per le implementazioni su piccola scala, questo approccio può aggiungere ulteriori considerazioni di progettazione. Consultare il nostro articolo su [Consistency, availability, and performance tradeoffs](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) per ulteriori dettagli su questo argomento.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire l'account Azure Cosmos e altri concetti:Learn how to manage your Azure Cosmos account and other concepts:

* [Come gestire l'account Azure Cosmos](how-to-manage-database-account.md)
* [Distribuzione globale](distribute-data-globally.md)
* [Livelli di coerenza](consistency-levels.md)
* [Working with Azure Cosmos containers and items](databases-containers-items.md) (Uso di contenitori ed elementi di Azure Cosmos)
* [Endpoint di servizio di rete virtuale per l'account Azure Cosmos](vnet-service-endpoint.md)
* [Firewall per gli indirizzi IP per l'account Azure Cosmos](firewall-support.md)
* [Come aggiungere e rimuovere aree di Azure per l'account Azure Cosmos](how-to-manage-database-account.md)
* [Contratti di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
