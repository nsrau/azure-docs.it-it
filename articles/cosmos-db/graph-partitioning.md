---
title: Partizionamento dei dati nell'API Gremlin di Azure Cosmos DB
description: Informazioni su come usare un grafo partizionato in Azure Cosmos DB. Questo articolo descrive anche i requisiti e le procedure consigliate per un grafo partizionato.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623364"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Uso di un grafo partizionato in Azure Cosmos DB

Una delle funzionalità chiave dell'API Gremlin di Azure Cosmos DB è la possibilità di gestire grafi su vasta scala tramite la scalabilità orizzontale. I contenitori possono essere ridimensionati in modo indipendente sia a livello di archiviazione che di velocità effettiva. È possibile creare contenitori in Azure Cosmos DB che possono essere ridimensionati automaticamente per archiviare i dati di un grafo. I dati vengono bilanciati automaticamente in base alla **chiave di partizione** specificata.

**Il partizionamento è necessario** se si prevede che il contenitore memorizzi più di 20 GB o se si desidera allocare più di 10.000 unità richiesta al secondo. Gli stessi principi generali del meccanismo di partizionamento di [Azure Cosmos DB](partition-data.md) si applicano con alcune ottimizzazioni specifiche del grafico descritte di seguito.

![Partizionamento del grafico.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Meccanismo di partizionamento del grafico

Le linee guida seguenti descrivono il funzionamento della strategia di partizionamento in Azure Cosmos DB:The following guidelines describe how the partitioning strategy in Azure Cosmos DB operates:

- **Sia i vertici che gli archi vengono archiviati come documenti JSON**.

- **Per i vertici è richiesta una chiave di partizione**. Questa chiave determina in quali partizione verrà archiviato il vertice tramite un algoritmo di hash. Il nome della proprietà della chiave di partizione viene `/partitioning-key-name`definito durante la creazione di un nuovo contenitore e ha un formato: .

- **Gli archi verranno archiviati insieme al relativo vertice di origine**. In altre parole, per ogni vertice la relativa chiave di partizione definisce la posizione di archiviazione insieme ai relativi archi in uscita. Questa ottimizzazione viene eseguita per evitare query tra partizioni quando si usa la `out()` cardinalità nelle query grafo.

- **I bordi contengono riferimenti ai vertici a cui puntano a**. Tutti i bordi vengono archiviati con le chiavi di partizione e gli ID dei vertici a cui puntano. Questo calcolo `out()` rende tutte le query di direzione sempre una query partizionata con ambito e non una query tra partizioni cieche. 

- **Per le query sul grafo è necessario specificare una chiave di partizione**. Per sfruttare appieno il partizionamento orizzontale in Azure Cosmos DB, è necessario specificare la chiave di partizione quando si seleziona un singolo vertice, ogni volta possibile. Le query seguenti consentono la selezione di uno o più vertici in un grafo partizionato:

    - `/id` e `/label` non sono supportate come chiavi di partizione per un contenitore nell'API Gremlin.


    - Selezione di un vertice in base all'ID, quindi **uso del passaggio `.has()` per specificare la proprietà chiave di partizione**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Selezione di un vertice **specificando una tupla che include il valore della chiave di partizione e l'ID**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Specifica di una **matrice di tuple di valori di chiave di partizione e ID**:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Selezione di un insieme di vertici con i relativi ID e **specifica di un elenco di valori di chiave di partizione:** 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Utilizzo della **strategia di partizione** all'inizio di una query e specifica di una partizione per l'ambito del resto della query Gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Procedure consigliate quando si usa un grafo partizionato

Usare le linee guida seguenti per garantire prestazioni e scalabilità quando si usano grafi partizionati con contenitori illimitati:

- **Specificare sempre il valore della chiave di partizione quando si eseguono query su un vertice**. Il recupero di un vertice da una partizione nota è il modo più efficiente in termini di prestazioni. Tutte le operazioni di adiacenza successive verranno sempre nell'ambito di una partizione poiché I bordi contengono l'ID di riferimento e la chiave di partizione ai vertici di destinazione.

- **Usare la direzione in uscita quando si eseguono query sugli archi ogni volta che è possibile**. Come indicato in precedenza, gli archi vengono archiviati con i rispettivi vertici di origine nella direzione in uscita. Ciò significa che le probabilità di ricorrere a query tra partizioni sono ridotte al minimo quando i dati e le query sono progettati tenendo conto di questo meccanismo. Al contrario, `in()` la query sarà sempre una costosa query fan-out.

- **Scegliere una chiave di partizione che distribuisce i dati in modo uniforme tra le partizioni**. Questa decisione dipende in gran parte dal modello di dati della soluzione. Per altre informazioni sulla creazione di una chiave di partizione appropriata, vedere [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).

- **Ottimizzare le query per ottenere i dati entro i limiti di una partizione**. Una strategia di partizionamento ottimale deve essere allineata ai modelli per l'esecuzione di query. Le query che ottengono dati da una singola partizione offrono le migliori prestazioni possibili.

## <a name="next-steps"></a>Passaggi successivi

Successivamente si può procedere alla lettura degli articoli seguenti:

* Leggere le informazioni su [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).
* Leggere le informazioni sul [supporto di Gremlin nell'API Gremlin](gremlin-support.md).
* Vedere l'[introduzione all'API Gremlin](graph-introduction.md).
