---
title: Visualizzare le informazioni sulle funzionalità nell'Android SDK mappe di Azure | Mappe Microsoft Azure
description: Informazioni su come visualizzare le informazioni quando gli utenti interagiscono con le funzionalità della mappa. Usare il Android SDK mappe di Azure per visualizzare i messaggi di tipo avviso popup e altri tipi di messaggi.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 44c5f9f1a7c0d014d101ad45a80e1e53c42f69a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009036"
---
# <a name="display-feature-information"></a>Visualizzare informazioni sulle funzionalità

I dati spaziali vengono spesso rappresentati tramite punti, linee e poligoni. A questi dati sono spesso associate informazioni sui metadati. Un punto, ad esempio, può rappresentare la posizione di un negozio e i metadati relativi a tale ristorante possono essere il nome, l'indirizzo e il tipo di cibo che serve. Questi metadati possono essere aggiunti come proprietà di queste funzionalità usando un `JsonObject` . Il codice seguente crea una funzionalità di punto semplice con una `title` proprietà con valore "Hello World!"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Quando un utente interagisce con una funzionalità sulla mappa, gli eventi possono essere usati per rispondere a tali azioni. Uno scenario comune consiste nel visualizzare un messaggio composto dalle proprietà dei metadati di una funzionalità con cui l'utente ha eseguito l'interazione. L' `OnFeatureClick` evento è l'evento principale utilizzato per rilevare quando l'utente ha toccato una funzionalità sulla mappa. Esiste anche un `OnLongFeatureClick` evento. Quando si aggiunge l' `OnFeatureClick` evento alla mappa, può essere limitato a un singolo livello passando l'ID di un livello per limitarlo a. Se non viene passato alcun ID livello, toccando qualsiasi funzionalità sulla mappa, indipendentemente dal livello in cui si trova, questo evento verrà generato. Il codice seguente crea un livello di simboli per il rendering dei dati del punto sulla mappa, quindi aggiunge un `OnFeatureClick` evento e lo limita a questo livello di simbolo.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Visualizza un messaggio di avviso popup

Un messaggio di avviso popup è uno dei modi più semplici per visualizzare le informazioni all'utente ed è disponibile in tutte le versioni di Android. Non supporta alcun tipo di input dell'utente e viene visualizzato solo per un breve periodo di tempo. Se si desidera consentire all'utente di conoscere rapidamente gli elementi che hanno toccato, un messaggio di avviso popup potrebbe essere un'opzione appropriata. Nel codice seguente viene illustrato come è possibile utilizzare un messaggio di avviso popup con l' `OnFeatureClick` evento.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![Animazione di una funzionalità toccata e visualizzazione di un messaggio popup](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Oltre ai messaggi di avviso popup, esistono molti altri modi per presentare le proprietà dei metadati di una funzionalità, ad esempio:

- [Widget Snakbar](https://developer.android.com/training/snackbar/showing.html) -snackbars fornisce feedback leggero su un'operazione. Visualizzano un breve messaggio nella parte inferiore della schermata in dispositivi mobili e in basso a sinistra nei dispositivi più grandi. Snackbars vengono visualizzati sopra tutti gli altri elementi sullo schermo e ne può essere visualizzato solo uno alla volta.
- Finestre di [dialogo](https://developer.android.com/guide/topics/ui/dialogs) : una finestra di dialogo è una piccola finestra in cui viene richiesto all'utente di prendere una decisione o immettere informazioni aggiuntive. Una finestra di dialogo non riempie lo schermo e viene in genere usata per gli eventi modali che richiedono agli utenti di eseguire un'azione prima di poter continuare.
- Aggiungere un [frammento](https://developer.android.com/guide/components/fragments) all'attività corrente.
- Passare a un'altra attività o a una vista.

## <a name="next-steps"></a>Passaggi successivi

Per aggiungere altri dati alla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere forme a una mappa Android](how-to-add-shapes-to-android-map.md)
