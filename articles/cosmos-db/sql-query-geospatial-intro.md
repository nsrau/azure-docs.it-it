---
title: Dati della località geospaziale e GeoJSON in Azure Cosmos DB
description: Informazioni su come creare oggetti spaziali con Azure Cosmos DB e l'API SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367585"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Dati della località geospaziale e GeoJSON in Azure Cosmos DB

Questo articolo offre un'introduzione alla funzionalità geospaziale in Azure Cosmos DB. L'archiviazione e l'accesso ai dati geospaziali attualmente sono supportati solo da account Azure Cosmos DB API SQL. Dopo aver letto la documentazione sull'indicizzazione geospaziale, sarà possibile rispondere alle domande seguenti:

* Come si archiviano i dati spaziali in Azure Cosmos DB?
* Come si eseguono query su dati geospaziali in Azure Cosmos DB in SQL e LINQ?
* Come si abilita o disabilita l'indicizzazione spaziale in Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Introduzione ai dati spaziali

I dati spaziali descrivono la posizione e la forma degli oggetti nello spazio. Nella maggior parte delle applicazioni, questi corrispondono a oggetti sulla terra e a dati geospaziali. I dati spaziali possono essere utilizzati per rappresentare la posizione di una persona, un luogo di interesse o i confini di una città o di un lago. Casi d'uso comuni includono spesso query di prossimità, ad esempio, "trova tutti i negozi vicini alla mia posizione attuale".

L'API SQL di Azure Cosmos DB supporta due tipi di dati spaziali: il tipo di dati **Geometry** e il tipo di dati **geography** .

- Il tipo **Geometry** rappresenta i dati in un sistema di coordinate euclideo (piano)
- Il tipo **geography** rappresenta i dati in un sistema di coordinate terrestri.

## <a name="supported-data-types"></a>Tipi di dati supportati

Azure Cosmos DB supporta l'indicizzazione e le query dei dati di punti geospaziali rappresentati tramite la [specifica GeoJSON](https://tools.ietf.org/html/rfc7946). Le strutture di dati GeoJSON sono sempre oggetti JSON validi, quindi possono essere archiviate e sottoposte a query usando Azure Cosmos DB senza librerie o strumenti specializzati.

Azure Cosmos DB supporta i tipi di dati spaziali seguenti:

- Point
- LineString
- Polygon
- MultiPolygon

### <a name="points"></a>punti

Un **punto** indica una posizione singola nello spazio. Nei dati geospaziali, un punto rappresenta la posizione esatta, che può essere l'indirizzo di un negozio, un chiosco, un'automobile o una città.  Un punto viene rappresentato in GeoJSON (e Azure Cosmos DB) tramite la sua coppia di coordinate o longitudine e latitudine.

Ecco un esempio di JSON per un punto:

**Punti in Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

I tipi di dati spaziali possono essere incorporati in un documento di Azure Cosmos DB, come illustrato in questo esempio di un profilo utente contenente i dati relativi alla posizione:

**Usare un profilo con località archiviata in Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Punti in un sistema di coordinate geometrico

Per il tipo di dati **Geometry** , la specifica GeoJSON specifica prima l'asse orizzontale e il secondo asse verticale.

### <a name="points-in-a-geography-coordinate-system"></a>Punti in un sistema di coordinate geography

Per il tipo di dati **geography** , la specifica GeoJSON specifica la longitudine First e la latitudine Second. Come in altre applicazioni di mapping, longitudine e latitudine sono angoli e sono espresse in gradi. I valori della longitudine vengono misurati dal meridiano principale e sono compresi tra -180 e 180,0 gradi, mentre i valori della latitudine sono misurati dall'equatore e sono compresi tra -90,0 e 90,0 gradi.

Azure Cosmos DB interpreta le coordinate come rappresentate secondo il sistema di riferimento WGS-84. Per altre informazioni sui sistemi di coordinate di riferimento, vedere di seguito.

### <a name="linestrings"></a>LineStrings

**LineStrings** rappresentano una serie di due o più punti nello spazio e i segmenti lineari che li connettono. Nei dati geospaziali, gli oggetti linestring di solito vengono usati per rappresentare autostrade o fiumi.

**Oggetti LineString in GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Poligoni

Un oggetto **poligono** è un perimetro costituito da punti connessi che forma un elemento LineString chiuso. I poligoni vengono comunemente utilizzati per rappresentare formazioni naturali come laghi o giurisdizioni politiche come città e stati. Di seguito è riportato un esempio di poligono in Azure Cosmos DB:

**Poligoni in GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> La specifica GeoJSON richiede che per i poligoni validi l'ultima coppia di coordinate indicata corrisponda alla prima, in modo da creare una forma chiusa.
>
> I punti all'interno di un poligono devono essere specificati in senso antiorario. Un poligono specificato in senso orario rappresenta l'inverso dell'area al suo interno.
>
>

### <a name="multipolygons"></a>Multipoligoni

Un **multipoligono** è una matrice di zero o più poligoni. I **multipoligoni** non possono sovrapporsi o avere un'area comune. Possono toccare uno o più punti.

**Multipoligoni in GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Sistemi di riferimento delle coordinate

Poiché la forma della terra è irregolare, le coordinate dei dati geospaziali geography sono rappresentate in molti sistemi di riferimento a coordinate (CRS), ognuno con i propri frame di riferimento e unità di misura. Ad esempio, il "National Grid of Britain" è un sistema di riferimento accurato per il Regno Unito, ma non al di fuori di questo.

Il più diffuso CRS attualmente in uso è il World Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Dispositivi GPS e molti servizi di mapping, tra cui Google Maps e le API di Bing Maps, utilizzano WGS-84. Azure Cosmos DB supporta l'indicizzazione e l'esecuzione di query di dati geospaziali geography usando solo il CRS WGS-84.

## <a name="creating-documents-with-spatial-data"></a>Creazione di documenti con dati spaziali
Quando si creano documenti che contengono valori GeoJSON, essi vengono indicizzati automaticamente con un indice spaziale in base al criterio di indicizzazione del contenitore. Se si usa un SDK di Azure Cosmos DB in un linguaggio tipizzato in modo dinamico come Python o Node.js, è necessario creare un GeoJSON valido.

**Creare documenti con i dati geospaziali in Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Se si lavora con le API SQL, è possibile usare le classi `Point`, `LineString`, `Polygon`e `MultiPolygon` nello spazio dei nomi `Microsoft.Azure.Cosmos.Spatial` per incorporare le informazioni sulla posizione all'interno degli oggetti dell'applicazione. Queste classi consentono di semplificare la serializzazione e la deserializzazione dei dati spaziali in GeoJSON.

**Creare documenti con i dati geospaziali in .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Se non si dispone delle informazioni di latitudine e longitudine, ma si dispone di indirizzi fisici o nome località come città o paese/area geografica, è possibile cercare le coordinate effettive usando un servizio di geocodifica come i servizi REST di Bing Maps. Ulteriori informazioni sulla geocodifica di Bing Maps sono disponibili [qui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come iniziare a usare il supporto geospaziale in Azure Cosmos DB, ecco le attività successive possibili:

* Altre informazioni sulle [query di Azure Cosmos DB](sql-query-getting-started.md)
* Altre informazioni sull' [esecuzione di query sui dati spaziali con Azure Cosmos DB](sql-query-geospatial-query.md)
* Altre informazioni sui [dati spaziali dell'indice con Azure Cosmos DB](sql-query-geospatial-index.md)