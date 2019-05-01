---
title: Aggiungere un livello di simboli per Android viene eseguito il mapping nelle mappe di Azure | Microsoft Docs
description: Come aggiungere simboli di una mappa utilizzando Azure Maps Android SDK
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64871078"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Aggiungere un livello di simboli per una mappa utilizzando Azure Maps Android SDK

Questo articolo illustra come eseguire il rendering di punto dati da un'origine dati come un livello di simboli in una mappa con le mappe di Azure di Android SDK.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo completamente, è necessario installare [Android SDK di Azure esegue il mapping](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) per caricare una mappa.

## <a name="add-a-symbol-layer"></a>Aggiungere un livello per i simboli

Per aggiungere un marcatore sulla mappa usando il livello di simboli, seguire questa procedura:

1. Modificare **res** > **layout** > **activity_main** in modo analogo il codice XML seguente:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Copiare il frammento di codice seguente nel **onCreate()** metodo le `MainActivity.java` classe.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Il frammento di codice precedente Ottiene prima di tutto un mappe di Azure mappa controllo istanza utilizzando il **onReady()** metodo di callback. Viene quindi creato un oggetto origine dati usando il **DataSource** classe e lo aggiunge alla mappa. Viene quindi aggiunta un' **funzionalità** contenente una punto di geometria a esso. Un'immagine del marcatore di colore rosso viene quindi impostata come icona per il simbolo. Oggetto **layer simbolo** Usa testo o icone per il rendering dei dati in base al punto di stato eseguito il wrapping nell'origine dati come simbolo sulla mappa. Viene quindi creato un livello di simboli e origine dati viene passato al metodo per eseguire il rendering e quindi viene aggiunto ai livelli della mappa.
    
    Dopo aver aggiunto il frammento di codice precedente, il `MainActivity.java` dovrebbe essere simile al seguente:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```
    
A questo punto, se si esegue l'applicazione si verrà visualizzato un marcatore sulla mappa, come illustrato di seguito:

<center>

![Aggiungi mappa Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Passaggi successivi

Per aggiungere altre opzioni per la mappa, vedere:

> [!div class="nextstepaction"]
> [Aggiungere forme a una mappa di Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)