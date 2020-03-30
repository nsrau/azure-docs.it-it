---
title: Modelli di immagine in Azure Maps Web SDK Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come usare i modelli di immagine con i marcatori HTML e vari livelli in Web SDK di Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: f3b1141ea3c3c8e33b8a2ae12c22b6962a90d32b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198225"
---
# <a name="how-to-use-image-templates"></a>Come usare i modelli di immagine

Le immagini possono essere usate con i marcatori HTML e i vari layer all'interno di Azure Maps Web SDK:

 - I layer simbolo possono eseguire il rendering dei punti sulla mappa con un'icona immagine. È inoltre possibile eseguire il rendering dei simboli lungo un percorso di linee.
 - È possibile eseguire il rendering dei livelli poligono con un'immagine del motivo di riempimento. 
 - I marcatori HTML possono eseguire il rendering dei punti utilizzando immagini e altri elementi HTML.

Per garantire buone prestazioni con i livelli, caricate le immagini nella risorsa sprite dell'immagine della mappa prima del rendering. [Il IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), del SymbolLayer , precarica un paio di immagini marker in una manciata di colori nella mappa immagine sprite, per impostazione predefinita. Queste immagini marker e altro ancora sono disponibili come modelli SVG. Possono essere utilizzati per creare immagini con scale personalizzate o come colore primario e secondario del cliente. In totale sono disponibili 42 modelli di immagine: 27 icone simbolo e 15 motivi di riempimento poligono.

I modelli di immagine possono essere aggiunti alle `map.imageSprite.createFromTemplate` risorse sprite dell'immagine della mappa utilizzando la funzione. Questa funzione consente di passare fino a cinque parametri;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

Si `id` tratta di un identificatore univoco creato. L'oggetto `id` viene assegnato all'immagine quando viene aggiunto allo sprite dell'immagine delle mappe. Usate questo identificatore nei livelli per specificare la risorsa immagine di cui eseguire il rendering. Il `templateName` specifica quale modello di immagine utilizzare. L'opzione `color` imposta il colore primario dell'immagine e le `secondaryColor` opzioni imposta il colore secondario dell'immagine. L'opzione `scale` ridimensiona il modello di immagine prima di applicarlo allo sprite immagine. Quando l'immagine viene applicata allo sprite dell'immagine, viene convertita in png. Per garantire un rendering nitido, è preferibile aumentare il modello di immagine prima di aggiungerlo allo sprite, piuttosto che aumentarlo in un livello.

Questa funzione carica in modo asincrono l'immagine nello sprite dell'immagine. Pertanto, restituisce una promessa che è possibile attendere il completamento di questa funzione.

Il codice seguente mostra come creare un'immagine da uno dei modelli predefiniti e usarla con un livello simbolo.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Usare un modello di immagine con un livello simbolo

Una volta caricato nello sprite dell'immagine della mappa, è possibile renderlo come simbolo in un `image` livello `iconOptions`di simbolo facendo riferimento all'ID risorsa immagine nell'opzione dell'opzione .

L'esempio seguente esegue il `marker-flat` rendering di un livello simbolo usando il modello di immagine con un colore primario tè e un colore secondario bianco. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello di simbolo con modello di icona incorporato" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il livello Simbolo penna <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>con il modello</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>di icona incorporato di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Usare un modello di immagine lungo un percorso di linee

Una volta che un modello di immagine viene caricato nello sprite dell'immagine della mappa, è possibile eseguire il `lineSpacing`rendering lungo il percorso di una linea `image` aggiungendo `iconOptions`un LineString a un'origine dati e utilizzando un livello simbolo con un'opzione e facendo riferimento all'ID della risorsa immagine nell'opzione di th . 

L'esempio seguente esegue il rendering di una linea `car` rosa sulla mappa e usa un livello simbolo usando il modello di immagine con un colore primario blu dodger e un colore secondario bianco. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello linea con modello di icona incorporato" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il livello Linea penna <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>con modello</a> di<a href='https://codepen.io/azuremaps'>@azuremaps</a>icona incorporato di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Se il modello di immagine `rotation` punta verso l'alto, impostare l'opzione dell'icona del livello simbolo su 90 se si desidera che punti nella stessa direzione della linea.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Usare un modello di immagine con un livello poligono

