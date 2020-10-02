---
title: 'Esercitazione: Creare un recinto virtuale e monitorare i dispositivi in Mappe di Azure'
description: Informazioni su come configurare un recinto virtuale. Vedere come tenere traccia dei dispositivi rispetto al recinto virtuale usando il servizio spaziale di Mappe di Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b374bbe086281c7f7914334be6ca275f0fd05b7f
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056510"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Esercitazione: Configurare un recinto virtuale con Mappe di Azure

Questa esercitazione illustra le basi per creare e usare i servizi di recinto virtuale di Mappe di Azure. nel contesto dello scenario seguente:

*Il direttore di un cantiere in costruzione deve monitorare i macchinari che entrano ed escono dal perimetro del cantiere. Ogni volta che un macchinario entra o esce, viene inviata una notifica tramite posta elettronica al direttore dei lavori.*

Mappe di Azure offre diversi servizi che consentono di monitorare i macchinari che entrano ed escono dal cantiere. In questa esercitazione:

> [!div class="checklist"]
> * Caricare i [dati GeoJSON di geofencing](geofence-geojson.md) che definiscono le aree del cantiere da monitorare. Si userà l'[API di caricamento dati](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) per caricare i recinti virtuali come coordinate di poligono nell'account di Mappe di Azure.
> * Configurare due [app per la logica](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) che, quando attivate, invieranno notifiche tramite posta elettronica al direttore dei lavori del cantiere quando i macchinari entrano ed escono dall'area del recinto virtuale.
> * Usare [Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview) per sottoscrivere gli eventi di entrata e uscita dal recinto virtuale di Mappe di Azure. Configurare due sottoscrizioni di eventi webhook che chiameranno gli endpoint HTTP definiti nelle due app per la logica. Le app per la logica invieranno quindi le notifiche di posta elettronica appropriate per i macchinari che escono o entrano nel recinto virtuale.
> * Usare l'[API GET di ricerca recinto virtuale](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) per ricevere notifiche quando un macchinario esce ed entra nelle aree del recinto virtuale.

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un account di Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione

Questa esercitazione usa l'applicazione [Postman](https://www.postman.com/), ma è possibile scegliere un ambiente di sviluppo API diverso.

## <a name="upload-geofencing-geojson-data"></a>Caricare i dati GeoJSON di geofencing

In questa esercitazione si caricheranno dati GeoJSON di geofencing che contengono un oggetto `FeatureCollection`. La `FeatureCollection` contiene due recinti virtuali che definiscono le aree poligonali all'interno del cantiere. Il primo recinto virtuale non presenta alcuna scadenza o restrizione. Il secondo può essere sottoposto a query solo durante gli orari di ufficio (9:00 - 17:00 fuso orario del Pacifico) e non sarà più valido dopo il 1° gennaio 2022. Per altre informazioni sul formato GeoJSON, vedere [Dati GeoJSON di geofencing](geofence-geojson.md).

