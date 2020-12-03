---
title: Impostare uno stile mappa usando mappe di Azure Android SDK
description: Vengono illustrati due modi per impostare lo stile di una mappa. Per modificare lo stile, vedere How to use the Microsoft Azure Maps Android SDK nel file di layout o nella classe Activity.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532482"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Impostare lo stile della mappa usando le mappe di Azure Android SDK

Questo articolo illustra come impostare gli stili della mappa usando le mappe di Azure Android SDK. Mappe di Azure include sei stili di mappe diversi tra cui scegliere. Per altre informazioni sugli stili di mappa supportati, vedere [stili di mappa supportati in mappe di Azure](./supported-map-styles.md).

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un account Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione
3. Scaricare e installare il [Android SDK mappe di Azure](./how-to-use-android-map-control-library.md).


## <a name="set-map-style-in-the-layout"></a>Impostare lo stile della mappa nel layout

È possibile impostare uno stile mappa nel file di layout per la classe Activity. Modificare `res > layout > activity_main.xml` , quindi ha un aspetto simile al seguente:

```XML
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

L' `mapcontrol_style` attributo precedente imposta lo stile della mappa su **grayscale_dark**.

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Mappe di Azure, immagine mappa che mostra lo stile grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Impostare lo stile della mappa nella classe MainActivity

Lo stile della mappa può essere impostato anche nella classe MainActivity. Aprire il `java > com.example.myapplication > MainActivity.java` file e copiare il frammento di codice seguente nel metodo **OnCreate ()** . Questo codice imposta lo stile della mappa su **satellite_road_labels**.

>[!WARNING]
>È possibile che Android Studio non abbia importato le classi obbligatorie.  Di conseguenza, il codice avrà alcuni riferimenti a irrisolvibile. Per importare le classi obbligatorie, è sufficiente passare il puntatore del mouse su ogni riferimento non risolto e premere `Alt + Enter` (opzione + Restituisci su un Mac).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Mappe di Azure, immagine mappa che mostra lo stile satellite_road_labels":::