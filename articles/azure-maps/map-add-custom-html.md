---
title: Aggiungere un indicatore HTML in Mappe | Mappe di Microsoft Azure
description: Informazioni su come aggiungere marcatori HTML a maps. Vedere come usare Azure Maps Web SDK per personalizzare i marcatori e aggiungere popup e eventi del mouse a un marcatore.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 58954a98215fd353c5944486446dab5664e5349c
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035434"
---
# <a name="add-html-markers-to-the-map"></a>Aggiungere indicatori HTML alla mappa

Questo articolo illustra come aggiungere un codice HTML personalizzato, ad esempio un file di immagine, alla mappa come indicatore HTML.

> [!NOTE]
> Gli indicatore HTML non si connettono alle origini dati. Le informazioni sulla posizione sono invece aggiunte direttamente all'indicatore, il quale viene aggiunto alla proprietà `markers` delle mappe, un [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> A differenza della maggior parte dei livelli nel controllo Web di Mappe di Azure, dove per il rendering viene sfruttato WebGL, gli indicatori HTML usano elementi DOM tradizionali. Di conseguenza, più si aggiungono indicatori HTML a una pagina, più saranno disponibili elementi DOM. Le prestazioni possono calare dopo aver aggiunto alcune centinaia di indicatori HTML. Per set di dati più grandi, prendere in considerazione il clustering dei dati o l'uso di un livello per i simboli o per le bolle.

## <a name="add-an-html-marker"></a>Aggiungere un indicatore HTML

La classe [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) ha uno stile predefinito. È possibile personalizzare l'indicatore configurandone le impostazioni di colore e testo. Lo stile predefinito della classe HtmlMarker è un modello SVG con un segnaposto `{color}` e `{text}`. Configurare le proprietà di colore e testo nelle opzioni dell'indicatore HTML per una rapida personalizzazione. 

Il codice seguente crea un indicatore HTML e imposta la proprietà del colore su "DodgerBlue" e la proprietà del testo su "10". All'indicatore viene associato un popup e, per attivare la visibilità del popup, viene usato l'evento `click`.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Di seguito è riportato l'esempio di codice completo e funzionante della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un indicatore HTML a una mappa' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento 2078801 <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Aggiungere un indicatore HTML a una mappa</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Creare un indicatore HTML basato su modello SVG

Il valore predefinito `htmlContent` di un indicatore Html è un modello di SVG che include i segnaposto `{color}` e `{text}`. È possibile creare stringhe SVG personalizzate e aggiungere questi segnaposto in SVG, in modo che l'impostazione delle opzioni `color` e `text` dell'indicatore aggiorni tali segnaposto in SVG.

<br/>

<iframe height='500' scrolling='no' title='Indicatore HTML con Modello SVG personalizzato' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento 2078801 <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>indicatore HTML con modello SVG personalizzato</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps Web SDK offre vari modelli di immagini SVG che possono essere usati con indicatori HTML. Per altre informazioni, vedere il documento [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md).

## <a name="add-a-css-styled-html-marker"></a>Aggiungere un indicatore HTML in stile CSS

Uno dei vantaggi degli indicatori HTML è la presenza di diverse eccezionali personalizzazioni che si possono ottenere sfruttando lo stile CSS. In questo esempio, il contenuto di HtmlMarker è costituito da HTML e CSS che creano un indicatore animato che cade sulla posizione ed emette un'onda.

<br/>

<iframe height='500' scrolling='no' title='Origine dati HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Indicatori HTML trascinabili

Questo esempio mostra come rendere trascinabile un indicatore HTML. Gli indicatori HTML supportano gli eventi `drag`, `dragstart` e `dragend`.

<br/>

<iframe height='500' scrolling='no' title='Indicatore HTML trascinabile' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento 2078801 <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Indicatore HTML trascinabile</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Aggiungere eventi del mouse a indicatori HTML

Questi esempi illustrano come aggiungere eventi mouse-and-drag a un marcatore HTML.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere eventi del mouse a indicatori HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento 2078801 <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Aggiungere eventi del mouse a indicatori HTML</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](./map-add-bubble-layer.md)
