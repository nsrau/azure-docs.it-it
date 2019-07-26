---
title: Creare un localizzatore di punti vendita con Mappe di Azure | Microsoft Docs
description: Creare un localizzatore di punti vendita con Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 4cc21a4dbab7d5114eed8414c6530eab5f42bb00
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478867"
---
# <a name="create-a-store-locator-by-using-azure-maps"></a>Creare un localizzatore di punti vendita con Mappe di Azure

Questa esercitazione illustra il processo di creazione di un semplice localizzatore di punti vendita con Mappe di Azure. I localizzatori di punti vendita sono molto diffusi. Molti dei concetti usati in questo tipo di applicazione sono applicabili a molti altri tipi di applicazioni. L'offerta di un localizzatore di punti vendita ai clienti è essenziale per la maggior parte delle aziende che vendono direttamente ai consumatori. In questa esercitazione si apprenderà come:
    
> [!div class="checklist"]
> * Creare una nuova pagina Web con l'API Controllo mappa di Azure.
> * Caricare dati personalizzati da un file e visualizzarli in una mappa.
> * Usare il servizio Ricerca di Mappe di Azure per trovare un indirizzo o immettere una query.
> * Ottenere la posizione dell'utente dal browser e visualizzarla sulla mappa.
> * Combinare più livelli per creare simboli personalizzati sulla mappa.  
> * Creare cluster di punti dati.  
> * Aggiungere controlli zoom alla mappa.

<a id="Intro"></a>

Passare all'[esempio attivo di localizzatore di punti vendita](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) o al [codice sorgente](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). 

## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure disponibili in questa esercitazione, è prima di tutto necessario [creare un account Mappe di Azure](./tutorial-search-location.md#createaccount) e [ottenere la chiave di sottoscrizione per l'account](./tutorial-search-location.md#getkey).

## <a name="design"></a>Progettazione

Prima di passare al codice, è consigliabile definire la struttura. Il localizzatore di punti vendita può essere semplice o complesso, in base alle necessità specifiche. In questa esercitazione viene creato un localizzatore di punti vendita semplice. L'esercitazione include alcuni suggerimenti utili per estendere alcune funzionalità, se necessario. Verrà creato un localizzatore di punti vendita per una società fittizia denominata Contoso Coffee. La figura seguente mostra un wireframe del layout generale del localizzatore di punti vendita creato in questa esercitazione:

<br/>
<center>

