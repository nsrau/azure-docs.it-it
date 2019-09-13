---
title: Criteri di indicizzazione di Azure Cosmos DB
description: Informazioni su come configurare e modificare i criteri di indicizzazione predefiniti per l'indicizzazione automatica e prestazioni maggiori in Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 86ac042bdddce36f00be71cc5109618bec909d90
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914181"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Criteri di indicizzazione in Azure Cosmos DB

In Azure Cosmos DB, ogni contenitore dispone di un criterio di indicizzazione che determina come devono essere indicizzati gli elementi del contenitore. I criteri di indicizzazione predefiniti per i contenitori appena creati indicizzano ogni proprietà di ogni elemento, applicando gli indici di intervallo per qualsiasi stringa o numero e indici spaziali per qualsiasi oggetto GeoJSON di tipo Point. Ciò consente di ottenere prestazioni di query elevate senza dover pensare all'indicizzazione e alla gestione degli indici in anticipo.

In alcune situazioni, potrebbe essere necessario eseguire l'override di questo comportamento automatico per soddisfare al meglio le proprie esigenze. È possibile personalizzare i criteri di indicizzazione di un contenitore impostando la *modalità di indicizzazione*e includendo o escludendo i *percorsi delle proprietà*.

> [!NOTE]
> Il metodo di aggiornamento dei criteri di indicizzazione descritti in questo articolo si applica solo all'API SQL (Core) di Azure Cosmos DB.

## <a name="indexing-mode"></a>Modalità di indicizzazione

Azure Cosmos DB supporta due modalità di indicizzazione:

- **Coerente**: Se i criteri di indicizzazione di un contenitore sono impostati su coerenti, l'indice viene aggiornato in modo sincrono durante la creazione, l'aggiornamento o l'eliminazione di elementi. Ciò significa che la coerenza delle query di lettura sarà la [coerenza configurata per l'account](consistency-levels.md).
- **Nessuna**: Se i criteri di indicizzazione di un contenitore sono impostati su None, l'indicizzazione viene disabilitata in tale contenitore. Questa operazione viene in genere usata quando un contenitore viene usato come archivio chiave-valore puro senza la necessità di indici secondari. Consente inoltre di velocizzare le operazioni di inserimento bulk.

Inoltre, è necessario impostare la proprietà **Automatic** nei criteri di indicizzazione su **true**. L'impostazione di questa proprietà su true consente Azure Cosmos DB di indicizzare automaticamente i documenti man mano che vengono scritti.

## <a name="including-and-excluding-property-paths"></a>Inclusione ed esclusione dei percorsi delle proprietà

