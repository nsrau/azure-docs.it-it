---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bc920493b32d500602a5b683c098d23aff855150
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823190"
---
1. In **Android Studio** selezionare **Tools** (Strumenti) nel menu e quindi **SDK Manager**. 
2. Selezionare la versione di destinazione di Android SDK usata nel progetto e quindi **Show Package Details** (Visualizza dettagli pacchetto). 

    ![Android SDK Manager: selezionare la versione di destinazione](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selezionare il componente **Google APIs** (API Google), se non è già installato.

    ![Android SDK Manager: selezione di Google APIs (API Google)](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Passare alla scheda **SDK Tools**. Se lo strumento Google Play Services non è già installato, fare clic su **Google Play Services** come illustrato nell'immagine di seguito. Fare quindi clic su **Apply** per installarlo. Prendere nota del percorso dell'SDK per l'uso in un passaggio successivo.

    ![Android SDK Manager: selezione di Google Play Services](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Se viene visualizzata la finestra di dialogo **Confirm Change** (Conferma modifica), selezionare **OK**. Il programma di installazione componenti installa i componenti richiesti. Al termine dell'installazione dei componenti, selezionare **Finish** (Fine).
4. Selezionare **OK** per chiudere la finestra di dialogo **Settings for New Projects** (Impostazioni per nuovi progetti).  
5. Aprire il file `build.gradle` nella directory **app** e aggiungere questa riga sotto `dependencies`. 

    ```text
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Selezionare l'icona **Sync Now** (Sincronizza ora) sulla barra degli strumenti.

    ![Sincronizzazione con Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Aprire **AndroidManifest.xml** e aggiungere il tag seguente al tag *application* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
