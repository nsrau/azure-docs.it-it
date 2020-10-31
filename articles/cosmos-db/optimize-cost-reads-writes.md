---
title: Ottimizzazione del costo delle richieste in Azure Cosmos DB
description: Questo articolo illustra come ottimizzare i costi quando si inviano richieste in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 3f4c33a88d9a8fbf2c3d64135d93da54cf75fab3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097505"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Ottimizza il costo della richiesta in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Questo articolo descrive il modo in cui le richieste di lettura e scrittura vengono convertite in [unità richiesta](request-units.md) e come ottimizzare il costo di tali richieste. Le operazioni di lettura includono letture di punti e query. Le operazioni di scrittura includono INSERT, Replace, DELETE e Upsert di elementi.

Azure Cosmos DB offre un'ampia gamma di operazioni di database che operano sugli elementi all'interno di un contenitore. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Anziché considerare e gestire le risorse hardware, è possibile pensare a un'unità richiesta (UR) come singola misura per le risorse necessarie per eseguire diverse operazioni di database per soddisfare una richiesta.

## <a name="measuring-the-ru-charge-of-a-request"></a>Misurazione dell'addebito delle UR di una richiesta

È importante misurare l'addebito delle UR delle richieste per comprenderne il costo effettivo e valutare anche l'efficacia delle ottimizzazioni. È possibile recuperare questo costo usando il portale di Azure o controllando la risposta restituita da Azure Cosmos DB tramite uno degli SDK. Per istruzioni dettagliate su come ottenere questo risultato, vedere la pagina relativa all' [addebito per le unità richiesta in Azure Cosmos DB](find-request-unit-charge.md) .

## <a name="reading-data-point-reads-and-queries"></a>Lettura di dati: letture di punti e query

Le operazioni di lettura in Azure Cosmos DB vengono in genere ordinate dal più veloce/più efficiente al più lento o meno efficiente in termini di utilizzo delle unità richiesta, come indicato di seguito:  

* Letture di punti (ricerca chiave/valore su un ID singolo elemento e una chiave di partizione).
* Query con una clausola di filtro in una singola chiave di partizione.
* Query senza una clausola di filtro di uguaglianza o intervallo su qualsiasi proprietà.
* Query senza filtri.

### <a name="role-of-the-consistency-level"></a>Ruolo del livello di coerenza

Quando si usano i livelli di **bounded staleness** [coerenza](consistency-levels.md)forte o con decadimento **ristretto** , il costo delle unità richiesta di qualsiasi operazione di lettura (lettura a punti o query) viene raddoppiato.

### <a name="point-reads"></a>Letture di punti

L'unico fattore che influisce sull'addebito delle UR di una lettura del punto (oltre al livello di coerenza utilizzato) è la dimensione dell'elemento recuperato. La tabella seguente mostra il costo delle unità richiesta per gli elementi con dimensioni pari a 1 KB e 100 KB.

| **Dimensione elemento** | **Costo di un punto letto** |
| --- | --- |
| 1 KB | 1 UR |
| 100 kB | 10 UR |

