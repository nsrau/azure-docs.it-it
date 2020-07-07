---
title: Modificare lo stile della mappa in mappe di Azure | Mappe Microsoft Azure
description: In questo articolo vengono illustrate le funzionalità correlate allo stile disponibili in Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335678"
---
# <a name="change-the-style-of-the-map"></a>Cambiare lo stile della mappa

La mappa supporta diverse [Opzioni di stile](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) che possono essere impostate quando si inizializza la mappa o in un secondo momento usando la `setStyle` funzione maps. Questo articolo illustra come usare queste opzioni di stile per personalizzare l'aspetto delle mappe. Informazioni su come impostare uno stile durante il caricamento di una mappa e imparare a impostare un nuovo stile della mappa usando il controllo selezione stile.

## <a name="set-the-style-options"></a>Impostare le opzioni di stile 

Le opzioni di stile possono essere passate nella mappa quando vengono inizializzate o aggiornate in un secondo momento tramite la `setStyle` funzione maps.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

Lo strumento seguente mostra in che modo le diverse opzioni di stile cambiano in che modo viene eseguito il rendering della mappa. Per visualizzare gli edifici 3D, applicare lo zoom avanti a una città importante. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni stile mappa" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Opzioni di stile della mappa</a> di penna di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Scegliere uno stile della mappa di base

Una delle opzioni più comuni per lo stile della mappa viene utilizzata per modificare lo stile della mappa di base con stile. Molti degli [stili di mappa supportati in mappe di Azure](supported-map-styles.md) sono disponibili in Web SDK. 

### <a name="set-base-map-style-on-map-load"></a>Impostare lo stile della mappa di base sul carico della mappa


È possibile specificare lo stile della mappa quando si inizializza la mappa impostando l' `style` opzione. Nel codice seguente, l' `style` opzione della mappa è impostata `grayscale_dark` su all'inizializzazione.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Impostazione dello stile al caricamento della mappa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Setting the style on map load</a> (Impostazione dello stile al caricamento della mappa) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Aggiornare lo stile della mappa di base

 Lo stile della mappa può essere aggiornato utilizzando la `setStyle` funzione e impostando l' `style` opzione sullo stile della mappa desiderato.

```javascript
map.setStyle({ style: 'satellite' });
```

Nel codice seguente, dopo il caricamento di un'istanza della mappa, lo stile della mappa viene aggiornato da `road` a `satellite` utilizzando la funzione [Sestyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Aggiornamento dello stile' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Updating the style</a> (Aggiornamento dello stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Aggiungere il selettore di stile

Il controllo selezione stile fornisce un pulsante di facile utilizzo con il pannello a comparsa che può essere utilizzato dall'utente finale per modificare lo stile della mappa. Il selettore di stile dispone di due diverse opzioni di layout. Per impostazione predefinita, la selezione stile usa il `icons` layout e Visualizza tutto lo stile della mappa come una riga orizzontale di icone. 

<center>

![Layout dell'icona di selezione stile](media/choose-map-style/style-picker-icon-layout.png)</center>

Viene chiamata la seconda opzione `list` di layout e viene visualizzato un elenco scorrevole di stili mappa.  

<center>

![Layout elenco selezione stile](media/choose-map-style/style-picker-list-layout.png)</center>


Nel codice seguente viene illustrato come creare un'istanza del controllo selezione stile e aggiungerla all'angolo superiore destro della mappa. Il selettore stile è impostato in modo che abbia uno stile scuro e mostri un numero selezionato di stili di mappa utilizzando il livello elenco.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Il codice seguente aggiunge un controllo selezione stile con le impostazioni predefinite alla mappa, in modo che l'utente possa passare facilmente tra i diversi stili della mappa. Consente di impostare lo stile della mappa utilizzando il controllo dello stile della mappa accanto all'angolo superiore destro.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta del selettore di stile' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adding the style picker</a> (Aggiunta del selettore di stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Per impostazione predefinita, quando si usa il piano tariffario s0 di mappe di Azure, il controllo selezione stile elenca tutti gli stili disponibili. Se si desidera ridurre il numero di stili in questo elenco, passare una matrice degli stili che si desidera visualizzare nell'elenco nell' `mapStyle` opzione della selezione dello stile. Se si utilizza S1 e si desidera visualizzare tutti gli stili disponibili, impostare l' `mapStyles` opzione dello stile selezione su `"all"` .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Aggiungere i controlli alle mappe:

> [!div class="nextstepaction"]
> [Aggiungere controlli mappa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Aggiungere un segnaposto](map-add-pin.md)
