---
title: Indicizzazione in Azure Cosmos DB
description: Informazioni sull'indicizzazione in Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: 44706e5ebe2442dcb45dfc45e2c322938cf7dca9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068648"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>L'indicizzazione in Azure Cosmos DB - Panoramica

Azure Cosmos DB è un database indipendente dallo schema che consente di eseguire l'iterazione dell'applicazione senza dover gestire schemi o indici. Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutte le proprietà per tutti gli elementi di [contenitore](databases-containers-items.md#azure-cosmos-containers) senza che sia necessario definire alcuno schema o configurare gli indici secondari.

L'obiettivo di questo articolo è spiegare come Azure Cosmos DB indicizza i dati e utilizzo degli indici per migliorare le prestazioni delle query. È consigliabile leggere attentamente questa sezione prima di esaminare come personalizzare [criteri di indicizzazione](index-policy.md).

## <a name="from-items-to-trees"></a>Dagli elementi agli alberi

Ogni volta che un elemento viene archiviato in un contenitore, il relativo contenuto viene proiettato come documento JSON e quindi convertito in una rappresentazione ad albero. Ciò significa che tutte le proprietà di tale elemento viene rappresentata come nodo in una struttura ad albero. Un nodo radice pseudo viene creato come elemento padre di tutte le proprietà di primo livello dell'elemento. I nodi foglia contengono i valori scalari effettivi appartenente a un elemento.

Ad esempio, prendere in considerazione questo elemento:

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

Si sarebbe rappresentato dall'albero delle seguente:

![L'elemento precedente rappresentata come albero](./media/index-overview/item-as-tree.png)

Si noti come le matrici vengono codificate nell'albero: ogni voce in una matrice Ottiene un nodo intermedio contrassegnato con l'indice della voce all'interno della matrice (0, 1 e così via.).

## <a name="from-trees-to-property-paths"></a>Dagli alberi per i percorsi delle proprietà

Il motivo per cui Azure Cosmos DB Trasforma gli elementi in strutture ad albero è perché consente le proprietà a cui fa riferimento i relativi percorsi all'interno degli alberi. Per ottenere il percorso per una proprietà, è possibile attraversare l'albero del nodo principale per tale proprietà e concatenare le etichette di ogni nodo attraversata.

Di seguito sono elencati i percorsi per ogni proprietà dalla voce di esempio descritto in precedenza:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Quando viene scritto un elemento, Azure Cosmos DB indicizza in modo efficace percorso di ogni proprietà e il valore corrispondente.

## <a name="index-kinds"></a>Tipi di indice

Azure Cosmos DB supporta attualmente due tipi di indici:

Il **intervallo** tipologia di indice viene usato per:

- query di uguaglianza: 

   ```sql SELECT * FROM container c WHERE c.property = 'value'```

- Query di intervallo: 

   ```sql SELECT * FROM container c WHERE c.property > 'value'``` (adatto `>`, `<`, `>=`, `<=`, `!=`)

- `ORDER BY` query:

   ```sql SELECT * FROM container c ORDER BY c.property```

- `JOIN` query: 

   ```sql SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'```

Gli indici di intervallo possono essere utilizzati con valori scalari (stringa o numero).

Il **spaziali** tipologia di indice viene usato per:

- query di distanza geospaziali: 

   ```sql SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40```

- geospaziali all'interno di query: 

   ```sql SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })```

Gli indici spaziali sono utilizzabili in formato corretto [GeoJSON](geospatial.md) oggetti. Punti, oggetti linestring e poligoni sono attualmente supportati.

Il **composito** tipologia di indice viene usato per:

- `ORDER BY` query su più proprietà: 

   ```sql SELECT * FROM container c ORDER BY c.firstName, c.lastName```

## <a name="querying-with-indexes"></a>Esecuzione di query con indici

I percorsi estratti durante l'indicizzazione dei dati rendono più semplice per la ricerca dell'indice durante l'elaborazione di una query. Creando una corrispondenza tra il `WHERE` clausola di una query con l'elenco di percorsi indicizzati, è possibile identificare gli elementi che corrispondono al predicato di query molto rapidamente.

Ad esempio, si consideri la seguente query: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Il predicato della query (filtrando gli elementi, in qualsiasi posizione ha "Francia" paese) sarebbe corrisponde al percorso evidenziato in rosso riportata di seguito:

![Corrispondenza di un percorso specifico all'interno di una struttura ad albero](./media/index-overview/matching-path.png)

> [!NOTE]
> Un' `ORDER BY` clausola che ordina in base a una singola proprietà *sempre* necessita di un intervallo di indicizzare e avrà esito negativo se il percorso fa riferimento non lo possiede. Analogamente, un multiplo `ORDER BY` query *sempre* necessita di un indice composto.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Criterio di indicizzazione](index-policy.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