Poiché le letture di punti (ricerca chiave/valore sull'ID elemento) sono il tipo di lettura più efficiente, è necessario assicurarsi che l'ID dell'elemento abbia un valore significativo, in modo da poter recuperare gli elementi con un punto di lettura, anziché una query, quando possibile.

### <a name="queries"></a>Query

Le unità richiesta per le query dipendono da numerosi fattori. Ad esempio, il numero di elementi di Azure Cosmos caricati o restituiti, il numero di ricerche rispetto all'indice, il tempo di compilazione della query e così via. Azure Cosmos DB assicura che la stessa query, se eseguita sugli stessi dati, consumi sempre lo stesso numero di UR, anche con esecuzioni ripetute. Il profilo di query mediante le metriche di esecuzione di query offre una buona idea di come vengano impiegate le unità richiesta.  

In alcuni casi è possibile visualizzare una sequenza di 200 e 429 risposte e unità di richiesta di variabili in un'esecuzione di paging di query, poiché le query verranno eseguite il più velocemente possibile in base alle unità richiesta disponibili. È possibile che l'esecuzione di query venga suddivisa in più pagine/round trip tra client e server. Ad esempio, 10.000 elementi possono essere restituiti come più pagine e addebitati in base al calcolo eseguito per la rispettiva pagina. Quando si esegue la somma tra queste pagine, si otterrà lo stesso numero di unità richiesta dell'intera query.

#### <a name="metrics-for-troubleshooting-queries"></a>Metriche per la risoluzione dei problemi relativi alle query

Le prestazioni e la velocità effettiva utilizzate dalle query, incluse le funzioni definite dall'utente, dipendono principalmente dal corpo della funzione. Il modo più semplice per determinare il tempo impiegato per l'esecuzione della query nella funzione definita dall'utente e il numero di ur utilizzate consiste nell'abilitare le metriche della query. Se si usa .NET SDK, di seguito sono riportate le metriche di query di esempio restituite dall'SDK:

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

#### <a name="best-practices-to-cost-optimize-queries"></a>Procedure consigliate per ottimizzare il costo delle query 

Per l'ottimizzazione del costo delle query, prendere in considerazione le procedure consigliate seguenti:

* **Condividere il percorso per più tipi di entità**

   Provare a usare la condivisione percorso per più tipi di entità all'interno di un contenitore singolo o di un numero più piccolo di contenitori. Questo metodo offre vantaggi non solo dal punto di vista dei prezzi, ma anche per le transazioni e l'esecuzione di query. Le query sono limitate a un singolo contenitore; le transazioni atomiche su più record mediante stored procedure/trigger sono limitate a una chiave di partizione all'interno di un singolo contenitore. La condivisione percorso per entità all'interno dello stesso contenitore consente di ridurre il numero di round trip di rete per risolvere le relazioni tra i record, aumentando così le prestazioni end-to-end, abilitando transazioni atomiche su più record per un set di dati più grande e, di conseguenza, riducendo i costi. Nel caso in cui la condivisione percorso per più tipi di entità all'interno di un singolo contenitore o di un numero più piccolo di contenitori sia difficile per il proprio scenario, in genere perché si esegue la migrazione di un'applicazione esistente e non si desidera apportare modifiche al codice, è necessario considerare il provisioning della velocità effettiva a livello di database.  

* **Misurare e ottimizzare per ottenere un utilizzo minore di unità richiesta al secondo**

   La complessità di una query influisce sulla quantità di unità richiesta usate per un'operazione. Il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query. 

Azure Cosmos DB offre prestazioni prevedibili in termini di velocità effettiva e latenza usando un modello di velocità effettiva con provisioning. La velocità effettiva di cui è stato effettuato il provisioning viene rappresentata in termini di [unità richieste](request-units.md) al secondo, o UR/sec. Un'unità richiesta (UR) è un'astrazione logica sulle risorse di calcolo (CPU, memoria, I/O e così via) necessarie per eseguire una richiesta. La velocità effettiva di cui è stato effettuato il provisioning (UR) viene riservata e dedicata al contenitore o al database perché questo possa garantire latenza e velocità effettiva prevedibili. La velocità effettiva di cui è stato effettuato il provisioning consente ad Azure Cosmos DB di offrire prestazioni coerenti e prevedibili, di garantire una bassa latenza e una disponibilità elevata su qualsiasi scala. Le unità richiesta rappresentano la valuta normalizzata che semplifica il calcolo della quantità di risorse necessaria per un'applicazione.

Il costo di una richiesta restituito nell'intestazione della richiesta indica il costo di una determinata query. Ad esempio, se una query restituisce 1000 elementi da 1 KB ciascuno, il costo dell'operazione è 1000. Entro un secondo, il server rispetterà quindi solo due richieste di questo tipo prima di limitare la velocità delle richieste successive. Per altre informazioni, vedere l'articolo [Unità richiesta](request-units.md) e il calcolatore di unità richiesta.

## <a name="writing-data"></a>Scrittura dei dati

Il costo ur della scrittura di un elemento dipende da:

- Dimensione dell'elemento.
- Numero di proprietà analizzate dal [criterio di indicizzazione](index-policy.md) e necessarie per l'indicizzazione.

Inserimento di un elemento da 1 KB con meno di 5 proprietà per indicizzare i costi circa 5 UR. La sostituzione di un elemento comporta il doppio dei costi necessari per inserire lo stesso elemento.

### <a name="optimizing-writes"></a>Ottimizzazione delle Scritture

Il modo migliore per ottimizzare il costo delle unità richiesta per le operazioni di scrittura consiste nel dimensionare correttamente gli elementi e il numero di proprietà che vengono indicizzate.

- L'archiviazione di elementi di grandi dimensioni in Azure Cosmos DB comporta addebiti per le UR elevate e può essere considerata come un anti-modello. In particolare, non archiviare contenuto binario o blocchi di testo di grandi dimensioni su cui non è necessario eseguire una query. Una procedura consigliata consiste nell'inserire questo tipo di dati nell' [Archivio BLOB di Azure](../storage/blobs/storage-blobs-introduction.md) e archiviare un riferimento (o collegamento) al BLOB nell'elemento da scrivere Azure Cosmos DB.
- L'ottimizzazione dei criteri di indicizzazione per indicizzare solo le proprietà in base alle quali filtrare le query può comportare una notevole differenza nelle ur utilizzate dalle operazioni di scrittura. Quando si crea un nuovo contenitore, i criteri di indicizzazione predefiniti indicizzano ogni proprietà presente negli elementi. Sebbene si tratta di un valore predefinito valido per le attività di sviluppo, è consigliabile rivalutare e [personalizzare i criteri di indicizzazione](how-to-manage-indexing-policy.md) quando si passa alla produzione o quando il carico di lavoro inizia a ricevere traffico significativo.

Quando si esegue l'inserimento bulk dei dati, è consigliabile usare anche la [libreria Azure Cosmos DB Executor bulk](bulk-executor-overview.md) , perché è progettata per ottimizzare il consumo di tali operazioni da parte di ur. Facoltativamente, è anche possibile usare [Azure Data Factory](../data-factory/introduction.md) che si basa sulla stessa libreria.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare ad altre informazioni sull'ottimizzazione dei costi in Azure Cosmos DB con gli articoli seguenti:

* Altre informazioni sull'[Ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[Ottimizzazione dei costi della velocità effettiva](optimize-cost-throughput.md)
* Altre informazioni sull'[ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multi-area](optimize-cost-regions.md)
* Ulteriori informazioni su [Azure Cosmos DB capacità riservata](cosmos-db-reserved-capacity.md)
