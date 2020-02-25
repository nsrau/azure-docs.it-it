---
title: Indicizzare i dati geospaziali con Azure Cosmos DB
description: Indicizzare i dati spaziali con Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566374"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indicizzare i dati geospaziali con Azure Cosmos DB

Il motore di database di Azure Cosmos DB è stato progettato in modo da essere effettivamente indipendente dallo schema e fornire supporto di prima classe per JSON. Il motore di database ottimizzato per la scrittura di Azure Cosmos DB riconosce in modo nativo i dati spaziali rappresentati nello standard GeoJSON.

In breve, la geometria è proiettata dalle coordinate geodetiche su un piano 2D, quindi suddivisa progressivamente in celle utilizzando un **quadtree**. Queste celle vengono mappate in 1D in base alla posizione della cella all'interno di una **curva di riempimento dello spazio di Hilbert**, che consente di mantenere la posizione dei punti. Quando i dati di località vengono indicizzati, passano attraverso un processo noto come **mosaico**: tutte le celle che intersecano una posizione vengono identificate e archiviate come chiavi nell'indice di Azure Cosmos DB. In fase di query, anche argomenti come punti e poligoni sono tassellati per estrarre gli intervalli degli ID delle celle pertinenti e quindi usati per recuperare dati dall'indice.

Se si specifica un criterio di indicizzazione che include l'indice spaziale per/* (tutti i percorsi), tutti i dati trovati all'interno del contenitore vengono indicizzati per query spaziali efficienti.

> [!NOTE]
> Azure Cosmos DB supporta l'indicizzazione di punti, LineString, poligoni e multipoligoni
>
>

## <a name="geography-data-indexing-examples"></a>Esempi di indicizzazione dei dati geography

Il frammento di codice JSON seguente mostra un criterio di indicizzazione con indicizzazione spaziale abilitata per il tipo di dati **geography** . È valido per i dati spaziali con il tipo di dati geography e indicizza qualsiasi punto GeoJSON, poligono, multipoligono o LineString trovato all'interno di documenti per le query spaziali. Se si modificano i criteri di indicizzazione usando il portale di Azure, è possibile specificare il JSON seguente per i criteri di indicizzazione per abilitare l'indicizzazione spaziale nel contenitore:

**JSON criteri di indicizzazione del contenitore con indicizzazione spaziale geography**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Se il valore GeoJSON della posizione all'interno del documento è non corretto o non valido, non verrà indicizzato per le query spaziali. È possibile convalidare valori della posizione utilizzando ST_ISVALID e ST_ISVALIDDETAILED.
>
>
>

È anche possibile [modificare i criteri di indicizzazione](how-to-manage-indexing-policy.md) usando l'interfaccia della riga di comando di Azure, PowerShell o qualsiasi SDK.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come iniziare a usare il supporto geospaziale in Azure Cosmos DB, ecco le attività successive possibili:

* Altre informazioni sulle [query di Azure Cosmos DB](sql-query-getting-started.md)
* Altre informazioni sull' [esecuzione di query sui dati spaziali con Azure Cosmos DB](sql-query-geospatial-query.md)
* Scopri di più sui [dati della località geospaziale e GeoJSON in Azure Cosmos DB](sql-query-geospatial-intro.md)