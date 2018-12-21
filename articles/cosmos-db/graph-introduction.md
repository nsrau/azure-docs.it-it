---
title: Introduzione all'API Gremlin di Azure Cosmos DB
description: Informazioni su come usare Azure Cosmos DB per archiviare, eseguire query e attraversare grafi di grandi dimensioni con bassa latenza usando il linguaggio di query per grafi Gremlin di Apache TinkerPop.
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: overview
ms.date: 09/05/2018
ms.author: lbosq
ms.openlocfilehash: e9e0d2f452a21f2da29568b953238318cb4477df
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077448"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introduzione ad Azure Cosmos DB: API Gremlin

[Azure Cosmos DB](introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Si tratta di un database multimodello che supporta modelli di dati di tipo documento, coppia chiave-valore, grafo e colonna. L'API Gremlin di Azure Cosmos DB consente di archiviare e operare sui dati del grafo. L'API Gremlin supporta la modellazione dei dati di Graph e mette a disposizione le API per attraversare i dati del grafo.

Questo articolo offre una panoramica dell'API Gremlin di Azure Cosmos DB e spiega come usarla per archiviare grafi di grandi dimensioni con miliardi di vertici e bordi. È possibile eseguire query nei grafi con una latenza di un millisecondo ed evolvere facilmente la struttura e lo schema del grafo. Per eseguire query su Azure Cosmos DB, è possibile usare il linguaggio di attraversamento tramite grafi [Apache TinkerPop](https://tinkerpop.apache.org) oppure [Gremlin](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps).

## <a name="what-is-a-graph-database"></a>Che cos'è un database del grafo
I dati presenti nel mondo reale vengono connessi in modo naturale. La modellazione di dati tradizionale si basa sul concetto di entità. Per molte applicazioni sussiste anche la necessità di modellare sia le entità che le relazioni in modo naturale.

Un [grafo](https://mathworld.wolfram.com/Graph.html) è una struttura composta da [vertici](http://mathworld.wolfram.com/GraphVertex.html) e [bordi](http://mathworld.wolfram.com/GraphEdge.html). I vertici e gli archi possono avere un numero arbitrario di proprietà. 

* **Vertici** - I vertici identificano oggetti distinti, ad esempio una persona, un luogo o un evento. 

* **Archi** - Gli archi indicano le relazioni tra i vertici. Ad esempio, una persona potrebbe conoscere un'altra persona, essere coinvolta in un evento e recentemente essersi trovata in una determinata posizione. 

* **Proprietà** - Le proprietà forniscono informazioni su vertici e archi. Le proprietà di esempio includono un vertice con un nome e un'età. Un arco con un timestamp e/o un peso. Ufficialmente questo modello è noto come [grafo di proprietà](https://tinkerpop.apache.org/docs/current/reference/#intro). Azure Cosmos DB supporta il modello di grafo di proprietà.

Il grafo di esempio seguente, ad esempio, mostra le relazioni tra le persone, i dispositivi mobili, gli interessi e i sistemi operativi:

![Database di esempio che mostra persone, dispositivi e interessi](./media/graph-introduction/sample-graph.png)

I database di grafi consentono di modellare e archiviare grafi in modo naturale e sono quindi una scelta ottimale in molti scenari. I database di grafi sono in genere database NoSQL perché questi casi di utilizzo spesso necessitano anche di un'iterazione rapida e di uno schema flessibile.

È possibile combinare gli attraversamenti rapidi forniti dai database di grafi con algoritmi di grafi, ad esempio la ricerca in profondità, la ricerca in ampiezza e l'algoritmo di Dijkstra, per risolvere i problemi in vari domini come i social network, la gestione di contenuti, la geospazialità e le raccomandazioni.

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funzionalità del database di grafi Azure Cosmos DB
 
Azure Cosmos DB è un database di grafi completamente gestito che consente distribuzione globale, scalabilità elastica in termini di archiviazione e velocità effettiva, query e indicizzazione automatica, livelli di coerenza regolabili e supporto per lo standard TinkerPop.

![Architettura di grafi di Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos DB offre le seguenti funzionalità specifiche rispetto ad altri database di grafi disponibili sul mercato:

* Archiviazione e velocità effettiva altamente scalabile

 La scalabilità dei grafi nel mondo reale deve superare la capacità di un singolo server. Con Azure Cosmos DB la scalabilità dei grafi può essere ottenuta facilmente tra più server. È anche possibile scalare la velocità effettiva del grafo in modo indipendente in base ai modelli di accesso. Azure Cosmos DB supporta i database di grafi che è possono scalare in misura praticamente illimitata a livello di archiviazione e velocità effettiva con provisioning.

* Replica di tipo multiarea

 Azure Cosmos DB replica in modo trasparente i dati del grafo in tutte le aree associate all'account. La replica consente di sviluppare applicazioni che richiedono l'accesso ai dati globale. Sussistono compromessi in termini di coerenza, disponibilità e prestazioni e garanzie corrispondenti. Azure Cosmos DB fornisce failover regionale trasparente con API multihosting. È possibile scalare in modo elastico la velocità effettiva e l'archiviazione su scala globale.

* Query rapide e attraversamenti con la sintassi Gremlin nota

 Archiviare bordi e vertici eterogenei ed eseguire query nei documenti tramite una sintassi Gremlin familiare. Azure Cosmos DB usa una tecnologia di indicizzazione a simultaneità elevata, priva di blocchi e strutturata a log per indicizzare automaticamente l'intero contenuto. Questa funzionalità consente di eseguire attraversamenti e query avanzati in tempo reale senza la necessità di specificare hint di schema, indici secondari o visualizzazioni. Per altre informazioni, vedere [Eseguire query sui grafi usando Gremlin](gremlin-support.md).

* Soluzione completamente gestita

 Azure Cosmos DB elimina la necessità di gestire le risorse di database e computer. Questo servizio di Microsoft Azure completamente gestito non richiede la gestione di macchine virtuali, la distribuzione e la configurazione di software, la gestione della scalabilità o di complessi aggiornamenti a livello dati. Il backup di ogni grafo viene eseguito automaticamente e ogni grafo è protetto da errori nell'area geografica specifica. È possibile aggiungere con facilità un account Azure Cosmos DB ed eseguire il provisioning della capacità in base alle esigenze in modo da potersi concentrare sull'applicazione anziché sul funzionamento e sulla gestione del database.

* Indicizzazione automatica

 Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutte le proprietà all'interno di nodi e bordi nel grafo e non prevede né richiede schemi o la creazione di indici secondari.

* Compatibilità con Apache TinkerPop

 Azure Cosmos DB supporta in modo nativo lo standard Gremlin open source e può essere integrato con altri sistemi a grafi che supportano TinkerPop. In questo modo, è possibile migrare con facilità da un altro database di grafi come Titan o Neo4j o usare Azure Cosmos DB con framework di analisi dei grafi come Apache Spark GraphX.

* Livelli di coerenza regolabili

 È possibile scegliere tra cinque livelli di coerenza ben definiti per ottenere un compromesso ottimale tra coerenza e prestazioni. Per query e operazioni di lettura, Azure Cosmos DB offre cinque livelli di coerenza distinti, ovvero avanzata, con decadimento ristretto, sessione, prefisso coerente e futura. Questi livelli di coerenza granulari e ben definiti permettono di ottenere un compromesso ottimale tra coerenza, disponibilità e latenza. Per altre informazioni, vedere [Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB](consistency-levels.md).

Azure Cosmos DB consente anche di usare più modelli, come il documento e il grafo all'interno degli stessi contenitori/database. È possibile usare un contenitore di documenti per archiviare i dati del grafico in modo affiancato con documenti. È possibile utilizzare query SQL su JSON e query Gremlin per eseguire query sugli stessi dati come grafo.

## <a name="get-started"></a>Attività iniziali

È possibile usare l'interfaccia della riga di comando di Azure (CLI), Azure PowerShell o il portale di Azure per creare un account API Gremlin di Azure Cosmos DB e accedervi. Dopo aver creato un account, è possibile accedere ai database di grafi all'interno di tale account tramite un endpoint del servizio API Gremlin `https://<youraccount>.gremlin.cosmosdb.azure.com`, che offre un front-end WebSocket per Gremlin. È possibile configurare gli strumenti compatibili con TinkerPop, come la [console di Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console), per connettersi a questo endpoint e compilare applicazioni in Java, Node.js o un qualsiasi driver client Gremlin.

La tabella seguente illustra i driver Gremlin noti che è possibile usare in Azure Cosmos DB:

| Download | Documentazione | Introduzione | Versione connettore supportata |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET su GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Creare un'app Graph con .NET](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [JavaDoc Gremlin](https://tinkerpop.apache.org/javadocs/current/full/) | [Creare un'app Graph con Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript in GitHub](https://github.com/jbmusso/gremlin-javascript) | [Creare un'app Graph con Node.js](create-graph-nodejs.md) | 2.6.0|
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python in GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Creare un'app Graph con Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP su GitHub](https://github.com/PommeVerte/gremlin-php) | [Creare un'app Graph con PHP](create-graph-php.md) | 3.1.0 |
| [Console Gremlin](https://tinkerpop.apache.org/downloads.html) | [Documenti TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Creare un'app Graph con la console Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="graph-database-design-considerations"></a>Considerazioni sulla progettazione del database di grafi

Durante la progettazione del grafo, la decisione di modellare un'entità come vertice indipendente anziché come proprietà di altre entità del vertice ha implicazioni sulle prestazioni e sui costi. La motivazione principale per questa decisione si basa su come i dati verranno sottoposti a query, nonché sulla scalabilità del modello stesso.

Prima di pianificare come modellare l'entità, è consigliabile rispondere alle domande seguenti:

* Quali entità devono essere recuperate come vertici per la maggior parte delle query?

* Quali sono le informazioni incluse nel grafo che vengono aggiunte allo scopo di filtrare i dati?

* Quali entità sono semplici connessioni ad altre entità, che quindi vengono recuperate per i relativi valori?

* Quali informazioni deve recuperare la query e qual è il costo delle UR che verranno generate?

Si supponga, ad esempio, la progettazione del grafo seguente:

![Esempi di considerazioni sulla progettazione del grafo](./media/graph-introduction/graph-design-considerations-example.png)

* A seconda delle query, è possibile che la relazione District->Store venga usata solo per filtrare i vertici Store, ad esempio, se le query sono nel formato: "ottenere tutti i punti vendita che appartengono a una zona specifica". In questo caso, vale la pena di considerare la compressione dell'entità District da un vertice proprio a una proprietà del vertice Store. 

* Questo approccio presenta il vantaggio di ridurre i costi di recupero di ogni vertice Store ottenendo tre oggetti grafo alla volta (District, District->Store, Store) in un singolo vertice Store. Ciò può contribuire a migliorare le prestazioni e a ridurre il costo unitario delle query,

* perché il vertice Store si collega a due diverse entità, Employee e Product. Ciò rende Store un vertice necessario, in quanto può offrire altre possibilità di attraversamento.  



## <a name="scenarios-that-can-use-gremlin-api"></a>Scenari in cui è possibile usare l'API Gremlin
Ecco alcuni scenari in cui è possibile usare il supporto di Graph di Azure Cosmos DB:

* Social network

 Combinando i dati sui clienti e le relative interazioni con altri utenti, è possibile sviluppare esperienze personalizzate, prevedere il comportamento dei clienti o connettere gli utenti ad altri utenti con interessi simili. Azure Cosmos DB consente di gestire le social network e tenere traccia dei dati e delle preferenze dei clienti.

* Motore di raccomandazione

 Questo scenario viene comunemente usato nel settore della vendita al dettaglio. Combinando informazioni su prodotti, utenti e interazioni utente come acquisti, esplorazioni o valutazioni di un elemento, è possibile generare raccomandazioni personalizzate. La bassa latenza, il ridimensionamento elastico, il supporto nativo per grafi di Azure Cosmos DB è ideale per la modellazione di queste interazioni.

* GeoSpatial

 Molte applicazioni nel settore delle telecomunicazioni, della logistica e della pianificazione di viaggi devono individuare una posizione di interesse all'interno di una determinata area oppure trovare l'itinerario più breve/ottimale tra due posizioni. Azure Cosmos DB è una scelta ideale in questi scenari.

* Internet delle cose

 Con la rete e le connessioni tra dispositivi IoT modellati come grafo, è possibile comprendere meglio lo stato dei dispositivi e degli asset e come le modifiche apportate in una parte della rete possono potenzialmente influire su un'altra parte.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul supporto di Graph in Azure Cosmos DB, vedere:

* Introduzione all'[esercitazione sui grafi di Azure Cosmos DB](create-graph-dotnet.md).
* Informazioni su come [eseguire query sui grafi in Azure Cosmos DB usando Gremlin](gremlin-support.md).
