---
title: Modelli di immagine in Azure Maps Web SDK | Microsoft Docs
description: Come usare i modelli di immagine in Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 1ca29fafae1269b21f4e39a9d2594f84af035d25
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856689"
---
# <a name="how-to-use-image-templates"></a>Come usare i modelli di immagine

Le immagini possono essere usate per marcatori HTML e diversi livelli all'interno di Azure Maps Web SDK:

 - I livelli dei simboli possono eseguire il rendering di punti sulla mappa con un'icona di immagine. È anche possibile eseguire il rendering dei simboli lungo un tracciato di righe.
 - È possibile eseguire il rendering dei livelli poligono con un'immagine del modello di riempimento. 
 - I marcatori HTML possono eseguire il rendering dei punti usando immagini e altri elementi HTML.

Per garantire prestazioni ottimali con i livelli, è necessario caricare queste immagini nella risorsa sprite dell'immagine della mappa prima del rendering. Per impostazione predefinita, il [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions) di SymbolLayer precarica un paio di immagini marcatore in pochi colori nell'immagine della mappa sprite. Queste stesse immagini del marcatore e altro ancora sono disponibili come modelli SVG e possono essere usate per creare immagini con scale personalizzate, oltre a un colore primario e secondario del cliente. In totale sono disponibili 42 modelli di immagine; 27 icone di simboli e 15 modelli di riempimento poligono.

I modelli di immagine possono essere aggiunti alle risorse sprite dell'immagine della mappa `map.imageSprite.createFromTemplate` utilizzando la funzione. Questa funzione consente di passare fino a cinque parametri;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id` dove è un identificatore univoco creato che viene assegnato all'immagine quando viene aggiunto allo sprite dell'immagine maps. Usare questo identificatore nei livelli per specificare quale risorsa immagine eseguire il rendering. Specifica `templateName` il modello di immagine da utilizzare. L' `color` opzione consente di impostare il colore principale dell'immagine e `secondaryColor` le opzioni per impostare il colore secondario dell'immagine. L' `scale` opzione consente di ridimensionare il modello di immagine prima di applicarlo all'immagine sprite. Quando l'immagine viene applicata allo sprite dell'immagine, viene convertita in un formato PNG. Per garantire il rendering nitido, è preferibile ridimensionare il modello di immagine prima di aggiungerlo allo sprite rispetto alla scalabilità verticale in un livello.

Questa funzione carica in modo asincrono l'immagine nello sprite dell'immagine e quindi restituisce un suggerimento che è possibile attendere il completamento di questa funzione.

Il codice seguente illustra come creare un'immagine da uno dei modelli predefiniti e usarla con un livello di simboli.

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>Usare un modello di immagine con un livello di simbolo

Quando un modello di immagine viene caricato nello sprite dell'immagine della mappa, è possibile eseguirne il rendering come simbolo in un livello di simbolo facendo riferimento all'ID `image` risorsa `iconOptions`dell'immagine nell'opzione di.

Nell'esempio seguente viene eseguito il rendering di un livello `marker-flat` di simbolo utilizzando il modello di immagine con un colore primario verde acqua e un colore secondario bianco. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello simbolo con modello icona predefinito" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il livello di simbolo della penna <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>con il modello di icona predefinito</a> di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Usare un modello di immagine lungo un tracciato di linee

Quando un modello di immagine viene caricato nello sprite dell'immagine della mappa, è possibile eseguirne il rendering lungo il percorso di una linea aggiungendo una LineString a un'origine dati e usando un livello `lineSpacing`di simbolo con un'opzione e facendo riferimento all'ID della risorsa `image` immagine nell'opzione o f Th `iconOptions`. 

Nell'esempio seguente viene eseguito il rendering di una linea rosa sulla mappa e viene utilizzato un livello `car` di simbolo utilizzando il modello di immagine con un colore primario blu Dodger e un colore secondario bianco. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello linea con modello icona predefinito" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>livello linea penna con il modello di icona predefinito</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Se il modello di immagine punta verso l'alto `rotation` , impostare l'opzione icona del livello simboli su 90 se si desidera che punti alla stessa direzione della riga.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Usare un modello di immagine con un livello poligono

