---
title: Indicizzazione in Azure Cosmos DB
description: Comprendere il funzionamento dell'indicizzazione in Azure Cosmos DB, diversi tipi di indici, ad esempio Range, Spatial, indici compositi supportati.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: thweiss
ms.openlocfilehash: 684799ee12715c789910accf80aa5b4afec763d4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273240"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indicizzazione in Azure Cosmos DB - Panoramica

Database Cosmos di Azure è un database indipendente dall'schema che consente di eseguire l'iterazione sull'applicazione senza dover gestire la gestione dello schema o dell'indice. Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente ogni proprietà per tutti gli elementi nel [contenitore](databases-containers-items.md#azure-cosmos-containers) senza dover definire alcuno schema o configurare gli indici secondari.

Lo scopo di questo articolo è spiegare come Azure Cosmos DB indicizza i dati e come usa gli indici per migliorare le prestazioni delle query. Si consiglia di esaminare questa sezione prima di esaminare come personalizzare [i criteri di indicizzazione.](index-policy.md)

## <a name="from-items-to-trees"></a>Dagli elementi agli alberi

Ogni volta che un elemento viene archiviato in un contenitore, il relativo contenuto viene proiettato come documento JSON, quindi convertito in una rappresentazione della struttura ad albero. Ciò significa che ogni proprietà di tale elemento viene rappresentata come nodo in una struttura ad albero. Uno pseudo nodo radice viene creato come elemento padre per tutte le proprietà di primo livello dell'elemento. I nodi foglia contengono i valori scalari effettivi trasportati da un elemento.

Ad esempio, si consideri questo elemento:As an example, consider this item:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Sarebbe rappresentato dal seguente albero:

![L'elemento precedente rappresentato come struttura ad albero](./media/index-overview/item-as-tree.png)

Si noti come le matrici sono codificate nella struttura ad albero: ogni voce in una matrice ottiene un nodo intermedio etichettato con l'indice di tale voce all'interno della matrice (0, 1 e così via).

## <a name="from-trees-to-property-paths"></a>Dagli alberi ai sentieri di proprietà

Il motivo per cui Azure Cosmos DB trasforma gli elementi in alberi è perché consente alle proprietà di fare riferimento ai relativi percorsi all'interno di tali alberi. Per ottenere il percorso di una proprietà, è possibile attraversare l'albero dal nodo radice a tale proprietà e concatenare le etichette di ogni nodo attraversato.

Di seguito sono riportati i percorsi per ogni proprietà dall'elemento di esempio descritto in precedenza:Here are the paths for each property from the example item described above:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Quando viene scritto un elemento, Azure Cosmos DB indicizza in modo efficace il percorso di ogni proprietà e il valore corrispondente.

## <a name="index-kinds"></a>Tipi di indice

Azure Cosmos DB supporta attualmente tre tipi di indici.

### <a name="range-index"></a>Indice intervallo

**L'indice** dell'intervallo è basato su una struttura ad albero ordinata. Il tipo di indice dell'intervallo viene utilizzato per:

- Query di uguaglianza:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Corrispondenza di uguaglianza in un elemento della matriceEquality match on an array element
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Query di intervallo:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (funziona `>`per `<` `>=`, `<=` `!=`, , , )

- Verifica della presenza di un immobile:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Corrispondenze di prefisso stringa (la parola chiave CONTAINS non sfrutterà l'indice dell'intervallo):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`Query:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`Query:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Gli indici di intervallo possono essere utilizzati su valori scalari (stringa o numero).

### <a name="spatial-index"></a>Indice spaziale

**Gli** indici spaziali consentono query efficienti su oggetti geospaziali quali punti, linee, poligoni e poligono multiplo. Queste query utilizzano ST_DISTANCE, ST_WITHIN ST_INTERSECTS parole chiave. Di seguito sono riportati alcuni esempi che usano il tipo di indice spaziale:The following are some examples that use spatial index kind:

- Query sulla distanza geospaziale:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geospaziale all'interno delle query:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Query di intersezione geospaziale:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Gli indici spaziali possono essere utilizzati su oggetti [GeoJSON](geospatial.md) formattati correttamente. Points, LineStrings, Polygons e MultiPolygons sono attualmente supportati.

### <a name="composite-indexes"></a>Indici compositi

Gli indici **compositi** aumentano l'efficienza quando si eseguono operazioni su più campi. Il tipo di indice composito viene utilizzato per:The composite index kind is used for:

- `ORDER BY`query su più proprietà:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Esegue una query `ORDER BY`con un filtro e . Queste query possono utilizzare un indice composito `ORDER BY` se la proprietà filter viene aggiunta alla clausola.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Query con un filtro su due o più proprietà in cui almeno una proprietà è un filtro di uguaglianza

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Se un predicato di filtro utilizza uno del tipo di indice, il motore di query lo valuterà prima di analizzare il resto. Ad esempio, se si dispone di una query SQL come`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* La query precedente verrà innanzitutto filtrare le voci in cui firstName -"Andrew" utilizzando l'indice. Passa quindi tutte le voci firstName - "Andrew" tramite una pipeline successiva per valutare il predicato del filtro CONTAINS.

* È possibile velocizzare le query ed evitare scansioni complete del contenitore quando si usano funzioni che non usano l'indice (ad esempio CONTAINS) aggiungendo predicati di filtro aggiuntivi che utilizzano l'indice. L'ordine delle clausole di filtro non è importante. Il motore di query è capire quali predicati sono più selettivi ed eseguire la query di conseguenza.


## <a name="querying-with-indexes"></a>Esecuzione di query con indici

I percorsi estratti durante l'indicizzazione dei dati semplificano la ricerca dell'indice durante l'elaborazione di una query. Confrontando `WHERE` la clausola di una query con l'elenco dei percorsi indicizzati, è possibile identificare gli elementi che corrispondono al predicato della query molto rapidamente.

Si consideri ad esempio `SELECT location FROM location IN company.locations WHERE location.country = 'France'`la query seguente: . Il predicato della query (filtro sugli elementi, in cui qualsiasi località ha "Francia" come paese) corrisponderebbe al percorso evidenziato in rosso di seguito:

![Corrispondenza di un percorso specifico all'interno di un albero](./media/index-overview/matching-path.png)

> [!NOTE]
> Una `ORDER BY` clausola che ordina in base a una singola proprietà ha *sempre* bisogno di un indice di intervallo e avrà esito negativo se il percorso a cui fa riferimento non ne ha uno. Analogamente, `ORDER BY` una query che ordina in base a più proprietà richiede *sempre* un indice composito.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Criterio di indicizzazione](index-policy.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
