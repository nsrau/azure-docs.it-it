---
title: Introduzione al controllo mappa di Android | Mappe Microsoft Azure
description: In questo articolo si apprenderà come iniziare a usare il controllo mappa di Android usando le mappe di Microsoft Azure Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e0f0f311b7ec8adae6ddb25e01046141adadfa4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548546"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introduzione ad Azure Maps Android SDK

Il Android SDK mappe di Azure è una libreria di mappa vettoriale per Android. Questo articolo illustra i processi di installazione delle mappe di Azure Android SDK e il caricamento di una mappa.

## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

Per completare le procedure descritte in questo articolo, è prima di tutto necessario [creare un account Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) nel piano tariffario S1 e [ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) per l'account.

Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Scarica Android Studio

Scaricare Android Studio e creare un progetto con un'attività vuota prima di installare il Android SDK Maps di Azure. È possibile [scaricare gratuitamente Android Studio](https://developer.android.com/studio/) da Google. 

## <a name="create-a-project-in-android-studio"></a>Creare un progetto in Android Studio

Per prima cosa, creare un nuovo progetto con un'attività vuota. Completare questi passaggi per creare un progetto Android Studio:

1. In **scegliere il progetto**selezionare **telefono e Tablet**. L'applicazione verrà eseguita in questo fattore di forma.
2. Nella scheda **telefono e Tablet** selezionare **attività vuota**, quindi fare clic su **Avanti**.
3. In **Configure your project** (Configurare il progetto) selezionare `API 21: Android 5.0.0 (Lollipop)` come SDK minimo. Questa è la versione meno recente supportata dalle mappe di Azure Android SDK.
4. Accettare l'impostazione `Activity Name` predefinita `Layout Name` e quindi fare clic su **fine**.

Per ulteriori informazioni sull'installazione di Android Studio e sulla creazione di un nuovo progetto, vedere la [documentazione Android Studio](https://developer.android.com/studio/intro/) .

