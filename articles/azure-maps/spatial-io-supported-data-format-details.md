---
title: Dettagli del formato dati supportati | Mappe Microsoft Azure
description: Informazioni sul modo in cui i dati spaziali delimitati vengono analizzati nel modulo IO spaziale.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4b7c82e4650c7680709e809d9f563d79f068601f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127928"
---
# <a name="supported-data-format-details"></a>Dettagli sul formato dati supportato

Questo articolo fornisce informazioni specifiche sul supporto di lettura e scrittura per tutti i tag XML e i tipi di geometria del testo noti. Vengono inoltre illustrate le modalità di analisi dei dati spaziali delimitati nel modulo di i/o spaziale.

## <a name="supported-xml-namespaces"></a>Spazi dei nomi XML supportati

Il modulo i/o spaziale supporta i tag XML dei seguenti spazi dei nomi.

| Prefisso spazio dei nomi | URI dello spazio dei nomi   | Note                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Supporto di sola lettura nei file GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Supporto di sola lettura nei file GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Supporto di sola lettura nei file GPX. Analizza e utilizza DisplayColor. Tutte le altre proprietà aggiunte ai metadati della forma. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Supportato nei file GPX. Usa il colore della linea. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Sola lettura. GeoRSS scrive utilizzando il formato Atom.              |

## <a name="supported-xml-elements"></a>Elementi XML supportati

Il modulo IO spaziale supporta gli elementi XML seguenti. Eventuali tag XML non supportati verranno convertiti in un oggetto JSON. Ogni tag verrà quindi aggiunto come proprietà nel `properties` campo della forma padre o del livello.

### <a name="kml-elements"></a>Elementi KML

Il modulo IO spaziale supporta gli elementi KML seguenti.

