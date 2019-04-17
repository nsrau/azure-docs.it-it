---
title: Più itinerari con Mappe di Azure | Microsoft Docs
description: Trovare gli itinerari per diverse modalità di trasporto tramite Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: db01c2f51e9069e8fc9ee979eacf746bee8dbdd2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260919"
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
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>

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

    Si noti che l'intestazione HTML include i file di risorse CSS e JavaScript ospitati dalla libreria del controllo mappa di Azure. Notare l'evento `onload` nel corpo della pagina, che chiamerà la funzione `GetMap` dopo il caricamento del corpo della pagina. Questa funzione deve contenere il codice JavaScript inline per l'accesso alle API di Mappe di Azure.

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

    La classe `atlas.Map` fornisce il controllo per una mappa Web visiva e interattiva ed è un componente dell'API del controllo mappa di Azure.

4. Salvare il file e aprirlo nel browser. A questo punto si ha una mappa di base che è possibile sviluppare ulteriormente.

   ![Visualizzare la mappa di base](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizzare il flusso del traffico

1. Aggiungere la visualizzazione del flusso di traffico alla mappa. L'evento `ready` delle mappe aspetta finché non vengono caricate le risorse della mappa con cui è possibile interagire in sicurezza.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    Nel gestore dell'evento `ready` il valore dell'impostazione relativa al flusso del traffico nella mappa è `relative`, che corrisponde alla velocità sulla strada in relazione a condizioni di traffico regolare. È anche possibile impostare il flusso del traffico su `absolute`, ovvero sul valore assoluto della velocità sulla strada, o su `relative-delay`, che visualizza la velocità relativa in caso di variazione rispetto alle condizioni di traffico libero.

2. Salvare il file **MapTruckRoute.html** e aggiornare la pagina nel browser. Se si interagisce con la mappa e si fa zoom in avanti su Los Angeles, si dovrebbero visualizzare le strade con i dati aggiornati sul traffico.

   ![Visualizzare la mappa con il traffico](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definire il rendering dell'itinerario

In questa esercitazione verranno calcolati due itinerari e ne verrà eseguito il rendering nella mappa. Un itinerario usa le strade accessibili alle auto e l'altro quelle accessibile agli autocarri. Quando viene eseguito il rendering verranno visualizzate icone di simbolo per l'inizio e la fine dell'itinerario e linee di colore diverso per ogni percorso dell'itinerario.

1. Dopo aver inizializzato la mappa, aggiungere il codice JavaScript seguente nel gestore dell'evento `ready` delle mappe.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

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
    });
    ```
    
    Nel gestore dell'evento `ready` delle mappe viene creata un'origine dati per archiviare le linee relative all'itinerario e i punti di partenza e di arrivo. Viene creato un livello linea che viene allegato all'origine dati per definire il rendering della linea dell'itinerario. Le espressioni consentono di recuperare il colore e lo spessore della linea dalle proprietà della funzionalità delle linea dell'itinerario. Quando si aggiunge il livello alla mappa, viene passato un secondo parametro con valore `'labels'`, per indicare che il rendering di questo livello deve essere eseguito sotto le etichette della mappa. In questo modo la linea dell'itinerario non coprirà le etichette delle strade. Viene creato un livello simboli che viene allegato all'origine dati. Questo livello consente di specificare in che modo verrà eseguito il rendering dei punti di partenza e di arrivo. In questo caso sono state aggiunte espressioni per recuperare le informazioni sull'icona dell'immagine e il testo dell'etichetta dalle proprietà di ogni oggetto punto. 
    
2. Per questa esercitazione impostare come punto di partenza una società fittizia di Seattle denominata Fabrikam e come punto di arrivo un ufficio di Microsoft. Nel gestore dell'evento `ready` delle mappe aggiungere il codice seguente.

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
    ```

    Questo codice crea due [oggetti GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) per rappresentare i punti iniziale e finale del percorso. A ogni punto vengono aggiunge le proprietà `title` e `icon`.

3. Aggiungere quindi il codice JavaScript seguente per includere nella mappa i segnaposto per i punti di partenza e di arrivo:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    I punti di partenza e di arrivo vengono aggiunti all'origine dati. Per il calcolo del rettangolo di selezione viene usata la funzione `atlas.data.BoundingBox.fromData`. Questo rettangolo di selezione viene usato per impostare la visualizzazione delle videocamere della mappa sull'intero percorso con la funzione `map.setCamera`. Per compensare le dimensioni in pixel delle icone di simbolo, viene aggiunta una spaziatura interna.

