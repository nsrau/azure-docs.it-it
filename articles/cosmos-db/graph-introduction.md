---
title: Introduzione all'API Gremlin di Azure Cosmos DB
description: Informazioni su come usare Azure Cosmos DB per archiviare, eseguire query e attraversare grafi di grandi dimensioni con bassa latenza usando il linguaggio di query per grafi Gremlin di Apache TinkerPop.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/10/2020
ms.author: jasonh
ms.openlocfilehash: 72f40a980a2f7039d036ca14c549bae8ab45abb2
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279847"
---
# <a name="introduction-to-gremlin-api-in-azure-cosmos-db"></a>Introduzione all'API Gremlin in Azure Cosmos DB

[Azure Cosmos DB](introduction.md)  è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni mission-critical. Si tratta di un database multimodello che supporta modelli di dati basati su documenti, coppie chiave-valore, grafi e famiglie di colonne. "Azure Cosmos DB offre un servizio di database a grafo tramite l'API Gremlin in un servizio di database completamente gestito progettato per qualsiasi scala.  

:::image type="content" source="./media/graph-introduction/cosmosdb-graph-architecture.png" alt-text="Architettura di grafi di Azure Cosmos DB" border="false":::

Questo articolo offre una panoramica dell'API Gremlin di Azure Cosmos DB e descrive come usarla per archiviare grafi di grandi dimensioni con miliardi di vertici e archi. È possibile eseguire query nei grafi con una latenza di un millisecondo ed evolvere facilmente la struttura del grafo. L'API Gremlin di Azure Cosmos DB è basata su [Apache TinkerPop](https://tinkerpop.apache.org), un framework di elaborazione dei grafi. L'API Gremlin di Azure Cosmos DB usa il linguaggio di query Gremlin.

L'API Gremlin di Azure Cosmos DB combina le potenzialità degli algoritmi per database a grafo con un'infrastruttura gestita a scalabilità elevata per offrire una soluzione univoca e flessibile per i problemi relativi ai dati più comuni associati alla mancanza di flessibilità e agli approcci relazionali.

## <a name="features-of-azure-cosmos-dbs-gremlin-api"></a>Funzionalità dell'API Gremlin di Azure Cosmos DB
 
Azure Cosmos DB è un database di grafi completamente gestito che consente distribuzione globale, scalabilità elastica in termini di archiviazione e velocità effettiva, query e indicizzazione automatica, livelli di coerenza regolabili e supporto per lo standard TinkerPop.

Di seguito vengono descritte le varie funzionalità offerte dall'API Gremlin di Azure Cosmos DB:

* **Archiviazione e velocità effettiva con scalabilità elastica**

  La scalabilità dei grafi nel mondo reale deve superare la capacità di un singolo server. Azure Cosmos DB supporta database a grafo con scalabilità orizzontale che possono avere dimensioni virtualmente illimitate in termini di archiviazione e velocità effettiva con provisioning. Con l'aumentare delle dimensioni del database a grafo, i dati verranno distribuiti automaticamente tramite [partizionamento del grafo](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replica in più aree**

  Azure Cosmos DB può replicare automaticamente i dati dei grafi in qualsiasi area di Azure in tutto il mondo. La replica globale semplifica lo sviluppo delle applicazioni che richiedono l'accesso globale ai dati. Oltre a ridurre al minimo la latenza di lettura e scrittura in tutto il mondo, Azure Cosmos DB offre un meccanismo di failover automatico dell'area in grado di assicurare la continuità dell'applicazione nella rara eventualità di un'interruzione del servizio in un'area.

* **Query e attraversamenti rapidi con lo standard per query su grafo più diffuso**

  Archiviare vertici e archi eterogenei ed eseguire query su di essi usando una sintassi Gremlin già nota. Gremlin è un linguaggio di query imperativo e funzionale che offre un'interfaccia avanzata per implementare gli algoritmi per i grafi comuni.
  
  Azure Cosmos DB permette l'esecuzione di query e attraversamenti avanzati in tempo reale, senza dovere specificare hint di schema, indici secondari o viste. Per altre informazioni, vedere [Eseguire query sui grafi usando Gremlin](gremlin-support.md).

* **Database a grafo completamente gestito**

  Azure Cosmos DB elimina la necessità di gestire le risorse di database e computer. La maggior parte delle piattaforme esistenti per database a grafo è vincolata dalle limitazioni dell'infrastruttura e spesso richiede un elevato livello di manutenzione per garantire il funzionamento. 
  
  Trattandosi di un servizio completamente gestito, con Cosmos DB non sono richieste operazioni quali la gestione di macchine virtuali, l'aggiornamento del software di runtime, la gestione del partizionamento orizzontale o della replica o di aggiornamenti complessi del livello dati. Il backup di ogni grafo viene eseguito automaticamente e ogni grafo è protetto da errori nell'area geografica specifica. In questo modo gli sviluppatori possono concentrarsi sugli aspetti che consentono di offrire valore con l'applicazione invece di preoccuparsi della gestione e della manutenzione dei database a grafo. 

* **Indicizzazione automatica**

  Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutte le proprietà all'interno di nodi (anche detti vertici) e archi nel grafo e non prevede né richiede schemi o la creazione di indici secondari. Altre informazioni sull'[indicizzazione in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview).

* **Compatibilità con Apache TinkerPop**

  Azure Cosmos DB supporta lo [standard open source Apache TinkerPop](https://tinkerpop.apache.org/). Lo standard Tinkerpop include un ampio ecosistema di applicazioni e librerie che possono essere facilmente integrate con l'API Gremlin di Azure Cosmos DB.

* **Livelli di coerenza regolabili**

  Azure Cosmos DB consente di scegliere tra cinque livelli di coerenza ben definiti, per ottenere il giusto compromesso tra coerenza e prestazioni dell'applicazione. Per query e operazioni di lettura, Azure Cosmos DB offre cinque livelli di coerenza distinti, ovvero avanzata, con decadimento ristretto, sessione, prefisso coerente e futura. Questi livelli di coerenza granulari e ben definiti permettono di ottenere un compromesso ottimale tra coerenza, disponibilità e latenza. Per altre informazioni, vedere [Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-use-gremlin-api"></a>Scenari in cui è si usa l'API Gremlin

Ecco alcuni scenari in cui il supporto di Graph di Azure Cosmos DB può risultare utile:

* **Social network/Customer 365**

  Combinando i dati sui clienti e le relative interazioni con altri utenti, è possibile sviluppare esperienze personalizzate, prevedere il comportamento dei clienti o connettere gli utenti ad altri utenti con interessi simili. Azure Cosmos DB consente di gestire le social network e tenere traccia dei dati e delle preferenze dei clienti.

* **Motori di raccomandazione**

  Questo scenario viene comunemente usato nel settore della vendita al dettaglio. Combinando informazioni su prodotti, utenti e interazioni utente come acquisti, esplorazioni o valutazioni di un elemento, è possibile generare raccomandazioni personalizzate. La bassa latenza, il ridimensionamento elastico, il supporto nativo per grafi fanno di Azure Cosmos DB la scelta ideale per questi scenari.

* **Geospaziale**

  Molte applicazioni nel settore delle telecomunicazioni, della logistica e della pianificazione di viaggi devono individuare una posizione di interesse all'interno di una determinata area oppure trovare l'itinerario più breve/ottimale tra due posizioni. Azure Cosmos DB è una scelta ideale in questi scenari.

* **Internet delle cose**

  Con la rete e le connessioni tra dispositivi IoT modellati come grafo, è possibile comprendere meglio lo stato dei dispositivi e degli asset e come le modifiche apportate in una parte della rete possono potenzialmente influire su un'altra parte.

## <a name="introduction-to-graph-databases"></a>Introduzione ai database a grafo

I dati presenti nel mondo reale vengono connessi in modo naturale. La modellazione dei dati tradizionale è incentrata sulla definizione di entità separatamente e sul calcolo delle rispettive relazioni in fase di esecuzione. Anche se questo modello presenta dei vantaggi, la gestione dei vincoli posti da dati molto connessi può risultare complessa.  

L'approccio con database a grafo si basa invece su relazioni persistenti nel livello archiviazione, con conseguente efficienza elevata delle operazioni di recupero del grafo. L'API Gremlin di Azure Cosmos DB supporta il [modello di grafo di proprietà](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Oggetti del grafo di proprietà

Un [grafo](http://mathworld.wolfram.com/Graph.html) di proprietà è una struttura composta da [vertici](http://mathworld.wolfram.com/GraphVertex.html) e [archi](http://mathworld.wolfram.com/GraphEdge.html). Entrambi gli oggetti possono avere un numero arbitrario di coppie chiave-valore come proprietà. 

* **Vertici/nodi** - I vertici identificano entità discrete, ad esempio una persona, un luogo o un evento.

* **Archi/relazioni** - Gli archi indicano le relazioni tra i vertici. Ad esempio, una persona potrebbe conoscere un'altra persona, essere coinvolta in un evento e recentemente essersi trovata in una determinata posizione.

* **Proprietà** - Le proprietà forniscono informazioni su vertici e archi. Può esistere un numero qualsiasi di proprietà nei vertici o negli archi ed è possibile usarle per descrivere e filtrare gli oggetti in una query. Sono esempi di proprietà un vertice con nome ed età o un arco con un timestamp e/o un peso.

* **Etichetta** - Un'etichetta è un nome o l'identificatore di un vertice o di un arco. Le etichette possono raggruppare più vertici o archi in modo che tutti quelli di un gruppo abbiano una specifica etichetta. Ad esempio, un grafo può includere più vertici con il tipo di etichetta "persona".

I database a grafo sono spesso inclusi all'interno di NoSQL, ovvero la categoria di database non relazionali, perché non esiste alcuna dipendenza da uno schema o un modello di dati vincolato. Questa mancanza dello schema consente la modellazione e l'archiviazione di strutture collegate in modo naturale ed efficiente.

### <a name="graph-database-by-example"></a>Database a grafo di esempio

Verrà ora usato un grafo di esempio per comprendere come le query possono essere espresse in Gremlin. La figura seguente illustra un'applicazione aziendale che gestisce i dati su utenti, interessi e dispositivi sotto forma di grafo.  

:::image type="content" source="./media/gremlin-support/sample-graph.png" alt-text="Architettura di grafi di Azure Cosmos DB" border="false"::: 

Questo grafo presenta i tipi di *vertice* seguenti (anche detti "etichetta" in Gremlin):

* **Persone** : il grafo include tre persone, Robin, Thomas e Ben
* **Interessi** : i loro interessi, in questo esempio, sono rappresentati dal gioco del football
* **Dispositivi** : i dispositivi usati dalle persone
* **Sistemi operativi** : i sistemi operativi eseguiti nei dispositivi
* **Posizione** : le posizioni da cui si accede ai dispositivi

Per rappresentare le relazioni tra queste entità si usano i tipi di *archi* seguenti:

* **Conosce** : ad esempio, "Thomas conosce Robin"
* **Interessato** : per rappresentare gli interessi delle persone nel nostro grafo, ad esempio, "Ben è interessato al football"
* **RunsOS** : il portatile esegue il sistema operativo Windows
* **Usa** : per rappresentare quale dispositivo viene usato da una persona. Ad esempio, Robin usa un telefono Motorola con numero di serie 77
* **Posizionato** : per rappresentare la posizione da cui si accede ai dispositivi

La console Gremlin è un terminale interattivo fornito da Apache TinkerPop, che si usa per interagire con i dati del grafo. Per altre informazioni, vedere la documentazione di avvio rapido su [come usare la console Gremlin](create-graph-gremlin-console.md). È anche possibile eseguire queste operazioni usando i driver Gremlin nella piattaforma di propria scelta (Java, Node.js, Python o .NET). Gli esempi seguenti illustrano come eseguire query sui dati di questo grafo usando la console Gremlin.

Verrà dapprima esaminato CRUD. L'istruzione Gremlin seguente inserisce il vertice "Thomas" nel grafo:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Successivamente, l'istruzione Gremlin seguente inserisce un arco "conosce" tra Thomas e Robin.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

La query seguente restituisce i vertici "persona" secondo l'ordine decrescente dei relativi nomi:

```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

I grafi sono eccellenti quando è necessario rispondere a domande come "Quali sistemi operativi usano gli amici di Thomas?". È possibile eseguire questo attraversamento Gremlin per ottenere informazioni dal grafo:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul supporto di Graph in Azure Cosmos DB, vedere:

* Introduzione all'[esercitazione sui grafi di Azure Cosmos DB](create-graph-dotnet.md).
* Informazioni su come [eseguire query sui grafi in Azure Cosmos DB usando Gremlin](gremlin-support.md).
