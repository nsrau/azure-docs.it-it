---
title: Eseguire ricerche con Mappe di Azure | Microsoft Docs
description: Eseguire ricerche vicino a un punto di interesse con Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 01/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ce425278bfc0f9b95285c33e9863b508246d5e79
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992298"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Eseguire ricerche vicino a punti di interesse con Mappe di Azure

Questa esercitazione illustra come configurare un account con Mappe di Azure e quindi usare le API di Mappe per cercare un punto di interesse. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Mappe di Azure
> * Recuperare la chiave primaria per l'account di Mappe
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Usare il servizio di ricerca di Mappe per trovare un punto di interesse più vicino

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Creare un account con Mppe di Azure

Creare un nuovo account di Mappe con i passaggi seguenti:

1. Nell'angolo superiore sinistro del [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa**.
2. Nella casella *Cerca nel Marketplace* digitare **Mappe**.
3. Fra i *risultati* selezionare **Mappe**. Fare clic sul pulsante **Crea** visualizzato sotto la mappa.
4. Nella pagina **Crea account di Mappe** immettere i valori seguenti:
    * Il *nome* del nuovo account.
    * La *sottoscrizione* da usare per l'account.
    * Il nome del *gruppo di risorse* per l'account. Per il gruppo di risorse è possibile selezionare l'opzione *Crea nuovo* o *Usa esistente*.
    * Selezionare la *località del gruppo di risorse*.
    * Leggere la *Licenza* e l'*Informativa sulla Privacy* e selezionare la casella di controllo per accettare le condizioni.
    * Fare clic sul pulsante **Create** (Crea).

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
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
        
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        
        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>
        
        <script>        
        var map, datasource, client, popup;
        
        function GetMap(){
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

3. Aggiungere il codice JavaScript seguente alla funzione `GetMap` del file HTML. Sostituire la stringa **\<Your Azure Maps Key\>** con la chiave primaria copiata dall'account Mappe.

   ```JavaScript
   //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
   atlas.setSubscriptionKey('<Your Azure Maps Key>');

   //Initialize a map instance.
   map = new atlas.Map('myMap');
   ```

   Questo segmento inizializza l'API Controllo mappa per la chiave dell'account Mappe di Azure. Lo spazio dei nomi che contiene l'API e i componenti visivi correlati è denominato **atlas**. **atlas.Map** fornisce il controllo per una mappa Web visiva e interattiva. 

4. Salvare le modifiche al file e aprire la pagina HTML in un browser. Si tratta della mappa più semplice che è possibile creare chiamando **atlas.map** e usando la chiave dell'account.

   ![Visualizzare la mappa](./media/tutorial-search-location/basic-map.png)

5. Dopo aver inizializzato la mappa, aggiungere il codice JavaScript seguente nella funzione `GetMap`. 

   ```JavaScript
   //Wait until the map resources have fully loaded.
   map.events.add('load', function () {

      //Create a data source and add it to the map.
      datasource = new atlas.source.DataSource();
      map.sources.add(datasource);

      //Add a layer for rendering point data.
      var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
         iconOptions: {
            iconImage: 'pin-round-darkblue',
            anchor: 'center',
            allowOverlap: true
         }
      });
      map.layers.add(resultLayer);

   });
   ```

   Alla mappa viene aggiunto un evento di caricamento che verrà attivato dopo che le risorse della mappa sono state caricate completamente. Nel gestore dell'evento di caricamento della mappa viene creata un'origine dati per archiviare i dati dei risultati. Viene creato un livello simboli che viene allegato all'origine dati. Questo livello consente di specificare la modalità di rendering dei dati dei risultati nell'origine dati. In questo caso verrà usata l'icona di una puntina rotonda di colore blu scuro al centro delle coordinate dei risultati che consente la sovrapposizione di altre icone. 

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Aggiungere funzionalità di ricerca

Questa sezione illustra come usare l'API di ricerca di Mappe per trovare un punto di interesse sulla mappa. Si tratta di un'API RESTful progettata per gli sviluppatori che consente di eseguire la ricerca di indirizzi, punti di interesse e altre informazioni geografiche. Il servizio di ricerca assegna le informazioni di latitudine e longitudine a un indirizzo specificato. Il **modulo del servizio** illustrato di seguito può essere usato per cercare un percorso con l'API Ricerca mappe.

### <a name="service-module"></a>Modulo del servizio

1. Per creare un'istanza del servizio client, aggiungere il codice Javascript seguente nel gestore dell'evento di caricamento della mappa.

    ```JavaScript
    //Create an instance of the services client.
     client = new atlas.service.Client(atlas.getSubscriptionKey());
    ```

2. Aggiungere quindi il blocco script seguente per compilare la query di ricerca. Viene usato il servizio di ricerca fuzzy, che è un'API di base del servizio di ricerca. Il servizio di ricerca fuzzy gestisce la maggior parte degli input fuzzy come indirizzi, località e punti di interesse. Questo codice consente di cercare nelle vicinanze le stazioni di servizio all'interno del raggio specificato. La risposta viene quindi analizzata in formato GeoJSON e aggiunta all'origine dati, in modo che venga eseguito automaticamente il rendering dei dati nella mappa tramite il livello simboli. Nell'ultima parte dello script viene impostata la visualizzazione delle videocamere della mappa usando il rettangolo di selezione dei risultati con la proprietà [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa. Per compensare le dimensioni in pixel delle icone di simbolo, viene aggiunta una spaziatura interna dal momento che il rettangolo di selezione viene calcolato in base alle coordinate. 
 
   ```JavaScript
   //Execute a POI search query then add the results to the map.
    client.search.getSearchPOI('gasoline station', {
        lat: 47.6292,
        lon: -122.2337,
        radius: 100000
    }).then(response => {
        //Parse the response into GeoJSON so that the map can understand.
        var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);
        var results = geojsonResponse.getGeoJsonResults();

        //Add the results to the data source so they can be rendered. 
        datasource.add(results);

        // Set the camera bounds
        map.setCamera({
            bounds: results.bbox,
            padding: 50
        });
    });
   ```
 
3. Salvare il file **MapSearch.html** e aggiornare il browser. Ora la mappa è centrata su Seattle e punti di colore blu contrassegnano le posizioni delle stazioni di benzina nell'area.

   ![Visualizzare la mappa con i risultati della ricerca](./media/tutorial-search-location/pins-map.png)

4. È possibile visualizzare i dati non elaborati di cui la mappa esegue il mapping immettendo la richiesta HTTP seguente nel browser. Sostituire la \<chiave di Mappe di Azure\> con la chiave primaria.

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<Your Azure Maps Key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

A questo punto la pagina MapSearch può visualizzare le posizioni dei punti di interesse che vengono restituiti da una query di ricerca fuzzy. Aggiungere alcune funzionalità interattive e altre informazioni sulle posizioni.

## <a name="add-interactive-data"></a>Aggiungere dati interattivi

La mappa creata finora analizza solo i dati di longitudine/latitudine per i risultati della ricerca. Se si esamina il codice JSON non elaborato restituito dal servizio di ricerca di Mappe, si noterà che contiene anche altre informazioni su ogni stazione, inclusi il nome e il numero civico. È possibile incorporare questi dati nella mappa con finestre popup interattive.

1. Per eseguire una query nel servizio di ricerca fuzzy, aggiungere le righe di codice seguenti nel gestore dell'evento di caricamento della mappa dopo il codice. Verrà creata un'istanza di un popup e verrà aggiunto un evento mouseover al livello simboli.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```
    
    La funzione **atlas.Popup** dell'API consente di aggiungere una finestra di informazioni ancorata alla posizione richiesta sulla mappa. 
      
2. Nel tag *script* aggiungere il codice seguente dopo la funzione `GetMap` per mostrare nel popup le informazioni sui risultati al passaggio del mouse. 

   ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occured on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
   ```

2. Salvare il file e aggiornare il browser. Ora la mappa nel browser mostrerà finestre popup di informazioni al passaggio del mouse sui punti creati mediante la ricerca.

    ![Controllo mappa e servizio di ricerca di Azure](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un account con Mppe di Azure
> * Ottenere la chiave primaria per l'account
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Usare il servizio di ricerca per trovare il punto di interesse più vicino

È possibile accedere all'esempio di codice per questa esercitazione qui:

> [Cercare una posizione con Mappe di Azure](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

[Vedere qui l'esempio in tempo reale](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

L'esercitazione successiva illustra come visualizzare un itinerario tra due posizioni.

> [!div class="nextstepaction"]
> [Trovare l'itinerario per una destinazione](./tutorial-route-location.md)
