---
title: Modificare lo stile della mappa in Mappe di Azure . Mappe di Microsoft Azure
description: In questo articolo verranno fornite informazioni sulle funzionalità correlate allo stile disponibili in Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335678"
---
# <a name="change-the-style-of-the-map"></a>Cambiare lo stile della mappa

La mappa supporta diverse opzioni di [stile](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) che possono essere impostate quando `setStyle` la mappa viene inizializzata o successivamente utilizzando la funzione maps. In questo articolo viene illustrato come utilizzare queste opzioni di stile per personalizzare l'aspetto delle mappe. Informazioni su come impostare uno stile al caricamento di una mappa e su come impostare un nuovo stile di mappa utilizzando il controllo selezione stile.

## <a name="set-the-style-options"></a>Impostare le opzioni di stile 

Le opzioni di stile possono essere passate alla mappa `setStyle` quando viene inizializzata o aggiornata in un secondo momento utilizzando la funzione maps.

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

Lo strumento seguente mostra come le diverse opzioni di stile modificano il modo in cui viene eseguito il rendering della mappa. Per vedere gli edifici 3D, ingrandisci vicino a una grande città. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni dello stile della mappa" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>opzioni di stile</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Mappa penna di Mappe di Azure ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Scegliere uno stile di mappa di base

Una delle opzioni di stile della mappa più comuni viene utilizzata per modificare lo stile della mappa di base a cui è applicato lo stile. Molti degli stili di [mappa supportati in Mappe](supported-map-styles.md) di Azure sono disponibili in Web SDK. 

### <a name="set-base-map-style-on-map-load"></a>Imposta stile mappa di base al caricamento della mappa


Lo stile della mappa può essere specificato `style` durante l'inizializzazione della mappa impostando l'opzione. Nel codice seguente, `style` l'opzione della `grayscale_dark` mappa è impostata su al successivo inizializzazione.

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

 Lo stile della mappa `setStyle` può essere `style` aggiornato utilizzando la funzione e impostando l'opzione sullo stile di mappa desiderato.

```javascript
map.setStyle({ style: 'satellite' });
```

Nel codice seguente, dopo il caricamento di un'istanza `road` `satellite` della mappa, lo stile della mappa viene aggiornato da a utilizzando la funzione [setStyle.In](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) the following code, after a map instance is loaded, the map style is updated from to using the setStyle function.

<br/>

<iframe height='500' scrolling='no' title='Aggiornamento dello stile' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Updating the style</a> (Aggiornamento dello stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Aggiungere il selettore di stile

Il controllo selezione stile offre un pulsante facile da usare con pannello a comparsa che può essere utilizzato dall'utente finale per modificare lo stile della mappa. Il selettore di stile ha due diverse opzioni di layout. Per impostazione predefinita, `icons` il selettore di stile utilizza il layout e visualizza tutto lo stile della mappa come una riga orizzontale di icone. 

<center>

![Layout dell'icona di selezione stili](media/choose-map-style/style-picker-icon-layout.png)</center>

La seconda opzione `list` di layout è denominata e visualizza un elenco scorrevole di stili di mappa.  

<center>

![Layout elenco di selezione stili](media/choose-map-style/style-picker-list-layout.png)</center>


Il codice seguente mostra come creare un'istanza del controllo di selezione stile e aggiungerla all'angolo superiore destro della mappa. Il selettore di stile è impostato per avere uno stile scuro e visualizzare alcuni stili di mappa selezionati utilizzando il layer elenco.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Il codice seguente aggiunge un controllo di selezione stile con le impostazioni predefinite alla mappa, in modo che l'utente possa passare facilmente tra i diversi stili di mappa. Attivare/disattivare lo stile della mappa utilizzando il controllo dello stile della mappa nell'angolo in alto a destra.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta del selettore di stile' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adding the style picker</a> (Aggiunta del selettore di stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Per impostazione predefinita, quando si usa il piano tariffario S0 di Azure Maps, il controllo selezione stile elenca tutti gli stili disponibili. Se si desidera ridurre il numero di stili in questo elenco, passare una matrice `mapStyle` degli stili che si desidera visualizzare nell'elenco nell'opzione del selettore di stile. Se si utilizza S1 e si desidera visualizzare `mapStyles` tutti gli stili `"all"`disponibili, impostare l'opzione del selettore di stile su .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StileOpzioni](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions (Opzioni Di StyleControl)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Aggiungere controlli alle mappe:

> [!div class="nextstepaction"]
> [Aggiungere controlli mappa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Aggiungere un segnaposto](map-add-pin.md)
