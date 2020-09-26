---
title: Aggiungere un popup a un punto su una mappa | Mappe Microsoft Azure
description: Informazioni sui popup, sui modelli popup e sugli eventi popup nelle mappe di Azure. Vedere come aggiungere un popup a un punto su una mappa e come riutilizzare e personalizzare i popup.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: a67fad5d0980730eb73f4219f12eaf3a7b9ec7dc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310476"
---
# <a name="add-a-popup-to-the-map"></a>Aggiungere un popup alla mappa

Questo articolo illustra come aggiungere un pop a un punto in una mappa.

## <a name="understand-the-code"></a>Informazioni sul codice

Il codice seguente aggiunge alla mappa una funzionalità punto, `name` con `description` proprietà e, usando un livello di simboli. Viene creata un'istanza della [classe popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) che non viene visualizzata. Gli eventi del mouse vengono aggiunti al livello del simbolo per attivare l'apertura e la chiusura del popup. Quando il simbolo del marcatore viene spostato, la proprietà del popup `position` viene aggiornata con la posizione del marcatore e l' `content` opzione viene aggiornata con un codice HTML che esegue il wrapping delle  `name` `description` proprietà e della funzionalità punto al passaggio del mouse. Il popup viene quindi visualizzato sulla mappa usando la relativa `open` funzione.

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

Di seguito è riportato l'esempio di codice completo e funzionante della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un popup usando Mappe di Azure' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a> (Aggiungere un popup usando Mappe di Azure) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Riutilizzare un popup con più punti

Esistono casi in cui l'approccio migliore consiste nel creare un popup e riutilizzarlo. Ad esempio, è possibile avere un numero elevato di punti e si desidera visualizzare solo un popup alla volta. Riutilizzando il popup, il numero di elementi DOM creati dall'applicazione è notevolmente ridotto, che può offrire prestazioni migliori. Nell'esempio seguente vengono create funzionalità a tre punti. Se si fa clic su una di esse, verrà visualizzato un popup con il contenuto di tale funzionalità punto.

<br/>

<iframe height='500' scrolling='no' title='Riutilizzo di popup con più segnaposto' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a> (Riutilizzo di popup con più segnaposto) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizzazione di un popup

Per impostazione predefinita, il popup presenta uno sfondo bianco, una freccia del puntatore nella parte inferiore e un pulsante Chiudi nell'angolo superiore destro. Nell'esempio seguente il colore di sfondo viene modificato in nero utilizzando l' `fillColor` opzione del popup. Il pulsante Chiudi viene rimosso impostando l' `CloseButton` opzione su false. Il contenuto HTML del popup utilizza un riempimento di 10 pixel dai bordi del popup. Il testo viene reso bianco, quindi viene visualizzato perfettamente sullo sfondo nero.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup personalizzato" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>popup personalizzato</a> della penna di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Aggiungere modelli popup alla mappa

I modelli popup semplificano la creazione di layout basati sui dati per i popup. Le sezioni seguenti illustrano l'uso di diversi modelli popup per generare contenuto formattato usando le proprietà delle funzionalità.

> [!NOTE]
> Per impostazione predefinita, tutti i contenuti di cui viene eseguito il rendering usano il modello popup in un iframe come funzionalità di sicurezza. Esistono tuttavia alcune limitazioni:
>
> - Tutti gli script, i moduli, il blocco del puntatore e la funzionalità di navigazione superiore sono disabilitati. Quando si fa clic sui collegamenti, è possibile aprirli in una nuova scheda. 
> - I browser meno recenti che non supportano il `srcdoc` parametro sugli iframe saranno limitati al rendering di una piccola quantità di contenuto.
> 
> Se si considera attendibili i dati caricati nei popup e potenzialmente si vuole che questi script caricati nei popup siano in grado di accedere all'applicazione, è possibile disabilitare questa impostazione impostando l'opzione Popup Templates `sandboxContent` su false. 

### <a name="string-template"></a>Modello stringa

Il modello di stringa sostituisce i segnaposto con i valori delle proprietà della funzionalità. Alle proprietà della funzionalità non è necessario assegnare un valore di tipo String. Ad esempio, `value1` include un numero intero. Questi valori vengono quindi passati alla proprietà Content dell'oggetto `popupTemplate` . 

L' `numberFormat` opzione specifica il formato del numero da visualizzare. Se `numberFormat` non è specificato, il codice userà il formato data dei modelli popup. L' `numberFormat` opzione Formatta i numeri usando la funzione [Number. toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) . Per formattare numeri elevati, provare a usare l' `numberFormat` opzione con funzioni di [NumberFormat. Format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Ad esempio, il frammento di codice seguente usa `maximumFractionDigits` per limitare il numero di cifre frazionarie a due.

