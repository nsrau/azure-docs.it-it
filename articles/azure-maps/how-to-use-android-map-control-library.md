---
title: Introduzione a controllo mappa Android nelle mappe di Azure | Microsoft Docs
description: Il controllo mappa Android nelle mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9df5eb9fa4493f82c6efd4a8e30eee324e4eac2a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273843"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introduzione a Azure Maps Android SDK

Le mappe di Azure di Android SDK è una libreria di mappe vettoriali per Android. Questo articolo illustra le procedure di installazione di Android SDK mappe di Azure e il caricamento di una mappa.

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

1. Aprire il livello superiore **Build. gradle** file e aggiungere il codice seguente per il **tutti i progetti**, **repository** Blocca sezione:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aggiornamento di **gradle** e aggiungervi il codice seguente:
    
    1. Assicurarsi che il progetto **minSdkVersion** sono API 21 o versione successiva.

    2. Aggiungere il codice seguente alla sezione Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Aggiornare il blocco delle dipendenze e aggiungere una nuova riga di implementazione delle dipendenze per il SDK Android mappe di Azure più recente:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Le mappe di Azure di Android SDK viene regolarmente aggiornata e migliorata. È possibile vedere le [Introduzione a controllo mappa Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) documentazione, per ottenere il numero di versione di implementazione mappe di Azure più recente. Inoltre, è possibile impostare il numero di versione da "0.2" a "0 +" per sempre puntare all'ultima versione.

3. Modificare **res** > **layout** > **activity_main** e sostituirlo con quanto segue:
    
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

4. Nel **mainactivity. Java** file sarà necessario:
    
    * aggiungere le importazioni per le mappe di Azure SDK
    * impostare le informazioni di autenticazione di mappe di Azure
    * ottenere l'istanza del controllo mappa nel **onCreate** (metodo)

    Impostare le informazioni di autenticazione nella classe AzureMaps a livello globale tramite i metodi setSubscriptionKey o setAadProperties semplifica in modo non sarà necessario aggiungere le informazioni di autenticazione in ogni visualizzazione. Il controllo mappa contiene i propri metodi del ciclo di vita per la gestione OpenGL ciclo di vita di Android, che deve essere chiamato direttamente dall'attività che lo contiene. Affinché l'app in modo corretto, chiamare i metodi del ciclo di vita del controllo mappa, è necessario eseguire l'override di metodi del ciclo di vita seguenti nell'attività che contiene il controllo mappa e chiamare il metodo di controllo mappa corrispondente. 

    Modificare il **mainactivity. Java** file come segue:
    
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
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
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

<center>

![Mappa di Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere dati di sovrimpressione sulla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello di simboli per una mappa di Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Aggiungere forme a una mappa di Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Modifica stili mappa a maps Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
