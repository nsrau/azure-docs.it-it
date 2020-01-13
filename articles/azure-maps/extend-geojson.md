---
title: Geometrie estese GeoJSON | Mappe Microsoft Azure
description: In questo articolo si apprenderà come Microsoft Azure Maps estende le specifiche GeoJSON per rappresentare determinate geometrie.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 75ad83411edfdfe7545e8f80df17fea56e317ee0
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911644"
---
# <a name="extended-geojson-geometries"></a>Geometrie estese GeoJSON

Mappe di Azure offre una serie di API avanzate per la ricerca all'interno delle funzionalità geografiche o lungo di esse.
Queste API standardizzano le [specifiche GeoJSON][1] per rappresentare le funzionalità geografiche (ad esempio, i limiti di stato e le route).  

La [specifica GeoJSON][1] supporta solo le geometrie seguenti:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Alcune API di Azure Maps (ad esempio, la [ricerca all'interno di Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) accettano geometrie come "Circle", che non fanno parte della [specifica GeoJSON][1].

Questo articolo fornisce una spiegazione dettagliata su come Azure Maps estende le [specifiche GeoJSON][1] per rappresentare determinate geometrie.

## <a name="circle"></a>Circle

La geometria `Circle` non è supportata dalla [specifica GeoJSON][1]. Viene usato un oggetto `GeoJSON Point Feature` per rappresentare un cerchio.

Una geometria `Circle` rappresentata usando l'oggetto `GeoJSON Feature`__deve__ contenere quanto segue:

- Center

    Il centro del cerchio viene rappresentato usando un oggetto `GeoJSON Point`.

- Radius

    L'elemento `radius` del cerchio viene rappresentato usando le proprietà di `GeoJSON Feature`. Il valore del raggio è espresso in _metri_ e deve essere di tipo `double`.

- Sottotipo

    La geometria circle deve contenere anche la proprietà `subType`. Questa proprietà deve essere una parte delle proprietà del `GeoJSON Feature`e il relativo valore deve essere _Circle_

#### <a name="example"></a>Esempio

Ecco come si rappresenterà un cerchio con il centro in corrispondenza di (latitudine: 47,639754, longitudine: -122,126986) con raggio di 100 metri, usando un oggetto `GeoJSON Feature`:

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

La geometria `Rectangle` non è supportata dalla [specifica GeoJSON][1]. Viene usato un oggetto `GeoJSON Polygon Feature` per rappresentare un rettangolo. L'estensione Rectangle viene utilizzata principalmente dal modulo degli strumenti di disegno di Web SDK.

Una geometria `Rectangle` rappresentata usando l'oggetto `GeoJSON Polygon Feature`__deve__ contenere quanto segue:

- Angoli

    Gli angoli del rettangolo sono rappresentati usando le coordinate di un oggetto `GeoJSON Polygon`. Devono essere presenti cinque coordinate, una per ogni angolo e una quinta coordinata uguale alla prima per chiudere l'anello del poligono. Si presuppone che queste coordinate siano allineate e ruotate in base alle esigenze dello sviluppatore.

- Sottotipo

    La geometria del rettangolo deve contenere anche la proprietà `subType`. Questa proprietà deve essere una parte delle proprietà del `GeoJSON Feature`e il relativo valore deve essere _Rectangle_

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
> [Glossario mappe di Azure](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