> [!Note]
> Esiste solo un modo in cui il modello di stringa può eseguire il rendering delle immagini. Per prima cosa, il modello di stringa deve avere un tag di immagine. Il valore passato al tag image deve essere un URL di un'immagine. Quindi, il modello di stringa deve avere `isImage` impostato su true in `HyperLinkFormatOptions` . L' `isImage` opzione specifica che il collegamento ipertestuale è per un'immagine e il collegamento ipertestuale verrà caricato in un tag di immagine. Quando si fa clic sul collegamento ipertestuale, viene visualizzata l'immagine.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Modello PropertyInfo

Il modello PropertyInfo Visualizza le proprietà disponibili della funzionalità. L' `label` opzione specifica il testo da visualizzare all'utente. Se `label` non è specificato, verrà visualizzato il collegamento ipertestuale. Se il collegamento ipertestuale è un'immagine, verrà visualizzato il valore assegnato al tag "Alt". `dateFormat`Specifica il formato della data e, se il formato della data non è specificato, il rendering della data verrà eseguito sotto forma di stringa. L' `hyperlinkFormat` opzione esegue il rendering dei collegamenti selezionabili, in modo analogo, `email` è possibile usare l'opzione per eseguire il rendering degli indirizzi di posta elettronica selezionabili.

Prima che il modello PropertyInfo visualizzi le proprietà dell'utente finale, verifica in modo ricorsivo che le proprietà siano effettivamente definite per tale funzionalità. Ignora inoltre la visualizzazione delle proprietà di stile e titolo. Ad esempio, non verranno visualizzati `color` , `size` , `anchor` , `strokeOpacity` e `visibility` . Quindi, una volta completato il controllo del percorso della proprietà nel back-end, il modello PropertyInfo Visualizza il contenuto in un formato di tabella.

```javascript
var templateOptions = {
  content: [
    {
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
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Più modelli di contenuto

Una funzionalità può inoltre visualizzare contenuto utilizzando una combinazione del modello stringa e del modello PropertyInfo. In questo caso, il modello di stringa esegue il rendering dei valori segnaposto su uno sfondo bianco.  Il modello PropertyInfo esegue il rendering di un'immagine a larghezza intera all'interno di una tabella. Le proprietà in questo esempio sono simili alle proprietà illustrate negli esempi precedenti.

```javascript
var templateOptions = {
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
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Punti senza un modello definito

Quando il modello popup non è definito come un modello stringa, un modello PropertyInfo o una combinazione di entrambi, USA le impostazioni predefinite. Quando `title` e `description` sono le uniche proprietà assegnate, il modello popup mostra uno sfondo bianco, un pulsante Chiudi nell'angolo superiore destro. Nelle schermate piccole e medie viene visualizzata una freccia in basso. Le impostazioni predefinite vengono visualizzate all'interno di una tabella per tutte le proprietà diverse da `title` e `description` . Anche quando si esegue il fallback delle impostazioni predefinite, il modello popup può comunque essere modificato a livello di codice. Ad esempio, gli utenti possono disabilitare il rilevamento dei collegamenti ipertestuali e le impostazioni predefinite verranno comunque applicate ad altre proprietà.

Fare clic sui punti della mappa nella CodePen. È presente un punto sulla mappa per ognuno dei modelli popup seguenti: modello di stringa, modello PropertyInfo e modello di contenuto multiplo. Sono inoltre disponibili tre punti per illustrare il rendering dei modelli utilizzando le impostazioni predefinite.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Riutilizza modello popup

Analogamente al riutilizzo di popup, è possibile riutilizzare i modelli popup. Questo approccio è utile quando si desidera visualizzare solo un modello popup alla volta per più punti. Riutilizzando il modello popup, viene ridotto il numero di elementi DOM creati dall'applicazione, che consente di migliorare le prestazioni dell'applicazione. Nell'esempio seguente viene usato lo stesso modello popup per tre punti. Se si fa clic su una di esse, verrà visualizzato un popup con il contenuto di tale funzionalità punto.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventi popup

I popup possono essere aperti, chiusi e trascinati. La classe popup fornisce eventi per aiutare gli sviluppatori a rispondere a questi eventi. Nell'esempio seguente vengono evidenziati gli eventi generati quando l'utente apre, chiude o trascina il popup. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventi popup" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere gli <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eventi popup</a> della penna di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Vedere gli articoli seguenti che contengono esempi efficaci di codice completo:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un indicatore HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)
