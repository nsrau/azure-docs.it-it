---
title: Geometrie GeoJSON estese Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come Microsoft Azure Maps estende la specifica GeoJSON per rappresentare determinate geometrie.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276400"
---
# <a name="extended-geojson-geometries"></a>Geometrie GeoJSON estese

Mappe di Azure offre un elenco di API potenti in cui eseguire ricerche all'interno e lungo le funzionalità geografiche. Queste API aderiscono alla [specifica GeoJSON][1] standard che rappresenta le caratteristiche geografiche.  

La [specifica GeoJSON][1] supporta solo le seguenti geometrie:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Alcune API di Mappe di Azure accettano geometrie che non fanno parte della [specifica GeoJSON][1]. Ad esempio, l'API [Cerca all'interno della geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) accetta Circle e Polygons.For instance, the Search Inside Geometry API accepts Circle and Polygons.

Questo articolo illustra in dettaglio come Mappe di Azure estende la [specifica GeoJSON][1] per rappresentare determinate geometrie.

## <a name="circle"></a>Circle

La `Circle` geometria non è supportata dalla [specifica GeoJSON][1]. Usiamo un `GeoJSON Point Feature` oggetto per rappresentare un cerchio.

Una `Circle` geometria rappresentata utilizzando l'oggetto `GeoJSON Feature` __deve__ contenere le seguenti coordinate e proprietà:

- Center

    Il centro del cerchio viene `GeoJSON Point` rappresentato utilizzando un oggetto.

- Radius

    L'elemento `radius` del cerchio viene rappresentato usando le proprietà di `GeoJSON Feature`. Il valore del raggio è espresso in _metri_ e deve essere di tipo `double`.

- Sottotipo

    La geometria circle deve contenere anche la proprietà `subType`. Questa proprietà deve essere `GeoJSON Feature`una parte delle proprietà e il relativo valore deve essere _Circle_

#### <a name="example"></a>Esempio

Ecco come rappresenterai un cerchio `GeoJSON Feature` usando un oggetto. Aliamo il cerchio alla latitudine: 47.639754 e longitudine: -122.126986, e assegniamogli un raggio pari a 100 metri:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Rectangle

La `Rectangle` geometria non è supportata dalla [specifica GeoJSON][1]. Usiamo un `GeoJSON Polygon Feature` oggetto per rappresentare un rettangolo. L'estensione del rettangolo viene utilizzata principalmente dal modulo degli strumenti di disegno di Web SDK.

Una `Rectangle` geometria rappresentata utilizzando l'oggetto `GeoJSON Polygon Feature` __deve__ contenere le seguenti coordinate e proprietà:

- Angoli

    Gli angoli del rettangolo vengono rappresentati utilizzando le coordinate di un `GeoJSON Polygon` oggetto. Dovrebbero esserci cinque coordinate, una per ogni angolo. E, una quinta coordinata che è uguale alla prima coordinata, per chiudere l'anello del poligono. Si presuppone che queste coordinate si allineino e che lo sviluppatore possa ruotarle come desiderato.

- Sottotipo

    La geometria del rettangolo deve contenere anche la `subType` proprietà. Questa proprietà deve essere `GeoJSON Feature`una parte delle proprietà di 's e il _relativo_ valore deve essere Rectangle

### <a name="example"></a>Esempio

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui dati GeoJSON in Mappe di Azure:Learn more about GeoJSON data in Azure Maps:

> [!div class="nextstepaction"]
> [Geofence Formato GeoJSON](geofence-geojson.md)

Esaminare il glossario dei termini tecnici comuni associati ad Azure Maps e alle applicazioni di business intelligence sulla posizione:Review the glossary of common technical terms associated with Azure Maps and location intelligence applications:

> [!div class="nextstepaction"]
> [Glossario di Mappe di AzureAzure Maps glossary](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
