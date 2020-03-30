---
title: Aggiunta di un layer di simboli alle mappe Di Android Mappe di Microsoft Azure
description: In questo articolo imparerai a eseguire il rendering dei dati punto su una mappa aggiungendovi un layer di simboli usando Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0eca73b5acae715283c05125181e12729ed5a772
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335580"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Aggiungere un layer di simboli a una mappa usando Azure Maps Android SDKAdd a symbol layer to a map using Azure Maps Android SDK

Questo articolo illustra come eseguire il rendering dei dati punto da un'origine dati come layer di simboli su una mappa usando Android SDK di Azure Maps.This article shows you how to render point data from a data source as a symbol layer on a map using the Azure Maps Android SDK.

## <a name="prerequisites"></a>Prerequisiti

Per seguire completamente i passaggi descritti in questo articolo, è necessario installare [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) per caricare una mappa.

## <a name="add-a-symbol-layer"></a>Aggiungere un livello per i simboli

Per aggiungere un marcatore sulla mappa utilizzando il layer simbolo, attenersi alla seguente procedura:

1. Modificare il > **layout** > **activity_main.xml** in modo che sia simile al codice XML seguente: **res**
    
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

2. Copiare il frammento di codice seguente `MainActivity.java` nel metodo **onCreate()** della classe.

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
    
    Il frammento di codice precedente ottiene innanzitutto un'istanza del controllo mappa di Azure Maps usando il metodo di callback **onReady().** Viene quindi creato un oggetto origine dati utilizzando il **DataSource** classe e lo aggiunge alla mappa. Aggiunge quindi una **feature** contenente una geometria Point. Un'immagine indicatore rosso viene quindi impostata come icona per il simbolo. Un **layer di simboli** utilizza testo o icone per eseguire il rendering di dati basati su punti di cui è stato eseguito il wrapping nell'origine dati come simbolo sulla mappa. Viene quindi creato un layer di simboli e l'origine dati viene passata per il rendering e quindi aggiunta ai layer della mappa.
    
    Dopo aver aggiunto il `MainActivity.java` frammento di codice precedente, l'aspetto dovrebbe essere simile a quello riportato di seguito:After adding the code snippet above, your should look like the one below:
    
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
    
A questo punto, se si esegue l'applicazione si dovrebbe vedere un marcatore sulla mappa, come illustrato di seguito:At this point, if you run your application you should see a marker on the map, as shown here:

<center>

![Puntina da disegno sulla mappa Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Passaggi successivi

Per aggiungere altri elementi alla mappa, vedere:

> [!div class="nextstepaction"]
> [Aggiungere forme a una mappa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Visualizzare informazioni sulle funzionalità](display-feature-information-android.md)