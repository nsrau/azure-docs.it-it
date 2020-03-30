---
title: Dettagli sul formato dei dati supportati Mappe di Microsoft Azure
description: Informazioni su come i dati spaziali delimitati vengono analizzati nel modulo I/O spaziale.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334090"
---
# <a name="supported-data-format-details"></a>Dettagli sul formato dati supportati

In questo articolo vengono fornite specifiche sul supporto in lettura e scrittura per tutti i tag XML e i tipi di geometria di testo noto. Descrive inoltre come i dati spaziali delimitati vengono analizzati nel modulo I/O spaziale.

## <a name="supported-xml-namespaces"></a>Spazi dei nomi XML supportatiSupported XML namespaces

Il modulo I/O spaziale supporta i tag XML dagli spazi dei nomi seguenti.

| Prefisso spazio dei nomiNamespace Prefix | URI dello spazio dei nomi   | Note                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Supporto di sola lettura nei file GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Supporto di sola lettura nei file GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Supporto di sola lettura nei file GPX. Analizza e utilizza DisplayColor. Tutte le altre proprietà aggiunte ai metadati della forma. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Supportato nei file GPX. Utilizza il colore della linea. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Sola lettura. GeoRSS scrive utilizzando il formato Atom.              |

## <a name="supported-xml-elements"></a>Elementi XML supportati

Il modulo I/O spaziale supporta i seguenti elementi XML. Tutti i tag XML non supportati verranno convertiti in un oggetto JSON. Quindi, ogni tag verrà aggiunto come `properties` proprietà nel campo della forma o del livello padre.

### <a name="kml-elements"></a>Elementi KML

Il modulo IO spaziale supporta i seguenti elementi KML.

