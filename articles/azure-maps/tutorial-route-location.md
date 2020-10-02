---
title: 'Esercitazione: Come visualizzare le indicazioni stradali usando il servizio di pianificazione percorso e il controllo mappa di Mappe di Microsoft Azure'
description: Informazioni su come visualizzare le indicazioni stradali usando il servizio di pianificazione percorso e il controllo mappa di Mappe di Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 35a3f6d1e7894eec9baa4ea5432a8e3fec138a21
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085043"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>Esercitazione: Come visualizzare le indicazioni stradali usando il servizio di pianificazione percorso e il controllo mappa di Mappe di Azure

Questa esercitazione illustra come usare l'[API del servizio di pianificazione percorso](https://docs.microsoft.com/rest/api/maps/route) e il [controllo mappa](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) di Mappe di Azure per visualizzare le indicazioni stradali da un punto di partenza a un punto di arrivo. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare e visualizzare il controllo mappa in una pagina Web. 
> * Definire il rendering della visualizzazione del percorso definendo [livelli simbolo](map-add-pin.md) e [livelli linea](map-add-line-layer.md).
> * Creare e aggiungere oggetti GeoJSON alla mappa per rappresentare i punti di partenza e di arrivo.
> * Ottenere le indicazioni stradali dal punto di partenza a quello di arrivo usando l'[API Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

È possibile ottenere il codice sorgente completo per l'esempio [qui](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html). Un esempio eseguibile è disponibile [qui](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un account Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>Creare e visualizzare il controllo mappa

La procedura seguente illustra come creare e visualizzare il controllo mappa in una pagina Web.

1. Nel computer locale creare un nuovo file con il nome **MapRoute.html**.
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

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="Rendering di una mappa di base del controllo mappa":::

## <a name="define-route-display-rendering"></a>Definire il rendering della visualizzazione del percorso

In questa esercitazione verrà eseguito il rendering del percorso usando un livello linea. Il rendering dei punti iniziale e finale verrà eseguito con un livello di simboli. Per altre informazioni sull'aggiunta di livelli linea, vedere [Aggiungere un livello linea a una mappa](map-add-line-layer.md). Per altre informazioni sui livelli simbolo, vedere [Aggiungere un livello simbolo a una mappa](map-add-pin.md).

1. Aggiungere il codice JavaScript seguente alla funzione `GetMap`. Questo codice implementa il gestore dell'evento `ready` del controllo mappa. Il resto del codice in questa esercitazione verrà inserito all'interno del gestore dell'evento `ready`.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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

    Nel gestore dell'evento `ready` del controllo mappa viene creata un'origine dati per archiviare il percorso dal punto di partenza a quello di arrivo. Per definire il rendering della linea del percorso viene creato un livello linea che viene collegato all'origine dati.  Per assicurarsi che la linea del percorso non copra le etichette stradali, è stato passato un secondo parametro con il valore `'labels'`.

    Viene poi creato un livello simbolo collegato all'origine dati. Questo livello specifica come viene eseguito il rendering dei punti iniziale e finale. Sono state aggiunte espressioni per recuperare l'immagine dell'icona e le informazioni sulle etichette di testo dalle proprietà in ogni oggetto punto. Per altre informazioni sulle espressioni, vedere [Espressioni di stile basate su dai](data-driven-style-expressions-web-sdk.md).

2. Impostare Microsoft come punto di partenza e una stazione di rifornimento a Seattle come punto di arrivo.  Nel gestore dell'evento `ready` del controllo mappa aggiungere il codice seguente.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Questo codice crea due [oggetti punto GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) per rappresentare i punti di partenza e di arrivo, che vengono quindi aggiunti all'origine dati. 

    L'ultimo blocco di codice imposta la visualizzazione della fotocamera tramite le informazioni di latitudine e longitudine del punto di partenza e di arrivo. I punti di partenza e di arrivo vengono aggiunti all'origine dati. Per il calcolo del rettangolo di selezione viene usata la funzione `atlas.data.BoundingBox.fromData`. Questo rettangolo di selezione viene usato per impostare la visualizzazione delle videocamere della mappa sull'intero percorso con la funzione `map.setCamera`. Per compensare le dimensioni in pixel delle icone di simbolo, viene aggiunta una spaziatura interna. Per altre informazioni sulla proprietà setCamera del controllo mappa, vedere [setCamera(CameraOptions | CameraBoundsOptions e AnimationOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false).

3. Salvare **MapRoute.html** e aggiornare il browser. La mappa è ora centrata sulla città di Seattle. L'indicatore blu a goccia contrassegna il punto di partenza. L'indicatore rotondo a goccia contrassegna il punto di arrivo.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="Rendering di una mappa di base del controllo mappa":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>Ottenere le indicazioni stradali

Questa sezione illustra come usare l'API di indicazioni stradali di Mappe di Azure per ottenere indicazioni stradali e il tempo previsto di arrivo da un punto a un altro.

>[!TIP]
>I servizi di pianificazione percorso offrono API per pianificare più tipi di percorso, ad esempio *più veloce*, *più breve*, *più ecologico* o *più entusiasmante*, in base alla distanza, alle condizioni del traffico e alla modalità di trasporto usata. Il servizio consente anche di pianificare percorsi futuri in base alle condizioni storiche del traffico. Gli utenti possono visualizzare la durata stimata del percorso per qualsiasi ora specificata. Per altre informazioni, vedere [API Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

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

2. Dopo aver impostato le credenziali e l'URL, aggiungere il codice seguente nel gestore dell'evento `ready` del controllo. Questo codice costruisce il percorso dal punto di partenza a quello di arrivo. `routeURL` richiede all'API del servizio di pianificazione percorso di Mappe di Azure di calcolare le indicazioni stradali. Dalla risposta viene estratta una raccolta di funzionalità GeoJSON con il metodo `geojson.getFeatures()` e viene aggiunta all'origine dati.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Salvare il file **MapRoute.html** e aggiornare il Web browser. La mappa dovrebbe ora visualizzare il percorso dal punto di partenza al punto di arrivo.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Rendering di una mappa di base del controllo mappa":::

    È possibile ottenere il codice sorgente completo per l'esempio [qui](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html). Un esempio eseguibile è disponibile [qui](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="next-steps"></a>Passaggi successivi

L'esercitazione successiva mostra come creare una query del percorso con restrizioni, ad esempio la modalità di trasporto o il tipo di carico. È quindi possibile visualizzare più percorsi sulla stessa mappa.

> [!div class="nextstepaction"]
> [Find routes for different modes of travel (Trovare i percorsi per diverse modalità di trasporto)](./tutorial-prioritized-routes.md)