Un criterio di indicizzazione personalizzato può specificare percorsi di proprietà inclusi o esclusi in modo esplicito dall'indicizzazione. Ottimizzando il numero di percorsi indicizzati, è possibile ridurre la quantità di spazio di archiviazione usato dal contenitore e migliorare la latenza delle operazioni di scrittura. Questi percorsi vengono definiti seguendo [il metodo descritto nella sezione Panoramica dell'indicizzazione](index-overview.md#from-trees-to-property-paths) con le aggiunte seguenti:

- un percorso che conduce a un valore scalare (stringa o numero) termina con`/?`
- gli elementi di una matrice vengono risolti insieme `/[]` tramite la notazione `/0`, anziché `/1` e così via.
- il `/*` carattere jolly può essere usato per trovare la corrispondenza con qualsiasi elemento al di sotto del nodo

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

- il `headquarters`percorso `employees` di è`/headquarters/employees/?`

- il `locations`percorsoè `country``/locations/[]/country/?`

- il percorso di qualsiasi elemento `headquarters` in è`/headquarters/*`

Ad esempio, è possibile includere il `/headquarters/employees/?` percorso. Questo percorso garantisce che la proprietà Employees venga indicizzata, ma non verrà indicizzato un altro JSON annidato all'interno di questa proprietà.

## <a name="includeexclude-strategy"></a>Includi/Escludi strategia

Tutti i criteri di indicizzazione devono includere il `/*` percorso radice come un percorso incluso o escluso.

- Includere il percorso radice per escludere in modo selettivo i percorsi che non devono essere indicizzati. Si tratta dell'approccio consigliato, in quanto consente Azure Cosmos DB indicizzare in modo proattivo tutte le nuove proprietà che possono essere aggiunte al modello.
- Escludere il percorso radice per includere in modo selettivo i percorsi che devono essere indicizzati.

- Per i percorsi con caratteri regolari che includono caratteri alfanumerici e _ (carattere di sottolineatura), non è necessario eseguire l'escape della stringa di percorso intorno alle virgolette doppie (ad esempio, "/Path/?"). Per i percorsi con altri caratteri speciali, è necessario eseguire l'escape della stringa di percorso intorno alle virgolette doppie (\"ad esempio,\""/Path-ABC/?"). Se si prevede che nel percorso siano presenti caratteri speciali, è possibile evitare ogni percorso per la sicurezza. Dal punto di vista funzionale, non fa alcuna differenza se si Escape ogni percorso rispetto a quelli che contengono caratteri speciali.

- Per impostazione predefinita, la proprietà di sistema "ETag" viene esclusa dall'indicizzazione, a meno che l'eTag non venga aggiunto al percorso incluso per l'indicizzazione.

Quando si includono ed escludono i percorsi, è possibile che si verifichino gli attributi seguenti:

- `kind`può essere `range` o `hash`. La funzionalità degli indici di intervallo fornisce tutte le funzionalità di un indice hash, quindi è consigliabile usare un indice di intervallo.

- `precision`numero definito a livello di indice per i percorsi inclusi. Il valore `-1` indica la precisione massima. È consigliabile impostare sempre questo valore su `-1`.

- `dataType`può essere `String` o `Number`. Indica i tipi di proprietà JSON che saranno indicizzate.

Quando non è specificato, queste proprietà avranno i valori predefiniti seguenti:

| **Nome proprietà**     | **Valore predefinito** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` e `Number` |

Per esempi di criteri di indicizzazione per l'inclusione e l'esclusione di percorsi, vedere [questa sezione](how-to-manage-indexing-policy.md#indexing-policy-examples) .

## <a name="spatial-indexes"></a>Indici spaziali

Quando si definisce un percorso spaziale nei criteri di indicizzazione, è necessario definire quale ```type``` Indice applicare a tale percorso. I tipi possibili per gli indici spaziali includono:

* Punto

* Polygon

* MultiPolygon

* LineString

Per impostazione predefinita, Azure Cosmos DB non creerà indici spaziali. Se si desidera utilizzare le funzioni predefinite di SQL spaziali, è necessario creare un indice spaziale sulle proprietà obbligatorie. Vedere [questa sezione](geospatial.md) per esempi di criteri di indicizzazione per l'aggiunta di indici spaziali.

## <a name="composite-indexes"></a>Indici compositi

Per le query con `ORDER BY` una clausola con due o più proprietà è necessario un indice composto. È anche possibile definire un indice composito per migliorare le prestazioni di molte query di uguaglianza e di intervallo. Per impostazione predefinita, non sono definiti indici compositi, pertanto è necessario [aggiungere indici compositi](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) in base alle esigenze.

Quando si definisce un indice composito, è necessario specificare:

- Due o più percorsi di proprietà. Sequenza in cui vengono definiti i percorsi delle proprietà.

- Ordine (crescente o decrescente).

> [!NOTE]
> Quando si aggiunge un indice composito, come per gli altri tipi di indice, è possibile che le query restituiscano risultati incoerenti durante l'aggiornamento dell'indice.

### <a name="order-by-queries-on-multiple-properties"></a>Ordina per query su più proprietà:

Quando si usano indici compositi per le query con una `ORDER BY` clausola con due o più proprietà, vengono usate le considerazioni seguenti:

- Se i percorsi di indice composito non corrispondono alla sequenza delle proprietà nella `ORDER BY` clausola, l'indice composito non può supportare la query.

- L'ordine dei percorsi di indice composito (crescente o decrescente) deve corrispondere `order` anche `ORDER BY` a nella clausola.

- L'indice composito supporta anche `ORDER BY` una clausola con l'ordine opposto su tutti i percorsi.

Si consideri l'esempio seguente in cui viene definito un indice composito per le proprietà Name, Age e _ts:

| **Indice composto**     | **Query `ORDER BY` di esempio**      | **Supportato da un indice composito?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

È necessario personalizzare i criteri di indicizzazione per poter soddisfare tutte `ORDER BY` le query necessarie.

### <a name="queries-with-filters-on-multiple-properties"></a>Query con filtri su più proprietà

Se una query dispone di filtri su due o più proprietà, potrebbe essere utile creare un indice composto per queste proprietà.

Si consideri, ad esempio, la query seguente che presenta un filtro di uguaglianza per due proprietà:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Questa query sarà più efficiente, richiedendo meno tempo e consumando meno ur, se è in grado di sfruttare un indice composto in (nome ASC, Age ASC).

È possibile ottimizzare le query con filtri di intervallo anche con un indice composto. Tuttavia, la query può avere solo un filtro di intervallo singolo. I filtri di `>`intervallo `<`includono `<=`, `>=`,, `!=`e. Il filtro di intervallo deve essere definito per ultimo nell'indice composto.

Si consideri la query seguente con i filtri di uguaglianza e di intervallo:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Questa query sarà più efficiente con un indice composto in (nome ASC, Age ASC). Tuttavia, la query non utilizzerebbe un indice composto su (Age ASC, Name ASC) perché i filtri di uguaglianza devono essere definiti per primi nell'indice composto.

Quando si creano indici compositi per le query con filtri su più proprietà, vengono usate le considerazioni seguenti.

- Le proprietà nel filtro della query devono corrispondere a quelle nell'indice composto. Se una proprietà è nell'indice composto ma non è inclusa nella query come filtro, la query non utilizzerà l'indice composto.
- Se una query include proprietà aggiuntive nel filtro che non sono state definite in un indice composito, per valutare la query verrà utilizzata una combinazione di indici composti e di intervallo. Questa operazione richiederà meno ur rispetto all'uso esclusivo degli indici di intervallo.
- Se una proprietà ha un filtro di intervallo`>`( `<`, `<=`, `>=`, o `!=`), questa proprietà deve essere definita per ultima nell'indice composto. Se una query contiene più di un filtro di intervallo, non utilizzerà l'indice composto.
- Quando si crea un indice composto per ottimizzare le query con più filtri `ORDER` , l'oggetto dell'indice composto non avrà alcun effetto sui risultati. Questa proprietà è facoltativa.
- Se non si definisce un indice composto per una query con filtri su più proprietà, la query avrà comunque esito positivo. Tuttavia, il costo ur della query può essere ridotto con un indice composto.

Si considerino gli esempi seguenti in cui viene definito un indice composito per le proprietà Name, Age e timestamp:

| **Indice composto**     | **Query di esempio**      | **Supportato da un indice composito?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Query con un filtro e una clausola ORDER BY

Se una query Filtra in base a una o più proprietà e dispone di proprietà diverse nella clausola ORDER BY, può essere utile aggiungere le proprietà del filtro alla `ORDER BY` clausola.

Se ad esempio si aggiungono le proprietà nel filtro alla clausola ORDER BY, è possibile riscrivere la query seguente per sfruttare un indice composto:

Query con l'indice di intervallo:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Query con indice composito:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Lo stesso modello e le stesse ottimizzazioni di query possono essere generalizzate per le query con più filtri di uguaglianza:

Query con l'indice di intervallo:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Query con indice composito:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Quando si creano indici compositi per ottimizzare una query con un filtro e una clausola `ORDER BY` , vengono utilizzate le considerazioni seguenti:

* Se la query Filtra le proprietà, è necessario includerle prima nella `ORDER BY` clausola.
* Se non si definisce un indice composto in una query con un filtro su una proprietà e una clausola separata `ORDER BY` con una proprietà diversa, la query avrà comunque esito positivo. Tuttavia, il costo ur della query può essere ridotto con un indice composito, in particolare se la proprietà nella `ORDER BY` clausola ha una cardinalità elevata.
* Sono comunque valide tutte le considerazioni per `ORDER BY` la creazione di indici compositi per le query con più proprietà e per le query con filtri su più proprietà.


| **Indice composto**                      | **Query `ORDER BY` di esempio**                                  | **Supportato da un indice composito?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Modifica dei criteri di indicizzazione

I criteri di indicizzazione di un contenitore possono essere aggiornati in qualsiasi momento [usando il portale di Azure o uno degli SDK supportati](how-to-manage-indexing-policy.md). Un aggiornamento del criterio di indicizzazione attiva una trasformazione dall'indice precedente a quello nuovo, che viene eseguito online e sul posto (pertanto non viene utilizzato alcuno spazio di archiviazione aggiuntivo durante l'operazione). L'indice del criterio precedente viene trasformato in modo efficiente nei nuovi criteri senza influire sulla disponibilità di scrittura o sulla velocità effettiva di cui è stato effettuato il provisioning nel contenitore. La trasformazione dell'indice è un'operazione asincrona e il tempo necessario per il completamento dipende dalla velocità effettiva con provisioning, dal numero di elementi e dalle relative dimensioni.

> [!NOTE]
> Mentre è in corso la reindicizzazione, è possibile che le query non restituiscano tutti i risultati corrispondenti, senza restituire alcun errore. Ciò significa che i risultati della query potrebbero non essere coerenti fino al completamento della trasformazione dell'indice. È possibile tenere traccia dello stato di avanzamento della trasformazione dell'indice [usando uno degli SDK](how-to-manage-indexing-policy.md).

Se la nuova modalità dei criteri di indicizzazione è impostata su coerente, non è possibile applicare altre modifiche ai criteri di indicizzazione mentre è in corso la trasformazione dell'indice. Una trasformazione dell'indice in esecuzione può essere annullata impostando la modalità del criterio di indicizzazione su None (che eliminerà immediatamente l'indice).

## <a name="indexing-policies-and-ttl"></a>Criteri di indicizzazione e TTL

La [funzionalità TTL (time-to-Live)](time-to-live.md) richiede che l'indicizzazione sia attiva nel contenitore in cui è attivata. Vale a dire che:

- non è possibile attivare la durata (TTL) in un contenitore in cui la modalità di indicizzazione è impostata su None,
- non è possibile impostare la modalità di indicizzazione su None in un contenitore in cui è attivato TTL.

Per gli scenari in cui non è necessario indicizzare alcun percorso di proprietà, ma è necessario specificare un criterio di indicizzazione con:

- modalità di indicizzazione impostata su coerente e
- nessun percorso incluso e
- `/*`come unico percorso escluso.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Panoramica dell'indicizzazione](index-overview.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
