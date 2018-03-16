---
title: Percorsi multipli con Servizi Location Based di Azure | Microsoft Docs
description: "Trovare i percorsi per diverse modalità di trasporto tramite Servizi Location Based di Azure"
services: location-based-services
keywords: 
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 986e8667ca421baa78647e77e43fef92d81a7982
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Trovare i percorsi per diverse modalità di trasporto tramite Servizi Location Based di Azure

Questa esercitazione illustra come usare l'account Servizi Location Based di Azure e l'SDK del servizio di pianificazione percorso per trovare il percorso più adatto per raggiungere un punto di interesse in base alla modalità di trasporto selezionata. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare la query da inviare al servizio di pianificazione percorso
> * Classificare i percorsi in ordine di priorità in base alla modalità di trasporto

## <a name="prerequisites"></a>prerequisiti

Prima di procedere, [creare l'account Servizi Location Based di Azure](./tutorial-search-location.md#createaccount) e [ottenere una chiave dall'account](./tutorial-search-location.md#getkey). Può anche essere utile esaminare le istruzioni per l'uso delle API del servizio di ricerca e del controllo mappa riportate nell'esercitazione [Cercare un punto di interesse vicino tramite Servizi Location Based di Azure](./tutorial-search-location.md) e anche apprendere le nozioni di base sull'uso delle API del servizio di pianificazione percorso, come illustrato nell'esercitazione [Trovare il percorso per raggiungere un punto di interesse tramite Servizi Location Based di Azure](./tutorial-route-location.md).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Configurare la query da inviare al servizio di pianificazione percorso

Seguire questa procedura per creare una pagina HTML statica incorporata usando l'API del controllo mappa di Servizi Location Based. 

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
    Si noti che nell'intestazione HTML sono incorporati i percorsi delle risorse per i file CSS e JavaScript relativi alla libreria di Servizi Location Based di Azure. Si noti inoltre il segmento *script* nel corpo del file HTML, che deve contenere il codice JavaScript inline per l'accesso all'API del controllo mappa di Azure.
3. Aggiungere il codice JavaScript seguente al blocco *script* del file HTML. Sostituire il segnaposto *<insert-key>* con la chiave primaria dell'account Servizi Location Based.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** fornisce il controllo per una mappa Web visiva e interattiva ed è un componente dell'API del controllo mappa di Azure.

4. Nel blocco *script* aggiungere il codice JavaScript seguente per la visualizzazione del flusso del traffico sulla mappa:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Questo codice imposta il flusso del traffico su `relative`, che corrisponde alla velocità sulla strada in relazione a condizioni di traffico libero. È anche possibile impostare il flusso del traffico su `absolute`, ovvero sul valore assoluto della velocità sulla strada, o su `relative-delay`, che visualizza la velocità relativa in caso di variazione rispetto alle condizioni di traffico libero. 

5. Aggiungere il codice JavaScript seguente per creare i segnaposto per i punti iniziale e finale del percorso:

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

6. Aggiungere il codice JavaScript seguente per aggiungere livelli di *linestrings* al controllo mappa, in modo da visualizzare i percorsi in base alla modalità di trasporto, ad esempio _car_ e _truck_.

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

7. Aggiungere il codice JavaScript seguente per includere nella mappa i punti iniziale e finale:

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
    La funzione **map.setCameraBounds** dell'API regola la finestra della mappa in base alle coordinate dei punti iniziale e finale. La funzione **map.addPins** dell'API aggiunge i punti al controllo mappa come componenti visivi.

8. Salvare il file **MapTruckRoute.html** nel computer. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Classificare i percorsi in ordine di priorità in base alla modalità di trasporto

Questa sezione illustra come usare l'API del servizio di pianificazione percorso di Servizi Location Based di Azure per trovare più percorsi per raggiungere una destinazione da un determinato punto di partenza, in base alla modalità di trasporto. Il servizio di pianificazione percorso fornisce le API per pianificare il percorso più veloce, più breve o più ecologico tra due punti, considerando le condizioni di traffico in tempo reale. Consente inoltre agli utenti di pianificare percorsi per il futuro usando il database dei dati storici sul traffico di Azure e fornendo stime della durata dei percorsi per qualsiasi giorno e ora. 

1. Aprire il file **MapTruckRoute.html** creato nella sezione precedente e aggiungere il codice JavaScript seguente al blocco *script* per ottenere il percorso di un autocarro tramite il servizio di pianificazione percorso.

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
    truckRouteUrl += "&subscription-key=" + LBSAccountKey;
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
    
    Il frammento di codice invia anche la query al servizio di pianificazione percorso, per ottenere il percorso relativo ai punti iniziale e finale specificati, per la chiave dell'account. Per indicare il percorso per un autocarro pesante vengono usati i parametri facoltativi seguenti:
   - Il parametro `travelMode=truck` specifica la modalità di trasporto *truck*. Le altre modalità di trasporto supportate sono *taxi*, *bus*, *van*, *motorcycle* e la modalità predefinita *car*.
   - I parametri `vehicleWidth`, `vehicleHeight` e `vehicleLength` specificano le dimensioni del veicolo in metri e vengono considerate solo se è impostata la modalità di trasporto *truck*.
   - Il parametro `vehicleLoadType` classifica il carico come pericoloso e soggetto a limitazioni su alcune strade. Anche questo parametro viene considerato solo per la modalità *truck*.

2. Aggiungere il codice JavaScript seguente per ottenere il percorso di un'automobile usando il servizio di pianificazione percorso:

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
    carRouteUrl += "&subscription-key=" + LBSAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Questo frammento di codice crea un altro oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) e aggiunge un gestore eventi per analizzare la risposta in ingresso. Per ottenere una risposta, crea una matrice di coordinate per il percorso restituito e aggiunge tale elemento al livello `carRouteLayerName` della mappa. 
    
    Il frammento di codice invia anche la query al servizio di pianificazione percorso, per ottenere il percorso relativo ai punti iniziale e finale specificati, per la chiave dell'account. Poiché non sono impostati altri parametri, viene restituito il percorso per la modalità di trasporto *car* predefinita. 

3. Salvare il file **MapTruckRoute.html** in locale, aprirlo in un Web browser di propria scelta e osservare il risultato. Se la connessione con le API di Servizi Location Based è stata stabilita correttamente, verrà visualizzata una mappa simile alla seguente. 

    ![Percorsi classificati in ordine di priorità con il servizio di pianificazione percorso](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Si noti che il percorso dell'autocarro è di colore blu, mentre quello dell'auto è di colore porpora.

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la query da inviare al servizio di pianificazione percorso
> * Classificare i percorsi in ordine di priorità in base alla modalità di trasporto

Proseguire con gli articoli inclusi in **Concetti** e **Guide alle procedure** per approfondimenti sull'SDK di Servizi Location Based di Azure. 
