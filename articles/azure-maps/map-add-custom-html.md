---
title: Aggiungere un indicatore HTML in Mappe di Azure | Microsoft Docs
description: Come aggiungere un indicatore HTML alla mappa Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1c812a77429e13ea39b2f4946043c13e10aaf097
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769669"
---
# <a name="add-html-markers-to-the-map"></a>Aggiungere indicatori HTML alla mappa

Questo articolo illustra come aggiungere un codice HTML personalizzato, ad esempio un file di immagine, alla mappa come indicatore HTML.

> [!NOTE]
> Gli indicatore HTML non si connettono alle origini dati. Le informazioni sulla posizione sono invece aggiunte direttamente all'indicatore, il quale viene aggiunto alla proprietà `markers` delle mappe, un [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> A differenza della maggior parte dei livelli nel controllo Web di Mappe di Azure, dove per il rendering viene sfruttato WebGL, gli indicatori HTML usano elementi DOM tradizionali. Di conseguenza, più si aggiungono indicatori HTML a una pagina, più saranno disponibili elementi DOM. Le prestazioni possono calare dopo aver aggiunto alcune centinaia di indicatori HTML. Per set di dati più grandi, prendere in considerazione il clustering dei dati o l'uso di un livello per i simboli o per le bolle.

## <a name="add-an-html-marker"></a>Aggiungere un indicatore HTML

La classe HtmlMarker ha uno stile predefinito. È possibile personalizzare l'indicatore configurandone le impostazioni di colore e testo. Lo stile predefinito della classe HtmlMarker è un modello SVG con un segnaposto colore e testo. Configurare le proprietà di colore e testo nelle opzioni HtmlMarker per una rapida personalizzazione. 

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un indicatore HTML a una mappa' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento 2078801 <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Aggiungere un indicatore HTML a una mappa</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice aggiunge un [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) alla mappa usando la proprietà [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) della classe [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). L'HtmlMarker viene aggiunto alla mappa all'interno della funzione [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che venga visualizzato dopo il caricamento completo della mappa.

## <a name="create-svg-templated-html-marker"></a>Creare un indicatore HTML basato su modello SVG

Il valore predefinito `htmlContent` di un indicatore Html è un modello di SVG che include i segnaposto `{color}` e `{text}`. È possibile creare stringhe SVG personalizzate e aggiungere questi segnaposto in SVG, in modo che l'impostazione delle opzioni `color` e `text` dell'indicatore aggiorni tali segnaposto in SVG.

<br/>

<iframe height='500' scrolling='no' title='Indicatore HTML con Modello SVG personalizzato' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento 2078801 <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>indicatore HTML con modello SVG personalizzato</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

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
> [Aggiungere un livello per i simboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](./map-add-bubble-layer.md)