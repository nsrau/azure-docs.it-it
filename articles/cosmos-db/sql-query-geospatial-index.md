---
title: Indicizzare i dati geospaziali con Azure Cosmos DB
description: Indicizzare i dati spaziali con Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/03/2020
ms.author: tisande
ms.openlocfilehash: 546b664c74980b3522fefed82c00eec414641eaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326627"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indicizzare i dati geospaziali con Azure Cosmos DB

Il motore di database di Azure Cosmos DB è stato progettato in modo da essere effettivamente indipendente dallo schema e fornire supporto di prima classe per JSON. Il motore di database ottimizzato per la scrittura di Azure Cosmos DB riconosce in modo nativo i dati spaziali rappresentati nello standard GeoJSON.

In breve, la geometria è proiettata dalle coordinate geodetiche su un piano 2D, quindi suddivisa progressivamente in celle utilizzando un **quadtree**. Queste celle vengono mappate in 1D in base alla posizione della cella all'interno di una **curva di riempimento dello spazio di Hilbert**, che consente di mantenere la posizione dei punti. Quando i dati di località vengono indicizzati, passano attraverso un processo noto come **mosaico**: tutte le celle che intersecano una posizione vengono identificate e archiviate come chiavi nell'indice di Azure Cosmos DB. In fase di query, anche argomenti come punti e poligoni sono tassellati per estrarre gli intervalli degli ID delle celle pertinenti e quindi usati per recuperare dati dall'indice.

Se si specifica un criterio di indicizzazione che include l'indice spaziale per/* (tutti i percorsi), tutti i dati trovati all'interno del contenitore vengono indicizzati per query spaziali efficienti.

> [!NOTE]
> Azure Cosmos DB supporta l'indicizzazione di punti, LineString, poligoni e multipoligoni
>
>

## <a name="modifying-geospatial-data-type"></a>Modifica del tipo di dati geospaziali

Nel contenitore la **configurazione geospaziale** specifica come verranno indicizzati i dati spaziali. Specificare una **configurazione geospaziale** per ogni contenitore, ovvero Geography o Geometry.

È possibile alternare il tipo spaziale **geography** e **Geometry** nel portale di Azure. È importante creare un [criterio di indicizzazione di geometria spaziale valido con un](#geometry-data-indexing-examples) rettangolo di selezione prima di passare al tipo spaziale geometry.

Di seguito viene illustrato come impostare la **configurazione geospaziale** in **Esplora dati** all'interno del portale di Azure:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Impostazione della configurazione geospaziale":::

È anche possibile modificare `geospatialConfig` in .NET SDK per modificare la **configurazione geospaziale**:

Se non specificato, `geospatialConfig` per impostazione predefinita viene impostato il tipo di dati geography. Quando si modifica la `geospatialConfig` , tutti i dati geospaziali esistenti nel contenitore verranno reindicizzati.

Di seguito è riportato un esempio per modificare il tipo di dati geospaziale in impostando `geometry` la `geospatialConfig` proprietà e aggiungendo un **BoundingBox**:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Esempi di indicizzazione dei dati geography

Il frammento di codice JSON seguente mostra un criterio di indicizzazione con indicizzazione spaziale abilitata per il tipo di dati **geography** . È valido per i dati spaziali con il tipo di dati geography e indicizza qualsiasi punto GeoJSON, poligono, multipoligono o LineString trovato all'interno di documenti per le query spaziali. Se si modificano i criteri di indicizzazione usando il portale di Azure, è possibile specificare il JSON seguente per i criteri di indicizzazione per abilitare l'indicizzazione spaziale nel contenitore:

**JSON criteri di indicizzazione del contenitore con indicizzazione spaziale geography**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
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
    "excludedPaths": []
}
```

> [!NOTE]
> Se il valore GeoJSON della posizione all'interno del documento è non corretto o non valido, non verrà indicizzato per le query spaziali. È possibile convalidare valori della posizione utilizzando ST_ISVALID e ST_ISVALIDDETAILED.

È anche possibile [modificare i criteri di indicizzazione](how-to-manage-indexing-policy.md) usando l'interfaccia della riga di comando di Azure, PowerShell o qualsiasi SDK.

## <a name="geometry-data-indexing-examples"></a>Esempi di indicizzazione di dati geometry

Con il tipo di dati **Geometry** , simile al tipo di dati geography, è necessario specificare i percorsi e i tipi rilevanti da indicizzare. Inoltre, è necessario specificare un valore `boundingBox` all'interno dei criteri di indicizzazione per indicare l'area desiderata da indicizzare per il percorso specifico. Ogni percorso geospaziale richiede un proprio `boundingBox` .

Il rettangolo di delimitazione è costituito dalle proprietà seguenti:

- **xmin**: coordinata x minima indicizzata
- **yMin**: coordinata y minima indicizzata
- **Xmax**: coordinata x massima indicizzata
- **yMax**: coordinata y massima indicizzata

È necessario un rettangolo di delimitazione perché i dati geometrici occupano un piano che può essere infinito. Gli indici spaziali, tuttavia, richiedono uno spazio finito. Per il tipo di dati **geography** , la terra è il limite e non è necessario impostare un rettangolo di delimitazione.

Creare un rettangolo di delimitazione che contenga tutti (o più) i dati. Solo le operazioni calcolate sugli oggetti interamente all'interno del rettangolo di delimitazione saranno in grado di utilizzare l'indice spaziale. Se il riquadro delimitatore è più grande del necessario, avrà un impatto negativo sulle prestazioni delle query.

Ecco un esempio di criterio di indicizzazione che indicizza i dati **Geometry** con **geospatialConfig** impostato su `geometry` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Il criterio di indicizzazione precedente presenta un **BoundingBox** di (-10, 10) per le coordinate x e (-20, 20) per le coordinate y. Il contenitore con i criteri di indicizzazione elencati sopra indicizza tutti i punti, i poligoni, i multipoligoni e gli oggetti LineString che sono interamente all'interno di questa area.

> [!NOTE]
> Se si tenta di aggiungere un criterio di indicizzazione con un **BoundingBox** a un contenitore con `geography` tipo di dati, l'operazione avrà esito negativo. È necessario modificare il **geospatialConfig** del contenitore prima di `geometry` aggiungere un **BoundingBox**. È possibile aggiungere dati e modificare il resto del criterio di indicizzazione, ad esempio i percorsi e i tipi, prima o dopo aver selezionato il tipo di dati geospaziale per il contenitore.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come iniziare a usare il supporto geospaziale in Azure Cosmos DB, ecco le attività successive possibili:

* Altre informazioni sulle [query di Azure Cosmos DB](sql-query-getting-started.md)
* Altre informazioni sull' [esecuzione di query sui dati spaziali con Azure Cosmos DB](sql-query-geospatial-query.md)
* Scopri di più sui [dati della località geospaziale e GeoJSON in Azure Cosmos DB](sql-query-geospatial-intro.md)