| Nome dell'elemento         | Lettura    | Scrittura   | Note                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial | sì     | L'oggetto viene analizzato ma non viene usato per la forma di posizionamento.                                                                    |
| `AddressDetails`     | partial | no      | L'oggetto viene analizzato ma non viene usato per la forma di posizionamento.                                                                    |
| `atom:author`        | Sì     | Sì     |                                                                                                                            |
| `atom:link`          | Sì     | Sì     |                                                                                                                            |
| `atom:name`          | Sì     | Sì     |                                                                                                                            |
| `BalloonStyle`       | partial | partial | `displayMode` non è supportata. Convertito in un oggetto `PopupTemplate` . Per scrivere, aggiungere una `popupTemplate` proprietà come proprietà della funzionalità per la quale si desidera scrivere. |
| `begin`              | Sì     | Sì     |                                                                                                                            |
| `color`              | Sì     | Sì     | Include `#AABBGGRR` e `#BBGGRR` . Analizzato in una stringa di colore CSS                                                           |
| `colorMode`          | sì     | no      |                                                                                                                            |
| `coordinates`        | sì     | Sì     |                                                                                                                            |
| `Data`               | Sì     | Sì     |                                                                                                                            |
| `description`        | Sì     | Sì     |                                                                                                                            |
| `displayName`        | Sì     | Sì     |                                                                                                                            |
| `Document`           | Sì     | Sì     |                                                                                                                            |
| `drawOrder`          | partial | no      | Leggere per le sovrapposizioni di base e usarle per ordinarle. 
| `east`               | Sì     | Sì     |                                                                                                                            |
| `end`                | Sì     | Sì     |                                                                                                                            |
| `ExtendedData`       | Sì     | Sì     | Supporta `Data` `SimpleData` `Schema` le sostituzioni di entità non tipizzate, o e del form `$[dataName]` .                      |
| `extrude`            | partial | partial | Supportato solo per i poligoni. La multigeometria con poligoni di altezze diverse verrà suddivisa in singole funzionalità. Gli stili di linea non sono supportati. I poligoni con un'altitudine pari a 0 verranno sottoposti a rendering come poligono bidimensionale. Durante la lettura, l'altitudine della prima coordinata nell'anello esterno verrà aggiunta come proprietà Height del poligono. Quindi, l'altitudine della prima coordinata verrà utilizzata per eseguire il rendering del poligono sulla mappa. |
| `fill`               | Sì     | Sì     |                                                                                                                            |
| `Folder`             | Sì     | Sì     |                                                                                                                            |
| `GroundOverlay`      | Sì     | Sì     | `color`non è supportato                                                                                                   |
| `heading`            | partial | no      | Analizzato ma non sottoposto a rendering da `SimpleDataLayer` . Scrive solo se i dati vengono archiviati nella proprietà della forma.                 |
| `hotSpot`            | sì     | partial | Scrive solo se i dati vengono archiviati nella proprietà della forma. Le unità vengono restituite solo come "pixel".                         |
| `href`               | Sì     | Sì     |                                                                                                                            |
| `Icon`               | partial | partial | Analizzato ma non sottoposto a rendering da `SimpleDataLayer` . Scrive solo la proprietà Icon della forma se contiene dati URI. È supportato solo `href`. |
| `IconStyle`          | partial | partial | `icon``heading` `colorMode` i valori,, e `hotspots` vengono analizzati, ma non vengono sottoposti a rendering da`SimpleDataLayer`         |
| `innerBoundaryIs`    | Sì     | Sì     |                                                                                                                            |
| `kml`                | Sì     | sì     |                                                                                                                            |
| `LabelStyle`         | No      | no      |                                                                                                                            |
| `LatLonBox`          | sì     | Sì     |                                                                                                                            |
| `gx:LatLonQuad`      | Sì     | Sì     |                                                                                                                            |
| `LinearRing`         | Sì     | Sì     |                                                                                                                            |
| `LineString`         | Sì     | Sì     |                                                                                                                            |
| `LineStyle`          | Sì     | Sì     | `colorMode` non è supportata.                                                                                         |
| `Link`               | sì     | no      | Per i `href` collegamenti di rete è supportata solo la proprietà.                                                                   |
| `MultiGeometry`      | partial | partial | Può essere suddiviso in singole funzionalità durante la lettura.                                                                     |
| `name`               | Sì     | Sì     |                                                                                                                            |
| `NetworkLink`        | sì     | no      | I collegamenti devono trovarsi nello stesso dominio del documento.                                                                  |
| `NetworkLinkControl` | No      | no      |                                                                                                                            |
| `north`              | sì     | Sì     |                                                                                                                            |
| `open`               | Sì     | Sì     |                                                                                                                            |
| `outerBoundaryIs`    | Sì     | Sì     |                                                                                                                            |
| `outline`            | Sì     | sì     |                                                                                                                            |
| `overlayXY`          | No      | no      |                                                                                                                            |
| `Pair`               | partial | no      | `normal`È supportato solo lo stile in un oggetto `StyleMap` . `highlight` non è supportata.                                   |
| `phoneNumber`        | Sì     | sì     |                                                                                                                            |
| `PhotoOverlay`       | No      | no      |                                                                                                                            |
| `Placemark`          | sì     | Sì     |                                                                                                                            |
| `Point`              | Sì     | Sì     |                                                                                                                            |
| `Polygon`            | Sì     | Sì     |                                                                                                                            |
| `PolyStyle`          | Sì     | Sì     |                                                                                                                            |
| `Region`             | partial | partial | `LatLongBox`è supportato a livello di documento.                                                                      |
| `rotation`           | No      | No      |                                                                                                                            |
| `rotationXY`         | No      | No      |                                                                                                                            |
| `scale`              | No      | no      |                                                                                                                            |
| `Schema`             | sì     | Sì     |                                                                                                                            |
| `SchemaData`         | Sì     | Sì     |                                                                                                                            |
| `schemaUrl`          | partial | sì     | Non supporta il caricamento di stili da documenti esterni che non sono inclusi in un KMZ.                             |
| `ScreenOverlay`      | No      | No      |                                                                                                                            |
| `screenXY`           | No      | no      |                                                                                                                            |
| `SimpleData`         | sì     | Sì     |                                                                                                                            |
| `SimpleField`        | Sì     | sì     |                                                                                                                            |
| `size`               | No      | no      |                                                                                                                            |
| `Snippet`            | partial | partial | `maxLines`l'attributo viene ignorato.                                                                                  |
| `south`              | Sì     | Sì     |                                                                                                                            |
| `Style`              | Sì     | Sì     |                                                                                                                            |
| `StyleMap`           | partial | no      | È supportato solo lo stile normale in un oggetto `StyleMap` .                                                                        |
| `styleUrl`           | partial | sì     | Gli URL di stile esterno non sono supportati.                                                                         |
| `text`               | Sì     | sì     | La sostituzione di `$[geDirections]` non è supportata                                                                          |
| `textColor`          | Sì     | Sì     |                                                                                                                            |
| `TimeSpan`           | Sì     | Sì     |                                                                                                                            |
| `TimeStamp`          | Sì     | Sì     |                                                                                                                            |
| `value`              | Sì     | sì     |                                                                                                                            |
| `viewRefreshMode`    | partial | no      |  Se punta a un servizio WMS, `onStop` è supportato solo per le sovrapposizioni di base. Verrà accodato `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` all'URL e aggiornato durante lo spostamento della mappa.  |
| `visibility`         | Sì     | Sì     |                                                                                                                            |
| `west`               | Sì     | Sì     |                                                                                                                            |
| `when`               | Sì     | Sì     |                                                                                                                            |
| `width`              | Sì     | sì     |                                                                                                                            |

