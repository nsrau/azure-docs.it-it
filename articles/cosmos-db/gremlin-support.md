---
title: Supporto per Gremlin di Azure Cosmos DB
description: Informazioni sul linguaggio Gremlin di Apache TinkerPop. Informazioni sulle funzionalità e sulle procedure disponibili in Azure Cosmos DB
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: 0c1ca054f9d28bb81c6f8acf6c0f43b134a596ed
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293780"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Supporto Gremlin Graph di Azure Cosmos DB
Azure Cosmos DB supporta il linguaggio di attraversamento di grafi di [Apache Tinkerpop](https://tinkerpop.apache.org), noto come [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). È possibile usare il linguaggio Gremlin per creare le entità dei grafi (vertici e archi), modificare proprietà all'interno di tali entità, eseguire query e attraversamenti ed eliminare entità. 

Questo articolo illustra una procedura dettagliata di Gremlin ed enumera le funzionalità di Gremlin supportate dall'API Gremlin.

## <a name="compatible-client-libraries"></a>Librerie client compatibili

La tabella seguente illustra i driver Gremlin noti che è possibile usare in Azure Cosmos DB:

| Download | Source (Sorgente) | Introduzione | Versione connettore supportata |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET su GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Creare un'app Graph con .NET](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [JavaDoc Gremlin](https://tinkerpop.apache.org/javadocs/current/full/) | [Creare un'app Graph con Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript in GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Creare un'app Graph con Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python in GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Creare un'app Graph con Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP su GitHub](https://github.com/PommeVerte/gremlin-php) | [Creare un'app Graph con PHP](create-graph-php.md) | 3.1.0 |
| [Console Gremlin](https://tinkerpop.apache.org/downloads.html) | [Documenti TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Creare un'app Graph con la console Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Graph oggetti supportato
TinkerPop è uno standard che copre un'ampia gamma di tecnologie a grafi. Pertanto, ha una terminologia standard usata per descrivere le funzionalità offerte da un provider di grafi. Azure Cosmos DB offre un database a grafi scrivibile, ad alta concorrenza, persistente, che può essere partizionato tra più server o cluster. 

La tabella seguente elenca le funzionalità di TinkerPop implementate da Azure Cosmos DB: 

| Categoria | Implementazione di Azure Cosmos DB |  Note | 
| --- | --- | --- |
| Funzionalità del grafo | Offre persistenza e accesso simultaneo. Progettata per supportare le transazioni | Metodi di calcolo possono essere implementati tramite il connettore Spark. |
| Funzionalità della variabile | Supporta Boolean, Integer, Byte, Double, Float, Integer, Long, String | Supporta i tipi primitivi, è compatibile con i tipi complessi tramite modello di dati |
| Funzionalità del vertice | Supporta RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Supporta la creazione, la modifica e l'eliminazione di vertici |
| Funzionalità della proprietà del vertice | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Supporta la creazione, la modifica e l'eliminazione di proprietà di vertici |
| Funzionalità dell'arco | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Supporta la creazione, la modifica e l'eliminazione di archi |
| Funzionalità della proprietà dell'arco | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Supporta la creazione, la modifica e l'eliminazione di proprietà dell'arco |

## <a name="gremlin-wire-format-graphson"></a>Formato wire Gremlin: GraphSON

Azure Cosmos DB usa il [ formato GraphSON](http://tinkerpop.apache.org/docs/current/reference/#graphson) per la restituzione di risultati dalle operazioni Gremlin. Azure Cosmos DB attualmente supporta la versione "GraphSONv2". GraphSON è il formato standard Gremlin per la rappresentazione di vertici, archi e proprietà (singolo e multi-valore) tramite JSON.

Ad esempio, il frammento di codice seguente mostra una rappresentazione GraphSON di un vertice *restituito al client* da Azure Cosmos DB. 

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

Le proprietà usate da GraphSON per i vertici sono descritte di seguito:

| Proprietà | DESCRIZIONE | 
| --- | --- | --- |
| `id` | ID del vertice. Deve essere univoco (in combinazione con il valore di `_partition` se applicabile). Se non viene specificato alcun valore, viene automaticamente assegnato un GUID | 
| `label` | Etichetta del vertice. Questa proprietà viene usata per descrivere il tipo di entità. |
| `type` | Usato per distinguere i vertici da documenti non a grafo |
| `properties` | Contenitore delle proprietà definite dall'utente associate al vertice. Ogni proprietà può avere più valori. |
| `_partition` | La chiave di partizione del vertice. Usata per il [partizionamento Graph](graph-partitioning.md). |
| `outE` | Questa proprietà contiene un elenco degli archi uscenti da un vertice. L'archiviazione delle informazioni di adiacenza con il vertice consente l'esecuzione rapida degli attraversamenti. Gli archi vengono raggruppati in base alle relative etichette. |

E l'arco contiene le informazioni seguenti per spostarsi in altre parti del grafo.

| Proprietà | DESCRIZIONE |
| --- | --- |
| `id` | ID dell'arco. Deve essere univoco (in combinazione con il valore di `_partition` se applicabile). |
| `label` | Etichetta dell'arco. Questa proprietà è facoltativa e viene usata per descrivere il tipo di relazione. |
| `inV` | Questa proprietà contiene un elenco di vertici per un bordo. L'archiviazione delle informazioni di adiacenza con il bordo consente l'esecuzione rapida degli attraversamenti. I vertici vengono raggruppati in base alle relative etichette. |
| `properties` | Contenitore delle proprietà definite dall'utente associate all'arco. Ogni proprietà può avere più valori. |

Ogni proprietà può archiviare più valori all'interno di una matrice. 

| Proprietà | DESCRIZIONE |
| --- | --- |
| `value` | Valore della proprietà

## <a name="gremlin-steps"></a>Step di Gremlin
Verranno ora esaminati gli step di Gremlin supportati da Azure Cosmos DB. Per informazioni complete su Gremlin, vedere [TinkerPop reference](https://tinkerpop.apache.org/docs/3.3.2/reference) (Riferimento a TinkerPop).

| step | DESCRIZIONE | Documentazione TinkerPop 3.2 |
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

## <a name="next-steps"></a>Passaggi successivi
* Iniziare a creare un'applicazione Graph [tramite SDK](create-graph-dotnet.md) 
* Altre informazioni sul [supporto dei grafi](graph-introduction.md) in Azure Cosmos DB
