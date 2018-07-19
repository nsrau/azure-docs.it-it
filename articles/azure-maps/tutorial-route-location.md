---
title: Trovare un percorso con Mappe di Azure | Microsoft Docs
description: Trovare il percorso per raggiungere un punto di interesse usando Mappe di Azure
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 09828fade464c3b7b5f6eedaa16513e9eab49467
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989643"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Trovare il percorso per raggiungere un punto di interesse usando Mappe di Azure

Questa esercitazione illustra come usare l'account Mappe di Azure e l'SDK del servizio di pianificazione percorso per trovare il percorso per raggiungere un determinato punto di interesse. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Impostare le coordinate di un indirizzo
> * Inviare una query al servizio di pianificazione percorso per ottenere le indicazioni stradali per un punto di interesse

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, seguire i passaggi dell'esercitazione precedente per [creare l'account Mappe di Azure](./tutorial-search-location.md#createaccount) e [ottenere la chiave di sottoscrizione per l'account](./tutorial-search-location.md#getkey). 

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Creare una nuova mappa 
La procedura seguente illustra come creare una pagina HTML statica incorporata usando l'API del controllo mappa. 

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
    L'intestazione HTML incorpora le posizioni delle risorse per i file CSS e JavaScript relative alla libreria di Mappe di Azure. Il segmento *script* nel corpo del file HTML deve contenere il codice JavaScript inline per l'accesso alle API di Mappe di Azure.

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

   ![Visualizzare la mappa di base](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>Impostare i punti di partenza e di arrivo

Per questa esercitazione, impostare Microsoft come punto di partenza e un distributore a Seattle come punto di destinazione. 

1. Nello stesso blocco di *script* del file **MapRoute.html** aggiungere il codice JavaScript seguente per creare i punti di partenza e di arrivo:

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
    Questo codice crea due [oggetti GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) per rappresentare i punti iniziale e finale del percorso. 

2. Aggiungere il codice JavaScript seguente per includere nella mappa i segnaposto per i punti iniziale e finale:

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
    La funzione **map.setCameraBounds** regola la finestra della mappa in base alle coordinate dei punti di partenza e di arrivo. La funzione **map.addPins** dell'API aggiunge i punti al controllo mappa come componenti visivi.

7. Salvare il file **MapRoute.html** e aggiornare il browser. Ora la mappa è centrata su Seattle ed è possibile vedere il segnaposto blu rotondo che indica il punto di partenza e il segnaposto blu che indica il punto di arrivo.

   ![Visualizzare la mappa con i punti di partenza e di arrivo contrassegnati](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Ottenere le indicazioni stradali

Questa sezione illustra come usare l'API del servizio di pianificazione percorso di Mappe per trovare il percorso per raggiungere una destinazione da un determinato punto di partenza. Il servizio di pianificazione percorso fornisce le API per pianificare il percorso *più veloce*, *più breve*, *più ecologico* o *più interessante* tra due posizioni. Consente inoltre agli utenti di pianificare percorsi per il futuro usando il database dei dati storici sul traffico di Azure e fornendo stime della durata dei percorsi per qualsiasi giorno e ora. Per altre informazioni, vedere [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Ottenere le indicazioni stradali).


1. Aggiungere prima un nuovo livello sulla mappa per visualizzare l'itinerario, ovvero l'elemento *linestring*. Aggiungere il codice JavaScript seguente al blocco *script*:

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

2. Creare un oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) e aggiungere un gestore eventi per analizzare la risposta JSON inviata dal servizio di pianificazione percorso di Mappe. Questo codice costruisce una matrice di coordinate per i segmenti di linea del percorso, quindi aggiunge il set di coordinate al livello linestring della mappa. 

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

3. Aggiungere il codice seguente per compilare la query e inviare l'oggetto XMLHttpRequest al servizio di pianificazione percorso di Mappe:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "api-version=1.0";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```

3. Salvare il file **MapRoute.html** e aggiornare il Web browser. Se la connessione con le API di Mappe è stata stabilita correttamente, verrà visualizzata una mappa simile alla seguente. 

    ![Controllo mappa e servizio di pianificazione percorso di Azure](./media/tutorial-route-location/map-route.png)


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Impostare le coordinate di un indirizzo
> * Inviare una query al servizio di pianificazione percorso per ottenere le indicazioni stradali per un punto di interesse

L'esercitazione successiva illustra come creare una query del percorso con restrizioni, ad esempio la modalità di trasporto o il tipo di carico, e come visualizzare più percorsi sulla mappa stessa. 

> [!div class="nextstepaction"]
> [Find routes for different modes of travel (Trovare i percorsi per diverse modalità di trasporto)](./tutorial-prioritized-routes.md)