### <a name="georss-elements"></a>Elementi GeoRSS

Il modulo IO spaziale supporta gli elementi GeoRSS seguenti.

| Nome dell'elemento             | Lettura    | Scrittura | Note                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | Sì     | Sì   |                                                                                                |
| `atom:category`          | Sì     | Sì   |                                                                                                |
| `atom:content`           | Sì     | Sì   |                                                                                                |
| `atom:contributor`       | Sì     | Sì   |                                                                                                |
| `atom:email`             | Sì     | Sì   |                                                                                                |
| `atom:entry`             | Sì     | Sì   |                                                                                                |
| `atom:feed`              | Sì     | Sì   |                                                                                                |
| `atom:icon`              | Sì     | Sì   |                                                                                                |
| `atom:id`                | Sì     | Sì   |                                                                                                |
| `atom:link`              | Sì     | Sì   |                                                                                                |
| `atom:logo`              | Sì     | Sì   |                                                                                                |
| `atom:name`              | Sì     | Sì   |                                                                                                |
| `atom:published`         | Sì     | Sì   |                                                                                                |
| `atom:rights`            | Sì     | Sì   |                                                                                                |
| `atom:source`            | Sì     | Sì   |                                                                                                |
| `atom:subtitle`          | Sì     | Sì   |                                                                                                |
| `atom:summary`           | Sì     | Sì   |                                                                                                |
| `atom:title`             | Sì     | Sì   |                                                                                                |
| `atom:updated`           | Sì     | Sì   |                                                                                                |
| `atom:uri`               | Sì     | Sì   |                                                                                                |
| `geo:lat`                | sì     | no    | Scritto come `georss:point` .                                                                   |
| `geo:lon`                | sì     | no    | Scritto come `georss:point` .                                                                   |
| `geo:long`               | sì     | no    | Scritto come `georss:point` .                                                                   |
| `georss:box`             | sì     | no    | Lettura come poligono e data una `subType` proprietà di "Rectangle"                                |
| `georss:circle`          | Sì     | Sì   |                                                                                                |
| `georss:elev`            | Sì     | Sì   |                                                                                                |
| `georss:featurename`     | Sì     | Sì   |                                                                                                |
| `georss:featuretypetag`  | Sì     | Sì   |                                                                                                |
| `georss:floor`           | Sì     | Sì   |                                                                                                |
| `georss:line`            | Sì     | Sì   |                                                                                                |
| `georss:point`           | Sì     | Sì   |                                                                                                |
| `georss:polygon`         | Sì     | Sì   |                                                                                                |
| `georss:radius`          | Sì     | Sì   |                                                                                                |
| `georss:relationshiptag` | Sì     | Sì   |                                                                                                |
| `georss:where`           | Sì     | Sì   |                                                                                                |
| `geourl:latitude`        | sì     | no    | Scritto come `georss:point` .                                                                   |
| `geourl:longitude`       | sì     | no    | Scritto come `georss:point` .                                                                   |
| `position`               | sì     | no    | In alcuni feed XML verrà eseguito il wrapping di GML con un tag position anziché eseguire il wrapping con un `georss:where` tag. Questo tag verrà letto, ma verrà scritto utilizzando un `georss:where` tag. |
| `rss`                    | sì     | no    | GeoRSS scritto in formato ATOM.                                                                 |
| `rss:author`             | sì     | partial | Scritto come `atom:author` .                                                                 |
| `rss:category`           | sì     | partial | Scritto come `atom:category` .                                                               |
| `rss:channel`            | sì     | no    |                                                                                                |
| `rss:cloud`              | sì     | no    |                                                                                                |
| `rss:comments`           | sì     | no    |                                                                                                |
| `rss:copyright`          | sì     | partial | Scritto come `atom:rights` forma if non dispone già di una `rights` `properties` Proprietà.       |
| `rss:description`        | sì     | partial | Scritto come `atom:content` forma if non dispone già di una `content` `properties` Proprietà.      |
| `rss:docs`               | sì     | no    |                                                                                                |
| `rss:enclosure`          | sì     | no    |                                                                                                |
| `rss:generator`          | sì     | no    |                                                                                                |
| `rss:guid`               | sì     | partial | Scritto come `atom:id` forma if non dispone già di una `id` `properties` Proprietà.         |
| `rss:image`              | sì     | partial | Scritto come `atom:logo` forma if non dispone già di una `logo` `properties` Proprietà.      |
| `rss:item`               | sì     | partial | Scritto come `atom:entry` .                                                                  |
| `rss:language`           | sì     | no    |                                                                                                |
| `rss:lastBuildDate`      | sì     | partial | Scritto come `atom:updated` forma if non dispone già di una `updated` `properties` Proprietà.     |
| `rss:link`               | sì     | partial | Scritto come `atom:link` .                                                                   |
| `rss:managingEditor`     | sì     | partial | Scritto come `atom:contributor` .                                                            |
| `rss:pubDate`            | sì     | partial | Scritto come `atom:published` forma if non dispone già di una `published` `properties` Proprietà.  |
| `rss:rating`             | sì     | no    |                                                                                                |
| `rss:skipDays`           | sì     | no    |                                                                                                |
| `rss:skipHours`          | sì     | no    |                                                                                                |
| `rss:source`             | sì     | partial | Scritto come oggetto `atom:source` contenente un oggetto `atom:link` .                                       |
| `rss:textInput`          | sì     | no    |                                                                                                |
| `rss:title`              | sì     | partial | Scritto come `atom:title` .                                                                  |
| `rss:ttl`                | sì     | no    |                                                                                                |
| `rss:webMaster`          | sì     | no    |                                                                                                |

