---
title: Usare il modulo Piante di interni di Mappe di Azure
description: Informazioni su come usare il modulo Piante di interni di Mappe di Microsoft Azure per eseguire il rendering delle mappe incorporando le librerie JavaScript del modulo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: d852d17bdf11ea45f833e3d59cacb435166827fe
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895461"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Usare il modulo Piante di interni di Mappe di Azure

L'SDK Web di Mappe di Azure include il modulo *Interni di Mappe di Azure* . Il modulo *Interni di Mappe di Azure* consente di eseguire il rendering delle piante di interni create in Creator di Mappe di Azure.

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un account Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Creare una risorsa Creator](how-to-manage-creator.md)
3. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione.
4. Ottenere un oggetto `tilesetId` e `statesetId` completando l'[esercitazione per la creazione di piante di interni](tutorial-creator-indoor-maps.md).
 È necessario usare questi identificatori per eseguire il rendering delle piante di interni con il modulo Piante di interni di Mappe di Azure.

## <a name="embed-the-indoor-maps-module"></a>Incorporare il modulo Piante di interni

È possibile installare e incorporare il modulo *Interni di Mappe di Azure* in una delle due modalità riportate di seguito.

Per usare la versione della Rete di distribuzione dei contenuti di Azure ospitata a livello globale del modulo *Interni di Mappe di Azure* , vedere i seguenti riferimenti al foglio di stile e a JavaScript nell'elemento `<head>` del file HTML:

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 In alternativa, è possibile scaricare il modulo *Interni di Mappe di Azure* . Il modulo *Interni di Mappe di Azure* contiene una libreria client per accedere ai servizi di Mappe di Azure. Attenersi alla procedura seguente per installare e caricare il modulo *Interni* nell'applicazione Web.  
  
  1. Installare il [pacchetto Azure-Maps-indoor](https://www.npmjs.com/package/azure-maps-indoor).
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. Fare riferimento al foglio di stile e a JavaScript del modulo *Interni di Mappe di Azure* nell'elemento `<head>` del file HTML:

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>Creare un'istanza dell'oggetto Map

Per prima cosa, creare un *oggetto Map* . L' *oggetto Map* verrà usato nel passaggio successivo per creare un'istanza dell'oggetto *Indoor Manager* .  Il codice seguente illustra come creare un'istanza dell' *oggetto Map* :

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13315, 47.63637],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Creare un'istanza di Indoor Manager

Per caricare il set di tessere per gli interni e lo stile della mappa delle tessere, è necessario creare un'istanza di *Indoor Manager* . Creare un'istanza di *Indoor Manager* fornendo l' *oggetto Map* e l'oggetto `tilesetId` corrispondente. Se si vuole supportare l'[applicazioni di stili dinamici alla mappa](indoor-map-dynamic-styling.md), è necessario passare l'oggetto `statesetId`. Il nome della variabile `statesetId` fa distinzione tra maiuscole e minuscole. Il codice dovrebbe essere simile al codice JavaScript riportato di seguito.

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

Per abilitare il polling dei dati di stato forniti, è necessario fornire `statesetId` e chiamare `indoorManager.setDynamicStyling(true)`. Il polling dei dati di stato consentono di aggiornare dinamicamente lo stato delle proprietà o degli *stati* dinamici. Ad esempio, una funzionalità come stanza può avere una proprietà dinamica ( *stato* ) denominata `occupancy`. L'applicazione potrebbe voler eseguire il polling di qualsiasi modifica allo *stato* per riflettere la modifica all'interno della mappa visiva. Il codice seguente illustra come abilitare il polling dello stato:

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>Controllo di selezione del livello di interni

 Il controllo di *selezione del livello di interni* consente di modificare il livello della mappa di cui è stato eseguito il rendering. Facoltativamente, è possibile inizializzare il controllo di *selezione del livello di interni* tramite *Indoor Manager* . Di seguito è riportato il codice per inizializzare il controllo di selezione del livello:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Eventi di interni

 Il modulo *Interni di Mappe di Azure* supporta gli eventi dell' *oggetto Map* . I listener degli eventi dell' *oggetto Map* vengono richiamati quando viene modificato un livello o una struttura. Se si vuole eseguire il codice dopo la modifica di un livello o una struttura, inserire il codice all'interno del listener di eventi. Il codice riportato di seguito mostra come è possibile aggiungere listener di eventi all' *oggetto Map* .

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

