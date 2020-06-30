---
title: Aggiungere un livello linea a una mappa | Mappe di Microsoft Azure
description: In questo articolo viene spiegato come aggiungere un livello linea a una mappa usando Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c473be25907eb3a761fbccd598bb9b732e5be5b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802349"
---
# <a name="add-a-line-layer-to-the-map"></a>Aggiungere un livello linea alla mappa

Un livello linea può essere usato per eseguire il rendering delle funzionalità `LineString` e `MultiLineString` come percorsi o route sulla mappa. Un livello linea può essere usato anche per eseguire il rendering della struttura delle funzionalità `Polygon` e `MultiPolygon`. Un'origine dati è connessa a un livello linea per fornire a quest'ultimo i dati di cui eseguire il rendering. 

> [!TIP]
> Per impostazione predefinita, i livelli linea eseguiranno il rendering delle coordinate dei poligoni e delle linee in un'origine dati. Per limitare il livello in modo che esegua solo il rendering delle funzionalità LineString, impostare la proprietà `filter` del livello su `['==', ['geometry-type'], 'LineString']` o `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`, se si vuole includere anche le funzionalità MultiLineString.

Il codice seguente illustra come creare una linea. Aggiungere la linea a un'origine dati e quindi eseguirne il rendering con un livello linea usando la classe [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest).

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

Di seguito è riportato l'esempio di codice completo e funzionante della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere una linea a una mappa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Aggiungere una linea a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

È possibile applicare uno stile ai livelli linea usando [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) e [usare le espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Aggiungere simboli lungo una linea

Questo esempio illustra come aggiungere icone a freccia lungo una linea sulla mappa. Quando si usa un livello simbolo, impostare l'opzione "posizionamento" su "linea". Questa opzione consente di eseguire il rendering dei simboli lungo la linea e di ruotare le icone (0 gradi = destra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Visualizzare la freccia lungo la linea" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show arrow along line</a> (Visualizzare la freccia lungo la linea) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps Web SDK offre diversi modelli di immagine personalizzabili che è possibile usare con il livello simbolo. Per altre informazioni, vedere il documento [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md).

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Aggiungere una sfumatura del tratto a una linea

È possibile applicare un colore a tratto singolo a una linea. È anche possibile riempire una linea con una sfumatura di colori per visualizzare la transizione da un segmento di linea al segmento di linea successivo. Ad esempio, è possibile usare le sfumature di linea per rappresentare le modifiche nel tempo e nella distanza oppure con temperature diverse in una linea di oggetti connessa. Per applicare questa funzionalità a una linea, è necessario che l'opzione `lineMetrics` dell'origine dati sia impostata su true e quindi un'espressione di sfumatura del colore possa essere passata all'opzione `strokeColor` della linea. L'espressione di sfumatura del tratto deve fare riferimento all'espressione dati `['line-progress']` che espone le metriche di linea calcolate all'espressione.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linea con sfumatura del tratto" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>Line with Stroke Gradient</a> (Linea con sfumatura del tratto) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Personalizzare un livello linea

Per il livello linea sono disponibili varie opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello linea' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opzioni del livello linea</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine dati](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)
