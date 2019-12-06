---
title: Indicizzazione in Azure Cosmos DB
description: Informazioni sul funzionamento dell'indicizzazione in Azure Cosmos DB, diversi tipi di indici, ad esempio intervallo, spaziale e indici compositi supportati.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873625"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indicizzazione in Azure Cosmos DB-Panoramica

Azure Cosmos DB è un database indipendente dallo schema che consente di eseguire l'iterazione dell'applicazione senza dover gestire lo schema o la gestione degli indici. Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente ogni proprietà per tutti gli elementi nel [contenitore](databases-containers-items.md#azure-cosmos-containers) senza dover definire alcuno schema o configurare indici secondari.

Lo scopo di questo articolo è spiegare come Azure Cosmos DB indicizza i dati e come usa gli indici per migliorare le prestazioni delle query. È consigliabile eseguire questa sezione prima di esplorare come personalizzare i criteri di [indicizzazione](index-policy.md).

## <a name="from-items-to-trees"></a>Da elementi a alberi

Ogni volta che un elemento viene archiviato in un contenitore, il relativo contenuto viene proiettato come documento JSON e quindi convertito in una rappresentazione ad albero. Ciò significa che ogni proprietà di tale elemento viene rappresentata come nodo in un albero. Un nodo pseudo-radice viene creato come elemento padre per tutte le proprietà di primo livello dell'elemento. I nodi foglia contengono i valori scalari effettivi di un elemento.

Si consideri ad esempio questo elemento:

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

Verrà rappresentato dall'albero seguente:

![Elemento precedente rappresentato come albero](./media/index-overview/item-as-tree.png)

Si noti il modo in cui le matrici vengono codificate nella struttura ad albero: ogni voce in una matrice ottiene un nodo intermedio etichettato con l'indice di tale voce all'interno della matrice (0, 1 e così via).

## <a name="from-trees-to-property-paths"></a>Dagli alberi ai percorsi delle proprietà

Il motivo per cui Azure Cosmos DB trasforma gli elementi in alberi è dato dal fatto che consente di fare riferimento alle proprietà in base ai relativi percorsi all'interno di tali alberi. Per ottenere il percorso di una proprietà, è possibile attraversare l'albero dal nodo radice a tale proprietà e concatenare le etichette di ogni nodo attraversato.

Ecco i percorsi per ogni proprietà dall'elemento di esempio descritto in precedenza:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Quando viene scritto un elemento, Azure Cosmos DB indicizza in modo efficace il percorso di ogni proprietà e il relativo valore corrispondente.

## <a name="index-kinds"></a>Tipi di indice

Azure Cosmos DB supporta attualmente tre tipi di indici.

### <a name="range-index"></a>Indice intervallo

L'indice di **intervallo** è basato su una struttura di tipo albero ordinata. Il tipo di indice di intervallo viene usato per:

- Query di uguaglianza:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Corrispondenza di uguaglianza su un elemento di matrice
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- Query di intervallo:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (funziona per `>`, `<`, `>=`, `<=``!=`)

- Verifica della presenza di una proprietà:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Corrispondenze Prefisso stringa (CONTAINs parola chiave non utilizzerà l'indice di intervallo):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- query `ORDER BY`:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- query `JOIN`:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Gli indici di intervallo possono essere utilizzati in valori scalari (stringa o numero).

### <a name="spatial-index"></a>Indice spaziale

Gli indici **spaziali** consentono di eseguire query efficienti su oggetti geospaziali quali punti, linee, poligoni e multipoligoni. Queste query usano parole chiave ST_DISTANCE, ST_WITHIN ST_INTERSECTS. Di seguito sono riportati alcuni esempi che usano il tipo di indice spaziale:

- Query di distanza geospaziale:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geospaziale all'interno di query:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Query di intersezione geospaziale:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Gli indici spaziali possono essere usati in oggetti [GeoJSON](geospatial.md) formattati correttamente. Punti, oggetti LineString, poligoni e multipoligoni sono attualmente supportati.

### <a name="composite-indexes"></a>Indici compositi

Gli indici **compositi** aumentano l'efficienza quando si eseguono operazioni su più campi. Il tipo di indice composito viene usato per:

- `ORDER BY` query su più proprietà:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Esegue una query con un filtro e un `ORDER BY`. Queste query possono utilizzare un indice composto se la proprietà Filter viene aggiunta alla clausola `ORDER BY`.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Esegue una query con un filtro su due o più proprietà in cui almeno una proprietà è un filtro di uguaglianza

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Finché un predicato del filtro USA sul tipo di indice, il motore di query lo valuterà prima di analizzare il resto. Ad esempio, se si dispone di una query SQL come `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* La query precedente filtra innanzitutto le voci in cui firstName = "Andrew" viene utilizzato l'indice. Passa quindi tutte le voci firstName = "Andrew" tramite una pipeline successiva per valutare il predicato del filtro CONTAINs.

* È possibile velocizzare le query ed evitare scansioni complete del contenitore quando si usano funzioni che non usano l'indice (ad esempio CONTAINs) aggiungendo predicati di filtro aggiuntivi che usano l'indice. L'ordine delle clausole di filtro non è importante. Il motore di query consente di individuare i predicati più selettivi ed eseguire la query di conseguenza.


## <a name="querying-with-indexes"></a>Esecuzione di query con indici

I percorsi estratti durante l'indicizzazione dei dati facilitano la ricerca dell'indice durante l'elaborazione di una query. Abbinando la clausola `WHERE` di una query con l'elenco dei percorsi indicizzati, è possibile identificare molto rapidamente gli elementi che corrispondono al predicato della query.

Si consideri, ad esempio, la query seguente: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Il predicato della query (filtraggio per gli elementi, dove qualsiasi località ha "Francia" come paese) corrisponderà al percorso evidenziato in rosso sotto:

![Corrispondenza di un percorso specifico all'interno di un albero](./media/index-overview/matching-path.png)

> [!NOTE]
> Una clausola `ORDER BY` che Orders by a una singola proprietà necessita *sempre* di un indice di intervallo e avrà esito negativo se il percorso a cui fa riferimento non ne ha uno. Analogamente, un `ORDER BY` query per la quale Orders by multiple properties necessita *sempre* di un indice composto.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Criteri di indicizzazione](index-policy.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
