---
title: Come usare il modulo i/O spaziale di Azure Maps Mappe di Microsoft Azure
description: Informazioni su come usare il modulo i/O spaziale fornito da Azure Maps Web SDK. Questo modulo offre funzionalità affidabili che semplificano agli sviluppatori l'integrazione dei dati spaziali con il Web sdk di Azure Maps.This module provides robust features to make it easy for developers to integrate spatial data with the Azure Maps web sdk.
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcfeebc1fcb96cfdf6ea802293eb4027f339815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335201"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Come usare il modulo i/O spaziale di Azure MapsHow to use the Azure Maps Spatial IO module

Il Web SDK di Azure Maps fornisce il **modulo I/O spaziale**, che integra i dati spaziali con Azure Maps Web SDK tramite JavaScript o TypeScript. Le caratteristiche affidabili di questo modulo consentono agli sviluppatori di:

- Leggere e scrivere file di [dati spaziali comuni.](spatial-io-read-write-spatial-data.md) I formati di file supportati includono: KML, KM, GPX, GeoRSS, GML, GeoJSON e file CSV contenenti colonne con informazioni spaziali. Supporta anche il testo noto (WKT).
- [Connettersi ai servizi Open Geospatial Consortium (OGC) e integrarsi con Azure Maps Web SDK. Overlay Web Map Services (WMS) e Web Map Tile Services (WMTS) come layer sulla mappa](spatial-io-add-ogc-map-layer.md).
- [Eseguire query sui dati in un Web Feature Service (WFS)](spatial-io-connect-wfs-service.md).
- [Sovrapporre set di dati complessi che contengono informazioni sullo stile e fare in modo che il rendering venga eseguito automaticamente utilizzando codice minimo.](spatial-io-add-simple-data-layer.md)
- Sfruttare le classi di lettura e scrittura di [file XML e delimitate ad alta velocità.](spatial-io-core-operations.md)

In questa guida verrà illustrato come integrare e utilizzare il modulo I/O spaziale in un'applicazione Web.In this guide, we'll learn how to integrate and use the Spatial IO module in a web application.

> [!WARNING]
> Utilizzare solo dati e servizi provenienti da un'origine attendibile, soprattutto se si fa riferimento ad esso da un altro dominio. Il modulo I/O spaziale adotta misure per ridurre al minimo i rischi, tuttavia l'approccio più sicuro è troppo non consentire l'inizio di dati in danagerous nell'applicazione. 

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare il modulo I/O spaziale, è necessario [creare un account Azure Maps](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) e ottenere la chiave di sottoscrizione principale per [l'account.](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)

## <a name="installing-the-spatial-io-module"></a>Installazione del modulo I/O spaziale

È possibile caricare il modulo I/O spaziale di Azure Maps usando una delle due opzioni:You can load the Azure Maps spatial IO module using one of the two options:

* Rete CDN di Azure ospitata a livello globale per il modulo I/O spaziale di Azure Maps.The globally hosted Azure CDN for the Azure Maps spatial IO module. Per questa opzione, aggiungete un riferimento `<head>` a JavaScript nell'elemento del file HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Il codice sorgente per [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) può essere caricato in locale e quindi ospitato con l'app. Questo pacchetto include anche le definizioni TypeScript. Per questa opzione, utilizzare il comando seguente per installare il pacchetto:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Quindi, aggiungete un riferimento a `<head>` JavaScript nell'elemento del documento HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Utilizzo del modulo IO spaziale

1. Creare un nuovo file HTML.

2. Caricare Azure Maps Web SDK e inizializzare il controllo mappa. Per i dettagli, vedere la guida al controllo delle mappe di Mappe di Azure.See the [Azure Maps map control](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) guide for the details. Una volta che hai finito con questo passaggio, il file HTML dovrebbe essere simile al seguente:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Caricare il modulo di I/O spaziale di Azure Maps.Load the Azure Maps spatial IO module. Per questo esercizio, usare la rete CDN per il modulo i/O spaziale di Azure Maps.For this exercise, use the CDN for the Azure Maps spatial IO module. Aggiungi il riferimento `<head>` seguente all'elemento del file HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inizializzare `datasource`un oggetto e aggiungere l'origine dati alla mappa. Inizializzare `layer`un oggetto e aggiungere l'origine dati al livello mappa. Quindi, eseguire il rendering sia dell'origine dati che del livello. Prima di scorrere verso il basso per visualizzare il codice completo nel passaggio successivo, considerare le posizioni migliori in cui inserire l'origine dati e gli snippet di codice del livello. Tenere presente che, prima di modificare la mappa a livello di codice, è necessario attendere che la risorsa mappa sia pronta.

    ```javascript
    var datasource, layer;
    ```

    e

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Mettendo tutto insieme, il codice HTML dovrebbe essere simile al codice seguente. In questo esempio viene illustrato come leggere un file XML da un URL. Quindi, caricare e visualizzare i dati di feature del file sulla mappa. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Ricordarsi `<Your Azure Maps Key>` di sostituirlo con la chiave primaria. Apri il file HTML e vedrai risultati simili all'immagine seguente:

    <center>

    ![Esempio di dati spaziali](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Passaggi successivi

La funzionalità che abbiamo dimostrato qui è solo una delle molte funzionalità disponibili nel modulo IO spaziale. Leggi le guide seguenti per scoprire come usare altre funzionalità nel modulo I/O spaziale:

> [!div class="nextstepaction"]
> [Aggiungere un livello dati semplice](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Lettura e scrittura di dati spaziali](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Connettersi a un servizio WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Sfruttare le operazioni di base](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli sul formato dati supportati](spatial-io-supported-data-format-details.md)

Fare riferimento alla documentazione di I/O spaziale di Azure Maps:Refer to the Azure Maps Spatial IO documentation:

> [!div class="nextstepaction"]
> [Pacchetto i/O spaziale di Azure MapsAzure Maps Spatial IO package](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
