---
title: Guida introduttiva al controllo della mappa di Android Mappe di Microsoft Azure
description: In questo articolo si apprenderà come iniziare a usare il controllo mappa Android usando Microsoft Azure Maps Android SDK.
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

Azure Maps Android SDK è una libreria di mappe vettoriali per Android.The Azure Maps Android SDK is a vector map library for Android. Questo articolo illustra i processi di installazione di Azure Maps Android SDK e il caricamento di una mappa.

## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

Per completare le procedure descritte in questo articolo, è innanzitutto necessario [creare un account Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) nel piano tariffario S1 e ottenere la chiave [primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) per l'account.

Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Scarica Android Studio

Scaricare Android Studio e creare un progetto con un'attività vuota prima di installare Azure Maps Android SDK. È possibile [scaricare Android Studio](https://developer.android.com/studio/) gratuitamente da Google. 

## <a name="create-a-project-in-android-studio"></a>Creare un progetto in Android Studio

Creare innanzitutto un nuovo progetto con un'attività vuota. Completare questi passaggi per creare un progetto Android Studio:Complete these steps to create an Android Studio project:

1. In **Scegliere il progetto**selezionare Telefono e **tablet**. L'applicazione verrà eseguita su questo fattore di forma.
2. Nella scheda **Telefono e tablet** selezionare Attività **vuota**e quindi **Avanti**.
3. In **Configure your project** (Configurare il progetto) selezionare `API 21: Android 5.0.0 (Lollipop)` come SDK minimo. Questa è la versione meno recente supportata da Azure Maps Android SDK.
4. Accettare l'impostazione predefinita `Activity Name` e `Layout Name` selezionare **Fine**.

Vedere la [documentazione](https://developer.android.com/studio/intro/) di Android Studio per ulteriori informazioni sull'installazione di Android Studio e la creazione di un nuovo progetto.

![Creare un progetto in Android StudioCreate a project in Android studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurare un dispositivo virtuale

Android Studio consente di configurare un dispositivo Android virtuale nel computer. In questo modo è possibile testare l'applicazione durante lo sviluppo. Per configurare un dispositivo virtuale, selezionare l'icona Gestione dispositivi virtuali Android (AVD) nell'angolo superiore destro della schermata del progetto e quindi selezionare **Crea dispositivo virtuale**. È inoltre possibile accedere a AVD Manager selezionando **Strumenti** > **Android** > **AVD Manager** dalla barra degli strumenti. Nella categoria **Telefoni** selezionare **Nexus 5X**, quindi **Avanti**.

Ulteriori informazioni sulla configurazione di un AVD sono riportate nella [documentazione di Android Studio.](https://developer.android.com/studio/run/managing-avds)

![Emulatore Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installare Android SDK di Azure MapsInstall the Azure Maps Android SDK

Il passaggio successivo nella compilazione dell'applicazione consiste nell'installare Azure Maps Android SDK. Completare questi passaggi per installare l'SDK:

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
    * ottenere l'istanza del controllo mappa nel metodo **onCreateGet** the map control instance in the onCreate method

    Se si impostano le informazioni di autenticazione globalmente nella classe `AzureMaps` usando il metodo `setSubscriptionKey` o `setAadProperties`, si evita di dover aggiungere tali informazioni in ogni visualizzazione. 

    Il controllo mappa contiene i propri metodi del ciclo di vita per la gestione del ciclo di vita OpenGL di Android. Questi metodi del ciclo di vita devono essere chiamati direttamente dall'attività contenitore. Affinché l'app chiami correttamente i metodi del ciclo di vita del controllo mappa, devi eseguire l'override dei metodi del ciclo di vita seguenti nell'attività che contiene il controllo mappa. Inoltre, è necessario chiamare il rispettivo metodo di controllo della mappa. 

    * onCreate(Pacchetto) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundle) 
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

## <a name="import-classes"></a>Classi di importazione

Dopo aver completato i passaggi precedenti, probabilmente riceverai avvisi da Android Studio su parte del codice. Per risolvere questi avvisi, importare `MainActivity.java`le classi a cui si fa riferimento in .

Per importare automaticamente queste classi, selezionate Alt , Invio (Opzione-Invio su un Mac).

Selezionare il pulsante Esegui, come illustrato nel grafico seguente (o premere CTRL e R su un Mac) per compilare l'applicazione.

![Fare clic su Run (Esegui)](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio richiederà alcuni secondi per compilare l'applicazione. Al termine della compilazione, è possibile testare l'applicazione nel dispositivo Android emulato. Dovresti vedere una mappa come questa:

<center>

![Mappe di Azure nell'applicazione AndroidAzure Maps in Android application](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Localizzare la mappa

Azure Maps Android SDK offre tre modi diversi per impostare la lingua e la visualizzazione regionale della mappa. Il codice seguente mostra come impostare la lingua su francese ("fr-FR") e la visualizzazione regionale su "auto". 

La prima opzione consiste nel passare la `AzureMaps` lingua e `setLanguage` visualizzare `setView` le informazioni regionali nella classe utilizzando i metodi statici e globali. In questo modo la lingua predefinita e la visualizzazione regionale verranno impostate in tutti i controlli di Azure Maps caricati nell'app.

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

Ecco un esempio di Mappe di Azure con la lingua impostata su "fr-FR" e la visualizzazione regionale impostata su "auto".

<center>

![Mappe di Azure, immagine della mappa che mostra le etichette in franceseAzure Maps, map image showing labels in French](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Un elenco completo delle lingue e delle visualizzazioni a livello di area supportate è disponibile [qui](supported-languages.md).

## <a name="navigating-the-map"></a>Navigazione nella mappa

Esistono diversi modi in cui la mappa può essere ingrandita, panoramica, ruotata e inclinata. Di seguito vengono descritti in dettaglio tutti i diversi modi per spostarsi all'interno della mappa.

**Ingrandire la mappa**

- Toccare la mappa con due dita e avvicinare le dita per ridurre o allontanare le dita per eseguire lo zoom avanti.
- Tocca due volte la mappa per ingrandire un livello.
- Toccare due volte con due dita per ingrandire la mappa di un livello.
- Toccare due volte; al secondo tocco, tenere premuto il dito sulla mappa e trascinare verso l'alto per ingrandire o ridurre per eseguire lo zoom indietro.

**Eseguire la panoramica della mappa**

- Toccare la mappa e trascinare in qualsiasi direzione.

**Ruotare la mappa**

- Toccare la mappa con due dita e ruotare.

**Pitch della mappa**

- Toccare la mappa con due dita e trascinarle verso l'alto o verso il basso insieme.

## <a name="next-steps"></a>Passaggi successivi

Scopri come aggiungere dati di sovrapposizione sulla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un layer di simboli a una mappa Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere forme a una mappa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Modificare gli stili delle mappe nelle mappe Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
