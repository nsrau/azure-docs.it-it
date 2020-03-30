---
title: Criteri di indicizzazione di Azure Cosmos DB
description: Informazioni su come configurare e modificare i criteri di indicizzazione predefiniti per l'indicizzazione automatica e prestazioni più elevate in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292077"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indexing policies in Azure Cosmos DB (Criteri di indicizzazione in Azure Cosmos DB)

In Azure Cosmos DB, ogni contenitore ha un criterio di indicizzazione che determina il modo in cui gli elementi del contenitore devono essere indicizzati. I criteri di indicizzazione predefiniti per i contenitori appena creati indicizzano ogni proprietà di ogni elemento, applicando indici di intervallo per qualsiasi stringa o numero e indici spaziali per qualsiasi oggetto GeoJSON di tipo Point. In questo modo è possibile ottenere prestazioni di query elevate senza dover pensare all'indicizzazione e alla gestione degli indici in anticipo.

In alcune situazioni potrebbe essere necessario eseguire l'override di questo comportamento automatico per soddisfare al meglio le proprie esigenze. È possibile personalizzare i criteri di indicizzazione di un contenitore impostandone la *modalità di indicizzazione*e includendo o escludendo *i percorsi delle proprietà.*

> [!NOTE]
> Il metodo di aggiornamento dei criteri di indicizzazione descritto in questo articolo si applica solo all'API SQL (Core) di Azure Cosmos DB.

## <a name="indexing-mode"></a>Modalità di indicizzazione

Azure Cosmos DB supporta due modalità di indicizzazione:Azure Cosmos DB supports two indexing modes:

- **Coerente**: l'indice viene aggiornato in modo sincrono durante la creazione, l'aggiornamento o l'eliminazione di elementi. Ciò significa che la coerenza delle query di lettura sarà la [coerenza configurata per l'account.](consistency-levels.md)
- **Nessuno:** l'indicizzazione è disabilitata nel contenitore. Viene usato in genere quando un contenitore viene usato come archivio chiave-valore puro senza la necessità di indici secondari. Può anche essere utilizzato per migliorare le prestazioni delle operazioni bulk. Al termine delle operazioni in blocco, la modalità di indice può essere impostata su Coerente e quindi monitorata utilizzando [IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) fino al completamento.

> [!NOTE]
> Azure Cosmos DB supporta anche una modalità di indicizzazione lazy. L'indicizzazione differita esegue aggiornamenti all'indice a un livello di priorità molto più basso quando il motore non esegue altre operazioni. Ciò può causare risultati di query **incoerenti o incompleti.** Se si prevede di eseguire una query su un contenitore Cosmos, non selezionare l'indicizzazione differita.

Per impostazione predefinita, i `automatic`criteri di indicizzazione sono impostati su . Ciò si ottiene `automatic` impostando la proprietà nei `true`criteri di indicizzazione su . L'impostazione `true` di questa proprietà su consente ad Azure CosmosDB di indicizzare automaticamente i documenti così come vengono scritti.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Inclusione ed esclusione di percorsi di proprietà

