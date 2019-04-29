---
title: Partizionamento dei dati nell'API Gremlin di Azure Cosmos DB
description: Informazioni su come usare un grafo partizionato in Azure Cosmos DB. Questo articolo descrive anche i requisiti e le procedure consigliate per un grafo partizionato.
author: rockboyfor
ms.author: v-yeche
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 03/18/2019
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888412"
---
<!--Verify sucessfully-->
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Uso di un grafo partizionato in Azure Cosmos DB

Una delle funzionalità chiave dell'API Gremlin di Azure Cosmos DB è la possibilità di gestire grafi su vasta scala tramite la scalabilità orizzontale. La scalabilità orizzontale viene implementata tramite le [funzionalità di partizionamento in Azure Cosmos DB](partition-data.md). I contenitori possono essere ridimensionati in modo indipendente sia a livello di archiviazione che di velocità effettiva. È possibile creare contenitori in Azure Cosmos DB che possono essere ridimensionati automaticamente per archiviare i dati di un grafo. I dati vengono bilanciati automaticamente in base alla **chiave di partizione** specificata.

In questo documento verranno descritti i dettagli del partizionamento dei database a grafo insieme alle implicazioni sia per i vertici (o nodi) che per gli archi.

## <a name="requirements-for-partitioned-graph"></a>Requisiti per i grafi partizionati

Le informazioni seguenti includono i dettagli che occorre conoscere per la creazione di un contenitore grafo partizionato:

- Il **partizionamento è obbligatorio** se si prevede che il contenitore archivi più di 10 GB o se si vogliono allocare più di 10.000 unità richiesta al secondo (UR).

- **Sia i vertici che gli archi vengono archiviati come documenti JSON**.

- **Per i vertici è richiesta una chiave di partizione**. Questa chiave determina in quali partizione verrà archiviato il vertice tramite un algoritmo di hash. Il nome di questa chiave di partizione è una stringa composta da una singola parola senza spazi o caratteri speciali. La chiave di partizione viene definita quando si crea un nuovo contenitore con il formato: `/partitioning-key-name`.

- **Gli archi verranno archiviati insieme al relativo vertice di origine**. In altre parole, per ogni vertice la relativa chiave di partizione definisce la posizione di archiviazione insieme ai relativi archi in uscita. Ciò è necessario per evitare query tra partizioni quando si usa la cardinalità `out()` nelle query sul grafo.

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

Usare le linee guida seguenti per garantire prestazioni e scalabilità quando si usano grafi partizionati con contenitori illimitati:

- **Specificare sempre il valore della chiave di partizione quando si eseguono query su un vertice**. Il recupero di un vertice da una partizione nota è il modo più efficiente in termini di prestazioni.

- **Usare la direzione in uscita quando si eseguono query sugli archi ogni volta che è possibile**. Come indicato in precedenza, gli archi vengono archiviati con i rispettivi vertici di origine nella direzione in uscita. Ciò significa che le probabilità di ricorrere a query tra partizioni sono ridotte al minimo quando i dati e le query sono progettati tenendo conto di questo meccanismo.

- **Scegliere una chiave di partizione che distribuisce i dati in modo uniforme tra le partizioni**. Questa decisione dipende in gran parte dal modello di dati della soluzione. Per altre informazioni sulla creazione di una chiave di partizione appropriata, vedere [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).

- **Ottimizzare le query per ottenere i dati entro i limiti di una partizione**. Una strategia di partizionamento ottimale deve essere allineata ai modelli per l'esecuzione di query. Le query che ottengono dati da una singola partizione offrono le migliori prestazioni possibili.

## <a name="next-steps"></a>Passaggi successivi

Successivamente si può procedere alla lettura degli articoli seguenti:

* Leggere le informazioni su [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).
* Leggere le informazioni sul [supporto di Gremlin nell'API Gremlin](gremlin-support.md).
* Vedere l'[introduzione all'API Gremlin](graph-introduction.md).

<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->