Una volta che un modello di immagine viene caricato nello sprite dell'immagine della mappa, può essere `fillPattern` visualizzato come un motivo di riempimento in un livello poligono facendo riferimento all'ID della risorsa immagine nell'opzione del livello.

L'esempio seguente esegue il `dot` rendering di un livello poligono usando il modello di immagine con un colore primario rosso e un colore secondario trasparente.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Riempi poligono con modello di icona incorporato" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>poligono Di riempimento</a> penna con<a href='https://codepen.io/azuremaps'>@azuremaps</a>icon incorporato di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> L'impostazione del colore secondario dei motivi di riempimento rende più semplice visualizzare la mappa sottostante continuerà a fornire il motivo primario. 

## <a name="use-an-image-template-with-an-html-marker"></a>Usare un modello di immagine con un marcatore HTML

Un modello di immagine `altas.getImageTemplate` può essere recuperato utilizzando la funzione e utilizzato come contenuto di un marcatore HTML. Il modello può essere `htmlContent` passato all'opzione del marcatore `color` `secondaryColor`e `text` quindi personalizzato utilizzando le opzioni , e .

Nell'esempio seguente `marker-arrow` viene utilizzato il modello con un colore primario rosso, un colore secondario rosa e un valore di testo pari a "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marcatore HTML con modello di icona incorporato" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>marcatore HTML</a> della penna con il<a href='https://codepen.io/azuremaps'>@azuremaps</a>modello di icona incorporato di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Creare modelli riutilizzabili personalizzati

Se l'applicazione usa la stessa icona con icone diverse o se si crea un modulo che aggiunge altri modelli di immagine, è possibile aggiungerle e recuperarle facilmente da Azure Maps Web SDK. Usare le funzioni statiche seguenti nello `atlas` spazio dei nomi .

| Nome | Tipo restituito | Descrizione | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Aggiunge un modello di immagine SVG personalizzato allo spazio dei nomi atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Recupera un modello SVG in base al nome. |
| `getAllImageTemplateNames()` | string[] |  Recupera un modello SVG in base al nome. |

I modelli di immagine SVG supportano i seguenti valori segnaposto:

| Segnaposto | Descrizione |
|-|-|
| `{color}` | Colore primario. | 
| `{secondaryColor}` | Colore secondario. | 
| `{scale}` | L'immagine SVG viene convertita in un'immagine png quando viene aggiunta allo sprite dell'immagine della mappa. Questo segnaposto può essere utilizzato per ridimensionare un modello prima che venga convertito per garantire che il rendering venga eseguito in modo chiaro. | 
| `{text}` | Posizione in cui eseguire il rendering del testo quando viene utilizzato con un marcatore HTML. |

L'esempio seguente mostra come accettare un modello SVG e aggiungerlo a Azure Maps Web SDK come modello di icona riutilizzabile. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggiungere un modello di icona personalizzato allo spazio dei nomi atlasAdd custom icon template to atlas namespace" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il modello di icona personalizzata Pen Add<a href='https://codepen.io/azuremaps'>@azuremaps</a>to <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>atlas</a> di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Elenco dei modelli di immagine

Questa tabella elenca tutti i modelli di immagine attualmente disponibili nell'SDK Web di Azure Maps.This table lists all image templates currently available within the Azure Maps web SDK. Il nome del modello è sopra ogni immagine. Per impostazione predefinita, il colore primario è blu e il colore secondario è il bianco. Per rendere il colore secondario più facile da vedere su uno sfondo bianco, le immagini seguenti hanno il colore secondario impostato sul nero.

**Modelli di icone dei simboli**

