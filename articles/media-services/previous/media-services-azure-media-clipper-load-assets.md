---
title: Caricare asset in Azure Media Clipper | Microsoft Docs
description: Passaggi per caricare asset in Azure Media Clipper
services: media-services
keywords: clip;clip secondaria;codifica;multimediale
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a479218ff8bd5addf4273b23c06380859e0ea08
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="loading-assets-into-azure-media-clipper"></a>Caricamento di asset in Azure Media Clipper
È possibile caricare asset in Azure Media Clipper con due metodi:
1. Passaggio statico di una libreria di asset
2. Generazione dinamica di un elenco di asset tramite API

## <a name="statically-load-videos-into-clipper"></a>Caricamento statico dei video in Clipper
I video vengono caricati in modo statico in Clipper per consentire agli utenti finali di compilare clip senza selezionare i video dal pannello di selezione degli asset.

In questo caso, si passa un set statico di asset a Clipper. Ogni asset include un ID filtro/asset AMS, un nome e un URL di streaming pubblicato. Se applicabile, è possibile passare un token di autenticazione per la protezione del contenuto o una matrice di URL di anteprima. Se vengono passate, le anteprime vengono inserite nell'interfaccia. Negli scenari in cui la libreria di asset è statica e di piccole dimensioni, è possibile passare il contratto di ogni asset nella libreria.

> [!NOTE]
> Quando si caricano in modo statico in Clipper, gli asset vengono aggiunti **direttamente alla sequenza temporale** e **non viene eseguito il rendering del riquadro degli asset**. Il primo asset viene aggiunto alla sequenza temporale mentre gli asset rimanenti vengono raggruppati in pila sul lato destro della sequenza temporale).

Per caricare una libreria statica di asset, usare il metodo **load** per passare una rappresentazione JSON di ogni asset. L'esempio di codice seguente illustra la rappresentazione JSON per un asset.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> È consigliabile concatenare la chiamata del metodo load() con il metodo ready(handler), come illustrato nell'esempio precedente. L'esempio precedente garantisce che il widget sia pronto prima di caricare gli asset.

> [!NOTE]
> Per funzionare come previsto nella sequenza temporale di Clipper, gli URL di anteprima devono essere distribuiti in modo uniforme nel video (in base alla durata) e in ordine cronologico all'interno della matrice. È possibile usare il frammento JSON seguente del set di impostazioni come riferimento di esempio per generare immagini con il processore Media Encoder Standard:

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Caricamento dinamico dei video in Clipper
I video vengono caricati in modo dinamico in Clipper per consentire agli utenti finali di selezionare i video dal pannello di selezione degli asset.

In alternativa, è possibile caricare asset in modo dinamico tramite un callback. In scenari con asset generati in modo dinamico o una libreria di grandi dimensioni, è consigliabile eseguire il caricamento tramite callback. Per caricare asset in modo dinamico, è necessario implementare la funzione di callback facoltativa onLoadAssets. Questa funzione viene passata a Clipper in fase di inizializzazione. Gli asset risolti dovranno rispettare lo stesso contratto degli asset caricati in modo statico. L'esempio di codice seguente illustra la firma del metodo e l'input e l'output previsti.

> [!NOTE]
> Quando si caricano in modo dinamico gli asset in Clipper, viene eseguito il rendering degli asset nel **pannello di selezione degli asset**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```