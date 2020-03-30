---
title: Dati geospaziali dell'indice con Azure Cosmos DBIndex geospatial data with Azure Cosmos DB
description: Indicizzare i dati spaziali con Azure Cosmos DBIndex spatial data with Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137904"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Dati geospaziali dell'indice con Azure Cosmos DBIndex geospatial data with Azure Cosmos DB

Abbiamo progettato il motore di database di Azure Cosmos DB per essere veramente indipendente da schema e fornire supporto di prima classe per JSON. Il motore di database ottimizzato per la scrittura di Azure Cosmos DB riconosce in modo nativo i dati spaziali rappresentati nello standard GeoJSON.

In breve, la geometria è proiettata dalle coordinate geodetiche su un piano 2D, quindi suddivisa progressivamente in celle utilizzando un **quadtree**. Queste celle vengono mappate in 1D in base alla posizione della cella all'interno di una **curva di riempimento dello spazio di Hilbert**, che consente di mantenere la posizione dei punti. Quando i dati di località vengono indicizzati, passano attraverso un processo noto come **mosaico**: tutte le celle che intersecano una posizione vengono identificate e archiviate come chiavi nell'indice di Azure Cosmos DB. In fase di query, anche argomenti come punti e poligoni sono tassellati per estrarre gli intervalli degli ID delle celle pertinenti e quindi usati per recuperare dati dall'indice.

Se si specifica un criterio di indicizzazione che include l'indice spaziale per l'opzione /o (tutti i percorsi), tutti i dati trovati all'interno del contenitore vengono indicizzati per query spaziali efficienti.

> [!NOTE]
> Azure Cosmos DB supports indexing of Points, LineStrings, Polygons, and MultiPolygons
>
>

## <a name="modifying-geospatial-data-type"></a>Modifica del tipo di dati geospaziali

Nel contenitore, `geospatialConfig` l'oggetto specifica come verranno indicizzati i dati geospaziali. È necessario `geospatialConfig` specificarne uno per contenitore: geografia o geometria. Se non specificato, per impostazione predefinita verrà impostato `geospatialConfig` il tipo di dati geography. Quando si `geospatialConfig`modifica l'oggetto , tutti i dati geospaziali esistenti nel contenitore verranno reindicizzati.

> [!NOTE]
> Azure Cosmos DB supporta attualmente modifiche a geospatialConfig in .NET SDK solo nelle versioni 3.6 e successive.
>

Di seguito è riportato un esempio `geometry` per `geospatialConfig` modificare il tipo di dati geospaziali impostando la proprietà e aggiungendo un **oggetto boundingBox**:

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

## <a name="geography-data-indexing-examples"></a>Esempi di indicizzazione dei dati geograficiGeography data indexing examples

Il frammento JSON seguente mostra un criterio di indicizzazione con l'indicizzazione spaziale abilitata per il tipo di dati **geography.** È valido per i dati spaziali con il tipo di dati geography e indicizzerà qualsiasi punto GeoJSON, poligono, multipoligono o lineString trovato all'interno dei documenti per l'esecuzione di query spaziali. Se si modificano i criteri di indicizzazione usando il portale di Azure, è possibile specificare il codice JSON seguente per i criteri di indicizzazione per abilitare l'indicizzazione spaziale nel contenitore:If you are modifyinging the indexing policy using the Azure portal, you can specify the following JSON for indexing policy to enable spatial indexing on your container:

**Criteri di indicizzazione contenitore JSON con indicizzazione spaziale geograficaContainer indexing policy JSON with geography spatial indexing**

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

È anche possibile modificare i [criteri di indicizzazione](how-to-manage-indexing-policy.md) usando l'interfaccia della riga di comando di Azure, PowerShell o qualsiasi SDK.

## <a name="geometry-data-indexing-examples"></a>Esempi di indicizzazione dei dati geometriciGeometry data indexing examples

Con il tipo di dati **geometry,** simile al tipo di dati geography, è necessario specificare percorsi e tipi rilevanti da indicizzare. Inoltre, è necessario specificare anche un `boundingBox` all'interno dei criteri di indicizzazione per indicare l'area desiderata da indicizzare per il percorso specifico. Ogni percorso geospaziale`boundingBox`richiede il proprio .

Il riquadro di delimitazione è costituito dalle seguenti proprietà:

- **xmin**: coordinata x indicizzata minima
- **ymin**: la coordinata y indicizzata minima
- **xmax**: la coordinata x indicizzata massima
- **ymax**: la coordinata y massima indicizzata

Un riquadro di delimitazione è necessario perché i dati geometrici occupano un piano che può essere infinito. Gli indici spaziali, tuttavia, richiedono uno spazio finito. Per il tipo di dati **geografici,** la Terra è il limite e non è necessario impostare un riquadro di delimitazione.

È necessario creare un riquadro di delimitazione contenente tutti (o la maggior parte) dei dati. Solo le operazioni calcolate sugli oggetti che si trovano interamente all'interno del riquadro di delimitazione saranno in grado di utilizzare l'indice spaziale. Non è consigliabile ingrandire notevolmente il riquadro di delimitazione del necessario perché ciò influirà negativamente sulle prestazioni delle query.

Di seguito è riportato un criterio di indicizzazione di esempio `geometry`che indicizza i dati della geometria con geospatialConfig impostato su:Here is an example indexing policy that indexes **geometry** data with **geospatialConfig** set to:

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

I criteri di indicizzazione precedenti hanno un **boundingBox** di (-10, 10) per le coordinate x e (-20, 20) per le coordinate y. Il contenitore con i criteri di indicizzazione precedenti indicherà tutti i punti, poligoni, MultiPolygons e LineStrings che si trovano interamente all'interno di questa area.

> [!NOTE]
> Se si tenta di aggiungere un criterio di indicizzazione `geography` con un **boundingBox** a un contenitore con tipo di dati, avrà esito negativo. È necessario modificare **geospatialConfig** del `geometry` contenitore in modo che sia prima di aggiungere un **oggetto boundingBox**. È possibile aggiungere dati e modificare il resto dei criteri di indicizzazione, ad esempio percorsi e tipi, prima o dopo aver selezionato il tipo di dati geospaziali per il contenitore.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come iniziare a usare il supporto geospaziale in Azure Cosmos DB, ecco le attività successive possibili:

* Altre informazioni sulle [query di Azure Cosmos DB](sql-query-getting-started.md)
* Altre informazioni [sull'esecuzione di query sui dati spaziali con Azure Cosmos DBLearn](sql-query-geospatial-query.md) more about Querying spatial data with Azure Cosmos DB
* Altre informazioni sui dati di [posizione geospaziale e GeoJSON in Azure Cosmos DBLearn more about Geospatial and GeoJSON location data in Azure Cosmos DB](sql-query-geospatial-intro.md)