|||||
|:-:|:-:|:-:|:-:|
| marker | spessore | marker-cerchio | marcatore-piatto |
|![icona del marcatore](./media/image-templates/marker.png)|![icona di spessore marcatore](./media/image-templates/marker-thick.png)|![icona del cerchio marcatore](./media/image-templates/marker-circle.png)|![icona indicatore-piatta](./media/image-templates/marker-flat.png)|
||||
| marcatore-quadrato | marcatore-quadrato-cluster | marcatore-freccia | marker-ball-pin | 
|![icona del marcatore quadrato](./media/image-templates/marker-square.png)|![icona marker-square-cluster](./media/image-templates/marker-square-cluster.png)|![icona a freccia di pennarello](./media/image-templates/marker-arrow.png)|![icona marker-ball-pin](./media/image-templates/marker-ball-pin.png)|
||||
| arrotondato a un marcatore quadrato | marcatore quadrato-arrotondato-cluster | flag | bandiera-triangolo |
| ![icona a forma di quadrato](./media/image-templates/marker-square-rounded.png) | ![icona del cluster a arrotondato](./media/image-templates/marker-square-rounded-cluster.png) | ![icona della bandiera](./media/image-templates/flag.png) | ![Icona di flag-triangle](./media/image-templates/flag-triangle.png) |
||||
| triangolo | spessore di triangolo | triangolo-freccia su | triangolo-freccia-sinistra |
| ![icona triangolo](./media/image-templates/triangle.png) | ![icona a triangolo spesso](./media/image-templates/triangle-thick.png) | ![Icona triangolare verso l'alto](./media/image-templates/triangle-arrow-up.png) | ![icona triangolo-freccia-sinistra](./media/image-templates/triangle-arrow-left.png) |
||||
| Esagono | spessore esagono | arrotondato esagonale | esagono-arrotondato di spessore |
| ![Icona esagonale](./media/image-templates/hexagon.png) | ![Icona spessa esagono](./media/image-templates/hexagon-thick.png) | ![Icona arrotondata esagono](./media/image-templates/hexagon-rounded.png) | ![Icona spessa arrotondata](./media/image-templates/hexagon-rounded-thick.png) |
||||
| aggiunta | pin-round | quadrato arrotondato | arrotondato-quadrato-spessore |
| ![Icona Aggiungi](./media/image-templates/pin.png) | ![icona rotonda a puntini](./media/image-templates/pin-round.png) | ![icona arrotondata-quadrato](./media/image-templates/rounded-square.png) | ![icona di spessore arrotondato](./media/image-templates/rounded-square-thick.png) |
||||
| freccia in su | freccia su-sottile | car ||
| ![icona freccia su](./media/image-templates/arrow-up.png) | ![icona a freccia su-sottile](./media/image-templates/arrow-up-thin.png) | ![icona dell'auto](./media/image-templates/car.png) | |

**Modelli di motivi di riempimento poligono**

|||||
|:-:|:-:|:-:|:-:|
| Checker | ruotato a scacchi | cerchi | spazi a cerchi |
| ![icona del correttore](./media/image-templates/checker.png) | ![Icona ruotata con il correttore](./media/image-templates/checker-rotated.png) | ![icona cerchi](./media/image-templates/circles.png) | ![icona con spaziatura dei cerchi](./media/image-templates/circles-spaced.png) |
|||||
| linee diagonali-up | diagonale-linee-giù | strisce diagonali-up | strisce diagonali-giù |
| ![icona diagonale-up](./media/image-templates/diagonal-lines-up.png) | ![icona diagonale-linee verso il basso](./media/image-templates/diagonal-lines-down.png) | ![Icona a strisce diagonali](./media/image-templates/diagonal-stripes-up.png) | ![icona a strisce diagonali verso il basso](./media/image-templates/diagonal-stripes-down.png) |
|||||
| linee griglia | linee a griglia ruotate | rotaia ruotata | Riempimento x |
| ![Icona delle linee della griglia](./media/image-templates/grid-lines.png) | ![icona a griglia-linee ruotate](./media/image-templates/rotated-grid-lines.png) | ![icona di strisce a griglia ruotata](./media/image-templates/rotated-grid-stripes.png) | ![Icona x-fill](./media/image-templates/x-fill.png) |
|||||
| zig-zag | zig-zag-verticale | Punti |  |
| ![Icona zig-zag](./media/image-templates/zig-zag.png) | ![Icona zig-zag-verticale](./media/image-templates/zig-zag-vertical.png) | ![icona con i punti](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Provalo ora

Con lo strumento seguente, è possibile eseguire il rendering dei diversi modelli di immagine incorporati in vari modi e personalizzare i colori e la scala primari e secondari.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opzioni del modello di icona" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere opzioni del <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>modello Icona</a> penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>di Mappe di Azure ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [spazio dei nomi atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Per altri esempi di codice in cui è possibile usare i modelli di immagine, vedere gli articoli seguenti:See the following articles for more code samples where image templates can be used:

> [!div class="nextstepaction"]
> [Aggiungere un livello simbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere marcatori HTML](map-add-bubble-layer.md)
