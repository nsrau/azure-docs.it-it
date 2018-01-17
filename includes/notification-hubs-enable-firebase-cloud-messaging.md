

1. Accedere alla [console di Firebase](https://firebase.google.com/console/). Creare un nuovo progetto Firebase se non è già disponibile.
2. Dopo aver creato il progetto, fare clic su **Add Firebase to your Android app** (Aggiungi Firebase all'app Android) e seguire le istruzioni fornite.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Nella console di Firebase fare clic sul file cog per il progetto e quindi fare clic su **Impostazioni progetto**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Fare clic sulla scheda **General** (Generale) nelle impostazioni del progetto e quindi scaricare il file **google-services.json** che contiene la chiave API del server e l'ID client.
5. Fare clic sulla scheda **Cloud Messaging** nelle impostazioni del progetto e copiare il valore di **Legacy server key** (Chiave server legacy). Questo valore verrà usato per configurare i criteri di accesso all'hub di notifica.
