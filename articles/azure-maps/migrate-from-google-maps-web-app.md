---
title: "Esercitazione: Eseguire la migrazione di un'app Web da Google Maps | Mappe di Microsoft Azure"
description: Come eseguire la migrazione di un'app Web da Google Maps a Mappe di Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 67f9168d2b18a98850588554f77c4a5859f365df
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086425"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Eseguire la migrazione di un'app Web da Google Maps

La maggior parte delle app Web che usa Google Maps usa Google Maps V3 JavaScript SDK. Azure Maps Web SDK è l'SDK basato su Azure idoneo per la migrazione. Azure Maps Web SDK consente di personalizzare le mappe interattive con contenuto e immagini personali per eseguire l'app sia nelle applicazioni Web o per dispositivi mobili. Questo controllo usa WebGL, consentendo di eseguire il rendering di set di dati di grandi dimensioni con prestazioni elevate. Sviluppare con questo SDK usando JavaScript o TypeScript.

Se si esegue la migrazione di un'applicazione Web esistente, verificare se usa una libreria di controllo mappa open source. Alcuni esempi di libreria open source per il controllo mappa sono: Cesium, Leaflet e OpenLayers. Se l'applicazione usa una libreria open source per il controllo mappa e non si vuole usare Azure Maps Web SDK, è comunque possibile eseguire la migrazione. In questo caso, connettere l'applicazione ai servizi tessere di Mappe di Azure ([tessere stradali](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [tessere satellitari](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). I punti seguenti illustrano in dettaglio come usare Mappe di Azure in alcune librerie di controllo mappa open source comunemente usate.

