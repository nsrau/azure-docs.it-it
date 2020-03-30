---
title: Aggiunta di un popup a un punto su una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come aggiungere un popup a un punto usando Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055957"
---
# <a name="add-a-popup-to-the-map"></a>Aggiungere un popup alla mappa

Questo articolo illustra come aggiungere un pop a un punto in una mappa.

## <a name="understand-the-code"></a>Informazioni sul codice

Il codice seguente aggiunge una `name` feature `description` punto, che ha e proprietà, alla mappa utilizzando un layer di simboli. Un'istanza della [classe Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) viene creata ma non visualizzata. Gli eventi del mouse vengono aggiunti al livello simbolo per attivare l'apertura e la chiusura del popup. Quando il simbolo del marcatore è `position` al passaggio del mouse, `content` la proprietà del popup viene `name` aggiornata con la posizione del marcatore e l'opzione viene aggiornata con alcuni HTML che mandaa a capo le proprietà e `description` della feature punto al passaggio del mouse. Il popup viene quindi visualizzato sulla `open` mappa utilizzando la sua funzione.

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

Di seguito è riportato l'esempio di codice in esecuzione completo della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un popup usando Mappe di Azure' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a> (Aggiungere un popup usando Mappe di Azure) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Riutilizzare un popup con più punti

Ci sono casi in cui l'approccio migliore consiste nel creare un popup e riutilizzarlo. Ad esempio, è possibile avere un numero elevato di punti e si desidera visualizzare un solo popup alla volta. Riutilizzando il popup, il numero di elementi DOM creati dall'applicazione è notevolmente ridotto, che può fornire prestazioni migliori. Nell'esempio seguente vengono create feature a 3 punti. Se si fa clic su una di esse, verrà visualizzato un popup con il contenuto di tale funzionalità punto.

<br/>

<iframe height='500' scrolling='no' title='Riutilizzo di popup con più segnaposto' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a> (Riutilizzo di popup con più segnaposto) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizzazione di un popup

Per impostazione predefinita, il popup ha uno sfondo bianco, una freccia del puntatore in basso e un pulsante di chiusura nell'angolo in alto a destra. L'esempio seguente cambia il colore `fillColor` di sfondo in nero utilizzando l'opzione del popup. Il pulsante chiudi viene `CloseButton` rimosso impostando l'opzione su false. Il contenuto HTML del popup utilizza riempito di 10 pixel dai bordi del popup. Il testo è reso bianco, quindi si presenta bene sullo sfondo nero.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup personalizzato" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>popup personalizzato</a> penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>da Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Aggiungere modelli popup alla mappa

I modelli popup semplificano la creazione di layout basati sui dati per i popup. Nelle sezioni seguenti viene illustrato l'utilizzo di vari modelli popup per generare contenuto formattato utilizzando le proprietà delle feature.

> [!NOTE]
> Per impostazione predefinita, tutto il contenuto di cui è stato eseguito il rendering utilizza il modello popup verrà in modalità sandbox all'interno di un iframe come funzionalità di sicurezza. Tuttavia, esistono limitazioni:However, there are limitations:
>
> - Tutti gli script, i moduli, il blocco del puntatore e la funzionalità di navigazione superiore sono disabilitati. Quando si fa clic su si fa clic, è possibile che i collegamenti si aprano in una nuova scheda. 
> - I browser meno recenti che `srcdoc` non supportano il parametro negli iframe saranno limitati al rendering di una piccola quantità di contenuto.
> 
> Se si considerano attendibili i dati caricati nei popup e potenzialmente si desidera che questi script caricati `sandboxContent` in popup siano in grado di accedere all'applicazione, è possibile disabilitarlo impostando l'opzione dei modelli popup su false. 

### <a name="string-template"></a>Modello di stringa

Il modello Stringa sostituisce i segnaposto con i valori delle proprietà della feature. Alle proprietà della funzionalità non è necessario assegnare un valore di tipo String. Ad esempio, `value1` contiene un numero intero. Questi valori vengono quindi passati `popupTemplate`alla proprietà content dell'oggetto . 

