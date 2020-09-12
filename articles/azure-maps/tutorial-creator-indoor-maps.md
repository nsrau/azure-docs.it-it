---
title: Usare Creator per creare piante di interni
description: Usare Creator di Mappe di Azure per creare piante di interni.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9ed6690348816229d369bcff5d92c9703a4b3702
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469916"
---
# <a name="use-creator-to-create-indoor-maps"></a>Usare Creator per creare piante di interni

Questa esercitazione mostra come creare piante di interni. Questa esercitazione descrive come usare l'API per:

> [!div class="checklist"]
> * Caricare il pacchetto di disegno della pianta di interni
> * Convertire il pacchetto di disegno in dati della pianta
> * Creare un set di dati dai dati della pianta
> * Creare un set di tessere a partire dai dati nel set di dati
> * Eseguire una query sull'API Web Feature Service (WFS) di Mappe di Azure per ottenere informazioni sulle funzionalità della pianta
> * Creare un set di stati delle funzionalità usando le funzionalità della pianta e i dati nel set di dati
> * Aggiornare il set di stati della funzionalità

## <a name="prerequisites"></a>Prerequisiti

Per creare piante di interni è necessario:

1. [Creare un account Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione
3. [Creare una risorsa Creator](how-to-manage-creator.md)
4. Scaricare il [pacchetto di disegno di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

Questa esercitazione usa l'applicazione [Postman](https://www.postman.com/), tuttavia è possibile scegliere un ambiente di sviluppo API diverso.

>[!IMPORTANT]
> Gli URL dell'API in questo documento possono essere modificati in base alla posizione della risorsa dell'autore. Per altri dettagli, vedere [accesso ai servizi Creator](how-to-manage-creator.md#access-to-creator-services).

## <a name="upload-a-drawing-package"></a>Caricare un pacchetto di disegno

Usare l'[API Data Upload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) per caricare un pacchetto di disegno nelle risorse di Mappe di Azure.

L'API Data Upload è una transazione a esecuzione prolungata che implementa il modello qui definito. Al termine dell'operazione, verrà usato `udid` per accedere al pacchetto caricato e convertirlo. Seguire questa procedura per ottenere `udid`.

1. Aprire l'app Postman. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Collection** (Raccolta).  Assegnare un nome alla raccolta e selezionare **Create** (Crea).

2. Per creare la richiesta, selezionare nuovamente **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare la raccolta creata nel passaggio precedente e fare clic su **Save** (Salva).

3. Selezionare il metodo HTTP **POST** nella scheda del generatore e immettere l'URL seguente per caricare il pacchetto di disegno nel servizio Mappe di Azure. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Nella scheda **Headers** (Intestazioni) specificare un valore per la chiave `Content-Type`. Il pacchetto di disegno consiste in una cartella compressa, quindi usare il valore `application/octet-stream`. Nella scheda **Body** (Corpo) selezionare **binary** (binario). Fare clic su **Select File** (Seleziona file) e scegliere un pacchetto di disegno.

     ![data-management](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Fare clic sul pulsante blu **Send** (Invia) e attendere l'elaborazione della richiesta. Al completamento della richiesta, passare alla scheda di risposta **Headers** (Intestazioni). Copiare il valore della chiave **Location** (Posizione), ovvero lo `status URL`.

6. Per controllare lo stato della chiamata API, creare una richiesta HTTP **GET** sullo `status URL`. È necessario accodare la chiave di sottoscrizione primaria all'URL per l'autenticazione. La richiesta **Get** dovrebbe essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Quando la richiesta HTTP **GET** viene completata correttamente, restituisce un `resourceLocation`. `resourceLocation` contiene il valore `udid` univoco per il contenuto caricato. Se si vuole, è possibile usare l'URL `resourceLocation` per recuperare i metadati da questa risorsa nel passaggio successivo.

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

9. Quando la richiesta HTTP **GET** viene completata correttamente, il corpo della risposta conterrà il valore `udid` specificato nel `resourceLocation` del passaggio 7, il percorso per accedere al contenuto in futuro e scaricarlo e altri metadati sul contenuto, come data di creazione/aggiornamento, dimensioni e così via. Un esempio di risposta complessiva è:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Convertire un pacchetto di disegno

 Ora che il pacchetto di disegno è stato caricato, si userà `udid` per il pacchetto caricato per convertirlo in dati della pianta. L'API Conversion usa una transazione con esecuzione prolungata, che implementa il modello definito in [questo articolo](creator-long-running-operation.md). Al termine dell'operazione, viene usato `conversionId` per accedere ai dati convertiti. Seguire questa procedura per ottenere `conversionId`.

1. Selezionare **Nuovo**. Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request name** (Nome richiesta) e selezionare una raccolta. Fare clic su **Salva**.

2. Selezionare il metodo HTTP **POST** nella scheda del generatore e immettere l'URL seguente per convertire il pacchetto di disegno caricato in dati della pianta. Usare `udid` per il pacchetto caricato.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > Gli URL dell'API in questo documento possono essere modificati in base alla posizione della risorsa dell'autore. Per altri dettagli, vedere [accesso ai servizi Creator](how-to-manage-creator.md#access-to-creator-services).

3. Fare clic su **Send** (Invia) e attendere l'elaborazione della richiesta. Al completamento della richiesta, passare alla scheda della risposta **Headers** (Intestazioni) e cercare la chiave **Location** (Posizione). Copiare il valore della chiave **Location** (Posizione), che è lo `status URL` per la richiesta di conversione. Che verrà usato nel passaggio successivo.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="Copiare il valore della chiave location":::

4. Selezionare il metodo HTTP **GET** nella scheda del generatore. Accodare la chiave di sottoscrizione primaria di Mappe di Azure allo `status URL`. Eseguire una richiesta **Get** in `status URL` copiata nel passaggio 3. L' `status URL` aspetto dell'URL è simile al seguente:

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    Se il processo di conversione non è ancora stato completato, è possibile che venga visualizzata una risposta JSON simile alla seguente:

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Una volta completata la richiesta, viene visualizzato un messaggio di stato di operazione riuscita nel corpo della risposta.  Copiare `conversionId` dall'URL `resourceLocation` per il pacchetto convertito. `conversionId` viene usato da altre API per accedere ai dati convertiti della pianta.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>L'applicazione Postman non supporta in modo nativo richieste HTTP con esecuzione prolungata. Di conseguenza si può notare un lungo ritardo durante l'esecuzione di una richiesta **GET** nell'URL di stato.  Attendere circa trenta secondi e provare a fare clic nuovamente su **Send** (Invia) fino a quando la risposta non mostra esito positivo o negativo.

Il pacchetto di disegno di esempio deve essere convertito senza errori o avvisi. Tuttavia, se si ricevono errori o avvisi dal pacchetto di disegno, la risposta JSON fornirà un collegamento al [Drawing error visualizer](drawing-error-visualizer.md) (Visualizzatore di errori di disegno). Il Drawing Error visualizer (Visualizzatore di errori di disegno) consente di esaminare i dettagli degli errori e degli avvisi. Per ricevere consigli su come risolvere gli errori e gli avvisi di conversione, vedere [Errori e avvisi di conversione dei disegni](drawing-conversion-error-codes.md).

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Creare un set di dati

Il set di dati è una raccolta di funzionalità della pianta come edifici, livelli e stanze. Per creare un set di dati, usare l'[API Dataset Create](https://docs.microsoft.com/rest/api/maps/dataset/createpreview). L'API Dataset Create accetta il `conversionId` per il pacchetto di disegno convertito e restituisce un `datasetId` del set di dati creato. La procedura seguente mostra come creare un set di dati.

1. Nell'applicazione Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request name** (Nome richiesta) e selezionare una raccolta. Fare clic su **Save** (Salva).

2. Per creare un nuovo set di dati, creare una richiesta **POST** all'[API Dataset Create](https://docs.microsoft.com/rest/api/maps/dataset/createpreview). Prima di inviare la richiesta, accodare sia la chiave di sottoscrizione sia il `conversionId` con il `conversionId` ottenuto durante il processo di conversione nel passaggio 5.  La richiesta deve essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Ottenere lo `statusURL` nella chiave **Location** (Posizione) degli **Header** (Intestazioni) di risposta.

4. Effettuare una richiesta **GET** allo `statusURL` per ottenere il `datasetId`. Accodare la chiave di sottoscrizione primaria di Mappe di Azure per l'autenticazione. La richiesta deve essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Quando la richiesta HTTP **GET** viene completata correttamente, l'intestazione della risposta conterrà il `datasetId` per il set di dati creato. Copiare `datasetId`. Per creare un set di tessere è necessario usare `datasetId`.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Creare un set di tessere

Un set di tessere è un set di tessere vettoriali che esegue il rendering sulla mappa. I set di tessere vengono creati da set di dati esistenti. Tuttavia, un set di tessere è indipendente dal set di dati da cui è originato. Se il set di dati viene eliminato, il set di tessere continuerà a esistere. Per creare un set di tessere, seguire la procedura seguente:

1. Nell'applicazione Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request name** (Nome richiesta) e selezionare una raccolta. Fare clic su **Save** (Salva).

2. Creare una richiesta **POST** nella scheda del generatore. La richiesta URL dovrebbe essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Effettuare una richiesta **GET** allo `statusURL` per il set di tessere. Accodare la chiave di sottoscrizione primaria di Mappe di Azure per l'autenticazione. La richiesta deve essere simile all'URL seguente:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Quando la richiesta HTTP **GET** viene completata correttamente, l'intestazione della risposta conterrà il `tilesetId` per il set di tessere creato. Copiare `tilesetId`.

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Eseguire query sui set di dati con l'API WFS

 È possibile eseguire query sui set di dati usando l'[API WFS](https://docs.microsoft.com/rest/api/maps/wfs). Con l'API WFS è possibile eseguire una query per le raccolte di funzionalità, per una raccolta specifica o per una funzionalità specifica con una funzionalità **ID**. La funzionalità **ID** identifica in modo univoco la funzionalità all'interno del set di dati. Viene usato, ad esempio, per identificare lo stato della funzionalità da aggiornare all'interno di un determinato set di stati.

1. Nell'applicazione Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request name** (Nome richiesta) e selezionare una raccolta. Fare clic su **Save** (Salva).

2. Effettuare una richiesta **GET** per visualizzare un elenco delle raccolte nel set di dati. Sostituire `<dataset-id>` con il `datasetId`. Usare la chiave primaria di Mappe di Azure invece del segnaposto. La richiesta deve essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. Il corpo della risposta verrà fornito in formato GeoJSON e conterrà tutte le raccolte all'interno del set di dati. Per semplicità, l'esempio mostra solo la raccolta `unit`. Per un esempio contenente tutte le raccolte, vedere [API WFS - Descrizioni raccolte](https://docs.microsoft.com/rest/api/maps/wfs/collectiondescriptionpreview). Per altre informazioni sulle raccolte è possibile fare clic su uno degli URL all'interno dell'elemento `link`.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Effettuare una richiesta **GET** per le raccolte di funzionalità di `unit`.  Sostituire `{datasetId}` con il `datasetId`. Usare la chiave primaria di Mappe di Azure invece del segnaposto. Il corpo della risposta conterrà tutte le funzionalità della raccolta `unit`. La richiesta deve essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Copiare la funzionalità `id` per una funzionalità di unità con proprietà di stile che possono essere modificate dinamicamente.  Dato che la temperatura e lo stato di occupazione delle unità possono essere aggiornati dinamicamente, nella sezione successiva verrà usata la funzionalità `id`. Nell'esempio seguente la proprietà `id` è "UNIT26". si fa riferimento alle proprietà di stile di questa funzionalità come stati e viene usata la funzionalità per creare un set di stati.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Creare un set di stati della funzionalità

1. Nell'applicazione Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request name** (Nome richiesta) e selezionare una raccolta. Fare clic su **Save** (Salva).

2. Effettuare una richiesta **POST** all'API [Create Stateset](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview). Usare il `datasetId` del set di dati che contiene lo stato che si vuole modificare. La richiesta deve essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Negli **Headers** (Intestazioni) della richiesta **POST**, impostare `Content-Type` su `application/json`. In **Body** (Corpo), specificare gli stili seguenti per riflettere le modifiche apportate agli *stati* `occupied` e `temperature`. Al termine, fare clic su **Send**(Invia).

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Copiare lo `statesetId` dal corpo dalla risposta.

5. Creare una richiesta **POST** per aggiornare lo stato: Passare lo statesetId e la funzionalità `ID` con la chiave di sottoscrizione di Mappe di Azure. La richiesta deve essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Negli **Headers** (Intestazioni) della richiesta **POST**, impostare `Content-Type` su `application/json`. Nel **BODY** (CORPO) della richiesta **POST** copiare e incollare il codice JSON nell'esempio seguente.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > L'aggiornamento verrà salvato solo se lo stamp relativo all'ora di pubblicazione è successivo al timestamp della richiesta precedente. È possibile passare qualsiasi nome di chiave configurato in precedenza durante la creazione.

7. Dopo un aggiornamento correttamente completato, si riceve un codice di stato HTTP `200 OK`. Se è stato [implementato lo stile dinamico](indoor-map-dynamic-styling.md) per una pianta interna, l'aggiornamento viene visualizzato nella pianta di cui è stato eseguito il rendering al timestamp specificato.

L'[API Feature Get States](https://docs.microsoft.com/rest/api/maps/featurestate/getstatespreview) consente di recuperare lo stato di una funzionalità usando la relativa funzionalità `ID`. È anche possibile eliminare i set di stati e le relative risorse usando l'[API Feature State Delete](https://docs.microsoft.com/rest/api/maps/featurestate/deletestatesetpreview).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Caricare il pacchetto di disegno della pianta di interni
> * Convertire il pacchetto di disegno in dati della pianta
> * Creare un set di dati dai dati della pianta
> * Creare un set di tessere a partire dai dati nel set di dati
> * Eseguire una query sul servizio WFS Mappe di Azure per ottenere informazioni sulle funzionalità della pianta
> * Creare un set di stati delle funzionalità usando le funzionalità della pianta e i dati nel set di dati
> * Aggiornare il set di stati della funzionalità

A questo punto si hanno le competenze necessarie per passare alle guide successive:

> [!div class="nextstepaction"]
> [Usare il modulo di piante di interni](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementare stili dinamici per le piante di interni](indoor-map-dynamic-styling.md)

Informazioni sui diversi servizi di Mappe di Azure descritti in questo articolo:

> [!div class="nextstepaction"]
> [Data Upload](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversione dati](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Set di dati](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Set di tessere](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Set di stati della funzionalità](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [Servizio WFS](creator-indoor-maps.md#web-feature-service-api)
