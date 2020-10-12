---
title: Lettura e scrittura dei dati spaziali | Mappe Microsoft Azure
description: Informazioni su come leggere e scrivere dati usando il modulo di i/o spaziale, fornito da Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: dd4a02ffdc062ed1940d35ca64e02a5e0a88a248
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333325"
---
# <a name="read-and-write-spatial-data"></a>Leggere e scrivere dati spaziali

Nella tabella seguente sono elencati i formati di file spaziali supportati per la lettura e la scrittura di operazioni con il modulo IO spaziale.

| Formato dati       | Lettura | Scrittura |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| CSV spaziale       | ✓  |  ✓  |
| Testo Well-Known   | ✓  |  ✓  |

Queste sezioni successive descrivono tutti i diversi strumenti per la lettura e la scrittura dei dati spaziali usando il modulo di i/o spaziale.

## <a name="read-spatial-data"></a>Lettura dei dati spaziali

La `atlas.io.read` funzione è la funzione principale usata per leggere i formati di dati spaziali comuni, ad esempio file KML, GPX, GeoRSS, GeoJSON e CSV con dati spaziali. Questa funzione può anche leggere le versioni compresse di questi formati come file zip o file KMZ. Il formato del file KMZ è una versione compressa di KML che può includere anche asset come le immagini. In alternativa, la funzione Read può assumere un URL che punta a un file in uno di questi formati. Gli URL devono essere ospitati in un endpoint abilitato per CORS o fornire un servizio proxy nelle opzioni di lettura. Il servizio proxy viene usato per caricare le risorse nei domini che non sono abilitati per CORS. La funzione Read restituisce un suggerimento per aggiungere le icone dell'immagine alla mappa ed elabora i dati in modo asincrono per ridurre al minimo l'effetto sul thread dell'interfaccia utente.

Quando si legge un file compresso, come zip o KMZ, questo verrà decompresso e analizzato per il primo file valido. Ad esempio, doc. KML o un file con altre estensioni valide, ad esempio:. KML,. XML, GeoJSON,. JSON,. csv,. TSV o. txt. Quindi, le immagini a cui viene fatto riferimento nei file KML e GeoRSS vengono precaricate per assicurarsi che siano accessibili. I dati di immagine inaccessibili possono caricare un'immagine di fallback alternativa o verranno rimossi dagli stili. Le immagini estratte dai file KMZ verranno convertite in URI di dati.

Il risultato della funzione Read è un `SpatialDataSet` oggetto. Questo oggetto estende la classe Featurecollection di GeoJSON. È possibile passare facilmente in un oggetto `DataSource` così come sono per eseguire il rendering delle relative funzionalità su una mappa. `SpatialDataSet`Non solo contiene informazioni sulle funzionalità, ma può includere anche sovrapposizioni di base KML, metriche di elaborazione e altri dettagli, come illustrato nella tabella seguente.

| Nome proprietà | Type | Descrizione | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Rettangolo di delimitazione di tutti i dati nel set di dati. |
| `features` | `Feature[]` | Funzionalità GeoJSON all'interno del set di dati. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Una matrice di GroundOverlays KML. |
| `icons` | &lt;Stringa di record, stringa&gt; | Set di URL di icone. Chiave = nome icona, valore = URL. |
| properties | any | Informazioni sulle proprietà fornite a livello di documento di un set di dati spaziali. |
| `stats` | `SpatialDataSetStats` | Statistiche relative al contenuto e al tempo di elaborazione di un set di dati spaziali. |
| `type` | `'FeatureCollection'` | Valore di tipo GeoJSON di sola lettura. |

## <a name="examples-of-reading-spatial-data"></a>Esempi di lettura dei dati spaziali

Nel codice seguente viene illustrato come leggere un set di dati spaziali ed eseguirne il rendering sulla mappa utilizzando la `SimpleDataLayer` classe. Il codice usa un file GPX a cui punta un URL.

<br/>

<iframe height='500' scrolling='no' title='Caricamento semplice dei dati spaziali' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa ai <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>dati spaziali di caricamento</a> delle penne semplice di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

La demo del codice seguente mostra come leggere e caricare il codice KML, o KMZ, nella mappa. Il linguaggio KML può contenere sovrapposizioni di base, che saranno sotto forma di `ImageLyaer` o `OgcMapLayer` . È necessario aggiungere tali sovrimpressioni sulla mappa separatamente dalle funzionalità. Inoltre, se il set di dati include icone personalizzate, queste icone devono essere caricate nelle risorse Maps prima del caricamento delle funzionalità.

<br/>

<iframe height='500' scrolling='no' title='Carica il formato KML nella mappa' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa al caricamento di un <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>KML nella mappa</a> da Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Facoltativamente, è possibile fornire un servizio proxy per l'accesso a risorse tra domini che potrebbero non essere abilitate per CORS. La funzione Read tenterà di accedere ai file in un altro dominio usando prima CORS. Dopo la prima volta che non riesce ad accedere a una risorsa in un altro dominio usando CORS, richiederà solo file aggiuntivi se è stato fornito un servizio proxy. La funzione Read accoda l'URL del file alla fine dell'URL del proxy fornito. Questo frammento di codice Mostra come passare un servizio proxy nella funzione Read:

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

La demo seguente mostra come leggere un file delimitato ed eseguirne il rendering nella mappa. In questo caso, il codice utilizza un file CSV con colonne di dati spaziali.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un file delimitato' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la penna <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>aggiungere un file delimitato</a> da mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Scrivere dati spaziali

