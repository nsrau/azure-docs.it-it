---
title: Introduzione alle API Graph di Azure Cosmos DB | Documentazione Microsoft
description: Informazioni su come usare Azure Cosmos DB per archiviare, eseguire query e attraversare grafi di grandi dimensioni con latenza bassa usando il linguaggio di query Gremlin.
services: cosmosdb
author: arramac
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 963822f0927afea90c8a03b60b7ee93fe36b4070
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Introduzione ad Azure Cosmos DB: API Graph

[Azure Cosmos DB](introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Azure Cosmos DB assicura la [distribuzione globale chiavi in mano](../documentdb/documentdb-distribute-data-globally.md), la [scalabilità elastica in termini di archiviazione e velocità effettiva](partition-data.md) a livello globale e latenze pari a singole unità di millisecondi al 99° percentile, [cinque livelli di coerenza ben definiti](../documentdb/documentdb-consistency-levels.md) e una disponibilità elevata garantita, il tutto supportato da [contratti di servizio leader nel settore](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/). Azure Cosmos DB [indicizza automaticamente i dati](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) senza dover gestire indici e schemi. Si tratta di un database multimodello che supporta modelli di dati di documenti, coppie chiave/valore, grafi e colonne. 

![Gremlin, Graph e Azure Cosmos DB](./media/graph-introduction/graph-gremlin.png) 

Azure Cosmos DB fornisce API di modellazione e attraversamento di grafi, oltre a distribuzione globale chiavi in mano, scalabilità elastica in termini di archiviazione e velocità effettiva, latenze di letture < 10 ms e < 15 ms a p99, query e indicizzazione automatica, livelli di coerenza perfezionabili e contratti di servizio completi, inclusa la disponibilità al 99,99%. È possibile eseguire query in Azure Cosmos DB usando lo [standard Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) e Azure Cosmos DB si integra con gli altri sistemi a grafi compatibili con Gremlin.

Questo articolo fornisce una panoramica dell'API Graph di Azure Cosmos DB, dei possibili usi per l'archiviazione di grafi di grandi dimensioni con miliardi di vertici e archi, dell'esecuzione di query con latenze di millisecondi e della facile evoluzione dello schema e dello struttura del grafo. 

## <a name="graph-databases"></a>Database di grafi
I dati presenti nel mondo reale vengono connessi in modo naturale. La modellazione di dati tradizionale si basa sul concetto di entità. Tuttavia per molte applicazioni è necessario anche modellare relazioni avanzate tra le entità. I grafi consentono di modellare le entità e le relazioni in modo naturale. 

Un [grafo](http://mathworld.wolfram.com/Graph.html) è una struttura composto da [vertici](http://mathworld.wolfram.com/GraphVertex.html) e [archi](http://mathworld.wolfram.com/GraphEdge.html). I vertici e gli archi possono avere un numero arbitrario di proprietà. I vertici identificano gli oggetti distinti, ad esempio una persona, un luogo o un evento. Gli archi indicano le relazioni tra i vertici. Ad esempio, una persona può conoscere un'altra persona, avere partecipato a un evento e/o essersi recata recentemente in un determinato luogo. Le proprietà indicano le informazioni su vertici e archi. Le proprietà di esempio includono un vertice con un nome, un'età e un arco con un timestamp e/o un peso. Ufficialmente questo modello è noto come [grafo di proprietà](https://github.com/tinkerpop/blueprints/wiki/Property-Graph-Model). Azure Cosmos DB supporta il modello di grafo di proprietà. 

Ad esempio, il seguente grafo di esempio mostra la relazione tra le persone, i dispositivi mobili, gli interessi e i sistemi operativi. 

![Database di esempio che mostra persone, dispositivi e interessi](./media/graph-introduction/sample-graph.png) 

I grafi sono utili per comprendere una vasta gamma di set di dati in ambito scientifico, tecnologico e aziendale. I database di grafi consentono di modellare e archiviare i grafi in modo naturale e sono quindi una scelta ottimale in molti scenari. I database di grafi sono in genere database NoSQL poiché questi casi d'uso spesso necessitano anche di un'iterazione rapida e di flessibilità dello schema. 

I grafi offrono una tecnica di modellazione dei dati potente e innovativa. Questo motivo da solo non giustifica l'uso del database di grafi. In molti modelli e casi d'uso relativi agli attraversamenti di grafi, offrono prestazioni superiori ai database SQL e NoSQL tradizionali in ordini di grandezza. Questa differenza a livello di prestazioni aumenta ulteriormente quando si attraversano più relazioni come amico di amico. 

È possibile combinare gli attraversamenti rapidi forniti dai database di grafi con gli algoritmi di grafi, come la ricerca in profondità, la ricerca in ampiezza, l'algoritmo di Dijkstra e così via, per risolvere i problemi in domini differenti, come le social network, la gestione di contenuti, le funzioni geospaziali e le raccomandazioni. 

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Grafi a scalabilità globale con Azure Cosmos DB
Azure Cosmos DB è un database di grafi completamente gestito che consente distribuzione globale, scalabilità elastica in termini di archiviazione e velocità effettiva, query e indicizzazione automatica, livelli di coerenza perfezionabili e supporto per lo standard TinkerPop.  

![Architettura di grafi di Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png) 

Azure Cosmos DB offre le seguenti funzionalità specifiche rispetto agli altri database a grafi disponibili sul mercato: 

* **Scalabilità elastica di archiviazione e velocità effettiva**: la scalabilità dei grafi nel mondo reale deve superare la capacità di un singolo server. Con Azure Cosmos DB la scalabilità dei grafi può essere ottenuta facilmente tra più server. È possibile aumentare la velocità effettiva del grafo anche in modo indipendente in base ai modelli di accesso. Azure Cosmos DB supporta i database di grafi che è possono scalare in misura praticamente illimitata a livello di archiviazione e velocità effettiva con provisioning. 

* **Replica in più aree:** Azure Cosmos DB replica in modo trasparente i dati dei grafi in tutte le aree associate al proprio account, consentendo lo sviluppo di applicazioni che richiedono l'accesso globale ai dati, con un efficace compromesso tra coerenza, disponibilità e prestazioni, il tutto con le garanzie corrispondenti. Azure Cosmos DB fornisce il failover trasparente a livello di area con le API multihosting e la possibilità di aumentare o ridurre la velocità effettiva e le risorse di archiviazione in tutto il mondo.

* **Query e attraversamenti rapidi con la sintassi Gremlin familiare**: archiviare vertici e archi eterogenei ed eseguire query su questi documenti usando la sintassi Gremlin familiare; sarà disponibile a breve anche una sintassi SQL migliorata. Azure Cosmos DB usa una tecnologia di indicizzazione a simultaneità elevata, priva di blocchi, strutturata con log per l'indicizzazione automatica dell'intero contenuto. Ciò permette l'esecuzione di query e attraversamenti avanzati in tempo reale, senza dovere specificare hint di schema, indici secondari o visualizzazioni. Per altre informazioni, vedere [Eseguire query su grafi usando Gremlin](gremlin-support.md).

* **Completamente gestito:** non è più necessario gestire le risorse di database e computer. Questo servizio Microsoft Azure completamente gestito non richiede la gestione di macchine virtuali, la distribuzione e la configurazione di software, la gestione del ridimensionamento o complessi aggiornamenti di livello dati. Il backup di ogni grafo viene eseguito automaticamente e ogni grafo è protetto da errori nell'area geografica specifica. La possibilità di aggiungere con facilità account e capacità di provisioning di Azure Cosmos DB in base alla necessità permette di concentrarsi sull'applicazione invece che sul funzionamento e sulla gestione del database.

* **Indicizzazione automatica:** per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutte le proprietà all'interno di nodi e archi nel grafo e non prevede né richiede schemi o la creazione di indici secondari. 

* **Compatibilità con Gremlin**: Azure Cosmos DB supporta in modo nativo lo standard Gremlin open source e può essere integrato con altri sistemi a grafi abilitati per Gremlin. È quindi possibile migrare facilmente da un altro database di grafi come Titan o Neo4j o usare Azure Cosmos DB con i framework di analisi dei grafi come Apache Spark GraphX.

* **Livelli di coerenza perfezionabili:** è possibile scegliere tra cinque livelli di coerenza ben definiti per ottenere un compromesso ottimale tra coerenza e prestazioni. Per query e operazioni di lettura, Azure Cosmos DB offre cinque livelli di coerenza distinti, ovvero avanzata, con decadimento ristretto, sessione, prefisso coerente e futura. Questi livelli di coerenza granulari e ben definiti permettono di ottenere un compromesso ottimale tra coerenza, disponibilità e latenza. Ulteriori informazioni sono disponibili in [Utilizzo dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni di DocumentDB](../documentdb/documentdb-consistency-levels.md).

Azure Cosmos DB offre anche la possibilità di usare più modelli, come il documento o il grafo all'interno degli stessi contenitori/database. È possibile usare una raccolta di documenti per archiviare i dati del grafo affiancati ai documenti e usare sia le query SQL su JSON sia le query Gremlin per eseguire query sugli stessi dati come grafo. 

## <a name="getting-started"></a>introduttiva
Gli account Azure Cosmos DB possono essere creati tramite l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure con il supporto per l'API Graph. Una volta creati, il portale di Azure fornisce un endpoint di servizio come `https://<youraccount>.graphs.azure.com` che offre un front-end WebSocket per Gremlin. È possibile configurare gli strumenti compatibili con Gremlin come la [console di Gremin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) per connettersi a questo endpoint e compilare applicazioni Java, Node.js o un qualsiasi driver client Gremlin.

La tabella seguente illustra i driver Gremlin comuni che è possibile iniziare a usare in Azure Cosmos DB:

| Scaricare | Documentazione |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[JavaDoc Gremlin](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.js](https://www.npmjs.com/package/gremlin) |[Gremlin-JavaScript in Github](https://github.com/jbmusso/gremlin-javascript) |
| [Console Gremlin](https://tinkerpop.apache.org/downloads.html) |[Documenti TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos DB fornisce anche una libreria .NET di prima entità con metodi di estensione Gremlin oltre agli [SDK di Azure Cosmos DB](../documentdb/documentdb-sdk-dotnet.md) tramite NuGet. Questa libreria fornisce un server Gremlin "in-proc" che può essere usato per connettersi direttamente alle partizioni di dati DocumentDB. 

| Scaricare | Documentazione |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

Usando l'[emulatore Azure Cosmos DB](../documentdb/documentdb-nosql-local-emulator.md), è possibile sviluppare e testare in locale usando l'API Graph, senza creare una sottoscrizione di Azure né sostenere costi. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud.

## <a name="scenarios-for-azure-cosmos-dbs-graph-support"></a>Scenari per il supporto Graph di Azure Cosmos DB
Ecco alcuni scenari in cui è possibile usare il supporto di Graph in Azure Cosmos DB: 

* **Social network**: combinando i dati sui clienti e le relative interazioni con altri utenti, è possibile sviluppare esperienze personalizzate, prevedere il comportamento dei clienti o connettere gli utenti con altri utenti con interessi simili. Azure Cosmos DB consente di gestire le social network e tenere traccia dei dati e delle preferenze dei clienti. 

* **Motori di raccomandazione**: usati comunemente nel settore della vendita al dettaglio. Combinando le informazioni su prodotti, utenti e interazioni di utenti come acquisti, esplorazioni o valutazione di un elemento, è possibile generare raccomandazioni personalizzate. Azure Cosmos DB è ideale per la modellazione di tali interazioni assicurando latenza bassa, scalabilità elastica e supporto nativo dei grafi .

* **Funzioni geospaziali**: molte applicazioni nel settore di telecomunicazioni, logistica e pianificazione di viaggi devono individuare una posizione di interesse all'interno di una determinata area o l'itinerario più breve/ottimale tra due posizioni. Azure Cosmos DB è una scelta ideale in questi scenari. 

* **Internet delle cose**: con la rete e le connessioni tra i dispositivi IoT modellati come grafo, è possibile comprendere meglio lo stato di dispositivi e asset e come le modifiche apportate in un segmento della rete possono influire su un altro segmento. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul supporto di Graph in Azure Cosmos DB, vedere:

* Introduzione all'[esercitazione sui grafi di Azure Cosmos DB](create-graph-dotnet.md)
* Informazioni su come [eseguire query su grafi in Azure Cosmos DB usando Gremlin](gremlin-support.md)

