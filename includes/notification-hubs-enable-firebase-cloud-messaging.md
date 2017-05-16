

1. Accedere alla [console di Firebase](https://firebase.google.com/console/). Creare un nuovo progetto Firebase se non è già disponibile.
2. Dopo aver creato il progetto, fare clic su **Add Firebase to your Android app** (Aggiungi Firebase all'app Android) e seguire le istruzioni fornite.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Nella console di Firebase fare clic sul file cog per il progetto e quindi fare clic su **Impostazioni progetto**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Fare clic sulla scheda **Cloud Messaging** nelle impostazioni del progetto e copiare il valore di **Chiave server** e **ID mittente**. Questi valori verranno usati in un secondo momento per configurare i criteri di accesso dell'hub di notifica de e il gestore delle notifiche nell'app.
