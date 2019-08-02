---
title: Aggiungere un livello simbolo in Mappe di Azure | Microsoft Docs
description: Come aggiungere simboli alla mappa JavaScript
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ba5d5d3aaa6a83dbcc5e5072872bca0fcd22bbf9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638715"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Aggiungere un livello simbolo a una mappa

Questo articolo illustra come eseguire il rendering di un punto dati da un'origine dati come livello simbolo in una mappa. I livelli dei simboli vengono visualizzati con WebGL e supportano set di punti molto più grandi rispetto ai marcatori HTML, ma non supportano gli elementi CSS e HTML tradizionali per lo stile.  

> [!TIP]
> Per impostazione predefinita, i livelli simbolo eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo che esegua il rendering solo delle funzionalità di `filter` geometria dei punti, impostare `['==', ['geometry-type'], 'Point']` la `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` proprietà del livello su o se si desidera includere anche le funzionalità multipoint.

## <a name="add-a-symbol-layer"></a>Aggiungere un livello per i simboli

<iframe height='500' scrolling='no' title='Modifica della posizione della puntina' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice in alto costruisce un oggetto Map. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Una [feature] contenente una geometria [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) viene sottoposta a wrapping nella classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest), per semplificare l'aggiornamento, e successivamente viene creata e aggiunta all'origine dati.

Il terzo blocco di codice crea un [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) e aggiorna le coordinate del punto al clic del mouse usando il metodo [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) della classe Shape.

Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa.  L'origine dati, il listener di eventi click e il livello Symbol vengono creati e aggiunti alla mappa all'interno della `ready` funzione [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il punto venga visualizzato dopo che la mappa è stata caricata e pronta per l'accesso.

> [!TIP]
> Per impostazione predefinita, per le prestazioni i livelli di simboli ottimizzano il rendering dei simboli nascondendo i simboli che si sovrappongono. Quando si esegue lo zoom avanti, i simboli nascosti diventano visibili. Per disabilitare questa funzionalità ed eseguire il rendering di tutti i simboli in qualsiasi `allowOverlap` momento, impostare `iconOptions` la proprietà `true`delle opzioni su.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Aggiungere un'icona personalizzata a un livello simbolo

Il rendering dei livelli simbolo viene eseguito tramite WebGL. Di conseguenza tutte le risorse, ad esempio le immagini icona, devono essere caricate nel contesto di WebGL. In questo esempio viene illustrato come aggiungere un'icona personalizzata alle risorse della mappa e quindi utilizzarla per eseguire il rendering dei dati del punto con un simbolo personalizzato sulla mappa. La proprietà `textField` del livello simbolo richiede che venga specificata un'espressione. In questo caso, si vuole eseguire il rendering della proprietà temperature, ma poiché si tratta di un numero, è necessario convertirlo in una stringa. Inoltre, si desidera aggiungere "° f". Per eseguire questa operazione, è possibile usare un'espressione. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Icona immagine simbolo personalizzata' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> (Icona immagine simbolo personalizzata) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Personalizzare un livello simbolo 

Per il livello simbolo sono disponibili numerose opzioni di stile. Di seguito è disponibile uno strumento per testare queste opzioni.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello simbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> (Opzioni del livello simbolo) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere una forma](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere marcatori HTML](map-add-bubble-layer.md)
