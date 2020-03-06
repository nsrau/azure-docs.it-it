---
title: Dettagli del formato dati supportati | Mappe Microsoft Azure
description: Informazioni sul modo in cui i dati spaziali delimitati vengono analizzati nel modulo IO spaziale.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cbeb1ccaed6ec2c62aaa731e88fcefbe84d1df02
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370882"
---
# <a name="supported-data-format-details"></a>Dettagli del formato dati supportati

Questo articolo fornisce informazioni specifiche sul supporto di lettura e scrittura per tutti i tag XML e i tipi di geometria del testo noti. Viene inoltre illustrata la modalità di analisi dei dati spaziali delimitati nel modulo IO spaziale.

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

Il modulo IO spaziale supporta gli elementi XML seguenti. Eventuali tag XML non supportati verranno convertiti in un oggetto JSON. Ogni tag verrà quindi aggiunto come proprietà nel campo `properties` della forma padre o del livello.

### <a name="kml-elements"></a>Elementi KML

Il modulo IO spaziale supporta gli elementi KML seguenti.

| Nome dell'elemento         | Lettura    | Scrittura   | Note                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | riga parzialmente eseguita | sì     | L'oggetto viene analizzato ma non viene usato per la forma di posizionamento.                                                                    |
| `AddressDetails`     | riga parzialmente eseguita | no      | L'oggetto viene analizzato ma non viene usato per la forma di posizionamento.                                                                    |
| `atom:author`        | sì     | sì     |                                                                                                                            |
| `atom:link`          | sì     | sì     |                                                                                                                            |
| `atom:name`          | sì     | sì     |                                                                                                                            |
| `BalloonStyle`       | riga parzialmente eseguita | riga parzialmente eseguita | `displayMode` non è supportata. Convertito in un `PopupTemplate`. Per scrivere, aggiungere una proprietà `popupTemplate` come proprietà della funzionalità per la quale si desidera scrivere. |
| `begin`              | sì     | sì     |                                                                                                                            |
| `color`              | sì     | sì     | Include `#AABBGGRR` e `#BBGGRR`. Analizzato in una stringa di colore CSS                                                           |
| `colorMode`          | sì     | no      |                                                                                                                            |
| `coordinates`        | sì     | sì     |                                                                                                                            |
| `Data`               | sì     | sì     |                                                                                                                            |
| `description`        | sì     | sì     |                                                                                                                            |
| `displayName`        | sì     | sì     |                                                                                                                            |
| `Document`           | sì     | sì     |                                                                                                                            |
| `drawOrder`          | riga parzialmente eseguita | no      | Leggere per le sovrapposizioni di base e usarle per ordinarle. 
| `east`               | sì     | sì     |                                                                                                                            |
| `end`                | sì     | sì     |                                                                                                                            |
| `ExtendedData`       | sì     | sì     | Supporta le sostituzioni di `Data`, `SimpleData` o `Schema`non tipizzate e le sostituzioni di entità del modulo `$[dataName]`.                      |
| `extrude`            | riga parzialmente eseguita | riga parzialmente eseguita | Supportato solo per i poligoni. La multigeometria con poligoni di altezze diverse verrà suddivisa in singole funzionalità. Gli stili di linea non sono supportati. I poligoni con un'altitudine pari a 0 verranno sottoposti a rendering come poligono bidimensionale. Durante la lettura, l'altitudine della prima coordinata nell'anello esterno verrà aggiunta come proprietà Height del poligono. Quindi, l'altitudine della prima coordinata verrà utilizzata per eseguire il rendering del poligono sulla mappa. |
| `fill`               | sì     | sì     |                                                                                                                            |
| `Folder`             | sì     | sì     |                                                                                                                            |
| `GroundOverlay`      | sì     | sì     | `color` non è supportato                                                                                                   |
| `heading`            | riga parzialmente eseguita | no      | Analizzato ma non sottoposto a rendering da `SimpleDataLayer`. Scrive solo se i dati vengono archiviati nella proprietà della forma.                 |
| `hotSpot`            | sì     | riga parzialmente eseguita | Scrive solo se i dati vengono archiviati nella proprietà della forma. Le unità vengono restituite solo come "pixel".                         |
| `href`               | sì     | sì     |                                                                                                                            |
| `Icon`               | riga parzialmente eseguita | riga parzialmente eseguita | Analizzato ma non sottoposto a rendering da `SimpleDataLayer`. Scrive solo la proprietà Icon della forma se contiene dati URI. È supportato solo `href`. |
| `IconStyle`          | riga parzialmente eseguita | riga parzialmente eseguita | i valori `icon`, `heading`, `colorMode`e `hotspots` vengono analizzati, ma non vengono sottoposti a rendering per `SimpleDataLayer`         |
| `innerBoundaryIs`    | sì     | sì     |                                                                                                                            |
| `kml`                | sì     | sì     |                                                                                                                            |
| `LabelStyle`         | no      | no      |                                                                                                                            |
| `LatLonBox`          | sì     | sì     |                                                                                                                            |
| `gx:LatLonQuad`      | sì     | sì     |                                                                                                                            |
| `LinearRing`         | sì     | sì     |                                                                                                                            |
| `LineString`         | sì     | sì     |                                                                                                                            |
| `LineStyle`          | sì     | sì     | `colorMode` non è supportata.                                                                                         |
| `Link`               | sì     | no      | Per i collegamenti di rete è supportata solo la proprietà `href`.                                                                   |
| `MultiGeometry`      | riga parzialmente eseguita | riga parzialmente eseguita | Può essere suddiviso in singole funzionalità durante la lettura.                                                                     |
| `name`               | sì     | sì     |                                                                                                                            |
| `NetworkLink`        | sì     | no      | I collegamenti devono trovarsi nello stesso dominio del documento.                                                                  |
| `NetworkLinkControl` | no      | no      |                                                                                                                            |
| `north`              | sì     | sì     |                                                                                                                            |
| `open`               | sì     | sì     |                                                                                                                            |
| `outerBoundaryIs`    | sì     | sì     |                                                                                                                            |
| `outline`            | sì     | sì     |                                                                                                                            |
| `overlayXY`          | no      | no      |                                                                                                                            |
| `Pair`               | riga parzialmente eseguita | no      | È supportato solo lo stile `normal` in un `StyleMap`. `highlight` non è supportata.                                   |
| `phoneNumber`        | sì     | sì     |                                                                                                                            |
| `PhotoOverlay`       | no      | no      |                                                                                                                            |
| `Placemark`          | sì     | sì     |                                                                                                                            |
| `Point`              | sì     | sì     |                                                                                                                            |
| `Polygon`            | sì     | sì     |                                                                                                                            |
| `PolyStyle`          | sì     | sì     |                                                                                                                            |
| `Region`             | riga parzialmente eseguita | riga parzialmente eseguita | `LatLongBox` è supportato a livello di documento.                                                                      |
| `rotation`           | no      | no      |                                                                                                                            |
| `rotationXY`         | no      | no      |                                                                                                                            |
| `scale`              | no      | no      |                                                                                                                            |
| `Schema`             | sì     | sì     |                                                                                                                            |
| `SchemaData`         | sì     | sì     |                                                                                                                            |
| `schemaUrl`          | riga parzialmente eseguita | sì     | Non supporta il caricamento di stili da documenti esterni che non sono inclusi in un KMZ.                             |
| `ScreenOverlay`      | no      | no      |                                                                                                                            |
| `screenXY`           | no      | no      |                                                                                                                            |
| `SimpleData`         | sì     | sì     |                                                                                                                            |
| `SimpleField`        | sì     | sì     |                                                                                                                            |
| `size`               | no      | no      |                                                                                                                            |
| `Snippet`            | riga parzialmente eseguita | riga parzialmente eseguita | `maxLines` attributo viene ignorato.                                                                                  |
| `south`              | sì     | sì     |                                                                                                                            |
| `Style`              | sì     | sì     |                                                                                                                            |
| `StyleMap`           | riga parzialmente eseguita | no      | È supportato solo lo stile normale in una `StyleMap`.                                                                        |
| `styleUrl`           | riga parzialmente eseguita | sì     | Gli URL di stile esterno non sono supportati.                                                                         |
| `text`               | sì     | sì     | La sostituzione di `$[geDirections]` non è supportata                                                                          |
| `textColor`          | sì     | sì     |                                                                                                                            |
| `TimeSpan`           | sì     | sì     |                                                                                                                            |
| `TimeStamp`          | sì     | sì     |                                                                                                                            |
| `value`              | sì     | sì     |                                                                                                                            |
| `viewRefreshMode`    | riga parzialmente eseguita | no      |  Se si fa riferimento a un servizio WMS, solo `onStop` è supportato per le sovrapposizioni di base. Accoda `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` all'URL e aggiorna quando la mappa viene spostata.  |
| `visibility`         | sì     | sì     |                                                                                                                            |
| `west`               | sì     | sì     |                                                                                                                            |
| `when`               | sì     | sì     |                                                                                                                            |
| `width`              | sì     | sì     |                                                                                                                            |

