---
title: Introduzione al controllo mappa di Android - Mappe di Microsoft Azure
description: In questo articolo si apprenderà come iniziare a usare il controllo mappa di Android usando Android SDK per Mappe di Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: f3a19a3e596d4d0162e92ac3f69c1333eea6aa41
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132161"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introduzione ad Android SDK per Mappe di Azure

Android SDK per Mappe di Azure è una raccolta di mappe in formato vettoriale per Android. Questo articolo illustra i processi di installazione di Android SDK per Mappe di Microsoft Azure e caricamento di una mappa.

## <a name="prerequisites"></a>Prerequisiti

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

Per completare le procedure descritte in questo articolo è prima di tutto necessario [creare un account Mappe di Azure ](quick-demo-map-app.md#create-an-azure-maps-account) nel piano tariffario S1 e [ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) per l'account.

Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Scaricare Android Studio

Scaricare Android Studio e creare un progetto con un'attività vuota prima di installare Android SDK per Mappe di Azure. È possibile [scaricare Android Studio](https://developer.android.com/studio/) gratuitamente da Google. 

## <a name="create-a-project-in-android-studio"></a>Creare un progetto in Android Studio

Per prima cosa, creare un nuovo progetto con un'attività vuota. Completare questi passaggi per creare un progetto di Android Studio:

1. In **Choose your project** (Scegliere il progetto) selezionare **Phone and Tablet** (Telefoni e tablet). L'applicazione verrà eseguita in questo fattore di forma.
2. Nella scheda **Phone e Tablet** (Telefoni e tablet) selezionare **Empty Activity** (Attività vuota), quindi selezionare **Next** (Avanti).
3. In **Configure your project** (Configurare il progetto) selezionare `API 21: Android 5.0.0 (Lollipop)` come SDK minimo. Questa è la versione minima supportata da Android SDK per Mappe di Azure.
4. Accettare i valori `Activity Name` e `Layout Name` predefiniti e selezionare **Finish** (Fine).

Vedere la [documentazione di Android Studio](https://developer.android.com/studio/intro/) per altre informazioni sull'installazione di Android Studio e la creazione di un nuovo progetto.

![Creare un progetto in Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurare un dispositivo virtuale

Android Studio consente di configurare un dispositivo Android virtuale nel computer. Questa operazione può essere utile per testare l'applicazione durante lo sviluppo. Per configurare un dispositivo virtuale, selezionare l'icona del gestore di dispositivi virtuali Android, AVD Manager, in alto a destra nello schermo del progetto, quindi selezionare **Create Virtual Device** (Crea dispositivo virtuale). È anche possibile aprire AVD Manager selezionando **Tools (Strumenti)**  > **Android** > **AVD Manager** dalla barra degli strumenti. Nella categoria **Phones** (Telefoni) selezionare **Nexus 5X** e quindi **Next** (Avanti).

Per altre informazioni sulla configurazione di un dispositivo virtuale Android, vedere la [documentazione di Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulatore Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installare Android SDK per Mappe di Azure

Il passaggio successivo per la creazione dell'applicazione consiste nell'installare Android SDK per Mappe di Azure. Per installare l'SDK, seguire questa procedura:

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
        implementation "com.microsoft.azure.maps:mapcontrol:0.4"
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
    * Ottenere l'istanza del controllo mappa nel metodo **OnCreate**

    Se si impostano le informazioni di autenticazione globalmente nella classe `AzureMaps` usando il metodo `setSubscriptionKey` o `setAadProperties`, si evita di dover aggiungere tali informazioni in ogni visualizzazione. 

    Il controllo mappa contiene i propri metodi del ciclo di vita per la gestione del ciclo di vita OpenGL di Android. Questi metodi del ciclo di vita devono essere chiamati direttamente dall'attività che li contiene. Perché l'app possa chiamare correttamente i metodi del ciclo di vita del controllo mappa, occorre eseguire l'override dei metodi del ciclo di vita seguenti nell'attività che contiene il controllo mappa e chiamare il rispettivo metodo del controllo mappa. 

    * onCreate(Bundle) 
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

## <a name="import-classes"></a>Importare le classi

Dopo aver completato i passaggi precedenti, probabilmente si riceveranno avvisi da Android Studio relativi a parti del codice. Per risolvere questi avvisi, importare le classi a cui viene fatto riferimento in `MainActivity.java`.

È possibile importare automaticamente queste classi selezionando ALT+INVIO (OPZIONE+INVIO in un Mac).

Per compilare l'applicazione, selezionare il pulsante di esecuzione illustrato nell'immagine seguente oppure premere CTRL+R su un Mac.

![Fare clic su Run (Esegui)](./media/how-to-use-android-map-control-library/run-app.png)

Per la compilazione dell'applicazione in Android Studio occorreranno pochi secondi. Al termine della compilazione è possibile testare l'applicazione nel dispositivo Android emulato. Verrà visualizzata una mappa simile a questa:

<center>

![Mappe di Azure nell'applicazione Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Localizzare la mappa

Android SDK per Mappe di Azure offre tre modi diversi per impostare la lingua e la visualizzazione a livello di area della mappa. Il codice seguente mostra come impostare la lingua su Francese ("fr-FR") e la visualizzazione a livello di area su automatica. 

La prima opzione consiste nel passare le informazioni sulla lingua e sulla visualizzazione a livello di area nella classe `AzureMaps` usando i metodi statici `setLanguage` e `setView` a livello globale. In questo modo la lingua e la visualizzazione a livello di area predefinite vengono impostate in tutti i controlli di Mappe di Azure caricati nell'app.

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

Di seguito è riportato un esempio di Mappe di Azure con la lingua impostata su "fr-FR" e la visualizzazione a livello di area impostata su "auto".

<center>

![Mappe di Azure, immagine di mappa con etichette in francese](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Un elenco completo delle lingue e delle visualizzazioni a livello di area supportate è disponibile [qui](supported-languages.md).

## <a name="navigating-the-map"></a>Esplorazione della mappa

Esistono diversi modi in cui è possibile ingrandire, fare una panoramica, ruotare e inclinare la mappa. Di seguito vengono descritti tutti i diversi modi per esplorare la mappa.

**Zoom della mappa**

- Toccare la mappa con due dita e avvicinarle per fare zoom indietro o allontanarle per fare zoom avanti.
- Effettuare un doppio tocco sulla mappa per ingrandire di un livello.
- Effettuare un doppio tocco con due dita per fare zoom indietro di un livello.
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
> [Aggiungere forme a una mappa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Cambiare gli stili della mappa nelle mappe Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
