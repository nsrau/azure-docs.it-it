---
title: Ricerca con Servizi Location Based di Azure | Microsoft Docs
description: Cercare un punto di interesse vicino tramite Servizi Location Based di Azure
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
ms.openlocfilehash: 8da7d9112c9527945ab4b524625603faa84cf00d
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="search-nearby-point-of-interest-using-azure-location-based-services"></a>Cercare un punto di interesse vicino tramite Servizi Location Based di Azure

Questa esercitazione illustra come configurare un account con Servizi Location Based di Azure e quindi usare le API fornite per cercare un punto di interesse. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account con Servizi Location Based di Azure
> * Conoscere la chiave primaria per l'account Servizi Location Based di Azure
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Usare il servizio di ricerca per trovare il punto di interesse più vicino

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

# <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure
Accedere al [Portale di Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-location-based-services"></a>Creare un account con Servizi Location Based di Azure

Seguire questa procedura per creare un nuovo account Servizi Location Based.

1. Nell'angolo superiore sinistro del [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa**.
2. Nella casella *Cerca nel Marketplace* digitare **Servizi Location Based**.
3. In *Risultati* fare clic su **Servizi Location Based (preview)**. Fare clic sul pulsante **Crea** visualizzato sotto la mappa. 
4. Nella pagina **Crea account Servizi Location Based** immettere i valori seguenti:
    - Il *nome* del nuovo account. 
    - La *sottoscrizione* da usare per l'account.
    - Il nome del *gruppo di risorse* per l'account. Per il gruppo di risorse è possibile selezionare l'opzione *Crea nuovo* o *Usa esistente*.
    - Selezionare la *località del gruppo di risorse*.
    - Leggere le *condizioni per l'anteprima* e selezionare la casella di controllo per accettarle. 
    - Infine fare clic sul pulsante **Crea**.
   
    ![Creare un account Servizi Location Based nel portale](./media/tutorial-search-location/create-lbs-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Ottenere la chiave primaria per l'account

Dopo aver creato l'account Servizi Location Based, seguire questa procedura per collegarlo alle API di ricerca mappe:

1. Aprire l'account Servizi Location Based nel portale.
2. Passare a **IMPOSTAZIONI** per il proprio account e quindi selezionare **Chiavi**.
3. Copiare il valore di **Chiave primaria** negli Appunti. Salvare la chiave in locale per usarla nei passaggi successivi. 

    ![Ottenere la chiave primaria nel portale](./media/tutorial-search-location/lbs-get-key.png)


<a id="createmap"></a>

## <a name="create-new-web-page-using-azure-map-control-api"></a>Creare una nuova pagina Web usando l'API del controllo mappa di Azure
L'API del controllo mappa di Azure è una pratica libreria client che consente di integrare facilmente Servizi Location Based di Azure in un'applicazione Web. Questa API nasconde la complessità del codice essenziale delle chiamate al servizio REST e consente di migliorare la produttività con componenti personalizzabili. La procedura seguente illustra come creare una pagina HTML statica incorporata usando l'API del controllo mappa di Servizi Location Based. 

1. Nel computer locale creare un nuovo file con il nome **MapSearch.html**. 
2. Aggiungere al file i componenti HTML seguenti:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

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
    Si noti che l'intestazione HTML include i file di risorse CSS e JavaScript ospitati dalla libreria del controllo mappa di Azure. Si noti inoltre il segmento *script* nel *corpo* del file HTML, che deve contenere il codice JavaScript inline per l'accesso alle API di Servizi Location Based di Azure.
 
3.  Aggiungere il codice JavaScript seguente al blocco *script* del file HTML. Usare la chiave primaria dell'account Servizi Location Based nello script. 

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    Questo segmento avvia l'API del controllo mappa per la chiave dell'account Servizi Location Based di Azure. Lo spazio dei nomi che contiene l'API del controllo mappa di Azure e i componenti visivi correlati è denominato **Atlas**. **atlas.Map** fornisce il controllo per una mappa Web visiva e interattiva. È possibile osservare l'aspetto grafico della mappa aprendo la pagina HTML nel browser. 

4. Nel blocco *script* aggiungere il codice JavaScript seguente per specificare un livello per i segnaposto di ricerca nel controllo mappa:

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

5. Salvare il file nel computer. 


<a id="usesearch"></a>

## <a name="use-search-service-to-find-nearby-point-of-interest"></a>Usare il servizio di ricerca per trovare il punto di interesse più vicino

Questa sezione illustra come usare l'API del servizio di ricerca di Servizi Location Based di Azure per trovare un punto di interesse sulla mappa. Si tratta di un'API RESTful progettata per gli sviluppatori che consente di eseguire la ricerca di indirizzi, punti di interesse e altre informazioni geografiche. Il servizio di ricerca assegna le informazioni di latitudine e longitudine a un indirizzo specificato. 

1. Aprire il file **MapSearch.html** creato nella sezione precedente e aggiungere il codice JavaScript seguente al blocco *script* per illustrare il servizio di ricerca. 
    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```
    Questo frammento di codice crea un oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) e aggiunge un gestore eventi per analizzare la risposta in ingresso. Per ottenere una risposta, raccoglie gli indirizzi, i nomi e le informazioni su latitudine e longitudine per ogni posizione restituita, nella variabile `searchPins`. Infine aggiunge al controllo `map` questa raccolta di punti di posizione come segnaposto. 

2. Aggiungere il codice seguente al blocco *script* per inviare l'oggetto XMLHttpRequest al servizio di ricerca di Servizi Location Based di Azure:

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "&api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Questo frammento di codice usa l'API di base del servizio di ricerca, definita **ricerca fuzzy**. Supporta anche input con un basso livello di corrispondenza fuzzy gestendo qualsiasi combinazione di token di indirizzo o *punto di interesse*. Esegue la ricerca della **stazione di servizio** più vicina, per l'indirizzo con i valori di latitudine e longitudine indicati, e all'interno del raggio specificato. Usa la chiave primaria dell'account fornita in precedenza nel file di esempio per eseguire la chiamata a Servizi Location Based. Infine restituisce i risultati come coppie di latitudine e longitudine per le posizioni trovate. È possibile osservare i segnaposto di ricerca aprendo la pagina HTML nel browser. 

3. Aggiungere le righe seguenti al blocco *script* per creare le finestre popup per i punti di interesse restituiti dal servizio di ricerca:

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    La funzione **atlas.Popup** dell'API consente di aggiungere una finestra di informazioni ancorata alla posizione richiesta sulla mappa. Questo frammento di codice imposta il contenuto e la posizione per la finestra popup e aggiunge un listener di eventi al controllo `map`, che rimane in attesa del passaggio del _mouse_ sulla finestra popup. 

4. Salvare il file, aprire **MapSearch.html** in un Web browser di propria scelta e osservare il risultato. A questo punto, la mappa nel browser mostrerà finestre popup di informazioni al passaggio del mouse sui segnaposto di ricerca visualizzati, in modo simile a quanto illustrato nella figura seguente. 

    ![Controllo mappa e servizio di ricerca di Azure](./media/tutorial-search-location/lbs-map-search.png)


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un account con Servizi Location Based di Azure
> * Ottenere la chiave primaria per l'account
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Usare il servizio di ricerca per trovare il punto di interesse più vicino

Proseguire con l'esercitazione [Trovare il percorso per raggiungere un punto di interesse tramite Servizi Location Based di Azure](./tutorial-route-location.md) per informazioni su come usare Servizi Location Based di Azure per individuare il percorso da seguire per raggiungere un punto di interesse. 