Un criterio di indicizzazione personalizzato può specificare percorsi di proprietà inclusi o esclusi in modo esplicito dall'indicizzazione. Ottimizzando il numero di percorsi indicizzati, è possibile ridurre la quantità di spazio di archiviazione utilizzato dal contenitore e migliorare la latenza delle operazioni di scrittura. Questi percorsi vengono definiti seguendo [il metodo descritto nella sezione relativa alla panoramica dell'indicizzazione](index-overview.md#from-trees-to-property-paths) con le aggiunte seguenti:These paths are defined following the method described in the indexing overview section with the following additions:

- un percorso che porta a un valore scalare (stringa o numero) termina con`/?`
- elementi di una matrice sono `/[]` affrontati insieme `/0`attraverso `/1` la notazione (invece di , ecc.)
- il `/*` carattere jolly può essere utilizzato per abbinare qualsiasi elemento al di sotto del nodo

Riprendendo lo stesso esempio:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- il `headquarters` `employees` percorso è`/headquarters/employees/?`

- il `locations` `country` percorso è`/locations/[]/country/?`

- il percorso per `headquarters` qualsiasi cosa sotto è`/headquarters/*`

Ad esempio, potremmo `/headquarters/employees/?` includere il percorso. Questo percorso garantisce l'indicizzazione della proprietà employees ma non indicizza il codice JSON annidato aggiuntivo all'interno di questa proprietà.

## <a name="includeexclude-strategy"></a>Includi/escludi strategia

Qualsiasi criterio di indicizzazione deve `/*` includere il percorso radice come percorso incluso o escluso.

- Includere il percorso radice per escludere in modo selettivo i percorsi che non devono essere indicizzati. Questo è l'approccio consigliato in quanto consente ad Azure Cosmos DB di indicizzare in modo proattivo qualsiasi nuova proprietà che può essere aggiunta al modello.
- Escludere il percorso radice per includere in modo selettivo i percorsi che devono essere indicizzati.

- Per i percorsi con caratteri normali che includono: caratteri alfanumerici e _ (carattere di sottolineatura), non è necessario eseguire l'escape della stringa di percorso intorno alle virgolette doppie (ad esempio, "/path/?"). Per i percorsi con altri caratteri speciali, è necessario eseguire l'escape della stringa di percorso intorno alle virgolette doppie (ad esempio, "/\"path-abc\"/?"). Se ti aspetti caratteri speciali sul tuo percorso, puoi sfuggire a ogni percorso per la sicurezza. Funzionalmente non fa alcuna differenza se si esegue l'escape di ogni percorso Vs solo quelli che hanno caratteri speciali.

- La proprietà `_etag` di sistema è esclusa dall'indicizzazione per impostazione predefinita, a meno che l'etag non venga aggiunto al percorso incluso per l'indicizzazione.

- Se la modalità di **consistent**indicizzazione è `id` impostata su costante , le proprietà di sistema e `_ts` vengono indicizzate automaticamente.

Quando si includono ed escludii i tracciati, è possibile che si verifichino i seguenti attributi:

- `kind`può essere `range` `hash`uno o . La funzionalità dell'indice di intervallo fornisce tutte le funzionalità di un indice hash, pertanto è consigliabile usare un indice di intervallo.

- `precision`è un numero definito a livello di indice per i percorsi inclusi. Il valore `-1` di indica la precisione massima. È consigliabile impostare `-1`sempre questo valore su .

- `dataType`può essere `String` `Number`uno o . Ciò indica i tipi di proprietà JSON che verranno indicizzate.

Se non specificato, queste proprietà avranno i seguenti valori predefiniti:

| **Nome proprietà**     | **Valore predefinito** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` e `Number` |

Vedere [questa sezione](how-to-manage-indexing-policy.md#indexing-policy-examples) per gli esempi di criteri di indicizzazione per l'inclusione e l'esclusione di percorsi.

## <a name="spatial-indexes"></a>Indici spaziali

Quando si definisce un percorso spaziale nei criteri ```type``` di indicizzazione, è necessario definire l'indice da applicare a tale percorso. I tipi possibili per gli indici spaziali includono:Possible types for spatial indexes include:

* Point

* Polygon

* MultiPolygon

* LineString

Azure Cosmos DB, per impostazione predefinita, non creerà alcun indice spaziale. Se si desidera utilizzare funzioni predefinite SQL spaziali, è necessario creare un indice spaziale sulle proprietà necessarie. Vedere [questa sezione](geospatial.md) per gli esempi di criteri di indicizzazione per l'aggiunta di indici spaziali.

## <a name="composite-indexes"></a>Indici compositi

Le query `ORDER BY` che dispongono di una clausola con due o più proprietà richiedono un indice composito. È inoltre possibile definire un indice composito per migliorare le prestazioni di molte query di uguaglianza e intervallo. Per impostazione predefinita, non vengono definiti indici compositi, pertanto è necessario [aggiungere indici compositi](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) in base alle esigenze.

Quando si definisce un indice composito, è necessario specificare:When defining a composite index, you specify:

- Due o più percorsi di proprietà. La sequenza in cui vengono definiti i percorsi di proprietà è importante.

- L'ordine (crescente o decrescente).

> [!NOTE]
> Quando si aggiunge un indice composito, la query utilizzerà gli indici di intervallo esistenti fino al completamento della nuova addizione dell'indice composito. Pertanto, quando si aggiunge un indice composito, è possibile che non si osservino immediatamente i miglioramenti delle prestazioni. È possibile tenere traccia dello stato di avanzamento della trasformazione [dell'indice utilizzando uno degli SDK.](how-to-manage-indexing-policy.md)

### <a name="order-by-queries-on-multiple-properties"></a>Query ORDER BY su più proprietà:

Quando si usano indici compositi per query `ORDER BY` con una clausola con due o più proprietà, vengono utilizzate le considerazioni seguenti:The following considerations are used when using composite indexes for queries with an clause with two or more properties:

- Se i percorsi di indice composito `ORDER BY` non corrispondono alla sequenza delle proprietà nella clausola, l'indice composito non può supportare la query.

- Anche l'ordine dei percorsi di indice compositi (ascendenti o decrescenti) deve corrispondere `order` a nella `ORDER BY` clausola .

- L'indice composito `ORDER BY` supporta anche una clausola con l'ordine opposto in tutti i percorsi.

Si consideri l'esempio seguente in cui viene definito un indice composito in base a nome, età e _ts delle proprietà:Consider the following example where a composite index is defined on properties name, age, and _ts:

| **Indice composito**     | **Query `ORDER BY` di esempio**      | **Supportato da Composite Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

È consigliabile personalizzare i criteri di `ORDER BY` indicizzazione in modo da poter gestire tutte le query necessarie.

### <a name="queries-with-filters-on-multiple-properties"></a>Query con filtri su più proprietà

Se una query include filtri su due o più proprietà, può essere utile creare un indice composito per queste proprietà.

Si consideri ad esempio la query seguente che dispone di un filtro di uguaglianza su due proprietà:For example, consider the following query which has an equality filter on two properties:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Questa query sarà più efficiente, richiedendo meno tempo e richiedendo meno RU, se è in grado di sfruttare un indice composito su (nome ASC, età ASC).

Le query con filtri di intervallo possono anche essere ottimizzate con un indice composito. Tuttavia, la query può avere un solo filtro di intervallo. I filtri `>` `<`intervallo `>=`includono `!=`, , `<=`, e . Il filtro intervallo deve essere definito per ultimo nell'indice composito.

Si consideri la query seguente con filtri di uguaglianza e intervallo:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Questa query sarà più efficiente con un indice composito su (nome ASC, età ASC). Tuttavia, la query non utilizzerebbe un indice composito su (età ASC, nome ASC) perché i filtri di uguaglianza devono essere definiti prima nell'indice composito.

Le considerazioni seguenti vengono utilizzate quando si creano indici compositi per query con filtri su più proprietàThe following considerations are used when creating composite indexes for queries with filters on multiple properties

- Le proprietà nel filtro della query devono corrispondere a quelle nell'indice composito. Se una proprietà si trova nell'indice composito ma non è inclusa nella query come filtro, la query non utilizzerà l'indice composito.
- Se una query contiene proprietà aggiuntive nel filtro che non sono state definite in un indice composito, verrà utilizzata una combinazione di indici compositi e di intervallo per valutare la query. Ciò richiederà meno RU rispetto esclusivamente utilizzando gli indici di intervallo.
- Se una proprietà dispone`>`di `<` `<=`un `>=`filtro `!=`di intervallo ( , , , , o ), questa proprietà deve essere definita per ultima nell'indice composito. Se una query ha più di un filtro di intervallo, non utilizzerà l'indice composito.
- Quando si crea un indice composito `ORDER` per ottimizzare le query con più filtri, l'indice composito non avrà alcun impatto sui risultati. Questa proprietà è facoltativa.
- Se non si definisce un indice composito per una query con filtri su più proprietà, la query avrà comunque esito positivo. Tuttavia, il costo RU della query può essere ridotto con un indice composito.

Si considerino gli esempi seguenti in cui viene definito un indice composito in base a nome, età e timestamp delle proprietà:Consider the following examples where a composite index is defined on properties name, age, and timestamp:

| **Indice composito**     | **Query di esempio**      | **Supportato da Composite Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Query con un filtro e una clausola ORDER BY

Se una query filtra una o più proprietà e dispone di proprietà diverse nella clausola ORDER `ORDER BY` BY, può essere utile aggiungere le proprietà nel filtro alla clausola.

Ad esempio, aggiungendo le proprietà nel filtro alla clausola ORDER BY, la query seguente potrebbe essere riscritta per sfruttare un indice composito:

Eseguire query utilizzando l'indice di intervallo:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Query tramite indice composito:Query using composite index:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Le stesse ottimizzazioni di modello e query possono essere generalizzate per le query con più filtri di uguaglianza:The same pattern and query optimizations can be generalized for queries with multiple equality filters:

Eseguire query utilizzando l'indice di intervallo:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Query tramite indice composito:Query using composite index:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Quando si creano indici compositi per ottimizzare una `ORDER BY` query con un filtro e una clausola, vengono utilizzate le considerazioni seguenti:The following considerations are used when creating composite indexes to optimize a query with a filter and clause:

* Se la query filtra le proprietà, queste `ORDER BY` devono essere incluse per prime nella clausola.
* Se non si definisce un indice composito in una `ORDER BY` query con un filtro su una proprietà e una clausola separata utilizzando una proprietà diversa, la query avrà comunque esito positivo. Tuttavia, il costo RU della query può essere ridotto con `ORDER BY` un indice composito, in particolare se la proprietà nella clausola ha un'elevata cardinalità.
* Tutte le considerazioni per `ORDER BY` la creazione di indici compositi per le query con più proprietà, nonché le query con filtri su più proprietà sono comunque valide.


| **Indice composito**                      | **Query `ORDER BY` di esempio**                                  | **Supportato da Composite Index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Modifica dei criteri di indicizzazione

I criteri di indicizzazione di un contenitore possono essere aggiornati in qualsiasi momento usando il portale di [Azure o uno degli SDK supportati.](how-to-manage-indexing-policy.md) Un aggiornamento del criterio di indicizzazione attiva una trasformazione dall'indice precedente a quello nuovo, che viene eseguito online e sul posto (in modo che non venga utilizzato spazio di archiviazione aggiuntivo durante l'operazione). L'indice dei criteri precedenti viene trasformato in modo efficiente nel nuovo criterio senza influire sulla disponibilità di scrittura o sulla velocità effettiva di cui è stato eseguito il provisioning nel contenitore. La trasformazione dell'indice è un'operazione asincrona e il tempo necessario per il completamento dipende dalla velocità effettiva di cui è stato eseguito il provisioning, dal numero di elementi e dalle relative dimensioni.

> [!NOTE]
> Durante l'aggiunta di un intervallo o di un indice spaziale, le query potrebbero non restituire tutti i risultati corrispondenti e lo faranno senza restituire errori. Ciò significa che i risultati della query potrebbero non essere coerenti fino al completamento della trasformazione dell'indice. È possibile tenere traccia dello stato di avanzamento della trasformazione [dell'indice utilizzando uno degli SDK.](how-to-manage-indexing-policy.md)

Se la modalità del nuovo criterio di indicizzazione è impostata su Coerente, non è possibile applicare altre modifiche ai criteri di indicizzazione mentre è in corso la trasformazione dell'indice. Una trasformazione dell'indice in esecuzione può essere annullata impostando la modalità del criterio di indicizzazione su Nessuno (che interromperà immediatamente l'indice).

## <a name="indexing-policies-and-ttl"></a>Criteri di indicizzazione e TTL

La [funzionalità Time-to-Live (TTL)](time-to-live.md) richiede che l'indicizzazione sia attiva nel contenitore in cui è attivata. Ciò significa che:

- non è possibile attivare TTL su un contenitore in cui la modalità di indicizzazione è impostata su Nessuno,
- non è possibile impostare la modalità di indicizzazione su None in un contenitore in cui è attivato TTL.

Per gli scenari in cui non è necessario indicizzare il percorso di proprietà, ma è necessario TTL, è possibile usare un criterio di indicizzazione con:For scenarios where no property path needs to be indexed, but TTL is required, you can use an indexing policy with:

- una modalità di indicizzazione impostata su Coerente e
- nessun percorso incluso, e
- `/*`come unico percorso escluso.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Panoramica dell'indicizzazione](index-overview.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
