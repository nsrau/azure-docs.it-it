---
title: Partizionamento nell'API Gremlin di Azure Cosmos DB | Microsoft Docs
description: Informazioni su come usare un grafo partizionato in Azure Cosmos DB.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: conceptual
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: bf91ec77f8d7248c6fa2047636f295c187f626c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078223"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Uso di un grafo partizionato in Azure Cosmos DB

Una delle funzionalità chiave dell'API Gremlin di Azure Cosmos DB è la possibilità di gestire grafi su vasta scala tramite la scalabilità orizzontale. Questo processo viene implementato tramite le [funzionalità di partizionamento in Azure Cosmos DB](partition-data.md#how-does-partitioning-work), che usano contenitori, che supportano il ridimensionamento in modo indipendente, sia a livello di archiviazione che di velocità effettiva. Azure Cosmos DB supporta i tipi di contenitori seguenti per tutte le API:

- **Contenitore fisso**: questi contenitori possono archiviare un database a grafo con dimensioni massime di 10 GB a cui possono essere allocate un massimo di 10.000 unità richiesta al secondo. Per creare un contenitore fisso non è necessario specificare una proprietà chiave di partizione nei dati.

- **Contenitore illimitato**: questi contenitori possono essere ridimensionati automaticamente per archiviare un grafo oltre il limite di 10 GB tramite il partizionamento orizzontale. In ogni partizione verranno archiviati 10 GB e i dati verranno automaticamente bilanciati in base alla **chiave di partizione specificata**, che sarà un parametro obbligatorio per l'uso di un contenitore illimitato. Questo tipo di contenitore può archiviare dati di dimensioni virtualmente illimitate e supportare fino a 100.000 unità richiesta al secondo o più [contattando il supporto](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

In questo documento verranno descritti i dettagli del partizionamento dei database a grafo insieme alle implicazioni sia per i vertici (o nodi) che per gli archi.

## <a name="requirements-for-partitioned-graph"></a>Requisiti per i grafi partizionati

Le informazioni seguenti includono i dettagli che occorre conoscere per la creazione di un contenitore grafo partizionato:

- La **configurazione del partizionamento sarà necessaria** se si prevede che le dimensioni del contenitore saranno maggiori di 10 GB e/o se sarà richiesta l'allocazione di più di 10.000 unità richiesta al secondo (UR/sec).

- **Sia i vertici che gli archi vengono archiviati come documenti JSON** nel back-end di un contenitore dell'API Gremlin di Azure Cosmos DB.

- **Per i vertici è richiesta una chiave di partizione**. Questa chiave determina in quali partizione verrà archiviato il vertice tramite un algoritmo di hash. Il nome di questa chiave di partizione è una stringa composta da una singola parola senza spazi o caratteri speciali e viene definita al momento della creazione di un nuovo contenitore usando il formato `/partitioning-key-name` nel portale.

- **Gli archi verranno archiviati insieme al relativo vertice di origine**. In altre parole, per ogni vertice la relativa chiave di partizione definirà la posizione di archiviazione insieme ai relativi archi in uscita. Ciò è necessario per evitare query tra partizioni quando si usa la cardinalità `out()` nelle query sul grafo.

- **Per le query sul grafo è necessario specificare una chiave di partizione**. Per sfruttare appieno il partizionamento orizzontale in Azure Cosmos DB, è necessario specificare la chiave di partizione quando si seleziona un singolo vertice, ogni volta possibile. Le query seguenti consentono la selezione di uno o più vertici in un grafo partizionato:

    - `/id` e `/label` non sono supportate come chiavi di partizione per un contenitore nell'API Gremlin.


    - Selezione di un vertice in base all'ID, quindi **uso del passaggio `.has()` per specificare la proprietà chiave di partizione**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Selezione di un vertice **specificando una tupla che include il valore della chiave di partizione e l'ID**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Specifica di una **matrice di tuple di valori di chiave di partizione e ID**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Selezione di un set di vertici e **specifica di un elenco di valori di chiave di partizione**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Procedure consigliate quando si usa un grafo partizionato

Le linee guida seguenti rappresentano le procedure consigliate per assicurarsi di ottenere la massima efficienza a livello di prestazioni e scalabilità quando si usano grafi partizionati in contenitori illimitati:

- **Specificare sempre il valore della chiave di partizione quando si eseguono query su un vertice**. Il recupero di un vertice da una partizione nota è il modo più efficiente in termini di prestazioni.

- **Usare la direzione in uscita quando si eseguono query sugli archi ogni volta che è possibile**. Come indicato in precedenza, gli archi vengono archiviati con i rispettivi vertici di origine nella direzione in uscita. Ciò significa che le probabilità di ricorrere a query tra partizioni sono ridotte al minimo quando i dati e le query sono progettati tenendo conto di questo meccanismo.

- **Scegliere una chiave di partizione che distribuisce i dati in modo uniforme tra le partizioni**. Questa decisione dipende in gran parte dal modello di dati della soluzione. Per altre informazioni sulla creazione di una chiave di partizione appropriata, vedere [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).

- **Ottimizzare le query per ottenere i dati entro i limiti di una partizione quando possibile**. Una strategia di partizionamento ottimale deve essere allineata ai modelli per l'esecuzione di query. Le query che ottengono dati da una singola partizione offrono le migliori prestazioni possibili.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata illustrata una panoramica dei concetti e delle procedure consigliate per il partizionamento con un'API Gremlin di Azure Cosmos DB. 

* Leggere le informazioni su [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).
* Leggere le informazioni sul [supporto di Gremlin nell'API Gremlin](gremlin-support.md).
* Vedere l'[introduzione all'API Gremlin](graph-introduction.md).