---
title: Aggiungere un livello di simbolo ad Android Maps | Mappe Microsoft Azure
description: In questo articolo si apprenderà come eseguire il rendering dei dati di un punto su una mappa aggiungendovi un livello di simbolo usando il Android SDK Microsoft Azure maps.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b6b73fef368dc8dec5d8c042cdac1d3cea899450
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242441"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Aggiungere un livello di simbolo a una mappa usando Azure Maps Android SDK

Questo articolo illustra come eseguire il rendering dei dati punto da un'origine dati come livello di simbolo su una mappa usando Azure Maps Android SDK.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, è necessario installare [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) per caricare una mappa.

## <a name="add-a-symbol-layer"></a>Aggiungere un livello per i simboli

Per aggiungere un marcatore sulla mappa utilizzando il livello di simboli, attenersi alla procedura seguente:

1. Modificare **res**  >  il**layout** res  >  **activity_main.xml** in modo che abbia un aspetto simile al codice XML seguente:
    
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

2. Copiare il frammento di codice seguente nel metodo **OnCreate ()** della `MainActivity.java` classe.

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
    
    Il frammento di codice precedente ottiene innanzitutto un'istanza del controllo mappa di Azure Maps usando il metodo di callback **onReady ()** . Viene quindi creato un oggetto origine dati utilizzando la classe **DataSource** e viene aggiunto alla mappa. Viene quindi aggiunta una **funzionalità** contenente una geometria puntiforme. Un'immagine del marcatore rosso viene quindi impostata come icona per il simbolo. Un **livello di simbolo** usa il testo o le icone per eseguire il rendering dei dati basati su punti racchiusi nell'origine dati come simbolo sulla mappa. Viene quindi creato un livello di simboli e l'origine dati viene passata al rendering e viene quindi aggiunta ai livelli della mappa.
    
    Dopo aver aggiunto il frammento di codice precedente, il `MainActivity.java` dovrebbe essere simile a quello riportato di seguito:
    
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
    
A questo punto, se si esegue l'applicazione, viene visualizzato un marcatore sulla mappa, come illustrato di seguito:

<center>

![Puntina da disegno sulla mappa Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> Per impostazione predefinita, i livelli dei simboli ottimizzano il rendering dei simboli nascondendo i simboli che si sovrappongono. Quando si esegue lo zoom avanti, i simboli nascosti diventano visibili. Per disabilitare questa funzionalità ed eseguire il rendering di tutti i simboli in qualsiasi momento, impostare l' `iconAllowOverlap` opzione su `true` .

## <a name="next-steps"></a>Passaggi successivi

Per aggiungere altri elementi alla mappa, vedere:

> [!div class="nextstepaction"]
> [Aggiungere forme a una mappa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Visualizzare informazioni sulle funzionalità](display-feature-information-android.md)