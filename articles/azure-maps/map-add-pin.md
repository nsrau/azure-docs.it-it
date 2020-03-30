---
title: Aggiunta di un layer Simbolo a una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come utilizzare il layer Symbol per personalizzare un simbolo e aggiungere simboli su una mappa usando Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209699"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Aggiungere un livello simbolo a una mappa

Connettere un simbolo a un'origine dati e utilizzarlo per eseguire il rendering di un'icona o di un testo in un determinato punto. 

Il rendering dei livelli simbolo viene eseguito tramite WebGL. Utilizzare un layer di simboli per eseguire il rendering di grandi raccolte di punti sulla mappa. Rispetto al marcatore HTML, il livello di simbolo esegue il rendering di un numero elevato di dati punto sulla mappa, con prestazioni migliori. Tuttavia, il livello simbolo non supporta gli elementi CSS e HTML tradizionali per l'applicazione di stili.  

> [!TIP]
> Per impostazione predefinita, i livelli simbolo eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il layer in modo che `filter` esegga `['==', ['geometry-type'], 'Point']` solo `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` feature geometria punto impostare la proprietà del layer su o, se lo si desidera, è possibile includere anche le feature MultiPoint.

Il gestore sprite immagine mappe carica le immagini personalizzate utilizzate dal layer simbolo. Supporta i seguenti formati di immagine:

- JPEG
- PNG
- SVG
- BMP
- GIF (nessuna animazione)

## <a name="add-a-symbol-layer"></a>Aggiungere un livello per i simboli

Prima di poter aggiungere un layer simbolo alla mappa, è necessario eseguire un paio di passaggi. Creare innanzitutto un'origine dati e aggiungerla alla mappa. Creare un livello simbolo. Quindi, passare l'origine dati al livello di simbolo, per recuperare i dati dall'origine dati. Infine, aggiungere dati nell'origine dati, in modo che sia necessario eseguire il rendering. 

Il codice seguente illustra cosa deve essere aggiunto alla mappa dopo il caricamento. In questo esempio viene eseguito il rendering di un singolo punto sulla mappa utilizzando un layer di simboli. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Esistono quattro diversi tipi di dati punto che possono essere aggiunti alla mappa:

- Geometria punto GeoJSON - Questo oggetto contiene solo una coordinata di un punto e nient'altro. La `atlas.data.Point` classe helper può essere utilizzata per creare facilmente questi oggetti.
- Geometria GeoJSON MultiPoint - Questo oggetto contiene le coordinate di più punti e nient'altro. La `atlas.data.MultiPoint` classe helper può essere utilizzata per creare facilmente questi oggetti.
- Feature GeoJSON: questo oggetto è costituito da qualsiasi geometria GeoJSON e da un set di proprietà che contengono metadati associati alla geometria. La `atlas.data.Feature` classe helper può essere utilizzata per creare facilmente questi oggetti.
- `atlas.Shape`classe è simile alla funzionalità GeoJSON. Entrambi sono costituiti da una geometria GeoJSON e da un set di proprietà che contengono metadati associati alla geometria. Se un oggetto GeoJSON viene aggiunto a un'origine dati, può essere facilmente sottoposto a rendering in un livello. Tuttavia, se la proprietà coordinates di tale oggetto GeoJSON viene aggiornata, l'origine dati e la mappa non cambiano. Questo perché non esiste alcun meccanismo nell'oggetto JSON per attivare un aggiornamento. La classe shape fornisce funzioni per l'aggiornamento dei dati in essa contenuti. Quando viene apportata una modifica, l'origine dati e la mappa vengono automaticamente notificate e aggiornate. 

L'esempio di codice seguente crea una geometria `atlas.Shape` GeoJSON Point e la passa alla classe per semplificarne l'aggiornamento. Il centro della mappa viene inizialmente utilizzato per eseguire il rendering di un simbolo. Un evento click viene aggiunto alla mappa in modo che quando viene generato, le coordinate del mouse vengono utilizzate con la funzione shapes. `setCoordinates` Le coordinate del mouse vengono registrate al momento dell'evento click. Quindi, `setCoordinates` aggiorna la posizione del simbolo sulla mappa.

<br/>

<iframe height='500' scrolling='no' title='Modifica della posizione della puntina' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Per impostazione predefinita, i livelli dei simboli ottimizzano il rendering dei simboli nascondendo i simboli che si sovrappongono. Quando si esegue lo zoom avanti, i simboli nascosti diventano visibili. Per disattivare questa funzione ed eseguire il `allowOverlap` rendering `iconOptions` di `true`tutti i simboli in qualsiasi momento, impostate la proprietà delle opzioni su .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Aggiungere un'icona personalizzata a un livello simbolo

Il rendering dei livelli simbolo viene eseguito tramite WebGL. Di conseguenza tutte le risorse, ad esempio le immagini icona, devono essere caricate nel contesto di WebGL. In questo esempio viene illustrato come aggiungere un'icona personalizzata alle risorse della mappa. Questa icona viene quindi utilizzata per eseguire il rendering dei dati punto con un simbolo personalizzato sulla mappa. La proprietà `textField` del livello simbolo richiede che venga specificata un'espressione. In questo caso, vogliamo rendere la proprietà temperature. Poiché la temperatura è un numero, deve essere convertita in una stringa. Inoltre, si desidera aggiungere "F" ad esso. Un'espressione può essere utilizzata per eseguire questa concatenazione; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Icona immagine simbolo personalizzata' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon</a> (Icona immagine simbolo personalizzata) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Il Web SDK di Azure Maps offre diversi modelli di immagine personalizzabili che è possibile usare con il layer di simboli. Per ulteriori informazioni, vedere il documento Come utilizzare i modelli di [immagine.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-symbol-layer"></a>Personalizzare un livello simbolo 

Per il livello simbolo sono disponibili numerose opzioni di stile. Di seguito è disponibile uno strumento per testare queste opzioni.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello simbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options</a> (Opzioni del livello simbolo) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando desiderate eseguire il rendering solo del testo con un `image` livello simbolo, potete `'none'`nascondere l'icona impostando la proprietà delle opzioni dell'icona su .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opzioni di testo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine datiCreate a data source](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello bolla](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere marcatori HTML](map-add-bubble-layer.md)
