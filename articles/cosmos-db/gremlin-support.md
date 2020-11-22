---
title: Supporto di Gremlin in Azure Cosmos DB e compatibilità con le funzionalità di TinkerPop
description: Informazioni sul linguaggio Gremlin di Apache TinkerPop. Informazioni sulle funzionalità e sulle procedure disponibili in Azure Cosmos DB e sulle differenze di compatibilità del motore TinkerPop Graph.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/11/2020
ms.author: sngun
ms.openlocfilehash: a149f0b331a77462aa53b948fedf25dd1331969e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683625"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Supporto dei grafi Gremlin in Azure Cosmos DB e compatibilità con le funzionalità di TinkerPop
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB supporta il linguaggio di attraversamento di grafi di [Apache Tinkerpop](https://tinkerpop.apache.org), noto come [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). È possibile usare il linguaggio Gremlin per creare le entità dei grafi (vertici e archi), modificare proprietà all'interno di tali entità, eseguire query e attraversamenti ed eliminare entità.

Il motore Azure Cosmos DB Graph segue rigorosamente la specifica dei passaggi trasversali di [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), ma esistono differenze nell'implementazione specifiche per Azure Cosmos DB. Questo articolo illustra una procedura dettagliata di Gremlin ed enumera le funzionalità di Gremlin supportate dall'API Gremlin.

## <a name="compatible-client-libraries"></a>Librerie client compatibili

La tabella seguente illustra i driver Gremlin noti che è possibile usare in Azure Cosmos DB:

| Download | Source (Sorgente) | Introduzione | Versione connettore supportata |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Gremlin.NET su GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Creare un'app Graph con .NET](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [JavaDoc Gremlin](https://tinkerpop.apache.org/javadocs/current/full/) | [Creare un'app Graph con Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript in GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Creare un'app Graph con Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python in GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Creare un'app Graph con Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP su GitHub](https://github.com/PommeVerte/gremlin-php) | [Creare un'app Graph con PHP](create-graph-php.md) | 3.1.0 |
| [Go Lang](https://github.com/supplyon/gremcos/) | [Go Lang](https://github.com/supplyon/gremcos/) | | Questa libreria è creata da collaboratori esterni. Il team di Azure Cosmos DB non offre alcun supporto né gestisce la libreria. |
| [Console Gremlin](https://tinkerpop.apache.org/downloads.html) | [Documenti TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Creare un'app Graph con la console Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Graph oggetti supportato

TinkerPop è uno standard che copre un'ampia gamma di tecnologie a grafi. Pertanto, ha una terminologia standard usata per descrivere le funzionalità offerte da un provider di grafi. Azure Cosmos DB offre un database a grafi scrivibile, ad alta concorrenza, persistente, che può essere partizionato tra più server o cluster. 

La tabella seguente elenca le funzionalità di TinkerPop implementate da Azure Cosmos DB: 

| Category | Implementazione di Azure Cosmos DB |  Note | 
| --- | --- | --- |
| Funzionalità del grafo | Offre persistenza e accesso simultaneo. Progettata per supportare le transazioni | Metodi di calcolo possono essere implementati tramite il connettore Spark. |
| Funzionalità della variabile | Supporta Boolean, Integer, Byte, Double, Float, Integer, Long, String | Supporta i tipi primitivi, è compatibile con i tipi complessi tramite modello di dati |
| Funzionalità del vertice | Supporta RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Supporta la creazione, la modifica e l'eliminazione di vertici |
| Funzionalità della proprietà del vertice | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Supporta la creazione, la modifica e l'eliminazione di proprietà di vertici |
| Funzionalità dell'arco | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Supporta la creazione, la modifica e l'eliminazione di archi |
| Funzionalità della proprietà dell'arco | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Supporta la creazione, la modifica e l'eliminazione di proprietà dell'arco |

## <a name="gremlin-wire-format"></a>Formato wire Gremlin

Azure Cosmos DB usa il formato JSON per la restituzione di risultati dalle operazioni Gremlin. Azure Cosmos DB attualmente supporta il formato JSON. Ad esempio, il frammento di codice seguente mostra una rappresentazione JSON di un vertice *restituito al client* da Azure Cosmos DB:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Le proprietà usate dal formato JSON per i vertici sono descritte di seguito:

| Proprietà | Descrizione | 
| --- | --- | --- |
| `id` | ID del vertice. Deve essere univoco (in combinazione con il valore di `_partition` se applicabile). Se non viene specificato alcun valore, viene automaticamente assegnato un GUID | 
| `label` | Etichetta del vertice. Questa proprietà viene usata per descrivere il tipo di entità. |
| `type` | Usato per distinguere i vertici da documenti non a grafo |
| `properties` | Contenitore delle proprietà definite dall'utente associate al vertice. Ogni proprietà può avere più valori. |
| `_partition` | La chiave di partizione del vertice. Usata per il [partizionamento Graph](graph-partitioning.md). |
| `outE` | Questa proprietà contiene un elenco degli archi uscenti da un vertice. L'archiviazione delle informazioni di adiacenza con il vertice consente l'esecuzione rapida degli attraversamenti. Gli archi vengono raggruppati in base alle relative etichette. |

E l'arco contiene le informazioni seguenti per spostarsi in altre parti del grafo.

| Proprietà | Descrizione |
| --- | --- |
| `id` | ID dell'arco. Deve essere univoco (in combinazione con il valore di `_partition` se applicabile). |
| `label` | Etichetta dell'arco. Questa proprietà è facoltativa e viene usata per descrivere il tipo di relazione. |
| `inV` | Questa proprietà contiene un elenco di vertici per un bordo. L'archiviazione delle informazioni di adiacenza con il bordo consente l'esecuzione rapida degli attraversamenti. I vertici vengono raggruppati in base alle relative etichette. |
| `properties` | Contenitore delle proprietà definite dall'utente associate all'arco. Ogni proprietà può avere più valori. |

Ogni proprietà può archiviare più valori all'interno di una matrice. 

| Proprietà | Descrizione |
| --- | --- |
| `value` | Valore della proprietà

## <a name="gremlin-steps"></a>Step di Gremlin

Verranno ora esaminati gli step di Gremlin supportati da Azure Cosmos DB. Per informazioni complete su Gremlin, vedere [TinkerPop reference](https://tinkerpop.apache.org/docs/3.3.2/reference) (Riferimento a TinkerPop).

| step | Descrizione | Documentazione TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Aggiunge un arco tra due vertici | [addE step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Aggiunge un vertice al grafo | [addV step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Assicura che tutti gli attraversamenti restituiscono un valore | [and step](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Modulatore di step per assegnare una variabile all'output di uno step | [as step](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Modulatore di step usato con `group` e `order` | [by step](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Restituisce il primo attraversamento che restituisce un risultato | [coalesce step](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Restituisce un valore costante. Usato con `coalesce`| [constant step](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Restituisce il conteggio risultante dall'attraversamento | [count step](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Restituisce i valori con i duplicati rimossi | [dedup step](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Elimina i valori (vertice/arco) | [drop step](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Crea una descrizione di tutte le operazioni generate dallo step di Gremlin eseguito | [executionProfile step](graph-execution-profile.md) |
| `fold` | Si comporta come una barriera che calcola l'aggregazione di risultati| [fold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Raggruppa i valori in base alle etichette specificate| [group step](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Usato per filtrare proprietà, vertici e archi. Supporta `hasLabel`, `hasId`, `hasNot` e le varianti `has`. | [has step](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Inserisce i valori in un flusso| [inject step](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Usato per eseguire un filtro con un'espressione booleana | [is step](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Usato per limitare il numero di elementi nell'attraversamento| [limit step](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Esegue il wrapping di una sezione di attraversamento, simile a una sottoquery | [local step](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Usato per produrre la negazione di un filtro | [not step](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Restituisce il risultato dell'attraversamento specificato se fornisce un risultato, in caso contrario restituisce l'elemento chiamante | [optional step](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Garantisce che almeno uno degli attraversamenti restituisca un valore | [or step](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Restituisce i risultati nell'ordinamento specificato | [order step](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Restituisce il percorso completo dell'attraversamento | [path step](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Proietta le proprietà come Mappa | [project step](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Restituisce le proprietà per le etichette specificate | [properties step](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtra per l'intervallo di valori specificato| [range step](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Ripete lo step per il numero di volte specificato. Usato per eseguire i cicli | [repeat step](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Usato per campionare i risultati dell'attraversamento | [sample step](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Usato per proiettare i risultati dell'attraversamento |  [select step](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Usato per le aggregazioni non bloccanti risultanti dall'attraversamento | [store step](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Funzione di filtraggio di stringhe. Viene usata come predicato relativo allo step `has()` per consentire di trovare una corrispondenza con una proprietà che inizia con una determinata stringa | [Predicati TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Funzione di filtraggio di stringhe. Viene usata come predicato relativo allo step `has()` per consentire di trovare una corrispondenza con una proprietà che termina con una determinata stringa | [Predicati TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Funzione di filtraggio di stringhe. Viene usata come predicato relativo allo step `has()` per consentire di trovare una corrispondenza con una proprietà che contiene una determinata stringa | [Predicati TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Funzione di filtraggio di stringhe. Viene usata come predicato relativo allo step `has()` per consentire di trovare una corrispondenza con una proprietà che non inizia con una determinata stringa | [Predicati TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Funzione di filtraggio di stringhe. Viene usata come predicato relativo allo step `has()` per consentire di trovare una corrispondenza con una proprietà che non termina con una determinata stringa | [Predicati TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Funzione di filtraggio di stringhe. Viene usata come predicato relativo allo step `has()` per consentire di trovare una corrispondenza con una proprietà che non contiene una determinata stringa | [Predicati TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Aggrega i percorsi da un vertice a una struttura ad albero | [tree step](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Srotola un iteratore come step| [unfold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Unisce i risultati di più attraversamenti| [union step](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Include gli step necessari per gli attraversamenti tra vertici e archi `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`, e `otherV` per | [vertex steps](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Usato per filtrare i risultati dell'attraversamento. Supporta `eq`, `neq`, `lt`, `lte`, `gt`, `gte` e gli operatori `between`  | [where step](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Il motore ottimizzato per la scrittura fornito da Azure Cosmos DB supporta l'indicizzazione automatica di tutte le proprietà comprese all'interno di vertici e archi per impostazione predefinita. Pertanto, query con filtri, query di intervallo, ordinamento o aggregazioni in qualsiasi proprietà vengono elaborati dall'indice e serviti in modo efficiente. Per altre informazioni sul funzionamento dell'indicizzazione in Azure Cosmos DB, vedere il documento sull'[indicizzazione senza schema](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="behavior-differences"></a>Differenze di comportamento

* Il motore Azure Cosmos DB Graph esegue l'attraversamento ***in ampiezza**, mentre quello di TinkerPop Gremlin è in profondità. Questo comportamento consente di ottenere prestazioni migliori in un sistema scalabile orizzontalmente, come Cosmos DB.

## <a name="unsupported-features"></a>Funzionalità non supportate

_ * **[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)** _ è una specifica non dipendente da un linguaggio di programmazione per gli attraversamenti dei grafi. Cosmos DB Graph non la supporta ancora. Usare `GremlinClient.SubmitAsync()` e passare l'attraversamento come stringa di testo.

_ La cardinalità set * **`property(set, 'xyz', 1)`** _ non è attualmente supportata. In alternativa, utilizzare `property(list, 'xyz', 1)`. Per altre informazioni, vedere le [proprietà dei vertici con TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

_ Il passaggio * **`match()`** _ non è attualmente disponibile. Questo passaggio fornisce funzionalità di query dichiarative.

_ Gli ***oggetti come proprietà** _ su vertici o archi non sono supportati. Le proprietà possono essere solo tipi primitivi o matrici.

_ L'***ordinamento per proprietà di matrici** _ `order().by(<array property>)` non è supportato. L'ordinamento è supportato solo per tipi primitivi.

_ I ***tipi JSON non primitivi** _ non sono supportati. Usare i tipi `string`, `number` o `true`/`false`. I valori `null` non sono supportati. 

_ Il serializzatore ***GraphSONv3** _ non è attualmente supportato. Usare le classi Serializer, Reader e Writer di `GraphSONv2` nella configurazione della connessione. I risultati restituiti dall'API Gremlin di Azure Cosmos DB non hanno lo stesso formato di GraphSON. 

_ Le ***espressioni e le funzioni lambda** non sono attualmente supportate. Sono incluse le funzioni `.map{<expression>}`, `.by{<expression>}` e `.filter{<expression>}`. Per altre informazioni, anche su come riscriverle usando i passaggi di Gremlin, vedere le [note sulle espressioni lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* Le ***transazioni** _ non sono supportate a causa della natura distribuita del sistema.  Configurare il modello di coerenza appropriato nell'account Gremlin per ottenere garanzie "read your own writes" e usare la concorrenza ottimistica per risolvere le scritture in conflitto.

## <a name="known-limitations"></a>Limitazioni note

_ **Utilizzo degli indici per le query Gremlin con passaggi di attraversamento intermedio `.V()`** : Attualmente, solo la prima chiamata `.V()` di un attraversamento userà l'indice per risolvere eventuali filtri o predicati collegati. Le chiamate successive non consulteranno l'indice, il che potrebbe aumentare la latenza e il costo della query.
    
    Assuming default indexing, a typical read Gremlin query that starts with the `.V()` step would use parameters in its attached filtering steps, such as `.has()` or `.where()` to optimize the cost and performance of the query. For example:

    ```java
    g.V().has('category', 'A')
    ```

    However, when more than one `.V()` step is included in the Gremlin query, the resolution of the data for the query might not be optimal. Take the following query as an example:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    This query will return two groups of vertices based on their property called `category`. In this case, only the first call, `g.V().has('category', 'A')` will make use of the index to resolve the vertices based on the values of their properties.

    A workaround for this query is to use subtraversal steps such as `.map()` and `union()`. This is exemplified below:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    You can review the performance of the queries by using the [Gremlin `executionProfile()` step](graph-execution-profile.md).

## <a name="next-steps"></a>Passaggi successivi

* Iniziare a creare un'applicazione Graph [tramite SDK](create-graph-dotnet.md) 
* Altre informazioni sul [supporto dei grafi](graph-introduction.md) in Azure Cosmos DB
