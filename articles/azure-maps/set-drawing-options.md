---
title: Impostare le opzioni di disegno nelle mappe di Azure | Microsoft Docs
description: Come impostare i dati delle opzioni di disegno usando Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309785"
---
# <a name="set-drawing-options"></a>Imposta opzioni di disegno

Questo articolo illustra come diverse opzioni del gestore di [disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) modificano l'esperienza utente. È possibile specificare le opzioni per il gestore di disegno durante la creazione di un'istanza o utilizzare la funzione **drawingManager. SetOption ()** per impostare le opzioni.


## <a name="set-drawing-mode"></a>Imposta modalità di disegno

Il codice seguente crea un'istanza del gestore di disegno e imposta l'opzione della **modalità** di disegno. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Il codice seguente è un esempio completo in cui viene illustrato come impostare una modalità di disegno di gestione del disegno. Fare clic sulla mappa per iniziare a disegnare un poligono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Creare un poligono" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le informazioni su come <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>creare un poligono</a> per<a href='https://codepen.io/azuremaps'>@azuremaps</a>mappe di Azure () in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="set-interaction-type"></a>Imposta tipo di interazione

Il codice seguente imposta il tipo di interazione del disegno a cui deve aderire il gestore del disegno. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Di seguito è riportato l'esempio di codice che implementa la funzionalità che consente di estrarre liberamente la mappa, tenendo premuto il pulsante sinistro del mouse e trascinandolo. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Disegno a mano libera" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>disegno a mano libera</a> di Pen di Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="customizing-drawing-options"></a>Personalizzazione delle opzioni di disegno

Negli esempi precedenti è stato illustrato come personalizzare le opzioni di disegno durante la creazione di un'istanza di gestione disegno. È inoltre possibile impostare le opzioni di gestione disegno utilizzando la funzione **drawingManager. Seoptions ()** . Di seguito è riportato uno strumento per testare la personalizzazione di tutte le opzioni per la gestione del disegno usando la funzione seoptions.

<br/>

<iframe height="685" title="Personalizzare gestione disegno" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Vedere la pagina relativa ai <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>dati di forma</a> di penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>Get di Azure Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gestione disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra degli strumenti disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
