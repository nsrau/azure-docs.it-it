---
title: Ottimizzare le unità di richiesta e i costi per l'esecuzione di query in Azure Cosmos DB
description: Informazioni su come valutare gli addebiti delle unità di richiesta per una query e ottimizzare la query in termini di prestazioni e costi.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: cb85d09a1d5dee6cb54254baac4698cdad093785
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457667"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Ottimizzare il costo delle query in Azure Cosmos DB

Azure Cosmos DB offre un set completo di operazioni di database, incluse query relazionali e gerarchiche che operano sugli elementi in un contenitore. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie a eseguire diverse operazioni di database e rispondere a una richiesta. Questo articolo illustra come valutare gli addebiti delle unità di richiesta per una query e ottimizzare la query in termini di prestazioni e costi. 

Le query in Azure Cosmos DB vengono in genere ordinate dalla più veloce/più efficiente alla più lenta/meno efficiente in termini di velocità effettiva, come indicato di seguito:  

* Operazione get su una singola chiave di partizione e chiave di elemento.

* Query con una clausola di filtro in una singola chiave di partizione.

* Query senza una clausola di filtro di uguaglianza o intervallo su qualsiasi proprietà.

* Query senza filtri.

Le query che leggono i dati da una o più partizioni comportano una latenza maggiore e usano un numero maggiore di unità richiesta. Poiché ogni partizione dispone dell'indicizzazione automatica per tutte le proprietà, la query può essere fornita in modo efficiente dall'indice. È possibile eseguire più velocemente le query che interessano partizioni multiple usando le opzioni di parallelismo. Per altre informazioni sul partizionamento e sulle chiavi di partizione, vedere [Partizionamento in Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Valutare l'addebito delle unità richiesta per una query

Dopo che sono stati memorizzati alcuni dati nei contenitori di Azure Cosmos, è possibile usare Esplora dati nel portale di Azure per creare ed eseguire le query. È anche possibile ottenere il costo delle query usando Esplora dati. Questo metodo offre un'idea dei costi effettivi coinvolti con le query e le operazioni tipiche che il sistema supporta.

È anche possibile ottenere il costo delle query a livello programmatico tramite gli SDK. Per misurare l'overhead di qualunque operazione, ad esempio creazione, aggiornamento o eliminazione, ispezionare l'intestazione `x-ms-request-charge` quando si usa l'API REST. Se si usa l'SDK .net o Java, la proprietà `RequestCharge` è la proprietà equivalente per ottenere il costo della richiesta ed è presente all'interno di ResourceResponse o FeedResponse.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Fattori che incidono sul costo delle unità richiesta per una query

Le unità richiesta per le query dipendono da numerosi fattori. Ad esempio, il numero di elementi di Azure Cosmos caricati/restituiti, il numero di ricerche in base all'indice, il tempo di compilazione di query e così via. Azure Cosmos DB assicura che la stessa query, se eseguita sugli stessi dati, consumi sempre lo stesso numero di UR, anche con esecuzioni ripetute. Il profilo di query mediante le metriche di esecuzione di query offre una buona idea di come vengano impiegate le unità richiesta.  

In alcuni casi è possibile visualizzare una sequenza di 200 e 429 risposte e unità di richiesta di variabili in un'esecuzione di paging di query, poiché le query verranno eseguite il più velocemente possibile in base alle unità richiesta disponibili. È possibile che l'esecuzione di query venga suddivisa in più pagine/round trip tra client e server. Ad esempio, 10.000 elementi possono essere restituiti come più pagine e addebitati in base al calcolo eseguito per la rispettiva pagina. Quando si esegue la somma tra queste pagine, si otterrà lo stesso numero di unità richiesta dell'intera query.  

## <a name="metrics-for-troubleshooting"></a>Metriche per la risoluzione dei problemi

Le prestazioni e la velocità effettiva usata principalmente dalle query e dalle funzioni definite dall'utente (UDF) dipende dal corpo della funzione. Il modo più semplice per scoprire quanto tempo viene impiegato dall'esecuzione della query per la funzione definita dall'utente e il numero di unità richiesta consumate, consiste nell'abilitare le metriche di query. Se si usa l'SDK .net, ecco le metriche di query di esempio restituite dall'SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Procedure consigliate per ottimizzare il costo delle query 

Per l'ottimizzazione del costo delle query, prendere in considerazione le procedure consigliate seguenti:

* **Condividere il percorso per più tipi di entità**

   Provare a usare la condivisione percorso per più tipi di entità all'interno di un contenitore singolo o di un numero più piccolo di contenitori. Questo metodo offre vantaggi non solo dal punto di vista dei prezzi, ma anche per le transazioni e l'esecuzione di query. Le query sono limitate a un singolo contenitore; le transazioni atomiche su più record mediante stored procedure/trigger sono limitate a una chiave di partizione all'interno di un singolo contenitore. La condivisione percorso per entità all'interno dello stesso contenitore consente di ridurre il numero di round trip di rete per risolvere le relazioni tra i record, aumentando così le prestazioni end-to-end, abilitando transazioni atomiche su più record per un set di dati più grande e, di conseguenza, riducendo i costi. Nel caso in cui la condivisione percorso per più tipi di entità all'interno di un singolo contenitore o di un numero più piccolo di contenitori sia difficile per il proprio scenario, in genere perché si esegue la migrazione di un'applicazione esistente e non si desidera apportare modifiche al codice, è necessario considerare il provisioning della velocità effettiva a livello di database.  

* **Misurare e ottimizzare per ottenere un utilizzo minore di unità richiesta al secondo**

   La complessità di una query influisce sulla quantità di unità richiesta usate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query. 

   Il costo di una richiesta restituito nell'intestazione della richiesta indica il costo di una determinata query. Ad esempio, se una query restituisce 1000 elementi da 1 KB ciascuno, il costo dell'operazione è 1000. Entro un secondo, il server rispetterà quindi solo due richieste di questo tipo prima di limitare la velocità delle richieste successive. Per altre informazioni, vedere l'articolo [Unità richiesta](request-units.md) e il calcolatore di unità richiesta. 

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare ad altre informazioni sull'ottimizzazione dei costi in Azure Cosmos DB con gli articoli seguenti:

* Altre informazioni sul [Funzionamento dei prezzi di Azure Cosmos DB](how-pricing-works.md)
* Altre informazioni sull'[Ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni sulla [Fatturazione in Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[Ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[Ottimizzazione dei costi delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multi-area](optimize-cost-regions.md)
* Altre informazioni sulla [capacità riservata di Azure Cosmos DB](cosmos-db-reserved-capacity.md)

