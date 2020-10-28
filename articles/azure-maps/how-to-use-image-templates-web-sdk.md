---
title: Modelli di immagine in Azure Maps Web SDK | Mappe Microsoft Azure
description: Informazioni su come aggiungere icone di immagini e poligoni riempiti da pattern a Maps usando Azure Maps Web SDK. Visualizzare i modelli di modello di riempimento e immagine disponibili.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895682"
---
# <a name="how-to-use-image-templates"></a>Come usare i modelli di immagine

Le immagini possono essere usate con marcatori HTML e diversi livelli all'interno di Azure Maps Web SDK:

 - I livelli dei simboli possono eseguire il rendering di punti sulla mappa con un'icona di immagine. È anche possibile eseguire il rendering dei simboli lungo un tracciato di righe.
 - È possibile eseguire il rendering dei livelli poligono con un'immagine del modello di riempimento. 
 - I marcatori HTML possono eseguire il rendering dei punti usando immagini e altri elementi HTML.

Per garantire prestazioni ottimali con i livelli, caricare le immagini nella risorsa sprite dell'immagine della mappa prima del rendering. Per impostazione predefinita, [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions), di SymbolLayer, consente di precaricare un paio di immagini del marcatore in pochi colori nello sprite dell'immagine della mappa. Queste immagini del marcatore e altro ancora sono disponibili come modelli SVG. Possono essere usati per creare immagini con scale personalizzate o usate come colore primario e secondario del cliente. In totale sono disponibili modelli di immagine 42:27 icone dei simboli e 15 modelli di riempimento poligono.

I modelli di immagine possono essere aggiunti alle risorse sprite dell'immagine della mappa utilizzando la `map.imageSprite.createFromTemplate` funzione. Questa funzione consente di passare fino a cinque parametri;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id`È un identificatore univoco creato dall'utente. `id`Viene assegnato all'immagine quando viene aggiunto allo sprite dell'immagine maps. Usare questo identificatore nei livelli per specificare quale risorsa immagine eseguire il rendering. `templateName`Specifica il modello di immagine da utilizzare. L' `color` opzione consente di impostare il colore principale dell'immagine e le `secondaryColor` Opzioni per impostare il colore secondario dell'immagine. L' `scale` opzione consente di ridimensionare il modello di immagine prima di applicarlo all'immagine sprite. Quando l'immagine viene applicata allo sprite dell'immagine, viene convertita in un formato PNG. Per garantire il rendering nitido, è preferibile ridimensionare il modello di immagine prima di aggiungerlo allo sprite, anziché ridimensionarlo in un livello.

Questa funzione carica in modo asincrono l'immagine nello sprite dell'immagine. Pertanto, viene restituita una promessa che è possibile attendere il completamento di questa funzione.

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

Quando un modello di immagine viene caricato nello sprite dell'immagine della mappa, è possibile eseguirne il rendering come simbolo in un livello di simbolo facendo riferimento all'ID risorsa dell'immagine nell' `image` opzione di `iconOptions` .

Nell'esempio seguente viene eseguito il rendering di un livello di simbolo utilizzando il `marker-flat` modello di immagine con un colore primario verde acqua e un colore secondario bianco. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello simbolo con modello icona predefinito" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il livello di simbolo della penna <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>con il modello di icona predefinito</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Usare un modello di immagine lungo un tracciato di linee

Quando un modello di immagine viene caricato nello sprite dell'immagine della mappa, è possibile eseguirne il rendering lungo il percorso di una linea aggiungendo una LineString a un'origine dati e usando un livello di simbolo con un' `lineSpacing` opzione e facendo riferimento all'ID della risorsa immagine nell' `image` opzione di `iconOptions` . 

Nell'esempio seguente viene eseguito il rendering di una linea rosa sulla mappa e viene utilizzato un livello di simbolo utilizzando il `car` modello di immagine con un colore primario blu Dodger e un colore secondario bianco. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello linea con modello icona predefinito" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>livello linea penna con il modello di icona predefinito</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Se il modello di immagine punta verso l'alto, impostare l' `rotation` opzione icona del livello simboli su 90 se si desidera che punti alla stessa direzione della riga.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Usare un modello di immagine con un livello poligono

