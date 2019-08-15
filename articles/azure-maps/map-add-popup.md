---
title: Aggiungere un popup con Mappe di Azure | Microsoft Docs
description: Come aggiungere un popup ad Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cde6c745034d0963bd372e36e6e5a046113c202b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976559"
---
# <a name="add-a-popup-to-the-map"></a>Aggiungere un popup alla mappa

Questo articolo illustra come aggiungere un pop a un punto in una mappa.

## <a name="understand-the-code"></a>Informazioni sul codice

Il codice seguente aggiunge alla mappa una funzionalità punto, `name` con `description` proprietà e, usando un livello di simboli. Viene creata un'istanza della [classe popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) che non viene visualizzata. Gli eventi del mouse vengono aggiunti al livello del simbolo per attivare l'apertura e la chiusura del popup quando il mouse passa sopra e fuori dal marcatore del simbolo. Quando il simbolo del marcatore viene spostato, la `position` proprietà del popup viene aggiornata con la posizione del marcatore e l' `content` opzione viene aggiornata con un codice HTML che esegue `description` il wrapping delle `name` proprietà e della funzionalità punto al passaggio del mouse. Il popup viene quindi visualizzato sulla mappa usando la relativa `open` funzione.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Di seguito è riportato l'esempio di codice completo per l'esecuzione delle funzionalità sopra riportate.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un popup usando Mappe di Azure' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a> (Aggiungere un popup usando Mappe di Azure) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Riutilizzare un popup con più punti

Quando si dispone di un numero elevato di punti e si desidera visualizzare solo un popup alla volta, l'approccio migliore consiste nel creare un popup e riutilizzarlo anziché creare un popup per ogni funzionalità del punto. Riutilizzando il popup, il numero di elementi DOM creati dall'applicazione è notevolmente ridotto, che può offrire prestazioni migliori. Nell'esempio seguente vengono create funzionalità a tre punti. Se si fa clic su una di esse, verrà visualizzato un popup con il contenuto di tale funzionalità punto.

<br/>

<iframe height='500' scrolling='no' title='Riutilizzo di popup con più segnaposto' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a> (Riutilizzo di popup con più segnaposto) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizzazione di un popup

Per impostazione predefinita, il popup presenta uno sfondo bianco, una freccia del puntatore in basso e un pulsante Chiudi nell'angolo superiore destro. Nell'esempio seguente il colore di sfondo viene modificato in nero `fillColor` utilizzando l'opzione del popup. Il pulsante Chiudi viene rimosso impostando l' `shoCloseButton` opzione su false. Il contenuto HTML del popup USA 10 pixel riempiti dai bordi del popup e il testo viene reso bianco, in modo che venga visualizzato perfettamente sullo sfondo nero.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup personalizzato" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>popup personalizzato</a> della penna di Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventi popup

I popup possono essere aperti, chiusi e trascinati. La classe popup fornisce eventi per aiutare gli sviluppatori a rispondere a queste azioni. Nell'esempio seguente vengono evidenziati gli eventi che vengono generati quando si apre, si chiude o si trascina il popup. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventi popup" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere gli <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eventi popup</a> della penna di Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Vedere gli articoli seguenti che contengono esempi efficaci di codice completo:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un indicatore HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello linea](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello poligono](map-add-shape.md)