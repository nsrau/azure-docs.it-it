---
title: Come usare controllo mappa di Android in Mappe di Azure | Microsoft Docs
description: Usare il controllo mappa di Android in Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e3f7579324e1218cc2e2c3594889db776da6e529
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119142"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Come usare Android SDK per Mappe di Azure

Android SDK per Mappe di Azure è una raccolta di mappe in formato vettoriale per Android. Questo articolo illustra il processo per installare Android SDK per Mappe di Azure, caricare una mappa e collocare su di essa una puntina da disegno.

## <a name="prerequisites-to-get-started"></a>Prerequisiti per iniziare

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure 

Per seguire le procedure in questa guida è prima necessario vedere [Gestire l'account e le chiavi](how-to-manage-account-keys.md) per creare e gestire la sottoscrizione dell'account con il piano tariffario S1.

### <a name="download-android-studio"></a>Scaricare Android Studio

È possibile scaricare [Android Studio](https://developer.android.com/studio/) gratuitamente da Google. Per installare Android SDK per Mappe di Azure, è necessario innanzitutto scaricare Android Studio e creare un progetto con un'attività vuota.

## <a name="create-a-project-in-android-studio"></a>Creare un progetto in Android Studio

È necessario creare un nuovo progetto con un'attività vuota. Seguire questa procedura per creare un nuovo progetto di Android Studio:

1. In *Choose your project* (Scegliere il progetto) selezionare "Phone and Tablet" (Telefono e tablet) come fattore di forma su cui verrà eseguita l'applicazione.
2. Sotto il fattore di forma selezionare *Empty Activity* (Attività vuota) e fare clic su **Next** (Avanti).
3. In *Configure your project* (Configurare il progetto) selezionare `API 21: Android 5.0.0 (Lollipop)` come SDK minimo. Questa è la versione minima supportata da Android SDK per Mappe di Azure.
4. Accettare i valori `Activity Name` e `Layout Name` predefiniti e fare clic su **Finish** (Fine)

Vedere la [documentazione di Android Studio](https://developer.android.com/studio/intro/) per altre informazioni sull'installazione di Android Studio e la creazione di un nuovo progetto.

![creare un nuovo progetto](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurare un dispositivo virtuale

Android Studio consente di configurare un dispositivo Android virtuale nel computer. Questo è utile per testare l'applicazione durante lo sviluppo. Per configurare un dispositivo virtuale fare clic sull'icona del gestore di dispositivi virtuali Android, AVD Manager, in alto a destra nello schermo del progetto. Quindi, fare clic sul pulsante **Create Virtual Device** (Crea dispositivo virtuale). È possibile accedere al gestore anche tramite Tools (Strumenti) > Android > AVD Manager sulla barra degli strumenti. Nella categoria **Phones** (Telefoni) selezionare **Nexus 5X** e fare clic su **Next** (Avanti).

Per altre informazioni sulla configurazione di un dispositivo virtuale Android, vedere la [documentazione di Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulatore Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Installare Android SDK per Mappe di Azure

Prima di procedere con la compilazione dell'applicazione, seguire questa procedura per installare Android SDK per Mappe di Azure. 

1. Aggiungere quanto segue al blocco di repository **allprojects** nel file **build.gradle**.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aggiornare **app/build.gradle** e aggiungere quanto segue:

    1. Aggiungere quanto segue al blocco Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Aggiornare il blocco delle dipendenze e aggiungere quanto segue:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Impostare le autorizzazioni aggiungendo quanto segue al file AndroidManifest.xml

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Modificare **res > layout > activity_main** in modo che abbia un aspetto simile al codice XML seguente:
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Modificare **MainActivity. Java** per creare una classe di attività di visualizzazione mappa. Dopo la modifica, l'aspetto della classe dovrebbe essere simile al seguente:

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

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

## <a name="import-classes"></a>Importare le classi

Dopo aver completato i passaggi precedenti, probabilmente si riceveranno avvisi da Android Studio relativi a parti del testo nel codice. Per risolvere il problema è necessario importare alcune delle classi a cui viene fatto riferimento in `MainActivity.java`.

È possibile importare automaticamente queste classi premendo `Alt`+`Enter`(`Option`+`Return` su Mac). 

Fare clic sul pulsante **Run 'App'** (Esegui 'app') (o premere `Control`+`R` su Mac) per compilare l'applicazione.

![Fare clic su Run (Esegui)](./media/how-to-use-android-map-control-library/run-app.png)

Per la compilazione dell'applicazione in Android Studio occorreranno pochi secondi. Al termine della compilazione è possibile testare l'applicazione nel dispositivo Android emulato. Si vedrà una mappa simile alla seguente.

![Mappa Android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Aggiungere un marcatore alla mappa

Per aggiungere un marcatore alla mappa, aggiungere la funzione `mapView.getMapAsync()` a `MainActivity.java`. L'aspetto finale di `MainActivity.java` dovrebbe essere simile al seguente:

```java
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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
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

Eseguire nuovamente l'applicazione. Sulla mappa dovrebbe essere visualizzato un marcatore simile al seguente.

![Puntina da disegno sulla mappa Android](./media/how-to-use-android-map-control-library/android-map-pin.png)