4. Salvare il file e aggiornare il browser per visualizzare i punti sulla mappa. Ora la mappa è centrata su Seattle ed è possibile vedere il segnaposto blu rotondo che indica il punto di partenza e il segnaposto blu che indica il punto di arrivo.

   ![Visualizzare la mappa con i punti di partenza e di arrivo](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Classificare i percorsi in ordine di priorità in base alla modalità di trasporto

Questa sezione illustra come usare l'API del servizio di pianificazione itinerari per trovare più itinerari per raggiungere una destinazione da un determinato punto di partenza, in base alla modalità di trasporto. Il servizio di pianificazione itinerari fornisce le API per pianificare l'itinerario *più veloce*, *più breve*, *più ecologico* o *più entusiasmante* tra due punti, considerando le condizioni di traffico in tempo reale. Consente inoltre agli utenti di pianificare percorsi per il futuro usando il database dei dati storici sul traffico di Azure e fornendo stime della durata dei percorsi per qualsiasi giorno e ora. Per altre informazioni, vedere [Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Ottenere le indicazioni stradali). Tutti i blocchi di codice seguenti devono essere aggiunti **all'interno del listener di eventi del caricamento mappa** per garantire che vengano caricati dopo che la mappa è stata caricata completamente.

1. Nella funzione GetMap aggiungere il codice JavaScript seguente.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` crea un elemento `SubscriptionKeyCredentialPolicy` per autenticare le richieste HTTP in Mappe di Azure con la chiave di sottoscrizione. `atlas.service.MapsURL.newPipeline()` acquisisce il criterio `SubscriptionKeyCredential` e crea un'istanza [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest). `routeURL` rappresenta un URL per le operazioni di [pianificazione del percorso](https://docs.microsoft.com/rest/api/maps/route) di Mappe di Azure.

2. Dopo aver configurato le credenziali e l'URL, aggiungere il codice JavaScript seguente per creare un percorso dalla partenza al punto di arrivo per un autocarro che trasporta carico classificato come USHazmatClass2 e visualizzare i risultati.

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

    Questo frammento di codice sopra indicato esegue query sul servizio di routing di Mappe di Azure tramite il metodo [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest). La linea dell'itinerario viene estratta dalla raccolta di funzionalità GeoJSON dalla risposta ottenuta usando il metodo `geojson.getFeatures()`. La linea di percorso viene quindi aggiunta all'origine dati. Aggiunge inoltre un indice pari a 0 per assicurarsi che ne venga eseguito il rendering prima di tutte le altre linee nell'origine dati. Il calcolo dell'itinerario per gli autocarri sarà infatti spesso più lento rispetto q quello dell'itinerario per le auto, di conseguenza se la linea dell'itinerario per gli autocarri viene aggiunto all'origine dati dopo quello per le auto, ne verrà eseguito il rendering su quest'ultimo. Alla linea dell'itinerario per gli autocarri vengono aggiunte due proprietà, un colore di tratto con sfumatura blu e uno spessore tratto di 9 pixel.

3. Aggiungere il codice JavaScript seguente per creare l'itinerario per un'auto e visualizzare i risultati.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Questo frammento di codice sopra indicato esegue query sul servizio di routing di Mappe di Azure tramite il metodo [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest). La linea dell'itinerario viene estratta dalla raccolta di funzionalità GeoJSON dalla risposta ottenuta usando il metodo `geojson.getFeatures()`. La linea di percorso viene quindi aggiunta all'origine dati. Alla linea dell'itinerario per le auto vengono aggiunte due proprietà, un colore di tratto con sfumatura viola e uno spessore tratto di 5 pixel.  

4. Salvare il file **MapTruckRoute.html** e aggiornare il browser per osservare il risultato. Se la connessione con le API di Mappe è stata stabilita correttamente, verrà visualizzata una mappa simile alla seguente.

    ![Percorsi classificati in ordine di priorità con il servizio di pianificazione percorso](./media/tutorial-prioritized-routes/prioritized-routes.png)

    L'itinerario per gli autocarri è blu e più è spesso, mentre l'itinerario per le auto è viola e più sottile. L'itinerario per le auto passa attraverso Washington Lake tramite la I-90, che attraversa i tunnel sotto le zone residenziali e pertanto è interdetto ai carichi di rifiuti pericolosi. L'itinerario per gli autocarri, che specifica il tipo di carico USHazmatClass2, suggerisce correttamente di usare un'autostrada diversa.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Visualizzare il flusso del traffico sulla mappa
> * Creare query di itinerario che dichiarano la modalità di viaggio
> * Visualizzare più itinerari sulla mappa

È possibile accedere all'esempio di codice per questa esercitazione qui:

> [Più itinerari con Mappe di Azure](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[Vedere qui l'esempio in tempo reale](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

L'esercitazione successiva illustra il processo di creazione di un semplice localizzatore di punti vendita con Mappe di Azure.

> [!div class="nextstepaction"]
> [Creare un localizzatore di punti vendita con Mappe di Azure](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)