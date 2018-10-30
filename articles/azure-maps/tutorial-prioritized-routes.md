---
title: Più itinerari con Mappe di Azure | Microsoft Docs
description: Trovare gli itinerari per diverse modalità di trasporto tramite Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 10/22/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 864f662cd6be3c5929166db92f2dad92b9c6586e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648208"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Trovare gli itinerari per diverse modalità di trasporto tramite Mappe di Azure

Questa esercitazione illustra come usare l'account Mappe di Azure e il servizio di pianificazione itinerari per trovare gli itinerari più adatti per raggiungere un punto di interesse in base alla modalità di trasporto selezionata. Saranno visualizzati due itinerari diversi sulla mappa, uno per le automobili e uno per gli autocarri che potrebbero essere soggetti a limitazioni a causa di altezza, peso e pericolosità del carico. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Visualizzare il flusso del traffico sulla mappa
> * Creare query di itinerario che dichiarano la modalità di viaggio
> * Visualizzare più itinerari sulla mappa

## <a name="prerequisites"></a>Prerequisiti

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
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>
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

3. Aggiungere il codice JavaScript seguente al blocco *script* del file HTML. Sostituire la stringa **\<your account key\>** con la chiave primaria copiata dall'account Mappe. Se l'utente non segnala alla mappa dove concentrarsi, viene mostrata la visualizzazione del mondo intero. Questo codice imposta il punto centrale della mappa e dichiara un livello di zoom in modo che sia possibile concentrarsi su una determinata area per impostazione predefinita.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var mapCenterPosition = [-73.985708, 40.75773];
    atlas.setSubscriptionKey("<your account key>");
    var map = new atlas.Map("map", {
      center: mapCenterPosition,
      zoom: 11
    });
    ```
    **atlas.Map** fornisce il controllo per una mappa Web visiva e interattiva ed è un componente dell'API del controllo mappa di Azure.

4. Salvare il file e aprirlo nel browser. A questo punto si ha una mappa di base che è possibile sviluppare ulteriormente. 

   ![Visualizzare la mappa di base](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizzare il flusso del traffico

1. Aggiungere la visualizzazione del flusso di traffico alla mappa.  La funzione **map.events.add** assicura che tutte le funzioni di mappa aggiunte alla mappa vengano caricate dopo che la mappa è stata caricata completamente.

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
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
    
    map.events.add("load", function() { 
        // Add pins to the map for the start and end point of the route
        map.addPins([startPin, destinationPin], {
            name: "route-pins",
            textFont: "SegoeUi-Regular",
            textOffset: [0, -20]
        });
    });
    ```
    La chiamata della funzione **map.setCameraBounds** regola la finestra della mappa in base alle coordinate dei punti di partenza e di arrivo. La funzione **map.events.add** assicura che tutte le funzioni di mappa aggiunte alla mappa vengano caricate dopo che la mappa è stata caricata completamente. La funzione **map.addPins** dell'API aggiunge i punti al controllo mappa come componenti visivi.

3. Salvare il file e aggiornare il browser per visualizzare i punti sulla mappa. Anche se la mappa è stata dichiarata con un punto centrale in Los Angeles, **map.setCameraBounds** ha spostato la visualizzazione in modo da mostrare i punti di partenza e di arrivo.

   ![Visualizzare la mappa con i punti di partenza e di arrivo](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Classificare i percorsi in ordine di priorità in base alla modalità di trasporto

Questa sezione illustra come usare l'API del servizio di pianificazione itinerari per trovare più itinerari per raggiungere una destinazione da un determinato punto di partenza, in base alla modalità di trasporto. Il servizio di pianificazione itinerari fornisce le API per pianificare l'itinerario *più veloce*, *più breve*, *più ecologico* o *più entusiasmante* tra due punti, considerando le condizioni di traffico in tempo reale. Consente inoltre agli utenti di pianificare percorsi per il futuro usando il database dei dati storici sul traffico di Azure e fornendo stime della durata dei percorsi per qualsiasi giorno e ora. Per altre informazioni, vedere [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Ottenere le indicazioni stradali). Tutti i blocchi di codice seguenti devono essere aggiunti **all'interno del listener di eventi del caricamento mappa** per garantire che vengano caricati dopo che la mappa è stata caricata completamente.

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
    // Instantiate the service client  
    var client = new atlas.service.Client(MapsAccountKey);

    // Construct the route query string
    var routeQuery = startPoint.coordinates[1] +
        "," +
        startPoint.coordinates[0] +
        ":" +
        destinationPoint.coordinates[1] +
        "," +
        destinationPoint.coordinates[0];

    // Execute the truck route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery, {
        travelMode: "truck",
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: "USHazmatClass2"
    }).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson
            .GeoJsonRouteDirectionsResponse(response);

        // Get the first in the array of routes and add it to the map
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: truckRouteLayerName
        });
    });
    ```
    Il frammento di codice precedente crea un'istanza del client del servizio e una stringa di query per l'itinerario. Viene eseguita una query per il servizio di routing di Mappe di Azure tramite il metodo [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) e viene quindi analizzata la risposta in formato GeoJSON usando [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Viene quindi creata una matrice di coordinate per l'itinerario restituito e tale elemento viene aggiunto al livello `truckRouteLayerName` della mappa.

3. Aggiungere il codice JavaScript seguente per richiedere l'itinerario per un'auto e visualizzare i risultati:

    ```JavaScript
    // Execute the car route query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson
            .GeoJsonRouteDiraectionsResponse(response);

        // Get the first in the array of routes and add it to the map 
        map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
            name: carRouteLayerName
        });
    });
    ```
    Questo frammento di codice usa la stessa query di itinerario di un autocarro per un'auto. Viene eseguita una query per il servizio di routing di Mappe di Azure tramite il metodo [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) e viene quindi analizzata la risposta in formato GeoJSON usando [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Viene quindi creata una matrice di coordinate per l'itinerario restituito e tale elemento viene aggiunto al livello `carRouteLayerName` della mappa.

4. Salvare il file **MapTruckRoute.html** e aggiornare il browser per osservare il risultato. Se la connessione con le API di Mappe è stata stabilita correttamente, verrà visualizzata una mappa simile alla seguente.

    ![Percorsi classificati in ordine di priorità con il servizio di pianificazione percorso](./media/tutorial-prioritized-routes/prioritized-routes.png)

    L'itinerario per gli autocarri è blu e più è spesso, mentre l'itinerario per le auto è viola e più sottile. L'itinerario per le auto passa attraverso Washington Lake tramite la I-90, che attraversa i tunnel sotto le zone residenziali e pertanto è interdetto ai carichi di rifiuti pericolosi. L'itinerario per gli autocarri, che specifica il tipo di carico USHazmatClass2, suggerisce correttamente di usare un'autostrada diversa.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Visualizzare il flusso del traffico sulla mappa
> * Creare query di itinerario che dichiarano la modalità di viaggio
> * Visualizzare più itinerari sulla mappa

È possibile accedere all'esempio di codice per questa esercitazione qui:

> [Più itinerari con Mappe di Azure](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/truckRoute.html)

Per altre informazioni sulla copertura e sulle funzionalità di Mappe di Azure:

> [!div class="nextstepaction"]
> [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md)

Per altri esempi di codice e un'esperienza di codifica interattiva:

> [!div class="nextstepaction"]
> [Come usare il controllo mappa](how-to-use-map-control.md)