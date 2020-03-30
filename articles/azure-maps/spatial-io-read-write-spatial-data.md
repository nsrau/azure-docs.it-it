---
title: Lettura e scrittura di dati spaziali Mappe di Microsoft Azure
description: Informazioni su come leggere e scrivere dati usando il modulo I/O spaziale fornito da Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334149"
---
# <a name="read-and-write-spatial-data"></a>Lettura e scrittura di dati spaziali

Nella tabella seguente sono elencati i formati di file spaziali supportati per le operazioni di lettura e scrittura con il modulo I/O spaziale.

| Formato dati       | Lettura | Scrittura |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| Gpx               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| Kmz               | ✓  |  ✓  |
| CSV spaziale       | ✓  |  ✓  |
| Testo noto   | ✓  |  ✓  |

Queste sezioni successive illustrano tutti i diversi strumenti per la lettura e la scrittura di dati spaziali utilizzando il modulo IO spaziale.

## <a name="read-spatial-data"></a>Leggere i dati spaziali

La `atlas.io.read` funzione è la funzione principale utilizzata per leggere formati di dati spaziali comuni come file KML, GPX, GeoRSS, GeoJSON e CSV con dati spaziali. Questa funzione può anche leggere le versioni compresse di questi formati, come un file zip o un file KM. Il formato di file KM è una versione compressa di KML che può includere anche risorse come immagini. In alternativa, la funzione di lettura può accettare un URL che punta a un file in uno di questi formati. Gli URL devono essere ospitati in un endpoint abilitato per CORS o nelle opzioni di lettura deve essere fornito un servizio proxy. Il servizio proxy viene utilizzato per caricare le risorse nei domini che non sono abilitati per CORS. La funzione di lettura restituisce una promessa di aggiungere le icone dell'immagine alla mappa ed elabora i dati in modo asincrono per ridurre al minimo l'impatto sul thread dell'interfaccia utente.

Durante la lettura di un file compresso, come zip o KM, verrà decompresso e analizzato per il primo file valido. Ad esempio, doc.kml o un file con altra estensione valida, ad esempio: .kml, .xml, geojson, .json, .csv, .tsv o .txt. Quindi, le immagini a cui viene fatto riferimento nei file KML e GeoRSS vengono precaricate per garantire che siano accessibili. I dati immagine inaccessibili possono caricare un'immagine di fallback alternativa o verranno rimossi dagli stili. Le immagini estratte dai file KM vengono convertite in URI di dati.

Il risultato della funzione `SpatialDataSet` di lettura è un oggetto. Questo oggetto estende la classe GeoJSON FeatureCollection. Può essere facilmente passato `DataSource` in un così com'è per eseguire il rendering delle sue feature su una mappa. Il `SpatialDataSet` non solo contiene informazioni sulle funzionalità, ma può anche includere sovrapposizioni di terra KML, metriche di elaborazione e altri dettagli, come descritto nella tabella seguente.

| Nome proprietà | Type | Descrizione | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Riquadro di delimitazione di tutti i dati nel set di dati. |
| `features` | `Feature[]` | Funzionalità GeoJSON all'interno del set di dati. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Matrice di KML GroundOverlays. |
| `icons` | Stringa&lt;di registrazione, stringa&gt; | Un set di URL di icone. Chiave, nome dell'icona, Valore e URL. |
| properties | any | Informazioni sulle proprietà fornite a livello di documento di un set di dati spaziali. |
| `stats` | `SpatialDataSetStats` | Statistiche sul contenuto e sul tempo di elaborazione di un set di dati spaziali. |
| `type` | `'FeatureCollection'` | Valore di tipo GeoJSON di sola lettura. |

## <a name="examples-of-reading-spatial-data"></a>Esempi di lettura di dati spaziali

Il codice seguente mostra come leggere un set di dati `SimpleDataLayer` spaziali ed eseguirne il rendering sulla mappa usando la classe . Il codice utilizza un file GPX a cui fa riferimento un URL.

<br/>

<iframe height='500' scrolling='no' title='Caricamento semplice dei dati spazialiLoad Spatial Data Simple' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la penna <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>di caricamento dei dati spaziali semplice</a> da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nella demo del codice successiva viene illustrato come leggere e caricare KML, o KM, sulla mappa. KML può contenere sovrapposizioni a terra, `ImageLyaer` che `OgcMapLayer`saranno sotto forma di un o . Queste sovrapposizioni devono essere aggiunte sulla mappa separatamente dalle feature. Inoltre, se il set di dati dispone di icone personalizzate, tali icone devono essere caricate nelle risorse di mapping prima del caricamento delle funzionalità.

<br/>

<iframe height='500' scrolling='no' title='Carica KML sulla mappa' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il carico penna <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>KML su</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>mappa di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Facoltativamente, è possibile fornire un servizio proxy per l'accesso alle risorse tra domini per i quali corS potrebbe non essere abilitato. La funzione di lettura tenterà di accedere ai file su un altro dominio utilizzando prima CORS. Dopo la prima volta non riesce ad accedere a qualsiasi risorsa su un altro dominio utilizzando CORS richiederà solo file aggiuntivi se è stato fornito un servizio proxy. La funzione di lettura aggiunge l'URL del file alla fine dell'URL del proxy fornito. Questo frammento di codice viene illustrato come passare un servizio proxy nella funzione di lettura:This snippet of code shows how to pass a proxy service into the read function:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

