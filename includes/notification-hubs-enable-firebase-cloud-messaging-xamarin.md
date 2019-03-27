---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57908287"
---
1. Accedere alla [console di Firebase](https://firebase.google.com/console/). Creare un nuovo progetto Firebase se non è già disponibile.
2. Dopo aver creato il progetto, selezionare **Add Firebase to your Android app** (Aggiungi Firebase all'app Android). 

    ![Aggiungere Firebase all'app Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Nella pagina **Aggiungere Firebase all'app Android** seguire questa procedura: 
    1. In **Android package name** (Nome pacchetto Android) immettere un nome per il pacchetto. Ad esempio: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Specificare il nome del pacchetto](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selezionare **Registra l'app**. 
4. Selezionare **Download google-services.json** (Scarica google-services.json), salvare il file nella cartella **app** del progetto e quindi selezionare **Avanti**. 

    ![Scaricare google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Selezionare **Avanti** nella pagina. 
7. Selezionare **Ignora questo passaggio** nella pagina. 

    ![Ignorare l'ultimo passaggio](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Nella console di Firebase selezionare il file COG per il progetto. Selezionare quindi **Project Settings** (Impostazioni progetto).

    ![Selezionare Project Settings (Impostazioni progetto)](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se non è stato scaricato il file **google-services.json**, è possibile scaricarlo da questa pagina. 
5. Passare alla scheda **Cloud Messaging** in alto. 
6. Copiare e salvare il valore di **Chiave server** per un uso successivo. Questo valore verrà usato per configurare l'hub di notifica.
