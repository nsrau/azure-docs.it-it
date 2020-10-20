---
title: "Esercitazione: Eseguire la migrazione di un'app Web da Bing Maps | Mappe di Microsoft Azure"
description: Questa esercitazione illustra come eseguire la migrazione di un'app Web da Bing Maps a Mappe di Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 42ba92a0134ae1e8da91bbe7513668fa24c4718f
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876516"
---
# <a name="tutorial---migrate-a-web-app-from-bing-maps"></a>Esercitazione - Eseguire la migrazione di un'app Web da Bing Maps

Le app Web che usano Bing Maps spesso usano Bing Maps V8 JavaScript SDK. Azure Maps Web SDK è l'SDK basato su Azure idoneo per la migrazione. Web SDK di Mappe di Azure consente di personalizzare le mappe interattive con contenuto e immagini personali per la visualizzazione in applicazioni Web o per dispositivi mobili. Questo controllo usa WebGL, consentendo di eseguire il rendering di set di dati di grandi dimensioni con prestazioni elevate. Sviluppare con questo SDK usando JavaScript o TypeScript.

Se si esegue la migrazione di un'applicazione Web esistente, verificare se usa una libreria di controllo mappa open source, ad esempio Cesium, Leaflet e OpenLayers. In caso affermativo e se si preferisce continuare a usare la libreria, è possibile connetterla ai servizi tessera di Mappe di Azure ([tessere stradali](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [tessere satellitari](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). I collegamenti seguenti puntano a informazioni dettagliate su come usare Mappe di Azure in alcune librerie di controlli mappa open source di uso comune.

