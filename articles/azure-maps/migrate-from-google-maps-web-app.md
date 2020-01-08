---
title: Eseguire la migrazione di un'app Web da Google Maps | Microsoft Docs
description: Esercitazione su come eseguire la migrazione di un'app Web da Google Maps a mappe Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a414d7b15f81ab81783b66f8297a207afe569365
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562171"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Eseguire la migrazione di un'app Web da Google Maps

La maggior parte delle app Web che usano Google Maps usa Google Maps V3 JavaScript SDK. Azure Maps Web SDK è l'SDK adatto basato su Azure per la migrazione a. Web SDK di Mappe di Azure consente di personalizzare le mappe interattive con contenuto e immagini personali per la visualizzazione in applicazioni Web o per dispositivi mobili. Questo controllo usa WebGL, consentendo di eseguire il rendering di set di dati di grandi dimensioni con prestazioni elevate. Sviluppare con questo SDK usando JavaScript o TypeScript.

Se si esegue la migrazione di un'applicazione Web esistente, verificare se usa una libreria open source per il controllo della mappa, ad esempio cesio, volantino e OpenLayers. In caso contrario e non si vuole usare Azure Maps Web SDK, un'altra opzione per eseguire la migrazione dell'applicazione consiste nel continuare a usare il controllo mappa open source e connetterlo ai servizi Tile di mappe di Azure ([riquadri stradali](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [riquadri satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Di seguito sono riportate informazioni dettagliate su come usare le mappe di Azure in alcune librerie di controlli della mappa Open Source comunemente usate.

