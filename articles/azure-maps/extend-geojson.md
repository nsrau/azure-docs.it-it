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
ms.openlocfilehash: 2cc0e29615ad4fc19040055d847435a9dffa9c95
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659334"
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

Alcune API di Mappe di Azure, ad esempio [Search Inside Geometry](https://docs.microsoft.com/en-us/rest/api/maps/search/postsearchinsidegeometry) accettano geometrie come "Circle", che non fanno parte della [specifica GeoJSON][1].

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

[1]: https://tools.ietf.org/html/rfc7946