>[!TIP]
>È possibile aggiornare i dati di geofencing in qualsiasi momento. Per altre informazioni, vedere [API di caricamento dati](https://docs.microsoft.com/rest/api/maps/data/uploadpreview).

1. Aprire l'app Postman. Nella parte superiore selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Collection** (Raccolta). Assegnare un nome alla raccolta e selezionare **Create** (Crea).

2. Per creare la richiesta, selezionare nuovamente **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare la raccolta creata nel passaggio precedente e fare clic su **Save** (Salva).

3. Selezionare il metodo HTTP **POST** nella scheda del generatore e immettere l'URL seguente per caricare i dati di geofencing in Mappe di Azure. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Il parametro `geojson` nel percorso dell'URL rappresenta il formato dei dati caricati.

4. Selezionare la scheda **Corpo**. Selezionare **raw** e quindi **JSON** come formato di input. Copiare e incollare i dati GeoJSON seguenti nell'area di testo **Body** (Corpo):

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Selezionare **Send** (Invia) e attendere l'elaborazione della richiesta. Al completamento della richiesta, passare alla scheda **Headers** (Intestazioni) della risposta. Copiare il valore della chiave **Location** (Posizione), ovvero lo `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Per controllare lo stato della chiamata API, creare una richiesta HTTP **GET** sullo `status URL`. È necessario accodare la chiave di sottoscrizione primaria all'URL per l'autenticazione. La richiesta **GET** dovrebbe essere simile all'URL seguente:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Quando viene completata, la richiesta HTTP **GET** restituisce `resourceLocation`. `resourceLocation` contiene il valore `udid` univoco per il contenuto caricato. Sarà necessario salvare `udid` per eseguire query sull'API Get Geofence nell'ultima sezione dell'esercitazione. Se si vuole, è possibile usare l'URL `resourceLocation` per recuperare i metadati da questa risorsa nel passaggio successivo.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Per recuperare i metadati del contenuto, creare una richiesta HTTP **GET** sull'URL `resourceLocation` recuperato nel passaggio 7. Assicurarsi di accodare la chiave di sottoscrizione primaria all'URL per l'autenticazione. La richiesta **GET** dovrebbe essere simile all'URL seguente:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Quando la richiesta HTTP **GET** viene completata correttamente, il corpo della risposta conterrà l'oggetto `udid` specificato in `resourceLocation` nel passaggio 7. Conterrà anche la posizione per accedere e scaricare il contenuto in futuro, oltre ad altri metadati sul contenuto. Un esempio di risposta complessiva è:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Creare flussi di lavoro in App per la logica di Azure

Successivamente creare due endpoint di [app per la logica](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) che attiveranno una notifica tramite posta elettronica. Ecco come creare il primo:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nell'angolo in alto a sinistra del portale di Azure selezionare **Crea una risorsa**.

3. Nella casella **Cerca nel Marketplace** digitare **App per la logica**.

4. Nei risultati selezionare **App per la logica** > **Crea**.

5. Nella pagina **App per la logica** immettere i valori seguenti:
    * La **sottoscrizione** da usare per l'app per la logica.
    * Il nome del **gruppo di risorse** per l'app per la logica. Per il gruppo di risorse è possibile selezionare l'opzione **Crea nuovo** o **Usa esistente**.
    * Il **nome dell'app per la logica**. In questo caso usare `Equipment-Enter` come nome.

    Ai fini di questa esercitazione, mantenere le impostazioni predefinite per tutti gli altri valori.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Screenshot che mostra come creare un'app per la logica.":::

6. Selezionare **Rivedi e crea**. Rivedere le impostazioni e selezionare **Crea** per inviare la distribuzione. Al termine della distribuzione, selezionare **Vai alla risorsa**. Si aprirà **Progettazione app per la logica**.

7. Selezionare un tipo di trigger. Scorrere verso il basso fino alla sezione **Inizia con un trigger comune**. Selezionare **Alla ricezione di una richiesta HTTP**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Screenshot che mostra come creare un'app per la logica.":::

8. Nell'angolo in alto a destra di Progettazione app per la logica selezionare **Salva**. Viene automaticamente generato l'**URL POST HTTP**. Salvare l'URL. Sarà necessario nella sezione successiva per creare un endpoint di evento.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Screenshot che mostra come creare un'app per la logica.":::

9. Selezionare **+ Nuovo passaggio**. A questo punto scegliere un'azione. Digitare `outlook.com email` nella casella di ricerca. Nell'elenco **Azioni** scorrere verso il basso e selezionare **Invia un messaggio di posta elettronica (v2)** .
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Screenshot che mostra come creare un'app per la logica.":::

10. Accedere all'account Outlook. Assicurarsi di selezionare **Sì** per consentire all'app per la logica di accedere all'account. Compilare i campi per l'invio di un messaggio di posta elettronica.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Screenshot che mostra come creare un'app per la logica.":::

    >[!TIP]
    > È possibile recuperare i dati della risposta GeoJSON, ad esempio `geometryId` o `deviceId`, nelle notifiche tramite posta elettronica. È possibile configurare App per la logica per leggere i dati inviati da Griglia di eventi. Per informazioni su come configurare App per la logica per l'utilizzo e il passaggio dei dati degli eventi nelle notifiche tramite posta elettronica, vedere [Esercitazione: Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando Griglia di eventi e App per la logica](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps).

11. Nell'angolo in alto a sinistra di Progettazione app per la logica selezionare **Salva**.

Per creare una seconda app per la logica che invii notifiche al direttore dei lavori quando un macchinario esce ed entra nel cantiere, ripetere i passaggi 3-11. Assegnare all'app per la logica il nome `Equipment-Exit`.

## <a name="create-azure-maps-events-subscriptions"></a>Creare le sottoscrizioni di eventi di Mappe di Azure

Mappe di Azure supporta [tre tipi di eventi](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). In questo scenario è necessario creare due sottoscrizioni di eventi diverse, una per gli eventi di entrata nel recinto virtuale e l'altra per quelli di uscita.

I passaggi seguenti illustrano come creare una sottoscrizione di eventi di entrata nel recinto virtuale. È possibile creare la sottoscrizione per gli eventi di uscita dal recinto virtuale ripetendo i passaggi allo stesso modo.

1. Passare all'account di Mappe di Azure. Nel dashboard selezionare **Sottoscrizioni**. Selezionare il nome della sottoscrizione e scegliere **Eventi** dal menu Impostazioni.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Screenshot che mostra come creare un'app per la logica.":::

2. Per creare una sottoscrizione per gli eventi, selezionare **+ Sottoscrizione di eventi** nella pagina Eventi.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Screenshot che mostra come creare un'app per la logica.":::

3. Nella pagina **Crea sottoscrizione di eventi** immettere i valori seguenti:
    * In **Nome** specificare un nome per la sottoscrizione di eventi.
    * In **Schema evento** selezionare *Schema griglia di eventi*.
    * Il valore di **Nome dell'argomento del sistema** per questa sottoscrizione di eventi, che in questo caso è `Contoso-Construction`.
    * Per **Filtra per tipi di evento**, scegliere `Geofence Entered` come tipo di evento.
    * Per **Tipo di endpoint**, scegliere `Web Hook`.
    * Per **Endpoint**, copiare l'URL POST HTTP per l'endpoint di entrata dell'app per la logica creato nella sezione precedente. Se si è dimenticato di salvarlo, basta tornare in Progettazione app per la logica e copiarlo dal passaggio relativo al trigger HTTP.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Screenshot che mostra come creare un'app per la logica.":::

4. Selezionare **Crea**.

Ripetere i passaggi da 1 a 4 per l'endpoint di uscita dell'app per la logica creato nella sezione precedente. Al passaggio 3 assicurarsi di scegliere `Geofence Exited` come tipo di evento.

## <a name="use-spatial-geofence-get-api"></a>Usare l'API spaziale Get Geofence

Usare l'[API Get Geofence del servizio spaziale](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) per inviare notifiche tramite posta elettronica al direttore dei lavori ogni volta che un macchinario entra o esce dal recinto virtuale.

Ogni macchinario è associato a un valore `deviceId`. In questa esercitazione verrà monitorato un singolo macchinario il cui ID univoco è `device_1`.

Il diagramma seguente mostra le cinque posizioni del macchinario nel tempo, a partire dalla posizione *Start*, che si trova all'esterno dei recinti virtuali. Ai fini di questa esercitazione, la posizione *Start* non è definita, dato che non verranno eseguite query sul dispositivo in tale posizione.

Quando si esegue una query sull'[API Get Geofence del sevizio spaziale](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) con una posizione del macchinario che indica l'entrata o l'uscita iniziale dal recinto virtuale, Griglia di eventi chiama l'endpoint appropriato dell'app per la logica per inviare una notifica tramite posta elettronica al direttore dei lavori.

Ognuna delle sezioni seguenti crea richieste API Geofencing usando le cinque diverse coordinate di posizione del macchinario.

![Diagramma della mappa di recinti virtuali in Mappe di Azure](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Posizione 1 del macchinario (47.638237,-122.132483)

1. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Specificare *Location 1* (Posizione 1). Selezionare la raccolta creata nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data) e selezionare **Salva**.

2. Selezionare il metodo HTTP **GET**  nella scheda del generatore e immettere l'URL seguente. Sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria e `{udid}` con il valore `udid` salvato nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Selezionare **Send** (Invia). Nella finestra della risposta verrà visualizzato il codice GeoJSON seguente.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

Nella risposta GeoJSON riportata sopra la distanza negativa dal recinto virtuale del cantiere principale indica che il macchinario si trova all'interno del recinto virtuale. La distanza positiva dal recinto virtuale del cantiere secondario indica che il macchinario si trova al di fuori del recinto virtuale del cantiere secondario. Poiché questa è la prima volta che il dispositivo si trova all'interno del recinto virtuale del cantiere principale, il parametro `isEventPublished` è impostato su `true`. Il direttore dei lavori riceve una notifica tramite posta elettronica che segnala che il macchinario è entrato nel recinto virtuale.

### <a name="location-2-4763800-122132531"></a>Posizione 2 (47.63800,-122.132531)

1. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Specificare *Location 2* (Posizione 2). Selezionare la raccolta creata nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data) e selezionare **Salva**.

