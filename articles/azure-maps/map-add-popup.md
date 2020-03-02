---
title: Aggiungere un popup a un punto su una mappa | Mappe Microsoft Azure
description: In questo articolo si apprenderà come aggiungere un popup a un punto usando il Microsoft Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 588de08666930937c3ad965b2609f8e207b75eca
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208849"
---
# <a name="add-a-popup-to-the-map"></a>Aggiungere un popup alla mappa

Questo articolo illustra come aggiungere un pop a un punto in una mappa.

## <a name="understand-the-code"></a>Informazioni sul codice

Il codice seguente aggiunge alla mappa una funzionalità di punto, che ha `name` e `description` proprietà, usando un livello di simboli. Viene creata un'istanza della [classe popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) che non viene visualizzata. Gli eventi del mouse vengono aggiunti al livello del simbolo per attivare l'apertura e la chiusura del popup. Quando il simbolo del marcatore viene posizionato al passaggio del mouse, la proprietà `position` del popup viene aggiornata con la posizione del marcatore e l'opzione `content` viene aggiornata con un codice HTML che esegue il wrapping del `name` e `description` le proprietà della funzionalità punto al passaggio del mouse. Il popup viene quindi visualizzato sulla mappa utilizzando la relativa funzione `open`.

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

<iframe height='500' scrolling='no' title='Aggiungere un popup usando Mappe di Azure' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a> (Aggiungere un popup usando Mappe di Azure) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Riutilizzare un popup con più punti

Esistono casi in cui l'approccio migliore consiste nel creare un popup e riutilizzarlo. Ad esempio, è possibile avere un numero elevato di punti e si desidera visualizzare solo un popup alla volta. Riutilizzando il popup, il numero di elementi DOM creati dall'applicazione è notevolmente ridotto, che può offrire prestazioni migliori. Nell'esempio seguente vengono create funzionalità a tre punti. Se si fa clic su una di esse, verrà visualizzato un popup con il contenuto di tale funzionalità punto.

<br/>

<iframe height='500' scrolling='no' title='Riutilizzo di popup con più segnaposto' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a> (Riutilizzo di popup con più segnaposto) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizzazione di un popup

Per impostazione predefinita, il popup presenta uno sfondo bianco, una freccia del puntatore nella parte inferiore e un pulsante Chiudi nell'angolo superiore destro. Nell'esempio seguente il colore di sfondo viene modificato in nero utilizzando l'opzione `fillColor` del popup. Il pulsante Chiudi viene rimosso impostando l'opzione `CloseButton` su false. Il contenuto HTML del popup utilizza un riempimento di 10 pixel dai bordi del popup. Il testo viene reso bianco, quindi viene visualizzato perfettamente sullo sfondo nero.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup personalizzato" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>popup personalizzato</a> della penna da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Aggiungere modelli popup alla mappa

I modelli popup semplificano la creazione di layout basati sui dati per i popup. Le sezioni seguenti illustrano l'uso di diversi modelli popup per generare contenuto formattato usando le proprietà delle funzionalità.

### <a name="string-template"></a>Modello stringa

Il modello di stringa sostituisce i segnaposto con i valori delle proprietà della funzionalità. Alle proprietà della funzionalità non è necessario assegnare un valore di tipo String. Ad esempio, `value1` include un numero intero. Questi valori vengono quindi passati alla proprietà Content del `popupTemplate`. 

L'opzione `numberFormat` specifica il formato del numero da visualizzare. Se il `numberFormat` non è specificato, il codice userà il formato data dei modelli popup. L'opzione `numberFormat` formatta i numeri con la funzione [Number. toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) . Per formattare numeri elevati, provare a usare l'opzione `numberFormat` con le funzioni di [NumberFormat. Format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Ad esempio, il frammento di codice seguente usa `maximumFractionDigits` per limitare il numero di cifre frazionarie a due.

> [!Note]
> Esiste solo un modo in cui il modello di stringa può eseguire il rendering delle immagini. Per prima cosa, il modello di stringa deve avere un tag di immagine. Il valore passato al tag image deve essere un URL di un'immagine. Il modello di stringa deve quindi avere `isImage` impostato su true nel `HyperLinkFormatOptions`. L'opzione `isImage` specifica che il collegamento ipertestuale è per un'immagine e il collegamento ipertestuale verrà caricato in un tag di immagine. Quando si fa clic sul collegamento ipertestuale, viene visualizzata l'immagine.

```javascript
new atlas.data.Feature(new atlas.data.Point([-20, -20]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    popupTemplate: {
        content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
        numberFormat: {
            maximumFractionDigits: 2
        }
    }
}),
```

### <a name="propertyinfo-template"></a>Modello PropertyInfo