### <a name="georss-elements"></a>Elementi GeoRSS

Il modulo IO spaziale supporta gli elementi GeoRSS seguenti.

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
| `geo:lat`                | sì     | no    | Scritto come `georss:point`.                                                                   |
| `geo:lon`                | sì     | no    | Scritto come `georss:point`.                                                                   |
| `geo:long`               | sì     | no    | Scritto come `georss:point`.                                                                   |
| `georss:box`             | sì     | no    | Viene letto come poligono e viene data una `subType` proprietà di "Rectangle"                                |
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
| `geourl:latitude`        | sì     | no    | Scritto come `georss:point`.                                                                   |
| `geourl:longitude`       | sì     | no    | Scritto come `georss:point`.                                                                   |
| `position`               | sì     | no    | In alcuni feed XML verrà eseguito il wrapping di GML con un tag position anziché eseguire il wrapping con un tag GeoRSS: Where. Questo tag verrà letto, ma verrà scritto usando un tag GeoRSS: Where. |
| `rss`                    | sì     | no    | GeoRSS scritto in formato ATOM.                                                                 |
| `rss:author`             | sì     | riga parzialmente eseguita | Scritto come `atom:author`.                                                                 |
| `rss:category`           | sì     | riga parzialmente eseguita | Scritto come `atom:category`.                                                               |
| `rss:channel`            | sì     | no    |                                                                                                |
| `rss:cloud`              | sì     | no    |                                                                                                |
| `rss:comments`           | sì     | no    |                                                                                                |
| `rss:copyright`          | sì     | riga parzialmente eseguita | Scritto come `atom:rights` se Shape non dispone già di una proprietà `rights` `properties`.       |
| `rss:description`        | sì     | riga parzialmente eseguita | Scritto come `atom:content` se Shape non dispone già di una proprietà `content` `properties`.      |
| `rss:docs`               | sì     | no    |                                                                                                |
| `rss:enclosure`          | sì     | no    |                                                                                                |
| `rss:generator`          | sì     | no    |                                                                                                |
| `rss:guid`               | sì     | riga parzialmente eseguita | Scritto come `atom:id` se Shape non dispone già di una proprietà `id` `properties`.         |
| `rss:image`              | sì     | riga parzialmente eseguita | Scritto come `atom:logo` se Shape non dispone già di una proprietà `logo` `properties`.      |
| `rss:item`               | sì     | riga parzialmente eseguita | Scritto come `atom:entry`.                                                                  |
| `rss:language`           | sì     | no    |                                                                                                |
| `rss:lastBuildDate`      | sì     | riga parzialmente eseguita | Scritto come `atom:updated` se Shape non dispone già di una proprietà `updated` `properties`.     |
| `rss:link`               | sì     | riga parzialmente eseguita | Scritto come `atom:link`.                                                                   |
| `rss:managingEditor`     | sì     | riga parzialmente eseguita | Scritto come `atom:contributor`.                                                            |
| `rss:pubDate`            | sì     | riga parzialmente eseguita | Scritto come `atom:published` se Shape non dispone già di una proprietà `published` `properties`.  |
| `rss:rating`             | sì     | no    |                                                                                                |
| `rss:skipDays`           | sì     | no    |                                                                                                |
| `rss:skipHours`          | sì     | no    |                                                                                                |
| `rss:source`             | sì     | riga parzialmente eseguita | Scritto come `atom:source` contenente una `atom:link`.                                       |
| `rss:textInput`          | sì     | no    |                                                                                                |
| `rss:title`              | sì     | riga parzialmente eseguita | Scritto come `atom:title`.                                                                  |
| `rss:ttl`                | sì     | no    |                                                                                                |
| `rss:webMaster`          | sì     | no    |                                                                                                |

