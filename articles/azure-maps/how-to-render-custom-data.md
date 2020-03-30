---
title: Eseguire il rendering di dati personalizzati su una mappa raster Mappe di Microsoft Azure
description: In this article, you'll learn how to render custom data on a raster map by using Microsoft Azure Maps static image Service.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335512"
---
# <a name="render-custom-data-on-a-raster-map"></a>Rendering di dati personalizzati in una mappa raster

Questo articolo spiega come utilizzare il [servizio immagini statiche,](https://docs.microsoft.com/rest/api/maps/render/getmapimage)con la funzionalità di composizione delle immagini, per consentire le sovrapposizioni sopra una mappa raster. La composizione dell'immagine include la possibilità di ottenere un riquadro raster, con dati aggiuntivi come puntine personalizzate, etichette e sovrapposizioni di geometria.

Per eseguire il rendering di puntine, etichette e sovrapposizioni di geometria personalizzate, è possibile utilizzare l'applicazione Postman. È possibile usare le API di Azure Maps [Data Service](https://docs.microsoft.com/rest/api/maps/data) per archiviare ed eseguire il rendering delle sovrapposizioni.

> [!Tip]
> È spesso molto più conveniente usare Azure Maps Web SDK per mostrare una mappa semplice in una pagina Web piuttosto che usare il servizio di immagini statiche. Il Web SDK usa i riquadri della mappa e, a meno che l'utente non panoramichi e esegua lo zoom della mappa, spesso genererà solo una frazione di una transazione per ogni caricamento della mappa. Si noti che l'SDK Web di Mappe di Azure include opzioni per disabilitare la panoramica e lo zoom. Inoltre, Azure Maps Web SDK offre un set più completo di opzioni di visualizzazione dei dati rispetto a un servizio Web di mappe statiche.  

## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

Per completare le procedure descritte in questo articolo, è innanzitutto necessario creare un account di Azure Maps e ottenere la chiave dell'account Maps.To complete the procedures in this article, you first need to create an Azure Maps account and get your maps account key. Seguire le istruzioni in [Creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) per creare una sottoscrizione di account di Azure Maps e seguire i passaggi descritti in Ottenere la chiave [primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) per ottenere la chiave primaria per l'account. Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Rendering di puntine con etichette e un'immagine personalizzata

> [!Note]
> La procedura descritta in questa sezione richiede un account di Azure Maps nel piano tariffario S0 o S1.The procedure in this section requires an Azure Maps account in pricing tier S0 or S1.

Il livello S0 dell'account di Azure `pins` Maps supporta una sola istanza del parametro. Consente di eseguire il rendering fino a cinque puntine, specificati nella richiesta URL, con un'immagine personalizzata.

Per eseguire il rendering delle puntine con etichette e un'immagine personalizzata, completare questi passaggi:To render pushpins with labels and a custom image, complete these steps:

1. Creare una raccolta in cui archiviare le richieste. Nell'app Postman selezionare **Nuovo**. Nella finestra **Crea nuovo** selezionare **Raccolta**. Assegnare un nome alla raccolta e selezionare il pulsante **Crea.Name** the collection and select the Create button. 

2. Per creare la richiesta, selezionare di nuovo **Nuovo.** Nella finestra **Crea nuovo** selezionare **Richiedi**. Immettere un **Nome richiesta** per i puntine. Selezionare la raccolta creata nel passaggio precedente, come posizione in cui salvare la richiesta. Selezionare quindi **Salva**.
    
    ![Creare una richiesta in Postman](./media/how-to-render-custom-data/postman-new.png)

3. Selezionare il metodo GET HTTP nella scheda Generatore e immettere l'URL seguente per creare una richiesta GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Ecco l'immagine risultante:

    ![Una puntina da una puntina da una funzione personalizzata con un'etichetta](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Ottenere dati dall'archivio dati di Mappe di Azure

> [!Note]
> La procedura descritta in questa sezione richiede un account di Azure Maps nel piano tariffario S1.The procedure in this section requires an Azure Maps account in pricing tier S1.

È inoltre possibile ottenere le informazioni sul percorso e sui pin utilizzando [l'API di caricamento dei dati](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Seguire questa procedura per caricare i dati di percorso e puntine da disegno.

1. Nell'app Postman aprire una nuova scheda nella raccolta creata nella sezione precedente. Selezionare il metodo HTTP POST nella scheda Generatore e immettere l'URL seguente per effettuare una richiesta POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Nella scheda **Parametri** immettere le seguenti coppie chiave/valore, utilizzate per l'URL della richiesta POST. Sostituire `subscription-key` il valore con la chiave di sottoscrizione di Azure Maps.Replace the value with your Azure Maps subscription key.
    
    ![Parametri chiave/valore in Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Nella scheda **Corpo** selezionare il formato di input non elaborato e scegliere JSON come formato di input dall'elenco a discesa. Fornire questo JSON come dati da caricare:Provide this JSON as data to be uploaded:
    
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

4. Selezionare **Invia** ed esaminare l'intestazione della risposta. Una volta completata la richiesta, l'intestazione Location conterrà l'URI di stato per controllare lo stato corrente della richiesta di caricamento. L'URI di stato sarebbe nel formato seguente.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Copiare l'URI di stato e aggiungervi il parametro della chiave di sottoscrizione con il valore della chiave di sottoscrizione dell'account di Azure Maps.Copy your status URI and append the subscription-key parameter to it with the value of your Azure Maps account subscription key. Usare la stessa chiave di sottoscrizione dell'account usata per caricare i dati. Il formato URI di stato dovrebbe essere simile a quello riportato di seguito:The status URI format should look like the one below:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Per ottenere l'udId, apri una nuova scheda nell'app Postman. Selezionare Get HTTP method (Metodo GET) nella scheda Builder (Crea una richiesta GET all'URI di stato). Se il caricamento dei dati è riuscito, si riceverà un udId nel corpo della risposta. Copiare il file udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Utilizzare `udId` il valore ricevuto dall'API di caricamento dei dati per eseguire il rendering delle feature sulla mappa. A tale scopo, aprire una nuova scheda nella raccolta creata nella sezione precedente. Selezionare il metodo GET HTTP nella scheda generatore, sostituire la chiave di sottoscrizione e l'udId con i valori e immettere questo URL per effettuare una richiesta GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Ecco l'immagine della risposta:

    ![Ottenere dati dall'archivio dati di Mappe di Azure](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Rendering di un poligono con colore e opacità

> [!Note]
> La procedura descritta in questa sezione richiede un account di Azure Maps nel piano tariffario S1.The procedure in this section requires an Azure Maps account in pricing tier S1.


È possibile modificare l'aspetto di un poligono usando i modificatori di stile con il [parametro path](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Nell'app Postman aprire una nuova scheda nella raccolta creata in precedenza. Selezionare il metodo GET HTTP nella scheda Generatore e immettere l'URL seguente per configurare una richiesta GET per il rendering di un poligono con colore e opacità:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Ecco l'immagine della risposta:

    ![Rendering di un poligono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Rendering di un cerchio e puntine con etichette personalizzate

> [!Note]
> La procedura descritta in questa sezione richiede un account di Azure Maps nel piano tariffario S1.The procedure in this section requires an Azure Maps account in pricing tier S1.


È possibile modificare l'aspetto dei perni aggiungendo modificatori di stile. Ad esempio, per ingrandire o ridurre le etichette, `sc` utilizzare il modificatore "stile scala". Questo modificatore accetta un valore maggiore di zero. Un valore pari a 1 è la scala standard. I valori maggiori di 1 renderanno le puntine da disegno più grandi e i valori inferiori a 1 le renderanno più piccole. Per ulteriori informazioni sui modificatori di stile, vedere Parametri del percorso di [servizio immagine statica](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Per eseguire il rendering di un cerchio e di una puntina da una alette con etichette personalizzate, attenersi alla seguente procedura:

1. Nell'app Postman aprire una nuova scheda nella raccolta creata in precedenza. Selezionare il metodo GET HTTP nella scheda Generatore e immettere questo URL per effettuare una richiesta GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Ecco l'immagine della risposta:

    ![Rendering di un cerchio con puntine personalizzate](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Per modificare il colore delle puntine dell'ultimo passaggio, modificare il modificatore di stile "co". Guarda `pins=default|la15+50|al0.66|lc003C62|co002D62|`, il colore corrente verrebbe specificato come #002D62 in CSS. Supponiamo che tu voglia cambiarlo in #41d42a. Scrivere il nuovo valore di colore dopo l'identificatore "co", in questo modo: `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. Effettuare una nuova richiesta GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Ecco l'immagine di risposta dopo aver modificato i colori dei perni:

    ![Rendering di un cerchio con puntine aggiornate](./media/how-to-render-custom-data/circle-updated-pins.png)

Analogamente, è possibile modificare, aggiungere e rimuovere altri modificatori di stile.

## <a name="next-steps"></a>Passaggi successivi


* Esplorare la documentazione dell'[API per il recupero dell'immagine della mappa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/render/getmapimage).
* Per altre informazioni su Azure Maps Data Service, vedere la documentazione del [servizio.](https://docs.microsoft.com/rest/api/maps/data)

