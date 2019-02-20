---
title: Come eseguire il rendering di dati personalizzati su una mappa raster in Mappe di Azure | Microsoft Docs
description: Eseguire il rendering di dati personalizzati su una mappa raster in Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9b3c0f7b1ff56cb269f6852be8fd2affeca8b8f1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119161"
---
# <a name="render-custom-data-on-raster-map"></a>Eseguire il rendering di dati personalizzati su una mappa raster

Questo articolo illustra come usare il [servizio immagini statiche](https://docs.microsoft.com/rest/api/maps/render/getmapimage) con la funzionalità di composizione immagini per consentire sovrimpressioni su una mappa raster. La composizione delle immagini include la possibilità di ottenere una tessera raster con dati aggiuntivi, come puntine da disegno personalizzate, etichette e sovrimpressioni di geometria. Per eseguire il rendering di geometria, etichette e puntine da disegno personalizzate, si userà l'applicazione Postman. Aprire l'app Postman, fare clic su New | Create New (Nuovo | Crea nuovo), selezionare e assegnare un nome a una raccolta o una cartella in cui salvare e fare clic su Save (Salva).

Per archiviare ed eseguire il rendering delle sovrimpressioni si useranno le [API del servizio dati](https://docs.microsoft.com/rest/api/maps/data) di Mappe di Azure. 


## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure 

Per seguire le procedure in questa guida è prima necessario vedere [Gestire l'account e le chiavi](how-to-manage-account-keys.md) per creare e gestire la sottoscrizione dell'account con il piano tariffario S1.

## <a name="render-pushpins-with-labels-and-custom-image"></a>Eseguire il rendering di puntine da disegno con etichette e un'immagine personalizzata

> [!Note]
> Questo esempio richiede un account Mappe di Azure con piano tariffario S0 o S1. 

Lo SKU S0 dell'account Mappe di Azure supporta solo una singola istanza del parametro `pins`, consentendo agli utenti di eseguire il rendering di un massimo di 5 puntine da disegno specificate nella richiesta URL con l'immagine personalizzata.

Per eseguire il rendering di puntine da disegno con etichette e un'immagine personalizzata, seguire questa procedura:

1. Aprire l'app Postman, fare clic su New (Nuovo) | Create New (Crea nuovo) e selezionare Request (Richiesta). Immettere un nome di richiesta per il rendering delle puntine da disegno, selezionare una raccolta o una cartella in cui salvarla e quindi fare clic su Save (Salva).
    
    ![Caricare i recinti virtuali usando Postman](./media/tutorial-geofence/postman-new.png)

2. Selezionare il metodo HTTP GET nella scheda del generatore e immettere l'URL seguente per effettuare una richiesta GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Di seguito è riportata l'immagine di risposta che si ottiene.

    ![rendering di puntine da disegno personalizzate con etichette](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Ottenere dati dall'archivio dati di Mappe di Azure

> [!Note]
> Questo esempio richiede un account Mappe di Azure con piano tariffario S1.

Le informazioni di posizione su percorso e puntine da disegno si possono ottenere anche tramite l'[API di caricamento dati](https://docs.microsoft.com/rest/api/maps/mapdata/upload). Seguire questa procedura per caricare i dati di percorso e puntine da disegno.

1. Nell'app Postman aprire una nuova scheda nella stessa raccolta creata in precedenza. Selezionare il metodo HTTP POST nella scheda del generatore e immettere l'URL seguente per effettuare una richiesta POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Fare clic su Params (Parametri) e immettere le coppie chiave/valore seguenti da usare per l'URL della richiesta POST. Sostituire il valore subscription-key con la chiave di sottoscrizione di Mappe di Azure.
    
    ![Parametri chiave-valore di Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Fare clic su **Body** (Corpo), quindi selezionare il formato di input non elaborato e scegliere JSON come formato di input nell'elenco a discesa. Specificare il codice JSON seguente come dati da caricare:
    
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

4. Fare clic su Send (Invia) e quindi esaminare l'intestazione della risposta. L'intestazione di posizione contiene l'URI per accedere o scaricare i dati per un uso futuro. Include anche un valore `udId` univoco per i dati caricati.   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. Usare il valore `udid` ricevuto dall'API di caricamento dati per il rendering delle funzionalità sulla mappa. A tale scopo, aprire una nuova scheda nella stessa raccolta creata in precedenza. Selezionare il metodo HTTP GET nella scheda del generatore e immettere l'URL seguente per effettuare una richiesta GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. L'immagine di risposta dovrebbe essere simile alla seguente:

    ![rendering dei dati caricati](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>Eseguire il rendering di un poligono con colore e opacità

> [!Note]
> Questo esempio richiede un account Mappe di Azure con piano tariffario S1.

È possibile modificare l'aspetto di un poligono usando i modificatori di stile con il [parametro path](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage#uri-parameters).

1. Nell'app Postman aprire una nuova scheda nella stessa raccolta creata in precedenza. Selezionare il metodo HTTP GET nella scheda del generatore e immettere l'URL seguente per effettuare una richiesta GET per il rendering di un poligono con colore e opacità:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

L'immagine di risposta dovrebbe essere simile alla seguente:

![eseguire il rendering di un poligono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>Eseguire il rendering di un poligono con un cerchio e puntine da disegno con etichette personalizzate

> [!Note]
> Questo esempio richiede un account Mappe di Azure con piano tariffario S1.

È possibile ingrandire o ridurre le dimensioni delle puntine da disegno e delle relative etichette usando il modificatore dello stile di scala 'sc'. Questo è un valore maggiore di zero. Un valore pari a 1 è la scala standard. I valori maggiori di 1 renderanno le puntine da disegno più grandi e i valori inferiori a 1 le renderanno più piccole. Per altre informazioni sui modificatori di stile, vedere i [parametri path del servizio immagini statiche](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

Seguire questa procedura per eseguire il rendering di un poligono con un cerchio e puntine da disegno con etichette personalizzate:

1. Nell'app Postman aprire una nuova scheda nella stessa raccolta creata in precedenza. Selezionare il metodo HTTP GET nella scheda del generatore e immettere l'URL seguente per effettuare una richiesta GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

L'immagine di risposta dovrebbe essere simile alla seguente:

![eseguire il rendering di un cerchio con puntine da disegno personalizzate](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Passaggi successivi

* Esplorare la documentazione dell'[API per il recupero dell'immagine della mappa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search).
* Per altre informazioni sulle funzionalità del Servizio dati di Mappe di Azure, vedere la [documentazione del servizio](https://docs.microsoft.com/rest/api/maps/data).