Quando un modello di immagine viene caricato nello sprite dell'immagine della mappa, è possibile eseguirne il rendering come modello di riempimento in un livello poligono facendo riferimento all' `fillPattern` ID risorsa dell'immagine nell'opzione del livello.

Nell'esempio seguente viene eseguito il rendering di un livello `dot` poligono usando il modello di immagine con un colore primario rosso e un colore secondario trasparente.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Riempimento del poligono con il modello di icona predefinito" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il poligono di riempimento della penna <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>con il modello di icona predefinito</a> di<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> L'impostazione del colore secondario dei pattern di riempimento rende più semplice la visualizzazione della mappa sottostante che fornirà comunque il modello principale. 

## <a name="use-an-image-template-with-an-html-marker"></a>Usare un modello di immagine con un marcatore HTML

Un modello di immagine può essere recuperato usando `altas.getImageTemplate` la funzione e usato come contenuto di un marcatore HTML. Il modello può `htmlContent` essere passato nell'opzione del marcatore e quindi personalizzato mediante le `color`opzioni, `secondaryColor`e `text` .

Nell'esempio seguente viene usato `marker-arrow` il modello con un colore primario rosso, un colore secondario rosa e un valore di testo pari a "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marcatore HTML con modello di icona incorporato" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>marcatore HTML penna con il modello di icona predefinito</a> di Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Creare modelli riutilizzabili personalizzati

Se l'applicazione usa la stessa icona con icone diverse o se si sta creando un modulo che aggiunge modelli di immagine aggiuntivi, è possibile aggiungere e recuperare facilmente queste icone da Azure Maps Web SDK usando le funzioni statiche seguenti nel `atlas` namespace.

| Name | Tipo restituito | Descrizione | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Aggiunge un modello di immagine SVG personalizzato allo spazio dei nomi dell'Atlante. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Recupera un modello SVG in base al nome. |
| `getAllImageTemplateNames()` | string[] |  Recupera un modello SVG in base al nome. |

I modelli di immagine SVG supportano i seguenti valori segnaposto:

| Segnaposto | DESCRIZIONE |
|-|-|
| `{color}` | Colore primario. | 
| `{secondaryColor}` | Colore secondario. | 
| `{scale}` | L'immagine SVG viene convertita in un'immagine PNG quando viene aggiunta allo sprite dell'immagine della mappa. Questo segnaposto può essere utilizzato per ridimensionare un modello prima che venga convertito in modo da garantirne il rendering chiaro. | 
| `{text}` | Posizione in cui eseguire il rendering del testo quando viene usato con un marcatore HTML. |

L'esempio seguente illustra come prendere un modello SVG e aggiungerlo ad Azure Maps Web SDK come modello di icona riutilizzabile. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggiungere un modello di icona personalizzata allo spazio dei nomi Atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>aggiungere un modello di icona personalizzata allo spazio dei nomi Atlas</a> da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Elenco di modelli di immagine

La tabella seguente elenca tutti i modelli di immagine attualmente disponibili in Azure Maps Web SDK con il nome del modello sopra ogni immagine. Per impostazione predefinita, il colore primario è blu e il colore secondario è bianco. Per semplificare la visualizzazione del colore secondario su uno sfondo bianco, le immagini seguenti hanno il colore secondario impostato su nero.

**Modelli icona simboli**