L'opzione `numberFormat` specifica il formato del numero da visualizzare. Se `numberFormat` l'oggetto non è specificato, il codice utilizzerà il formato data dei modelli popup. L'opzione `numberFormat` formatta i numeri utilizzando la funzione [Number.toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) . Per formattare numeri grandi, `numberFormat` è consigliabile utilizzare l'opzione con le funzioni [di NumberFormat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Ad esempio, il frammento di codice riportato di seguito viene utilizzato `maximumFractionDigits` per limitare il numero di cifre frazionarie a due.

> [!Note]
> C'è solo un modo in cui il modello String può eseguire il rendering delle immagini. In primo luogo, il modello String deve avere un tag di immagine in esso. Il valore passato al tag image deve essere un URL di un'immagine. Quindi, il modello String `isImage` deve avere `HyperLinkFormatOptions`impostato su true nel file . L'opzione `isImage` specifica che il collegamento ipertestuale è per un'immagine e il collegamento ipertestuale verrà caricato in un tag immagine. Quando si fa clic sul collegamento ipertestuale, l'immagine si aprirà.

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

Il modello PropertyInfo visualizza le proprietà disponibili della funzionalità. L'opzione `label` specifica il testo da visualizzare all'utente. Se `label` non viene specificato, verrà visualizzato il collegamento ipertestuale. E, se il collegamento ipertestuale è un'immagine, verrà visualizzato il valore assegnato al tag "alt". Specifica `dateFormat` il formato della data e, se il formato della data non è specificato, il rendering della data verrà eseguito come stringa. L'opzione `hyperlinkFormat` esegue il rendering dei `email` collegamenti selezionabili, in modo analogo, l'opzione può essere utilizzata per eseguire il rendering degli indirizzi e-mail selezionabili.

Prima che il PropertyInfo modello visualizzare le proprietà per l'utente finale, controlla in modo ricorsivo che le proprietà sono effettivamente definite per tale funzionalità. Ignora inoltre la visualizzazione delle proprietà di stile e titolo. Ad esempio, `color`non visualizzerà `size` `anchor`, `strokeOpacity`, `visibility`, , e . Pertanto, una volta completato il controllo del percorso della proprietà nel back-end, il modello PropertyInfo mostra il contenuto in un formato di tabella.

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

Una funzionalità può anche visualizzare il contenuto utilizzando una combinazione di String modello e il PropertyInfo modello. In questo caso, il modello String esegue il rendering dei valori dei segnaposto su uno sfondo bianco.  E, il PropertyInfo modello esegue il rendering di un'immagine a larghezza intera all'interno di una tabella. Le proprietà in questo esempio sono simili a quelle illustrate negli esempi precedenti.

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

Quando il modello Popup non è definito come un modello String, un modello PropertyInfo o una combinazione di entrambi, vengono utilizzate le impostazioni predefinite. Quando `title` l'oggetto e `description` sono le uniche proprietà assegnate, il modello popup mostra uno sfondo bianco, un pulsante di chiusura nell'angolo in alto a destra. E, su schermi piccoli e medi, mostra una freccia in basso. Le impostazioni predefinite vengono visualizzate all'interno `title` di `description`una tabella per tutte le proprietà diverse da e . Anche quando si esegue il fallback alle impostazioni predefinite, il modello popup può comunque essere modificato a livello di codice. Ad esempio, gli utenti possono disattivare il rilevamento dei collegamenti ipertestuali e le impostazioni predefinite verranno comunque applicate ad altre proprietà.

Fare clic sui punti sulla mappa nella CodePen. È presente un punto sulla mappa per ognuno dei modelli popup seguenti: modello stringa, modello PropertyInfo e modello di contenuto multiplo. Ci sono anche tre punti per mostrare come i modelli eseguono il rendering utilizzando le impostazioni predefinite.

<br/>

<iframe height='500' scrolling='no' title='PopupModelli' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> di<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Riutilizza modello popup

Analogamente al riutilizzo popup, è possibile riutilizzare i modelli popup. Questo approccio è utile quando si desidera visualizzare solo un modello popup alla volta, per più punti. Riutilizzando il modello popup, viene ridotto il numero di elementi DOM creati dall'applicazione, che migliora quindi le prestazioni dell'applicazione. L'esempio seguente usa lo stesso modello popup per tre punti. Se si fa clic su una di esse, verrà visualizzato un popup con il contenuto di tale funzionalità punto.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> di<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventi Popup

I popup possono essere aperti, chiusi e trascinati. La classe popup fornisce eventi che consentono agli sviluppatori di reagire a questi eventi. Nell'esempio seguente vengono evidenziati gli eventi che vengono attivati quando l'utente apre, chiude o trascina il popup. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventi Popup" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere gli <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eventi Popup</a> penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate (modello popup)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Vedere gli articoli seguenti che contengono esempi efficaci di codice completo:

> [!div class="nextstepaction"]
> [Aggiungere un livello simbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un indicatore HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)