La demo seguente mostra come leggere un file delimitato ed eseguirne il rendering sulla mappa. In questo caso, il codice utilizza un file CSV con colonne di dati spaziali.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta di un file delimitato' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la penna Aggiungere un file<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>delimitato</a> da Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Scrivere dati spaziali

Ci sono due funzioni di scrittura principali nel modulo I/O spaziale. La `atlas.io.write` funzione genera una `atlas.io.writeCompressed` stringa, mentre la funzione genera un file zip compresso. Il file zip compresso conterrà un file basato su testo contenente i dati spaziali in esso contenuti. Entrambe queste funzioni restituiscono la promessa di aggiungere i dati al file. Entrambi possono scrivere uno dei seguenti `SpatialDataSet`dati: `ImageLayer` `OgcMapLayer`, `DataSource`, , , , la raccolta di feature, la feature, la geometria o una matrice di qualsiasi combinazione di questi tipi di dati. Quando si scrivono utilizzando una delle due funzioni, è possibile specificare il formato di file desiderato. Se il formato del file non è specificato, i dati verranno scritti come KML.

Lo strumento riportato di seguito illustra la maggior `atlas.io.write` parte delle opzioni di scrittura che possono essere utilizzate con la funzione.

<br/>

<iframe height='700' scrolling='no' title='Opzioni di scrittura dei dati spaziali' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere le opzioni di scrittura dei<a href='https://codepen.io/azuremaps'>@azuremaps</a>dati Pen <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Spatial</a> di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Esempio di scrittura di dati spaziali

L'esempio seguente consente di trascinare e rilasciare e quindi caricare i file spaziali sulla mappa. È possibile esportare i dati GeoJSON dalla mappa e scriverlo in uno dei formati di dati spaziali supportati come stringa o come file compresso.

<br/>

<iframe height='700' scrolling='no' title='Trascinare e rilasciare file spaziali sulla mappa' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>Drag and drop di file spaziali sulla mappa</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Facoltativamente, è possibile fornire un servizio proxy per l'accesso alle risorse tra domini per i quali corS potrebbe non essere abilitato. Questo frammento di codice mostra che è possibile incorporare un servizio proxy:This snippet of code shows you could incorporate a proxy service:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Lettura e scrittura di testo noto (WKT)

[Well-Known Text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) è uno standard Open Geospatial Consortium (OGC) per la rappresentazione delle geometrie spaziali come testo. Molti sistemi geospaziali supportano WKT, ad esempio SQL di Azure e Azure PostgreSQL usando il plug-in PostGIS. Come la maggior parte degli standard OGC, le coordinate sono formattate come "latitudine di longitudine" per allinearsi alla convenzione "x y". Ad esempio, un punto a longitudine -110 e `POINT(-110 45)` la latitudine 45 può essere scritto come utilizzando il formato WKT.

Il testo noto può `atlas.io.ogc.WKT.read` essere letto utilizzando `atlas.io.ogc.WKT.write` la funzione e scritto utilizzando la funzione.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Esempi di lettura e scrittura di testo noto (WKT)

Il codice seguente mostra come leggere la `POINT(-122.34009 47.60995)` stringa di testo nota ed eseguirne il rendering sulla mappa utilizzando un livello a bolle.

<br/>

<iframe height='500' scrolling='no' title='Leggi testo ben noto' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>testo noto</a> Pen Read<a href='https://codepen.io/azuremaps'>@azuremaps</a>di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice seguente vengono illustrate la lettura e la scrittura di testo noto avanti e indietro.

<br/>

<iframe height='700' scrolling='no' title='Lettura e scrittura di testi noti' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere La penna Lettura e scrittura di<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>testo noto</a> da Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Lettura e scrittura GML

GML è una specifica di file XML spaziale che viene spesso utilizzata come estensione ad altre specifiche XML. I dati GeoJSON possono essere scritti come `atlas.io.core.GmlWriter.write` XML con tag GML utilizzando la funzione. Il codice XML che contiene GML può essere letto utilizzando la `atlas.io.core.GmlReader.read` funzione . La funzione di lettura ha due opzioni:

- L'opzione `isAxisOrderLonLat` - L'ordine dell'asse delle coordinate "latitudine, longitudine" o "longitudine, latitudine" può variare da un set di dati all'altro e non è sempre ben definito. Per impostazione predefinita, il lettore GML legge i dati delle coordinate come "latitudine, longitudine", ma l'impostazione di questa opzione su true lo leggerà come "longitudine, latitudine".
- L'opzione `propertyTypes` - Questa opzione è una tabella di ricerca di valori chiave in cui la chiave è il nome di una proprietà nel set di dati. Il valore è il tipo di oggetto su cui eseguire il cast il valore durante l'analisi. I valori di `string`tipo `number` `boolean`supportati `date`sono: , , , e . Se una proprietà non è presente nella tabella di ricerca o il tipo non è definito, la proprietà verrà analizzata come stringa.

La `atlas.io.read` funzione utilizzerà `atlas.io.core.GmlReader.read` per impostazione predefinita la funzione quando rileva che i dati di input sono XML, ma i dati non sono uno degli altri formati XML spaziali di supporto.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [atlas.io funzioni statiche](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.io.ogc.WKT (funzioni)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Connettersi a un servizio WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Sfruttare le operazioni di base](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli sul formato dati supportati](spatial-io-supported-data-format-details.md)
