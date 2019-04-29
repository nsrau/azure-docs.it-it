---
title: Estensione delle geometrie GeoJSON in Mappe di Azure | Microsoft Docs
description: Informazioni su come estendere le geometrie GeoJSON in Mappe di Azure
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: be3c31951c4721a861f9239c5220419dec11b6bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799143"
---
# <a name="extending-geojson-geometries"></a>Estensione delle geometrie GeoJSON

Mappe di Azure offre una serie di API avanzate per la ricerca all'interno delle funzionalità geografiche o lungo di esse.
Queste API usano come standard la [specifica GeoJSON][1] per rappresentare le funzionalità geografiche (ad esempio, confini di stato, strade).  

La [specifica GeoJSON][1] supporta solo le geometrie seguenti:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Alcune API di mappe di Azure (ad esempio: [Ricerca all'interno della geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) accettare le geometrie, ad esempio "Cerchio", che non sono in parte il [GeoJSON specifica][1].

Questo articolo illustra in dettaglio come Mappe di Azure estende la [specifica GeoJSON][1] per rappresentare determinate geometrie.

### <a name="circle"></a>Circle

La geometria `Circle` non è supportata dalla [specifica GeoJSON][1]. Per rappresentare un cerchio, viene usato l'oggetto `GeoJSON Feature`.

Una geometria `Circle` rappresentata usando l'oggetto `GeoJSON Feature` __deve__ contenere quanto segue:

1. Center
   >Il centro del cerchio viene rappresentato usando un tipo `GeoJSON Point`.

2. Radius
   >L'elemento `radius` del cerchio viene rappresentato usando le proprietà di `GeoJSON Feature`. Il valore del raggio è espresso in _metri_ e deve essere di tipo `double`.

3. SubType
   >La geometria circle deve contenere anche la proprietà `subType`. Questa proprietà deve fare parte delle proprietà di `GeoJSON Feature` e il valore deve essere _Circle_


#### <a name="example"></a>Esempio

Ecco come si sarà rappresentano un cerchio centrato nella (latitudine: 47.639754, longitudine:-122.126986) con un raggio pari a 100 metri, usando un `GeoJSON Feature` oggetto:

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

[1]: https://tools.ietf.org/html/rfc7946
