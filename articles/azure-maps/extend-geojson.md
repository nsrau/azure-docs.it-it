---
title: Geometrie estese GeoJSON | Mappe Microsoft Azure
description: Informazioni su come Azure Maps estende le specifiche GeoJSON per includere altre forme geometriche. Visualizzare esempi che configurano i cerchi e i rettangoli da usare nelle mappe.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e6cfbef3751a7b4256f689af0e5b3524ae6fa878
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88037457"
---
# <a name="extended-geojson-geometries"></a>Geometrie estese GeoJSON

Azure Maps fornisce un elenco di potenti API per la ricerca all'interno e nelle funzionalità geografiche. Queste API rispettano la [specifica GeoJSON][1] standard di che rappresenta le funzionalità geografiche.  

La [specifica GeoJSON][1] supporta solo le geometrie seguenti:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Alcune API di Azure Maps accettano geometrie che non fanno parte della [specifica GeoJSON][1]. Ad esempio, la [ricerca all'interno](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) dell'API Geometry accetta il cerchio e i poligoni.

Questo articolo illustra in dettaglio come Mappe di Azure estende la [specifica GeoJSON][1] per rappresentare determinate geometrie.

## <a name="circle"></a>Circle

La `Circle` geometria non è supportata dalla [specifica GeoJSON][1]. Viene usato un `GeoJSON Point Feature` oggetto per rappresentare un cerchio.

Una `Circle` geometria rappresentata utilizzando l' `GeoJSON Feature` oggetto __deve__ contenere le coordinate e le proprietà seguenti:

- Center

    Il centro del cerchio viene rappresentato utilizzando un `GeoJSON Point` oggetto.

- Radius

    L'elemento `radius` del cerchio viene rappresentato usando le proprietà di `GeoJSON Feature`. Il valore del raggio è espresso in _metri_ e deve essere di tipo `double`.

- Sottotipo

    La geometria circle deve contenere anche la proprietà `subType`. Questa proprietà deve essere una parte delle `GeoJSON Feature` proprietà di e il relativo valore deve essere _Circle_

#### <a name="example"></a>Esempio

Ecco come si rappresenterà un cerchio usando un `GeoJSON Feature` oggetto. Si concentrerà il cerchio in Latitudine: 47,639754 e Longitudine:-122,126986 e si assegnerà un raggio uguale a 100 metri:

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

## <a name="rectangle"></a>Rettangolo

La `Rectangle` geometria non è supportata dalla [specifica GeoJSON][1]. Viene usato un `GeoJSON Polygon Feature` oggetto per rappresentare un rettangolo. L'estensione Rectangle viene utilizzata principalmente dal modulo degli strumenti di disegno di Web SDK.

Una `Rectangle` geometria rappresentata utilizzando l' `GeoJSON Polygon Feature` oggetto __deve__ contenere le coordinate e le proprietà seguenti:

- Angoli

    Gli angoli del rettangolo sono rappresentati usando le coordinate di un `GeoJSON Polygon` oggetto. Devono essere presenti cinque coordinate, una per ogni angolo. E, una quinta coordinata uguale alla prima coordinata, per chiudere l'anello del poligono. Si presuppone che queste coordinate siano allineate e che lo sviluppatore possa ruotarle come desiderato.

- Sottotipo

    La geometria del rettangolo deve contenere anche la `subType` Proprietà. Questa proprietà deve essere una parte delle `GeoJSON Feature` proprietà di e il relativo valore deve essere _Rectangle_

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

Altre informazioni sui dati GeoJSON in mappe di Azure:

> [!div class="nextstepaction"]
> [Formato GeoJSON Geofence](geofence-geojson.md)

Esaminare il Glossario dei termini tecnici comuni associati a mappe di Azure e alle applicazioni di Business Intelligence per la posizione:

> [!div class="nextstepaction"]
> [Glossario di Mappe di Azure](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
