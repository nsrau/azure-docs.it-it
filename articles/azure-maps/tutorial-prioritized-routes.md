---
title: 'Esercitazione: Trovare più itinerari per modalità di trasporto | Mappe di Microsoft Azure'
description: Esercitazione su come usare Mappe di Azure per trovare gli itinerari per modalità di viaggio specifiche verso i punti di interesse. Vedere come visualizzare più itinerari sulle mappe.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 0d57e86088ee472c63b433bde14a0e4316cc20a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321748"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>Esercitazione: Trovare e visualizzare percorsi per diverse modalità di viaggio con Mappe di Azure

Questa esercitazione illustra come usare il [servizio di pianificazione percorso](https://docs.microsoft.com/rest/api/maps/route) e il [controllo mappa](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) di Mappe di Azure per visualizzare le indicazioni stradali sia per veicoli privati che commerciali (camion) con tipo di carico `USHazmatClass2`. Verrà inoltre illustrato come visualizzare i dati sul traffico in tempo reale su una mappa. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare e visualizzare il controllo mappa in una pagina Web
> * Eseguire il rendering dei dati sul traffico in tempo reale su una mappa
> * Richiedere e visualizzare i percorsi di veicoli privati e commerciali su una mappa

## <a name="prerequisites"></a>Prerequisiti

1. Accedere al [portale di Azure](https://portal.azure.com).

2. [Creare un account di Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](how-to-manage-authentication.md).

È possibile ottenere il codice sorgente completo per l'esempio [qui](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html). Un esempio eseguibile è disponibile [qui](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

## <a name="create-a-new-web-page-using-the-map-control-api"></a>Creare una nuova pagina Web usando l'API del controllo mappa

La procedura seguente illustra come creare e visualizzare il controllo mappa in una pagina Web.

1. Nel computer locale creare un nuovo file con il nome **MapTruckRoute.html**.
2. Copiare e incollare il markup HTML seguente nel file.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

     L'intestazione HTML include i file di risorse CSS e JavaScript ospitati dalla libreria del controllo mappa di Azure. L'evento `onload` del corpo chiama la funzione `GetMap`. Nel passaggio successivo verrà aggiunto il codice di inizializzazione del controllo mappa.

3. Aggiungere il codice JavaScript seguente alla funzione `GetMap`. Sostituire la stringa `<Your Azure Maps Key>` con la chiave primaria copiata dall'account Mappe.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

4. Salvare il file e aprirlo nel browser. Viene visualizzata una mappa semplice.

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="Rendering di una mappa di base del controllo mappa":::

## <a name="render-real-time-traffic-data-on-a-map"></a>Eseguire il rendering dei dati sul traffico in tempo reale su una mappa

1. Aggiungere il codice JavaScript seguente alla funzione `GetMap`. Questo codice implementa il gestore dell'evento `ready` del controllo mappa. Il resto del codice in questa esercitazione verrà inserito all'interno del gestore dell'evento `ready`.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    Nel gestore dell'evento `ready` della mappa il valore dell'impostazione relativa al flusso del traffico è `relative`, che corrisponde alla velocità sulla strada in relazione a condizioni di traffico regolare. Per altre opzioni sul traffico, vedere l'[interfaccia TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions?view=azure-maps-typescript-latest&preserve-view=false).

2. Salvare il file **MapTruckRoute.html** e aggiornare la pagina nel browser. Se si fa zoom avanti su una città, ad esempio Los Angeles, si noterà che le strade vengono visualizzate con i dati aggiornati sul flusso del traffico.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="Rendering di una mappa di base del controllo mappa":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>Definire il rendering della visualizzazione del percorso

In questa esercitazione verranno calcolati due itinerari e ne verrà eseguito il rendering nella mappa. Il primo percorso verrà calcolato per un veicolo privato (auto). Il secondo verrà calcolato per un veicolo commerciale (camion) per mostrare la differenza tra i risultati. Quando ne viene eseguito il rendering, la mappa visualizza l'icona di un simbolo per i punti iniziale e finale del percorso, oltre a figure geometriche con linee di colore diverso per ogni tragitto del percorso. Per altre informazioni sull'aggiunta di livelli linea, vedere [Aggiungere un livello linea a una mappa](map-add-line-layer.md). Per altre informazioni sui livelli simbolo, vedere [Aggiungere un livello simbolo a una mappa](map-add-pin.md).

1. Nel gestore dell'evento `ready` del controllo mappa aggiungere il codice seguente.

    ```JavaScript

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Add a layer for rendering the route lines and have it render under the map labels.
    map.layers.add(new atlas.layer.LineLayer(datasource, null, {
        strokeColor: ['get', 'strokeColor'],
        strokeWidth: ['get', 'strokeWidth'],
        lineJoin: 'round',
        lineCap: 'round'
    }), 'labels');

    //Add a layer for rendering point data.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: ['get', 'icon'],
            allowOverlap: true
        },
        textOptions: {
            textField: ['get', 'title'],
            offset: [0, 1.2]
        },
        filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
    }));

    ```


    Nel gestore dell'evento `ready` del controllo mappa viene creata un'origine dati per archiviare il percorso dall'inizio alla fine. Le [espressioni](data-driven-style-expressions-web-sdk.md) consentono di recuperare il colore e lo spessore delle linee dalle proprietà della funzionalità delle linea del percorso. Per assicurarsi che la linea del percorso non copra le etichette stradali, è stato passato un secondo parametro con il valore `'labels'`.

    Viene poi creato un livello simbolo collegato all'origine dati. Questo livello specifica come viene eseguito il rendering dei punti iniziale e finale. Sono state aggiunte espressioni per recuperare l'immagine dell'icona e le informazioni sulle etichette di testo dalle proprietà in ogni oggetto punto. Per altre informazioni sulle espressioni, vedere [Espressioni di stile basate su dai](data-driven-style-expressions-web-sdk.md).

2. Impostare un'azienda fittizia di Seattle, Fabrikam, come punto iniziale e un ufficio di Microsoft come punto finale.  Nel gestore dell'evento `ready` del controllo mappa aggiungere il codice seguente.


    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    Questo codice crea due [oggetti punto GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) per rappresentare i punti di partenza e di arrivo, che vengono quindi aggiunti all'origine dati.

    L'ultimo blocco di codice imposta la visualizzazione della fotocamera tramite le informazioni di latitudine e longitudine del punto di partenza e di arrivo. I punti di partenza e di arrivo vengono aggiunti all'origine dati. Per il calcolo del rettangolo di selezione viene usata la funzione `atlas.data.BoundingBox.fromData`. Questo rettangolo di selezione viene usato per impostare la visualizzazione delle videocamere della mappa sull'intero percorso con la funzione `map.setCamera`. Per compensare le dimensioni in pixel delle icone di simbolo, viene aggiunta una spaziatura interna. Per altre informazioni sulla proprietà setCamera del controllo mappa, vedere [setCamera(CameraOptions | CameraBoundsOptions e AnimationOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false).

3. Salvare il file **TruckRoute.html** e aggiornare il browser. La mappa è ora centrata sulla città di Seattle. L'indicatore blu a goccia contrassegna il punto di partenza. L'indicatore rotondo a goccia contrassegna il punto di arrivo.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="Rendering di una mappa di base del controllo mappa":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>Richiedere e visualizzare i percorsi di veicoli privati e commerciali su una mappa

Questa sezione illustra come usare il servizio di pianificazione percorso di Mappe di Azure per ottenere le indicazioni da un punto a un altro, in base alla modalità di trasporto. Verranno usate due modalità di trasporto: camion e auto.

>[!TIP]
>Il servizio di pianificazione percorso include le API per pianificare il percorso *più veloce*, *più breve*, *più ecologico* o *più entusiasmante* tra due punti, in base alla distanza, alle condizioni del traffico e alla modalità di trasporto usata. Il servizio consente anche di pianificare percorsi futuri in base alle condizioni storiche del traffico. Gli utenti possono visualizzare la durata stimata del percorso per qualsiasi ora specificata. Per altre informazioni, vedere [API Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

1. Nella funzione `GetMap`, all'interno del gestore dell'evento `ready` del controllo, aggiungere quanto segue al codice JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` crea un elemento `SubscriptionKeyCredentialPolicy` per autenticare le richieste HTTP in Mappe di Azure con la chiave di sottoscrizione. `atlas.service.MapsURL.newPipeline()` acquisisce il criterio `SubscriptionKeyCredential` e crea un'istanza [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline). `routeURL` rappresenta un URL per le operazioni di [pianificazione del percorso](https://docs.microsoft.com/rest/api/maps/route) di Mappe di Azure.

2. Dopo aver configurato le credenziali e l'URL, aggiungere il codice JavaScript seguente per creare il percorso per un camion dal punto iniziale a quello finale. Questo percorso viene creato e visualizzato per un camion che trasporta un carico di classi `USHazmatClass2`.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Il codice precedente esegue una query sul servizio di pianificazione percorso di Mappe di Azure tramite l'[API di indicazioni stradali di Mappe di Azure ](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-). La linea dell'itinerario viene estratta dalla raccolta di funzionalità GeoJSON dalla risposta ottenuta usando il metodo `geojson.getFeatures()`. Infine, la linea del percorso viene aggiunta all'origine dati. Viene aggiunta in corrispondenza dell'indice 0, per assicurarsi che il rendering del percorso del camion venga eseguito prima di qualsiasi altra linea dell'origine dati, perché il calcolo del percorso del camion è spesso più lento di quello di un'auto. Se la linea del percorso per autocarri viene aggiunta all'origine dati dopo quella del percorso per auto, il rendering verrà eseguito sopra di essa. Alla linea del percorso del camion vengono aggiunte due proprietà, un colore di tratto blu e uno spessore di tratto di 9 pixel.

    >[!TIP]
    > Per vedere tutte le opzioni e i valori possibili per l'API di indicazioni stradali di Mappe di Azure, vedere [Parametri dell'URI per POST di indicazioni percorso](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#uri-parameters).

3. Aggiungere ora il codice JavaScript seguente per creare un percorso per un'auto.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    Il codice precedente esegue una query sul servizio di pianificazione percorso di Mappe di Azure tramite il metodo dell'[API di indicazioni stradali di Mappe di Azure ](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-). La linea dell'itinerario viene estratta dalla raccolta di funzionalità GeoJSON dalla risposta ottenuta usando il metodo `geojson.getFeatures()`. Infine, la linea del percorso viene aggiunta all'origine dati. Alla linea del percorso dell'auto vengono aggiunte due proprietà, un colore di tratto viola e uno spessore di tratto di 5 pixel.

4. Salvare il file **TruckRoute.html** e aggiornare il Web browser. La mappa dovrebbe ora visualizzare i percorsi del camion e dell'auto.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Rendering di una mappa di base del controllo mappa":::

    Il percorso del camion viene visualizzato con una linea spessa blu. Il percorso dell'auto viene visualizzato con una linea sottile viola. Il percorso dell'auto attraversa il lago Washington tramite l'autostrada I-90, passando nei tunnel sotto le aree residenziali. e pertanto è vietato per i carichi di rifiuti pericolosi. Il percorso del camion, che specifica un tipo di carico `USHazmatClass2`, è indirizzato verso un'autostrada diversa.

È possibile ottenere il codice sorgente completo per l'esempio [qui](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html). Un esempio eseguibile è disponibile [qui](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

È possibile anche [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)



## <a name="next-steps"></a>Passaggi successivi

L'esercitazione successiva illustra il processo di creazione di un semplice localizzatore di punti vendita con Mappe di Azure.

> [!div class="nextstepaction"]
> [Creare un localizzatore di punti vendita con Mappe di Azure](./tutorial-create-store-locator.md)