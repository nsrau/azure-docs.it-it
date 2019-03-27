---
title: Aggiungere un livello simbolo in Mappe di Azure | Microsoft Docs
description: Come aggiungere simboli alla mappa JavaScript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: a4d1a54e94b3228c64352bf08cd8cc69820a5e2d
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500050"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Aggiungere un livello simbolo a una mappa

Questo articolo illustra come eseguire il rendering di un punto dati da un'origine dati come livello simbolo in una mappa. Il rendering dei livelli simbolo viene eseguito tramite WebGL e tali livelli supportano un numero significativamente maggiore di punti dati rispetto ai marcatori HTML, ma non supportano elementi HTML e CSS tradizionali per lo stile.  

> [!TIP]
> Per impostazione predefinita, i livelli simbolo eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo da eseguire il rendering solo delle funzionalità della geometria dei punti, impostare la proprietà `filter` del livello su `['==', '$type', 'Point']`

## <a name="add-a-symbol-layer"></a>Aggiungere un livello per i simboli

<iframe height='500' scrolling='no' title='Modifica della posizione della puntina' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice in alto costruisce un oggetto Map. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Una [feature] contenente una geometria [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) viene sottoposta a wrapping nella classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest), per semplificare l'aggiornamento, e successivamente viene creata e aggiunta all'origine dati.

Il terzo blocco di codice crea un [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) e aggiorna le coordinate del punto al clic del mouse usando il metodo [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) della classe Shape.

Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa.  L'origine dati, il listener dell'evento click e il livello simbolo vengono creati e aggiunti alla mappa all'interno della funzione [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che il punto venga visualizzato dopo il caricamento completo della mappa.

> [!TIP]
> Per impostazione predefinita, per le prestazioni, i livelli di simbolo ottimizzare il rendering dei simboli, nascondendo i simboli che si sovrappongono. Come è ingrandire i simboli nascosti diventano visibili. Per disabilitare questa funzionalità ed eseguire il rendering di tutti i simboli in qualsiasi momento, impostare il `allowOverlap` proprietà del `iconOptions` opzioni per `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Aggiungere un'icona personalizzata a un livello simbolo

Il rendering dei livelli simbolo viene eseguito tramite WebGL. Di conseguenza tutte le risorse, ad esempio le immagini icona, devono essere caricate nel contesto di WebGL. Questo esempio illustra come aggiungere un'icona simbolo personalizzata alle risorse della mappa e quindi usarla per eseguire il rendering di un punto dati con un simbolo personalizzato sulla mappa. La proprietà `textField` del livello simbolo richiede che venga specificata un'espressione. In questo caso, si vuole eseguire il rendering le proprietà di temperatura della funzionalità punto uguale al valore di testo. A tale scopo, usare questa espressione: `['get', 'temperature']`. 

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
> [Aggiungere un popup](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Aggiungere una forma](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](./map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere marcatori HTML](./map-add-bubble-layer.md)