- Cesio: controllo mappa 3D per il Web. [Documentazione](https://cesiumjs.org/) \| di [esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS)
- Volantino: controllo mappa 2D leggero per il Web. [Documentazione](https://leafletjs.com/) \| di [esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS)
- OpenLayers: controllo mappa 2D per il Web che supporta le proiezioni. [Documentazione](https://openlayers.org/) \| di [esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers)

## <a name="key-features-support"></a>Supporto delle funzionalità principali

La tabella seguente elenca le principali funzionalità dell'API di Google Maps V3 JavaScript SDK e il supporto di un'API simile in Azure Maps Web SDK.

| Funzionalità di Google Maps     | Supporto di Azure Maps Web SDK |
|-------------------------|:--------------------------:|
| Markers                 | ✓                          |
| Clustering di marcatori       | ✓                          |
| Polilinee & poligoni    | ✓                          |
| Livelli dati             | ✓                          |
| Sovrapposizioni di base         | ✓                          |
| Mappe termiche               | ✓                          |
| Livelli affiancati             | ✓                          |
| Livello KML               | ✓                          |
| Strumenti di disegno           | ✓                          |
| Servizio geocodificatore        | ✓                          |
| Servizio directions      | ✓                          |
| Servizio matrice di distanza | ✓                          |
| Servizio di elevazione       | Pianificata                    |

## <a name="notable-differences-in-the-web-sdks"></a>Differenze rilevanti negli SDK Web

Di seguito sono riportate alcune delle principali differenze tra Google Maps e Azure Maps Web SDK da tenere presente:

- Oltre a fornire un endpoint ospitato per l'accesso ad Azure Maps Web SDK, è disponibile anche un pacchetto NPM per incorporare SDK Web nelle app, se preferibile. Per ulteriori informazioni, vedere questa [documentazione](how-to-use-map-control.md) . Questo pacchetto include anche le definizioni TypeScript.
- Dopo aver creato un'istanza della classe Map in mappe di Azure, il codice deve attendere l'attivazione delle mappe `ready` o dell'evento `load` prima di interagire con la mappa. In questo modo, tutte le risorse della mappa sono state caricate e sono pronte per l'accesso.
- Entrambe le piattaforme usano un sistema di affiancamento simile per le mappe di base, tuttavia i riquadri in Google Maps sono di 256 pixel nella dimensione mentre i riquadri in Azure Maps sono 512 pixel nella dimensione. Di conseguenza, per ottenere la stessa vista mappa in mappe di Azure come Google Maps, un livello di zoom usato in Google Maps deve essere sottratto da uno in Maps di Azure.
- Le coordinate in Google Maps sono denominate "latitudine, Longitudine" mentre Azure Maps USA "Longitudine, latitudine". Questo è allineato al `[x, y]` standard seguito dalla maggior parte delle piattaforme GIS.
- Le forme in Azure Maps Web SDK sono basate sullo schema GeoJSON. Le classi helper vengono esposte tramite lo [spazio dei nomi *Atlas. Data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). È disponibile anche l' [*Atlante. Classe Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) che può essere usata per eseguire il wrapping degli oggetti GeoJSON e semplificarne l'aggiornamento e la gestione in modo associabile ai dati.
- Le coordinate in mappe di Azure sono definite come oggetti position che possono essere specificati come una matrice di numeri semplice nel formato `[longitude, latitude]` o New Atlas. Data. Position (longitudine, latitudine).
    > [!TIP]
    > La classe Position dispone di un metodo helper statico per l'importazione di coordinate nel formato "latitudine, Longitudine". Il metodo [Atlas. Data. Position. fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) può spesso sostituire il metodo `new google.maps.LatLng` nel codice di Google Maps.
- Anziché specificare le informazioni di stile per ogni forma aggiunta alla mappa, Azure Maps separa gli stili dai dati. I dati vengono archiviati nelle origini dati e sono connessi ai livelli di rendering usati dal codice di Azure Maps per eseguire il rendering dei dati. Questo approccio offre un miglioramento delle prestazioni. Molti livelli supportano inoltre lo stile basato sui dati in cui è possibile aggiungere la logica di business alle opzioni di stile del livello che modificano il rendering delle singole forme all'interno di un livello in base alle proprietà definite nella forma.

## <a name="web-sdk-side-by-side-examples"></a>Esempi side-by-side di Web SDK

Di seguito è riportata una raccolta di esempi di codice per ogni piattaforma che copre i casi d'uso comuni per facilitare la migrazione dell'applicazione Web da Google Maps V3 JavaScript SDK ad Azure Maps Web SDK. Gli esempi di codice relativi alle applicazioni Web sono disponibili in JavaScript; Tuttavia, Azure Maps fornisce anche le definizioni TypeScript come opzione aggiuntiva tramite un [modulo NPM](how-to-use-map-control.md).

### <a name="load-a-map"></a>Caricare una mappa

Il caricamento di una mappa in entrambi gli SDK segue lo stesso set di passaggi:

- Aggiungere un riferimento a map SDK.
- Aggiungere un tag `div` al corpo della pagina che fungerà da segnaposto per la mappa.
- Creare una funzione JavaScript che viene chiamata quando la pagina è stata caricata.
- Creare un'istanza della rispettiva classe map.

**Alcune differenze principali**

- Google Maps richiede la specifica di una chiave dell'account nel riferimento allo script dell'API. Le credenziali di autenticazione per le mappe di Azure vengono specificate come opzioni della classe map. Può trattarsi di una chiave di sottoscrizione o di Azure Active Directory informazioni.
- Google Maps accetta una funzione di callback nel riferimento allo script dell'API, che viene usata per chiamare una funzione di inizializzazione per caricare la mappa. Con Azure Maps è necessario usare l'evento OnLoad della pagina.
- Quando si fa riferimento all'elemento `div` in cui verrà eseguito il rendering della mappa, la classe `Map` in Azure Maps richiede solo il valore `id` mentre Google Maps richiede un oggetto `HTMLElement`.
- Le coordinate in mappe di Azure sono definite come oggetti position che possono essere specificati come una matrice di numeri semplice nel formato `[longitude, latitude]`.
- Il livello di zoom in mappe di Azure è un livello inferiore rispetto all'esempio di Google Maps, a causa della differenza tra le dimensioni del sistema di affiancamento tra le piattaforme.
- Per impostazione predefinita, Maps di Azure non aggiunge alcun controllo di navigazione all'area di disegno della mappa, ad esempio pulsanti di zoom e pulsanti dello stile della mappa. Esistono tuttavia controlli per l'aggiunta di una selezione stile mappa, pulsanti zoom, bussola o controllo rotazione e un controllo pitch.
- Un gestore eventi viene aggiunto in mappe di Azure per monitorare l'evento `ready` dell'istanza della mappa. Questa operazione viene attivata quando la mappa ha completato il caricamento del contesto WebGL e di tutte le risorse necessarie. Il codice post-caricamento può essere aggiunto in questo gestore eventi.

Gli esempi seguenti illustrano come caricare una mappa di base in modo che sia centrata su New York alle coordinate (Longitudine:-73,985, Latitudine: 40,747) ed è al livello di zoom 12 in Google Maps.

**Prima: Google Maps**

Il codice seguente è un esempio di come visualizzare una mappa Google centrata e ingrandita in una posizione.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Eseguendo questo codice in un browser verrà visualizzata una mappa simile all'immagine seguente:

<center>

![semplici](media/migrate-google-maps-web-app/simple-google-map.png)</center> Google Maps

**Dopo: mappe di Azure**

Il codice seguente illustra come caricare una mappa con la stessa visualizzazione in mappe di Azure insieme a un controllo dello stile della mappa e ai pulsanti dello zoom.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Eseguendo questo codice in un browser verrà visualizzata una mappa simile all'immagine seguente:

<center>

![semplici mappe di Azure](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

La documentazione dettagliata su come configurare e usare il controllo mappa di Azure Maps in un'app Web è disponibile [qui](how-to-use-map-control.md).

> [!NOTE]
> A differenza di Google Maps, Azure Maps non richiede un livello iniziale di centramento e di zoom da specificare durante il caricamento della mappa. Se queste informazioni non vengono fornite durante il caricamento della mappa, la mappa tenterà di determinare la città in cui si trova l'utente e verrà centrata e ingrandita la mappa.

**Risorse aggiuntive:**

- Azure Maps fornisce anche controlli di navigazione per la rotazione e il pitching della vista mappa, come descritto [qui](map-add-controls.md).

### <a name="localizing-the-map"></a>Localizzazione della mappa

Se i destinatari sono distribuiti in più paesi o parlano lingue diverse, la localizzazione è importante.

**Prima: Google Maps**

Per localizzare Google Maps, i parametri relativi alla lingua e all'area vengono aggiunti a

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Di seguito è riportato un esempio di Google Maps con la lingua impostata su "fr-FR".

<center>

![](media/migrate-google-maps-web-app/google-maps-localization.png)</center> di localizzazione di Google Maps

**Dopo: mappe di Azure**

Azure Maps offre due modi diversi per impostare la lingua e la visualizzazione a livello di area della mappa. La prima opzione consiste nell'aggiungere queste informazioni allo spazio dei nomi dell' *Atlante* globale che comporterà l'impostazione predefinita di tutte le istanze del controllo mappa nell'app. Il codice seguente imposta la lingua in francese ("fr-FR") e la vista regionale su "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

La seconda opzione consiste nel passare queste informazioni nelle opzioni della mappa quando si carica la mappa, ad esempio:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Con Azure Maps è possibile caricare più istanze della mappa nella stessa pagina con impostazioni diverse per lingua e area geografica. Inoltre, è anche possibile aggiornare queste impostazioni nella mappa dopo che è stata caricata. Un elenco dettagliato delle lingue supportate in mappe di Azure è disponibile [qui](supported-languages.md).

Di seguito è riportato un esempio di mappe di Azure con la lingua impostata su "fr" e l'area utente impostata su "fr-FR".

<center>

![di localizzazione di Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Impostazione della vista mappa

Le mappe dinamiche in Azure e Google Maps possono essere spostate a livello di codice in nuove posizioni geografiche chiamando le funzioni appropriate in JavaScript. Gli esempi seguenti illustrano come fare in modo che la mappa visualizzi immagini aeree satellite, centrare la mappa su una posizione con coordinate (Longitudine:-111,0225, Latitudine: 35,0272) e impostare il livello di zoom su 15 in Google Maps.

> [!NOTE]
> Google Maps usa riquadri con dimensioni di 256 pixel mentre Maps di Azure usa un riquadro di 512 pixel più grande. In questo modo si riduce il numero di richieste di rete richieste da mappe di Azure per caricare la stessa area mappa di Google Maps. Tuttavia, a causa del modo in cui le piramidi dei riquadri funzionano nei controlli mappa, i riquadri più grandi in mappe di Azure consentono di ottenere la stessa area visualizzabile di una mappa in Google Maps, quando si usano le mappe di Azure.

**Prima: Google Maps**

Il controllo mappa di Google Maps può essere spostato a livello di codice usando il metodo `setOptions`, che consente di specificare il centro della mappa e un livello di zoom.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![visualizzazione set di Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Dopo: mappe di Azure**

In mappe di Azure la posizione della mappa può essere modificata a livello di codice usando il metodo `setCamera` della mappa e la modifica dello stile della mappa viene modificata usando il metodo `setStyle`. Si noti che le coordinate in mappe di Azure sono in formato "Longitudine, latitudine" e il valore del livello di zoom viene sottratto di uno.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![visualizzazione set di mappe di Azure](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Risorse aggiuntive:**

- [Scegliere uno stile mappa](choose-map-style.md)
- [Stili mappa supportati](supported-map-styles.md)

### <a name="adding-a-marker"></a>Aggiunta di un marcatore

In mappe di Azure esistono diversi modi in cui è possibile eseguire il rendering dei dati punto sulla mappa.

- **Marcatori HTML** : esegue il rendering dei punti usando gli elementi DOM tradizionali. I marcatori HTML supportano il trascinamento.
- **Livello simboli** : esegue il rendering dei punti con un'icona e/o un testo all'interno del contesto WebGL.
- **Livello bolla** : esegue il rendering dei punti come cerchi sulla mappa. I raggi dei cerchi possono essere ridimensionati in base alle proprietà nei dati.

Viene eseguito il rendering dei livelli symbol e Bubble nel contesto WebGL e sono in grado di eseguire il rendering di set di punti molto grandi sulla mappa. Per questi livelli è necessario archiviare i dati in un'origine dati. È necessario aggiungere le origini dati e i livelli di rendering alla mappa dopo che è stato generato l'evento `ready`. I marcatori HTML vengono sottoposti a rendering come elementi DOM all'interno della pagina e non utilizzano un'origine dati. Maggiore è il numero di elementi DOM della pagina, minore sarà la pagina. Se si esegue il rendering di più di un centinaio di punti su una mappa, è consigliabile usare invece uno dei livelli di rendering.

Gli esempi seguenti aggiungono un marcatore alla mappa in (Longitudine:-0,2, Latitudine: 51,5) con il numero 10 sovrapposto come etichetta.

**Prima: Google Maps**

Con Google Maps, i marcatori vengono aggiunti alla mappa utilizzando la classe `google.maps.Marker` e specificando la mappa come una delle opzioni.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![marcatore di Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Dopo: mappe di Azure con marcatori HTML**

Nelle mappe di Azure, i marcatori HTML possono essere usati per visualizzare un punto sulla mappa e sono consigliati per le semplici app che devono solo visualizzare un numero ridotto di punti sulla mappa. Per usare un marcatore HTML, è sufficiente creare un'istanza della classe `atlas.HtmlMarker`, impostare le opzioni relative al testo e alla posizione e aggiungere il marcatore alla mappa usando il metodo `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![marcatore HTML di mappe di Azure](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Dopo: mappe di Azure con un livello di simboli**

Quando si usa un livello di simboli, è necessario aggiungere i dati a un'origine dati e all'origine dati associata al livello. Inoltre, l'origine dati e il livello devono essere aggiunti alla mappa dopo che è stato generato l'evento `ready`. Per eseguire il rendering di un valore di testo univoco sopra un simbolo, è necessario archiviare le informazioni di testo come proprietà del punto dati e della proprietà a cui si fa riferimento nell'opzione `textField` del livello. Si tratta di un po' più di lavoro rispetto all'uso di marcatori HTML, ma offre molti vantaggi in merito alle prestazioni

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![livello di simboli di mappe di Azure](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Risorse aggiuntive:**

- [Creare un'origine dati](create-data-source-web-sdk.md)
- [Aggiungere un livello di simbolo](map-add-pin.md)
- [Aggiungere un livello Bubble](map-add-bubble-layer.md)
- [Dati punto cluster](clustering-point-data-web-sdk.md)
- [Aggiungere marcatori HTML](map-add-custom-html.md)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)
- [Opzioni icona livello simboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opzione testo del livello simboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe marcatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opzioni marcatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Aggiunta di un marcatore personalizzato

Le immagini personalizzate possono essere usate per rappresentare punti su una mappa. Negli esempi seguenti viene usata l'immagine seguente: usare un'immagine personalizzata per visualizzare un punto sulla mappa in (Latitudine: 51,5, Longitudine:-0,2) e sfalsare la posizione del marcatore in modo che il punto dell'icona a forma di puntina da disegno sia allineato alla posizione corretta sulla mappa.

<center>

![immagine con puntina da disegno giallo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_la puntina da disegno. png</center>

**Prima: Google Maps**

In Google Maps viene creato un marcatore personalizzato specificando un oggetto `Icon` contenente i `url` all'immagine, un punto di `anchor` per allineare il punto dell'immagine della puntina da disegno alla coordinata sulla mappa. Il valore di ancoraggio in Google Maps rispetto all'angolo superiore sinistro dell'immagine.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![marcatore personalizzato di Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Dopo: mappe di Azure con marcatori HTML**

Per personalizzare un marcatore HTML in Maps di Azure, è possibile passare un `string` o `HTMLElement` HTML nell'opzione `htmlContent` del marcatore. In mappe di Azure viene usata un'opzione di `anchor` per specificare la posizione relativa del marcatore rispetto alla coordinata di posizione usando uno dei nove punti di riferimento definiti; "Center", "Top", "bottom", "left", "Right", "top-left", "top-right", "bottom-left", "bottom-right". Per impostazione predefinita, il contenuto è ancorato al centro inferiore del contenuto HTML. Per semplificare la migrazione del codice da Google Maps, impostare il `anchor` su "in alto a sinistra" e quindi usare l'opzione `pixelOffset` con lo stesso offset usato in Google Maps. Gli offset in Azure Maps si spostano nella direzione opposta di Google Maps, quindi moltiplicarli per meno uno.

> [!TIP]
> Aggiungere `pointer-events:none` come stile nel contenuto HTML per disabilitare il comportamento di trascinamento predefinito in Microsoft Edge che visualizzerà un'icona indesiderata.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure mapping](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center> marcatore HTML personalizzato

**Dopo: mappe di Azure con un livello di simboli**

I livelli dei simboli nelle mappe di Azure supportano anche immagini personalizzate, ma è necessario caricare prima l'immagine nelle risorse della mappa e assegnare un ID univoco. Il livello dei simboli può quindi fare riferimento a questo ID. Il simbolo può essere sfalsato per essere allineato al punto corretto sull'immagine usando l'opzione icona `offset`. In mappe di Azure viene usata un'opzione di `anchor` per specificare la posizione relativa del simbolo rispetto alla coordinata di posizione usando uno dei nove punti di riferimento definiti. "Center", "Top", "bottom", "left", "Right", "top-left", "top-right", "bottom-left", "bottom-right". Per impostazione predefinita, il contenuto è ancorato al centro inferiore del contenuto HTML. Per semplificare la migrazione del codice da Google Maps, impostare il `anchor` su "in alto a sinistra" e quindi usare l'opzione `offset` con lo stesso offset usato in Google Maps. Gli offset in Azure Maps si spostano nella direzione opposta di Google Maps, quindi moltiplicarli per meno uno.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![livello del simbolo dell'icona personalizzata di Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Per creare un rendering personalizzato avanzato dei punti, usare più livelli di rendering insieme. Ad esempio, se si vogliono avere più puntine da disegno con la stessa icona su cerchi colorati diversi, anziché creare un gruppo di immagini per ogni colore sovrapposto a un livello di simbolo su un livello di bolla e fare in modo che facciano riferimento alla stessa origine dati. Questa operazione sarà molto più efficiente rispetto alla creazione e la mappa manterrà una serie di immagini diverse.

**Risorse aggiuntive:**

- [Creare un'origine dati](create-data-source-web-sdk.md)
- [Aggiungere un livello di simbolo](map-add-pin.md)
- [Aggiungere marcatori HTML](map-add-custom-html.md)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)
- [Opzioni icona livello simboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opzione testo del livello simboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe marcatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opzioni marcatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Aggiunta di una polilinea

Le polilinee vengono usate per rappresentare una linea o un tracciato sulla mappa. Negli esempi seguenti viene illustrato come creare una polilinea tratteggiata sulla mappa.

**Prima: Google Maps**

In Google Maps la classe polilinea accetta un set di opzioni. Viene passata una matrice di coordinate nell'opzione `path` della polilinea.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![linea di Google Maps](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Dopo: mappe di Azure**

Nelle mappe di Azure le polilinee sono denominate oggetti LineString o MultiLineString. Questi oggetti possono essere aggiunti a un'origine dati e sottoposti a rendering utilizzando un livello linea.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center> polilinea di Azure Maps

**Risorse aggiuntive:**

- [Aggiungere righe alla mappa](map-add-line-layer.md)
- [Opzioni livello linea](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Aggiunta di un poligono

I poligoni vengono usati per rappresentare un'area sulla mappa. Mappe di Azure e Google Maps forniscono supporto molto simile per i poligoni. Gli esempi seguenti illustrano come creare un poligono che formi un triangolo basato sulla coordinata centrale della mappa.

**Prima: Google Maps**

In Google Maps la classe Polygon accetta un set di opzioni. Viene passata una matrice di coordinate nell'opzione `paths` del poligono.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![](media/migrate-google-maps-web-app/google-maps-polygon.png)</center> poligono di Google Maps

**Dopo: mappe di Azure**

Nelle mappe di Azure, gli oggetti Polygon e MultiPolygon possono essere aggiunti a un'origine dati e sottoposti a rendering sulla mappa usando i livelli. L'area di un poligono può essere sottoposta a rendering in un livello poligono. Il rendering del contorno di un poligono può essere eseguito usando un livello linea.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![poligono di Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Risorse aggiuntive:**

- [Aggiungere un poligono alla mappa](map-add-shape.md)
- [Aggiungere un cerchio alla mappa](map-add-shape.md#add-a-circle-to-the-map)
- [Opzioni livello poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Opzioni livello linea](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Visualizzare una finestra informazioni

Le informazioni aggiuntive per un'entità possono essere visualizzate sulla mappa come classe `google.maps.InfoWindow` in Google Maps, in Azure maps. questa operazione può essere eseguita usando la classe `atlas.Popup`. Negli esempi seguenti viene aggiunto un marcatore alla mappa e quando si fa clic su Visualizza una finestra informazioni/popup.

**Prima: Google Maps**

Con Google Maps, viene creata una finestra informazioni usando il costruttore `google.maps.InfoWindow`.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![popup di Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Dopo: mappe di Azure**

In mappe di Azure è possibile usare un popup per visualizzare informazioni aggiuntive per una località. È possibile passare un oggetto `string` o `HTMLElement` HTML nell'opzione `content` del popup. Se lo si desidera, è possibile visualizzare i popup indipendentemente da qualsiasi forma, quindi è necessario specificare un valore `position`. Per visualizzare un popup, chiamare il metodo `open` e passare la `map` in cui deve essere visualizzata la finestra popup.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![popup di Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Per eseguire la stessa operazione con un simbolo, una bolla, una linea o un livello poligono, è sufficiente passare il livello al codice dell'evento Maps invece che a un marcatore.

**Risorse aggiuntive:**

- [Aggiungere un popup](map-add-popup.md)
- [Popup con contenuto multimediale](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Popup sulle forme](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Riutilizzo di popup con più pin](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Classe popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Opzioni popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importare un file GeoJSON

Google Maps supporta il caricamento e l'applicazione dinamica di stili ai dati GeoJSON tramite la classe `google.maps.Data`. La funzionalità di questa classe è più allineata con lo stile basato sui dati delle mappe di Azure. Una differenza fondamentale consiste nel fatto che con Google Maps si specifica una funzione di callback e la logica di business per applicare lo stile a ogni funzionalità elaborata singolarmente nel thread dell'interfaccia utente. In mappe di Azure, i livelli supportano la specifica di espressioni basate sui dati come opzioni di stile. Queste espressioni vengono elaborate in fase di rendering in un thread separato e offrono prestazioni di rendering maggiori e consentono il rendering più rapido dei set di dati più grandi.

Gli esempi seguenti caricano un feed GeoJSON di tutti i terremoti negli ultimi sette giorni da USGS e li eseguono il rendering come cerchi ridimensionati sulla mappa. Il colore e la scala di ciascun cerchio si basano sulla grandezza di ogni terremoto archiviato nella proprietà `"mag"` di ogni funzionalità del set di dati. Se la grandezza è maggiore o uguale a cinque, il cerchio sarà rosso, se maggiore o uguale a tre ma minore di cinque, il cerchio sarà arancione, se inferiore a tre, il cerchio sarà verde. Il raggio di ogni cerchio sarà l'esponenziale della magnitude moltiplicato per 0,1.

**Prima: Google Maps**

In Google Maps è possibile specificare una singola funzione di callback nel metodo `map.data.setStyle`, che verrà usato per applicare la logica di business a ogni funzionalità caricata dal feed GeoJSON tramite il metodo `map.data.loadGeoJson`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Dopo: mappe di Azure**

GeoJSON è il tipo di dati di base in Maps di Azure e può essere facilmente importato in un'origine dati usando il metodo `datasource.importFromUrl`. Un livello Bubble fornisce funzionalità per il rendering di cerchi ridimensionati in base alle proprietà delle funzionalità di un'origine dati. Anziché disporre di una funzione di callback, la logica di business viene convertita in un'espressione e passata nelle opzioni di stile. Le espressioni definiscono il funzionamento della logica di business in modo che possa essere passato in un altro thread e valutato in base ai dati della funzionalità. È possibile aggiungere più origini dati e livelli a mappe di Azure, ognuno con una logica di business diversa, consentendo così il rendering di più set di dati sulla mappa in modi diversi.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Risorse aggiuntive:**

- [Aggiungere un livello di simbolo](map-add-pin.md)
- [Aggiungere un livello Bubble](map-add-bubble-layer.md)
- [Dati punto cluster](clustering-point-data-web-sdk.md)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clustering di marcatori

Quando si visualizzano molti punti dati sulla mappa, i punti si sovrappongono, la mappa sembra disordinata e diventa difficile da visualizzare e usare. Il clustering dei dati punto può essere usato per migliorare l'esperienza utente e migliorare le prestazioni. I dati del punto di clustering sono il processo di combinazione di dati punto vicini tra loro e che li rappresentano sulla mappa come singolo punto dati del cluster. Quando l'utente esegue lo zoom avanti sulla mappa, i cluster si suddividono nei singoli punti dati.

Gli esempi seguenti caricano un feed GeoJSON dei dati sismici della settimana precedente e lo aggiungono alla mappa. I cluster vengono sottoposti a rendering come cerchi ridimensionati e colorati a seconda del numero di punti in essi contenuti.

> [!NOTE]
> Esistono diversi algoritmi usati per il clustering dei marcatori. Google e Azure Maps usano algoritmi leggermente diversi. Di conseguenza, talvolta la distribuzione dei punti nei cluster può variare.

**Prima: Google Maps**

È possibile raggruppare i marcatori di Google Maps caricando nella libreria MarkerClusterer. Le icone del cluster sono limitate alle immagini che hanno i numeri da uno a cinque come nome e sono ospitate nella stessa directory.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![clustering di Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Dopo: mappe di Azure**

In mappe di Azure i dati vengono aggiunti e gestiti da un'origine dati. I livelli si connettono alle origini dati ed eseguono il rendering dei dati in essi contenuti. La classe `DataSource` in Azure Maps fornisce diverse opzioni di clustering.

- `cluster`: indica all'origine dati di raggruppare i dati del punto.
- `clusterRadius`: raggio in pixel tra i punti del cluster.
- `clusterMaxZoom`: il livello di zoom massimo in cui si verifica il clustering. Se si esegue lo zoom avanti, viene eseguito il rendering di tutti i punti come simboli.
- `clusterProperties`-definisce le proprietà personalizzate che vengono calcolate mediante espressioni su tutti i punti all'interno di ogni cluster e aggiunte alle proprietà di ogni punto del cluster.

Quando il clustering è abilitato, l'origine dati invierà i punti dati cluster e non cluster ai livelli per il rendering. L'origine dati è in grado di raggruppare centinaia di migliaia di punti dati. Un punto dati cluster presenta le proprietà seguenti:

| Nome proprietà             | Tipo    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se la funzionalità rappresenta un cluster. |
| `cluster_id`              | string  | ID univoco per il cluster che può essere utilizzato con i metodi DataSource `getClusterExpansionZoom`, `getClusterChildren`e `getClusterLeaves`. |
| `point_count`             | d'acquisto  | Numero di punti contenuti nel cluster.  |
| `point_count_abbreviated` | string  | Stringa che abbrevia il valore `point_count` se è lungo. (ad esempio, 4.000 diventa 4K)  |

La classe `DataSource` dispone della funzione helper seguente per accedere a informazioni aggiuntive su un cluster usando il `cluster_id`.

| Metodo | Tipo restituito | Description |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;matrice&lt;funzionalità&lt;geometria, qualsiasi&gt; \| forma&gt; | Recupera gli elementi figlio del cluster specificato al livello di zoom successivo. Questi elementi figlio possono essere costituiti da una combinazione di forme e sottocluster. I sottocluster saranno funzionalità con proprietà corrispondenti a ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;numero&gt; | Calcola un livello di zoom in corrispondenza del quale il cluster inizierà ad espandersi o suddividere. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;matrice&lt;funzionalità&lt;geometria, qualsiasi&gt; \| forma&gt; | Recupera tutti i punti in un cluster. Impostare il `limit` per restituire un subset dei punti e usare il `offset` per eseguire la pagina nei punti. |

Quando si esegue il rendering dei dati cluster sulla mappa, spesso è più semplice usare due o più livelli. L'esempio seguente usa tre livelli, un livello Bubble per il disegno di cerchi colorati ridimensionati in base alle dimensioni dei cluster, un livello di simboli per il rendering delle dimensioni del cluster come testo e un secondo livello di simbolo per il rendering dei punti non cluster. Sono disponibili molti altri modi per eseguire il rendering dei dati cluster nelle mappe di Azure evidenziati nella documentazione relativa ai [dati dei punti del cluster](clustering-point-data-web-sdk.md) .

I dati GeoJSON possono essere importati direttamente in mappe di Azure usando la funzione `importDataFromUrl` sulla classe `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![il clustering di Azure Maps](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Risorse aggiuntive:**

- [Aggiungere un livello di simbolo](map-add-pin.md)
- [Aggiungere un livello Bubble](map-add-bubble-layer.md)
- [Dati punto cluster](clustering-point-data-web-sdk.md)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Aggiungere una mappa termica

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati utilizzate per rappresentare la densità dei dati tramite una gamma di colori. Vengono spesso usate per visualizzare le "aree sensibili" dei dati su una mappa e sono un ottimo modo per eseguire il rendering di set di dati di punti di grandi dimensioni.

Gli esempi seguenti caricano un feed GeoJSON di tutti i terremoti nel mese precedente da USGS e li eseguono il rendering come mappa termica ponderata in cui la proprietà `"mag"` viene utilizzata come peso.

**Prima: Google Maps**

In Google Maps, per creare una mappa termica, è necessario caricare la libreria di "visualizzazione" aggiungendo `&libraries=visualization` all'URL dello script dell'API. Il livello mappa termica in Google Maps non supporta direttamente i dati GeoJSON, ma i dati devono essere prima scaricati e convertiti in una matrice di punti dati ponderati.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![mappa termica di Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Dopo: mappe di Azure**

In mappe di Azure caricare i dati GeoJSON in un'origine dati e connettere l'origine dati a un livello mappa termica. La proprietà che verrà usata per il peso può essere passata nell'opzione `weight` usando un'espressione. I dati GeoJSON possono essere importati direttamente in mappe di Azure usando la funzione `importDataFromUrl` sulla classe `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

Mappa termica ![mappe di Azure](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Risorse aggiuntive:**

- [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)
- [Classe livello mappa termica](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Opzioni del livello mappa termica](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Sovrapposizione di un livello sezione

I livelli affiancati, noti anche come sovrimpressioni delle immagini in Google Maps, consentono di sovrapporre immagini di grandi dimensioni suddivise in immagini affiancate più piccole allineate al sistema di affiancamento delle mappe. Si tratta di un modo comune per sovrapporre immagini di grandi dimensioni o set di dati di grandi dimensioni.

Gli esempi seguenti sovrappongono un livello sezione del radar meteorologico da Iowa Environmental Mesont of Iowa State University.

**Prima: Google Maps**

In Google Maps è possibile creare livelli di sezione usando la classe `google.maps.ImageMapType`.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![livello sezione di Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Dopo: mappe di Azure**

Nelle mappe di Azure è possibile aggiungere un livello sezione alla mappa in modo analogo a qualsiasi altro livello. URL formattato con i segnaposto x, y e zoom; `{x}`, `{y}`, `{z}` viene usato rispettivamente per indicare al livello dove accedere ai riquadri. I livelli tessera di Azure Maps supportano anche `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}` segnaposto.

> [!TIP]
> Nei livelli mappe di Azure è possibile eseguire facilmente il rendering sotto altri livelli, inclusi i livelli mappa di base. Spesso è consigliabile eseguire il rendering dei livelli affiancati sotto le etichette della mappa in modo che siano facili da leggere. Il metodo `map.layers.add` accetta un secondo parametro che è l'ID del livello in cui inserire il nuovo livello sotto. Per inserire un livello sezione sotto le etichette della mappa, è possibile usare il codice seguente: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![livello tessera di Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> È possibile acquisire le richieste dei riquadri utilizzando l'opzione `transformRequest` della mappa. Ciò consentirà di modificare o aggiungere intestazioni alla richiesta, se necessario.

**Risorse aggiuntive:**

- [Aggiungi livelli sezione](map-add-tile-layer.md)
- [Classe livello sezione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Opzioni del livello sezione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Mostra il traffico

I dati relativi al traffico possono essere sovrapposti ad Azure e a Google Maps.

**Prima: Google Maps**

Nei dati di Google Maps il traffico può essere sovrapposto alla mappa usando il livello di traffico.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![il traffico di Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Dopo: mappe di Azure**

Azure Maps offre diverse opzioni per la visualizzazione del traffico. Gli eventi imprevisti del traffico, come le chiusure e gli incidenti stradali, possono essere visualizzati come icone sulla mappa. Il flusso del traffico, le strade con codifica a colori, possono essere sovrapposti sulla mappa e i colori possono essere modificati in base al limite di velocità inviato, in relazione al ritardo previsto normale o al ritardo assoluto. I dati relativi agli eventi imprevisti nelle mappe di Azure vengono aggiornati ogni minuto e i dati di flusso ogni due minuti.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps Traffic](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Se si fa clic su una delle icone di traffico in mappe di Azure, vengono visualizzate informazioni aggiuntive in un popup.

<center>

![evento imprevisto del traffico di mappe di Azure](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Risorse aggiuntive:**

- [Mostrare il traffico sulla mappa](map-show-traffic.md)
- [Opzioni di sovrapposizione del traffico](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Aggiungere una sovrapposizione di base

Sia Azure che Google Maps supportano la sovrapposizione di immagini geografiche sulla mappa, in modo che vengano spostate e ridimensionate quando si esegue il panning e lo zoom della mappa. In Google Maps queste sono note come sovrapposizioni di base mentre in Azure Maps sono definite livelli di immagine. Si tratta di un'ottima soluzione per la creazione di piani di piano, la sovrapposizione di vecchie mappe o immagini da un drone.

**Prima: Google Maps**

Quando si crea una sovrapposizione di base in Google Maps, è necessario specificare l'URL dell'immagine da sovrapporre e un rettangolo di delimitazione per associare l'immagine alla mappa. Questo esempio sovrappone un'immagine mappa di [Newark New Jersey da 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) sulla mappa.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Eseguendo questo codice in un browser verrà visualizzata una mappa simile all'immagine seguente:

<center>

![sovrapposizione immagini di Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Dopo: mappe di Azure**

Nelle mappe di Azure le immagini georeferenziate possono essere sovrapposte usando la classe `atlas.layer.ImageLayer`. Questa classe richiede un URL di un'immagine e un set di coordinate per i quattro angoli dell'immagine. L'immagine deve essere ospitata nello stesso dominio o avere abilitato CORs.

> [!TIP]
> Se sono presenti solo le informazioni a nord, Sud, est, ovest e di rotazione e non le coordinate per ogni angolo dell'immagine, è possibile usare il metodo statico [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) .

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![sovrapposizione immagini di Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Risorse aggiuntive:**

- [Sovrapporre un'immagine](map-add-image-layer.md)
- [Classe livello immagine](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Esempi di codice aggiuntivi

Di seguito sono riportati alcuni esempi di codice aggiuntivi relativi alla migrazione di Google Maps:

- [Strumenti di disegno](map-add-drawing-toolbar.md)
- [Limitare il mapping a due panning del dito](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Limita zoom rotellina di scorrimento](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Creare un controllo fullscreen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Servizi:**

- [Uso del modulo servizi di mappe di Azure](how-to-use-services-module.md)
- [Cercare i punti di interesse](map-search-location.md)
- [Ottenere informazioni da una coordinata (geocodifica inversa)](map-get-information-from-coordinate.md)
- [Visualizzare le indicazioni stradali da A a B](map-route.md)
- [Cerca suggerimenti automatici con l'interfaccia utente di JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapping delle classi di Google Maps V3 ad Azure Maps

L'appendice seguente fornisce un mapping tra riferimenti incrociati delle classi più diffuse in Google Maps V3 ai rispettivi equivalenti di Azure Maps Web SDK.

### <a name="core-classes"></a>Classi principali

| Google Maps   | Mappe di Azure  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlas. Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlas. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. Data. Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [Atlante. Data. BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlas. CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlas. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlas. ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlas. StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlas. UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlas. Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Classi sovrapposte

| Google Maps  | Mappe di Azure  |
|--------------|-------------|
| `google.maps.Marker` | [Atlas. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[Atlante. Data. Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlas. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlas. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlas. TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlas. BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [Atlante. Data. Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlas. PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [Atlante. Data. LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Vedere [aggiungere un cerchio alla mappa](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlas. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Classi di servizio

Azure Maps Web SDK include un [modulo dei servizi](how-to-use-services-module.md) che può essere caricato separatamente. Questo modulo esegue il wrapping dei servizi REST di Azure Maps con un'API Web e può essere usato nelle applicazioni JavaScript, TypeScript e node. js.

| Google Maps | Mappe di Azure  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlante. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlas. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [Atlante. Service. RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlas. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [Atlante. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Librerie

Le librerie aggiungono funzionalità aggiuntive alla mappa. Molti di questi si trovano nell'SDK di base di Azure maps. Di seguito sono riportate alcune classi equivalenti da usare al posto di queste librerie di Google Maps

| Google Maps           | Mappe di Azure   |
|-----------------------|--------------|
| Libreria di disegno       | [Modulo Strumenti di disegno](set-drawing-options.md) |
| Libreria Geometry      | [Atlante. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Libreria di visualizzazione | [Livello mappa termica](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Come usare il controllo mappa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Come usare il modulo Servizi](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Come usare il modulo strumenti di disegno](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)

