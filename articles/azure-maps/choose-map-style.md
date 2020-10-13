---
title: Modificare lo stile del controllo mappa Web Maps di Azure
description: Informazioni su come modificare lo stile e le opzioni di una mappa. Vedere come aggiungere un controllo selezione stile a una mappa in mappe di Azure in modo che gli utenti possano passare da uno stile all'altro.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: e993e3086ac63a6e9d5b8372327e35016b36239f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285163"
---
# <a name="change-the-style-of-the-map"></a>Cambiare lo stile della mappa

Il controllo mappa supporta diverse opzioni di [stile](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) della mappa e [stili di mappa di base](supported-map-styles.md). Tutti gli stili possono essere impostati quando è in corso l'inizializzazione del controllo mappa. In alternativa, è possibile impostare gli stili usando la funzione del controllo mappa `setStyle` . Questo articolo illustra come usare queste opzioni di stile per personalizzare l'aspetto della mappa. Si apprenderà anche come implementare il controllo selezione stile nella mappa. Il controllo selezione stile consente all'utente di alternare diversi stili di base.

## <a name="set-map-style-options"></a>Imposta opzioni stile mappa

È possibile impostare le opzioni di stile durante l'inizializzazione del controllo Web. In alternativa, è possibile aggiornare le opzioni di stile chiamando la funzione del controllo mappa `setStyle` . Per visualizzare tutte le opzioni di stile disponibili, vedere [Opzioni di stile](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions).

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo = true,
    showFeedbackLink = true,
    style='road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo = false,
    showFeedbackLink = false
});
```

Lo strumento seguente mostra in che modo le diverse opzioni di stile cambiano in che modo viene eseguito il rendering della mappa. Per visualizzare gli edifici 3D, applicare lo zoom avanti a una città importante.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni stile mappa" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Opzioni di stile della mappa</a> di penna di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>Impostare uno stile della mappa di base

È anche possibile inizializzare il controllo mappa con uno degli [stili della mappa di base](supported-map-styles.md) disponibili in Web SDK. È quindi possibile usare la `setStyle` funzione per aggiornare lo stile di base con uno stile di mappa diverso.

### <a name="set-a-base-map-style-on-initialization"></a>Impostare uno stile della mappa di base all'inizializzazione

Gli stili di base del controllo mappa possono essere impostati durante l'inizializzazione. Nel codice seguente, l' `style` opzione del controllo mappa è impostata sullo [ `grayscale_dark` stile della mappa di base](supported-map-styles.md#grayscale_dark).  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Impostazione dello stile al caricamento della mappa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Setting the style on map load</a> (Impostazione dello stile al caricamento della mappa) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Aggiornare lo stile della mappa di base

Lo stile della mappa di base può essere aggiornato usando la `setStyle` funzione e impostando l' `style` opzione su modifica in uno stile di mappa di base diverso o Aggiungi altre opzioni di stile.

```javascript
map.setStyle({ style: 'satellite' });
```

Nel codice seguente, dopo il caricamento di un'istanza della mappa, lo stile della mappa viene aggiornato da `grayscale_dark` a `satellite` utilizzando la funzione [Sestyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Aggiornamento dello stile' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Updating the style</a> (Aggiornamento dello stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker-control"></a>Aggiungere il controllo selezione stile

Il controllo selezione stile fornisce un pulsante di facile utilizzo con il pannello a comparsa che può essere utilizzato dall'utente finale per spostarsi tra gli stili di base.

Il selettore stile presenta due diverse opzioni `icon` di layout: e `list` . Inoltre, la selezione stile consente di scegliere due diverse opzioni di controllo di selezione stile `style` : `light` e `dark` . In questo esempio, la selezione stile usa il `icon` layout e visualizza un elenco di selezione degli stili della mappa di base sotto forma di icone. Il selettore di controllo dello stile include il set di stili di base seguente: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . Per altre informazioni sulle opzioni di controllo di selezione stile, vedere [Opzioni di controllo dello stile](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions).

L'immagine seguente mostra il controllo selezione stile visualizzato nel `icon` layout.

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Layout dell'icona di selezione stile":::

L'immagine seguente mostra il controllo selezione stile visualizzato nel `list` layout.

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Layout dell'icona di selezione stile":::

> [!IMPORTANT]
> Per impostazione predefinita, il controllo selezione stile elenca tutti gli stili disponibili nel piano tariffario s0 di mappe di Azure. Se si desidera ridurre il numero di stili in questo elenco, passare una matrice degli stili che si desidera visualizzare nell'elenco nell' `mapStyle` opzione della selezione dello stile. Se si utilizza S1 e si desidera visualizzare tutti gli stili disponibili, impostare l' `mapStyles` opzione dello stile selezione su `"all"` .

Il codice seguente illustra come eseguire l'override dell' `mapStyles` elenco di stili di base predefinito. In questo esempio viene impostata l' `mapStyles` opzione per elencare gli stili di base che si desidera visualizzare tramite il controllo selezione stile.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta del selettore di stile' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adding the style picker</a> (Aggiunta del selettore di stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

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