### <a name="gml-elements"></a>GML (elementi)

Il modulo IO spaziale supporta gli elementi GML seguenti. 

| Nome dell'elemento            | Lettura | Scrittura | Note                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | sì  | no    | Scritto come `gml:posList` .                                                              |
| `gml:curveMember`       | sì  | no    |                                                                                        |
| `gml:curveMembers`      | sì  | no    |                                                                                        |
| `gml:Box`               | sì  | no    | Scritto come `gml:Envelope` .                                                             |
| `gml:description`       | Sì  | Sì   |                                                                                        |
| `gml:Envelope`          | Sì  | Sì   |                                                                                        |
| `gml:exterior`          | Sì  | Sì   |                                                                                        |
| `gml:Feature`           | sì  | no    | Scritto come forma.                                                                    |
| `gml:FeatureCollection` | sì  | no    | Scritto come raccolta di geometria.                                                      |
| `gml:featureMember`     | sì  | no    | Scritto come raccolta di geometria.                                                      |
| `gml:geometry`          | sì  | no    | Scritto come forma.                                                                    |
| `gml:geometryMember`    | Sì  | Sì   |                                                                                        |
| `gml:geometryMembers`   | Sì  | Sì   |                                                                                        |
| `gml:identifier`        | Sì  | Sì   |                                                                                        |
| `gml:innerBoundaryIs`   | sì  | no    | Scritto utilizzando `gml.interior` .                                                          |
| `gml:interior`          | Sì  | Sì   |                                                                                        |
| `gml:LinearRing`        | Sì  | Sì   |                                                                                        |
| `gml:LineString`        | Sì  | Sì   |                                                                                        |
| `gml:lineStringMember`  | Sì  | Sì   |                                                                                        |
| `gml:lineStringMembers` | sì  | no    |                                                                                        |
| `gml:MultiCurve`        | sì  | no    | Legge solo `gml:LineString` i membri. Scritto come`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial  | partial   | Viene letto solo come Featurecollection.                                              |
| `gml:MultiLineString`   | Sì  | Sì   |                                                                                        |
| `gml:MultiPoint`        | Sì  | Sì   |                                                                                        |
| `gml:MultiPolygon`      | Sì  | Sì   |                                                                                        |
| `gml:MultiSurface`      | sì  | no    | Legge solo `gml:Polygon` i membri. Scritto come`gml.MultiPolygon`                        |
| `gml:name`              | Sì  | Sì   |                                                                                        |
| `gml:outerBoundaryIs`   | sì  | no    | Scritto utilizzando `gml.exterior` .                                                          |
| `gml:Point`             | Sì  | Sì   |                                                                                        |
| `gml:pointMember`       | Sì  | Sì   |                                                                                        |
| `gml:pointMembers`      | sì  | no    |                                                                                        |
| `gml:Polygon`           | sì  | Sì   |                                                                                        |
| `gml:polygonMember`     | Sì  | Sì   |                                                                                        |
| `gml:polygonMembers`    | sì  | no    |                                                                                        |
| `gml:pos`               | sì  | Sì   |                                                                                        |
| `gml:posList`           | Sì  | Sì   |                                                                                        |
| `gml:surfaceMember`     | Sì  | Sì   |                                                                                        |

#### <a name="additional-notes"></a>Note aggiuntive

- Verrà eseguita la ricerca di una geometria che può essere nascosta negli elementi figlio. Questa operazione di ricerca è necessaria in quanto molti formati XML estesi da GML non possono inserire una geometria come figlio diretto di un elemento membro.
- `srsName`è parzialmente supportato per le coordinate WGS84 e i codici seguenti:[EPSG: 4326](https://epsg.io/4326)) e Web Mercator ([EPSG: 3857](https://epsg.io/3857) o uno dei codici alternativi. Qualsiasi altro sistema di coordinate verrà analizzato come WGS84.
- A meno che non venga specificato durante la lettura di un feed XML, l'ordine dell'asse viene determinato in base agli hint nel feed XML. Viene fornita una preferenza per l'ordine dell'asse "latitudine, Longitudine".
- A meno che non venga specificato uno spazio dei nomi GML personalizzato per le proprietà durante la scrittura in un file GML, le informazioni aggiuntive sulle proprietà non verranno aggiunte.

### <a name="gpx-elements"></a>Elementi GPX

Il modulo IO spaziale supporta gli elementi GPX seguenti.

| Nome dell'elemento             | Lettura    | Scrittura   | Note                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | Sì     | Sì     |                                                                                             |
| `gpx:author`             | Sì     | Sì     |                                                                                             |
| `gpx:bounds`             | Sì     | Sì     | Convertito in LocationRect durante la lettura.                                                    |
| `gpx:cmt`                | Sì     | Sì     |                                                                                             |
| `gpx:copyright`          | Sì     | Sì     |                                                                                             |
| `gpx:desc`               | Sì     | sì     | Copiato in una proprietà Description durante la lettura per l'allineamento con altri formati XML.               |
| `gpx:dgpsid`             | Sì     | Sì     |                                                                                             |
| `gpx:ele`                | Sì     | sì     |                                                                                             |
| `gpx:extensions`         | partial | partial | Quando viene letto, vengono estratte le informazioni sullo stile. Tutte le altre estensioni saranno appiattite in un semplice oggetto JSON. Vengono scritte solo le informazioni sullo stile della forma. |
| `gpx:geoidheight`        | Sì     | Sì     |                                                                                             |
| `gpx:gpx`                | Sì     | Sì     |                                                                                             |
| `gpx:hdop`               | Sì     | Sì     |                                                                                             |
| `gpx:link`               | Sì     | Sì     |                                                                                             |
| `gpx:magvar`             | Sì     | Sì     |                                                                                             |
| `gpx:metadata`           | Sì     | Sì     |                                                                                             |
| `gpx:name`               | Sì     | Sì     |                                                                                             |
| `gpx:pdop`               | Sì     | Sì     |                                                                                             |
| `gpx:rte`                | Sì     | Sì     |                                                                                             |
| `gpx:rtept`              | Sì     | Sì     |                                                                                             |
| `gpx:sat`                | Sì     | Sì     |                                                                                             |
| `gpx:src`                | Sì     | Sì     |                                                                                             |
| `gpx:sym`                | Sì     | sì     | Il valore viene acquisito, ma non viene usato per modificare l'icona della puntina da disegno.                               |
| `gpx:text`               | Sì     | Sì     |                                                                                             |
| `gpx:time`               | Sì     | Sì     |                                                                                             |
| `gpx:trk`                | Sì     | Sì     |                                                                                             |
| `gpx:trkpt`              | Sì     | Sì     |                                                                                             |
| `gpx:trkseg`             | Sì     | Sì     |                                                                                             |
| `gpx:type`               | Sì     | Sì     |                                                                                             |
| `gpx:vdop`               | Sì     | Sì     |                                                                                             |
| `gpx:wpt`                | Sì     | Sì     |                                                                                             |
| `gpx_style:color`        | Sì     | sì     |                                                                                             |
| `gpx_style:line`         | partial | partial | `color`, `opacity` , `width` , `lineCap` sono supportati.                                           |
| `gpx_style:opacity`      | Sì     | Sì     |                                                                                             |
| `gpx_style:width`        | Sì     | Sì     |                                                                                             |
| `gpxx:DisplayColor`      | sì     | no      | Utilizzato per specificare il colore di una forma. Quando si scrive, `gpx_style:line` viene invece utilizzato il colore.  |
| `gpxx:RouteExtension`    | partial | no      | Tutte le proprietà vengono lette in `properties` . Solo `DisplayColor` viene utilizzato                     |
| `gpxx:TrackExtension`    | partial | no      | Tutte le proprietà vengono lette in `properties` . Solo `DisplayColor` viene utilizzato                     |
| `gpxx:WaypointExtension` | partial | no      | Tutte le proprietà vengono lette in `properties` . Solo `DisplayColor` viene utilizzato                     |
| `gpx:keywords`           | Sì     | Sì     |                                                                                             |
| `gpx:fix`                | Sì     | sì     |                                                                                             |

#### <a name="additional-notes"></a>Note aggiuntive

Durante la scrittura;

- I più punti verranno suddivisi in singoli waypoint.
- Poligoni e multipoligoni verranno scritti come tracce. 
  
## <a name="supported-well-known-text-geometry-types"></a>Tipi di geometria del testo noti supportati

| Tipo di geometria | Lettura | Scrittura |
|--------------|:----:|:-----:|
| PUNTO | x | x |
| PUNTO Z | x | x | 
| PUNTO M | x | x<sup>[2]</sup> |
| PUNTO ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| ZM LINESTRING | x<sup>[1]</sup><sup>[2]</sup> | | 
| POLIGONO | x | x |
| POLIGONO Z | x | x |
| POLIGONO M | x | x<sup>[2]</sup> |
| ZM POLIGONO | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOINT | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| M MULTILINESTRING | x | x<sup>[2]</sup> |
| ZM MULTILINESTRING | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLIGONO M | x | x<sup>[2]</sup> |
| ZM MULTIPOLIGONO | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION (ZM) | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1 \] solo il parametro Z viene acquisito e aggiunto come terzo valore nel valore Position.

\[il \] parametro 2 M non viene acquisito.

## <a name="delimited-spatial-data-support"></a>Supporto dei dati spaziali delimitati

I dati spaziali delimitati, ad esempio i file con valori delimitati da virgole (CSV), presentano spesso colonne che contengono dati spaziali. Potrebbero ad esempio essere presenti colonne che contengono informazioni sulla latitudine e la longitudine. In un formato di testo noto, potrebbe essere presente una colonna che contiene dati geometrici spaziali.

### <a name="spatial-data-column-detection"></a>Rilevamento della colonna di dati spaziali

Quando si legge un file delimitato che contiene dati spaziali, l'intestazione verrà analizzata per determinare quali colonne contengono i campi del percorso. Se l'intestazione contiene informazioni sul tipo, verrà utilizzata per eseguire il cast dei valori di cella al tipo appropriato. Se non viene specificata alcuna intestazione, la prima riga verrà analizzata e utilizzata per generare un'intestazione. Quando si analizza la prima riga, viene eseguito un controllo per trovare la corrispondenza con i nomi di colonna con i nomi seguenti senza distinzione tra maiuscole e minuscole. L'ordine dei nomi è la priorità, nel caso in cui esistano due o più nomi in un file.

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

La prima riga di dati verrà analizzata per le stringhe in formato di testo noto. 

### <a name="delimited-data-column-types"></a>Tipi di colonna di dati delimitati

Quando si analizza la riga di intestazione, le informazioni sui tipi presenti nel nome della colonna verranno estratte e utilizzate per eseguire il cast delle celle in tale colonna. Di seguito è riportato un esempio di un nome di colonna con un valore di tipo: "ColumnName (typeName)". Sono supportati i nomi dei tipi senza distinzione tra maiuscole e minuscole seguenti:

#### <a name="numbers"></a>Numeri

- EDM. Int64
- INT
- long
- EDM. Double
- float
- double
- d'acquisto

#### <a name="booleans"></a>valori booleani

- EDM. Boolean
- bool
- boolean

#### <a name="dates"></a>Date

- EDM. DateTime
- Data
- Datetime

#### <a name="geography"></a>Area geografica

- EDM. Geography
- geography

#### <a name="strings"></a>Stringhe

- EDM. String
- varchar
- text
- string

Se non è possibile estrarre informazioni sul tipo dall'intestazione e l'opzione di tipizzazione dinamica è abilitata durante la lettura, ogni cella verrà analizzata singolarmente per determinare il tipo di dati più adatto per il cast.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Leggere e scrivere dati spaziali](spatial-io-read-write-spatial-data.md)
