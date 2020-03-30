---
title: Visualizzare le informazioni sulle funzionalità in Azure Maps Android SDK . Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come visualizzare le informazioni sulle funzionalità in una mappa usando Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911683"
---
# <a name="display-feature-information"></a>Visualizzare informazioni sulle funzionalità

I dati spaziali vengono spesso rappresentati utilizzando punti, linee e poligoni. A questi dati sono spesso associate informazioni sui metadati. Ad esempio, un punto può rappresentare la posizione di un negozio e i metadati relativi a quel ristorante possono essere il nome, l'indirizzo e il tipo di cibo che serve. Questi metadati possono essere aggiunti come `JsonObject`proprietà di queste funzionalità utilizzando un oggetto . Il codice seguente crea una `title` funzionalità punto semplice con una proprietà che ha un valore di "Hello World!"

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

Quando un utente interagisce con una feature sulla mappa, gli eventi possono essere utilizzati per reagire a tali azioni. Uno scenario comune consiste nel visualizzare un messaggio costituito dalle proprietà dei metadati di una funzionalità con cui l'utente ha interagito. L'evento `OnFeatureClick` è l'evento principale utilizzato per rilevare quando l'utente ha toccato una feature sulla mappa. C'è anche `OnLongFeatureClick` un evento. Quando si `OnFeatureClick` aggiunge l'evento alla mappa, può essere limitato a un singolo layer passando l'ID di un layer a cui limitarlo. Se non viene passato alcun ID layer, toccando qualsiasi feature sulla mappa, indipendentemente dal layer in cui si trova, questo evento verrà generato. Il codice seguente crea un layer di simboli per `OnFeatureClick` eseguire il rendering dei dati punto sulla mappa, quindi aggiunge un evento e lo limita a questo layer di simboli.

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

## <a name="display-a-toast-message"></a>Visualizzare un messaggio di tipo avviso popup

Un messaggio di tipo avviso popup è uno dei modi più semplici per visualizzare informazioni per l'utente ed è disponibile in tutte le versioni di Android.An toast message is one of the easiest way to display information to the user and is available in all versions of Android. Non supporta alcun tipo di input dell'utente e viene visualizzato solo per un breve periodo di tempo. Se vuoi informare rapidamente l'utente su ciò che ha toccato, un messaggio di tipo avviso popup potrebbe essere una buona opzione. Il codice seguente mostra come usare un `OnFeatureClick` messaggio di tipo avviso popup con l'evento.

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

![Animazione di una funzionalità toccata e di un messaggio di tipo avviso popup visualizzato](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Oltre ai messaggi di tipo avviso popup, esistono molti altri modi per presentare le proprietà dei metadati di una funzionalità, ad esempio:In addition to toast messages, There are many other ways to present the metadata properties of a feature, such as:

- [Widget Snakbar](https://developer.android.com/training/snackbar/showing.html) - Snackbars fornisce un feedback leggero su un'operazione. Mostrano un breve messaggio nella parte inferiore dello schermo su dispositivi mobili e in basso a sinistra sui dispositivi più grandi. Gli Snackbar vengono visualizzati sopra tutti gli altri elementi sullo schermo e solo uno alla volta.
- [Finestre di dialogo](https://developer.android.com/guide/topics/ui/dialogs) - Una finestra di dialogo è una piccola finestra che richiede all'utente di prendere una decisione o immettere informazioni aggiuntive. Una finestra di dialogo non riempie lo schermo e viene normalmente utilizzata per gli eventi modali che richiedono agli utenti di eseguire un'azione prima di poter continuare.
- Aggiungere un [frammento](https://developer.android.com/guide/components/fragments) all'attività corrente.
- Passare a un'altra attività o visualizzazione.

## <a name="next-steps"></a>Passaggi successivi

Per aggiungere altri dati alla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello simbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere forme a una mappa Android](how-to-add-shapes-to-android-map.md)
