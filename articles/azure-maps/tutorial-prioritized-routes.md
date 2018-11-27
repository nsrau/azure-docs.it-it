---
title: Più itinerari con Mappe di Azure | Microsoft Docs
description: Trovare gli itinerari per diverse modalità di trasporto tramite Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0a278eb1612ec9573c4d12611ccce2d1b5b971bc
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705280"
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
        <title>Multiple routes by mode of travel</title>
        
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

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
                position: relative;
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

3. Aggiungere il codice JavaScript seguente alla funzione `GetMap`. Sostituire la stringa **\<Your Azure Maps Key\>** con la chiave primaria copiata dall'account Mappe.

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    **atlas.Map** fornisce il controllo per una mappa Web visiva e interattiva ed è un componente dell'API del controllo mappa di Azure.

4. Salvare il file e aprirlo nel browser. A questo punto si ha una mappa di base che è possibile sviluppare ulteriormente.

   ![Visualizzare la mappa di base](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizzare il flusso del traffico

1. Aggiungere la visualizzazione del flusso di traffico alla mappa. La funzione `map.events.add` assicura che tutte le funzioni di mappa aggiunte alla mappa vengano caricate dopo che la mappa è stata caricata completamente.

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     Alla mappa viene aggiunto un evento di caricamento che verrà attivato dopo che le risorse della mappa sono state caricate completamente. Nel gestore dell'evento di caricamento della mappa il valore dell'impostazione relativa al flusso del traffico nella mappa è `relative`, che corrisponde alla velocità sulla strada in relazione a condizioni di traffico libero. È anche possibile impostare il flusso del traffico su `absolute`, ovvero sul valore assoluto della velocità sulla strada, o su `relative-delay`, che visualizza la velocità relativa in caso di variazione rispetto alle condizioni di traffico libero.

2. Salvare il file **MapTruckRoute.html** e aggiornare la pagina nel browser. Dovrebbero comparire le strade di Los Angeles con i dati del traffico in tempo reale.

   ![Visualizzare la mappa con il traffico](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definire il rendering dell'itinerario

In questa esercitazione verranno calcolati due itinerari e ne verrà eseguito il rendering nella mappa. Un itinerario usa le strade accessibili alle auto e l'altro quelle accessibile agli autocarri. Quando viene eseguito il rendering verranno visualizzate icone di simbolo per l'inizio e la fine dell'itinerario e linee di colore diverso per ogni percorso dell'itinerario.

1. Dopo aver inizializzato la mappa, aggiungere il codice JavaScript seguente nella funzione GetMap.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
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
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    Alla mappa viene aggiunto un evento di caricamento che verrà attivato dopo che le risorse della mappa sono state caricate completamente. Nel gestore dell'evento di caricamento della mappa viene creata un'origine dati per archiviare le linee relative all'itinerario e ai punti di partenza e di arrivo. Viene creato un livello linea che viene allegato all'origine dati per definire il rendering della linea dell'itinerario. Le espressioni consentono di recuperare il colore e lo spessore della linea dalle proprietà della funzionalità delle linea dell'itinerario. Viene aggiunto un filtro per garantire che questo livello esegua il rendering dei soli dati LineString GeoJSON. Quando si aggiunge il livello alla mappa, viene passato un secondo parametro con valore `'labels'`, per indicare che il rendering di questo livello deve essere eseguito sotto le etichette della mappa. In questo modo la linea dell'itinerario non coprirà le etichette delle strade. Viene creato un livello simboli che viene allegato all'origine dati. Questo livello consente di specificare in che modo verrà eseguito il rendering dei punti di partenza e di arrivo. In questo caso sono state aggiunte espressioni per recuperare le informazioni sull'icona dell'immagine e il testo dell'etichetta dalle proprietà di ogni oggetto punto. 
    
2. Per questa esercitazione impostare come punto di partenza una società fittizia di Seattle denominata Fabrikam e come punto di arrivo un ufficio di Microsoft. Nel gestore dell'evento di caricamento della mappa aggiungere il codice seguente.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-blue'
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
    
    I punti di partenza e di arrivo vengono aggiunti all'origine dati. Per il calcolo del rettangolo di selezione viene usata la funzione `atlas.data.BoundingBox.fromData`. Questo rettangolo di selezione viene usato per impostare la visualizzazione delle videocamere della mappa sui punti di partenza e di arrivo con la funzione `map.setCamera`. Per compensare le dimensioni in pixel delle icone di simbolo, viene aggiunta una spaziatura interna.

4. Salvare il file e aggiornare il browser per visualizzare i punti sulla mappa. Ora la mappa è centrata su Seattle ed è possibile vedere il segnaposto blu rotondo che indica il punto di partenza e il segnaposto blu che indica il punto di arrivo.

   ![Visualizzare la mappa con i punti di partenza e di arrivo](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Classificare i percorsi in ordine di priorità in base alla modalità di trasporto

Questa sezione illustra come usare l'API del servizio di pianificazione itinerari per trovare più itinerari per raggiungere una destinazione da un determinato punto di partenza, in base alla modalità di trasporto. Il servizio di pianificazione itinerari fornisce le API per pianificare l'itinerario *più veloce*, *più breve*, *più ecologico* o *più entusiasmante* tra due punti, considerando le condizioni di traffico in tempo reale. Consente inoltre agli utenti di pianificare percorsi per il futuro usando il database dei dati storici sul traffico di Azure e fornendo stime della durata dei percorsi per qualsiasi giorno e ora. Per altre informazioni, vedere [Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Ottenere le indicazioni stradali). Tutti i blocchi di codice seguenti devono essere aggiunti **all'interno del listener di eventi del caricamento mappa** per garantire che vengano caricati dopo che la mappa è stata caricata completamente.

1. Per creare un'istanza del servizio client, aggiungere il codice Javascript seguente nel gestore dell'evento di caricamento della mappa.

    ```JavaScript
    //If the service client hasn't been created, create an instance of it.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```
    
2. Aggiungere il blocco di codice seguente per creare una stringa di query di route.

    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. Aggiungere il codice JavaScript seguente per richiedere l'itinerario per un autocarro che trasporta un carico classificato come USHazmatClass2 e visualizzare i risultati:

    ```JavaScript
    //Execute the truck route query then add the route to the map.
    client.route.getRouteDirections(routeQuery, {
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```
    Nel frammento di codice seguente viene eseguita una query per il servizio itinerari di Mappe di Azure tramite il metodo [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) e viene quindi analizzata la risposta in formato GeoJSON usando [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Viene quindi creata una matrice di coordinate per l'itinerario restituito e tale elemento viene aggiunto all'origine dati. Viene anche creato un indice 0 per garantire che venga eseguito il rendering prima di tutte le altre linee nell'origine dati. Il calcolo dell'itinerario per gli autocarri sarà infatti spesso più lento rispetto q quello dell'itinerario per le auto, di conseguenza se la linea dell'itinerario per gli autocarri viene aggiunto all'origine dati dopo quello per le auto, ne verrà eseguito il rendering su quest'ultimo. Alla linea dell'itinerario per gli autocarri vengono aggiunte due proprietà, un colore di tratto con sfumatura blu e uno spessore tratto di 9 pixel. 

4. Aggiungere il codice JavaScript seguente per richiedere l'itinerario per un'auto e visualizzare i risultati:

    ```JavaScript
    //Execute the car route query then add the route to the map.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source.
        datasource.add(routeLine);
    });
    ```
    Questo frammento di codice usa la stessa query di itinerario di un autocarro per un'auto. Viene eseguita una query per il servizio di routing di Mappe di Azure tramite il metodo [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) e viene quindi analizzata la risposta in formato GeoJSON usando [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Viene quindi creata una matrice di coordinate per l'itinerario restituito e tale elemento viene aggiunto all'origine dati. Alla linea dell'itinerario per le auto vengono aggiunte due proprietà, un colore di tratto con sfumatura viola e uno spessore tratto di 5 pixel. 

5. Salvare il file **MapTruckRoute.html** e aggiornare il browser per osservare il risultato. Se la connessione con le API di Mappe è stata stabilita correttamente, verrà visualizzata una mappa simile alla seguente.

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