-   Cesium: controllo mappa 3D per il Web. [Esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentazione](https://cesiumjs.org/)
-   Leaflet: controllo mappa 2D leggero per il Web. [Esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentazione](https://leafletjs.com/)
-   OpenLayers: controllo mappa 2D per il Web che supporta le proiezioni. [Esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentazione](https://openlayers.org/)

Se si sviluppa con un framework JavaScript, può risultare utile uno dei progetti open source seguenti:

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps): wrapper di Angular 10 intorno alle mappe di Azure.
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components): componente Blazor di Mappe di Azure.
- [Componente React di Mappe di Azure](https://github.com/WiredSolutions/react-azure-maps): wrapper React per il controllo di Mappe di Azure.
- [Mappe di Azure per Vue](https://github.com/rickyruiz/vue-azure-maps): componente di Mappe di Azure per l'applicazione Vue.

## <a name="key-features-support"></a>Supporto delle funzionalità principali

La tabella seguente elenca le principali funzionalità dell'API di Bing Maps V8 JavaScript SDK e il supporto di un'API simile in Azure Maps Web SDK.

| Funzionalità di Bing Maps        | Supporto di Azure Maps Web SDK                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Puntine                 | ✓                                                                                      |
| Raggruppamento di puntine       | ✓                                                                                      |
| Polilinee e poligoni     | ✓                                                                                      |
| Overlay sul terreno          | ✓                                                                                      |
| Mappe termiche                | ✓                                                                                      |
| Livelli tessera              | ✓                                                                                      |
| Livello KLM                | ✓                                                                                      |
| Livello contorno            | [Esempi](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Livello contenitori per dati       | [Esempi](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)       |
| Livello tessera animata      | Incluso nel [modulo di animazione](https://github.com/Azure-Samples/azure-maps-animations) open source di Mappe di Azure |
| Strumenti di disegno            | ✓                                                                                      |
| Servizio di geocodifica         | ✓                                                                                      |
| Servizio Indicazioni       | ✓                                                                                      |
| Servizio Matrice di distanze  | ✓                                                                                      |
| Servizio di dati spaziali     | N/D                                                                                    |
| Immagini satellitari/aeree | ✓                                                                                      |
| Immagini di viste aeree         | Pianificata                                                                                |
| Immagini di viste a livello strada       | Pianificata                                                                                |
| Supporto di GeoJSON          | ✓                                                                                      |
| Supporto di GeoXML           | ✓                                                                                      |
| Supporto di Well-Known Text  | ✓                                                                                      |
| Stili di mappa personalizzati        | Partial                                                                                |

Mappe di Azure include anche molti [moduli open source per l'SDK Web](open-source-projects.md#open-web-sdk-modules) che ne estendono le funzionalità.

## <a name="notable-differences-in-the-web-sdks"></a>Differenze significative tra i Web SDK

Di seguito sono riportate alcune delle principali differenze tra Bing Maps SDK e Azure Maps Web SDK da tenere presenti:

-   Oltre a fornire un endpoint ospitato per l'accesso ad Azure Maps Web SDK, è disponibile anche un pacchetto NPM per incorporare Web SDK nelle app, se si preferisce. Per altre informazioni, vedere questa [documentazione](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control). Questo pacchetto include anche le definizioni TypeScript.
-   Bing Maps include due rami ospitati dell'SDK, Release e Experimental. Il ramo Experimental può ricevere più aggiornamenti al giorno quando viene implementato un nuovo sviluppo. Mappe di Azure ospita solo un ramo Release, mentre le funzionalità sperimentali vengono create come moduli personalizzati nel progetto di esempi di codice di Mappe di Azure open source. Bing Maps aveva anche un ramo bloccato che veniva aggiornato con una minore frequenza, riducendo in tal modo il rischio di modifiche di rilievo dovute a una versione. In Mappe di Azure è possibile usare il modulo NPM e puntare a qualsiasi versione secondaria precedente.

> [!TIP]
> Mappe di Azure pubblica sia le versioni minimizzate che non minimizzate dell'SDK. È sufficiente rimuovere `.min` dai nomi file. La versione non minimizzata è utile per il debug di problemi, ma assicurarsi di usare quella minimizzata in produzione per sfruttare le dimensioni ridotte dei file.

-   Dopo aver creato un'istanza della classe Map in Mappe di Azure, il codice deve attendere l'attivazione dell'evento `ready` o `load` delle mappe prima di interagire con la mappa. Questi eventi assicurano che tutte le risorse delle mappe siano state caricate e siano pronte per l'accesso.
-   Entrambe le piattaforme usano un sistema a tessere simile per le mappe di base, tuttavia le dimensioni delle tessere in Bing Maps sono di 256 pixel mentre quelle di Mappe di Azure sono di 512 pixel. Di conseguenza, per ottenere la stessa visualizzazione mappa di Bing Maps, in Mappe di Azure è necessario sottrarre 1 dal livello di zoom usato in Bing Maps.
-   Le coordinate in Bing Maps sono identificate da `latitude, longitude`, mentre Mappe di Azure usa `longitude, latitude`. Questo formato è in linea con lo standard `[x, y]` seguito dalla maggior parte delle piattaforme GIS.

-   Le forme in Azure Maps Web SDK sono basate sullo schema GeoJSON. Le classi helper vengono esposte tramite lo spazio dei nomi [atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data). È disponibile anche la classe [atlas.Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) che può essere usata per eseguire il wrapping di oggetti GeoJSON e semplificarne l'aggiornamento e la gestione grazie al binding dei dati.
-   Le coordinate in Mappe di Azure sono definite come oggetti Position che possono essere specificati come una semplice matrice di numeri nel formato `[longitude, latitude]` o `new atlas.data.Position(longitude, latitude)`.

> [!TIP]
> La classe Position include una funzione helper statica per l'importazione di coordinate in formato `latitude, longitude`. La funzione [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position) può spesso sostituire la funzione `new Microsoft.Maps.Location` nel codice di Bing Maps.

-   Anziché specificare le informazioni di stile per ogni forma aggiunta alla mappa, Mappe di Azure separa gli stili dai dati. I dati vengono archiviati in origini dati e vengono connessi a livelli di rendering usati da Mappe di Azure per il rendering dei dati. Questo approccio offre un vantaggio significativo in termini di prestazioni. Molti livelli supportano inoltre lo stile basato sui dati in cui è possibile aggiungere la logica di business alle opzioni di stile del livello che modificano il rendering delle singole forme all'interno di un livello in base alle proprietà definite nella forma.
-   Mappe di Azure include diverse funzioni matematiche spaziali utili nello spazio dei nomi `atlas.math`, che però sono diverse da quelle usate nel modulo di matematica spaziale di Bing Maps. La differenza principale è che Mappe di Azure non prevede funzioni predefinite per operazioni binarie come l'unione e l'intersezione, ma poiché si basa su GeoJSON, che è uno standard aperto, sono disponibili molte librerie open source. Una delle opzioni più diffuse che funziona bene con Mappe di Azure e offre una grande quantità di funzionalità matematiche spaziali è [turf js](http://turfjs.org/).

Per un elenco dettagliato di termini associati a Mappe di Azure, vedere anche il [Glossario di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/glossary).

## <a name="web-sdk-side-by-side-examples"></a>Esempi affiancati di Web SDK

Di seguito è riportata una raccolta di esempi di codice per ogni piattaforma che illustra i casi d'uso comuni per facilitare la migrazione dell'applicazione Web da Bing Maps V8 JavaScript SDK ad Azure Maps Web SDK. Gli esempi di codice relativi alle applicazioni Web sono disponibili in JavaScript. Mappe di Azure fornisce anche le definizioni TypeScript come opzione aggiuntiva tramite un [modulo NPM](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

**Argomenti**

- [Caricare una mappa](#load-a-map)
- [Localizzare la mappa](#localizing-the-map)
- [Impostare la visualizzazione mappa](#setting-the-map-view)
- [Aggiungere una puntina](#adding-a-pushpin)
- [Aggiungere una puntina personalizzata](#adding-a-custom-pushpin)
- [Aggiungere una polilinea](#adding-a-polyline)
- [Aggiungere un poligono](#adding-a-polygon)
- [Visualizzare una casella informativa](#display-an-infobox)
- [Raggruppamento di puntine](#pushpin-clustering)
- [Aggiungere una mappa termica](#add-a-heat-map)
- [Sovrapporre un livello tessera](#overlay-a-tile-layer)
- [Visualizzare i dati sul traffico](#show-traffic-data)
- [Aggiungere un overlay sul terreno](#add-a-ground-overlay)
- [Aggiungere dati KML alla mappa](#add-kml-data-to-the-map)
- [Aggiungere strumenti di disegno](#add-drawing-tools)


### <a name="load-a-map"></a>Caricare una mappa

Il caricamento di una mappa in entrambi gli SDK implica la stessa serie di passaggi:

-   Aggiungere un riferimento a Map SDK.
-   Aggiungere un tag `div` al corpo della pagina che fungerà da segnaposto per la mappa.
-   Creare una funzione JavaScript che viene chiamata quando la pagina è stata caricata.
-   Creare un'istanza della rispettiva classe Map.

**Alcune differenze principali**

-   Bing Maps richiede che venga specificata una chiave di account nel riferimento allo script dell'API o come opzione di mappa. Le credenziali di autenticazione per Mappe di Azure vengono specificate come opzioni della classe Map. Può trattarsi di una chiave di sottoscrizione o delle informazioni di Azure Active Directory.
-   Bing Maps accetta una funzione di callback nel riferimento allo script dell'API, che viene usata per chiamare una funzione di inizializzazione per caricare la mappa. Con Mappe di Azure è necessario usare l'evento OnLoad della pagina.
-   Quando si usa un ID per fare riferimento all'elemento `div` in cui verrà eseguito il rendering della mappa, Bing Maps usa un selettore HTML, ovvero `#myMap`, mentre Mappe di Azure usa solo il valore ID, ovvero `myMap`.
-   Le coordinate in Mappe di Azure sono definite come oggetti Position che possono essere specificati come una semplice matrice di numeri nel formato `[longitude, latitude]`.
-   Il livello di zoom in Mappe di Azure è inferiore di un livello rispetto all'esempio di Bing Maps, a causa della differenza tra le dimensioni del sistema di tessere tra le piattaforme.
-   Per impostazione predefinita, Mappe di Azure non aggiunge alcun controllo di spostamento all'area della mappa, ad esempio pulsanti di zoom e pulsanti di stile della mappa. Esistono tuttavia controlli per l'aggiunta della selezione dello stile della mappa, pulsanti di zoom, bussola o controllo rotazione e un controllo dell'inclinazione.
-   In Mappe di Azure viene aggiunto un gestore eventi per monitorare l'evento `ready` dell'istanza della mappa. Verrà attivato quando la mappa ha completato il caricamento del contesto WebGL e di tutte le risorse necessarie. Il codice post-caricamento può essere aggiunto in questo gestore eventi.

Gli esempi seguenti illustrano come caricare una mappa di base in modo che sia centrata su New York alle coordinate (longitudine:-73.985, latitudine: 40.747) con un livello di zoom 12 in Bing Maps.

**Prima: Bing Maps**

Il codice seguente è un esempio di come visualizzare una mappa di Bing Maps centrata e ingrandita su una posizione.

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
          center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Se si esegue questo codice in un browser verrà visualizzata una mappa simile all'immagine seguente:

<center>

![Mappa di Bing Maps](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)</center>

**Dopo: Mappe di Azure**

Il codice seguente illustra come caricare una mappa con la stessa visualizzazione in Mappe di Azure insieme a un controllo stile mappa e ai pulsanti di zoom.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                    new atlas.control.StyleControl(),
                    new atlas.control.ZoomControl()
                ], {
                    position: 'top-right'
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

![Mappa di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)</center>

La documentazione dettagliata su come configurare e usare il controllo mappa di Mappe di Azure in un'app Web, è disponibile [qui](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

> [!TIP]
> Mappe di Azure pubblica sia le versioni minimizzate che non minimizzate dell'SDK. Rimuovere `.min` dai nomi file. La versione non minimizzata è utile per il debug di problemi, ma assicurarsi di usare quella minimizzata in produzione per sfruttare le dimensioni ridotte dei file.

**Risorse aggiuntive**

-   Mappe di Azure fornisce anche i controlli di spostamento per la rotazione e l'inclinazione della visualizzazione mappa come illustrato [qui](https://docs.microsoft.com/azure/azure-maps/map-add-controls).

### <a name="localizing-the-map"></a>Localizzare la mappa

Se i destinatari sono distribuiti in più paesi o parlano lingue diverse, la localizzazione costituisce un aspetto importante.

**Prima: Bing Maps**

Per localizzare Bing Maps, la lingua e l'area geografica vengono specificate con i parametri `setLang` e `UR` aggiunte al riferimento del tag `<script>` nell'API. Alcune funzionalità di Bing Maps sono disponibili solo in determinati mercati, di conseguenza il mercato dell'utente viene specificato con il parametro `setMkt`.

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Ecco un esempio di Bing Maps con la lingua impostata su "fr-FR".

<center>

![Mappa di Bing Maps localizzata](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

**Dopo: Mappe di Azure**

Mappe di Azure offre solo le opzioni per impostare la lingua e la visualizzazione locale della mappa. Non viene usato un parametro relativo al mercato per limitare le funzionalità. È possibile impostare la lingua e la visualizzane locale della mappa in due modi diversi. La prima opzione consiste nell'aggiungere queste informazioni allo spazio dei nomi `atlas` globale, per cui tutte le istanze dei controlli mappa dell'app usano queste impostazioni per impostazione predefinita. Il codice seguente imposta la lingua su francese ("fr-FR") e la visualizzazione locale su `"auto"`:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

La seconda opzione consiste nel passare queste informazioni nelle opzioni della mappa durante il caricamento la mappa, ad esempio:

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
> Con Mappe di Azure è possibile caricare più istanze della mappa nella stessa pagina con impostazioni diverse per lingua e area geografica. È anche possibile aggiornare queste impostazioni nella mappa dopo che è stata caricata. L'elenco dettagliato delle lingue supportate in Mappe di Azure è disponibile [qui](https://docs.microsoft.com/azure/azure-maps/supported-languages).

Di seguito è riportato un esempio di Mappe di Azure con la lingua impostata su "fr" e l'area utente impostata su "fr-FR".

<center>

![Mappa di Mappe di Azure localizzata](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

### <a name="setting-the-map-view"></a>Impostare la visualizzazione mappa

Le mappe dinamiche in Mappe di Azure e Bing Maps possono essere spostate a livello di codice in nuove aree geografiche chiamando le funzioni appropriate in JavaScript. Gli esempi seguenti illustrano come visualizzare le immagini aeree satellitari, centrare la mappa su una posizione con le coordinate (longitudine:-111.0225, latitudine: 35.0272) e impostare il livello di zoom su 15 in Bing Maps.

> [!NOTE]
> Bing Maps usa tessere con dimensioni di 256 pixel, mentre Mappe di Azure usa tessere più grandi, da 512 pixel. In questo modo si riduce il numero di richieste di rete necessarie a Mappe di Azure per caricare la stessa area mappa di Bing Maps. Tuttavia, a causa del funzionamento delle piramidi di tessere nei controlli mappa, per ottenere la stessa area visualizzabile come mappa di Bing Maps con le tessere più grandi di Mappe di Azure è necessario sottrarre 1 dal livello di zoom di Bing Maps quando si usa Mappe di Azure.

**Prima: Bing Maps**

Il controllo mappa di Bing Maps può essere spostato a livello di codice usando la funzione `setView`, che consente di specificare il centro della mappa e un livello di zoom.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Impostazione della visualizzazione mappa in Bing Maps](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure la posizione della mappa può essere cambiata a livello di codice usando la funzione `setCamera` della mappa e lo stile della mappa può essere cambiato usando la funzione `setStyle`. Si noti che le coordinate in Mappe di Azure sono espresse nel formato "longitudine, latitudine" e il valore del livello di zoom è sottratto di 1.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

<center>

![Impostazione della visualizzazione mappa in Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)</center>

**Risorse aggiuntive**

-   [Scegliere uno stile mappa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
-   [Stili mappa supportati](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)

### <a name="adding-a-pushpin"></a>Aggiungere una puntina

In Mappe di Azure è possibile eseguire il rendering dei dati dei punti sulla mappa in diversi modi:

-   Indicatori HTML: il rendering dei punti viene eseguito usando elementi DOM tradizionali. Gli indicatori HTML supportano il trascinamento.
-   Livello simbolo: il rendering dei punti viene eseguito con un'icona e/o un testo all'interno del contesto WebGL.
-   Livello bolla: il rendering dei punti viene eseguito come cerchi sulla mappa. I raggi dei cerchi possono essere ridimensionati in base alle proprietà nei dati.

Il rendering dei livelli simbolo e bolla viene eseguito nel contesto WebGL. Questi livelli possono eseguire il rendering di set di punti molto grandi sulla mappa e richiedono che i dati siano archiviati in un'origine dati. È necessario aggiungere le origini dati e i livelli di rendering alla mappa dopo che è stato attivato l'evento `ready`. Il rendering dei marcatori HTML viene eseguito come elementi DOM all'interno della pagina, senza l'uso di un'origine dati. Maggiore è il numero di elementi DOM della pagina, più lenta sarà la pagina. Se si esegue il rendering di più di qualche centinaio di punti su una mappa, è consigliabile usare uno dei livelli di rendering.

Gli esempi seguenti aggiungono un marcatore alla pagina alle coordinate (longitudine:-0,2, latitudine: 51.5) con il numero 10 sovrapposto come etichetta.

**Prima: Bing Maps**

Con Bing Maps, i marcatori vengono aggiunti alla mappa usando la classe `Microsoft.Maps.Pushpin`*. Le puntine vengono quindi aggiunte alla mappa usando una di due funzioni.

La prima funzione consiste nel creare un livello, inserire la puntina e quindi aggiungere il livello alla proprietà `layers` della mappa.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

La seconda consiste nell'aggiungere la puntina usando la proprietà `entities` della mappa. Questa funzione è contrassegnata come deprecata nella documentazione di Bing Maps V8, ma è rimasta parzialmente funzionante per gli scenari di base.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

<center>

![Aggiunta di puntine in Bing Maps](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)</center>

**Dopo: Mappe di Azure con indicatori HTML**

In Mappe di Azure i marcatori HTML possono essere usati per visualizzare facilmente un punto sulla mappa e sono consigliati per le app semplici che devono visualizzare solo un numero ridotto di punti. Per usare un marcatore HTML, creare un'istanza della classe `atlas.HtmlMarker`, impostare le opzioni relative al testo e alla posizione, quindi aggiungere il marcatore alla mappa con la funzione `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Marcatore aggiunto a Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Dopo: Mappe di Azure con un livello simbolo**

Quando si usa un livello simbolo, è necessario aggiungere i dati a un'origine dati e collegare l'origine dati al livello. È anche necessario aggiungere l'origine dati e il livello alla mappa dopo che è stato attivato l'evento `ready`. Per eseguire il rendering di un valore di testo univoco sopra un simbolo, è necessario archiviare le informazioni di testo come proprietà del punto dati e fare riferimento a tale proprietà nell'opzione `textField` del livello. Questa procedura è più complessa rispetto all'uso degli indicatori HTML, ma offre molti vantaggi in termini di prestazioni.

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

![Livello simboli aggiunto a Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Risorse aggiuntive**

-   [Creare un'origine dati](https://docs.microsoft.com/azure/azure-maps/create-data-source-web-sdk)
-   [Aggiungere un livello simbolo](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [Aggiungere un livello bolla](https://docs.microsoft.com/azure/azure-maps/map-add-bubble-layer)
-   [Clustering dei dati dei punti](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk)
-   [Aggiungere gli indicatori HTML](https://docs.microsoft.com/azure/azure-maps/map-add-custom-html)
-   [Usare espressioni di stile basate sui dati](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)
-   [Opzioni icona del livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opzioni testo del livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
-   [Classe indicatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opzioni indicatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Aggiungere una puntina personalizzata

È possibile usare immagini personalizzate per rappresentare i punti su una mappa. Negli esempi riportati di seguito viene usata un'immagine personalizzata per visualizzare un punto sulla mappa alle coordinate (latitudine: 51.5, longitudine: -0.2) con un offset della posizione del marcatore affinché il punto dell'icona a forma di puntina da disegno sia allineato alla posizione corretta sulla mappa.

| ![Aggiunta di puntine in Mappe di Azure](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |


**Prima: Bing Maps**

In Bing Maps viene creato un marcatore personalizzato passando un URL a un'immagine nelle opzioni `icon` di una puntina da disegno. L'opzione `anchor` si usa per allineare il punto dell'immagine della puntina con la coordinata sulla mappa. Il valore di ancoraggio in Bing Maps è relativo all'angolo in alto a sinistra dell'immagine.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

<center>

![Aggiunta di una puntina personalizzata in Bing Maps](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)</center>

**Dopo: Mappe di Azure con indicatori HTML**

Per personalizzare un indicatore HTML in Mappe di Azure, è possibile passare un elemento HTML `string` o `HTMLElement` nell'opzione `htmlContent` dell'indicatore. In Mappe di Azure viene usata un'opzione `anchor` per specificare la posizione relativa dell'indicatore rispetto alla coordinata della posizione usando uno dei nove punti di riferimento definiti: "Al centro", "In alto", "In basso", "A sinistra", "A destra", "In alto a sinistra", "In alto a destra", "In basso a sinistra", "In basso a destra". Il contenuto viene ancorato e impostato su "in basso" per impostazione predefinita, che corrisponde alla parte centrale inferiore del contenuto HTML. Per semplificare la migrazione del codice da Bing Maps, impostare l'ancoraggio su "in alto a sinistra" e quindi usare l'opzione `offset` con lo stesso offset usato in Bing Maps. Gli offset in Mappe di Azure sono spostati nella direzione opposta di Bing Maps ed è quindi necessario moltiplicare i valori per -1.

> [!TIP]
> Aggiungere `pointer-events:none` come stile nel contenuto HTML per disabilitare il comportamento di trascinamento predefinito in Microsoft Edge che comporta la visualizzazione di un'icona indesiderata.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Aggiunta di un marcatore personalizzato di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)</center>

**Dopo: Mappe di Azure con un livello simbolo**

I livelli simbolo in Mappe di Azure supportano anche le immagini personalizzate, ma è prima necessario caricare l'immagine nelle risorse della mappa e assegnarle un ID univoco. Il livello simbolo può quindi fare riferimento a questo ID. È possibile scostare il simbolo affinché sia allineato al punto corretto sull'immagine usando l'opzione `offset` dell'icona. In Mappe di Azure viene usata un'opzione `anchor` per specificare la posizione relativa del simbolo rispetto alla coordinata della posizione usando uno dei nove punti di riferimento definiti: "Al centro", "In alto", "In basso", "A sinistra", "A destra", "In alto a sinistra", "In alto a destra", "In basso a sinistra", "In basso a destra". Il contenuto viene ancorato e impostato su "in basso" per impostazione predefinita, che corrisponde alla parte centrale inferiore del contenuto HTML. Per semplificare la migrazione del codice da Bing Maps, impostare l'ancoraggio su "in alto a sinistra" e quindi usare l'opzione `offset` con lo stesso offset usato in Bing Maps. Gli offset in Mappe di Azure sono spostati nella direzione opposta di Bing Maps ed è quindi necessario moltiplicare i valori per -1.

```javascript
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
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

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

![Aggiunta del livello di simboli personalizzato in Bing Maps](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)</center>

> [!TIP]
> Per creare un rendering dei punti personalizzato e avanzato, usare più livelli di rendering contemporaneamente. Se ad esempio si desidera visualizzare più puntine da disegno con la stessa icona su cerchi colorati diversi, anziché creare più immagini per ogni colore è possibile sovrapporre un livello simbolo su un livello bolla e impostare i livelli affinché facciano riferimento alla stessa origine dati. Questa procedura risulta molto più efficiente rispetto a creare e gestire numerose immagini diverse.

**Risorse aggiuntive**

-   [Creare un'origine dati](https://docs.microsoft.com/azure/azure-maps/create-data-source-web-sdk)
-   [Aggiungere un livello simbolo](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [Aggiungere gli indicatori HTML](https://docs.microsoft.com/azure/azure-maps/map-add-custom-html)
-   [Usare espressioni di stile basate sui dati](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)
-   [Opzioni icona del livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opzioni testo del livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
-   [Classe indicatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opzioni indicatore HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Aggiungere una polilinea

Le polilinee vengono usate per rappresentare una linea o un percorso sulla mappa. Gli esempi seguenti illustrano come creare una polilinea tratteggiata sulla mappa.

**Prima: Bing Maps**

In Bing Maps la classe Polyline accetta una matrice di posizioni e un set di opzioni.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

<center>

![Polilinea di Bing Azure](media/migrate-bing-maps-web-app/bing-maps-line.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure le polilinee fanno riferimento agli oggetti `LineString` o `MultiLineString` dei termini geospaziali più comuni. È possibile aggiungere questi oggetti a un'origine dati ed eseguirne il rendering usando un livello linea. Le opzioni relative al colore del tratto, allo spessore e alla matrice di trattini sono quasi identiche tra le due piattaforme.

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

![Linea di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-line.jpg)</center>

**Risorse aggiuntive**

-   [Aggiungere linee alla mappa](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-lines-to-the-map)
-   [Opzioni del livello linea](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Usare espressioni di stile basate sui dati](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="adding-a-polygon"></a>Aggiungere un poligono

I poligoni vengono usati per rappresentare un'area sulla mappa. Mappe di Azure e Bing Maps offrono un supporto molto simile per i poligoni. Gli esempi seguenti illustrano come creare un poligono che formi un triangolo basato sulla coordinata centrale della mappa.

**Prima: Bing Maps**

In Bing Maps la classe Polygon accetta una matrice di coordinate o anelli di coordinate e un set di opzioni.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

<center>

![Poligono di Bing Azure](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure è possibile aggiungere gli oggetti Polygon e MultiPolygon a un'origine dati ed eseguirne il rendering sulla mappa usando i livelli. È possibile eseguire il rendering dell'area di un poligono in un livello poligono. È possibile eseguire il rendering del contorno di un poligono in un livello linea.

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

![Poligono di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**Risorse aggiuntive**

-   [Aggiungere un poligono alla mappa](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-a-polygon-to-the-map)
-   [Aggiungere un cerchio alla mappa](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-a-circle-to-the-map)
-   [Opzioni del livello poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Opzioni del livello linea](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Usare espressioni di stile basate sui dati](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="display-an-infobox"></a>Visualizzare una casella informativa

È possibile visualizzare informazioni aggiuntive per un'entità sulla mappa come classe `Microsoft.Maps.Infobox` in Bing Maps e con la classe `atlas.Popup` in Mappe di Azure. Gli esempi seguenti aggiungono una puntina/marcatore alla mappa che, quando si seleziona, visualizza una casella informativa/popup.

**Prima: Bing Maps**

In Bing Maps la casella informativa viene creata usando il costruttore `Microsoft.Maps.Infobox`.

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

<center>

![Casella informativa di Bing Maps](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure è possibile usare un popup per visualizzare informazioni aggiuntive per una posizione, passando un oggetto `string` o `HTMLElement` HTML nell'opzione `content` della finestra popup. Le finestre popup possono essere visualizzate indipendentemente da qualsiasi forma e richiedono quindi che venga specificato un valore `position`. Per visualizzare un popup, chiamare la funzione `open` e passarla nella mappa in cui il popup dovrà essere visualizzato.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
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

![Finestra popup di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)</center>

> [!NOTE]
> Per eseguire la stessa operazione con un livello di simboli, bolle, linee o poligoni, passare il livello nel codice dell'evento maps invece di un marcatore.

**Risorse aggiuntive**

-   [Aggiungere un popup](https://docs.microsoft.com/azure/azure-maps/map-add-popup)
-   [Finestra popup con contenuto multimediale](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Finestre popup sulle forme](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Riutilizzo di finestre popup con più puntine da disegno](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Classe Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)
-   [Opzioni della finestra popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Raggruppamento di puntine

Quando si visualizzano molti punti dati sulla mappa, i punti si sovrappongono, la mappa ha un aspetto disordinato e diventa difficile da visualizzare e usare. Il clustering dei dati dei punti consente di migliorare l'esperienza utente e ottimizzare le prestazioni. Il clustering dei dati dei punti è la combinazione di dati dei punti vicini tra loro e rappresentati sulla mappa come singolo punto dati in cluster. Quando l'utente ingrandisce la mappa, i cluster si scompongono nei singoli punti dati.

Gli esempi seguenti caricano un feed GeoJSON di dati sismici della settimana precedente e lo aggiungono alla mappa. I cluster vengono rappresentati come cerchi ridimensionati e colorati a seconda del numero di punti in essi contenuti.

> [!NOTE]
> Sono disponibili numerosi algoritmi diversi per il raggruppamento delle puntine. Bing Maps usa una semplice funzione basata su griglia, mentre Mappe di Azure usa metodi di raggruppamento basati su punti, più avanzati e visivamente accattivanti.

**Prima: Bing Maps**

In Bing Maps i dati GeoJSON possono essere caricati usando il modulo GeoJSON. Le puntine possono essere raggruppate caricando l'apposito modulo e usando il livello che contiene.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Raggruppamento di Bing Maps](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure i dati vengono aggiunti e gestiti da un'origine dati. I livelli si connettono alle origini dati ed eseguono il rendering dei dati in essi contenuti. La classe `DataSource` in Mappe di Azure fornisce numerose opzioni di clustering.

-   `cluster`: indica all'origine dati di raggruppare i dati dei punti. 
-   `clusterRadius`: raggio in pixel in base a cui raggruppare i punti.
-   `clusterMaxZoom`: livello massimo di zoom in cui si verifica il raggruppamento. Se si usa un livello di zoom maggiore di questo valore, viene eseguito il rendering di tutti i punti come simboli.
-   `clusterProperties`: definisce le proprietà personalizzate che vengono calcolate usando le espressioni in tutti i punti all'interno di ogni cluster e aggiunte alle proprietà di ogni punto del cluster.

Quando il clustering è abilitato, l'origine dati invierà i punti dati in cluster e non in cluster ai livelli per il rendering. L'origine dati è in grado di raggruppare centinaia di migliaia di punti dati. Un punto dati in cluster presenta le proprietà seguenti:

| Nome proprietà               | Type    | Descrizione                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | Indica se la funzionalità rappresenta un cluster.     |
| `cluster_id`                | string  | È possibile usare un ID unico per il raggruppamento con le classi `DataSource` `getClusterExpansionZoom`, `getClusterChildren` e le funzioni `getClusterLeaves`. |
| `point_count`               | d'acquisto  | Numero di punti contenuti nel cluster.     |
| `point_count_abbreviated`   | string  | Stringa che abbrevia il valore `point_count` se è lungo. Ad esempio, 4.000 diventa 4K. |

La classe `DataSource` dispone della funzione helper seguente per accedere a informazioni aggiuntive su un cluster tramite `cluster_id`.

| Funzione       | Tipo restituito        | Descrizione     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Recupera gli elementi figlio del cluster specificato al livello di zoom successivo. Questi elementi figlio possono essere una combinazione di forme e sottoraggruppamenti. I sottoraggruppamenti saranno funzionalità con proprietà che corrispondono alle proprietà del raggruppamento. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Calcola un livello di zoom in corrispondenza del quale il raggruppamento inizierà a espandersi o a scomporsi.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Recupera tutti i punti in un cluster. Impostare `limit` per restituire un sottoinsieme di punti e usare `offset` per spostarsi attraverso i punti.    |

Quando si esegue il rendering dei dati in cluster sulla mappa, è in genere più facile usare due o più livelli. L'esempio seguente usa tre livelli, un livello di bolle per disegnare cerchi colorati di dimensioni basate su quelli dei raggruppamenti, un livello di simboli per il rendering delle dimensioni del raggruppamento come testo e un secondo livello di simboli per il rendering dei punti non raggruppati. Mappe di Azure offre molti altri modi per eseguire il rendering dei dati in cluster, come evidenziato nella documentazione relativa ai [dati dei punti in cluster](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk).

I dati GeoJSON possono essere importati direttamente in Mappe di Azure usando la funzione `importDataFromUrl` sulla classe `DataSource`.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
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

![Clustering di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)</center>

**Risorse aggiuntive**

-   [Aggiungere un livello simbolo](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [Aggiungere un livello bolla](https://docs.microsoft.com/azure/azure-maps/map-add-bubble-layer)
-   [Clustering dei dati dei punti](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk)
-   [Usare espressioni di stile basate sui dati](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="add-a-heat-map"></a>Aggiungere una mappa termica

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati utilizzate per rappresentare la densità dei dati tramite una gamma di colori. Vengono spesso usate per visualizzare le "aree sensibili" dei dati su una mappa e sono un ottimo modo per eseguire il rendering di set di dati di punti di grandi dimensioni.

Gli esempi seguenti caricano carica un feed GeoJSON di tutti i dati sismici del mese precedente dall'agenzia USGS e ne eseguono il rendering come mappa termica.

**Prima: Bing Maps**

Per creare una mappa termica in Bing Maps, caricare l'apposito modulo. Allo stesso modo, viene caricato il modulo GeoJSON per aggiungere il supporto di dati GeoJSON.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Mappa termica di Bing Maps](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure caricare i dati GeoJSON in un'origine dati e connettere l'origine dati a un livello mappa termica. I dati GeoJSON possono essere importati direttamente in Mappe di Azure usando la funzione `importDataFromUrl` sulla classe `DataSource`.

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
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Mappa termica di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)</center>

**Risorse aggiuntive**

-   [Aggiungere un livello mappa termica](https://docs.microsoft.com/azure/azure-maps/map-add-heat-map-layer)
-   [Classe del livello mappa termica](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Opzioni del livello mappa termica](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Usare espressioni di stile basate sui dati](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="overlay-a-tile-layer"></a>Sovrapporre un livello tessera

consentono di sovrapporre immagini di grandi dimensioni che sono state scomposte in immagini a tessere più piccole allineate al sistema a tessere delle mappe. Si tratta di una soluzione comune per sovrapporre immagini di grandi dimensioni o set di dati molto grandi.

Gli esempi seguenti mostrano la sovrapposizione di un livello di tessere di radar meteo dall'Iowa Environmental Mesonet dell'Iowa State University, che usa lo schema di denominazione X, Y, Zoom.

**Prima: Bing Maps**

In Bing Maps è possibile creare livelli di tessere usando la classe `Microsoft.Maps.TileLayer`.

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

<center>

![Mappa termica ponderato di Bing Maps](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure è possibile aggiungere un livello tessera alla mappa con la stessa procedura usata per qualsiasi altro livello. Viene usato un URL formattato con i segnaposto x, y e zoom. `{x}`, `{y}`, `{z}` indicano al livello dove accedere alle tessere. I livelli tessera di Mappe di Azure supportano anche i segnaposto `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`.

> [!TIP]
> In Mappe di Azure è possibile eseguire facilmente il rendering dei livelli sotto altri livelli, inclusi i livelli mappa di base. Spesso è consigliabile eseguire il rendering dei livelli tessera sotto le etichette della mappa in modo che siano facili da leggere. La funzione `map.layers.add` accetta un secondo parametro che corrisponde all'ID del secondo livello sotto il quale inserire il nuovo livello. Per inserire un livello di tessere sotto le etichette della mappa, è possibile usare il codice seguente:
> 
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Mappa termica ponderata di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)</center>

> [!TIP]
> È possibile acquisire le richieste delle tessere tramite l'opzione `transformRequest` della mappa. Questo consentirà di modificare o aggiungere intestazioni alla richiesta, se necessario.

**Risorse aggiuntive**

-   [Aggiungere livelli tessera](https://docs.microsoft.com/azure/azure-maps/map-add-tile-layer)
-   [Classe livello tessera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Opzioni del livello tessera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Visualizzare i dati sul traffico

I dati sul traffico possono essere sovrapposti sia nelle mappe di Bing che in quelle di Azure.

**Prima: Bing Maps**

In Bing Maps i dati sul traffico possono essere sovrapposti alla mappa usando il modulo corrispondente.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

<center>

![Traffico in Bing Maps](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)</center>

**Dopo: Mappe di Azure**

Mappe di Azure offre numerose opzioni per la visualizzazione del traffico. Gli eventi del traffico, come strade chiuse e incidenti stradali, possono essere visualizzati come icone sulla mappa. Il flusso del traffico e le strade con codifica a colori possono essere sovrapposti sulla mappa e i colori possono essere modificati in base al limite di velocità previsto, al normale ritardo previsto o al ritardo assoluto. In Mappe di Azure i dati sugli incidenti vengono aggiornati ogni minuti e i dati sul flusso ogni due minuti.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Traffico in Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)</center>

Se si fa clic su un'icona del traffico in Mappe di Azure, vengono visualizzate informazioni aggiuntive in una finestra popup.

<center>

![Popup sul traffico di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)</center>

**Risorse aggiuntive**

-   [Mostrare il traffico sulla mappa](https://docs.microsoft.com/azure/azure-maps/map-show-traffic)
-   [Opzioni di sovrapposizione del traffico](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Controllo traffico](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Aggiungere un overlay sul terreno

Sia Mappe di Azure che Bing Maps supportano la sovrapposizione di immagini con riferimenti geografici sulla mappa, per consentirne lo spostamento e il dimensionamento durante le operazioni di panoramica e zoom sulla mappa. In Bing Maps sono note come overlay sul terreno mentre in Mappe di Azure sono definite livelli immagine. Si tratta di un'ottima soluzione per la creazione di planimetrie, la sovrapposizione di vecchie mappe o delle immagini da un drone.

**Prima: Bing Maps**

Quando si crea un overlay sul terreno in Bing Maps, è necessario specificare l'URL dell'immagine da sovrapporre e un rettangolo delimitatore che conterrà l'immagine sulla mappa. Questo esempio mostra la sovrapposizione di un'immagine mappa di [Newark New Jersey del 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) sulla mappa.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Se si esegue questo codice in un browser verrà visualizzata una mappa simile all'immagine seguente:

<center>

![Overlay sul terreno di Bing Maps](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure è possibile sovrapporre le immagini con riferimenti geografici usando la classe `atlas.layer.ImageLayer`. Questa classe richiede l'URL di un'immagine e un set di coordinate per i quattro angoli dell'immagine. L'immagine deve essere ospitata nello stesso dominio oppure è necessario avere abilitato i criteri di condivisione tra le origini (CORS).

> [!TIP]
> Se per ogni angolo dell'immagine sono disponibili solo le informazioni relative a nord, sud, est, ovest e rotazione invece delle coordinate, è possibile usare la funzione statica [atlas.layer.ImageLayer.getCoordinatesFromEdges](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-).

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Overlay sul terreno di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)</center>

**Risorse aggiuntive**

-   [Sovrapporre un'immagine](https://docs.microsoft.com/azure/azure-maps/map-add-image-layer)
-   [Classe livello immagine](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Aggiungere dati KML alla mappa

Sia Mappe di Azure che Bing Maps consentono di importare e visualizzare dati KML, KMZ, GeoRSS, GeoJSON e Well-Known Text (WKT) sulla mappa. Mappe di Azure supporta anche GPX, GML, file CSV spaziali, Web-Mapping Services (WMS), Web-Mapping Tile Services (WMTS) e Web Feature Services (WFS).

**Prima: Bing Maps**

Se si esegue questo codice in un browser verrà visualizzata una mappa simile all'immagine seguente:

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });

            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![KML di Bing Maps](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure, GeoJSON è il formato dati principale usato nell'SDK Web, altri formati di dati spaziali possono essere facilmente integrati usando il  [​​modulo di I/O spaziale](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/). Questo modulo include funzioni sia per la lettura che per la scrittura di dati spaziali, oltre a un semplice livello dati che consente di eseguire facilmente il rendering dei dati da uno di questi formati di dati spaziali. Per leggere i dati in un file di dati spaziali, passare un URL o dati non elaborati come stringa o BLOB nella funzione `atlas.io.read` . Verranno restituiti tutti i dati analizzati dal file che è possibile aggiungere alla mappa. Il formato KML è un po' più complesso rispetto alla maggior parte dei dati spaziali, perché include molte più informazioni sullo stile. La classe `SpatialDataLayer` supporta il rendering della maggior parte di questi stili, tuttavia le immagini delle icone devono essere caricate nella mappa prima di caricare i dati delle caratteristiche e gli overlay sul terreno devono essere aggiunti come livelli alla mappa separatamente. Quando si caricano i dati tramite un URL, questo deve essere ospitato in un endpoint abilitato per CORs o è necessario passare un servizio proxy come opzione nella funzione read.

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
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

![KML di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)</center>

**Risorse aggiuntive**

-   [Funzione atlas.io.read](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Aggiungere strumenti di disegno

Sia Bing Maps che Mappe di Azure includono un modulo che aggiunge la possibilità di disegnare e modificare forme sulla mappa tramite il mouse o un altro dispositivo di input. Entrambe le piattaforme supportano il disegno di puntine, linee e poligoni. Mappe di Azure offre anche opzioni per il disegno di cerchi e rettangoli.

**Prima: Bing Maps**

In Bing Maps il modulo `DrawingTools` viene caricato usando la funzione `Microsoft.Maps.loadModule`. Una volta caricato, è possibile creare un'istanza della classe DrawingTools e chiamare la funzione `showDrawingManager` per aggiungere una barra degli strumenti alla mappa.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

<center>

![Strumenti di disegno di Bing Maps](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure il modulo di strumenti di disegno deve essere caricato caricando i file JavaScript e CSS a cui è necessario fare riferimento nell'app. Una volta caricata la mappa, è possibile creare un'istanza della classe `DrawingManager` e un'istanza di `DrawingToolbar` collegata.

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

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',
                
                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

<center>

![Strumenti di disegno di Mappe di Azure](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)</center>

> [!TIP]
> Nei livelli di Mappe di Azure gli strumenti di disegno offrono molte possibilità per il disegno di forme. Ad esempio, quando si disegna un poligono, l'utente può fare clic per aggiungere ogni punto oppure tenere premuto il pulsante del mouse e trascinarlo per disegnare un tragitto. Questa funzionalità può essere modificata con l'opzione `interactionType` di `DrawingManager`.

**Risorse aggiuntive**

-   [Documentazione](https://docs.microsoft.com/azure/azure-maps/set-drawing-options)
-   [Esempi di codice](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="next-steps"></a>Passaggi successivi

Esaminare i [moduli open source di Azure Maps Web SDK](open-source-projects.md#open-web-sdk-modules). Questi moduli offrono una grande quantità di funzionalità aggiuntive e sono completamente personalizzabili.

Esaminare gli esempi di codice correlati alla migrazione di altre funzionalità di Bing Maps:

**Visualizzazioni di dati**

> [!div class="nextstepaction"]
> [Livello contorno](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Creazione di contenitori per i dati](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Services**

> [!div class="nextstepaction"]
> [Utilizzo del modulo dei servizi di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Cercare i punti di interesse](https://docs.microsoft.com/azure/azure-maps/map-search-location)

> [!div class="nextstepaction"]
> [Ottenere informazioni su una coordinata (geocodifica inversa)](https://docs.microsoft.com/azure/azure-maps/map-get-information-from-coordinate)

> [!div class="nextstepaction"]
> [Visualizzare le indicazioni stradali da A a B](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Cercare suggerimenti automatici con l'interfaccia utente di JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Altre informazioni su Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Come usare il controllo mappa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Come usare il modulo dei servizi](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Come usare il modulo Strumenti di disegno](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Documentazione di riferimento per l'API del servizio Azure Maps Web SDK](https://docs.microsoft.com/javascript/api/azure-maps-control/)