| Nome dell'elemento         | Lettura    | Scrittura   | Note                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | parziale | sì     | L'oggetto viene analizzato ma non viene utilizzato per il posizionamento della forma.                                                                    |
| `AddressDetails`     | parziale | no      | L'oggetto viene analizzato ma non viene utilizzato per il posizionamento della forma.                                                                    |
| `atom:author`        | sì     | sì     |                                                                                                                            |
| `atom:link`          | sì     | sì     |                                                                                                                            |
| `atom:name`          | sì     | sì     |                                                                                                                            |
| `BalloonStyle`       | parziale | parziale | `displayMode` non è supportata. Convertito in `PopupTemplate`un oggetto . Per scrivere, `popupTemplate` aggiungere una proprietà come proprietà della funzionalità per la quale si desidera scriverla. |
| `begin`              | sì     | sì     |                                                                                                                            |
| `color`              | sì     | sì     | Include `#AABBGGRR` `#BBGGRR`e . Analizzato in una stringa di colore CSS                                                           |
| `colorMode`          | sì     | no      |                                                                                                                            |
| `coordinates`        | sì     | sì     |                                                                                                                            |
| `Data`               | sì     | sì     |                                                                                                                            |
| `description`        | sì     | sì     |                                                                                                                            |
| `displayName`        | sì     | sì     |                                                                                                                            |
| `Document`           | sì     | sì     |                                                                                                                            |
| `drawOrder`          | parziale | no      | Leggere per le sovrapposizioni di terra e utilizzati per ordinarli. 
| `east`               | sì     | sì     |                                                                                                                            |
| `end`                | sì     | sì     |                                                                                                                            |
| `ExtendedData`       | sì     | sì     | Supporta le sostituzioni `SimpleData` `Schema`di entità non tipizzate `Data`o o e le entità del modulo `$[dataName]`.                      |
| `extrude`            | parziale | parziale | Supportato solo per i poligoni. MultiGeometry con poligoni di altezza diverse verrà suddiviso in singole feature. Gli stili di linea non sono supportati. I poligoni con un'altitudine pari a 0 verranno visualizzati come poligono piatto. Durante la lettura, l'altitudine della prima coordinata nell'anello esterno verrà aggiunta come proprietà altezza del poligono. Quindi, l'altitudine della prima coordinata verrà utilizzata per eseguire il rendering del poligono sulla mappa. |
| `fill`               | sì     | sì     |                                                                                                                            |
| `Folder`             | sì     | sì     |                                                                                                                            |
| `GroundOverlay`      | sì     | sì     | `color`non è supportato                                                                                                   |
| `heading`            | parziale | no      | Analizzato ma non `SimpleDataLayer`sottoposto a rendering da . Scrive solo se i dati sono archiviati nella proprietà della forma.                 |
| `hotSpot`            | sì     | parziale | Scrive solo se i dati sono archiviati nella proprietà della forma. Le unità vengono emesse solo come "pixel".                         |
| `href`               | sì     | sì     |                                                                                                                            |
| `Icon`               | parziale | parziale | Analizzato ma non `SimpleDataLayer`sottoposto a rendering da . Scrive la proprietà icon della forma solo se contiene dati URI. È supportato solo `href`. |
| `IconStyle`          | parziale | parziale | `icon`, `heading` `colorMode`, `hotspots` e i valori vengono analizzati, ma non`SimpleDataLayer`         |
| `innerBoundaryIs`    | sì     | sì     |                                                                                                                            |
| `kml`                | sì     | sì     |                                                                                                                            |
| `LabelStyle`         | no      | no      |                                                                                                                            |
| `LatLonBox`          | sì     | sì     |                                                                                                                            |
| `gx:LatLonQuad`      | sì     | sì     |                                                                                                                            |
| `LinearRing`         | sì     | sì     |                                                                                                                            |
| `LineString`         | sì     | sì     |                                                                                                                            |
| `LineStyle`          | sì     | sì     | `colorMode` non è supportata.                                                                                         |
| `Link`               | sì     | no      | Solo `href` la proprietà è supportata per i collegamenti di rete.                                                                   |
| `MultiGeometry`      | parziale | parziale | Può essere suddiviso in singole funzioni durante la lettura.                                                                     |
| `name`               | sì     | sì     |                                                                                                                            |
| `NetworkLink`        | sì     | no      | I collegamenti devono trovarsi nello stesso dominio del documento.                                                                  |
| `NetworkLinkControl` | no      | no      |                                                                                                                            |
| `north`              | sì     | sì     |                                                                                                                            |
| `open`               | sì     | sì     |                                                                                                                            |
| `outerBoundaryIs`    | sì     | sì     |                                                                                                                            |
| `outline`            | sì     | sì     |                                                                                                                            |
| `overlayXY`          | no      | no      |                                                                                                                            |
| `Pair`               | parziale | no      | È `normal` supportato `StyleMap` solo lo stile in a. `highlight` non è supportata.                                   |
| `phoneNumber`        | sì     | sì     |                                                                                                                            |
| `PhotoOverlay`       | no      | no      |                                                                                                                            |
| `Placemark`          | sì     | sì     |                                                                                                                            |
| `Point`              | sì     | sì     |                                                                                                                            |
| `Polygon`            | sì     | sì     |                                                                                                                            |
| `PolyStyle`          | sì     | sì     |                                                                                                                            |
| `Region`             | parziale | parziale | `LatLongBox`è supportato a livello di documento.                                                                      |
| `rotation`           | no      | no      |                                                                                                                            |
| `rotationXY`         | no      | no      |                                                                                                                            |
| `scale`              | no      | no      |                                                                                                                            |
| `Schema`             | sì     | sì     |                                                                                                                            |
| `SchemaData`         | sì     | sì     |                                                                                                                            |
| `schemaUrl`          | parziale | sì     | Non supporta il caricamento di stili da documenti esterni che non sono inclusi in un KM.                             |
| `ScreenOverlay`      | no      | no      |                                                                                                                            |
| `screenXY`           | no      | no      |                                                                                                                            |
| `SimpleData`         | sì     | sì     |                                                                                                                            |
| `SimpleField`        | sì     | sì     |                                                                                                                            |
| `size`               | no      | no      |                                                                                                                            |
| `Snippet`            | parziale | parziale | `maxLines`l'attributo viene ignorato.                                                                                  |
| `south`              | sì     | sì     |                                                                                                                            |
| `Style`              | sì     | sì     |                                                                                                                            |
| `StyleMap`           | parziale | no      | È supportato solo `StyleMap` lo stile normale in a.                                                                        |
| `styleUrl`           | parziale | sì     | Gli URL di stile esterno non sono supportati.                                                                         |
| `text`               | sì     | sì     | La `$[geDirections]` sostituzione di non è supportata                                                                          |
| `textColor`          | sì     | sì     |                                                                                                                            |
| `TimeSpan`           | sì     | sì     |                                                                                                                            |
| `TimeStamp`          | sì     | sì     |                                                                                                                            |
| `value`              | sì     | sì     |                                                                                                                            |
| `viewRefreshMode`    | parziale | no      |  Se si punta a un servizio `onStop` WMS, è supportato solo per le sovrapposizioni a terra. Aggiungerà `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` all'URL e aggiornerà man mano che la mappa si sposta.  |
| `visibility`         | sì     | sì     |                                                                                                                            |
| `west`               | sì     | sì     |                                                                                                                            |
| `when`               | sì     | sì     |                                                                                                                            |
| `width`              | sì     | sì     |                                                                                                                            |

