---
title: Eseguire ricerche con Mappe di Azure | Microsoft Docs
description: Eseguire ricerche vicino a un punto di interesse con Mappe di Azure
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 4a414b5b865d31dd99b54ef9693abafb5490a50f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601786"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Eseguire ricerche vicino a punti di interesse con Mappe di Azure

Questa esercitazione illustra come configurare un account con Mappe di Azure e quindi usare le API di Mappe per cercare un punto di interesse. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Mappe di Azure
> * Recuperare la chiave primaria per l'account di Mappe
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Usare il servizio di ricerca di Mappe per trovare un punto di interesse più vicino

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure
Accedere al [Portale di Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Creare un account con Mppe di Azure

Creare un nuovo account di Mappe con i passaggi seguenti:

1. Nell'angolo superiore sinistro del [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa**.
2. Nella casella *Cerca nel Marketplace* digitare **Mappe**.
3. Fra i *risultati* selezionare **Mappe**. Fare clic sul pulsante **Crea** visualizzato sotto la mappa. 
4. Nella pagina **Crea account di Mappe** immettere i valori seguenti:
    - Il *nome* del nuovo account. 
    - La *sottoscrizione* da usare per l'account.
    - Il nome del *gruppo di risorse* per l'account. Per il gruppo di risorse è possibile selezionare l'opzione *Crea nuovo* o *Usa esistente*.
    - Selezionare la *località del gruppo di risorse*.
    - Leggere la *Licenza* e l'*Informativa sulla Privacy* e selezionare la casella di controllo per accettare le condizioni. 
    - Selezionare il pulsante **Create** .
   
    ![Creare account di Mappe nel portale](./media/tutorial-search-location/create-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Ottenere la chiave primaria per l'account

Dopo che è stato creato l'account di Mappe, recuperare la chiave che consente di eseguire query nelle API di Mappe.

1. Aprire l'account di Mappe nel portale.
2. Nella sezione delle impostazioni selezionare **Chiavi**.
3. Copiare il valore di **Chiave primaria** negli Appunti. Salvarlo in locale per usarlo in seguito in questa esercitazione. 

    ![Ottenere la chiave primaria nel portale](./media/tutorial-search-location/get-key.png)


<a id="createmap"></a>

## <a name="create-a-new-map"></a>Creare una nuova mappa 
L'API del controllo mappa è una pratica libreria client che consente di integrare facilmente Mappe in un'applicazione Web. Questa API nasconde la complessità del codice essenziale delle chiamate al servizio REST e consente di migliorare la produttività con componenti personalizzabili. La procedura seguente illustra come creare una pagina HTML statica incorporata usando l'API del controllo mappa. 

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
    Si noti che l'intestazione HTML include i file di risorse CSS e JavaScript ospitati dalla libreria del controllo mappa di Azure. Si noti inoltre il segmento *script* nel *corpo* del file HTML, che deve contenere il codice JavaScript inline per l'accesso alle API di Mappe di Azure.
 
3. Aggiungere il codice JavaScript seguente al blocco *script* del file HTML. Sostituire la stringa **\<your account key\>** con la chiave primaria copiata dall'account Mappe.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Questo segmento avvia l'API del controllo mappa per la chiave dell'account di Mappe di Azure. Lo spazio dei nomi che contiene l'API e i componenti visivi correlati è denominato **Atlas**. **Atlas.Map** fornisce il controllo per una mappa Web visiva e interattiva. 
    
4. Salvare le modifiche al file e aprire la pagina HTML in un browser. Si tratta della mappa più semplice che è possibile creare chiamando **atlas.map** e fornendo la chiave dell'account. 

   ![Visualizzare la mappa](./media/tutorial-search-location/basic-map.png)


<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Aggiungere funzionalità di ricerca

Questa sezione illustra come usare l'API di ricerca di Mappe per trovare un punto di interesse sulla mappa. Si tratta di un'API RESTful progettata per gli sviluppatori che consente di eseguire la ricerca di indirizzi, punti di interesse e altre informazioni geografiche. Il servizio di ricerca assegna le informazioni di latitudine e longitudine a un indirizzo specificato. 

1. Aggiungere un nuovo livello alla mappa per visualizzare i risultati della ricerca. Aggiungere il codice Javascript seguente al blocco *script*, dopo il codice che inizializza la mappa. 

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

2. Creare un oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) e aggiungere un gestore di evento per analizzare la risposta JSON inviata dal servizio di ricerca di Mappe. Questo frammento di codice crea il gestore di evento per raccogliere indirizzi, nomi e le informazioni di latitudine e longitudine per ogni posizione restituita nella variabile `searchPins`. Infine aggiunge al controllo `map` questa raccolta di punti di posizione come segnaposto. 

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

3. Aggiungere il codice seguente al blocco *script* per compilare la query e inviare l'oggetto XMLHttpRequest al servizio di ricerca di Mappe:

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Questo frammento di codice usa l'API di base del servizio di ricerca, definita **ricerca fuzzy**. Supporta anche input con un basso livello di corrispondenza fuzzy, inclusa qualsiasi combinazione di token di indirizzo o punto di interesse. Cerca la **stazione di servizio** più vicina entro il raggio specificato dalle coordinate di latitudine e longitudine date. Usa la chiave primaria dell'account fornita in precedenza nel file di esempio per eseguire la chiamata a Mappe. e infine restituisce i risultati come coppie di latitudine e longitudine per le posizioni trovate. 
    
4. Salvare il file **MapSearch.html** e aggiornare il browser. Ora la mappa è centrata su Seattle e punti di colore blu contrassegnano le posizioni delle stazioni di benzina nell'area. 

   ![Visualizzare la mappa con i risultati della ricerca](./media/tutorial-search-location/pins-map.png)

5. È possibile visualizzare i dati non elaborati di cui la mappa esegue il rendering prendendo l'oggetto XMLHTTPRequest compilato nel file e immettendolo nel browser. Sostituire la \<chiave dell'account\> con la chiave primaria. 

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

A questo punto la pagina MapSearch può visualizzare le posizioni dei punti di interesse che vengono restituiti da una query di ricerca fuzzy. Aggiungere alcune funzionalità interattive e altre informazioni sulle posizioni. 

## <a name="add-interactive-data"></a>Aggiungere dati interattivi

La mappa creata finora analizza solo i dati di latitudine e longitudine per i risultati della ricerca. Se si esamina il codice JSON non elaborato restituito dal servizio di ricerca di Mappe, si noterà che contiene anche altre informazioni su ogni stazione, inclusi il nome e il numero civico. È possibile incorporare questi dati nella mappa con finestre popup interattive. 

1. Aggiungere le righe seguenti al blocco *script* per creare le finestre popup per i punti di interesse restituiti dal servizio di ricerca:

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
        popupPositionElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    La funzione **atlas.Popup** dell'API consente di aggiungere una finestra di informazioni ancorata alla posizione richiesta sulla mappa. Questo frammento di codice imposta il contenuto e la posizione per la finestra popup e aggiunge un listener di eventi al controllo `map`, che rimane in attesa del passaggio del _mouse_ sulla finestra popup. 

4. Salvare il file e aggiornare il browser. Ora la mappa nel browser mostrerà finestre popup di informazioni al passaggio del mouse sui punti creati mediante la ricerca. 

    ![Controllo mappa e servizio di ricerca di Azure](./media/tutorial-search-location/popup-map.png)


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un account con Mppe di Azure
> * Ottenere la chiave primaria per l'account
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Usare il servizio di ricerca per trovare il punto di interesse più vicino

L'esercitazione successiva illustra come visualizzare un itinerario tra due posizioni. 

> [!div class="nextstepaction"]
> [Trovare l'itinerario per una destinazione](./tutorial-route-location.md)
