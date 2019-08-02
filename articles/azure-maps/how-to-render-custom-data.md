---
title: Come eseguire il rendering di dati personalizzati in una mappa raster in mappe di Azure | Microsoft Docs
description: Eseguire il rendering di dati personalizzati in una mappa raster in mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b6343931287ed59363db2715641ca63a814a9c32
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638806"
---
# <a name="render-custom-data-on-a-raster-map"></a>Eseguire il rendering di dati personalizzati in una mappa raster

Questo articolo illustra come usare il [servizio immagini statiche](https://docs.microsoft.com/rest/api/maps/render/getmapimage) con la funzionalità di composizione delle immagini per consentire le sovrimpressioni sopra una mappa raster. La composizione dell'immagine include la possibilità di ottenere un riquadro raster, con dati aggiuntivi come puntine da disegno personalizzati, etichette e sovrapposizioni di geometria.

Per eseguire il rendering di puntine da disegno, etichette e sovrapposizioni di geometria personalizzati, è possibile usare l'applicazione post. È possibile usare le [API del servizio dati](https://docs.microsoft.com/rest/api/maps/data) di Azure Maps per archiviare ed eseguire il rendering di sovrimpressioni.


## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

Per completare le procedure descritte in questo articolo, è prima di tutto necessario [creare un account Azure Maps](how-to-manage-account-keys.md) nel piano tariffario S1.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Eseguire il rendering di puntine da disegno con etichette e un'immagine personalizzata

> [!Note]
> Per la procedura descritta in questa sezione è necessario un account Azure Maps nel piano tariffario s0 o S1.

Il livello S0 dell'account mappe di Azure supporta solo una singola istanza `pins` del parametro. Consente di eseguire il rendering fino a cinque puntine da disegno, specificato nella richiesta URL, con un'immagine personalizzata.

Per eseguire il rendering di puntine da disegno con etichette e un'immagine personalizzata, completare i passaggi seguenti:

1. Creare una raccolta in cui archiviare le richieste. Nell'app post, selezionare **nuovo**. Nella finestra **Crea nuova** selezionare **raccolta**. Assegnare un nome alla raccolta e selezionare il pulsante **Crea** . 

2. Per creare la richiesta, selezionare nuovamente **nuovo** . Nella finestra **Crea nuova** selezionare **Request**. Immettere un **nome di richiesta** per il puntine da disegno, selezionare la raccolta creata nel passaggio precedente come percorso in cui salvare la richiesta e quindi selezionare **Salva**.
    
    ![Creare una richiesta in post](./media/how-to-render-custom-data/postman-new.png)

3. Selezionare il metodo GET HTTP nella scheda generatore e immettere l'URL seguente per creare una richiesta GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Di seguito è illustrata l'immagine risultante:

    ![Puntina da disegno personalizzata con etichetta](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Ottenere dati dall'archivio dati di Mappe di Azure

> [!Note]
> Per la procedura descritta in questa sezione è necessario un account Azure Maps nel piano tariffario S1.

È anche possibile ottenere il percorso e aggiungere le informazioni sul percorso usando l' [API di caricamento dei dati](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Seguire questa procedura per caricare i dati di percorso e puntine da disegno.

1. Nell'app post, aprire una nuova scheda nell'insieme creato nella sezione precedente. Selezionare il metodo HTTP POST nella scheda generatore e immettere l'URL seguente per effettuare una richiesta POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Nella scheda **params (parametri** ) immettere le coppie chiave/valore seguenti, che vengono usate per l'URL della richiesta post. Sostituire il `subscription-key` valore con la chiave di sottoscrizione di Azure maps.
    
    ![Parametri chiave/valore in subposte](./media/how-to-render-custom-data/postman-key-vals.png)

3. Nella scheda **corpo** selezionare il formato di input non elaborato e scegliere JSON come formato di input dall'elenco a discesa. Fornire questo JSON come dati da caricare:
    
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

4. Selezionare **Send (Invia** ) ed esaminare l'intestazione Response (risposta). Una volta completata la richiesta, l'intestazione Location conterrà l'URI dello stato per controllare lo stato corrente della richiesta di caricamento. Il formato dell'URI di stato è il seguente.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Copiare l'URI dello stato e aggiungere il parametro Subscription-Key con il relativo valore corrispondente alla chiave di sottoscrizione dell'account Azure Maps usata per caricare i dati. Il formato dell'URI di stato avrà un aspetto simile al seguente:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Per ottenere il udId, aprire una nuova scheda nell'app post-up e selezionare GET HTTP Method nella scheda Builder ed effettuare una richiesta GET nell'URI di stato. Se il caricamento dei dati ha avuto esito positivo, si riceverà un udId nel corpo della risposta. Copiare udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Usare il `udId` valore ricevuto dall'API di caricamento dei dati per eseguire il rendering delle funzionalità sulla mappa. A tale scopo, aprire una nuova scheda nell'insieme creato nella sezione precedente. Selezionare il metodo GET HTTP nella scheda generatore e immettere l'URL seguente per effettuare una richiesta GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Di seguito è illustrata l'immagine della risposta:

    ![Ottenere dati dall'archivio dati di Mappe di Azure](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Eseguire il rendering di un poligono con colore e opacità

> [!Note]
> Per la procedura descritta in questa sezione è necessario un account Azure Maps nel piano tariffario S1.


È possibile modificare l'aspetto di un poligono usando i modificatori di stile con il [parametro path](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Nell'app post, aprire una nuova scheda nella raccolta creata in precedenza. Selezionare il metodo GET HTTP nella scheda generatore e immettere l'URL seguente per configurare una richiesta GET per eseguire il rendering di un poligono con colore e opacità:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Di seguito è illustrata l'immagine della risposta:

    ![Eseguire il rendering di un poligono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Eseguire il rendering di un cerchio e puntine da disegno con etichette personalizzate

> [!Note]
> Per la procedura descritta in questa sezione è necessario un account Azure Maps nel piano tariffario S1.


È possibile fare in modo che puntine da disegno e le rispettive etichette siano `sc` maggiori o minori usando il modificatore dello stile di ridimensionamento. Questo modificatore accetta un valore maggiore di zero. Un valore pari a 1 è la scala standard. I valori maggiori di 1 renderanno le puntine da disegno più grandi e i valori inferiori a 1 le renderanno più piccole. Per altre informazioni sui modificatori di stile, vedere [parametri del percorso del servizio immagini statiche](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Seguire questa procedura per eseguire il rendering di un cerchio e puntine da disegno con etichette personalizzate:

1. Nell'app post, aprire una nuova scheda nella raccolta creata in precedenza. Selezionare il metodo GET HTTP nella scheda generatore e immettere l'URL seguente per effettuare una richiesta GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Di seguito è illustrata l'immagine della risposta:

    ![Eseguire il rendering di un cerchio con puntine da disegno personalizzato](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Passaggi successivi


* Esplorare la documentazione dell'[API per il recupero dell'immagine della mappa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/render/getmapimage).
* Per ulteriori informazioni sul servizio dati di Azure Maps, vedere la [documentazione del servizio](https://docs.microsoft.com/rest/api/maps/data).

