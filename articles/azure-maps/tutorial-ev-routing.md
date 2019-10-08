---
title: Pianificazione di itinerari per veicoli elettrici tramite Azure Notebooks (Python) | Microsoft Docs
description: Pianificazione di itinerari per veicoli elettrici tramite le API di Mappe di Azure e Azure Notebooks.
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 30751bebd397b378924453987462c9e2b3b55ebf
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803895"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Pianificazione di itinerari per veicoli elettrici tramite Azure Notebooks (Python)

Mappe di Azure è un portfolio di API di servizi geospaziali integrate in modalità nativa in Azure che consentono a sviluppatori, aziende e ISV di creare app con riconoscimento della posizione e soluzioni per Internet delle cose, mobilità, logistica e localizzazione dei beni. Le API REST di Mappe di Azure possono essere chiamate da linguaggi come Python e R per scenari di analisi di dati geospaziali e Machine Learning. Mappe di Azure include un set completo di [API di pianificazione di itinerari]([https://docs.microsoft.com/rest/api/maps/route) che consente di calcolare gli itinerari tra diversi punti dati in base a varie condizioni, come tipo di veicolo o area raggiungibile. Questa esercitazione illustra uno scenario in cui il conducente di un veicolo elettrico, con una carica della batteria insufficiente, viene aiutato a trovare la stazione di ricarica più vicina possibile rispetto al tempo di guida.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare ed eseguire un notebook di Jupyter in [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) nel cloud
> * Chiamare le API REST di Mappe di Azure in Python
> * Cercare un'area raggiungibile in base al modello di consumo del veicolo elettrico.
> * Cercare le stazioni di ricarica per veicoli elettrici entro l'area raggiungibile (o isocrona).
> * Eseguire il rendering dei limiti dell'area raggiungibile e delle stazioni di ricarica su una mappa.
> * Trovare e visualizzare l'itinerario per la stazione di ricarica di veicoli elettrici più vicina in base al tempo.


## <a name="prerequisites"></a>Prerequisiti 

Per completare i passaggi di questa esercitazione, è prima di tutto necessario creare un account di Mappe di Azure e ottenere la chiave primaria (chiave di sottoscrizione). Seguire le istruzioni in [Gestire l'account](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) per creare una sottoscrizione dell'account Mappe di Azure con il piano tariffario S1 ed eseguire la procedura descritta in [Ottenere la chiave primaria](./tutorial-search-location.md#getkey) per ottenere la chiave di sottoscrizione primaria per l'account.

## <a name="create-an-azure-notebook"></a>Creare un notebook di Azure

Per seguire questa esercitazione, è necessario creare un progetto di Azure Notebooks, quindi scaricare ed eseguire il file di notebook di Jupyter. Il file di notebook contiene codice Python, che implementa lo scenario dell'esercitazione. Seguire la procedura seguente per creare un progetto di Azure Notebooks e caricarvi il documento del notebook di Jupyter.

1. Passare ad [Azure Notebooks](https://notebooks.azure.com) ed eseguire l'accesso. Per altre informazioni, vedere [Avvio rapido](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
2. Nella parte superiore della pagina del profilo pubblico selezionare **Progetti personali**.

    ![progetto personale](./media/tutorial-ev-routing/myproject.png)

3. Nella pagina **Progetti personali** selezionare **Nuovo progetto**.
 
   ![nuovo progetto](./media/tutorial-ev-routing/create-project.png)

4. Nella finestra popup **Crea nuovo progetto** visualizzata immettere le informazioni seguenti e quindi fare clic su **Crea**:
    * Nome del progetto
    * ID progetto
 
    ![crea progetto](./media/tutorial-ev-routing/create-project-window.png)

5. Una volta creato il progetto, scaricare il [file del documento del notebook di Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) dal [repository di notebook di Jupyter per Mappe di Azure](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

6. Selezionare il progetto nell'elenco della pagina **Progetti personali** e fare clic su **Carica** per caricare il file del documento del notebook di Jupyter. Caricare il file dal computer e fare clic su **Fatto**.

    ![caricare il notebook](./media/tutorial-ev-routing/upload-notebook.png)

7. Dopo il caricamento, il file verrà visualizzato nella pagina del progetto. Fare clic sul file del notebook per aprirlo come notebook di Jupyter.

Per comprendere meglio la funzionalità implementata nel file del notebook, è consigliabile eseguire il codice del notebook una cella alla volta. Per eseguire il codice in ogni cella, fare clic sul pulsante **Esegui** nella parte superiore dell'app del notebook.

  ![Run](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Installare i pacchetti a livello di progetto

Per eseguire il codice del notebook, è necessario installare i pacchetti a livello di progetto. Seguire questa procedura per installare i pacchetti necessari:

1. Scaricare il file ["requirements.txt"](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) dal [repository di notebook di Jupyter per Mappe di Azure](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) e caricarlo nel progetto.
2. Nel dashboard del progetto selezionare **Project Settings** (Impostazioni progetto). 
3. Nella finestra popup visualizzata selezionare la scheda **Environment** (Ambiente) e quindi **+ Add** (Aggiungi).
4. In **Environment Setup Steps** (Passaggi di configurazione dell'ambiente) procedere come segue: 
    * Nel primo controllo a discesa scegliere **Requirements.txt**.
    * Nel secondo controllo a discesa scegliere il proprio file "requirements.txt".
    * Nel terzo controllo a discesa scegliere Python versione 3.6 come versione di Python.
7. Selezionare **Salva**.

    ![installare i pacchetti](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>Caricare i moduli e i framework necessari

Eseguire lo script seguente per caricare tutti i moduli e i framework necessari.

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>Richiesta dei limiti dell'area raggiungibile

In questo scenario una società di spedizioni ha alcuni veicoli elettrici nella propria flotta. Durante la giornata, i veicoli elettrici devono essere ricaricati senza la necessità di tornare nel magazzino. Ogni volta che l'autonomia del veicolo elettrico è inferiore a un'ora (carica insufficiente), è necessario cercare una serie di stazioni di ricarica situate entro l'area raggiungibile e ottenere informazioni sui limiti di tale area. Poiché la società preferisce usare itinerari che offrano un equilibrio tra costi contenuti e velocità, la richiesta di routeType è 'eco'. Lo script seguente chiama l'[API Get Route Range](https://docs.microsoft.com/rest/api/maps/route/getrouterange) del servizio di pianificazione di itinerari di Mappe di Azure con i parametri per il modello di consumo del veicolo e analizza la risposta per creare un oggetto poligonale in formato geoJSON che rappresenta l'area massima raggiungibile dell'auto.

Eseguire lo script nella cella seguente per ottenere i limiti dell'area raggiungibile del veicolo elettrico.

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get bounds for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>Cercare le stazioni di ricarica per veicoli elettrici entro l'area raggiungibile

Una volta ottenuta l'area raggiungibile (isocrona) per il veicolo elettrico, è possibile cercare le stazioni di ricarica in tale area. Lo script seguente chiama l'[API Post Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) di Mappe di Azure per cercare le stazioni di ricarica per veicoli elettrici entro i limiti dell'area massima raggiungibile dell'auto e quindi analizza la risposta in una matrice di località raggiungibili.

Eseguire lo script seguente per cercare stazioni di ricarica per veicoli elettrici entro l'area raggiungibile.

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Caricare l'area raggiungibile e i punti di ricarica nel servizio dati di Mappe di Azure

Per visualizzare le stazioni di ricarica e i limiti dell'area massima raggiungibile del veicolo elettrico sulla mappa, è necessario caricare i dati dei limiti e quelli delle stazioni di ricarica come oggetti geoJSON nel servizio dati di Mappe di Azure tramite l'[API Data Upload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Eseguire le due celle seguenti per caricare i dati dei limiti e dei punti di ricarica nel servizio dati di Mappe di Azure.

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload range data to Azure Maps data service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload EV charging stations data to Azure Maps data service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-charging-stations-and-reachable-range-on-map"></a>Eseguire il rendering delle stazioni di ricarica e dell'area raggiungibile sulla mappa

Dopo aver caricato i dati nel servizio dati, eseguire lo script seguente per chiamare il [servizio Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage) di Mappe di Azure per eseguire il rendering dei punti di ricarica e dei limiti dell'area massima raggiungibile sull'immagine statica della mappa.

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![area delle posizioni](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>Trovare la stazione di ricarica ottimale in cui fermarsi

Dopo aver trovato tutte le possibili stazioni di ricarica entro l'area raggiungibile, è necessario individuare quelle che è possibile raggiungere nella quantità di tempo minima. Lo script seguente chiama l'[API Matrix routing](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) di Mappe di Azure che restituisce per la posizione del veicolo specificata il tempo di viaggio e la distanza dalla posizione di ogni stazione di ricarica specificata. Lo script nella cella successiva analizza la risposta per ottenere la posizione della stazione di ricarica raggiungibile più vicina rispetto al tempo.

Eseguire la cella seguente per trovare la stazione di ricarica raggiungibile più vicina che è possibile raggiungere nella quantità di tempo minima.

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>Calcolare l'itinerario per la stazione di ricarica più vicina

Dopo aver trovato la stazione di ricarica più vicina, chiamare l'[API Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) per richiedere l'itinerario dettagliato dalla posizione corrente del veicolo elettrico alla stazione di ricarica.

Eseguire lo script nella cella seguente per ottenere l'itinerario e analizzare la risposta per creare un oggetto geoJSON che lo rappresenta.

```python
# Get route from current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Visualizzare l'itinerario

Per visualizzare l'itinerario, è necessario prima di tutto caricare i relativi dati come oggetto geoJSON nel servizio dati di Mappe di Azure usando l'[API Data Upload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) di Mappe di Azure. Quindi chiamare il servizio di rendering, l'[API Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage), per eseguire il rendering dell'itinerario sulla mappa e visualizzarlo.

Eseguire lo script seguente per ottenere un'immagine dell'itinerario sottoposto a rendering sulla mappa.

```python
# Upload route data to Azure data service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Route](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come chiamare direttamente le API REST di Mappe di Azure e visualizzare i dati di Mappe di Azure tramite Python.

Per esplorare le API di Mappe di Azure usate in questa esercitazione, vedere:

* [Get Route Range](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Post Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Data Upload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Render - Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Post Route Matrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Per un elenco completo delle API REST di Mappe di Azure, vedere:

* [API REST di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/#reference)

Per altre informazioni su Azure Notebooks, vedere:

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)