|||||
|:-:|:-:|:-:|:-:|
| marcatore | spessore indicatore | cerchio indicatore | indicatore-Flat |
|![icona marcatore](./media/image-templates/marker.png)|![icona spessore indicatore](./media/image-templates/marker-thick.png)|![icona del cerchio del marcatore](./media/image-templates/marker-circle.png)|![icona marcatore-Flat](./media/image-templates/marker-flat.png)|
||||
| quadrato marcatore | marcatore-quadrato-cluster | freccia marcatore | indicatore-palla-pin | 
|![icona marcatore-quadrato](./media/image-templates/marker-square.png)|![icona marcatore-quadrato-cluster](./media/image-templates/marker-square-cluster.png)|![icona freccia marcatore](./media/image-templates/marker-arrow.png)|![icona indicatore-palla-pin](./media/image-templates/marker-ball-pin.png)|
||||
| marcatore-quadrato arrotondato | marcatore-quadrato-arrotondato-cluster | bandiera | flag-triangolo |
| ![icona marcatore-quadrato arrotondato](./media/image-templates/marker-square-rounded.png) | ![icona del cluster di marcatore-quadrato-arrotondato](./media/image-templates/marker-square-rounded-cluster.png) | ![icona contrassegno](./media/image-templates/flag.png) | ![Flag-icona triangolo](./media/image-templates/flag-triangle.png) |
||||
| triangolo | spessore triangolare | triangolo-freccia su | triangolo-freccia sinistra |
| ![icona a forma di triangolo](./media/image-templates/triangle.png) | ![icona a forma di triangolo](./media/image-templates/triangle-thick.png) | ![triangolo-icona freccia su](./media/image-templates/triangle-arrow-up.png) | ![icona a forma di triangolo e freccia sinistra](./media/image-templates/triangle-arrow-left.png) |
||||
| esagono | spessore esagonale | esagonale arrotondato | esagono-arrotondato-spessore |
| ![icona esagonale](./media/image-templates/hexagon.png) | ![icona con spessore esagonale](./media/image-templates/hexagon-thick.png) | ![icona esagonale arrotondata](./media/image-templates/hexagon-rounded.png) | ![icona di spessore esagonale-arrotondato](./media/image-templates/hexagon-rounded-thick.png) |
||||
| aggiungi | pin-round | arrotondato-quadrato | arrotondato-spessore quadrato |
| ![icona Aggiungi](./media/image-templates/pin.png) | ![icona pin-round](./media/image-templates/pin-round.png) | ![icona arrotondata-quadrata](./media/image-templates/rounded-square.png) | ![icona arrotondata-con spessore rettangolare](./media/image-templates/rounded-square-thick.png) |
||||
| freccia su | freccia su-sottile | car ||
| ![icona freccia su](./media/image-templates/arrow-up.png) | ![freccia su-icona sottile](./media/image-templates/arrow-up-thin.png) | ![icona dell'auto](./media/image-templates/car.png) | |

**Modelli modello di riempimento poligono**

|||||
|:-:|:-:|:-:|:-:|
| Checker | controllo-ruotato | Cerchi | Cerchi-spaziatura |
| ![icona di controllo](./media/image-templates/checker.png) | ![controllo-icona ruotata](./media/image-templates/checker-rotated.png) | ![icona cerchi](./media/image-templates/circles.png) | ![icona con spaziatura a cerchi](./media/image-templates/circles-spaced.png) |
|||||
| righe diagonali | diagonali-linee-in basso | strisce diagonali | strisce diagonali-giù |
| ![icona linee diagonali](./media/image-templates/diagonal-lines-up.png) | ![icona delle linee diagonali](./media/image-templates/diagonal-lines-down.png) | ![icona con strisce diagonali](./media/image-templates/diagonal-stripes-up.png) | ![icona con strisce diagonali](./media/image-templates/diagonal-stripes-down.png) |
|||||
| linee griglia | ruotato-griglie-linee | ruotato-griglie-striping | riempimento x |
| ![icona linee griglia](./media/image-templates/grid-lines.png) | ![icona ruotata-griglia-linee](./media/image-templates/rotated-grid-lines.png) | ![icona ruotata-griglia-Stripe](./media/image-templates/rotated-grid-stripes.png) | ![icona riempimento x](./media/image-templates/x-fill.png) |
|||||
| zig-zag | zig-zag-verticale | punti |  |
| ![icona zig-zag](./media/image-templates/zig-zag.png) | ![icona zig-zag-Vertical](./media/image-templates/zig-zag-vertical.png) | ![icona punti](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Strumento prova ora

Con lo strumento seguente, è possibile eseguire il rendering dei diversi modelli di immagine incorporati in diversi modi e personalizzare i colori e la scala primari e secondari.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opzioni del modello icona" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Opzioni del modello icona</a> della penna di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [spazio dei nomi Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Vedere gli articoli seguenti per altri esempi di codice in cui è possibile usare i modelli di immagine:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere una forma](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere marcatori HTML](map-add-bubble-layer.md)
