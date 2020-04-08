---
title: Aggiunta di un layer linea a una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come aggiungere un livello linea a una mappa usando Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c473be25907eb3a761fbccd598bb9b732e5be5b9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802349"
---
# <a name="add-a-line-layer-to-the-map"></a>Aggiungere un livello linea alla mappa

Un layer linea può `LineString` essere `MultiLineString` utilizzato per eseguire il rendering e le feature come percorsi o percorsi sulla mappa. Un livello linea può essere utilizzato `Polygon` anche `MultiPolygon` per eseguire il rendering del contorno e delle feature. Un'origine dati è connessa a un livello linea per fornire i dati di cui eseguire il rendering. 

> [!TIP]
> Per impostazione predefinita, i livelli di linea eseguiranno il rendering delle coordinate dei poligoni e delle linee in un'origine dati. Per limitare il layer in modo che `filter` eseda `['==', ['geometry-type'], 'LineString']` solo `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` le feature LineString, impostare la proprietà del layer su o se si desidera includere anche le funzionalità MultiLineString.

Nel codice seguente viene illustrato come creare una riga. Aggiungete la linea a un'origine dati, quindi eseguitela con un livello linea utilizzando la classe [LineLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Di seguito è riportato l'esempio di codice in esecuzione completo della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere una linea a una mappa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Aggiungere una linea a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

È possibile applicare uno stile ai livelli linea utilizzando [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) e Utilizzare espressioni di [stile basate sui dati.](data-driven-style-expressions-web-sdk.md)

## <a name="add-symbols-along-a-line"></a>Aggiungere simboli lungo una linea

In questo esempio viene illustrato come aggiungere icone a freccia lungo una linea sulla mappa. Quando si utilizza un layer di simboli, impostare l'opzione "posizionamento" su "linea". Questa opzione eseguirà il rendering dei simboli lungo la linea e ruoterà le icone (0 gradi e destra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostra freccia lungo la linea" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/drBJwX/'>freccia Mostra</a> penna lungo<a href='https://codepen.io/azuremaps'>@azuremaps</a>la linea di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Il Web SDK di Azure Maps offre diversi modelli di immagine personalizzabili che è possibile usare con il layer di simboli. Per ulteriori informazioni, vedere il documento Come utilizzare i modelli di [immagine.](how-to-use-image-templates-web-sdk.md)

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Aggiungere una sfumatura di traccia a una linea

Potete applicare un singolo colore di traccia a una linea. È inoltre possibile riempire una linea con una sfumatura di colori per mostrare la transizione da un segmento di linea al segmento di linea successivo. Ad esempio, i gradienti di linea possono essere utilizzati per rappresentare le variazioni nel tempo e nella distanza o temperature diverse su una linea di oggetti collegata. Per applicare questa funzionalità a una linea, l'origine dati deve avere l'opzione `lineMetrics` impostata `strokeColor` su true e quindi un'espressione di sfumatura di colore può essere passata all'opzione della linea. L'espressione con sfumatura `['line-progress']` del tratto deve fare riferimento all'espressione dati che espone le metriche della linea calcolata all'espressione.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linea con sfumatura tratto" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la linea della penna con<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>gradiente tratto</a> di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Personalizzare un livello linea

Il livello Linea ha diverse opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello linea' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opzioni del livello linea</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Livello linea](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions (Opzioni LineLayer)](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

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
> [Aggiungere un livello per i poligoni](map-add-shape.md)
