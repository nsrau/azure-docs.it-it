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
ms.openlocfilehash: e6b949824ec5da60c5e2485be830e61d156a11ff
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55830669"
---
1. Accedere alla [console di Firebase](https://firebase.google.com/console/). Creare un nuovo progetto Firebase se non è già disponibile.
2. Dopo aver creato il progetto, selezionare **Add Firebase to your Android app** (Aggiungi Firebase all'app Android). 

    ![Aggiungere Firebase all'app Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Nella pagina **Aggiungere Firebase all'app Android** seguire questa procedura: 
    1. Per **Android package name** (Nome pacchetto Android), copiare il valore di **applicationId** nel file **build.gradle** dell'applicazione. In questo esempio è `com.fabrikam.fcmtutorial1app`. 

        ![Specificare il nome del pacchetto](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selezionare **Registra l'app**. 
4. Selezionare **Download google-services.json** (Scarica google-services.json), salvare il file nella cartella **app** del progetto e quindi selezionare **Avanti**. 

    ![Scaricare google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Apportare le seguenti **modifiche di configurazione** al progetto in Android Studio. 
    1.  Nel file **project-level build.gradle** (&lt;project&gt;/build.gradle) aggiungere l'istruzione seguente alla sezione **dependencies**. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Nel file **app-level build.gradle** (&lt;project&gt;/&lt;app-module&gt;/build.gradle) aggiungere l'istruzione seguente alla sezione **dependencies**. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Aggiungere la riga seguente alla fine del file **app-level build.gradle** dopo la sezione dependencies. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
 
        ![Modifiche di configurazione a build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selezionare **Avanti** nella pagina. 
7. Selezionare **Ignora questo passaggio** nella pagina. 

    ![Ignorare l'ultimo passaggio](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. Nella console di Firebase selezionare il file COG per il progetto. Selezionare quindi **Project Settings** (Impostazioni progetto).

    ![Selezionare Project Settings (Impostazioni progetto)](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se il file **google-services.json** non è stato scaricato nella cartella **app** del progetto di Android Studio, è possibile eseguire questa operazione in questa pagina. 
5. Passare alla scheda **Cloud Messaging** in alto. 
6. Copiare e salvare il valore di **Chiave server** per un uso successivo. Questo valore verrà usato per configurare l'hub di notifica.
