---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 01/04/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f00ca7ddf44a9d5b850cd47520970a0396a0c1b5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453091"
---
1. Aprire Android SDK Manager facendo clic sull'icona della barra degli strumenti di Android Studio o su **Strumenti** > **Android** > **SDK Manager** nel menu. Trovare la versione di destinazione di Android SDK usata nel progetto, aprirla facendo clic su **Show Package Details** (Visualizza dettagli pacchetto) e quindi scegliere **Google APIs** (API Google), se non è già installato.
2. Fare sulla scheda **SDK Tools** . Se Google Play Service non è già installato, fare clic su **Google Play Services** come mostrato di seguito. Fare quindi clic su **Apply** per installarlo. Prendere nota del percorso dell'SDK per l'uso in un passaggio successivo.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Aprire il file `build.gradle` nella directory dell'app.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Aggiungere questa riga sotto `dependencies`:

    ```text
    compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. Fare clic sul pulsante **Sync Project with Gradle Files** sulla barra degli strumenti.
6. Aprire **AndroidManifest.xml** e aggiungere il tag seguente al tag *application* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
