---
title: Come usare il modulo di i/o spaziale di Maps di Azure | Mappe Microsoft Azure
description: Informazioni su come usare il modulo IO spaziale fornito da Azure Maps Web SDK. Questo modulo fornisce funzionalità affidabili per consentire agli sviluppatori di integrare con facilità i dati spaziali con Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 61ea102d0e020f7890da1ae86cdfbb5c3db8f51b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335297"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Come usare il modulo di i/o spaziale di Maps di Azure

Azure Maps Web SDK fornisce il modulo di i/o **spaziale**, che integra i dati spaziali con Azure Maps Web SDK usando JavaScript o typescript. Le funzionalità affidabili di questo modulo consentono agli sviluppatori di:

- [Leggere e scrivere file di dati spaziali comuni](spatial-io-read-write-spatial-data.md). I formati di file supportati sono i file KML, KMZ, GPX, GeoRSS, GML, GeoJSON e CSV contenenti colonne con informazioni spaziali. Supporta anche Well-Known testo (WKT).
- [Connettersi ai servizi di Open Geospatial Consortium (OGC) ed eseguire l'integrazione con Azure Maps Web SDK. Sovrapposizione dei servizi di Web Map Services (WMS) e delle tessere mappa Web (WMTS) come livelli sulla mappa](spatial-io-add-ogc-map-layer.md).
- [Eseguire query sui dati in un servizio funzionalità Web (WFS)](spatial-io-connect-wfs-service.md).
- [Sovrapporre set di dati complessi che contengono informazioni sullo stile e visualizzarli automaticamente usando il codice minimo](spatial-io-add-simple-data-layer.md).
- [Sfruttare le classi di lettura e scrittura di file XML e delimitate ad alta velocità](spatial-io-core-operations.md).

In questa guida verrà illustrato come integrare e utilizzare il modulo di i/o spaziale in un'applicazione Web.

Questo video offre una panoramica del modulo di i/o spaziale in Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0" title="Integra facilmente i dati spaziali in mappe di Azure-video Microsoft Channel 9"></iframe>


> [!WARNING]
> Usare solo i dati e i servizi provenienti da un'origine attendibile, soprattutto se vi si fa riferimento da un altro dominio. Il modulo di i/o spaziale esegue i passaggi per ridurre al minimo i rischi, ma l'approccio più sicuro è troppo non consentire l'inizio di tutti i dati danagerous nell'applicazione. 

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare il modulo di i/o spaziale, è necessario [creare un account Azure Maps](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-azure-maps-account) e [ottenere la chiave di sottoscrizione primaria per l'account](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Installazione del modulo IO spaziale

È possibile caricare il modulo di i/o spaziale Maps di Azure usando una delle due opzioni seguenti:

* Rete CDN di Azure ospitata a livello globale per il modulo di i/o spaziale di Maps di Azure. Per questa opzione, si aggiunge un riferimento a JavaScript nell' `<head>` elemento del file HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Il codice sorgente per [Azure-Maps-Spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) può essere caricato localmente e quindi ospitato con l'app. Questo pacchetto include anche le definizioni TypeScript. Per questa opzione, utilizzare il comando seguente per installare il pacchetto:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Aggiungere quindi un riferimento a JavaScript nell' `<head>` elemento del documento HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Uso del modulo IO spaziale

1. Creare un nuovo file HTML.

2. Caricare Azure Maps Web SDK e inizializzare il controllo mappa. Per informazioni dettagliate, vedere la Guida di [controllo mappa di Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) . Al termine di questo passaggio, il file HTML dovrebbe essere simile al seguente:

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

2. Caricare il modulo di i/o spaziale Maps di Azure. Per questo esercizio, usare la rete CDN per il modulo di i/o spaziale di Maps di Azure. Aggiungere il riferimento seguente all' `<head>` elemento del file HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inizializzare un oggetto `datasource` e aggiungere l'origine dati alla mappa. Inizializzare un oggetto `layer` e aggiungere l'origine dati al livello mappa. Eseguire quindi il rendering dell'origine dati e del livello. Prima di scorrere verso il basso per visualizzare il codice completo nel passaggio successivo, valutare i punti migliori in cui inserire i frammenti di codice dell'origine dati e del livello. Prima di modificare a livello di codice la mappa, è necessario attendere che la risorsa della mappa sia pronta.

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

4. Riunendola, il codice HTML dovrebbe essere simile al codice seguente. In questo esempio viene illustrato come leggere un file XML da un URL. Quindi, caricare e visualizzare i dati delle funzionalità del file sulla mappa. 

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

5. Ricordarsi di sostituire `<Your Azure Maps Key>` con la chiave primaria. Aprire il file HTML. verranno visualizzati risultati simili all'immagine seguente:

    <center>

    ![Esempio di dati spaziali](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Passaggi successivi

La funzionalità illustrata di seguito è solo una delle numerose funzionalità disponibili nel modulo IO spaziale. Leggere le guide seguenti per informazioni su come usare altre funzionalità nel modulo IO spaziale:

> [!div class="nextstepaction"]
> [Aggiungere un livello dati semplice](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Leggere e scrivere dati spaziali](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Connettersi a un servizio WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Sfruttare le operazioni principali](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli sul formato dati supportato](spatial-io-supported-data-format-details.md)

Vedere la documentazione relativa ai i/o spaziali di Azure Maps:

> [!div class="nextstepaction"]
> [Pacchetto di i/o spaziale di mappe di Azure](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