![Creare un progetto in Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurare un dispositivo virtuale

Android Studio consente di configurare un dispositivo Android virtuale nel computer. Questa operazione può essere utile per testare l'applicazione durante lo sviluppo. Per configurare un dispositivo virtuale, selezionare l'icona Android Virtual Device (AVD) Manager nell'angolo in alto a destra della schermata del progetto, quindi selezionare **Crea dispositivo virtuale**. È anche possibile arrivare a AVD Manager selezionando **strumenti** > **Android** > **AVD Manager** dalla barra degli strumenti. Nella categoria **telefoni** selezionare **Nexus 5x**, quindi fare clic su **Avanti**.

Per altre informazioni sulla configurazione di un AVD, vedere la [documentazione Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulatore Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installare Azure Maps Android SDK

Il passaggio successivo per la creazione dell'applicazione consiste nell'installare le mappe di Azure Android SDK. Completare questi passaggi per installare l'SDK:

1. Aprire il file di primo livello **build.gradle** e aggiungere il codice seguente alla sezione del blocco **all projects**, **repositories**:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aggiornare **app/build.gradle** e aggiungervi il codice seguente:
    
    1. Verificare che il valore di **minSdkVersion** del progetto sia API 21 o versione successiva.

    2. Aggiungere il codice seguente alla sezione Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Aggiornare il blocco delle dipendenze e aggiungere una nuova riga di dipendenza implementation per la versione più recente di Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Passare a **File** sulla barra degli strumenti e fare clic su **Sync Project with Gradle Files** (Sincronizza progetto con file Gradle).
3. Aggiungere un frammento di mappa all'attività principale (res \> layout \> activity\_main.xml):
    
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
            />
    </FrameLayout>
    ```

4. Nel file **MainActivity.java** è necessario:
    
    * Aggiungere le importazioni per Azure Maps SDK
    * Impostare le informazioni di autenticazione di Mappe di Azure
    * ottenere l'istanza del controllo Map nel metodo **OnCreate**

    Se si impostano le informazioni di autenticazione globalmente nella classe `AzureMaps` usando il metodo `setSubscriptionKey` o `setAadProperties`, si evita di dover aggiungere tali informazioni in ogni visualizzazione. 

    Il controllo mappa contiene i propri metodi del ciclo di vita per la gestione del ciclo di vita OpenGL di Android. Questi metodi del ciclo di vita devono essere chiamati direttamente dall'attività contenitore. Affinché l'app chiami correttamente i metodi del ciclo di vita del controllo mappa, è necessario eseguire l'override dei metodi del ciclo di vita seguenti nell'attività che contiene il controllo mappa. È necessario chiamare il rispettivo metodo di controllo map. 

    * OnCreate (bundle) 
    * OnStart () 
    * OnResume () 
    * OnPause () 
    * OnStop () 
    * OnDestroy () 
    * onSaveInstanceState (bundle) 
    * onLowMemory() 

    Modificare il file **MainActivity.java** come segue:
    
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

## <a name="import-classes"></a>Importa classi

Dopo aver completato i passaggi precedenti, probabilmente si otterranno avvisi da Android Studio su parte del codice. Per risolvere questi avvisi, importare le classi a cui viene `MainActivity.java`fatto riferimento in.

È possibile importare automaticamente queste classi selezionando ALT + INVIO (opzione + Return in un Mac).

Per compilare l'applicazione, selezionare il pulsante Run (Esegui), come illustrato nell'immagine seguente (oppure premere CTRL + R su un Mac).

![Fare clic su Run (Esegui)](./media/how-to-use-android-map-control-library/run-app.png)

La compilazione dell'applicazione Android Studio richiedere alcuni secondi. Al termine della compilazione, è possibile testare l'applicazione nel dispositivo Android emulato. Verrà visualizzata una mappa simile alla seguente:

<center>

![Mappe di Azure nell'applicazione Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Localizzare la mappa

Azure Maps Android SDK offre tre diverse modalità di impostazione della lingua e della visualizzazione a livello di area della mappa. Nel codice riportato di seguito viene illustrato come impostare la lingua sul francese ("fr-FR") e la vista regionale su "auto". 

La prima opzione consiste nel passare la lingua e visualizzare le informazioni internazionali nella `AzureMaps` classe usando i metodi `setLanguage` statici `setView` e a livello globale. La lingua predefinita e la visualizzazione a livello di area vengono impostate in tutti i controlli di Azure Maps caricati nell'app.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

La seconda opzione consiste nel passare le informazioni sulla lingua e la visualizzazione nel codice XML del controllo mappa.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

La terza opzione consiste nell'impostare a livello di codice la lingua e la visualizzazione a livello di area della mappa usando il metodo `setStyle` della mappa. Questa operazione può essere eseguita in qualsiasi momento per cambiare la lingua e la visualizzazione a livello di area della mappa.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Di seguito è riportato un esempio di mappe di Azure con la lingua impostata su "fr-FR" e la vista regionale impostata su "auto".

<center>

![Mappe di Azure, immagine mappa che mostra le etichette in francese](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Un elenco completo delle lingue e delle visualizzazioni a livello di area supportate è disponibile [qui](supported-languages.md).

## <a name="navigating-the-map"></a>Esplorazione della mappa

Esistono diversi modi in cui è possibile eseguire lo zoom, la panoramica, la rotazione e il pitch della mappa. Di seguito vengono descritti in dettaglio tutti i modi per esplorare la mappa.

**Zoom della mappa**

- Toccare la mappa con due dita e pizzicare per eseguire lo zoom indietro o dividere le dita per eseguire lo zoom avanti.
- Toccare doppia mappa per ingrandire un livello.
- Doppio tocco con due dita per ingrandire il mapping di un livello.
- Toccare due volte; al secondo tocco, mantenere il dito sulla mappa e trascinare verso l'alto per eseguire lo zoom avanti o verso il basso per eseguire lo zoom indietro.

**Panoramica della mappa**

- Toccare la mappa e trascinare in qualsiasi direzione.

**Ruotare la mappa**

- Toccare la mappa con due dita e ruotare.

**Pitch mappa**

- Toccare la mappa con due dita e trascinarle insieme.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere dati sovrapposti sulla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello di simbolo a una mappa Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere forme a una mappa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Modificare gli stili della mappa nelle mappe Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
