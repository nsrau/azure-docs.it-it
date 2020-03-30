---
title: Aggiunta di un layer di sezioni alle mappe di Android Mappe di Microsoft Azure
description: In questo articolo imparerai a eseguire il rendering di un tile layer su una mappa usando Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335573"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Aggiungere un layer di riquadri a una mappa usando Azure Maps Android SDK

Questo articolo illustra come eseguire il rendering di un tile layer su una mappa usando Azure Maps Android SDK. I livelli riquadro consentono di sovrapporre immagini sopra i riquadri mappa di base in Mappe di Azure. Altre informazioni sul sistema di riquadri di Mappe di Azure sono reperibili nella documentazione [Livelli di Zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

Un layer Tile viene caricato in riquadri da un server. Queste immagini possono essere pre-renderizzate e archiviate come qualsiasi altra immagine su un server, usando una convenzione di denominazione che il livello di riquadro riconosce. In alternativa, è possibile eseguire il rendering di queste immagini con un servizio dinamico che genera le immagini quasi in tempo reale. Esistono tre diverse convenzioni di denominazione del servizio di tipo riquadro supportate dalla classe TileLayer di Azure:There are three different tile service namingconventions supported by Azure Maps TileLayer class:

* Notazione zoom di X, Y: in base al livello di zoom, x è la posizione nella colonna e y è la posizione nella riga del riquadro nella griglia dei riquadri.
* Notazione Quadkey: combinazione delle informazioni x, y e zoom in un singolo valore stringa che sia un identificatore univoco per un riquadro.
* Rettangolo di selezione: le coordinate del rettangolo di selezione possono essere utilizzate per specificare un'immagine nel formato `{west},{south},{east},{north}` comunemente utilizzata dai [ servizi Web di Mapping (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> TileLayer è un ottimo modo per visualizzare grandi set di dati sulla mappa. Non solo è possibile generare un livello riquadro da un'immagine, ma anche i dati vettoriali possono essere sottoposti a rendering come livello riquadro. Sottoponendo a rendering i dati vettoriali come livello riquadro, il controllo della mappa deve solo caricare i riquadri che possono essere molto più piccoli in termini di dimensione del file rispetto ai dati vettoriali che rappresentano. Molti utenti usano questa tecnica per eseguire il rendering di milioni di righe di dati sulla mappa.

L'URL di riquadro passato in un livello riquadro deve essere un URL http/https indirizzato a una risorsa TileJSON o un modello di URL di riquadro che usa i parametri seguenti: 

* `{x}` - posizione X del riquadro. Necessita inoltre di `{y}` e `{z}`.
* `{y}` - posizione Y del riquadro. Necessita inoltre di `{x}` e `{z}`.
* `{z}` - Livello di zoom del riquadro. Necessita inoltre di `{x}` e `{y}`.
* `{quadkey}` - Identificatore del riquadro quadkey basato sulla convenzione di denominazione del sistema di riquadri di Mappe di Bing.
* `{bbox-epsg-3857}` - Una stringa  del rettangolo delimitatore nel formato `{west},{south},{east},{north}` nel sistema di riferimento spaziale EPSG 3857.
* `{subdomain}`- Un segnaposto per i valori del sottodominio, se è specificato il valore del sottodominio.

## <a name="prerequisites"></a>Prerequisiti

Per completare il processo in questo articolo, è necessario installare [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) per caricare una mappa.


## <a name="add-a-tile-layer-to-the-map"></a>Aggiungere un layer di sezioni alla mappa

 In questo esempio viene illustrato come creare un layer di sezioni che punta a un set di riquadri. Queste tessere utilizzano il sistema di affiancamento "x, y, zoom". L'origine di questo livello riquadro è una sovrapposizione di radar meteo dall'[Iowa Environmental Mesonet dell'Iowa State University](https://mesonet.agron.iastate.edu/ogc/). 

È possibile aggiungere un layer tile alla mappa seguendo la procedura riportata di seguito.

1. Modificare **il layout > > activity_main.xml** in modo che sia simile a quello riportato di seguito:

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Copiare il frammento di codice seguente `MainActivity.java` nel metodo **onCreate()** della classe.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    Il frammento di codice precedente ottiene innanzitutto un'istanza del controllo mappa di Azure Maps usando il metodo di callback **onReady().** Viene quindi `TileLayer` creato un oggetto e viene eseguito `tileUrl` un URL di riquadro **xyz** formattato nell'opzione. L'opacità del livello `0.8` è impostata su e poiché i riquadri dal servizio di `tileSize` riquadri in uso sono riquadri di 256 pixel, queste informazioni vengono passate all'opzione. Il layer tile viene quindi passato in Gestione layer mappe.

    Dopo aver aggiunto il `MainActivity.java` frammento di codice precedente, l'aspetto dovrebbe essere simile a quello riportato di seguito:After adding the code snippet above, your should look like the one below:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
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

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
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

Se si esegue l'applicazione ora, si dovrebbe vedere una linea sulla mappa come illustrato di seguito:

<center>

![Linea della mappa Android](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Passaggi successivi

Vedere il seguente articolo per ulteriori informazioni su come impostare gli stili della mappa

> [!div class="nextstepaction"]
> [Modificare gli stili delle mappe nelle mappe Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)