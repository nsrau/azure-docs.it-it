---
title: Funzionalità degli stili delle mappe supportati in Mappe di Azure | Microsoft Docs
description: Informazioni sulle mappe di Azure stile la correlazione tra le funzionalità per Android SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870973"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Imposta lo stile della mappa usando Azure Maps Android SDK

Questo articolo illustra due modi per impostare gli stili mappa usando le mappe di Azure di Android SDK. Mappe di Azure ha sei mappe diversi stili tra cui scegliere. Per altre informazioni sugli stili mappa supportati, vedere [stili mappa è supportato nelle mappe di Azure](./supported-map-styles.md).


## <a name="prerequisites"></a>Prerequisiti

Per completare il processo in questo articolo, è necessario installare [Android SDK di Azure esegue il mapping](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) per caricare una mappa.


## <a name="set-map-style-in-the-layout"></a>Imposta lo stile della mappa nel layout

È possibile impostare uno stile della mappa nel file di layout per la classe di attività. Modificare **res > layout > activity_main**, in modo che risulti simile al seguente:

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

Il `mapcontrol_style` attributo precedente imposta lo stile di mappa **grayscale_dark**. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Imposta lo stile della mappa nella classe di attività

Nella classe di attività, è possibile impostare lo stile della mappa. Copiare il frammento di codice seguente nel **onCreate()** metodo le `MainActivity.java` classe. Si imposterà lo stile della mappa **satellite_road_labels**.

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>