2. Selezionare il metodo HTTP **GET**  nella scheda del generatore e immettere l'URL seguente. Sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria e `{udid}` con il valore `udid` salvato nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Selezionare **Send** (Invia). Nella finestra della risposta verrà visualizzato il codice GeoJSON seguente:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

Nella risposta GeoJSON riportata sopra il macchinario è rimasto nel recinto virtuale del cantiere principale e non è entrato in quello del cantiere secondario. Di conseguenza, il parametro `isEventPublished` è impostato su `false` e il direttore dei lavori non riceverà alcuna notifica tramite posta elettronica.

### <a name="location-3-4763810783315048-12213336020708084"></a>Posizione 3 (47.63810783315048,-122.13336020708084)

1. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Specificare *Location 3* (Posizione 3). Selezionare la raccolta creata nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data) e selezionare **Salva**.

2. Selezionare il metodo HTTP **GET**  nella scheda del generatore e immettere l'URL seguente. Sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria e `{udid}` con il valore `udid` salvato nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Selezionare **Send** (Invia). Nella finestra della risposta verrà visualizzato il codice GeoJSON seguente:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

Nella risposta GeoJSON riportata sopra il macchinario è rimasto nel recinto virtuale del cantiere principale, ma è entrato in quello del cantiere secondario. Di conseguenza, il parametro `isEventPublished` è impostato su `true`. Il direttore dei lavori riceve una notifica tramite posta elettronica che segnala che il macchinario è entrato in un recinto virtuale.