- Cesium: controllo mappa 3D per il Web. [Esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentazione](https://cesiumjs.org/)
- Leaflet: controllo mappa 2D leggero per il Web. [Esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentazione](https://leafletjs.com/)
- OpenLayers: controllo mappa 2D per il Web che supporta le proiezioni. [Esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentazione](https://openlayers.org/)

## <a name="key-features-support"></a>Supporto delle funzionalità principali

La tabella elenca le principali funzionalità dell'API di Google Maps V3 JavaScript SDK e la funzionalità API supportata in Azure Maps Web SDK.

| Funzionalità di Google Maps     | Supporto di Azure Maps Web SDK |
|-------------------------|:--------------------------:|
| Indicatori                 | ✓                          |
| Clustering di indicatori       | ✓                          |
| Polilinee e poligoni    | ✓                          |
| Livelli dati             | ✓                          |
| Overlay sul terreno         | ✓                          |
| Mappe termiche               | ✓                          |
| Livelli tessera             | ✓                          |
| Livello KLM               | ✓                          |
| Strumenti di disegno           | ✓                          |
| Servizio di geocodifica        | ✓                          |
| Servizio Indicazioni      | ✓                          |
| Servizio Matrice di distanze | ✓                          |
| Servizio Elevazione       | Pianificata                    |

## <a name="notable-differences-in-the-web-sdks"></a>Differenze significative tra i Web SDK

Di seguito sono riportate alcune delle principali differenze tra i Web SDK di Google Maps e Mappe di Azure da tenere presente:

- Oltre a fornire un endpoint ospitato per l'accesso ad Azure Maps Web SDK, è disponibile un pacchetto NPM per incorporare il pacchetto Web SDK nelle app. Per altre informazioni, vedere questa [documentazione](how-to-use-map-control.md). Questo pacchetto include anche le definizioni TypeScript.
- È prima necessario creare un'istanza della classe Map in Mappe di Azure. Attendere che l'evento `ready` o `load` delle mappe venga attivato prima di interagire a livello di codice con la mappa. Questo ordine garantisce che tutte le risorse della mappa siano state caricate e siano pronte per l'accesso.
- Entrambe le piattaforme usano un sistema a tessere simile per le mappe di base. In Google Maps le tessere hanno dimensioni di 256 pixel, mentre le tessere in Mappe di Azure hanno dimensioni di 512 pixel. Per ottenere la stessa visualizzazione mappa di Google Maps in Mappe di Azure, il livello di zoom usato in Google Maps deve essere inferiore di uno in Mappe di Azure.
- Le coordinate in Google Maps sono denominate "latitudine, longitudine", mentre Mappe di Azure usa "longitudine,latitudine". Il formato di Mappe di Azure è in linea con lo standard `[x, y]` seguito dalla maggior parte delle piattaforme GIS.
- Le forme in Azure Maps Web SDK sono basate sullo schema GeoJSON. Le classi helper vengono esposte tramite lo spazio dei nomi [*atlas.data*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). È anche disponibile la classe [*atlas.Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape). Usare questa classe per eseguire il wrapping di oggetti GeoJSON e semplificarne l'aggiornamento e la gestione grazie all'associazione dei dati.
- Le coordinate in Mappe di Azure sono definite come oggetti Position. Una coordinata è specificata come matrice di numeri nel formato `[longitude,latitude]`. In alternativa, viene specificata usando il nuovo formato atlas.data.Position(longitude, latitude).
    > [!TIP]
    > La classe Position dispone di un metodo helper statico per l'importazione di coordinate nel formato "latitudine,longitudine". Il metodo [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) può spesso sostituire il metodo `new google.maps.LatLng` nel codice di Google Maps.
- In Mappe di Azure gli stili sono separati dai dati. La separazione di dati e stili è più efficiente in termini di prestazioni rispetto a specificare le informazioni di stile in ogni forma aggiunta alla mappa. I dati vengono archiviati nelle origini dati connesse ai livelli di rendering. Il codice di Mappe di Azure usa le origini dati per eseguire il rendering dei dati. Questo approccio offre un vantaggio significativo in termini di prestazioni. Molti livelli supportano inoltre lo stile basato sui dati in cui è possibile aggiungere la logica di business alle opzioni di stile del livello. Questo supporto modifica la modalità di rendering delle singole forme all'interno di un livello in base alle proprietà definite nella forma.

## <a name="web-sdk-side-by-side-examples"></a>Esempi affiancati di Web SDK

Questa raccolta include esempi di codice per ogni piattaforma, ognuno dei quali tratta i casi d'uso comuni. È concepita per semplificare la migrazione dell'applicazione Web da Google Maps V3 JavaScript SDK ad Azure Maps Web SDK. Gli esempi di codice relativi alle applicazioni Web sono forniti in JavaScript. Tuttavia, Mappe di Azure fornisce anche le definizioni TypeScript come opzione aggiuntiva tramite un [modulo NPM](how-to-use-map-control.md).

### <a name="load-a-map"></a>Caricare una mappa

Entrambi gli SDK prevedono la stessa procedura per caricare una mappa:

- Aggiungere un riferimento a Map SDK.
- Aggiungere un tag `div` al corpo della pagina che fungerà da segnaposto per la mappa.
- Creare una funzione JavaScript che viene chiamata quando la pagina è stata caricata.
- Creare un'istanza della rispettiva classe Map.

**Alcune differenze principali**

- Google Maps richiede che venga specificata una chiave dell'account nel riferimento allo script dell'API. Le credenziali di autenticazione per Mappe di Azure vengono specificate come opzioni della classe Map. Le credenziali possono essere costituite da una chiave di sottoscrizione o dalle informazioni di Azure Active Directory.
- Google Maps accetta una funzione di callback nel riferimento allo script dell'API, che viene usata per chiamare una funzione di inizializzazione per caricare la mappa. Con Mappe di Azure è necessario usare l'evento OnLoad della pagina.
- Quando si fa riferimento all'elemento `div` in cui verrà eseguito il rendering della mappa, la classe `Map` in Mappe di Azure richiede solo il valore `id` mentre Google Maps richiede un oggetto `HTMLElement`.
- Le coordinate in Mappe di Azure sono definite come oggetti Position che possono essere specificati come una matrice di numeri semplice nel formato `[longitude, latitude]`.
- Il livello di zoom in Mappe di Azure è inferiore di un livello rispetto al livello di zoom in Google Maps. Questa discrepanza è dovuta alla differenza tra le dimensioni del sistema a tessere usato delle due piattaforme.
- Mappe di Azure non aggiunge alcun controllo di spostamento all'area di disegno della mappa. Di conseguenza, per impostazione predefinita una mappa non dispone di pulsanti di zoom e di pulsanti per lo stile della mappa. Esistono tuttavia controlli per l'aggiunta della selezione dello stile della mappa, pulsanti di zoom, bussola o controllo rotazione e un controllo dell'inclinazione.
- In Mappe di Azure viene aggiunto un gestore eventi per monitorare l'evento `ready` dell'istanza della mappa. Questo evento verrà attivato quando la mappa ha completato il caricamento del contesto WebGL e di tutte le risorse necessarie. Al termine del caricamento della mappa aggiungere il codice da eseguire in questo gestore eventi.

Gli esempi di base riportati sotto usano Google Maps per caricare una mappa centrata su New York alle coordinate seguenti: Longitudine: -73.985, latitudine: 40.747 con un livello di zoom della mappa pari a 12.

**Prima: Google Maps**

Visualizza una mappa di Google Maps centrata e ingrandita su una posizione.

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

Se si esegue questo codice in un browser verrà visualizzata una mappa simile all'immagine seguente:

<center>

![Mappa semplice di Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Dopo: Mappe di Azure**

Caricare una mappa con la stessa visualizzazione in Mappe di Azure insieme a un controllo stile mappa e ai pulsanti di zoom.

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

Se si esegue questo codice in un browser verrà visualizzata una mappa simile all'immagine seguente:

<center>

![Mappa semplice di Mappe di Azure](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

La documentazione dettagliata su come configurare e usare il controllo mappa di Mappe di Azure in un'app Web, è disponibile [qui](how-to-use-map-control.md).

> [!NOTE]
> A differenza di Google Maps, Mappe di Azure non richiede di specificare un centro e un livello di zoom iniziali per caricare la mappa. Se queste informazioni non vengono fornite durante il caricamento della mappa, Mappe di Azure tenterà di determinare la città dell'utente. La mappa verrà centrata e ingrandita su quella posizione.

**Risorse aggiuntive:**

- Mappe di Azure fornisce anche i controlli di spostamento per la rotazione e l'inclinazione della visualizzazione mappa come illustrato [qui](map-add-controls.md).

### <a name="localizing-the-map"></a>Localizzare la mappa

Se i destinatari sono distribuiti in più paesi o parlano lingue diverse, la localizzazione costituisce un aspetto importante.

**Prima: Google Maps**

Per localizzare la mappa di Google Maps, aggiungere i parametri relativi alla lingua e all'area.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Di seguito è riportato un esempio di Google Maps con la lingua impostata su "fr-FR".

<center>

![Localizzazione di Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Dopo: Mappe di Azure**

Mappe di Azure offre due modi diversi per impostare la lingua e la visualizzazione a livello di area della mappa. La prima opzione consiste nell'aggiungere queste informazioni allo spazio dei nomi *atlas* globale, affinché tutte le istanze di controllo mappa nell'app usino queste impostazioni predefinite. Il codice seguente imposta la lingua su Francese ("fr-FR") e la visualizzazione a livello di area su "automatica":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

La seconda opzione consiste nel passare queste informazioni nelle opzioni della mappa durante il caricamento la mappa nel modo seguente:

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
> Con Mappe di Azure è possibile caricare più istanze della mappa nella stessa pagina con impostazioni diverse per lingua e area geografica. È anche possibile aggiornare queste impostazioni nella mappa dopo che è stata caricata. 

Vedere un elenco dettagliato delle [lingue supportate](supported-languages.md) in Mappe di Azure.

Di seguito è riportato un esempio di Mappe di Azure con la lingua impostata su "fr" e l'area utente impostata su "fr-FR".

<center>

![Localizzazione di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Impostare la visualizzazione mappa

Le mappe dinamiche in Mappe di Azure e Google Maps possono essere spostate a livello di codice in nuove posizioni geografiche. A tale scopo, chiamare le funzioni appropriate in JavaScript. L'esempio mostra come visualizzare le immagini aeree satellitari, centrare la mappa su una posizione e modificare il livello di zoom impostandolo su 15 in Google Maps. Vengono usate le coordinate di posizione seguenti: longitudine: -111.0225 e latitudine: 35.0272.

> [!NOTE]
> Google Maps usa tessere con dimensioni di 256 pixel, mentre Mappe di Azure usa tessere più grandi, da 512 pixel. Di conseguenza, Mappe di Azure necessita di un numero di richieste di rete minore per caricare la stessa area mappa di Google Maps. A causa della modalità di funzionamento delle piramidi di tessere nei controlli mappa, è necessario sottrarre di uno il livello di zoom di Google Maps quando si usa Mappe di Azure. Questa operazione aritmetica garantisce che le tessere più grandi di Mappe di Azure eseguano il rendering della stessa area mappa di Google Maps.

**Prima: Google Maps**

Spostare il controllo mappa di Google Maps usando il metodo `setOptions`. Questo metodo consente di specificare il centro della mappa e un livello di zoom.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Impostazione visualizzazione di Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure modificare la posizione della mappa usando il metodo `setCamera` e modificare lo stile della mappa usando il metodo `setStyle`. Le coordinate in Mappe di Azure sono espresse nel formato "longitudine, latitudine" e il valore del livello di zoom è inferiore di uno.

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

![Impostazione visualizzazione di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Risorse aggiuntive:**

- [Scegliere uno stile mappa](choose-map-style.md)
- [Stili mappa supportati](supported-map-styles.md)

### <a name="adding-a-marker"></a>Aggiungere un marcatore

In Mappe di Azure esistono diversi modi in cui è possibile eseguire il rendering dei dati punto sulla mappa:

- **Indicatori HTML**: il rendering dei punti viene eseguito usando elementi DOM tradizionali. Gli indicatori HTML supportano il trascinamento.
- **Livello simbolo**: il rendering dei punti viene eseguito con un'icona o un testo all'interno del contesto WebGL.
- **Livello bolla**: il rendering dei punti viene eseguito come cerchi sulla mappa. I raggi dei cerchi possono essere ridimensionati in base alle proprietà nei dati.

Eseguire il rendering dei livelli simbolo e bolla all'interno del contesto WebGL. Entrambi i livelli possono eseguire il rendering di grandi set di punti sulla mappa e richiedono che i dati siano archiviati in un'origine dati. È necessario aggiungere le origini dati e i livelli di rendering alla mappa dopo che è stato attivato l'evento `ready`. Per gli indicatori HTML il rendering viene eseguito come elementi DOM all'interno della pagina e non è previsto l'uso di un'origine dati. Maggiore è il numero di elementi DOM della pagina, più lenta sarà la pagina. Se si esegue il rendering di più di qualche centinaio di punti su una mappa, è consigliabile usare uno dei livelli di rendering.

Aggiungere un indicatore alla mappa con il numero 10 sovrapposto come etichetta. Usare le coordinate seguenti: longitudine:-0.2 e latitudine: 51.5.

**Prima: Google Maps**

Con Google Maps, gli indicatori vengono aggiunti alla mappa usando la classe `google.maps.Marker` e specificando la mappa come una delle opzioni.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Marcatore di Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Dopo: Mappe di Azure con indicatori HTML**

In Mappe di Azure usare gli indicatori HTML per visualizzare un punto sulla mappa. Gli indicatori HTML sono consigliati per le app che visualizzeranno solo un numero ridotto di punti sulla mappa. Per usare un indicatore HTML, creare un'istanza della classe `atlas.HtmlMarker`. Impostare le opzioni relative al testo e alla posizione e aggiungere l'indicatore alla mappa usando il metodo `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Indicatore HTML di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Dopo: Mappe di Azure con un livello simbolo**

Per un livello simbolo, aggiungere i dati a un'origine dati. Collegare l'origine dati al livello. È anche necessario aggiungere l'origine dati e il livello alla mappa dopo che è stato attivato l'evento `ready`. Per eseguire il rendering di un valore di testo univoco sopra un simbolo, è necessario archiviare le informazioni di testo come proprietà del punto dati e fare riferimento a tale proprietà nell'opzione `textField` del livello. Questo approccio è più complesso rispetto all'uso degli indicatori HTML, ma offre prestazioni migliori.

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

![Livello simbolo di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Risorse aggiuntive:**

- [Creare un'origine dati](create-data-source-web-sdk.md)
- [Aggiungere un livello simbolo](map-add-pin.md)
- [Aggiungere un livello bolla](map-add-bubble-layer.md)
- [Clustering dei dati dei punti](clustering-point-data-web-sdk.md)
- [Aggiungere gli indicatori HTML](map-add-custom-html.md)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)
- [Opzioni icona del livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opzioni testo del livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe indicatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opzioni indicatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Aggiungere un marcatore personalizzato

È possibile usare immagini personalizzate per rappresentare i punti su una mappa. La mappa seguente usa un'immagine personalizzata per visualizzare un punto sulla mappa. Il punto è visualizzato in corrispondenza delle coordinate di latitudine: 51.5 e longitudine: -0.2. L'ancoraggio esegue l'offset della posizione dell'indicatore affinché il punto dell'icona a forma di puntina da disegno sia allineato alla posizione corretta sulla mappa.

<center>

![immagine puntina da disegno gialla](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**Prima: Google Maps**

Creare un indicatore personalizzato specificando un oggetto `Icon` contenente l'`url` dell'immagine. Specificare un punto `anchor` per allineare il punto dell'immagine della puntina da disegno alla coordinata sulla mappa. Il valore di ancoraggio in Google Maps è relativo all'angolo superiore sinistro dell'immagine.

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

![Marcatore personalizzato di Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Dopo: Mappe di Azure con indicatori HTML**

Per personalizzare un indicatore HTML passare un elemento HTML `string` o `HTMLElement` all'opzione `htmlContent` dell'indicatore. Usare l'opzione `anchor` per specificare la posizione relativa dell'indicatore rispetto alla coordinata della posizione. Assegnare uno dei nove punti di riferimento definiti all'opzione `anchor`. I punti definiti sono: "Al centro", "In alto", "In basso", "A sinistra", "A destra", "In alto a sinistra", "In alto a destra", "In basso a sinistra", "In basso a destra". Per impostazione predefinita, il contenuto è ancorato in basso al centro del contenuto HTML. Per semplificare la migrazione del codice da Google Maps, impostare `anchor` su "In alto a sinistra" e quindi usare l'opzione `pixelOffset` con lo stesso offset usato in Google Maps. Gli offset in Mappe di Azure si spostano nella direzione opposta rispetto a quelli di Google Maps ed è quindi necessario moltiplicare i valori per meno uno.

> [!TIP]
> Aggiungere `pointer-events:none` come stile nel contenuto HTML per disabilitare il comportamento di trascinamento predefinito in Microsoft Edge che comporta la visualizzazione di un'icona indesiderata.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Indicatore HTML personalizzato di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Dopo: Mappe di Azure con un livello simbolo**

I livelli simbolo in Mappe di Azure supportano anche le immagini personalizzate. Prima di tutto, caricare l'immagine nelle risorse della mappa e assegnare all'immagine un ID univoco. Fare riferimento all'immagine nel livello simbolo. Per allineare il punto dell'immagine punto corretto sulla mappa, usare l'opzione `offset`. Usare l'opzione `anchor` per specificare la posizione relativa del simbolo rispetto alle coordinate della posizione. Usare uno dei nove punti di riferimento definiti. I punti sono: "Al centro", "In alto", "In basso", "A sinistra", "A destra", "In alto a sinistra", "In alto a destra", "In basso a sinistra", "In basso a destra". Per impostazione predefinita, il contenuto è ancorato in basso al centro del contenuto HTML. Per semplificare la migrazione del codice da Google Maps, impostare `anchor` su "In alto a sinistra" e quindi usare l'opzione `offset` con lo stesso offset usato in Google Maps. Gli offset in Mappe di Azure si spostano nella direzione opposta rispetto a quelli di Google Maps ed è quindi necessario moltiplicare i valori per meno uno.

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

![Livello simbolo dell'icona personalizzata di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Per eseguire il rendering dei punti personalizzati avanzati, usare più livelli di rendering contemporaneamente. Si supponga ad esempio di voler usare più puntine da disegno con la stessa icona in cerchi colorati diversi. Anziché creare più immagini per ogni sovrapposizione di colori, è sufficiente aggiungere un livello simbolo sopra un livello bolla e impostare le puntine da disegno affinché facciano riferimento alla stessa origine dati. Questo approccio risulta molto più efficiente rispetto a creare e gestire numerose immagini diverse.

**Risorse aggiuntive:**

- [Creare un'origine dati](create-data-source-web-sdk.md)
- [Aggiungere un livello simbolo](map-add-pin.md)
- [Aggiungere gli indicatori HTML](map-add-custom-html.md)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)
- [Opzioni icona del livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opzioni testo del livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe indicatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opzioni indicatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Aggiungere una polilinea

Usare le polilinee per rappresentare una linea o un percorso sulla mappa. Ora verrà creata una polilinea tratteggiata sulla mappa.

**Prima: Google Maps**

La classe Polyline accetta un set di opzioni. Passare una matrice di coordinate nell'opzione `path` della polilinea.

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

![Polilinea di Google Maps](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Dopo: Mappe di Azure**

Le polilinee sono denominate come oggetti `LineString` o `MultiLineString`. È possibile aggiungere questi oggetti a un'origine dati ed eseguirne il rendering usando un livello linea. Aggiungere un oggetto `LineString` a un'origine dati, quindi aggiungere l'origine dati a un livello `LineLayer` per eseguirne il rendering.

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

![Polilinea di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Risorse aggiuntive:**

- [Aggiungere linee alla mappa](map-add-line-layer.md)
- [Opzioni del livello linea](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Aggiungere un poligono

Mappe di Azure e Google Maps forniscono un supporto simile per i poligoni. I poligoni vengono usati per rappresentare un'area sulla mappa. Gli esempi seguenti illustrano come creare un poligono che formi un triangolo basato sulla coordinata centrale della mappa.

**Prima: Google Maps**

La classe Polygon accetta un set di opzioni. Passare una matrice di coordinate nell'opzione `paths` del poligono.

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

![Poligono di Google Maps](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Dopo: Mappe di Azure**

Aggiungere un oggetto `Polygon` o `MultiPolygon` a un'origine dati. Eseguire il rendering dell'oggetto sulla mappa usando i livelli. Eseguire il rendering dell'area di un poligono usando un livello poligono. Infine, eseguire il rendering del contorno di un poligono usando un livello linea.

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

![Poligono di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Risorse aggiuntive:**

- [Aggiungere un poligono alla mappa](map-add-shape.md)
- [Aggiungere un cerchio alla mappa](map-add-shape.md#add-a-circle-to-the-map)
- [Opzioni del livello poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Opzioni del livello linea](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Visualizzare una finestra informazioni

È possibile visualizzare informazioni aggiuntive per un'entità sulla mappa come classe `google.maps.InfoWindow` in Google Maps. In Mappe di Azure la funzionalità equivalente è disponibile tramite la classe `atlas.Popup`. Negli esempi successivi viene aggiunto un indicatore alla mappa. Quando si fa clic sull'indicatore, viene visualizzata una finestra informazioni o un popup.

**Prima: Google Maps**

Creare un'istanza di una finestra informazioni usando il costruttore `google.maps.InfoWindow`.

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

![Finestra popup di Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Dopo: Mappe di Azure**

Usare una finestra popup per visualizzare informazioni aggiuntive sulla posizione. Passare un oggetto `string` o `HTMLElement` HTML all'opzione `content` della finestra popup. Se si desidera, è possibile visualizzare le finestre popup indipendentemente da qualsiasi forma. Per questo motivo, le finestre popup richiedono che venga specificato un valore `position`. Specificare il valore `position`. Per visualizzare una finestra popup, chiamare il metodo `open` e passare l'oggetto `map` in cui deve essere visualizzata la finestra popup.

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

![Finestra popup di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Per eseguire la stessa operazione con un livello simbolo, bolla, linea o poligono, passare il livello al codice dell'evento maps anziché un indicatore.

**Risorse aggiuntive:**

- [Aggiungere un popup](map-add-popup.md)
- [Finestra popup con contenuto multimediale](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Finestre popup sulle forme](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Riutilizzo di finestre popup con più puntine da disegno](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Classe Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Opzioni della finestra popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importare un file GeoJSON

Google Maps supporta il caricamento e l'applicazione dinamica di stili ai dati GeoJSON tramite la classe `google.maps.Data`. La funzionalità di questa classe è allineata con lo stile basato sui dati di Mappe di Azure. Ma esiste tuttavia una differenza fondamentale. Con Google Maps si specifica una funzione di callback. La logica di business per definire lo stile di ogni funzionalità è elaborata singolarmente nel thread dell'interfaccia utente. In Mappe di Azure i livelli consentono di specificare espressioni basate sui dati come opzioni di stile. Queste espressioni vengono elaborate in fase di rendering in un thread separato. L'approccio di Mappe di Azure migliora le prestazioni di rendering. Questo vantaggio si nota in particolare quando è necessario eseguire il rendering rapido di set di dati di dimensioni più elevate.

Gli esempi seguenti caricano un feed GeoJSON di tutti i terremoti negli ultimi sette giorni dall'agenzia United States Geological Survey (USGS) ed eseguono il rendering dei dati dei terremoti come cerchi dimensionati sulla mappa. Il colore e la scala di ogni cerchio sono basati sulla magnitudine di ogni terremoto archiviata nella proprietà `"mag"` di ogni funzionalità nel set di dati. Se la magnitudine è maggiore o uguale a cinque, il cerchio sarà rosso. Se è maggiore o uguale a tre, ma minore di cinque, il cerchio sarà arancione. Se è minore di tre, il cerchio sarà verde. Il raggio di ogni cerchio sarà il valore esponenziale della magnitudine moltiplicato per 0,1.

**Prima: Google Maps**

Specificare una singola funzione di callback nel metodo `map.data.setStyle`. All'interno della funzione di callback, applicare la logica di business a ogni funzionalità. Caricare il feed GeoJSON con il metodo `map.data.loadGeoJson`.

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

![GeoJSON di Google Maps](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Dopo: Mappe di Azure**

GeoJSON è il tipo di dati di base in Mappe di Azure. Importarlo in un'origine dati usando il metodo `datasource.importFromUrl`. Usare un livello bolla fornisce le funzionalità per il rendering di cerchi dimensionati in base alle proprietà delle funzionalità di un'origine dati. Anziché usare una funzione di callback, la logica di business viene convertita in un'espressione e passata nelle opzioni di stile. Le espressioni definiscono il funzionamento della logica di business e possono essere passate in un altro thread e valutate in base ai dati della funzionalità. In Mappe di Azure è possibile aggiungere più origini dati e livelli, ognuno con una logica di business diversa. Questa funzionalità consente di eseguire il rendering di più set di dati sulla mappa in modi diversi.

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

![GeoJSON di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Risorse aggiuntive:**

- [Aggiungere un livello simbolo](map-add-pin.md)
- [Aggiungere un livello bolla](map-add-bubble-layer.md)
- [Clustering dei dati dei punti](clustering-point-data-web-sdk.md)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clustering di indicatori

Quando si visualizzano molti punti dati sulla mappa, i punti possono sovrapporsi l'uno all'altro. La sovrapposizione rende la mappa confusa, difficile da leggere e da usare. Il clustering dei dati dei punti è la combinazione dei punti dati vicini tra loro e rappresentati sulla mappa come singolo punto dati in cluster. Quando l'utente ingrandisce la mappa, i cluster si scompongono nei singoli punti dati. I punti dati in cluster consentono di migliorare l'esperienza utente e le prestazioni della mappa.

Negli esempi seguenti il codice carica un feed GeoJSON dei dati sismici della settimana precedente e lo aggiunge alla mappa. Viene eseguito il rendering dei cluster come cerchi dimensionati e colorati. La scala e il colore dei cerchi dipendono dal numero di punti in essi contenuti.

> [!NOTE]
> Google Maps e Mappe di Azure usano algoritmi di clustering leggermente diversi. Di conseguenza, in alcuni casi la distribuzione dei punti nei cluster può variare.

**Prima: Google Maps**

Usare la libreria MarkerCluster per raggruppare gli indicatori. Le icone del cluster sono esclusivamente immagini contrassegnate con un numero da uno a cinque ospitate nella stessa directory.

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

![Clustering di Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Dopo: Mappe di Azure**

Aggiungere e gestire dati in un'origine dati. Connettere le origini dati e i livelli e quindi eseguire il rendering dei dati. La classe `DataSource` in Mappe di Azure fornisce numerose opzioni di clustering.

- `cluster`: indica all'origine dati di raggruppare i dati dei punti.
- `clusterRadius`: raggio in pixel in base a cui raggruppare i punti.
- `clusterMaxZoom`: livello di zoom massimo a cui viene applicato il clustering. Se si usa un livello di zoom maggiore di questo valore, viene eseguito il rendering di tutti i punti come simboli.
- `clusterProperties`: definisce le proprietà personalizzate che vengono calcolate usando le espressioni in tutti i punti all'interno di ogni cluster e aggiunte alle proprietà di ogni punto del cluster.

Quando il clustering è abilitato, l'origine dati invierà i punti dati in cluster e non in cluster ai livelli per il rendering. L'origine dati è in grado di raggruppare centinaia di migliaia di punti dati. Un punto dati in cluster presenta le proprietà seguenti:

| Nome proprietà             | Type    | Descrizione   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se la funzionalità rappresenta un cluster. |
| `cluster_id`              | string  | ID univoco per il cluster che può essere usato con i metodi `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves` della classe DataSource. |
| `point_count`             | d'acquisto  | Numero di punti contenuti nel cluster.  |
| `point_count_abbreviated` | string  | Stringa che abbrevia il valore `point_count` se è lungo. Ad esempio, 4.000 diventa 4K.  |

La classe `DataSource` dispone della funzione helper seguente per accedere a informazioni aggiuntive su un cluster tramite `cluster_id`.

| Metodo | Tipo restituito | Descrizione |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Recupera gli elementi figlio del cluster specificato al livello di zoom successivo. Questi elementi figlio possono essere costituiti da una combinazione di forme e sottocluster. I sottocluster saranno funzionalità con proprietà corrispondenti a ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;number&gt; | Calcola un livello di zoom in corrispondenza del quale il cluster inizierà a espandersi o scomporsi. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Recupera tutti i punti in un cluster. Impostare `limit` per restituire un subset dei punti e usare `offset` per spostarsi attraverso i punti. |

Quando si esegue il rendering dei dati in cluster sulla mappa, è in genere più facile usare due o più livelli. L'esempio seguente usa tre livelli. Un livello bolla per disegnare i cerchi colorati dimensionati in base alle dimensioni dei cluster. Un livello simbolo per il rendering delle dimensioni del cluster come testo. E infine un secondo livello simbolo per il rendering dei punti non in cluster. Sono disponibili molti altri modi per eseguire il rendering dei dati in cluster. Per altre informazioni, vedere la documentazione per i [punti dati in cluster](clustering-point-data-web-sdk.md).

Importare direttamente i dati GeoJSON nella mappa di Mappe di Azure usando la funzione `importDataFromUrl` sulla classe `DataSource`.

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

![Clustering di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Risorse aggiuntive:**

- [Aggiungere un livello simbolo](map-add-pin.md)
- [Aggiungere un livello bolla](map-add-bubble-layer.md)
- [Clustering dei dati dei punti](clustering-point-data-web-sdk.md)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Aggiungere una mappa termica

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati. Vengono usate per rappresentare la densità dei dati mediante una gamma di colori. Più spesso vengono usate per mostrare le "aree sensibili" dei dati su una mappa. Le mappe termiche sono un ottima soluzione per eseguire il rendering di set di dati di grandi dimensioni.

Gli esempi seguenti caricano un feed GeoJSON di tutti i terremoti nel mese precedente dall'agenzia United States Geological Survey (USGS) e ne eseguono il rendering come mappa termica ponderata in cui la proprietà `"mag"` viene usata come peso.

**Prima: Google Maps**

Per creare una mappa termica è necessario caricare la libreria delle "visualizzazioni" aggiungendo `&libraries=visualization` all'URL dello script dell'API. Il livello mappa termica in Google Maps non supporta direttamente i dati GeoJSON. Prima di tutto, scaricare i dati e convertirli in una matrice di punti dati ponderati:

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

![Mappa termica di Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Dopo: Mappe di Azure**

Caricare i dati GeoJSON in un'origine dati e connettere l'origine dati a un livello mappa termica. La proprietà che verrà usata per il peso può essere passata nell'opzione `weight` tramite un'espressione. Importare direttamente i dati GeoJSON in Mappe di Azure usando la funzione `importDataFromUrl` sulla classe `DataSource`.

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

![Mappa termica di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Risorse aggiuntive:**

- [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)
- [Classe del livello mappa termica](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Opzioni del livello mappa termica](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Sovrapporre un livello tessera

I livelli tessera in Mappe di Azure sono anche noti anche come Overlay immagine in Google Maps. I livelli tessera consentono di sovrapporre immagini di grandi dimensioni che sono state scomposte in immagini a tessere più piccole allineate al sistema a tessere delle mappe. Si tratta di un approccio usato di frequente per sovrapporre immagini o set di dati di grandi dimensioni.

Gli esempi seguenti mostrano la sovrapposizione di un livello tessera di radar meteo dall'Iowa Environmental Mesonet dell'Iowa State University.

**Prima: Google Maps**

In Google Maps è possibile creare livelli tessera usando la classe `google.maps.ImageMapType`.

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

![Livello tessera di Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Dopo: Mappe di Azure**

Aggiungere un livello tessera alla mappa con la stessa procedura usata per qualsiasi altro livello. Usare un URL formattato con i segnaposto x, y e zoom. `{x}`, `{y}`, `{z}` indicano al livello dove accedere alle tessere. I livelli tessera di Mappe di Azure supportano anche i segnaposto `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`.

> [!TIP]
> In Mappe di Azure è possibile eseguire facilmente il rendering dei livelli sotto altri livelli, inclusi i livelli mappa di base. Spesso è consigliabile eseguire il rendering dei livelli tessera sotto le etichette della mappa in modo che siano facili da leggere. Il metodo `map.layers.add` accetta un secondo parametro che è l'ID del livello sotto il quale inserire il nuovo livello. Per inserire un livello tessera sotto le etichette della mappa, usare il codice seguente: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Livello tessera di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> È possibile acquisire le richieste delle tessere tramite l'opzione `transformRequest` della mappa. Questo consentirà di modificare o aggiungere intestazioni alla richiesta, se necessario.

**Risorse aggiuntive:**

- [Aggiungere livelli tessera](map-add-tile-layer.md)
- [Classe livello tessera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Opzioni del livello tessera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Visualizzare il traffico

Sia Google Maps che Mappe di Azure consentono la sovrapposizione dei dati del traffico.

**Prima: Google Maps**

Sovrapporre i dati sul traffico sulla mappa usando il livello traffico.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Traffico di Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Dopo: Mappe di Azure**

Mappe di Azure offre numerose opzioni per la visualizzazione del traffico. Visualizzare gli eventi del traffico, come strade chiuse e incidenti stradali, come icone sulla mappa. Sovrapporre il flusso del traffico e le strade con codifica a colori sulla mappa. È possibile modificare i colori in base al limite di velocità indicato, al normale ritardo previsto o al ritardo assoluto. I dati relativi agli incidenti in Mappe di Azure vengono aggiornati ogni minuto e i dati del flusso di traffico ogni due minuti.

Assegnare i valori desiderati per le opzioni di `setTraffic`.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Traffico in Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Se si fa clic su un'icona del traffico in Mappe di Azure, vengono visualizzate informazioni aggiuntive in una finestra popup.

<center>

![Eventi del traffico di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Risorse aggiuntive:**

- [Mostrare il traffico sulla mappa](map-show-traffic.md)
- [Opzioni di sovrapposizione del traffico](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Aggiungere un overlay sul terreno

Sia Mappe di Azure che Google Maps supportano la sovrapposizione di immagini con riferimenti geografici sulla mappa. Le immagini con riferimenti geografici si spostano e vengono ridimensionate durante le operazioni di panoramica e zoom sulla mappa. In Google Maps le immagini con riferimenti geografici sono note come overlay sul terreno mentre in Mappe di Azure sono definite livelli immagine. Si tratta di un'ottima soluzione per la creazione di planimetrie, la sovrapposizione di vecchie mappe o delle immagini da un drone.

**Prima: Google Maps**

Specificare l'URL dell'immagine che si desidera sovrapporre e un rettangolo di selezione per associare l'immagine alla mappa. Questo esempio mostra la sovrapposizione di un'immagine mappa di [Newark New Jersey del 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) sulla mappa.

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

Se si esegue questo codice in un browser verrà visualizzata una mappa simile all'immagine seguente:

<center>

![Overlay immagine di Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Dopo: Mappe di Azure**

Usare la classe `atlas.layer.ImageLayer` per sovrapporre immagini con riferimenti geografici. Questa classe richiede l'URL di un'immagine e un set di coordinate per i quattro angoli dell'immagine. L'immagine deve essere ospitata nello stesso dominio oppure è necessario avere abilitato i criteri di condivisione tra le origini (CORS).

> [!TIP]
> Se sono presenti solo le informazioni relative a nord, sud, est, ovest e rotazione anziché le coordinate per ogni angolo dell'immagine, è possibile usare il metodo statico [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-).

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

![Overlay immagine di Mappe di Azure](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Risorse aggiuntive:**

- [Sovrapporre un'immagine](map-add-image-layer.md)
- [Classe livello immagine](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Esempi di codice aggiuntivi

Di seguito sono riportati alcuni esempi di codice aggiuntivi relativi alla migrazione da Google Maps:

- [Strumenti di disegno](map-add-drawing-toolbar.md)
- [Limitare la mappa alla panoramica con due dita](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Limitare lo zoom con la rotellina del mouse](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Creare un controllo Schermo intero](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Servizi:**

- [Utilizzo del modulo dei servizi di Mappe di Azure](how-to-use-services-module.md)
- [Cercare i punti di interesse](map-search-location.md)
- [Ottenere informazioni su una coordinata (geocodifica inversa)](map-get-information-from-coordinate.md)
- [Visualizzare le indicazioni stradali da A a B](map-route.md)
- [Cercare suggerimenti automatici con l'interfaccia utente di JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapping delle classi di Google Maps V3 ad Azure Maps Web SDK

L'appendice seguente offre un riferimento incrociato delle classi usate più di frequente in Google Maps V3 e delle classi equivalenti di Azure Maps Web SDK.

### <a name="core-classes"></a>Classi di base

| Google Maps   | Mappe di Azure  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [atlas.Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[atlas.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[atlas.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[atlas.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[atlas.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [atlas.Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Classi di overlay

| Google Maps  | Mappe di Azure  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[atlas.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[atlas.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[atlas.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[atlas.BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [atlas.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Vedere [Aggiungere un cerchio alla mappa](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[atlas.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Classi dei servizi

Azure Maps Web SDK include un modulo dei servizi che può essere caricato separatamente. Questo modulo raccoglie i servizi REST di Mappe di Azure in un'API Web e può essere usato nelle applicazioni JavaScript, TypeScript e Node.js.

| Google Maps | Mappe di Azure  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Librerie

Le librerie aggiungono funzionalità supplementari alla mappa. Molte di esse sono incluse nell'SDK di base di Mappe di Azure. Di seguito sono riportate alcune classi equivalenti da usare in sostituzione delle librerie di Google Maps

| Google Maps           | Mappe di Azure   |
|-----------------------|--------------|
| Libreria di disegno       | [Modulo Strumenti di disegno](set-drawing-options.md) |
| Libreria delle geometrie      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Libreria delle visualizzazioni | [Livello mappa termica](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Come usare il controllo mappa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Come usare il modulo dei servizi](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Come usare il modulo Strumenti di disegno](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)

