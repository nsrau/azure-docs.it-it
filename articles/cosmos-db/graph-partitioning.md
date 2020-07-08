---
title: Partizionamento dei dati nell'API Gremlin di Azure Cosmos DB
description: Informazioni su come usare un grafo partizionato in Azure Cosmos DB. Questo articolo descrive anche i requisiti e le procedure consigliate per un grafo partizionato.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 78c15da1ea9fe5f6307ce388e4d64d372e9eb8c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261767"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Uso di un grafo partizionato in Azure Cosmos DB

Una delle funzionalità chiave dell'API Gremlin di Azure Cosmos DB è la possibilità di gestire grafi su vasta scala tramite la scalabilità orizzontale. I contenitori possono essere ridimensionati in modo indipendente sia a livello di archiviazione che di velocità effettiva. È possibile creare contenitori in Azure Cosmos DB che possono essere ridimensionati automaticamente per archiviare i dati di un grafo. I dati vengono bilanciati automaticamente in base alla **chiave di partizione** specificata.

Il **partizionamento è obbligatorio** se si prevede che il contenitore memorizzi più di 20 GB di dimensioni o se si desidera allocare più di 10.000 unità richiesta al secondo (UR). Gli stessi principi generali del [meccanismo di partizionamento Azure Cosmos DB](partition-data.md) si applicano con alcune ottimizzazioni specifiche del grafo descritte di seguito.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Partizionamento del grafo." border="false":::

## <a name="graph-partitioning-mechanism"></a>Meccanismo di partizionamento del grafo

Le linee guida seguenti descrivono il funzionamento della strategia di partizionamento in Azure Cosmos DB:

- **Sia i vertici che gli archi vengono archiviati come documenti JSON**.

- **Per i vertici è richiesta una chiave di partizione**. Questa chiave determina in quali partizione verrà archiviato il vertice tramite un algoritmo di hash. Il nome della proprietà della chiave di partizione viene definito durante la creazione di un nuovo contenitore e presenta un formato: `/partitioning-key-name` .

- **Gli archi verranno archiviati insieme al relativo vertice di origine**. In altre parole, per ogni vertice la relativa chiave di partizione definisce la posizione di archiviazione insieme ai relativi archi in uscita. Questa ottimizzazione viene eseguita per evitare query tra partizioni quando si usa la `out()` cardinalità nelle query Graph.

- **I bordi contengono riferimenti ai vertici a cui puntano**. Tutti i bordi vengono archiviati con le chiavi di partizione e gli ID dei vertici a cui puntano. Questo calcolo rende sempre tutte le query `out()` di direzione una query partizionata con ambito e non una query nascosta tra partizioni. 

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
        
    - Selezione di un set di vertici con i relativi ID e **specifica di un elenco di valori di chiave di partizione**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Usando la **strategia di partizione** all'inizio di una query e specificando una partizione per l'ambito del resto della query Gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Procedure consigliate quando si usa un grafo partizionato

Usare le linee guida seguenti per garantire prestazioni e scalabilità quando si usano grafi partizionati con contenitori illimitati:

- **Specificare sempre il valore della chiave di partizione quando si eseguono query su un vertice**. Il recupero di un vertice da una partizione nota è il modo più efficiente in termini di prestazioni. Tutte le operazioni adiacenza successive verranno sempre limitate a una partizione, perché i bordi contengono l'ID di riferimento e la chiave di partizione nei vertici di destinazione.

- **Usare la direzione in uscita quando si eseguono query sugli archi ogni volta che è possibile**. Come indicato in precedenza, gli archi vengono archiviati con i rispettivi vertici di origine nella direzione in uscita. Ciò significa che le probabilità di ricorrere a query tra partizioni sono ridotte al minimo quando i dati e le query sono progettati tenendo conto di questo meccanismo. Al contrario, la `in()` query sarà sempre una query di fan-out costosa.

- **Scegliere una chiave di partizione che distribuisce i dati in modo uniforme tra le partizioni**. Questa decisione dipende in gran parte dal modello di dati della soluzione. Per altre informazioni sulla creazione di una chiave di partizione appropriata, vedere [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).

- **Ottimizzare le query per ottenere i dati entro i limiti di una partizione**. Una strategia di partizionamento ottimale deve essere allineata ai modelli per l'esecuzione di query. Le query che ottengono dati da una singola partizione offrono le migliori prestazioni possibili.

## <a name="next-steps"></a>Passaggi successivi

Successivamente si può procedere alla lettura degli articoli seguenti:

* Leggere le informazioni su [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).
* Leggere le informazioni sul [supporto di Gremlin nell'API Gremlin](gremlin-support.md).
* Vedere l'[introduzione all'API Gremlin](graph-introduction.md).
