---
title: Introduzione ad Android SDK per Mappe di Azure
description: Acquisire familiarità con le mappe di Microsoft Azure Android SDK. Vedere come creare un progetto in Android Studio, installare l'SDK e creare una mappa interattiva.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531262"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introduzione ad Android SDK per Mappe di Azure

Android SDK per Mappe di Azure è una raccolta di mappe in formato vettoriale per Android. Questo articolo illustra i processi di installazione di Android SDK per Mappe di Microsoft Azure e caricamento di una mappa.

## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

1. [Creare un account Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione
Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).
3. [Scaricare e installare Android Studio di Google](https://developer.android.com/studio/).

## <a name="create-a-project-in-android-studio"></a>Creare un progetto in Android Studio

Completare questi passaggi per creare un progetto di Android Studio:

1. Avviare Android Studio.
2. Fare clic su **+ Crea nuovo progetto**.
3. Nella scheda **telefono e Tablet** fare clic su **attività vuota**. Fare clic su **Avanti**.
4. In **Configure your project** (Configurare il progetto) selezionare `API 21: Android 5.0.0 (Lollipop)` come SDK minimo.
5. Selezionare `Java` come lingua.
6. Accettare l'impostazione predefinita `Name` per il progetto. Fare clic su **Fine**.

Vedere la [documentazione di Android Studio](https://developer.android.com/studio/intro/) per altre informazioni sull'installazione di Android Studio e la creazione di un nuovo progetto.

![Creare un progetto in Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Configura un dispositivo

Per testare l'applicazione durante lo sviluppo, è possibile usare un telefono Android o un emulatore Android.

Per ulteriori informazioni sulla configurazione di un AVD (dispositivo virtuale Android), vedere la [documentazione Android Studio](https://developer.android.com/studio/run/managing-avds).

## <a name="install-the-azure-maps-android-sdk"></a>Installare Android SDK per Mappe di Azure

Il passaggio successivo per la creazione dell'applicazione consiste nell'installare Android SDK per Mappe di Azure.

Per installare l'SDK, seguire questa procedura:

1. Nella scheda progetto espandere **script Gradle**. Aprire **Build. Gradle (Project: My_Application)** e aggiungere il codice seguente alla sezione **all projects (tutti i progetti**) `repositories`  :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aprire **Build. Gradle (Module: My_Application)**.

3. Assicurarsi che **minSdkVersion** nella `defaultConfig` sezione sia all'API 21 o successiva.

4. Aggiungere il codice seguente alla sezione Android:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Aggiungere il codice seguente alla sezione `dependencies`:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. Fare clic su **file** sulla barra degli strumenti principale e quindi selezionare **Sincronizza progetto con file Gradle**.

7. Aprire `res > layout > activity_main.xml`. Fare clic su `Code` Visualizza nell'angolo superiore destro. Aggiungere il codice XML seguente all'interno dell' `<androidx.constraintlayout.widget.ConstraintLayout>` elemento.
    
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
            />
    </FrameLayout>
    ```

8. Nel `java > com.example.myapplication > MainActivity.java` file sarà necessario:

    * aggiungere le importazioni per Azure Maps SDK.
    * impostare le informazioni di autenticazione di Azure maps.
    * ottenere l'istanza del controllo Map nel metodo **OnCreate** .

    Per evitare di dover aggiungere informazioni di autenticazione per ogni visualizzazione dell'applicazione, le informazioni di autenticazione verranno impostate a livello globale chiamando `AzureMaps.setSubscriptionKey` . È inoltre possibile chiamare `AzureMaps.setAadProperties` se si desidera eseguire l'autenticazione utilizzando Azure Active Directory.

    Il controllo mappa esegue l'override dei seguenti metodi del ciclo di vita della classe MainActivity. Questi metodi sono responsabili della gestione del ciclo di vita OpenGL di Android.

    * onCreate(Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState(Bundle)
    * onLowMemory()

    Modificare il `MainActivity.java` file come indicato di seguito:

    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {

        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.

            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

>[!WARNING]
>È possibile che Android Studio non abbia importato le classi obbligatorie.  Di conseguenza, il codice avrà alcuni riferimenti a irrisolvibile. Per importare le classi obbligatorie, è sufficiente passare il puntatore del mouse su ogni riferimento non risolto e premere `Alt + Enter` (opzione + Restituisci su un Mac).

Per la compilazione dell'applicazione in Android Studio occorreranno pochi secondi. Al termine della compilazione è possibile testare l'applicazione nel dispositivo Android emulato. Verrà visualizzata una mappa simile a questa:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Mappe di Azure nell'applicazione Android":::

## <a name="localizing-the-map"></a>Localizzare la mappa

Azure Maps Android SDK offre tre diverse modalità di impostazione della lingua e delle impostazioni internazionali della mappa.

1. Impostare la lingua e le impostazioni internazionali chiamando metodi statici sulla classe AzureMaps.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Definire la lingua e le impostazioni internazionali nel codice XML del controllo mappa.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Impostare la lingua e le impostazioni internazionali chiamando metodi sul controllo mappa. Questa opzione consente di modificare le impostazioni in fase di esecuzione.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Di seguito è riportato un esempio di mappe di Azure con la lingua impostata su `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Mappe di Azure, immagine di mappa con etichette in francese":::

Un elenco completo delle lingue e delle visualizzazioni a livello di area supportate è disponibile [qui](supported-languages.md).

## <a name="navigating-the-map"></a>Esplorazione della mappa

Esistono diversi modi in cui è possibile ingrandire, fare una panoramica, ruotare e inclinare la mappa. Di seguito vengono descritti tutti i diversi modi per esplorare la mappa.

**Zoom della mappa**

- Toccare la mappa con due dita e avvicinarle per fare zoom indietro o allontanarle per fare zoom avanti.
- Effettuare un doppio tocco sulla mappa per ingrandire di un livello.
- Doppio tocco con due dita per eseguire lo zoom indietro della mappa di un livello.
- Toccare due volte; al secondo tocco, mantenere il dito sulla mappa e trascinare verso l'alto per fare zoom avanti o verso il basso per fare zoom indietro.

**Panoramica della mappa**

- Toccare la mappa e trascinare in qualsiasi direzione.

**Rotazione della mappa**

- Toccare la mappa con due dita e ruotare.

**Inclinazione della mappa**

- Toccare la mappa con due dita e trascinarle insieme verso l'alto o verso il basso.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere dati di sovrapposizione sulla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello simbolo a una mappa Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere forme a una mappa Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Cambiare gli stili della mappa nelle mappe Android](./set-android-map-styles.md)