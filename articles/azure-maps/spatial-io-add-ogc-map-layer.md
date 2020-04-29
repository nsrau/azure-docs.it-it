---
title: Aggiungere un livello mappa Open Geospatial Consortium (OGC) | Mappe Microsoft Azure
description: Informazioni su come sovrapporre un livello mappa OGC sulla mappa e come usare le diverse opzioni nella classe OgcMapLayer.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334292"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Aggiungere un livello mappa dal Open Geospatial Consortium (OGC)

La `atlas.layer.OgcMapLayer` classe è in grado di sovrapporre immagini WMS (Web Map Services) e immagini WMTS (Web Mappa Tile Services) sulla mappa. WMS è un protocollo standard sviluppato da OGC per il servizio di immagini mappa georeferenziate su Internet. La georeferenziazione dell'immagine è il processo di associazione di un'immagine a una posizione geografica. WMTS è anche un protocollo standard sviluppato da OGC. È progettato per servire tessere mappa di cui è stato eseguito il rendering.

Le sezioni seguenti descrivono le funzionalità del servizio della mappa Web supportate dalla `OgcMapLayer` classe.

**Servizio mappa Web (WMS)**

- Versioni supportate: `1.0.0`, `1.1.0` `1.1.1`, e`1.3.0`
- Il servizio deve supportare il `EPSG:3857` sistema di proiezione o gestire le proiezioni.
- GetFeatureInfo richiede il supporto `EPSG:4326` o la gestione delle proiezioni da parte del servizio. 
- Operazioni supportate:

    | | |
    | :-- | :-- |
    | GetCapabilities | Recupera i metadati relativi al servizio con le funzionalità supportate |
    | GetMap | Recupera un'immagine mappa per un'area specificata |
    | GetFeatureInfo | Recupera `feature_info`, che contiene i dati sottostanti relativi alla funzionalità |

**Servizio tessera della mappa Web (WMTS)**

- Versioni supportate:`1.0.0`
- I riquadri devono essere quadrati, `TileWidth == TileHeight`ad esempio.
- CRS supportato: `EPSG:3857` o`GoogleMapsCompatible` 
- L'identificatore TileMatrix deve essere un valore intero corrispondente a un livello di zoom sulla mappa. In una mappa di Azure, il livello di zoom è un `"0"` valore `"22"`compreso tra e. Pertanto, `"0"` è supportato, ma `"00"` non è supportato.
- Operazioni supportate:

    | | |
    | :-- | :-- |
    | GetCapabilities | Recupera le operazioni e le funzionalità supportate |
    | GetTile | Recupera le immagini per un riquadro particolare |

## <a name="overlay-an-ogc-map-layer"></a>Sovrapposizione di un livello mappa OGC

`url` Può essere l'URL di base per il servizio o un URL completo con la query per ottenere le funzionalità del servizio. A seconda dei dettagli forniti, il client WFS può provare diversi formati di URL standard per determinare come accedere inizialmente al servizio.

Nel codice seguente viene illustrato come sovrapporre un livello mappa OGC sulla mappa.

<br/>

<iframe height='700' scrolling='no' title='Esempio di livello mappa OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l' <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>esempio di livello mappa</a> di Pen OGC di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opzioni del livello mappa OGC

Nell'esempio seguente vengono illustrate le diverse opzioni del livello mappa OGC. È possibile fare clic sul pulsante della penna del codice nell'angolo superiore destro per modificare la penna del codice.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello mappa OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Per informazioni sulle <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>Opzioni del livello mappa</a> di Pen OGC,<a href='https://codepen.io/azuremaps'>@azuremaps</a>vedere mappe di Azure () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Esplora servizi di mappe Web OGC

Lo strumento seguente sovrappone le immagini dei servizi Web Map Services (WMS) e della tessera della mappa Web (WMTS) come livelli. È possibile selezionare i livelli del servizio di cui viene eseguito il rendering sulla mappa. È anche possibile visualizzare le legende associate per questi livelli.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Esplora servizi di mappe Web OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Per informazioni su <a href='https://codepen.io'>CodePen</a>, vedere la pagina relativa a <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>Esplora servizi mappa Web</a> di Pen OGC di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>).
</iframe>

È anche possibile specificare le impostazioni della mappa per usare un servizio proxy. Il servizio proxy consente di caricare le risorse ospitate in domini per i quali non è abilitato CORS.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Vedere gli articoli seguenti, che contengono esempi di codice che è possibile aggiungere alle mappe:

> [!div class="nextstepaction"]
> [Connettersi a un servizio WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Sfruttare le operazioni principali](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli del formato dati supportati](spatial-io-supported-data-format-details.md)
