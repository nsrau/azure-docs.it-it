---
title: Come eseguire il rendering di dati personalizzati in una mappa raster nelle mappe di Azure | Microsoft Docs
description: Eseguire il rendering di dati personalizzati in una mappa raster nelle mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 46f08aaa33563f620e7a011620730249e903f7b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086599"
---
# <a name="render-custom-data-on-a-raster-map"></a>Eseguire il rendering di dati personalizzati in una mappa raster

Questo articolo illustra come usare il [servizio immagini statiche](https://docs.microsoft.com/rest/api/maps/render/getmapimage) con funzionalità di composizione di immagini per consentire le sovrimpressioni su una mappa raster. Composizione dell'immagine include la possibilità di ottenere un riquadro raster, con dati aggiuntivi come pushpins personalizzati, etichette e sovrapposizioni di geometria.

Per eseguire il rendering personalizzate puntine da disegno, etichette e le sovrimpressioni geometry, è possibile usare l'applicazione di Postman. È possibile usare le mappe di Azure [API del servizio dati](https://docs.microsoft.com/rest/api/maps/data) archiviare ed eseguire il rendering le sovrimpressioni.


## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

Per completare le procedure descritte in questo articolo, è necessario innanzitutto [creare un account mappe di Azure](how-to-manage-account-keys.md) nel piano tariffario S1.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Eseguire il rendering puntine da disegno con le etichette e un'immagine personalizzata

> [!Note]
> La procedura descritta in questa sezione è necessario un account mappe di Azure nel piano tariffario S0 o S1.

Le mappe di Azure dell'account supportati dal piano S0 solo una singola istanza del `pins` parametro. Consente di eseguire il rendering fino a cinque puntine da disegno, specificati nella richiesta dell'URL, con un'immagine personalizzata.

Per eseguire il rendering puntine da disegno con le etichette e un'immagine personalizzata, completare questi passaggi:

1. Creare una raccolta in cui archiviare le richieste. Selezionare l'app Postman **New**. Nel **Crea nuovo** finestra, seleziona **raccolta**. Denominare la raccolta e selezionare il **Create** pulsante. 

2. Per creare la richiesta, selezionare **New** nuovamente. Nel **Crea nuovo** finestra, seleziona **richiesta**. Immettere un **nome della richiesta** per i simboli, selezionare la raccolta è stato creato nel passaggio precedente come la posizione in cui si desidera salvare la richiesta e quindi selezionare **salvare**.
    
    ![Creare una richiesta in Postman](./media/tutorial-geofence/postman-new.png)

3. Selezionare il metodo HTTP GET nella scheda builder e immettere l'URL seguente per creare una richiesta GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Ecco l'immagine risultante:

    ![Una puntina da disegno personalizzato con un'etichetta](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Ottenere dati dall'archivio dati di Mappe di Azure

> [!Note]
> La procedura descritta in questa sezione è necessario un account mappe di Azure nel piano tariffario S1.

È anche possibile ottenere le informazioni sul percorso di percorso e il pin tramite il [API di caricamento dati](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Seguire questa procedura per caricare i dati di percorso e puntine da disegno.

1. Nell'app Postman, aprire una nuova scheda nella raccolta creata nella sezione precedente. Selezionare il metodo HTTP POST nella scheda builder e immettere l'URL seguente per effettuare una richiesta POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Nel **Params** , immettere le seguenti coppie chiave/valore, che vengono usate per l'URL della richiesta POST. Sostituire il `subscription-key` valore con la chiave di sottoscrizione di mappe di Azure.
    
    ![Parametri chiave/valore in Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Nel **corpo** scheda, selezionare il formato di input non elaborato e scegliere JSON come formato di input nell'elenco a discesa. Fornire questo codice JSON sotto forma di dati da caricare:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Selezionare **inviare** ed esaminare l'intestazione della risposta. L'intestazione location contiene l'URI usato per accedere o scaricare i dati per un uso futuro. Include anche un valore `udId` univoco per i dati caricati.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

5. Usare il `udId` valore ricevuto dall'API di caricare i dati per il rendering di funzionalità sulla mappa. A tale scopo, aprire una nuova scheda nella raccolta creata nella sezione precedente. Selezionare il metodo HTTP GET nella scheda builder e immettere l'URL per effettuare una richiesta GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Ecco l'immagine risultante:

    ![Ottenere dati dall'archivio dati di Mappe di Azure](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Eseguire il rendering di un poligono con un colore e opacità

> [!Note]
> La procedura descritta in questa sezione è necessario un account mappe di Azure nel piano tariffario S1.


È possibile modificare l'aspetto di un poligono usando i modificatori di stile con il [parametro path](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Nell'app Postman, aprire una nuova scheda nella raccolta creata in precedenza. Selezionare il metodo HTTP GET nella scheda builder e immettere l'URL seguente per configurare una richiesta GET per il rendering di un poligono con un colore e opacità:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

Ecco l'immagine risultante:

![Eseguire il rendering di un poligono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Eseguire il rendering di un cerchio e puntine da disegno con le etichette personalizzate

> [!Note]
> La procedura descritta in questa sezione è necessario un account mappe di Azure nel piano tariffario S1.


È possibile rendere puntine da disegno e le etichette in o aumentandone le dimensioni usando il `sc` modificatore di stile di scalabilità. Questo modificatore accetta un valore maggiore di zero. Un valore pari a 1 è la scala standard. I valori maggiori di 1 renderanno le puntine da disegno più grandi e i valori inferiori a 1 le renderanno più piccole. Per altre informazioni sui modificatori di stile, vedere [parametri del servizio percorso immagine statica](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Seguire questi passaggi per eseguire il rendering di un cerchio e puntine da disegno con le etichette personalizzate:

1. Nell'app Postman, aprire una nuova scheda nella raccolta creata in precedenza. Selezionare il metodo HTTP GET nella scheda builder e immettere l'URL per effettuare una richiesta GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

Ecco l'immagine risultante:

![Eseguire il rendering di un cerchio con puntine da disegno personalizzati](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Passaggi successivi


* Esplorare la documentazione dell'[API per il recupero dell'immagine della mappa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/render/getmapimage).
* Per altre informazioni sul servizio dati di mappe di Azure, vedere la [la documentazione del servizio](https://docs.microsoft.com/rest/api/maps/data).