### <a name="georss-elements"></a>Elementi GeoRSS

Il modulo IO spaziale supporta i seguenti elementi GeoRSS.

| Nome dell'elemento             | Lettura    | Scrittura | Note                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | sì     | sì   |                                                                                                |
| `atom:category`          | sì     | sì   |                                                                                                |
| `atom:content`           | sì     | sì   |                                                                                                |
| `atom:contributor`       | sì     | sì   |                                                                                                |
| `atom:email`             | sì     | sì   |                                                                                                |
| `atom:entry`             | sì     | sì   |                                                                                                |
| `atom:feed`              | sì     | sì   |                                                                                                |
| `atom:icon`              | sì     | sì   |                                                                                                |
| `atom:id`                | sì     | sì   |                                                                                                |
| `atom:link`              | sì     | sì   |                                                                                                |
| `atom:logo`              | sì     | sì   |                                                                                                |
| `atom:name`              | sì     | sì   |                                                                                                |
| `atom:published`         | sì     | sì   |                                                                                                |
| `atom:rights`            | sì     | sì   |                                                                                                |
| `atom:source`            | sì     | sì   |                                                                                                |
| `atom:subtitle`          | sì     | sì   |                                                                                                |
| `atom:summary`           | sì     | sì   |                                                                                                |
| `atom:title`             | sì     | sì   |                                                                                                |
| `atom:updated`           | sì     | sì   |                                                                                                |
| `atom:uri`               | sì     | sì   |                                                                                                |
| `geo:lat`                | sì     | no    | Scritto come `georss:point`un file .                                                                   |
| `geo:lon`                | sì     | no    | Scritto come `georss:point`un file .                                                                   |
| `geo:long`               | sì     | no    | Scritto come `georss:point`un file .                                                                   |
| `georss:box`             | sì     | no    | Leggere come un poligono e dato una `subType` proprietà di "Rettangolo"                                |
| `georss:circle`          | sì     | sì   |                                                                                                |
| `georss:elev`            | sì     | sì   |                                                                                                |
| `georss:featurename`     | sì     | sì   |                                                                                                |
| `georss:featuretypetag`  | sì     | sì   |                                                                                                |
| `georss:floor`           | sì     | sì   |                                                                                                |
| `georss:line`            | sì     | sì   |                                                                                                |
| `georss:point`           | sì     | sì   |                                                                                                |
| `georss:polygon`         | sì     | sì   |                                                                                                |
| `georss:radius`          | sì     | sì   |                                                                                                |
| `georss:relationshiptag` | sì     | sì   |                                                                                                |
| `georss:where`           | sì     | sì   |                                                                                                |
| `geourl:latitude`        | sì     | no    | Scritto come `georss:point`un file .                                                                   |
| `geourl:longitude`       | sì     | no    | Scritto come `georss:point`un file .                                                                   |
| `position`               | sì     | no    | Alcuni feed XML avranno a capo GML con `georss:where` un tag di posizione invece di eseguirne il wrapping con un tag. Leggerà questo tag, ma `georss:where` scriverà utilizzando un tag. |
| `rss`                    | sì     | no    | GeoRSS scritto in formato ATOM.                                                                 |
| `rss:author`             | sì     | parziale | Scritto come `atom:author`un file .                                                                 |
| `rss:category`           | sì     | parziale | Scritto come `atom:category`un file .                                                               |
| `rss:channel`            | sì     | no    |                                                                                                |
| `rss:cloud`              | sì     | no    |                                                                                                |
| `rss:comments`           | sì     | no    |                                                                                                |
| `rss:copyright`          | sì     | parziale | Scritto come `atom:rights` una forma if `rights` `properties` non ha già una proprietà.       |
| `rss:description`        | sì     | parziale | Scritto come `atom:content` una forma if `content` `properties` non ha già una proprietà.      |
| `rss:docs`               | sì     | no    |                                                                                                |
| `rss:enclosure`          | sì     | no    |                                                                                                |
| `rss:generator`          | sì     | no    |                                                                                                |
| `rss:guid`               | sì     | parziale | Scritto come `atom:id` una forma if `id` `properties` non ha già una proprietà.         |
| `rss:image`              | sì     | parziale | Scritto come `atom:logo` una forma if `logo` `properties` non ha già una proprietà.      |
| `rss:item`               | sì     | parziale | Scritto come `atom:entry`un file .                                                                  |
| `rss:language`           | sì     | no    |                                                                                                |
| `rss:lastBuildDate`      | sì     | parziale | Scritto come `atom:updated` una forma if `updated` `properties` non ha già una proprietà.     |
| `rss:link`               | sì     | parziale | Scritto come `atom:link`un file .                                                                   |
| `rss:managingEditor`     | sì     | parziale | Scritto come `atom:contributor`un file .                                                            |
| `rss:pubDate`            | sì     | parziale | Scritto come `atom:published` una forma if `published` `properties` non ha già una proprietà.  |
| `rss:rating`             | sì     | no    |                                                                                                |
| `rss:skipDays`           | sì     | no    |                                                                                                |
| `rss:skipHours`          | sì     | no    |                                                                                                |
| `rss:source`             | sì     | parziale | Scritto come `atom:source` un `atom:link`contenente un file .                                       |
| `rss:textInput`          | sì     | no    |                                                                                                |
| `rss:title`              | sì     | parziale | Scritto come `atom:title`un file .                                                                  |
| `rss:ttl`                | sì     | no    |                                                                                                |
| `rss:webMaster`          | sì     | no    |                                                                                                |

