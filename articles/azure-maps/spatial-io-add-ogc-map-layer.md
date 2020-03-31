---
title: Aggiunta di un layer mappa OGC (Open Geospatial Consortium) Mappe di Microsoft Azure
description: Informazioni su come sovrapporre un layer mappa OGC sulla mappa e come utilizzare le diverse opzioni della classe OgcMapLayer.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334292"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Aggiungere un layer mappa dall'Open Geospatial Consortium (OGC)

La `atlas.layer.OgcMapLayer` classe può sovrapporre immagini di Web Map Services (WMS) e immagini WMTS (Web Map Tile Services) sulla mappa. WMS è un protocollo standard sviluppato da OGC per servire immagini di mappe georeferenziate su Internet. La georeferenziazione delle immagini è i processi di associazione di un'immagine a una posizione geografica. WMTS è anche un protocollo standard sviluppato da OGC. È progettato per la gestione di riquadri mappa pre-rendering e georeferenziati.

Nelle sezioni seguenti vengono descritte le funzionalità `OgcMapLayer` del servizio mappe Web supportate dalla classe.

**Servizio mappe Web (WMS)**

- Versioni supportate: `1.0.0`, `1.1.0`, `1.1.1`, e`1.3.0`
- Il servizio deve `EPSG:3857` supportare il sistema di proiezione o gestire le riproiezioni.
- GetFeatureInfo richiede che `EPSG:4326` il servizio supporti o gestisca le riproiezioni. 
- Operazioni supportate:

    | | |
    | :-- | :-- |
    | GetCapabilities | Recupera i metadati relativi al servizio con le funzionalità supportate |
    | GetMappa | Recupera un'immagine della mappa per un'area specificata |
    | GetFeatureInfo | Recupera `feature_info`, che contiene i dati sottostanti sulla funzionalità |

**Servizio riquadro mappa Web (WMTS)**

- Versioni supportate:`1.0.0`
- I riquadri devono `TileWidth == TileHeight`essere quadrati, in modo tale che .
- CRS supportato: `EPSG:3857` o`GoogleMapsCompatible` 
- L'identificatore TileMatrix deve essere un valore intero che corrisponde a un livello di zoom sulla mappa. In una mappa di azure, il `"0"` `"22"`livello di zoom è un valore compreso tra e . Quindi, `"0"` è supportato, ma `"00"` non è supportato.
- Operazioni supportate:

    | | |
    | :-- | :-- |
    | GetCapabilities | Recupera le operazioni e le funzionalità supportate |
    | GetTile | Recupera le immagini per un riquadro specifico |

## <a name="overlay-an-ogc-map-layer"></a>Sovrapporre un livello mappa OGC

L'URL `url` può essere l'URL di base per il servizio o un URL completo con la query per ottenere le funzionalità del servizio. A seconda dei dettagli forniti, il client WFS può provare diversi formati URL standard per determinare come accedere inizialmente al servizio.

Il codice seguente mostra come sovrapporre un livello mappa OGC sulla mappa.

<br/>

<iframe height='700' scrolling='no' title='Esempio di livello mappa OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'esempio del <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>livello Pen OGC Map</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opzioni del livello mappa OGC

Nell'esempio seguente vengono illustrate le diverse opzioni del livello mappa OGC. È possibile fare clic sul pulsante della penna del codice nell'angolo in alto a destra per modificare la penna del codice.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello mappa OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere le opzioni del livello mappa<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC</a> di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Esplora servizio mappa Web OGC

Lo strumento seguente si sovrappone alle immagini da Web Map Services (WMS) e Web Map Tile Services (WMTS) come layer. È possibile selezionare i layer del servizio da eseguire il rendering sulla mappa. È inoltre possibile visualizzare le legende associate per questi layer.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Esplora servizio mappa Web OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web Map Service</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>explorer di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

È inoltre possibile specificare le impostazioni della mappa per utilizzare un servizio proxy. Il servizio proxy consente di caricare le risorse ospitate in domini in cui CORS non è abilitato.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [OgcMapLayer (informazioni in lingua stati inlinguata in](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [Opzioni OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Vedere gli articoli seguenti, che contengono esempi di codice che è possibile aggiungere alle mappe:See the following articles, which contain code samples you could add to your maps:

> [!div class="nextstepaction"]
> [Connettersi a un servizio WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Sfruttare le operazioni di base](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli sul formato dati supportati](spatial-io-supported-data-format-details.md)
