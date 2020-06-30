---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095515"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Creare un progetto Firebase e abilitare Firebase Cloud Messaging per Android

1. Accedere alla [console di Firebase](https://firebase.google.com/console/). Creare un nuovo progetto Firebase immettendo **PushDemo** in **Project name** (Nome progetto).

    > [!NOTE]
    > Verrà generato automaticamente un nome univoco. Per impostazione predefinita, questo nome è costituito da una variante in minuscolo del nome specificato più un numero generato separato da un trattino. Se si vuole è possibile modificarlo, purché rimanga un nome univoco globale.

1. Dopo aver creato il progetto, selezionare **Add Firebase to your Android app** (Aggiungi Firebase all'app Android).

    ![Aggiungere Firebase all'app Android](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. Nella pagina **Add Firebase to your Android app** (Aggiungere Firebase all'app Android) eseguire la procedura seguente.
    1. In **Android package name** (Nome pacchetto Android) immettere un nome per il pacchetto. Ad esempio: `com.<organization_identifier>.<package_name>`.

        ![Specificare il nome del pacchetto](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. Selezionare **Registra l'app**.  
    1. Selezionare **Scaricare google-services.json**. Salvare quindi il file in una cartella locale per usarlo in un secondo momento e selezionare **Avanti**.  

        ![Scaricare google-services.json](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. Selezionare **Avanti**.
    1. Selezionare **Continue to console** (Passa alla console).

        > [!NOTE]
        > Se il pulsante **Continue to console** non è abilitato, a causa del controllo di *verifica dell'installazione*, scegliere **Ignora questo passaggio**.

1. Nella console di Firebase selezionare il file COG per il progetto. Selezionare quindi **Project Settings** (Impostazioni progetto).

    ![Selezionare Project Settings (Impostazioni progetto)](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Se non è stato scaricato il file **google-services.json**, è possibile scaricarlo da questa pagina.

1. Passare alla scheda **Cloud Messaging** in alto. Copiare e salvare il valore di **Chiave server** per un uso successivo. Questo valore verrà usato per configurare l'hub di notifica.

    ![Copiare la chiave server](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