Quando un modello di immagine viene caricato nello sprite dell'immagine della mappa, è possibile eseguirne il rendering come modello di riempimento in un livello poligono facendo riferimento all'ID risorsa dell'immagine nell' `fillPattern` opzione del livello.

Nell'esempio seguente viene eseguito il rendering di un livello poligono usando il `dot` modello di immagine con un colore primario rosso e un colore secondario trasparente.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Riempimento del poligono con il modello di icona predefinito" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>poligono di riempimento della penna con il modello di icona predefinito</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> L'impostazione del colore secondario dei pattern di riempimento rende più semplice la visualizzazione della mappa sottostante che fornirà comunque il modello principale. 

## <a name="use-an-image-template-with-an-html-marker"></a>Usare un modello di immagine con un marcatore HTML

Un modello di immagine può essere recuperato usando la `altas.getImageTemplate` funzione e usato come contenuto di un marcatore HTML. Il modello può essere passato nell' `htmlContent` opzione del marcatore e quindi personalizzato mediante le `color` `secondaryColor` Opzioni, e `text` .

Nell'esempio seguente viene usato il `marker-arrow` modello con un colore primario rosso, un colore secondario rosa e un valore di testo pari a "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marcatore HTML con modello di icona incorporato" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>marcatore HTML penna con il modello di icona predefinito</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> È anche possibile usare i modelli di immagine all'esterno della mappa. Il funciton getImageTemplate restituisce una stringa SVG con segnaposto; `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. Sostituire i valori segnaposto per creare una stringa SVG valida. È quindi possibile aggiungere la stringa SVG direttamente al DOM HTML oppure convertirla in un URI di dati e inserirla in un tag di immagine. Esempio:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>Creare modelli riutilizzabili personalizzati

Se l'applicazione usa la stessa icona con icone diverse o se si sta creando un modulo che aggiunge modelli di immagine aggiuntivi, è possibile aggiungere e recuperare facilmente queste icone da Azure Maps Web SDK. Usare le funzioni statiche seguenti nello `atlas` spazio dei nomi.

| Nome | Tipo restituito | Description | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Aggiunge un modello di immagine SVG personalizzato allo spazio dei nomi dell'Atlante. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Recupera un modello SVG in base al nome. |
| `getAllImageTemplateNames()` | string[] |  Recupera un modello SVG in base al nome. |

I modelli di immagine SVG supportano i seguenti valori segnaposto:

| Segnaposto | Descrizione |
|-|-|
| `{color}` | Colore primario. | 
| `{secondaryColor}` | Colore secondario. | 
| `{scale}` | L'immagine SVG viene convertita in un'immagine PNG quando viene aggiunta allo sprite dell'immagine della mappa. Questo segnaposto può essere utilizzato per ridimensionare un modello prima che venga convertito in modo da garantirne il rendering chiaro. | 
| `{text}` | Posizione in cui eseguire il rendering del testo quando viene usato con un marcatore HTML. |

L'esempio seguente illustra come prendere un modello SVG e aggiungerlo ad Azure Maps Web SDK come modello di icona riutilizzabile. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggiungere un modello di icona personalizzata allo spazio dei nomi Atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>aggiungere un modello di icona personalizzata allo spazio dei nomi Atlas</a> da mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Elenco di modelli di immagine

Questa tabella elenca tutti i modelli di immagine attualmente disponibili in Azure Maps Web SDK. Il nome del modello è superiore a ogni immagine. Per impostazione predefinita, il colore primario è blu e il colore secondario è bianco. Per semplificare la visualizzazione del colore secondario su uno sfondo bianco, le immagini seguenti hanno il colore secondario impostato su nero.

**Modelli icona simboli**

:::row:::
   :::column span="":::
      marker
   :::column-end:::
   :::column span="":::
      spessore indicatore
   :::column-end:::
   :::column span="":::
      cerchio indicatore
   :::column-end:::
   :::column span="":::
      indicatore-Flat
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona marcatore](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![icona spessore indicatore](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![icona del cerchio del marcatore](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![icona marcatore-Flat](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      quadrato marcatore
   :::column-end:::
   :::column span="":::
      marcatore-quadrato-cluster
   :::column-end:::
   :::column span="":::
      freccia marcatore
   :::column-end:::
   :::column span="":::
      indicatore-palla-pin
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona marcatore-quadrato](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![icona marcatore-quadrato-cluster](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![icona freccia marcatore](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![icona indicatore-palla-pin](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      marcatore-quadrato arrotondato
   :::column-end:::
   :::column span="":::
      marcatore-quadrato-arrotondato-cluster
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      flag-triangolo
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona marcatore-quadrato arrotondato](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![icona del cluster di marcatore-quadrato-arrotondato](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![icona contrassegno](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![Flag-icona triangolo](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      triangolo
   :::column-end:::
   :::column span="":::
      spessore triangolare
   :::column-end:::
   :::column span="":::
      triangolo-freccia su
   :::column-end:::
   :::column span="":::
      triangolo-freccia sinistra
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona a forma di triangolo](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![icona a forma di triangolo](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![triangolo-icona freccia su](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![icona a forma di triangolo e freccia sinistra](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      esagono
   :::column-end:::
   :::column span="":::
      spessore esagonale
   :::column-end:::
   :::column span="":::
      esagonale arrotondato
   :::column-end:::
   :::column span="":::
      esagono-arrotondato-spessore
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona esagonale](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![icona con spessore esagonale](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![icona esagonale arrotondata](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![icona di spessore esagonale-arrotondato](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      aggiungi
   :::column-end:::
   :::column span="":::
      pin-round
   :::column-end:::
   :::column span="":::
      arrotondato-quadrato
   :::column-end:::
   :::column span="":::
      arrotondato-spessore quadrato
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![Icona Aggiungi](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![icona pin-round](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![icona arrotondata-quadrata](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![icona arrotondata-con spessore rettangolare](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      freccia su
   :::column-end:::
   :::column span="":::
      freccia su-sottile
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona freccia su](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![freccia su-icona sottile](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![icona dell'auto](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**Modelli modello di riempimento poligono**

:::row:::
   :::column span="":::
      Checker
   :::column-end:::
   :::column span="":::
      controllo-ruotato
   :::column-end:::
   :::column span="":::
      cerchi
   :::column-end:::
   :::column span="":::
      Cerchi-spaziatura
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona di controllo](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![controllo-icona ruotata](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![icona cerchi](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![icona con spaziatura a cerchi](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      righe diagonali
   :::column-end:::
   :::column span="":::
      diagonali-linee-in basso
   :::column-end:::
   :::column span="":::
      strisce diagonali
   :::column-end:::
   :::column span="":::
      strisce diagonali-giù
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona linee diagonali](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![icona delle linee diagonali](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![icona con strisce diagonali](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![icona con strisce diagonali](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      linee griglia
   :::column-end:::
   :::column span="":::
      ruotato-griglie-linee
   :::column-end:::
   :::column span="":::
      ruotato-griglie-striping
   :::column-end:::
   :::column span="":::
      riempimento x
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona linee griglia](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![icona ruotata-griglia-linee](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![icona ruotata-griglia-Stripe](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![icona riempimento x](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      zig-zag
   :::column-end:::
   :::column span="":::
      zig-zag-verticale
   :::column-end:::
   :::column span="":::
      punti
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![icona zig-zag](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![icona zig-zag-Vertical](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![icona punti](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**Icone di immagine precaricate**

La mappa precarica un set di icone in Maps image sprite usando i `marker` `pin` modelli, e `pin-round` . Questi nomi di icone e i relativi valori di colore sono elencati nella tabella seguente.

| nome icona | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>Strumento prova ora

Con lo strumento seguente, è possibile eseguire il rendering dei diversi modelli di immagine incorporati in diversi modi e personalizzare i colori e la scala primari e secondari.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opzioni del modello icona" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere le <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Opzioni del modello icona</a> della penna di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [spazio dei nomi Atlas](/javascript/api/azure-maps-control/atlas#functions
)

Vedere gli articoli seguenti per altri esempi di codice in cui è possibile usare i modelli di immagine:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere marcatori HTML](map-add-bubble-layer.md)