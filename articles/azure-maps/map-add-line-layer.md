---
title: Aggiungere un livello linea a Maps di Azure | Microsoft Docs
description: Come aggiungere un livello linea a Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f07e36d82c9044a212cda8173df9fe0a9544393a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977326"
---
# <a name="add-a-line-layer-to-the-map"></a>Aggiungere un livello linea alla mappa

Un livello linea può essere usato per eseguire `LineString` il `MultiLineString` rendering e le funzionalità come percorsi o route sulla mappa. Un livello linea può essere utilizzato anche per eseguire il rendering del `Polygon` contorno delle funzionalità e `MultiPolygon` . Un'origine dati è connessa a un livello linea per fornire i dati di cui eseguire il rendering. 

> [!TIP]
> Per impostazione predefinita, i livelli linea eseguono il rendering delle coordinate dei poligoni e delle linee in un'origine dati. Per limitare il livello in modo che esegua il rendering solo delle funzionalità `filter` LineString, impostare la proprietà `['==', ['geometry-type'], 'LineString']` del `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` livello su o se si desidera includere anche le funzionalità MultiLineString.

Il codice seguente illustra come creare una riga, aggiungerla a un'origine dati ed eseguirne il rendering con un livello linea usando la classe [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) .

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

Di seguito è riportato l'esempio di codice completo per l'esecuzione delle funzionalità sopra riportate.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere una linea a una mappa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Aggiungere una linea a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

È possibile applicare uno stile ai livelli linea utilizzando [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) e [utilizzare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Aggiungi simboli lungo una riga

In questo esempio viene illustrato come aggiungere icone a freccia lungo una riga sulla mappa. Quando si usa un livello di simboli, impostare l'opzione "posizionamento" su "linea", in modo da eseguire il rendering dei simboli lungo la linea e ruotare le icone (0 gradi = destra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostra freccia lungo la linea" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Per le mappe <a href='https://codepen.io/azuremaps/pen/drBJwX/'></a> di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>, vedere la freccia di visualizzazione della penna lungo la riga.
</iframe>

> [!TIP]
> Azure Maps Web SDK offre diversi modelli di immagine personalizzabili che è possibile usare con il livello dei simboli. Per altre informazioni, vedere il documento [come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md) .

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Aggiungere una sfumatura del tratto a una riga

Oltre a poter applicare un singolo colore tratto a una riga, è anche possibile riempire una linea con una sfumatura di colori per visualizzare la transizione da un segmento di linea a quello successivo. Ad esempio, è possibile usare le sfumature di linea per rappresentare le modifiche nel tempo e nella distanza oppure con temperature diverse in una linea di oggetti connessa. Per applicare questa funzionalità a una riga, è necessario che l' `lineMetrics` opzione dell'origine dati sia impostata su true, quindi un'espressione di sfumatura di colore possa essere passata `strokeColor` all'opzione della riga. L'espressione di sfumatura del tratto deve `['line-progress']` fare riferimento all'espressione di dati che espone le metriche della linea calcolata all'espressione.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Riga con sfumatura tratto" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la linea della penna <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>con sfumatura tratto</a> di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () in <a href='https://codepen.io'>CodePen</a>.
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
> [Aggiungere un livello poligono](map-add-shape.md)