![Wireframe di un localizzatore di punti vendita per le posizioni dei bar Contoso Coffee](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Per massimizzare l'utilità di questo localizzatore di punti vendita, includeremo un layout reattivo che si adatta quando la larghezza dello schermo di un utente è inferiore a 700 pixel. Un layout reattivo semplifica l'uso del localizzatore di punti vendita su un piccolo schermo, ad esempio in un dispositivo mobile. Ecco un wireframe del layout per uno schermo di piccole dimensioni:  

<br/>
<center>

![Wireframe del localizzatore di punti vendita di Contoso Coffee su un dispositivo mobile](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

I wireframe mostrano un'applicazione abbastanza semplice. L'applicazione include una casella di ricerca, un elenco di punti vendita nelle vicinanze, una mappa con alcuni marcatori (simboli) e una finestra popup che mostra informazioni aggiuntive quando l'utente seleziona un marcatore. Ecco una descrizione più dettagliata delle funzionalità create nel localizzatore di punti vendita in questa esercitazione:

* Tutte le posizioni dal file di dati importato con valori delimitati da tabulazioni vengono caricate nella mappa.
* L'utente può visualizzare una panoramica o usare lo zoom sulla mappa, eseguire una ricerca e selezionare il pulsante GPS "My Location".
* Il layout di pagina si adatta in base alla larghezza dello schermo del dispositivo.  
* Un'intestazione mostra il logo del punto vendita.  
* L'utente può usare una casella di ricerca e un pulsante di ricerca per individuare una posizione, ad esempio un indirizzo, un codice postale o una città. 
* Un evento `keypress` aggiunto alla casella di ricerca attiva una ricerca se l'utente preme INVIO. Questa funzionalità è spesso trascurata, ma consente di creare un'esperienza utente migliore.
* Quando la mappa si sposta, viene calcolata la distanza di ogni posizione rispetto al centro della mappa. L'elenco risultati viene aggiornato per visualizzare le posizioni più vicine nella parte superiore della mappa.  
* Quando si seleziona un risultato nell'elenco risultati, la mappa viene centrata rispetto alla posizione selezionata e vengono visualizzate in una finestra popup le informazioni sulla posizione.  
* Anche la selezione di una posizione specifica sulla mappa attiva una finestra popup.
* Quando l'utente fa zoom indietro, le posizioni vengono raggruppate in cluster. I cluster sono rappresentati da un cerchio con un numero all'interno del cerchio. I cluster si formano e si separano quando l'utente modifica il livello di zoom.
* Se si seleziona un cluster, i due livelli della mappa vengono ingranditi e la visualizzazione viene centrata rispetto alla posizione del cluster.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Creare il set di dati relativo alla posizione dei punti vendita

Prima di sviluppare un'applicazione di tipo localizzatore di punti vendita, è necessario creare un set di dati dei punti vendita da visualizzare sulla mappa. In questa esercitazione viene usato un set di dati per un bar fittizio denominato Contoso Coffee. Il set di dati per questo semplice localizzatore di punti vendita viene gestito in una cartella di lavoro di Excel. Il set di dati contiene 10.213 posizioni di bar Contoso Coffee in nove paesi/aree geografiche, ovvero Stati Uniti, Canada, Regno Unito, Francia, Germania, Italia, Paesi Bassi, Danimarca e Spagna. Ecco uno screenshot dell'aspetto dei dati:

<br/>
<center>

![Screenshot dei dati del localizzatore di punti vendita in una cartella di lavoro di Excel](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

È possibile [scaricare la cartella di lavoro di Excel](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Esaminando lo screenshot dei dati è possibile fare le osservazioni seguenti:
    
* Le informazioni sulla posizione sono archiviate mediante le colonne **AddressLine**, **City**, **Municipality** (comune), **AdminDivision** (stato/provincia), **PostCode** (codice postale) e **Country**.  
* Le colonne **Latitude** e **Longitude** contengono le coordinate per ogni posizione dei bar Contoso Coffee. Se non sono disponibili informazioni sulle coordinate, è possibile usare i servizi di ricerca in Mappe di Azure per determinare le coordinate relative alle posizioni.
* Alcune colonne aggiuntive contengono metadati correlati ai bar, ovvero numero di telefono, colonne booleane per hotspot Wi-Fi, accessibilità per disabili e orari di apertura e chiusura del bar in formato di 24 ore. È possibile creare colonne personalizzate contenenti i metadati più rilevanti per i dati di posizione specifici.

> [!Note]
> Mappe di Azure esegue il rendering dei dati nella proiezione sferica di Mercatore "EPSG:3857", ma legge i dati in "EPSG:4325" che usano il dato WGS84. 

È possibile esporre in molti modi il set di dati all'applicazione. Un approccio consiste nel caricare i dati in un database ed esporre un servizio Web che esegue query sui dati e invia i risultati al browser dell'utente. Questa opzione è ideale per set di dati di grandi dimensioni o per set di dati che vengono aggiornati spesso. Questa opzione richiede tuttavia un numero significativamente superiore di attività di sviluppo e ha un costo maggiore. 

Un altro approccio consiste nel convertire questo set di dati in un file flat di testo che può essere analizzato con facilità dal browser. Il file stesso può essere ospitato insieme al resto dell'applicazione. Questa opzione consente di semplificare le procedure, ma è ideale solo per set di dati più piccoli perché l'utente scarica tutti i dati. Per questo set di dati viene usato il file flat di testo perché le dimensioni del file di dati sono inferiori a 1 MB.  

Per convertire la cartella di lavoro in un file flat di testo, salvare la cartella di lavoro come file con valori delimitati da tabulazioni. Ogni colonna è delimitata da un carattere di tabulazione e le colonne risultano quindi facili da analizzare nel codice. È possibile usare il formato con valori delimitati da virgole (CSV), ma questa opzione richiede una quantità maggiore di logica di analisi. Qualsiasi campo delimitato da una virgola verrebbe racchiuso tra virgolette. Per esportare questi dati come file con valori delimitati da tabulazioni in Excel, selezionare **Salva con nome**. Nell'elenco a discesa **Salva come** selezionare **Testo (delimitato da tabulazione)(*.txt)** . Specificare il nome *ContosoCoffee.txt* per il file. 

<br/>
<center>

![Screenshot della finestra di dialogo con Tipo file](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Se si apre il file di testo in Blocco note, avrà un aspetto simile alla figura seguente:

<br/>
<center>

![Screenshot di un file di Blocco note che mostra un set di dati con valori delimitati da tabulazioni](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Configurare il progetto

Per creare un progetto, è possibile usare [Visual Studio](https://visualstudio.microsoft.com) o l'editor di codice che si preferisce. Nella cartella del progetto creare tre file: *index.html*, *index.css* e *index.js*. Questi file definiscono il layout, lo stile e la logica per l'applicazione. Creare una cartella denominata *data* e aggiungere il file *ContosoCoffee.txt* alla cartella. Creare un'altra cartella denominata *images*. In questa applicazione vengono usate dieci immagini per icone, pulsanti e marcatori sulla mappa. È possibile [scaricare queste immagini](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). La cartella del progetto dovrebbe avere ora un aspetto simile alla figura seguente:

<br/>
<center>

![Screenshot della cartella del progetto Simple Store Locator](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Creare l'interfaccia utente

Per creare l'interfaccia utente, aggiungere codice a *index.html*:

1. Aggiungere i tag `meta` seguenti a `head` di *index.html*. I tag definiscono il set di caratteri (UTF-8), richiedono a Internet Explorer e Microsoft Edge di usare la versione più recente del browser e specificano un viewport ideale per i layout reattivi.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Aggiungere riferimenti al controllo Web dei file JavaScript e CSS di Mappe di Azure:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Aggiungere un riferimento al modulo dei servizi di Mappe di Azure. Il modulo è una libreria di JavaScript che esegue il wrapping dei servizi REST di Mappe di Azure e li rende facili da usare in JavaScript. Questo modulo è utile per potenziare la funzionalità di ricerca.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Aggiungere riferimenti ai file *index.js* e *index.css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. Nel corpo del documento aggiungere un tag `header`. All'interno del tag `header` aggiungere il logo e il nome della società.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Aggiungere il tag `main` e creare un pannello di ricerca che include una casella di testo e un pulsante di ricerca. Aggiungere anche riferimenti `div` per la mappa, il pannello elenchi e il pulsante GPS My Location.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Al termine, il file *index.html* dovrebbe avere un aspetto analogo a [questo file index.html di esempio](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

Il passaggio successivo consiste nel definire gli stili CSS. Gli stili CSS consentono di definire il modo in cui i componenti dell'applicazione vengono disposti e l'aspetto dell'applicazione. Aprire il file *index.css* e aggiungere al file il codice seguente. Lo stile `@media` definisce le opzioni di stile alternative da usare quando la larghezza dello schermo è inferiore a 700 pixel.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Se si esegue adesso l'applicazione, vengono visualizzati l'intestazione, la casella di ricerca e il pulsante di ricerca, ma la mappa non è visibile perché non è stata ancora caricata. Se si prova a eseguire una ricerca, non si ottiene alcun risultato. È necessario configurare la logica di JavaScript descritta nella sezione successiva per accedere a tutte le funzionalità del localizzatore di punti vendita.

## <a name="wire-the-application-with-javascript"></a>Aggiungere JavaScript all'applicazione

A questo punto l'interfaccia utente è stata configurata. Ora è necessario aggiungere JavaScript per caricare e analizzare i dati e quindi eseguire il rendering dei dati sulla mappa. Per iniziare, aprire il file *index.js* e aggiungere codice al file, come illustrato nella procedura seguente.

1. Aggiungere opzioni globali per semplificare l'aggiornamento delle impostazioni. Definire inoltre le variabili per la mappa, una finestra popup, un'origine dati, un livello di icone, un marcatore HTML che mostra il centro di un'area di ricerca e un'istanza del client del servizio di ricerca di Mappe di Azure.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Aggiungere codice al file *index.js*. Il codice seguente consente di inizializzare la mappa, aggiungere un [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) che rimane in attesa fino al completamento del caricamento della pagina, aggiungere eventi per monitorare il caricamento della mappa e attivare il pulsante di ricerca e il pulsante My Location.

   Quando l'utente seleziona il pulsante di ricerca o quando l'utente preme INVIO dopo avere immesso una posizione nella casella di ricerca, viene avviata una ricerca fuzzy rispetto alla query dell'utente. Passare una matrice di valori ISO 2 relativi ai paesi all'opzione `countrySet` per limitare i risultati della ricerca a tali paesi/aree geografiche. La limitazione dei paesi/aree geografiche in cui eseguire la ricerca consente di migliorare la precisione dei risultati restituiti. 
  
   Al termine della ricerca, accettare il primo risultato e impostare la fotocamera della mappa su tale area. Quando l'utente seleziona il pulsante My Location, usare l'API per la georilevazione HTML5 incorporata nel browser per recuperare la posizione dell'utente e centrare la mappa rispetto a tale posizione.  

   > [!Tip]
   > Quando si usano finestre popup, è consigliabile creare una singola istanza di `Popup` e riutilizzare l'istanza caricandone il contenuto e la posizione. Per ogni istanza di `Popup` aggiunta al codice, più elementi DOM vengono aggiunti alla pagina. Maggiore il numero di elementi DOM presenti nella pagina, maggiore sarà il numero di elementi di cui il browser deve tenere traccia. Se il numero di elementi è troppo elevato, è possibile che il browser risulti lento.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. Nel listener di eventi `ready` della mappa aggiungere un controllo di zoom e un marcatore HTML per visualizzare il centro di un'area di ricerca.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. Nel listener di eventi `ready` della mappa aggiungere un'origine dati. Effettuare quindi una chiamata per il caricamento e l'analisi del set di dati. Abilitare il clustering sull'origine dati. Il clustering sull'origine dati raggruppa i punti sovrapposti in un cluster. I cluster si separano in singoli punti quando l'utente applica lo zoom avanti. È quindi possibile ottenere un'esperienza utente più fluida e prestazioni migliori.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Dopo il caricamento del set di dati nel listener di eventi `ready` della mappa, definire un set di livelli per il rendering dei dati. Un livello a bolle viene usato per il rendering dei punti dati in cluster. Un livello di simboli viene usato per il rendering del numero di punti in ogni cluster sopra il livello a bolle. Un secondo livello di simboli esegue il rendering di un'icona personalizzata per singole posizioni sulla mappa.

   Aggiungere gli eventi `mouseover` e `mouseout` ai livelli a bolle e di icone per modificare il cursore del mouse quando l'utente lo posiziona sopra un cluster o un'icona sulla mappa. Aggiungere un evento `click` al livello a bolle del cluster. Questo evento `click` ingrandisce la mappa in due livelli e la centra rispetto a un cluster quando l'utente seleziona un cluster. Aggiungere un evento `click` al livello di icone. Questo evento `click` mostra una finestra popup che visualizza i dettagli di un bar quando un utente seleziona un'icona relativa a una singola posizione. Aggiungere un evento alla mappa per monitorare il momento in cui viene completato lo spostamento della mappa. Quando questo evento viene generato, aggiornare gli elementi nel pannello elenchi.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. Quando viene caricato il set di dati relativo ai bar, è prima di tutto necessario scaricarlo. È quindi necessario suddividere in righe il file di testo. La prima riga contiene le informazioni dell'intestazione. Per semplificare la lettura del codice, l'intestazione viene analizzata in un oggetto, che può essere quindi usato per cercare l'indice delle celle di ogni proprietà. Dopo la prima riga, eseguire il ciclo delle righe rimanenti e creare una funzionalità punto. Aggiungere la funzionalità punto all'origine dati. Aggiornare infine il pannello elenchi.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. Quando il pannello elenchi viene aggiornato, viene calcolata la distanza dal centro della mappa a tutte le funzionalità punto nella visualizzazione corrente della mappa. Le funzionalità vengono quindi ordinate in base alla distanza. Viene generato codice HTML per visualizzare ogni posizione nel pannello elenchi.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. Quando l'utente seleziona un elemento dal pannello elenchi, la forma correlata all'elemento viene recuperata dall'origine dati. Viene generata una finestra popup basata sulle informazioni relative alle proprietà archiviate nella forma. La mappa viene centrata rispetto alla forma. Se la larghezza della mappa è inferiore a 700 pixel, viene eseguito lo scostamento della visualizzazione della mappa in modo che la finestra popup risulti visibile.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

È ora disponibile un localizzatore di punti vendita completamente funzionante. Nel Web browser aprire i file *index.html* per il localizzatore di punti vendita. Quando i cluster vengono visualizzati sulla mappa, è possibile cercare una posizione usando la casella di ricerca, selezionando il pulsante My Location, selezionando un cluster o ingrandendo la mappa per visualizzare le singole posizioni.

Quando un utente seleziona il pulsante My Location per la prima volta, il browser mostra un avviso di sicurezza che richiede l'autorizzazione per accedere alla posizione dell'utente. Se l'utente accetta di condividere la propria posizione, la mappa viene ingrandita per visualizzarla e vengono mostrati i bar presenti nelle vicinanze. 

<br/>
<center>

![Screenshot della richiesta del browser di accedere alla posizione dell'utente](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Quando si ingrandisce a sufficienza un'area che include posizioni dei bar, i cluster si separano in singole posizioni. Selezionare una delle icone sulla mappa oppure selezionare un elemento nel pannello laterale per visualizzare una finestra popup che mostra le informazioni per tale posizione.

<br/>
<center>

![Screenshot del localizzatore di punti vendita completato](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Se si ridimensiona la finestra del browser fino a una larghezza inferiore a 700 pixel o se si apre l'applicazione in un dispositivo mobile, il layout viene modificato per adattarsi meglio a schermi più piccoli. 

<br/>
<center>

![Screenshot della versione del localizzatore di punti vendita per schermi di piccole dimensioni](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione viene illustrato come creare un localizzatore di punti vendita di base con Mappe di Azure. Il localizzatore di punti vendita creato in questa esercitazione può includere tutte le funzionalità necessarie. È possibile aggiungere funzionalità al localizzatore di punti vendita o usare funzionalità più avanzate per ottenere un'esperienza utente più personalizzata: 

> [!div class="checklist"]
> * Abilitare i [suggerimenti durante la digitazione](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) nella casella di ricerca.  
> * Aggiungere il [supporto per più lingue](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
> * Consentire all'utente di [filtrare le posizioni lungo un tragitto](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
> * Aggiungere la possibilità di [impostare filtri](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
> * Aggiungere il supporto per specificare un valore di ricerca iniziale mediante una stringa di query. Quando si include questa opzione nel localizzatore di punti vendita, gli utenti possono aggiungere ai segnalibri e condividere le ricerche. Questa opzione consente anche di passare con facilità le ricerche a questa pagina da un'altra pagina.  
> * Distribuire il localizzatore di punti vendita come [app Web del Servizio app di Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html). 
> * Archiviare i dati in un database e cercare le posizioni nelle vicinanze. Per altre informazioni, vedere la [Panoramica dei tipi di dati spaziali di SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) e [Query dei dati spaziali per Nearest Neighbor](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

> [!div class="nextstepaction"]
> [Visualizzare codice sorgente completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [Visualizzare esempio in tempo reale](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Per altre informazioni sulla copertura e sulle funzionalità di Mappe di Azure:

> [!div class="nextstepaction"]
> [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md)

Per altri esempi di codice e un'esperienza di codifica interattiva:

> [!div class="nextstepaction"]
> [Come usare il controllo mappa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)