>[!NOTE]
>Se il macchinario si fosse spostato nel cantiere secondario dopo l'orario lavorativo, non sarebbe stato pubblicato alcun evento e il direttore dei lavori non avrebbe ricevuto alcuna notifica.  

### <a name="location-4-47637988-1221338344"></a>Posizione 4 (47.637988,-122.1338344)

1. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Specificare *Location 4* (Posizione 4). Selezionare la raccolta creata nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data) e selezionare **Salva**.

2. Selezionare il metodo HTTP **GET**  nella scheda del generatore e immettere l'URL seguente. Sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria e `{udid}` con il valore `udid` salvato nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Selezionare **Send** (Invia). Nella finestra della risposta verrà visualizzato il codice GeoJSON seguente:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

Nella risposta GeoJSON riportata sopra il macchinario è rimasto nel recinto virtuale del cantiere principale, ma è uscito da quello del cantiere secondario. Si noti tuttavia che il valore di `userTime` è successivo al valore di `expiredTime` definito nei dati del recinto virtuale. Di conseguenza, il parametro `isEventPublished` è impostato su `false` e il direttore dei lavori non riceverà una notifica tramite posta elettronica.

### <a name="location-547637988-1221338344"></a>Posizione 5 (47.637988,-122.1338344)

1. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Specificare *Location 5* (Posizione 5). Selezionare la raccolta creata nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data) e selezionare **Salva**.

2. Selezionare il metodo HTTP **GET**  nella scheda del generatore e immettere l'URL seguente. Sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria e `{udid}` con il valore `udid` salvato nella sezione [Caricare i dati GeoJSON di geofencing](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Selezionare **Send** (Invia). Nella finestra della risposta verrà visualizzato il codice GeoJSON seguente:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

Nella risposta GeoJSON riportata sopra il macchinario è uscito dal recinto virtuale del cantiere principale. Di conseguenza, il parametro `isEventPublished` è impostato su `true` e il direttore dei lavori riceverà una notifica tramite posta elettronica che segnala che il macchinario è uscito da un recinto virtuale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire tipi di contenuto in App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [Inviare notifiche di posta elettronica usando Griglia di eventi e App per la logica](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [Gestori di eventi supportati in Griglia di eventi](https://docs.microsoft.com/azure/event-grid/event-handlers)
