---
title: Trovare un percorso con Mappe di Azure | Microsoft Docs
description: Trovare il percorso per raggiungere un punto di interesse usando Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 7b5b82e80ab4998f7cd106f469bf7ac8e271285d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588365"
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
    <html>
    <head>
        <title>Map Route</title>
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

            #map {
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

    `atlas.Map` fornisce il controllo per una mappa Web visiva e interattiva ed è un componente dell'API del controllo mappa di Azure.

4. Salvare il file e aprirlo nel browser. A questo punto si ha una mappa di base che è possibile sviluppare ulteriormente.

   ![Visualizzare la mappa di base](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definire il rendering dell'itinerario

In questa esercitazione verrà eseguito il rendering di un itinerario semplice usando un'icona di simbolo per l'inizio e la fine dell'itinerario e una linea per il percorso dell'itinerario.

1. Dopo aver inizializzato la mappa, aggiungere il codice JavaScript seguente nella funzione GetMap.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    Alla mappa viene aggiunto un evento di caricamento che verrà attivato dopo che le risorse della mappa sono state caricate completamente. Nel gestore dell'evento di caricamento della mappa viene creata un'origine dati per archiviare la riga relativa all'itinerario e i punti di partenza e di arrivo. Viene creato un livello linea che viene allegato all'origine dati per definire il rendering della linea dell'itinerario. Il rendering del livello linea sarà caratterizzato da una sfumatura blu di larghezza pari a 5 pixel e giunzioni di linee ed estremità arrotondate. Viene aggiunto un filtro per garantire che questo livello esegua il rendering dei soli dati LineString GeoJSON. Quando si aggiunge il livello alla mappa, viene passato un secondo parametro con valore `'labels'`, per indicare che il rendering di questo livello deve essere eseguito sotto le etichette della mappa. In questo modo la linea dell'itinerario non coprirà le etichette delle strade. Viene creato un livello simboli che viene allegato all'origine dati. Questo livello consente di specificare in che modo verrà eseguito il rendering dei punti di partenza e di arrivo. In questo caso sono state aggiunte espressioni per recuperare le informazioni sull'icona dell'immagine e il testo dell'etichetta dalle proprietà di ogni oggetto punto. 
    
2. Per questa esercitazione impostare Microsoft come punto di partenza e una stazione di rifornimento a Seattle come punto di destinazione. Nel gestore dell'evento di caricamento della mappa aggiungere il codice seguente.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: 'Microsoft',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: 'Contoso Oil & Gas',
        icon: 'pin-blue'
    });    
    ```

    Questo codice crea due [oggetti punto GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) per rappresentare i punti di partenza e di arrivo dell'itinerario. A ogni punto vengono aggiunge le proprietà `title` e `icon`.
    
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
    
    I punti di partenza e di arrivo vengono aggiunti all'origine dati. Per il calcolo del rettangolo di selezione viene usata la funzione `atlas.data.BoundingBox.fromData`. Questo rettangolo di selezione viene usato per impostare la visualizzazione delle videocamere della mappa sui punti di partenza e di arrivo con la funzione **map.setCamera**. Per compensare le dimensioni in pixel delle icone di simbolo, viene aggiunta una spaziatura interna.

3. Salvare il file **MapRoute.html** e aggiornare il browser. Ora la mappa è centrata su Seattle ed è possibile vedere il segnaposto blu rotondo che indica il punto di partenza e il segnaposto blu che indica il punto di arrivo.

   ![Visualizzare la mappa con i punti di partenza e di arrivo contrassegnati](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Ottenere le indicazioni stradali

Questa sezione illustra come usare l'API del servizio di pianificazione percorso di Mappe per trovare il percorso per raggiungere una destinazione da un determinato punto di partenza. Il servizio di pianificazione percorso fornisce le API per pianificare il percorso *più veloce*, *più breve*, *più ecologico* o *più interessante* tra due posizioni. Consente inoltre agli utenti di pianificare percorsi per il futuro usando il database dei dati storici sul traffico di Azure e fornendo stime della durata dei percorsi per qualsiasi giorno e ora. Per altre informazioni, vedere [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Ottenere le indicazioni stradali). Tutte le funzionalità seguenti devono essere aggiunte **all'interno del listener di eventi del caricamento mappa** per garantire che vengano caricate dopo che la mappa è stata caricata completamente.

1. Per creare un'istanza del servizio client, aggiungere il codice Javascript seguente nel gestore dell'evento di caricamento della mappa.

    ```JavaScript
    //If the service client hasn't already been created, create an instance.
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

3. Per ottenere la route, aggiungere allo script il blocco di codice seguente. Viene eseguita una query al servizio di routing di Mappe di Azure tramite il metodo [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) e viene quindi analizzata la risposta in formato GeoJSON usando [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routegeojson?view=azure-iot-typescript-latest). Nella risposta all'origine dati viene quindi aggiunta la riga dell'itinerario, per eseguirne automaticamente il rendering nella mappa.

    ```JavaScript
    //Execute the car route query then add the route to the map once a response is received.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Add the route line to the data source.
        datasource.add(geoJsonResponse.getGeoJsonRoutes().features[0]);
    });
    ```

5. Salvare il file **MapRoute.html** e aggiornare il Web browser. Se la connessione con le API di Mappe è stata stabilita correttamente, verrà visualizzata una mappa simile alla seguente.

    ![Controllo mappa e servizio di pianificazione percorso di Azure](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Impostare le coordinate di un indirizzo
> * Inviare una query al servizio di pianificazione percorso per ottenere le indicazioni stradali per un punto di interesse

È possibile accedere all'esempio di codice per questa esercitazione qui:

> [Trovare un itinerario con Mappe di Azure](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[Vedere qui l'esempio in tempo reale](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

L'esercitazione successiva illustra come creare una query del percorso con restrizioni, ad esempio la modalità di trasporto o il tipo di carico, e come visualizzare più percorsi sulla mappa stessa.

> [!div class="nextstepaction"]
> [Find routes for different modes of travel (Trovare i percorsi per diverse modalità di trasporto)](./tutorial-prioritized-routes.md)