La variabile `eventData` contiene le informazioni sul livello o sulla struttura che ha richiamato rispettivamente l'evento `levelchanged` o `facilitychanged`. Quando viene modificato un livello, l'oggetto `eventData` conterrà `facilityId`, l'oggetto `levelNumber` nuovo e altri metadati. Quando viene modificata una struttura, l'oggetto `eventData` conterrà l'oggetto `facilityId` nuovo, l'oggetto `levelNumber` nuovo e altri metadati.

## <a name="example-use-the-indoor-maps-module"></a>Esempio: Usare il modulo Piante di interni

Questo esempio illustra come usare il modulo *Interni di Mappe di Azure* nell'applicazione Web. Sebbene l'ambito dell'esempio sia limitato, illustra le nozioni di base relative a ciò che è necessario per iniziare a usare il modulo *Interni di Mappe di Azure* . Il codice HTML completo è riportato alla fine di questa procedura.

1. Usare l' [opzione](#embed-the-indoor-maps-module) Rete per la distribuzione di contenuti di Azure per installare il modulo *Interni di Mappe di Azure* .

2. Creare un nuovo file HTML

3. Nell'intestazione HTML fare riferimento agli stili del foglio di stile e a JavaScript del modulo *Interni di Mappe di Azure* .

4. Inizializzare un *oggetto Map* . L' *oggetto Map* supporta le opzioni seguenti:
    - `Subscription key` è la chiave di sottoscrizione primaria di Mappe di Azure.
    - `center` definisce una latitudine e una longitudine per la posizione del centro della pianta di interni. Fornire un valore per `center` se non si vuole fornire un valore per `bounds`. Il formato deve essere visualizzato come `center`: [-122.13315, 47.63637].
    - `bounds` è la forma rettangolare più piccola che racchiude i dati della mappa del set di tessere. Impostare un valore per `bounds` se non si vuole impostare un valore per `center`. È possibile trovare i limiti della mappa chiamando l'[API dell'elenco di set di tessere](/rest/api/maps/tileset/listpreview). L'API dell'elenco di set di tessere restituisce `bbox`, che è possibile analizzare e assegnare a `bounds`. Il formato deve apparire come `bounds` : [# West, # South, # East, # North].
    - `style` consente di impostare il colore dello sfondo. Per visualizzare uno sfondo bianco, definire `style` come "blank".
    - `zoom` consente di specificare i livelli di zoom minimo e massimo per la mappa.

5. Successivamente, creare il modulo *Indoor Manager* . Assegnare l'oggetto `tilesetId` del modulo *Interni di Mappe di Azure* e, facoltativamente, aggiungere l'oggetto `statesetId`.

6. Creare un'istanza del controllo di *selezione del livello di interni* .

7. Aggiungere i listener di eventi dell' *oggetto Map* .  

Il file dovrebbe avere ora un aspetto simile al codice HTML riportato di seguito.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl, //level picker
          tilesetId,
          statesetId, //optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Per visualizzare la pianta di interni, caricarla in un Web browser. Dovrebbe essere simile all'immagine seguente. Se si fa clic sulla funzionalità scale, la *selezione del livello* verrà visualizzata nell'angolo superiore destro.

  ![immagine della pianta di interni](media/how-to-use-indoor-module/indoor-map-graphic.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle API correlate al modulo *Interni di Mappe di Azure* , vedere:

> [!div class="nextstepaction"]
> [Requisiti del pacchetto di disegno](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Creator per piante di interni](creator-indoor-maps.md)

Altre informazioni su come aggiungere altri dati alla mappa:

> [!div class="nextstepaction"]
> [Stili dinamici per le piante di interni](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Esempi di codice](/samples/browse/?products=azure-maps)