### <a name="gml-elements"></a>GML (elementi)

Il modulo IO spaziale supporta gli elementi GML seguenti. 

| Nome dell'elemento            | Lettura | Scrittura | Note                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | sì  | no    | Scritto come `gml:posList`.                                                              |
| `gml:curveMember`       | sì  | no    |                                                                                        |
| `gml:curveMembers`      | sì  | no    |                                                                                        |
| `gml:Box`               | sì  | no    | Scritto come `gml:Envelope`.                                                             |
| `gml:description`       | sì  | sì   |                                                                                        |
| `gml:Envelope`          | sì  | sì   |                                                                                        |
| `gml:exterior`          | sì  | sì   |                                                                                        |
| `gml:Feature`           | sì  | no    | Scritto come forma.                                                                    |
| `gml:FeatureCollection` | sì  | no    | Scritto come raccolta di geometria.                                                      |
| `gml:featureMember`     | sì  | no    | Scritto come raccolta di geometria.                                                      |
| `gml:geometry`          | sì  | no    | Scritto come forma.                                                                    |
| `gml:geometryMember`    | sì  | sì   |                                                                                        |
| `gml:geometryMembers`   | sì  | sì   |                                                                                        |
| `gml:identifier`        | sì  | sì   |                                                                                        |
| `gml:innerBoundaryIs`   | sì  | no    | Scritto con `gml.interior`.                                                          |
| `gml:interior`          | sì  | sì   |                                                                                        |
| `gml:LinearRing`        | sì  | sì   |                                                                                        |
| `gml:LineString`        | sì  | sì   |                                                                                        |
| `gml:lineStringMember`  | sì  | sì   |                                                                                        |
| `gml:lineStringMembers` | sì  | no    |                                                                                        |
| `gml:MultiCurve`        | sì  | no    | Legge solo i membri `gml:LineString`. Scritto come `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | riga parzialmente eseguita  | riga parzialmente eseguita   | Viene letto solo come Featurecollection.                                              |
| `gml:MultiLineString`   | sì  | sì   |                                                                                        |
| `gml:MultiPoint`        | sì  | sì   |                                                                                        |
| `gml:MultiPolygon`      | sì  | sì   |                                                                                        |
| `gml:MultiSurface`      | sì  | no    | Legge solo i membri `gml:Polygon`. Scritto come `gml.MultiPolygon`                        |
| `gml:name`              | sì  | sì   |                                                                                        |
| `gml:outerBoundaryIs`   | sì  | no    | Scritto con `gml.exterior`.                                                          |
| `gml:Point`             | sì  | sì   |                                                                                        |
| `gml:pointMember`       | sì  | sì   |                                                                                        |
| `gml:pointMembers`      | sì  | no    |                                                                                        |
| `gml:Polygon`           | sì  | sì   |                                                                                        |
| `gml:polygonMember`     | sì  | sì   |                                                                                        |
| `gml:polygonMembers`    | sì  | no    |                                                                                        |
| `gml:pos`               | sì  | sì   |                                                                                        |
| `gml:posList`           | sì  | sì   |                                                                                        |
| `gml:surfaceMember`     | sì  | sì   |                                                                                        |

#### <a name="additional-notes"></a>Note aggiuntive

- Verrà eseguita la ricerca di una geometria che può essere nascosta negli elementi figlio. Questa operazione di ricerca è necessaria in quanto molti formati XML estesi da GML non possono inserire una geometria come figlio diretto di un elemento membro.
- `srsName` è supportato parzialmente per le coordinate WGS84 e i codici seguenti:[EPSG: 4326](https://epsg.io/4326)) e Web Mercator ([EPSG: 3857](https://epsg.io/3857) o uno dei codici alternativi. Qualsiasi altro sistema di coordinate verrà analizzato come WGS84.
- A meno che non venga specificato durante la lettura di un feed XML, l'ordine dell'asse viene determinato in base agli hint nel feed XML. Viene fornita una preferenza per l'ordine dell'asse "latitudine, Longitudine".
- A meno che non venga specificato uno spazio dei nomi GML personalizzato per le proprietà durante la scrittura in un file GML, le informazioni aggiuntive sulle proprietà non verranno aggiunte.

### <a name="gpx-elements"></a>Elementi GPX

Il modulo IO spaziale supporta gli elementi GPX seguenti.

| Nome dell'elemento             | Lettura    | Scrittura   | Note                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | sì     | sì     |                                                                                             |
| `gpx:author`             | sì     | sì     |                                                                                             |
| `gpx:bounds`             | sì     | sì     | Convertito in LocationRect durante la lettura.                                                    |
| `gpx:cmt`                | sì     | sì     |                                                                                             |
| `gpx:copyright`          | sì     | sì     |                                                                                             |
| `gpx:desc`               | sì     | sì     | Copiato in una proprietà Description durante la lettura per l'allineamento con altri formati XML.               |
| `gpx:dgpsid`             | sì     | sì     |                                                                                             |
| `gpx:ele`                | sì     | sì     |                                                                                             |
| `gpx:extensions`         | riga parzialmente eseguita | riga parzialmente eseguita | Quando viene letto, vengono estratte le informazioni sullo stile. Tutte le altre estensioni saranno appiattite in un semplice oggetto JSON. Vengono scritte solo le informazioni sullo stile della forma. |
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
| `gpx:sym`                | sì     | sì     | Il valore viene acquisito, ma non viene usato per modificare l'icona della puntina da disegno.                               |
| `gpx:text`               | sì     | sì     |                                                                                             |
| `gpx:time`               | sì     | sì     |                                                                                             |
| `gpx:trk`                | sì     | sì     |                                                                                             |
| `gpx:trkpt`              | sì     | sì     |                                                                                             |
| `gpx:trkseg`             | sì     | sì     |                                                                                             |
| `gpx:type`               | sì     | sì     |                                                                                             |
| `gpx:vdop`               | sì     | sì     |                                                                                             |
| `gpx:wpt`                | sì     | sì     |                                                                                             |
| `gpx_style:color`        | sì     | sì     |                                                                                             |
| `gpx_style:line`         | riga parzialmente eseguita | riga parzialmente eseguita | sono supportati `color`, `opacity`, `width``lineCap`.                                           |
| `gpx_style:opacity`      | sì     | sì     |                                                                                             |
| `gpx_style:width`        | sì     | sì     |                                                                                             |
| `gpxx:DisplayColor`      | sì     | no      | Utilizzato per specificare il colore di una forma. Quando si scrive, viene invece utilizzato `gpx_style:line` colore.  |
| `gpxx:RouteExtension`    | riga parzialmente eseguita | no      | Tutte le proprietà vengono lette in `properties`. Viene usato solo `DisplayColor`.                     |
| `gpxx:TrackExtension`    | riga parzialmente eseguita | no      | Tutte le proprietà vengono lette in `properties`. Viene usato solo `DisplayColor`.                     |
| `gpxx:WaypointExtension` | riga parzialmente eseguita | no      | Tutte le proprietà vengono lette in `properties`. Viene usato solo `DisplayColor`.                     |
| `gpx:keywords`           | sì     | sì     |                                                                                             |
| `gpx:fix`                | sì     | sì     |                                                                                             |

#### <a name="additional-notes"></a>Note aggiuntive

Durante la scrittura;

- I più punti verranno suddivisi in singoli waypoint.
- Poligoni e multipoligoni verranno scritti come tracce. 
  
## <a name="supported-well-known-text-geometry-types"></a>Tipi di geometria del testo ben noti supportati

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

\[1\] solo il parametro Z viene acquisito e aggiunto come terzo valore nel valore Position.

il parametro \[2\] M non viene acquisito.

## <a name="delimited-spatial-data-support"></a>Supporto dei dati spaziali delimitati

I dati spaziali delimitati, ad esempio i file con valori delimitati da virgole (CSV), presentano spesso colonne che contengono dati spaziali. Potrebbero ad esempio essere presenti colonne che contengono informazioni sulla latitudine e la longitudine. In un formato di testo noto potrebbe essere presente una colonna che contiene dati geometrici spaziali.

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

#### <a name="longitude"></a>Longitude

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

**Numeri**

    - EDM. Int64
    - int
    - long
    - EDM. Double
    - float
    - double
    - numero

**Valori booleani**

    - EDM. Boolean
    - bool
    - boolean

**Date**

    - EDM. DateTime
    - date
    - datetime

**Area geografica**

    - EDM. Geography
    - geography

**Stringhe**

    - EDM. String
    - varchar
    - testo
    - stringa case

Se non è possibile estrarre informazioni sul tipo dall'intestazione e l'opzione di tipizzazione dinamica è abilitata durante la lettura, ogni cella verrà analizzata singolarmente per determinare il tipo di dati più adatto per il cast.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Lettura e scrittura dei dati spaziali](spatial-io-read-write-spatial-data.md)