Nel modulo di i/o spaziale sono presenti due funzioni di scrittura principali. La `atlas.io.write` funzione genera una stringa, mentre la `atlas.io.writeCompressed` funzione genera un file zip compresso. Il file zip compresso conterrebbe un file basato su testo con i dati spaziali. Entrambe queste funzioni restituiscono un suggerimento per aggiungere i dati al file. Entrambi possono scrivere i dati seguenti: `SpatialDataSet` ,, `DataSource` `ImageLayer` , `OgcMapLayer` , raccolta di funzionalità, funzionalità, geometria o una matrice di qualsiasi combinazione di questi tipi di dati. Quando si scrive usando entrambe le funzioni, è possibile specificare il formato di file desiderato. Se il formato del file non è specificato, i dati verranno scritti come KML.

Lo strumento seguente illustra la maggior parte delle opzioni di scrittura che è possibile usare con la `atlas.io.write` funzione.

<br/>

<iframe height='700' scrolling='no' title='Opzioni di scrittura dei dati spaziali' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere le <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Opzioni di scrittura dei dati spaziali</a> di Pen da mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Esempio di scrittura di dati spaziali

L'esempio seguente consente di trascinare e rilasciare i file spaziali sulla mappa. È possibile esportare i dati GeoJSON dalla mappa e scriverli in uno dei formati di dati spaziali supportati come stringa o come file compresso.

<br/>

<iframe height='700' scrolling='no' title='Trascinare e rilasciare i file spaziali nella mappa' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa al <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>trascinamento della selezione dei file spaziali su Map</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Facoltativamente, è possibile fornire un servizio proxy per l'accesso a risorse tra domini che potrebbero non essere abilitate per CORS. Questo frammento di codice mostra che è possibile incorporare un servizio proxy:

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

## <a name="read-and-write-well-known-text-wkt"></a>Leggere e scrivere Well-Known testo (WKT)

[Well-known text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) è uno standard Open Geospatial Consortium (OGC) per la rappresentazione di geometrie spaziali come testo. Molti sistemi geospaziali supportano WKT, ad esempio Azure SQL e Azure PostgreSQL, usando il plug-in PostGIS. Come la maggior parte degli standard OGC, le coordinate vengono formattate come "Latitudine Longitudine" per essere allineate con la convenzione "x y". Ad esempio, è possibile scrivere un punto alla Longitudine-110 e la latitudine 45 `POINT(-110 45)` usando il formato WKT.

Il testo noto può essere letto utilizzando la `atlas.io.ogc.WKT.read` funzione e scritto utilizzando la `atlas.io.ogc.WKT.write` funzione.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Esempi di lettura e scrittura Well-Known testo (WKT)

Nel codice seguente viene illustrato come leggere la stringa di testo Nota `POINT(-122.34009 47.60995)` ed eseguirne il rendering sulla mappa utilizzando un livello Bubble.

<br/>

<iframe height='500' scrolling='no' title='Leggi Well-Known testo' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa alla <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>lettura Well-Known testo</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice seguente viene illustrata la lettura e la scrittura di testo ben noto.

<br/>

<iframe height='700' scrolling='no' title='Leggere e scrivere Well-Known testo' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il testo della penna <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>lettura e scrittura Well-Known</a> da mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Leggi e Scrivi GML

GML è una specifica del file XML spaziale spesso utilizzata come estensione di altre specifiche XML. I dati GeoJSON possono essere scritti come XML con i tag GML usando la `atlas.io.core.GmlWriter.write` funzione. Il codice XML che contiene GML può essere letto utilizzando la `atlas.io.core.GmlReader.read` funzione. La funzione Read ha due opzioni:

- L' `isAxisOrderLonLat` opzione: l'ordine dell'asse delle coordinate "latitudine, Longitudine" o "Longitudine, latitudine" può variare tra i set di dati e non è sempre ben definito. Per impostazione predefinita, il lettore GML legge i dati delle coordinate come "latitudine, Longitudine", ma l'impostazione di questa opzione su true lo leggerà come "Longitudine, latitudine".
- `propertyTypes`Questa opzione è una tabella di ricerca del valore chiave in cui la chiave è il nome di una proprietà nel set di dati. Il valore è il tipo di oggetto su cui eseguire il cast del valore durante l'analisi. I valori di tipo supportati sono: `string` ,, `number` `boolean` e  `date` . Se una proprietà non è presente nella tabella di ricerca o il tipo non è definito, la proprietà verrà analizzata come stringa.

La funzione `atlas.io.read` utilizzerà per impostazione predefinita la `atlas.io.core.GmlReader.read` funzione quando rileva che i dati di input sono XML, ma i dati non sono uno degli altri formati XML spaziali di supporto.

Analizzerà le `GmlReader` coordinate che hanno uno dei seguenti SRID:

- EPSG: 4326 (scelta consigliata)
- EPSG: 4269, EPSG: 4283, EPSG: 4258, EPSG: 4308, EPSG: 4230, EPSG: 4272, EPSG: 4271, EPSG: 4267, EPSG: 4608, EPSG: 4674 eventualmente con un margine di errore ridotto.
- EPSG: 3857, EPSG: 102100, EPSG: 3785, EPSG: 900913, EPSG: 102113, EPSG: 41001, EPSG: 54004

## <a name="more-resources"></a>Altre risorse

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

[funzioni statiche atlas.io](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[funzioni Atlas. io. OGC. WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[Connettersi a un servizio WFS](spatial-io-connect-wfs-service.md)

[Sfruttare le operazioni principali](spatial-io-core-operations.md)

[Dettagli sul formato dati supportato](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

[Aggiungere un livello mappa OGC](spatial-io-add-ogc-map-layer.md)