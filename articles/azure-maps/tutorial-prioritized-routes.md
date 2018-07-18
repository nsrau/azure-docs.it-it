---
title: Più itinerari con Mappe di Azure | Microsoft Docs
description: Trovare gli itinerari per diverse modalità di trasporto tramite Mappe di Azure
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ea68c70befde1922fc30d06386a2ae326460c722
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601412"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Trovare gli itinerari per diverse modalità di trasporto tramite Mappe di Azure

Questa esercitazione illustra come usare l'account Mappe di Azure e il servizio di pianificazione itinerari per trovare gli itinerari più adatti per raggiungere un punto di interesse in base alla modalità di trasporto selezionata. Saranno visualizzati due itinerari diversi sulla mappa, uno per le automobili e uno per gli autocarri che potrebbero essere soggetti a limitazioni a causa di altezza, peso e pericolosità del carico. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Visualizzare il flusso del traffico sulla mappa
> * Creare query di itinerario che dichiarano la modalità di viaggio
> * Visualizzare più itinerari sulla mappa

## <a name="prerequisites"></a>prerequisiti

Prima di procedere eseguire i passaggi della prima esercitazione per [creare l'account Mappe di Azure](./tutorial-search-location.md#createaccount) e [ottenere la chiave di sottoscrizione per l'account](./tutorial-search-location.md#getkey). 


## <a name="create-a-new-map"></a>Creare una nuova mappa 
La procedura seguente illustra come creare una pagina HTML statica incorporata usando l'API del controllo mappa. 

1. Nel computer locale creare un nuovo file con il nome **MapTruckRoute.html**. 
2. Aggiungere al file i componenti HTML seguenti:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    
    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    L'intestazione HTML incorpora le posizioni delle risorse per i file CSS e JavaScript relative alla libreria di Mappe di Azure. Il segmento *script* nel corpo dell'HTML deve contenere il codice JavaScript per la mappa.
3. Aggiungere il codice JavaScript seguente al blocco *script* del file HTML. Sostituire la stringa **\<your account key\>** con la chiave primaria copiata dall'account Mappe.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    **atlas.Map** fornisce il controllo per una mappa Web visiva e interattiva ed è un componente dell'API del controllo mappa di Azure.

4. Salvare il file e aprirlo nel browser. A questo punto si ha una mappa di base che è possibile sviluppare ulteriormente. 

   ![Visualizzare la mappa di base](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizzare il flusso del traffico

1. Se l'utente non segnala alla mappa dove concentrarsi, viene mostrata la visualizzazione del mondo intero. Per poter visualizzare i dati di traffico, impostare un punto centrale e un livello di zoom sulla mappa. Sostituire il codice che dichiara una `new atlas.Map` con il codice JavaScript seguente: 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    Questo codice imposta il punto centrale della mappa e dichiara un livello di zoom in modo che sia possibile concentrarsi su una determinata area per impostazione predefinita. 

1. Aggiungere la visualizzazione del flusso di traffico alla mappa:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Questo codice imposta il flusso del traffico su `relative`, che corrisponde alla velocità sulla strada in relazione a condizioni di traffico libero. È anche possibile impostare il flusso del traffico su `absolute`, ovvero sul valore assoluto della velocità sulla strada, o su `relative-delay`, che visualizza la velocità relativa in caso di variazione rispetto alle condizioni di traffico libero. 

2. Salvare il file **MapTruckRoute.html** e aggiornare la pagina nel browser. Dovrebbero comparire le strade di Los Angeles con i dati del traffico in tempo reale.

   ![Visualizzare la mappa con il traffico](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Impostare i punti di partenza e di arrivo

Per questa esercitazione impostare come punto di partenza una società fittizia di Seattle denominata Fabrikam e come punto di arrivo un ufficio di Microsoft. 

1. Aggiungere il codice JavaScript seguente per creare i segnaposto per i punti iniziale e finale del percorso:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Questo codice crea due [oggetti GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) per rappresentare i punti iniziale e finale del percorso. 

2. Aggiungere il codice JavaScript seguente per includere nella mappa i punti iniziale e finale:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    La chiamata della funzione **map.setCameraBounds** regola la finestra della mappa in base alle coordinate dei punti di partenza e di arrivo. La funzione **map.addPins** dell'API aggiunge i punti al controllo mappa come componenti visivi.

3. Salvare il file e aggiornare il browser per visualizzare i punti sulla mappa. Anche se la mappa è stata dichiarata con un punto centrale in Los Angeles, **map.setCameraBounds** ha spostato la visualizzazione in modo da mostrare i punti di partenza e di arrivo. 

   ![Visualizzare la mappa con i punti di partenza e di arrivo](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Classificare i percorsi in ordine di priorità in base alla modalità di trasporto

Questa sezione illustra come usare l'API del servizio di pianificazione itinerari per trovare più itinerari per raggiungere una destinazione da un determinato punto di partenza, in base alla modalità di trasporto. Il servizio di pianificazione itinerari fornisce le API per pianificare l'itinerario *più veloce*, *più breve*, *più ecologico* o *più entusiasmante* tra due punti, considerando le condizioni di traffico in tempo reale. Consente inoltre agli utenti di pianificare percorsi per il futuro usando il database dei dati storici sul traffico di Azure e fornendo stime della durata dei percorsi per qualsiasi giorno e ora. Per altre informazioni, vedere [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Ottenere le indicazioni stradali).


1. Aggiungere prima un nuovo livello sulla mappa per visualizzare l'itinerario, ovvero l'elemento *linestring*. In questa esercitazione ci sono due itinerari diversi, **car-route** e **truck-route**, ciascuno con il proprio stile. Aggiungere il codice JavaScript seguente al blocco *script*:

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. Aggiungere il codice JavaScript seguente al blocco *script* per richiedere l'itinerario per un autocarro e visualizzare i risultati sulla mappa:

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    Questo frammento di codice crea un oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) e aggiunge un gestore eventi per analizzare la risposta in ingresso. Per ottenere una risposta, crea una matrice di coordinate per il percorso restituito e aggiunge tale elemento al livello `truckRouteLayerName` della mappa. 
    
    Questo frammento di codice crea la query per il servizio di pianificazione itinerari di Mappe mediante la chiave dell'account. La query include le coordinate del punto di partenza, le coordinate del punto di arrivo e parametri opzionali per indicare che l'itinerario è per un autocarro pesante.

2. Aggiungere il codice JavaScript seguente per richiedere l'itinerario per un'auto e visualizzare i risultati:

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Questo frammento di codice crea un altro oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) e aggiunge un gestore di evento per analizzare la risposta in ingresso. Per ottenere una risposta crea una matrice di coordinate per l'itinerario restituito e aggiunge tale elemento al livello `carRouteLayerName` della mappa. 
    
    Questo frammento di codice crea la query per il servizio di pianificazione itinerari di Mappe mediante la chiave dell'account. La query include le coordinate del punto di partenza e le coordinate del punto di arrivo. Poiché non sono forniti altri aggiuntivi, il servizio di pianificazione itinerari per impostazione predefinita usa la modalità *car*. 

3. Salvare il file **MapTruckRoute.html** e aggiornare il browser per osservare il risultato. Se la connessione con le API di Mappe è stata stabilita correttamente, verrà visualizzata una mappa simile alla seguente. 

    ![Percorsi classificati in ordine di priorità con il servizio di pianificazione percorso](./media/tutorial-prioritized-routes/prioritized-routes.png)

    L'itinerario per gli autocarri è blu e più è spesso, mentre l'itinerario per le auto è viola e più sottile. L'itinerario per le auto passa attraverso Washington Lake tramite la I-90, che attraversa i tunnel sotto le zone residenziali e pertanto è interdetto ai carichi di rifiuti pericolosi. L'itinerario per gli autocarri, che specifica il tipo di carico USHazmatClass2, suggerisce correttamente di usare un'autostrada diversa. 

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Visualizzare il flusso del traffico sulla mappa
> * Creare query di itinerario che dichiarano la modalità di viaggio
> * Visualizzare più itinerari sulla mappa

Per altre informazioni sulla copertura e le funzionalità di Mappe di Azure, vedere [Livelli di zoom e griglia dei riquadri](zoom-levels-and-tile-grid.md) e gli altri articoli relativi ai concetti. 

Per altri esempi di codice e un'esperienza interattiva di codifica, vedere [Come usare il controllo mappa](how-to-use-map-control.md) e le altre guide pratiche. 
