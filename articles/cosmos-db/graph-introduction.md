---
title: Introduzione alle API Graph di Azure Cosmos DB | Documentazione Microsoft
description: Ulteriori informazioni su come usare Azure Cosmos DB per archiviare, eseguire query e attraversare grafi con bassa latenza usando il linguaggio di query per grafi Gremlin di Apache TinkerPop.
services: cosmos-db
author: dennyglee
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/21/2017
ms.author: denlee
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 4ee52c2665d5856cb0022365edf0485458b629fe
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017

---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Introduzione ad Azure Cosmos DB: API Graph

[Azure Cosmos DB](introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni mission-critical. Azure Cosmos DB offre [distribuzione globale predefinita](distribute-data-globally.md), [scalabilità elastica in termini di archiviazione e velocità effettiva](partition-data.md) ovunque nel mondo, latenze pari a singole unità di millisecondi al 99° percentile, [cinque livelli di coerenza ben definiti](consistency-levels.md) e disponibilità elevata garantita, il tutto supportato da [contratti di servizio leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indicizza automaticamente i dati](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) senza che sia necessario gestire manualmente indici e schemi. Si tratta di un database multimodello che supporta modelli di dati di documenti, coppie chiave/valore, grafi e colonne.

![Gremlin, Graph e Azure Cosmos DB](./media/graph-introduction/graph-gremlin.png)

L'API Graph di Azure Cosmos DB fornisce:

- Modellazione di grafi
- API di attraversamento
- Distribuzione globale chiavi in mano
- Ridimensionamento elastico di archiviazione e velocità effettiva con latenze di lettura inferiori a 10 ms e inferiori a 15 ms al 99° percentile
- Indicizzazione automatica con disponibilità di query immediata
- Livelli di coerenza regolabili
- Contratti di servizio completi con disponibilità al 99,99%

Per eseguire query in DB Cosmos Azure, è possibile utilizzare il linguaggio di attraversamento grafo [Apache TinkerPop](http://tinkerpop.apache.org), [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) o altri sistemi di grafi compatibili con TinkerPop come [Apache Spark GraphX](spark-connector-graph.md).

Questo articolo fornisce una panoramica dell'API Graph di Azure Cosmos DB e spiega come usarla per archiviare grafi di grandi dimensioni con miliardi di vertici e bordi. È possibile eseguire query nei grafi con una latenza di un millisecondo ed evolvere facilmente la struttura e lo schema del grafo.

## <a name="graph-database"></a>Database del grafo
I dati presenti nel mondo reale vengono connessi in modo naturale. La modellazione di dati tradizionale si basa sul concetto di entità. Per molte applicazioni, sussiste anche la necessità di modellare entrambe le entità e le relazioni in modo naturale.

Un [grafo](http://mathworld.wolfram.com/Graph.html) è una struttura composta da [vertici](http://mathworld.wolfram.com/GraphVertex.html) e [bordi](http://mathworld.wolfram.com/GraphEdge.html). I vertici e gli archi possono avere un numero arbitrario di proprietà. I vertici identificano gli oggetti distinti, ad esempio una persona, un luogo o un evento. Gli archi indicano le relazioni tra i vertici. Ad esempio, una persona potrebbe conoscere un'altra persona, essere coinvolta in un evento e recentemente essersi trovata in una determinata posizione. Le proprietà indicano le informazioni su vertici e archi. Le proprietà di esempio includono un vertice con un nome, un'età e un bordo con un indicatore data e ora e/o un peso. Ufficialmente questo modello è noto come [grafo di proprietà](http://tinkerpop.apache.org/docs/current/reference/#intro). Azure Cosmos DB supporta il modello di grafo di proprietà.

Ad esempio, il grafo di esempio seguente mostra la relazione tra le persone, i dispositivi mobili, gli interessi e i sistemi operativi.

![Database di esempio che mostra persone, dispositivi e interessi](./media/graph-introduction/sample-graph.png)

I grafi sono utili per comprendere un'ampia gamma di set di dati in ambito scientifico, tecnologico e commerciale. I database di grafi consentono di modellare e archiviare grafi in modo naturale e sono quindi una scelta ottimale in molti scenari. I database di grafi sono in genere database NoSQL perché questi casi di utilizzo spesso necessitano anche di un'iterazione rapida e di uno schema flessibile.

I grafi offrono una tecnica di modellazione dei dati potente e innovativa. Questo fatto da solo non giustifica l'uso di un database di grafi. Per molti casi di utilizzo e pattern che includono attraversamenti di grafi, questi ultimi offrono prestazioni migliori rispetto ai database NoSQL e SQL tradizionali di parecchi ordini di grandezza. Questa differenza a livello di prestazioni aumenta ulteriormente quando si attraversano più relazioni, ad esempio un amico di un amico.

È possibile combinare gli attraversamenti rapidi forniti dai database di grafi con algoritmi di grafi, ad esempio la ricerca in profondità, la ricerca in ampiezza, l'algoritmo di Dijkstra e così via, per risolvere i problemi in domini diversi come i social, la gestione di contenuti, la geospazialità e le raccomandazioni.

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Grafi a scalabilità globale con Azure Cosmos DB
Azure Cosmos DB è un database di grafi completamente gestito che consente distribuzione globale, scalabilità elastica in termini di archiviazione e velocità effettiva, query e indicizzazione automatica, livelli di coerenza regolabili e supporto per lo standard TinkerPop.  

![Architettura di grafi di Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos DB offre le seguenti funzionalità specifiche rispetto ad altri database di grafi disponibili sul mercato:

* Archiviazione e velocità effettiva altamente scalabile

 La scalabilità dei grafi nel mondo reale deve superare la capacità di un singolo server. Con Azure Cosmos DB la scalabilità dei grafi può essere ottenuta facilmente tra più server. È anche possibile scalare la velocità effettiva del grafo in modo indipendente in base ai modelli di accesso. Azure Cosmos DB supporta i database di grafi che è possono scalare in misura praticamente illimitata a livello di archiviazione e velocità effettiva con provisioning.

* Replica di tipo multiarea

 Azure Cosmos DB replica in modo trasparente i dati del grafo in tutte le aree associate all'account. La replica consente di sviluppare applicazioni che richiedono l'accesso ai dati globale. Sussistono compromessi in termini di coerenza, disponibilità e prestazioni e garanzie corrispondenti. Azure Cosmos DB fornisce failover regionale trasparente con API multihosting. È possibile scalare in modo elastico la velocità effettiva e l'archiviazione su scala globale.

* Query rapide e attraversamenti con la sintassi Gremlin nota

 Archiviare bordi e vertici eterogenei ed eseguire query nei documenti tramite una sintassi Gremlin familiare. Azure Cosmos DB usa una tecnologia di indicizzazione a simultaneità elevata, priva di blocchi e strutturata a log per indicizzare automaticamente l'intero contenuto. Questa funzionalità consente di eseguire attraversamenti e query avanzati in tempo reale senza la necessità di specificare hint di schema, indici secondari o visualizzazioni. Per altre informazioni, vedere [Eseguire query su grafi usando Gremlin](gremlin-support.md).

* Soluzione completamente gestita

 Azure Cosmos DB elimina la necessità di gestire le risorse di database e computer. Questo servizio Microsoft Azure completamente gestito non richiede la gestione di macchine virtuali, la distribuzione e la configurazione di software, la gestione del ridimensionamento o complessi aggiornamenti di livello dati. Il backup di ogni grafo viene eseguito automaticamente e ogni grafo è protetto da errori nell'area geografica specifica. È possibile aggiungere con facilità un account Azure Cosmos DB ed eseguire il provisioning della capacità in base alle esigenze in modo da potersi concentrare sull'applicazione anziché sul funzionamento e sulla gestione del database.

* Indicizzazione automatica

 Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutte le proprietà all'interno di nodi e bordi nel grafo e non prevede né richiede schemi o la creazione di indici secondari.

* Compatibilità con Apache TinkerPop

 Azure Cosmos DB supporta in modo nativo lo standard Gremlin open source e può essere integrato con altri sistemi a grafi che supportano TinkerPop. In questo modo, è possibile migrare con facilità da un altro database di grafi come Titan o Neo4j o usare Azure Cosmos DB con framework di analisi dei grafi come [Apache Spark GraphX](spark-connector-graph.md).

* Livelli di coerenza regolabili

 È possibile scegliere tra cinque livelli di coerenza ben definiti per ottenere un compromesso ottimale tra coerenza e prestazioni. Per query e operazioni di lettura, Azure Cosmos DB offre cinque livelli di coerenza distinti, ovvero avanzata, con decadimento ristretto, sessione, prefisso coerente e futura. Questi livelli di coerenza granulari e ben definiti permettono di ottenere un compromesso ottimale tra coerenza, disponibilità e latenza. Ulteriori informazioni sono disponibili in [Utilizzo dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni di DocumentDB](consistency-levels.md).

Azure Cosmos DB consente anche di usare più modelli, come il documento e il grafo all'interno degli stessi contenitori/database. È possibile usare una raccolta di documenti per archiviare i dati del grafico in modo affiancato con documenti. È possibile utilizzare query SQL su JSON e query Gremlin per eseguire query sugli stessi dati come grafo.

## <a name="getting-started"></a>Introduzione
È possibile usare l'interfaccia della riga di comando di Azure (CLI), Azure Powershell o il portale di Azure con il supporto per l'API Graph per creare account di Azure Cosmos DB. Dopo aver creato gli account, il portale di Azure fornisce un endpoint di servizio `https://<youraccount>.graphs.azure.com` che offre un front-end WebSocket per Gremlin. È possibile configurare gli strumenti compatibili con TinkerPop come la [console di Gremlin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) per connettersi a questo endpoint e compilare applicazioni in Java, Node.js o un qualsiasi driver client Gremlin.

La tabella seguente illustra i driver Gremlin noti che è possibile usare in Azure Cosmos DB:

| Scaricare | Documentazione |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[JavaDoc Gremlin](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.js](https://www.npmjs.com/package/gremlin) |[Gremlin-JavaScript in Github](https://github.com/jbmusso/gremlin-javascript) |
| [Console Gremlin](https://tinkerpop.apache.org/downloads.html) |[Documenti TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos DB fornisce anche una libreria .NET con metodi di estensione Gremlin oltre agli [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) tramite NuGet. Questa libreria fornisce un server Gremlin "in-process" che è possibile usare per connettersi direttamente alle partizioni di dati DocumentDB.

| Scaricare | Documentazione |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

Usando l'[emulatore Azure Cosmos DB](local-emulator.md), è possibile usare l'API Graph per sviluppare e testare in locale senza creare una sottoscrizione di Azure né sostenere costi. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud.

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Scenari per il supporto Graph di Azure Cosmos DB
Ecco alcuni scenari in cui è possibile usare il supporto di Graph di Azure Cosmos DB:

* Social network

 Combinando i dati sui clienti e le relative interazioni con altri utenti, è possibile sviluppare esperienze personalizzate, prevedere il comportamento dei clienti o connettere gli utenti ad altri utenti con interessi simili. Azure Cosmos DB consente di gestire le social network e tenere traccia dei dati e delle preferenze dei clienti.

* Motore di raccomandazione

 Questo scenario viene comunemente usato nel settore della vendita al dettaglio. Combinando informazioni su prodotti, utenti e interazioni utente come acquisti, esplorazioni o valutazioni di un elemento, è possibile generare raccomandazioni personalizzate. La bassa latenza, il ridimensionamento elastico, il supporto nativo per grafi di Azure Cosmos DB è ideale per la modellazione di queste interazioni.

* GeoSpatial

 Molte applicazioni nel settore delle telecomunicazioni, della logistica e della pianificazione di viaggi devono individuare una posizione di interesse all'interno di una determinata area oppure trovare l'itinerario più breve/ottimale tra due posizioni. Azure Cosmos DB è una scelta ideale in questi scenari.

* Internet delle cose

 Con la rete e le connessioni tra dispositivi IoT modellati come grafo, è possibile comprendere meglio lo stato di dispositivi e asset e come le modifiche apportate in un segmento della rete possono potenzialmente influire su un altro segmento.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul supporto di Graph in Azure Cosmos DB, vedere:

* Introduzione all'[esercitazione sui grafi di Azure Cosmos DB](create-graph-dotnet.md).
* Scopri come [eseguire query sui grafi in Azure Cosmos DB usando Gremlin](gremlin-support.md).

