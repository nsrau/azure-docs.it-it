---
title: Trovare un percorso con Servizi Location Based di Azure | Microsoft Docs
description: Trovare il percorso per raggiungere un punto di interesse tramite Servizi Location Based di Azure
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7303347444952d9c09dc6c04eea5b962e18729b4
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Trovare il percorso per raggiungere un punto di interesse tramite Servizi Location Based di Azure

Questa esercitazione illustra come usare l'account Servizi Location Based di Azure e l'SDK del servizio di pianificazione percorso per trovare il percorso per raggiungere un determinato punto di interesse. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Ottenere le coordinate di un indirizzo
> * Inviare una query al servizio di pianificazione percorso per ottenere le indicazioni stradali per un punto di interesse

## <a name="prerequisites"></a>prerequisiti

Prima di procedere, [creare l'account Servizi Location Based di Azure](./tutorial-search-location.md#createaccount) e [ottenere la chiave di sottoscrizione per l'account](./tutorial-search-location.md#getkey). Può anche essere utile esaminare le istruzioni per l'uso delle API del servizio di ricerca e del controllo mappa riportate nell'esercitazione [Cercare un punto di interesse vicino tramite Servizi Location Based di Azure](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Ottenere le coordinate di un indirizzo

Seguire questa procedura per creare una pagina HTML statica incorporata usando l'API del controllo mappa di Servizi Location Based. 

1. Nel computer locale creare un nuovo file con il nome **MapRoute.html**. 
2. Aggiungere al file i componenti HTML seguenti:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
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
    Si noti che nell'intestazione HTML sono incorporati i percorsi delle risorse per i file CSS e JavaScript relativi alla libreria di Servizi Location Based di Azure. Si noti inoltre il segmento *script* nel corpo del file HTML, che deve contenere il codice JavaScript inline per l'accesso alle API di Servizi Location Based di Azure.

3. Aggiungere il codice JavaScript seguente al blocco *script* del file HTML. Usare la chiave primaria dell'account Servizi Location Based nello script.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** fornisce il controllo per una mappa Web visiva e interattiva ed è un componente dell'API del controllo mappa di Azure.

4. Aggiungere il codice JavaScript seguente al blocco *script*. In questo modo si introduce un livello di *linestrings* nel controllo mappa per mostrare il percorso:

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

5. Aggiungere il codice JavaScript seguente per creare i punti iniziale e finale del percorso:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Questo codice crea due [oggetti GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) per rappresentare i punti iniziale e finale del percorso. Il punto finale è il risultato della combinazione di latitudine/longitudine per una delle *stazioni di servizio* cercate nell'esercitazione precedente [Cercare un punto di interesse vicino tramite Servizi Location Based di Azure](./tutorial-search-location.md).

6. Aggiungere il codice JavaScript seguente per includere nella mappa i segnaposto per i punti iniziale e finale:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    La funzione **map.setCameraBounds** dell'API regola la finestra della mappa in base alle coordinate dei punti iniziale e finale. La funzione **map.addPins** dell'API aggiunge i punti al controllo mappa come componenti visivi.

7. Salvare il file **MapRoute.html** nel computer. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Inviare una query al servizio di pianificazione percorso per ottenere le indicazioni stradali per un punto di interesse

Questa sezione illustra come usare l'API del servizio di pianificazione percorso di Servizi Location Based di Azure per trovare il percorso per raggiungere una destinazione da un determinato punto di partenza. Il servizio di pianificazione percorso fornisce le API per pianificare il percorso più veloce, più breve o più ecologico tra due punti, considerando le condizioni di traffico in tempo reale. Consente inoltre agli utenti di pianificare percorsi per il futuro usando il database dei dati storici sul traffico di Azure e fornendo stime della durata dei percorsi per qualsiasi giorno e ora. 

1. Aprire il file **MapRoute.html** creato nella sezione precedente e aggiungere il codice JavaScript seguente al blocco *script* per illustrare il servizio di pianificazione percorso.

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```
    Questo frammento di codice crea un oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) e aggiunge un gestore eventi per analizzare la risposta in ingresso. Per ottenere una risposta, crea una matrice di coordinate per i segmenti del primo percorso restituito. Aggiunge quindi il set di coordinate per il percorso al livello di *linestrings* della mappa.

2. Aggiungere il codice seguente al blocco *script* per inviare l'oggetto XMLHttpRequest al servizio di pianificazione percorso di Servizi Location Based di Azure:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    La richiesta precedente mostra i parametri obbligatori, ovvero la chiave dell'account e le coordinate per i punti iniziale e finale, nell'ordine indicato. 

3. Salvare il file **MapRoute.html** in locale, aprirlo in un Web browser di propria scelta e osservare il risultato. Se la connessione con le API di Servizi Location Based è stata stabilita correttamente, verrà visualizzata una mappa simile alla seguente. 

    ![Controllo mappa e servizio di pianificazione percorso di Azure](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Ottenere le coordinate di un indirizzo
> * Inviare una query al servizio di pianificazione percorso per ottenere le indicazioni stradali per un punto di interesse

Proseguire con l'esercitazione [Trovare i percorsi per diverse modalità di viaggio tramite Servizi Location Based di Azure](./tutorial-prioritized-routes.md) per informazioni su come usare Servizi Location Based di Azure per individuare i percorsi più adatti per raggiungere un punto di interesse in base alla modalità di trasporto. 
