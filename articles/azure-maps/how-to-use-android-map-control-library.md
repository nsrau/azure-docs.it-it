---
title: Come usare il controllo mappa Android nelle mappe di Azure | Microsoft Docs
description: Il controllo mappa Android nelle mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770376"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Come usare le mappe di Azure di Android SDK

Le mappe di Azure di Android SDK è una libreria di mappe vettoriali per Android. Questo articolo illustra le procedure di installazione di Android SDK mappe di Azure, il caricamento di una mappa e l'inserimento di un pin sulla mappa.

## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

Per completare le procedure descritte in questo articolo, è necessario innanzitutto [creare un account mappe di Azure](how-to-manage-account-keys.md) nel piano tariffario S1.

### <a name="download-android-studio"></a>Scarica Android Studio

È necessario scaricare Android Studio e creare un progetto con un'attività vuota prima di installare il SDK Android mappe di Azure. È possibile [scaricare Android Studio](https://developer.android.com/studio/) gratuita da Google. 

## <a name="create-a-project-in-android-studio"></a>Creare un progetto in Android Studio

In primo luogo, è necessario creare un nuovo progetto con un'attività vuota. Completare questi passaggi per creare un progetto Android Studio:

1. Sotto **scegliere il progetto**, selezionare **telefoni e Tablet**. L'applicazione verrà eseguita in questo fattore di forma.
2. Nel **telefoni e Tablet** scheda, seleziona **attività vuota**e quindi selezionare **Next**.
3. In **Configure your project** (Configurare il progetto) selezionare `API 21: Android 5.0.0 (Lollipop)` come SDK minimo. Questa è la versione meno recente supportata da Azure Maps Android SDK.
4. Accettare il valore predefinito `Activity Name` e `Layout Name` e selezionare **fine**.

Vedere le [documentazione di Android Studio](https://developer.android.com/studio/intro/) per altre informazioni della Guida all'installazione di Android Studio e crea un nuovo progetto.

![Creare un progetto](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurare un dispositivo virtuale

Android Studio consente di configurare un dispositivo Android virtuale nel computer. In questo modo consentono di testare l'applicazione durante lo sviluppo. Per configurare un dispositivo virtuale, selezionare l'icona di Android Virtual Device (AVD) Manager nell'angolo superiore destro dello schermo del progetto e quindi selezionare **crea dispositivo virtuale**. È anche possibile ottenere da AVD Manager selezionando **degli strumenti** > **Android** > **AVD Manager** nella barra degli strumenti. Nel **telefoni** categoria, seleziona **Nexus 5 X**, quindi selezionare **Avanti**.

Altre informazioni sulla configurazione di un AVD nel [documentazione di Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulatore Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installare il SDK Android di mappe di Azure

Il passaggio successivo di compilazione dell'applicazione consiste nell'installare il SDK Android mappe di Azure. Completare questi passaggi per installare il SDK:

1. Aggiungere il codice seguente per il **tutti i progetti**, **repository** blocco nel **Build. gradle** file.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aggiornamento di **gradle** e aggiungervi il codice seguente:

    1. Aggiungere il codice seguente al blocco di Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Aggiornare il blocco delle dipendenze e aggiungervi il codice seguente:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Impostare le autorizzazioni aggiungendo il seguente codice XML per il **androidmanifest. XML** file:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Modificare **res** > **layout** > **activity_main** in modo analogo questo codice XML:
    
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

5. Modificare **MainActivity. Java** per creare una classe di attività di visualizzazione mappa. Dopo la modifica, sarà simile a questa classe:

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
        
        static {
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

Dopo aver completato i passaggi precedenti, probabilmente potrai avvisi da Android Studio sulla parte del codice. Per risolvere questi avvisi, importare le classi a cui fa riferimento `MainActivity.java`.

È possibile importare automaticamente queste classi selezionando Alt + Invio (opzione + ritorno su un Mac).

Selezionare il pulsante di esecuzione, come illustrato nel seguente grafico (o premere CTRL + R in un computer Mac), per compilare l'applicazione.

![Fare clic su Run (Esegui)](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio richiederà alcuni secondi per compilare l'applicazione. Dopo aver completata la compilazione, è possibile testare l'applicazione nel dispositivo Android emulato. Verrà visualizzata una mappa simile alla seguente:

![Mappa Android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Aggiungere un marcatore alla mappa

Per aggiungere un marcatore a una mappa, aggiungere il `mapView.getMapAsync()` funzione `MainActivity.java`. L'elemento finale `MainActivity.java` codice dovrebbe essere simile al seguente:

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

Eseguire nuovamente l'applicazione. Verrà visualizzato un marcatore sulla mappa, come illustrato di seguito:

![Puntina da disegno sulla mappa Android](./media/how-to-use-android-map-control-library/android-map-pin.png)