### <a name="gml-elements"></a>Elementi GML

Il modulo IO spaziale supporta i seguenti elementi GML. 

| Nome dell'elemento            | Lettura | Scrittura | Note                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | sì  | no    | Scritto `gml:posList`come .                                                              |
| `gml:curveMember`       | sì  | no    |                                                                                        |
| `gml:curveMembers`      | sì  | no    |                                                                                        |
| `gml:Box`               | sì  | no    | Scritto `gml:Envelope`come .                                                             |
| `gml:description`       | sì  | sì   |                                                                                        |
| `gml:Envelope`          | sì  | sì   |                                                                                        |
| `gml:exterior`          | sì  | sì   |                                                                                        |
| `gml:Feature`           | sì  | no    | Scritto come una forma.                                                                    |
| `gml:FeatureCollection` | sì  | no    | Scritto come una raccolta di geometria.                                                      |
| `gml:featureMember`     | sì  | no    | Scritto come una raccolta di geometria.                                                      |
| `gml:geometry`          | sì  | no    | Scritto come una forma.                                                                    |
| `gml:geometryMember`    | sì  | sì   |                                                                                        |
| `gml:geometryMembers`   | sì  | sì   |                                                                                        |
| `gml:identifier`        | sì  | sì   |                                                                                        |
| `gml:innerBoundaryIs`   | sì  | no    | Scritto `gml.interior`con .                                                          |
| `gml:interior`          | sì  | sì   |                                                                                        |
| `gml:LinearRing`        | sì  | sì   |                                                                                        |
| `gml:LineString`        | sì  | sì   |                                                                                        |
| `gml:lineStringMember`  | sì  | sì   |                                                                                        |
| `gml:lineStringMembers` | sì  | no    |                                                                                        |
| `gml:MultiCurve`        | sì  | no    | Legge solo `gml:LineString` i membri. Scritto come`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | parziale  | parziale   | Lettura solo come FeatureCollection.                                              |
| `gml:MultiLineString`   | sì  | sì   |                                                                                        |
| `gml:MultiPoint`        | sì  | sì   |                                                                                        |
| `gml:MultiPolygon`      | sì  | sì   |                                                                                        |
| `gml:MultiSurface`      | sì  | no    | Legge solo `gml:Polygon` i membri. Scritto come`gml.MultiPolygon`                        |
| `gml:name`              | sì  | sì   |                                                                                        |
| `gml:outerBoundaryIs`   | sì  | no    | Scritto `gml.exterior`con .                                                          |
| `gml:Point`             | sì  | sì   |                                                                                        |
| `gml:pointMember`       | sì  | sì   |                                                                                        |
| `gml:pointMembers`      | sì  | no    |                                                                                        |
| `gml:Polygon`           | sì  | sì   |                                                                                        |
| `gml:polygonMember`     | sì  | sì   |                                                                                        |
| `gml:polygonMembers`    | sì  | no    |                                                                                        |
| `gml:pos`               | sì  | sì   |                                                                                        |
| `gml:posList`           | sì  | sì   |                                                                                        |
| `gml:surfaceMember`     | sì  | sì   |                                                                                        |

#### <a name="additional-notes"></a>note aggiuntive

- Gli elementi membro verranno cercati per una geometria che può essere nascosta all'interno di elementi figlio. Questa operazione di ricerca è necessaria poiché molti formati XML che si estendono da GML non possono inserire una geometria come elemento figlio diretto di un elemento membro.
- `srsName`è parzialmente supportato per le coordinate WGS84 e i seguenti codici:[EPSG:4326](https://epsg.io/4326)) e web Mercator ([EPSG:3857](https://epsg.io/3857) o uno dei suoi codici alternativi. Qualsiasi altro sistema di coordinate verrà analizzato come WGS84 così com'è.
- A meno che non venga specificato durante la lettura di un feed XML, l'ordine degli assi viene determinato in base ai suggerimenti nel feed XML. Viene indicata una preferenza per l'ordine degli assi "latitudine, longitudine".
- A meno che non venga specificato uno spazio dei nomi GML personalizzato per le proprietà durante la scrittura in un file GML, non verranno aggiunte informazioni aggiuntive sulle proprietà.

### <a name="gpx-elements"></a>Elementi GPX

Il modulo IO spaziale supporta i seguenti elementi GPX.

| Nome dell'elemento             | Lettura    | Scrittura   | Note                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | sì     | sì     |                                                                                             |
| `gpx:author`             | sì     | sì     |                                                                                             |
| `gpx:bounds`             | sì     | sì     | Convertito in un LocationRect durante la lettura.                                                    |
| `gpx:cmt`                | sì     | sì     |                                                                                             |
| `gpx:copyright`          | sì     | sì     |                                                                                             |
| `gpx:desc`               | sì     | sì     | Copiato in una proprietà description durante la lettura per l'allineamento con altri formati XML.               |
| `gpx:dgpsid`             | sì     | sì     |                                                                                             |
| `gpx:ele`                | sì     | sì     |                                                                                             |
| `gpx:extensions`         | parziale | parziale | Quando si leggono, vengono estratte le informazioni sullo stile. Tutte le altre estensioni verranno appiattite in un oggetto JSON semplice. Vengono scritte solo le informazioni sullo stile della forma. |
| `gpx:geoidheight`        | sì     | sì     |                                                                                             |
| `gpx:gpx`                | sì     | sì     |                                                                                             |
| `gpx:hdop`               | sì     | sì     |                                                                                             |
| `gpx:link`               | sì     | sì     |                                                                                             |
| `gpx:magvar`             | sì     | sì     |                                                                                             |
| `gpx:metadata`           | sì     | sì     |                                                                                             |
| `gpx:name`               | sì     | sì     |                                                                                             |
| `gpx:pdop`               | sì     | sì     |                                                                                             |
| `gpx:rte`                | sì     | sì     |                                                                                             |
| `gpx:rtept`              | sì     | sì     |                                                                                             |
| `gpx:sat`                | sì     | sì     |                                                                                             |
| `gpx:src`                | sì     | sì     |                                                                                             |
| `gpx:sym`                | sì     | sì     | Il valore viene acquisito, ma non viene utilizzato per modificare l'icona della puntina da una puntina da sospensione.                               |
| `gpx:text`               | sì     | sì     |                                                                                             |
| `gpx:time`               | sì     | sì     |                                                                                             |
| `gpx:trk`                | sì     | sì     |                                                                                             |
| `gpx:trkpt`              | sì     | sì     |                                                                                             |
| `gpx:trkseg`             | sì     | sì     |                                                                                             |
| `gpx:type`               | sì     | sì     |                                                                                             |
| `gpx:vdop`               | sì     | sì     |                                                                                             |
| `gpx:wpt`                | sì     | sì     |                                                                                             |
| `gpx_style:color`        | sì     | sì     |                                                                                             |
| `gpx_style:line`         | parziale | parziale | `color`, `opacity` `width`, `lineCap` , sono supportati.                                           |
| `gpx_style:opacity`      | sì     | sì     |                                                                                             |
| `gpx_style:width`        | sì     | sì     |                                                                                             |
| `gpxx:DisplayColor`      | sì     | no      | Utilizzato per specificare il colore di una forma. Durante la `gpx_style:line` scrittura, viene utilizzato il colore.  |
| `gpxx:RouteExtension`    | parziale | no      | Tutte le proprietà `properties`vengono lette in . Solo `DisplayColor` viene utilizzato                     |
| `gpxx:TrackExtension`    | parziale | no      | Tutte le proprietà `properties`vengono lette in . Solo `DisplayColor` viene utilizzato                     |
| `gpxx:WaypointExtension` | parziale | no      | Tutte le proprietà `properties`vengono lette in . Solo `DisplayColor` viene utilizzato                     |
| `gpx:keywords`           | sì     | sì     |                                                                                             |
| `gpx:fix`                | sì     | sì     |                                                                                             |

#### <a name="additional-notes"></a>note aggiuntive

Quando si scrive;

- I MultiPoint saranno suddivisi in singoli waypoint.
- Poligoni e MultiPolygons verranno scritti come tracce. 
  
## <a name="supported-well-known-text-geometry-types"></a>Tipi di geometria di testo noto supportati

| Tipo di geometria | Lettura | Scrittura |
|--------------|:----:|:-----:|
| Punto | x | x |
| IL PUNTO . | x | x | 
| PUNTO M | x | x<sup>[2]</sup> |
| PUNTO : M | x<sup>[1]</sup><sup>[2]</sup> | | 
| Linestring | x | x |
| STRINGA DI LINEA | x | x | 
| LINESTRINGA M | x | x<sup>[2]</sup> |
| LINESTRING (StringaDIX) | x<sup>[1]</sup><sup>[2]</sup> | | 
| Poligono | x | x |
| POLIGONO | x | x |
| POLIGONO M | x | x<sup>[2]</sup> |
| POLIGONO -M | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipunto | x | x |
| PROPRIETÀ MULTIPOINT | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multilinestring | x | x |
| MULTILINESTRING (MULTILINESTRING) | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipolygon | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON MPOLYGON | x | x<sup>[2]</sup> |
| MULTIPOLYGON M | x<sup>[1]</sup><sup>[2]</sup> | | 
| Geometrycollection | x | x |
| CARATTERISTICHE DI GEOMETRYCOLLECTION | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION - M | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Solo il parametro s viene acquisito e aggiunto come terzo valore nel valore Posizione.

\[2\] M parametro non viene acquisito.

## <a name="delimited-spatial-data-support"></a>Supporto dei dati spaziali delimitati

I dati spaziali delimitati, ad esempio i file con valori delimitati da virgole (CSV), spesso contengono colonne che contengono dati spaziali. Ad esempio, potrebbero essere presenti colonne che contengono informazioni su latitudine e longitudine. Nel formato Testo noto potrebbe essere presente una colonna che contiene dati di geometria spaziale.

### <a name="spatial-data-column-detection"></a>Rilevamento delle colonne di dati spaziali

Durante la lettura di un file delimitato che contiene dati spaziali, l'intestazione verrà analizzata per determinare quali colonne contengono campi posizione. Se l'intestazione contiene informazioni sul tipo, verrà utilizzata per eseguire il cast dei valori della cella nel tipo appropriato. Se non viene specificata alcuna intestazione, la prima riga verrà analizzata e utilizzata per generare un'intestazione. Quando si analizza la prima riga, viene eseguito un controllo per far corrispondere i nomi delle colonne con i nomi seguenti in modo senza distinzione tra maiuscole e minuscole. L'ordine dei nomi è la priorità, nel caso in cui in un file esistano due o più nomi.

#### <a name="latitude"></a>Latitude

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Longitudine

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Altitudine

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Area geografica

La prima riga di dati verrà analizzata alla ricerca di stringhe in formato Testo noto. 

### <a name="delimited-data-column-types"></a>Tipi di colonne di dati delimitati

Durante l'analisi della riga di intestazione, tutte le informazioni sul tipo presenti nel nome della colonna verranno estratte e utilizzate per eseguire il cast delle celle in tale colonna. Di seguito è riportato un esempio di un nome di colonna con un valore di tipo: "ColumnName (typeName)". Sono supportati i seguenti nomi di tipo senza distinzione tra maiuscole e minuscole:

#### <a name="numbers"></a>Numeri

- edm.int64
- INT
- long
- edm.double
- float
- double
- d'acquisto

#### <a name="booleans"></a>valori booleani

- edm.boolean
- bool
- boolean

#### <a name="dates"></a>Date

- edm.datetime (data/o data)
- Data
- Datetime

#### <a name="geography"></a>Area geografica

- edm.geography
- geography

#### <a name="strings"></a>Stringhe

- edm.string
- varchar
- text
- string

Se nessuna informazione sul tipo può essere estratta dall'intestazione e l'opzione di digitazione dinamica è abilitata durante la lettura, ogni cella verrà analizzata singolarmente per determinare il tipo di dati con cui è più adatto per il cast.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Lettura e scrittura di dati spaziali](spatial-io-read-write-spatial-data.md)
