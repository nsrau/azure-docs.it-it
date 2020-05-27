---
title: Implementare stili dinamici per le piante di interni di Creator in Mappe di Azure
description: Informazioni su come implementare stili dinamici per le piante di interni di Creator
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6edc114a2d69dfe8f1e6e5d3c0a2d4af26dbad67
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596600"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>Implementare stili dinamici per le piante di interni

Il [servizio di stato della funzionalità](https://docs.microsoft.com/rest/api/maps/featurestate/featurestate) di Creator per Mappe di Azure consente di applicare stili in base alle proprietà dinamiche delle funzionalità di dati di piante di interni.  È, ad esempio, possibile eseguire il rendering delle sale riunioni con un colore specifico in modo che indichino se sono o meno occupate. Questo articolo illustra come eseguire dinamicamente il rendering delle funzionalità di piante di interni con il [servizio di stato della funzionalità](https://docs.microsoft.com/rest/api/maps/featurestate/featurestate) e il [modulo Web per piante di interni](how-to-use-indoor-module.md).

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un account di Mappe di Azure](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione.
3. [Creare una risorsa Creator](how-to-manage-creator.md)
4. Scaricare il [pacchetto di disegno di esempio](https://github.com/Azure-Samples/am-creator-indoor-data-examples).
5. [Creare una pianta di interni](tutorial-creator-indoor-maps.md) per ottenere i valori di `tilesetId` e `statesetId`.
6. Creare un'applicazione Web seguendo la procedura descritta in [Usare il modulo di piante di interni](how-to-use-indoor-module.md).

Questa esercitazione usa l'applicazione [Postman](https://www.postman.com/), ma è possibile scegliere un ambiente di sviluppo API diverso.

## <a name="implement-dynamic-styling"></a>Implementare stili dinamici

Dopo aver completato i prerequisiti, dovrebbe essere disponibile una semplice applicazione Web configurata con la chiave della sottoscrizione, `tilesetId`e `statesetId`.

### <a name="select-features"></a>Selezione delle funzionalità

Per implementare lo stile dinamico, una funzionalità, ad esempio una sala riunioni o una sala conferenze, deve fare riferimento al valore `id` della funzionalità. Il valore `id` della funzionalità verrà usato per aggiornare la proprietà dinamica o lo *stato* di tale funzionalità. Per visualizzare le funzionalità definite in un set di dati, è possibile usare uno dei metodi seguenti:

* API WFS (Web Feature Service). È possibile eseguire query sui set di dati usando l'API WFS. Il servizio WFS è conforme alle funzionalità dell'API Open Geospatial Consortium. L'API WFS è utile per eseguire query sulle funzionalità all'interno di un set di dati. È, ad esempio, possibile usare il servizio WFS per trovare tutte le sale riunioni di medie dimensioni di una determinata struttura e a determinato piano.

* Implementare codice personalizzato che consente a un utente di selezionare funzionalità in una mappa usando l'applicazione Web. In questo articolo verrà usata questa opzione.  

Lo script seguente implementa l'evento clic del mouse. Il codice recupera il valore `id` della funzionalità in base al punto su cui è stato fatto clic. Nell'applicazione è possibile inserire il codice sotto il blocco di codice di Indoor Manager. Eseguire l'applicazione e controllare la console per ottenere il valore `id` della funzionalità del punto su cui è stato fatto clic.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor")

    var result = features.reduce(function (ids, feature) {
        if (feature.layer.id == "indoor_unit_office") {
            console.log(feature);
        }
    }, []);
});
```

Nell'esercitazione [Creare una pianta di interni](tutorial-creator-indoor-maps.md) è stato configurato il valore stateset della funzionalità in modo da accettare gli aggiornamenti di stato relativi ad `occupancy`.

Nella sezione successiva lo *stato* di occupazione dell'ufficio `UNIT26` verrà impostato su `true`, mentre quello dell'ufficio `UNIT27` verrà impostato su `false`.

### <a name="set-occupancy-status"></a>Impostare lo stato di occupazione

 Verrà ora aggiornato lo stato dei due uffici, `UNIT26` e `UNIT27`:

1. Nell'applicazione Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un valore in **Request name** (Nome richiesta) e selezionare una raccolta. Fare clic su **Save** (Salva).

2. Per aggiornare lo stato, usare l'[API degli stati di aggiornamento delle funzionalità](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview). Passare l'ID stateset e `UNIT26` per una delle due unità. Accodare la chiave di sottoscrizione di Mappe di Azure. Ecco l'URL di una richiesta **POST** per l'aggiornamento dello stato:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. In **Headers** (Intestazioni) della richiesta **POST** impostare `Content-Type` su `application/json`. In **BODY** della richiesta **POST** scrivere il codice JSON con gli aggiornamenti delle funzionalità. L'aggiornamento verrà salvato solo se il timestamp pubblicato è successivo a quello usato nelle richieste di aggiornamento dello stato della funzionalità precedente per lo stesso valore `ID` della funzionalità. Passare il valore "occupied" a `keyName` per aggiornarne il valore.

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

4. Ripetere i passaggi 2 e 3 usando `UNIT27` con il codice JSON seguente.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Visualizzare gli stili dinamici in una mappa

L'applicazione Web aperta in precedenza in un browser rispecchia ora lo stato aggiornato delle funzionalità della mappa. `UNIT27`(151) dovrebbe essere visualizzata in verde e `UNIT26`(157) dovrebbe essere visualizzata in rosso.

![Sala disponibile in verde e sala occupata in rosso](./media/indoor-map-dynamic-styling/room-state.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

> [!div class="nextstepaction"]
> [Creator per piante di interni](creator-indoor-maps.md)

Vedere i riferimenti per le API menzionate in questo articolo:

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