Il modello PropertyInfo Visualizza le proprietà disponibili della funzionalità. L'opzione `label` specifica il testo da visualizzare all'utente. Se `label` non è specificato, verrà visualizzato il collegamento ipertestuale. Se il collegamento ipertestuale è un'immagine, verrà visualizzato il valore assegnato al tag "Alt". Il `dateFormat` specifica il formato della data e, se il formato della data non è specificato, il rendering della data verrà eseguito sotto forma di stringa. L'opzione `hyperlinkFormat` esegue il rendering dei collegamenti selezionabili, in modo analogo, è possibile usare l'opzione `email` per eseguire il rendering degli indirizzi di posta elettronica selezionabili.

Prima che il modello PropertyInfo visualizzi le proprietà dell'utente finale, verifica in modo ricorsivo che le proprietà siano effettivamente definite per tale funzionalità. Ignora inoltre la visualizzazione delle proprietà di stile e titolo. Ad esempio, non verranno visualizzati `color`, `size`, `anchor`, `strokeOpacity`e `visibility`. Quindi, una volta completato il controllo del percorso della proprietà nel back-end, il modello PropertyInfo Visualizza il contenuto in un formato di tabella.

```javascript
new atlas.data.Feature(new atlas.data.Point([20, -20]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com',
    popupTemplate: {
        content: [{
    propertyPath: 'createDate',
    label: 'Created Date'
    },
    {
    propertyPath: 'dateNumber',
    label: 'Formatted date from number',
    dateFormat: {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        timeZone: 'UTC',
        timeZoneName: 'short'
    }
    },
    {
    propertyPath: 'url',
    label: 'Code samples',
    hideLabel: true,
    hyperlinkFormat: {
        lable: 'Go to code samples!',
        target: '_blank'
    }
    },
    {
    propertyPath: 'email',
    label: 'Email us',
    hideLabel: true,
    hyperlinkFormat: {
        target: '_blank',
        scheme: 'mailto:'
        }
    }
        ]
    }
}),

```

### <a name="multiple-content-templates"></a>Più modelli di contenuto

Una funzionalità può inoltre visualizzare contenuto utilizzando una combinazione del modello stringa e del modello PropertyInfo. In questo caso, il modello di stringa esegue il rendering dei valori segnaposto su uno sfondo bianco.  Il modello PropertyInfo esegue il rendering di un'immagine a larghezza intera all'interno di una tabella. Le proprietà in questo esempio sono simili alle proprietà illustrate negli esempi precedenti.

```javascript
new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg',
    popupTemplate: {
    content: [
      'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
      [{
        propertyPath: 'imageLink',
        label: 'Image',
        hideImageLabel: true,
        hyperlinkFormat: {
          isImage: true
        }
      }]
    ],
    numberFormat: {
      maximumFractionDigits: 2
    }
    }
    }),
]);
```

### <a name="points-without-a-defined-template"></a>Punti senza un modello definito

Quando il modello popup non è definito come un modello stringa, un modello PropertyInfo o una combinazione di entrambi, USA le impostazioni predefinite. Quando il `title` e `description` sono le uniche proprietà assegnate, il modello popup mostra uno sfondo bianco, un pulsante Chiudi nell'angolo superiore destro. Nelle schermate piccole e medie viene visualizzata una freccia in basso. Le impostazioni predefinite vengono visualizzate all'interno di una tabella per tutte le proprietà diverse da `title` e `description`. Anche quando si esegue il fallback delle impostazioni predefinite, il modello popup può comunque essere modificato a livello di codice. Ad esempio, gli utenti possono disabilitare il rilevamento dei collegamenti ipertestuali e le impostazioni predefinite verranno comunque applicate ad altre proprietà.

Fare clic sui punti della mappa nella CodePen. È presente un punto sulla mappa per ognuno dei modelli popup seguenti: modello di stringa, modello PropertyInfo e modello di contenuto multiplo. Sono inoltre disponibili tre punti per illustrare il rendering dei modelli utilizzando le impostazioni predefinite.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Riutilizza modello popup

Analogamente al riutilizzo di popup, è possibile riutilizzare i modelli popup. Questo approccio è utile quando si desidera visualizzare solo un modello popup alla volta per più punti. Riutilizzando il modello popup, viene ridotto il numero di elementi DOM creati dall'applicazione, che consente di migliorare le prestazioni dell'applicazione. Nell'esempio seguente viene usato lo stesso modello popup per tre punti. Se si fa clic su una di esse, verrà visualizzato un popup con il contenuto di tale funzionalità punto.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventi popup

I popup possono essere aperti, chiusi e trascinati. La classe popup fornisce eventi per aiutare gli sviluppatori a rispondere a questi eventi. Nell'esempio seguente vengono evidenziati gli eventi generati quando l'utente apre, chiude o trascina il popup. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventi popup" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere gli <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eventi popup</a> della penna di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
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
