---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728848"
---
1. Accedere alla [console di Firebase](https://firebase.google.com/console/). Creare un nuovo progetto Firebase se non è già disponibile.
2. Dopo aver creato il progetto, selezionare **Add Firebase to your Android app** (Aggiungi Firebase all'app Android). 

    ![Aggiungere Firebase all'app Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Nella pagina **Aggiungere Firebase all'app Android** seguire questa procedura: 
    1. In **Android package name** (Nome pacchetto Android) immettere un nome per il pacchetto. Ad esempio: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Specificare il nome del pacchetto](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selezionare **Registra l'app**.  
    1. Selezionare **Scaricare google-services.json**. Salvare quindi il file nella cartella del progetto e selezionare **Avanti**. Se non è ancora stato creato il progetto di Visual Studio, è possibile eseguire questo passaggio dopo aver creato il progetto. 

        ![Scaricare google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Selezionare **Avanti**. 
    7. Selezionare **Ignora questo passaggio**. 

        ![Ignorare l'ultimo passaggio](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Nella console di Firebase selezionare il file COG per il progetto. Selezionare quindi **Project Settings** (Impostazioni progetto).

    ![Selezionare Project Settings (Impostazioni progetto)](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se non è stato scaricato il file **google-services.json**, è possibile scaricarlo da questa pagina. 

    ![Scaricare google-services.json dalla scheda Generale](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Passare alla scheda **Cloud Messaging** in alto. Copiare e salvare il valore di **Chiave server** per un uso successivo. Questo valore verrà usato per configurare l'hub di notifica.

    ![Copiare la chiave server](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
